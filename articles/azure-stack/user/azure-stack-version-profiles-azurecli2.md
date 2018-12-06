---
title: Připojení k Azure Stack pomocí rozhraní příkazového řádku | Dokumentace Microsoftu
description: Další informace o použití multiplatformního rozhraní příkazového řádku (CLI) ke správě a nasazování prostředků ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 6251a0c7fd43a12dbe02a0013f1530557d142d25
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969953"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Použití profilů verzí API pomocí Azure CLI ve službě Azure Stack

Provedením kroků v tomto článku se nastavit si rozhraní příkazového řádku Azure (CLI) ke správě prostředků Azure Stack Development Kit z klientských platformách Linux, Mac a Windows.

## <a name="install-cli"></a>Instalace rozhraní příkazového řádku

Přihlaste se k vaší pracovní stanici a nainstalovat rozhraní příkazového řádku. Azure Stack vyžaduje verzi 2.0 nebo novější z rozhraní příkazového řádku Azure. Který můžete nainstalovat pomocí kroků popsaných v [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) článku. Pokud chcete ověřit, jestli byla instalace úspěšná, otevřete okno příkazového řádku nebo terminálu a spusťte následující příkaz:

```azurecli
az --version
```

Měli byste vidět verzi rozhraní příkazového řádku Azure a dalších závislých knihoven nainstalovaných v počítači.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat certifikátu kořenové certifikační Autority Azure stacku

1. Získání certifikátu kořenové certifikační Autority Azure stacku z [operátor Azure stacku](../azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) a důvěřujete mu. Důvěřovat certifikátu kořenové certifikační Autority Azure stacku, přidejte je do existujícího certifikátu Python.

