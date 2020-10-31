# Connecting to DELL IPMI via javaws

Tested working on
|Date | Oct 31, 2020|
|-|-|
|Java|1.8.0-271|
|iDRAC6 firmware version|1.85 (Build 03)|


## Problem

When launching the DELL virtual console with your jnlp file, you will encounter the error:
> Application Error: Unable to launch the application.
>
> **Error:** Unsigned application requesting unrestricted access to system <br>
> The following resource is signed with a weak signature algorithm MD5withRSA and is treated as unsigned: https://192.168.1.xxx:443/software/avctKVM.jar


This is due to security developments in Java and cryptography over the years, whereas the jars behind iDRAC6 has not caught up with the latest developments. 
Recent Java versions now consider the algorithms used in JNLP (RC4, MD5withRSA) as security vulnerabilities by today's standard, and therefore blocks them from running by default. 
In the solution that follows, we are going to lift this default restriction from Java by changing its settings.

## Solution

### Step 0. Requirements

- Windows, ~~Linux~~ (work in progress)
- Install Java 8 SE (JRE)

### Step 1. Add server to Java Exception Site List

1. Open Java Control Panel `Control Panel > Java`
2. Open Exception Site List window `Java Control Panel > Security > Edit Site List... `
3. Enter your DELL server IP address `Add > https://192.168.1.xxx`
4. `OK > OK` to confirm your settings

### Step 2. Enable MD5withRSA and RC4

1. Launch notepad++ with Administrator privileges
2. Open `C:\Program Files\Java\jre1.8.0_xxx\lib\security\java.security`
3. Copy then comment out the original lines for `jdk.jar.disabledAlgorithms`. Delete MD5 from the list, as follows:
    ```
    # jdk.jar.disabledAlgorithms=MD2, MD5, RSA keySize < 1024, \
    #      DSA keySize < 1024, include jdk.disabled.namedCurves
    jdk.jar.disabledAlgorithms=MD2, RSA keySize < 1024, \
        DSA keySize < 1024, include jdk.disabled.namedCurves
    ```
4. Copy then comment out the original lines for `jdk.tls.disabledAlgorithms`. Delete MD5withRSA from the list, as follows:
    ```
    # jdk.tls.disabledAlgorithms=SSLv3, RC4, DES, MD5withRSA, DH keySize < 1024, \
    #    EC keySize < 224, 3DES_EDE_CBC, anon, NULL, \
    #    include jdk.disabled.namedCurves
    jdk.tls.disabledAlgorithms=SSLv3, DES, DH keySize < 1024, \
        EC keySize < 224, 3DES_EDE_CBC, anon, NULL, \
        include jdk.disabled.namedCurves
    ```


### Step 3. (optional) Clear cache

If you have tried solutions from other people before coming to this guide, chances are you have accumulated some temporary files. These are considered as vulnerabilities and will be blocked by the system, despite you changing the settings in Step 1 & 2.

1. Open Java Control Panel `Control Panel > Java`
2. `General > Temporary Internet Files > Settings...`
3. `Temporary File Settings > Delete Files ...`
4. Accept the default selection, click OK.


### Step 4. Launch DELL virtual console
Download the `viewer.jnlp` file again from the iDRAC6 web UI and launch it again. It should now work perfectly.

## References
- https://www.youtube.com/watch?v=qTdaeic6ttU&ab_channel=ink0gnit0
- https://www.blackmoreops.com/2017/06/08/fix-java-error-unsigned-application-requesting-unrestricted-access-to-system/