Repository: SerialCommunication — Copilot instructions

Build / test / lint

- Windows .NET WinForms app (SerialCommunication):
  - Open SerialCommunication.slnx in Visual Studio and Build (recommended).
  - CLI build: msbuild SerialCommunication.slnx /p:Configuration=Debug
  - Run the built executable: SerialCommunication\bin\Debug\SerialCommunication.exe
  - No unit tests or linters configured in repo.

- Arduino sketch (SerialCommunication.ino):
  - Open in Arduino IDE and upload to boards (UNO, Mega, etc.).
  - CLI compile/upload (arduino-cli):
    - Compile: arduino-cli compile --fqbn arduino:avr:uno SerialCommunication.ino
    - Upload:  arduino-cli upload -p COM3 --fqbn arduino:avr:uno SerialCommunication.ino
  - Default baudrate used by sketch: 115200

High-level architecture

- Three cooperating components:
  1) Arduino firmware (SerialCommunication.ino + SerialCommand.* + analog.c)
     - Uses a lightweight SerialCommand library (SerialCommand.h/.cpp) to tokenize and dispatch text commands received over Serial.
     - Commands implemented: set, toggle, get, ping, help, debug.
     - Digital pins: d2..d4 (outputs), d5..d7 (inputs); PWM: 9..11; Analog: A0..A5.
     - Baudrate constant: 115200 (SerialCommunication.ino defines Baudrate).
     - Command termination: newline ('\n'). Buffer size: SERIALCOMMANDBUFFER=32; MAXSERIALCOMMANDS=10.

  2) Desktop app (C# WinForms in SerialCommunication/)
     - UI enumerates serial ports (SerialPort.GetPortNames()) and exposes baudrate selection (defaults to 115200 in UI).
     - Project targets .NET Framework 4.7.2 (open with VS 2017+).
     - The Form1 UI currently scaffolds port selection and connect button; serial I/O glue may be incomplete — check Form1.cs.

  3) Shared/third-party pieces
     - SerialCommand library was originally authored by Steven Cogswell (embedded in repo). analog.c is Arduino wiring code.

Key conventions and repo-specific patterns

- Command token format:
  - Digital pins prefixed with 'd' (e.g., d2)
  - Analog pins prefixed with 'a' (e.g., a0)
  - PWM commands use 'pwm' prefix (e.g., pwm9)
  - Commands/arguments are space-delimited and parsed via strtok_r in SerialCommand.
  - Termination char is '\n' (SerialCommand::term).

- Safety / limits to observe:
  - SERIALCOMMANDBUFFER is small (32 bytes). Keep command strings short to avoid wrap-around behavior.
  - MAXSERIALCOMMANDS is 10; adding more commands requires increasing that constant.

- Baudrate alignment: Arduino sketch defines Baudrate=115200; Desktop UI selects 115200 by default. Keep these in sync when testing.

- C# project notes:
  - Project file: SerialCommunication\SerialCommunication.csproj targets v4.7.2 and expects a Visual Studio/MSBuild workflow.
  - Output path: bin\Debug or bin\Release per configuration.

AI assistant and other config files

- No CLAUDE.md, AGENTS.md, .cursorrules, .windsurfrules, CONVENTIONS.md, or other AI-assistant config files found in the repo root. If you add any, include a short summary here so future Copilot sessions can surface their rules.

What to look at first when returning to work

- For firmware behavior: SerialCommunication.ino, SerialCommand.{h,cpp}, analog.c
- For desktop integration/UI: SerialCommunication\Form1.cs and Program.cs
- To change buffer/command limits: SerialCommand.h (SERIALCOMMANDBUFFER, MAXSERIALCOMMANDS)

Notes for future Copilot sessions

- Focus on text-based serial protocol and matching baudrate/terminator when generating integration code or tests.
- When editing firmware command set, update both the Arduino sketch and the SerialCommand constants if size/command-count changes.
- If adding automated tests, note there are currently no test harnesses; adding a CI job would require defining build steps for both msbuild and arduino-cli.

Last steps

- File created at .github/copilot-instructions.md. If you want this expanded (examples of messages, unit test suggestions, or CI snippets), say which area to expand.