1. Najdete umístění certifikátu na svém počítači. Umístění se může lišit v závislosti na tom, kam jste nainstalovali Python. Budete muset mít [pip](https://pip.pypa.io) a [osobní](https://pypi.org/project/certifi/) nainstalovaným modulem. Můžete použít následující příkaz Pythonu na příkazovém řádku bash:

  ```bash  
    python -c "import certifi; print(certifi.where())"
  ```

  Poznamenejte si umístění certifikátu. Například, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Konkrétní cestu bude záviset na váš operační systém a verzi Pythonu, který jste nainstalovali.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Nastavit cestu pro vývojový počítač s v cloudu

Pokud používáte rozhraní příkazového řádku z počítače s Linuxem, který je vytvořen v rámci prostředí Azure Stack, spusťte následující příkaz prostředí bash s cestou k vašemu certifikátu.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Nastavit cestu pro vývojový počítač s mimo cloudu

Pokud používáte rozhraní příkazového řádku z počítače **mimo** prostředí Azure Stack:  

1. Musíte nastavit [připojení VPN ke službě Azure Stack](azure-stack-connect-azure-stack.md).

1. Zkopírujte certifikát PEM, který jste získali z operátory Azure stacku a poznamenejte si umístění souboru (PATH_TO_PEM_FILE).

1. Spusťte následující příkazy, v závislosti koncové v operačním systému stanici vývoje.

#### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Získejte koncový bod virtuálního počítače aliasy

Předtím, než se uživatelé mohou vytvářet virtuální počítače pomocí rozhraní příkazového řádku, musí kontaktovat operátory Azure stacku a získat identifikátor URI aliasy koncového bodu virtuálního počítače. Například Azure používá následující identifikátor URI: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. Správce cloudu by měl nastavit podobně jako koncový bod pro službu Azure Stack s obrázky, které jsou dostupné v Tržišti Azure Stack. Uživatelé musí předat identifikátor URI koncového bodu na `endpoint-vm-image-alias-doc` parametr `az cloud register` příkaz, jak je znázorněno v následující části. 
   

## <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Následující kroky použijte pro připojení ke službě Azure Stack:

1. Zaregistrovat vaším prostředím Azure Stack spuštěním `az cloud register` příkazu.
   
   a. K registraci *pro správu cloudu* prostředí, použijte:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. K registraci *uživatele* prostředí, použijte:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    c. K registraci *uživatele* ve víceklientské architektury prostředí, použijte:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --endpoint-active-directory-resource-id=<URI of the ActiveDirectoryServiceEndpointResourceID> \
        --profile 2018-03-01-hybrid
      ```

1. Pomocí následujících příkazů nastavte aktivní prostředí.

   a. Pro *pro správu cloudu* prostředí, použijte:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. Pro *uživatele* prostředí, použijte:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

1. Aktualizujte konfiguraci vašeho prostředí použít profil pro konkrétní verze rozhraní API Azure Stack. Pokud chcete aktualizovat konfiguraci, spusťte následující příkaz:

   ```azurecli
   az cloud update \
     --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi služby Azure Stack před sestavením. 1808, budete muset použít profilu verze rozhraní API **2017-03-09-profile** místo profilu verze rozhraní API **2018-03-01hybridní**.

1. Přihlaste se k prostředí Azure Stack pomocí `az login` příkazu. Můžete se přihlásit k prostředí Azure Stack jako uživatel, nebo jako [instanční objekt služby](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects). 

    * Prostředí AAD
      * Přihlaste se jako *uživatele*: můžete zadat uživatelské jméno a heslo přímo v rámci `az login` příkaz % $n nebo ověřování pomocí prohlížeče. Je nutné provést ten, pokud má váš účet zapnuté vícefaktorové ověřování.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Pokud váš uživatelský účet má povolené ověřování službou Multi-Factor Authentication, můžete použít `az login command` bez zadání `-u` parametru. Spuštěním příkazu poskytuje adresu URL a kód, který je nutné použít k ověření.
   
      * Přihlaste se jako *instanční objekt služby*: než se přihlásit, [vytvoření instančního objektu služby na webu Azure portal](azure-stack-create-service-principals.md) nebo rozhraní příkazového řádku a přiřaďte ho roli. Teď se přihlaste pomocí následujícího příkazu:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```
    * AD FS prostředí

        * Přihlaste se jako *instanční objekt služby*: 
          1.    Připravte soubor .pem, který má být použit pro přihlášením instančního objektu.
                * Na klientském počítači, kde byl vytvořen objekt zabezpečení a export certifikátu objektu služby jako pfx s privátním klíčem (umístěné na cert: \CurrentUser\My; název certifikátu má stejný název jako objekt zabezpečení).

                *   Převeďte soubor pfx na pem (použití OpenSSL nástroje).

          1.    Přihlaste se k rozhraní příkazového řádku. :
                ```azurecli
                az login --service-principal \
                 -u <Client ID from the Service Principal details> \
                 -p <Certificate's fully qualified name. Eg. C:\certs\spn.pem>
                 --tenant <Tenant ID> \
                 --debug 
                ```

## <a name="test-the-connectivity"></a>Otestovat připojení

Teď, když máme všechno, co instalační program, umožňuje vytvářet prostředky v rámci služby Azure Stack pomocí rozhraní příkazového řádku. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Chcete-li vytvořit skupinu prostředků s názvem "MyResourceGroup", použijte následující příkaz:

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Pokud skupina prostředků je úspěšně vytvořen, předchozí příkaz vypíše následující vlastnosti nově vytvořeného prostředku:

![Vytvoření výstupní skupiny prostředků](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Známé problémy
Existují některé známé problémy, které je třeba vědět, když používáte rozhraní příkazového řádku ve službě Azure Stack:

 - Tj interaktivní režim rozhraní příkazového řádku `az interactive` příkaz není dosud podporován ve službě Azure Stack.
 - Chcete-li získat seznam dostupných ve službě Azure Stack imagím virtuálních počítačů, použijte `az vm image list --all` příkaz místo `az vm image list` příkaz. Zadání `--all` možnost zajišťuje, že odpověď vrátí pouze obrázky, které jsou dostupné v prostředí Azure Stack.
 - Aliasy image virtuálního počítače, které jsou k dispozici v Azure nemusí být k dispozici ke službě Azure Stack. Při použití imagí virtuálních počítačů, musíte použít parametr celý název URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) místo aliasu image. Tento název URN musí odpovídat specifikaci bitové kopie odvozena z `az vm images list` příkazu.

## <a name="next-steps"></a>Další postup

[Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)

[Povolení rozhraní příkazového řádku Azure pro uživatele Azure stacku (operátor)](../azure-stack-cli-admin.md)

[Správa uživatelských oprávnění](azure-stack-manage-permissions.md)
