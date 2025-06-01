Project Description
My goal was to analyze the OWASP Zed Attack Proxy Root CA certificate to extract a hidden flag. To achieve this, I studied the certificate’s structure, examined its fields, and tested different encoding and hashing methods of key data to understand how the system forms and verifies the flag.

Work Done
1. Certificate Analysis
I obtained the certificate in .cer format and viewed its contents using:

openssl x509 -in owasp_zap_root_ca.cer -noout -text
From the output, I saw that the localityName (L) field contained the string 3580e22cbd191. This looked like a key piece of information for extracting the flag.

2. Converting the Certificate to DER and Parsing ASN.1
To study the structure more deeply, I extracted the content between BEGIN CERTIFICATE and END CERTIFICATE and saved it as cert.b64:

awk 'BEGIN{ok=0} /BEGIN CERTIFICATE/{ok=1; next} /END CERTIFICATE/{ok=0} ok' owasp_zap_root_ca.cer > cert.b64
base64 -d cert.b64 > cert.der
Then, I parsed the DER format using:

openssl asn1parse -in cert.der -inform DER
This allowed me to understand how the certificate fields were organized and confirmed the presence of the target string in the localityName field.

3. Checking String Encodings and Hashes
I checked various encodings and hashes of the string 3580e22cbd191:

echo -n "3580e22cbd191" | base64
# MzU4MGUyMmNiZDE5MQo=

echo -n "3580e22cbd191" | md5sum
# 0280761316e1f999fc0852c965eab89c

echo -n "3580e22cbd191" | sha1sum
# e6fa01b1342bb239c52f06c49a06e2918a1a2e3e

echo -n "3580e22cbd191" | sha256sum
# f14d616554fac0c0ec9c4b72b79ac288ec2198fc799ccb5dbba2388ee92550a9
However, manually guessing the flag didn’t work because the system itself should provide the flag automatically upon correct interaction.

!!!PART2 Problem on AttackBox!!!
The initial attempt to complete the task using AttackBox ran into technical limitations:

Some tools, including Godot Engine and GDRE Decompiler, either didn’t work properly or wouldn’t launch in the virtual AttackBox environment.

SmartScreen and the browser blocked or interfered with downloading necessary versions.

404 errors occurred when trying to download GDRE through the terminal.

Solution — Switching to Local Windows Machine
The decision was made to move the work to a local Windows PC, where:

The official version of Godot Engine was downloaded and installed from the official website.

The Windows version of GDRE Tools (GDRE_tools-v1.00-beta.3-windows.zip) was downloaded from the GitHub release page, extracted, and used to unpack the .pck file.

The following command was used in cmd to unpack the game:
gdsdecomp.exe -i thegame.pck -o thegame_unpacked

Game Analysis and Modification
In the unpacked project, the file gui.gd was opened using Godot Engine.

Inside the score-handling function:
func _on_Board_update_score(score, lines):
    $ContainerScore / ScoreBackground / ScoreValue.text = str(score)
    $ContainerLines / LinesBackground / LinesCount.text = str(lines)
    if score >= 999999:
        $ButtonContainer / T.show()
The condition was modified to:
if score >= 0:
    $ButtonContainer / T.show()
This change allowed the flag button to appear immediately upon starting the game, without needing to actually score any points.

 Final Step: Retrieving the Flag
After launching the game in Godot with the modified script, the flag was successfully displayed:
THM{I_CAN_READ*****}




