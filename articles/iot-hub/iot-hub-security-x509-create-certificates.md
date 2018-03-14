---
title: "Jak pomocí prostředí PowerShell k vytvoření certifikátů X.509 | Microsoft Docs"
description: "Jak pomocí prostředí PowerShell vytvořit certifikáty X.509 místně a povolit X.509 na základě zabezpečení ve službě Azure IoT hub v simulovaném prostředí."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2017
ms.author: dkshir
ms.openlocfilehash: b2f78e8debd367f86ee9bb06bf7de50590c61ad7
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>Skripty prostředí PowerShell ke správě certifikační Autority podepsané X.509 – certifikáty

Zabezpečení na základě certifikátu X.509 ve službě IoT Hub, musíte spustit s [řetěz certifikátů X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), což zahrnuje kořenový certifikát a také všechny zprostředkující certifikáty, až se certifikát typu list. To *postup* Průvodce vás provede ukázkové skripty prostředí PowerShell využívající [OpenSSL](https://www.openssl.org/) k vytvoření a podepsání certifikátů X.509. Doporučujeme používat tato příručka pro experimentování, vzhledem k tomu, že hodně těchto kroků dojde během výrobní proces ve skutečnosti. Tyto certifikáty můžete použít k simulaci zabezpečení v Azure IoT hub pomocí *ověřování pomocí certifikátu X.509*. Kroky v této příručce vytvářet certifikáty místně na počítači se systémem Windows. 

## <a name="prerequisites"></a>Požadavky
Tento kurz předpokládá, že jste získali OpenSSL binární soubory. Můžete buď může
    - Stáhněte si OpenSSL zdrojový kód a sestavte binární soubory na počítači, nebo 
    - Stáhněte a nainstalujte všechny [binární soubory OpenSSL třetích stran](https://wiki.openssl.org/index.php/Binaries), například z [tento projekt na SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>Vytvoření certifikáty X.509
Následující kroky ukazují příklad vytvoření kořenové certifikáty X.509 místně. 

1. Otevřete okno prostředí PowerShell jako *správce*. 
2. Přejděte do pracovního adresáře. Spusťte následující skript a nastavte globální proměnné. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertSubject         = "CN=Azure IoT Root CA"
    $_intermediateCertSubject = "CN=Azure IoT Intermediate {0} CA"
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
3. Spusťte následující skript, který zkopíruje OpenSSL binární soubory do pracovního adresáře a nastaví proměnné prostředí:

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
4. Potom spusťte následující skript, která hledá zda certifikát k zadanému *název subjektu* je již nainstalován, a jestli OpenSSL správně nakonfigurovaná na váš počítač:
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
    Pokud se vše správně nakonfigurován, měli byste vidět "ÚSPĚCH" zprávy. 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>Vytvořit řetěz certifikátů X.509
Vytvořit řetěz certifikátů s kořenové certifikační Autority, například "CN = Azure IoT kořenové certifikační Autority", tato ukázka používá spuštěním následujícího skriptu prostředí PowerShell. Tento skript také aktualizací úložiště certifikátů operačního systému Windows, také vytvoří soubory certifikátů v pracovním adresáři. 
    1. Následující skript vytvoří funkci prostředí PowerShell pro vytvoření certifikát podepsaný sám sebou, daný *název subjektu* a úřad pro podepisování. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$subjectName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$subjectName; 
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
    2. Následující funkce prostředí PowerShell vytvoří zprostředkující certifikáty X.509 pomocí funkce předchozí, jakož i OpenSSL binární soubory. 
    ```PowerShell
    function New-CAIntermediateCert([string]$subjectName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($subjectName + ".cer")
        $newCert = New-CASelfsignedCertificate $subjectName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. Následující funkce prostředí PowerShell vytvoří řetězu certifikátů X.509. Čtení [certifikát zřetězil](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) Další informace.
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
    Tento skript vytvoří soubor s názvem *RootCA.cer* v pracovním adresáři. 
    4. Nakonec použijte výše uvedené funkce prostředí PowerShell vytvořit řetěz certifikátů X.509, spuštěním příkazu `New-CACertChain` v okně prostředí PowerShell. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Ověření u sebe certifikátu X.509 certifikační Autority

Tento skript provede *důkaz vlastnictví* tok pro svůj certifikát X.509. 

V okně prostředí PowerShell na pracovní ploše spusťte následující kód:
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $cnRequestedSubjectName = ("CN={0}" -f $requestedSubjectName)
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $cnRequestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnRequestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

Tento kód vytvoří certifikát s názvem daným subjektem, podepsaný certifikační autoritou, jako soubor s názvem *VerifyCert4.cer* v pracovním adresáři. Tento soubor certifikátu pomůže ověřit pomocí služby IoT hub, zda máte podpisový oprávnění (to znamená, že privátní klíč) této certifikační autority.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Vytvoření certifikátu X.509 listu pro vaše zařízení

Tato část uvádí, že které můžete použít skript prostředí PowerShell, který vytváří listu zařízení certifikát a odpovídající řetěz certifikátů. 

V okně prostředí PowerShell na místním počítači spusťte následující skript vytvořit certifikát podepsaný certifikační Autority X.509 pro toto zařízení:

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $cnNewDeviceSubjectName = ("CN={0}" -f $deviceName)
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $cnNewDeviceSubjectName $signingCert $false
   
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

Spusťte `New-CADevice "<yourTestDevice>"` v okně prostředí PowerShell, pomocí popisný název, který jste použili k vytvoření vašeho zařízení. Po zobrazení výzvy k zadání hesla pro privátního klíče Certifikační autority, zadejte "123". Tím se vytvoří  _<yourTestDevice>.pfx_ souboru v pracovním adresáři.

## <a name="clean-up-certificates"></a>Vyčištění certifikáty

V řádku start nebo **nastavení** aplikace, vyhledejte a vyberte **spravovat certifikáty počítače**. Odeberte všechny certifikáty vydané ** Azure IoT certifikační Autority TestOnly ***. Tyto certifikáty by měly existovat v následujících třech umístěních: 

* Certifikáty - místní počítač > osobní > certifikáty
* Certifikáty - místní počítač > Důvěryhodné kořenové certifikační autority > certifikáty
* Certifikáty - místní počítač > zprostředkující certifikační úřady > certifikáty

   ![Odebrat Azure IoT certifikační Autority TestOnly certifikáty](./media/iot-hub-security-x509-create-certificates/cleanup.png)