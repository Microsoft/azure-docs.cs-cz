---
title: Použití Powershellu k vytvoření certifikátů X.509 | Dokumentace Microsoftu
description: Použití Powershellu k vytvoření certifikátů X.509 místně a povolení X.509 na základě zabezpečení ve službě Azure IoT hub v simulovaném prostředí.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: dkshir
ms.openlocfilehash: d0063ff79a0bda88fffb486f03286f6784ece7fa
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "34637595"
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>Skripty Powershellu pro správu certifikátů X.509 podepsaných certifikační Autority

Zabezpečení pomocí certifikátů X.509 ve službě IoT Hub je nutné začít s [řetěz certifikátů X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), který obsahuje kořenový certifikát, stejně jako všechny zprostředkující certifikáty až listový certifikát. To *jak* příručka vás provede ukázkové skripty Powershellu, které používají [OpenSSL](https://www.openssl.org/) k vytvoření a podepsání certifikátů X.509. Doporučujeme vám k použití tohoto průvodce pro experimentování ve službě, protože mnoho z těchto kroků se stane během výrobního procesu v reálném světě. Tyto certifikáty slouží k simulaci zabezpečení v Azure IoT hub pomocí *ověřování pomocí certifikátu X.509*. Kroky v tomto průvodci vytvoření certifikátů místně na svém počítači s Windows. 

## <a name="prerequisites"></a>Požadavky
Tento kurz předpokládá, že jste získali OpenSSL binární soubory. Můžete buď
    - Stáhněte si zdrojový kód OpenSSL a sestavit binární soubory na svém počítači, nebo 
    - Stáhněte a nainstalujte všechny [binárních souborů třetí strany OpenSSL](https://wiki.openssl.org/index.php/Binaries), například z [tento projekt na SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>Vytvoření certifikátů X.509
Následující kroky ukazují, příklad toho, jak vytvořit kořenové certifikáty X.509 místně. 

1. Otevřete okno Powershellu jako *správce*.  
   **Poznámka:** je nutné otevřít to v prostředí PowerShell samotného, není prostředí PowerShell ISE, Visual Studio Code nebo jiné nástroje, které balí základní konzola Powershellu.  Použití bez konzoly Powershellu na základě způsobí `openssl` příkazy pod předsazení.

2. Přejděte do pracovního adresáře. Spusťte následující skript, který nastaví globální proměnné. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertCommonName      = "Azure IoT Root CA"
    $_rootCertSubject         = "CN=$_rootCertCommonName"
    $_intermediateCertSubject = "Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Spusťte následující skript, který zkopíruje OpenSSL binárních souborů do pracovního adresáře a nastaví proměnné prostředí:

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. Potom spusťte následující skript, který vyhledá, jestli certifikát k zadanému *název subjektu* je už nainstalovaný, a zda OpenSSL správně nakonfigurovaná na vašem počítači:
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Pokud je vše nastaveno správně, měli byste vidět "Success" zpráva. 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>Vytvořit řetěz certifikátů X.509
Vytvořit řetěz certifikátů s kořenové certifikační Autority, například "CN = Azure IoT kořenové certifikační Autority", tato ukázka používá spuštěním následujícího skriptu prostředí PowerShell. Tento skript také aktualizuje svého úložiště certifikátů operačního systému Windows, také vytvoří soubory certifikátů ve svém pracovním adresáři. 
    1. Následující skript prostředí PowerShell funkci, která vytvoří vlastnoručně podepsaný certifikát, pro vytvoří danou *název subjektu* a úřad pro podepisování. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$commonName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$commonName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. Následující funkce prostředí PowerShell vytvoří zprostředkujících certifikátů X.509 pomocí předchozí funkce, stejně jako binární soubory OpenSSL. 
    ```PowerShell
    function New-CAIntermediateCert([string]$commonName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($commonName + ".cer")
        $newCert = New-CASelfsignedCertificate $commonName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. Následující funkce prostředí PowerShell vytvoří řetěz certifikátů X.509. Čtení [certifikát zřetězil](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) Další informace.
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Tento skript vytvoří soubor s názvem *RootCA.cer* ve svém pracovním adresáři. 
    4. Nakonec použijte výše uvedené funkce Powershellu vytvořit řetěz certifikátů X.509 spuštěním příkazu `New-CACertChain` v prostředí PowerShell. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Doklad o vlastnictví vaší certifikátu webu X.509

Tento skript provede *důkaz vlastnictví* toku pro váš certifikát X.509. 

V okně Powershellu na ploše spusťte následující kód:
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $requestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $requestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

Tento kód vytvoří certifikát s názvem daného subjektu, podepsaný certifikační autoritou, jako soubor s názvem *VerifyCert4.cer* ve svém pracovním adresáři. Tento soubor certifikátu vám pomůže ověřit pomocí služby IoT hub, které máte oprávnění podpisový (to znamená, privátní klíč) této certifikační autority.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Vytvoření listový certifikát X.509 pro vaše zařízení

Tato část ukazuje, že používáte skript prostředí PowerShell, který vytvoří certifikát pro zařízení typu list a odpovídající řetěz certifikátů. 

V okně Powershellu v místním počítači spusťte následující skript, abyste mohli vytvořit certifikát X.509 podepsaný certifikační Autoritou pro toto zařízení:

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $deviceName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

Potom spusťte `New-CADevice "<yourTestDevice>"` v prostředí PowerShell pomocí popisný název, který jste použili k vytvoření vašeho zařízení. Po zobrazení výzvy k zadání hesla privátního klíče Certifikační autority, zadejte "123". Tím se vytvoří  _<yourTestDevice>.pfx_ soubor ve svém pracovním adresáři.

## <a name="clean-up-certificates"></a>Vyčištění certifikáty

Panelu start nebo **nastavení** aplikace, vyhledejte a vyberte **spravovat certifikáty počítače**. Odeberte všechny certifikáty vydané ** TestOnly *** Azure IoT certifikační Autority. Tyto certifikáty by měly existovat v následujících třech místech: 

* Certifikáty - místní počítač > osobní > certifikáty
* Certifikáty - místní počítač > Důvěryhodné kořenové certifikační autority > certifikáty
* Certifikáty - místní počítač > zprostředkujících certifikačních autorit > certifikáty

   ![Odebrání Azure IoT certifikační Autority TestOnly certifikátů](./media/iot-hub-security-x509-create-certificates/cleanup.png)
