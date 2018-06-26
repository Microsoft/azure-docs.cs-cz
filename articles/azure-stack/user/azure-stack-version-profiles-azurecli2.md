---
title: Připojení k Azure zásobníku pomocí rozhraní příkazového řádku | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku (CLI) a platformy pro správu a nasazení prostředků v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: eb01d31d00177560aca3aa71750cd2d1ec096f8f
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938379"
---
# <a name="use-api-version-profiles-with-azure-cli-20-in-azure-stack"></a>Rozhraní API verze profilů pomocí Azure CLI 2.0 v Azure zásobníku

Můžete provést kroky v tomto článku nastavte Azure rozhraní příkazového řádku (CLI) ke správě prostředků Azure zásobníku Development Kit z klientské platformy Linux, Mac a Windows.

## <a name="install-cli"></a>Instalace rozhraní příkazového řádku

Přihlaste se k vaší pracovní stanici a nainstalovat rozhraní příkazového řádku. Azure zásobníku vyžaduje 2.0 verzi rozhraní příkazového řádku Azure. Můžete nainstalovat pomocí kroků popsaných v [nainstalovat Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) článku. Pokud chcete ověřit, pokud byla instalace úspěšná, otevřete okno příkazového řádku nebo terminál a spusťte následující příkaz:

```azurecli
az --version
```

Měli byste vidět verzi rozhraní příkazového řádku Azure a další závislé knihovny, které jsou nainstalovány ve vašem počítači.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat certifikátu kořenové certifikační Autority Azure zásobníku

1. Získání certifikátu kořenové certifikační Autority zásobník Azure z [vaše zásobník Azure operátor](..\azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) a důvěřujete mu. Chcete-li důvěryhodný kořenový certifikát certifikační Autority zásobník Azure, připojte ji existující certifikát Python.

2. Vyhledejte umístění certifikátu v počítači. Umístění se může lišit v závislosti na tom, kde je instalována Python. Budete muset mít [pip](https://pip.pypa.io) a [osobní](https://pypi.org/project/certifi/) modul nainstalovaný. Můžete použít následující příkaz z příkazového řádku bash Python:

  ```bash  
    python -c "import certifi; print(certifi.where())"
  ```

  Poznamenejte si umístění certifikátu. Například, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Konkrétní cestu závisí na operačním systému a verzi jazyka Python, který jste nainstalovali.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Nastavit cestu pro vývojovém počítači, v cloudu

Pokud používáte rozhraní příkazového řádku z počítače systému Linux, který je vytvořen v prostředí Azure zásobníku, spusťte následující příkaz bash s cestou k certifikátu.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Nastavit cestu pro vývojovém počítači mimo cloudu

Pokud používáte rozhraní příkazového řádku z počítače **mimo** prostředí Azure zásobníku:  

1. Je nutné nastavit [připojení VPN do Azure zásobníku](azure-stack-connect-azure-stack.md).

2. Zkopírujte certifikát PEM, který jste získali z Azure zásobníku operátor a poznamenejte si umístění souboru (PATH_TO_PEM_FILE).

3. Spusťte následující příkazy, v závislosti na stanici vývoje OS koncová.

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
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Získat koncový bod aliasy virtuálního počítače

Než uživatelé mohou vytvářet virtuální počítače pomocí rozhraní příkazového řádku, musí kontaktovat operátor zásobník Azure a získat identifikátor URI aliasy koncový bod virtuálního počítače. Například Azure používá následující identifikátor URI: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. Správce cloudu by měl nastavit koncový bod podobné pro zásobník Azure s obrázky, které jsou k dispozici v zásobníku Azure marketplace. Uživatelé třeba předat identifikátor URI koncového bodu na `endpoint-vm-image-alias-doc` parametru `az cloud register` příkaz, jak je znázorněno v následujícím oddílu. 
   

## <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Pro připojení k Azure zásobníku použijte následující kroky:

1. Zaregistrovat prostředí Azure zásobníku spuštěním `az cloud register` příkaz.
   
   a. K registraci *cloudu správu* prostředí, použijte:

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

2. Nastavení aktivního prostředí pomocí následujících příkazů.

   a. Pro *cloudu správu* prostředí, použijte:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. Pro *uživatele* prostředí, použijte:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. Aktualizujte konfiguraci prostředí použít profil pro konkrétní verzi rozhraní API zásobník Azure. Pokud chcete aktualizovat konfiguraci, spusťte následující příkaz:

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. Přihlaste se k prostředí Azure zásobníku pomocí `az login` příkaz. Můžete se přihlásit do prostředí Azure zásobníku buď jako uživatel, nebo jako [instanční objekt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects). 

   * Přihlaste se jako *uživatele*: můžete zadat uživatelské jméno a heslo přímo v rámci `az login` příkaz nebo ověřit pomocí prohlížeče. Je nutné provést k tomu, pokud má váš účet povolenou službou Multi-Factor authentication.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Pokud váš uživatelský účet má povolenou službou Multi-Factor authentication, můžete použít `az login command` bez zadání `-u` parametr. Spuštěním příkazu vám dává adresu URL a kód, který je nutné použít k ověření.
   
   * Přihlaste se jako *instanční objekt*: než se přihlásit, [vytvořit objekt služby prostřednictvím portálu Azure](azure-stack-create-service-principals.md) nebo rozhraní příkazového řádku a přiřaďte mu roli. Nyní přihlásit pomocí následujícího příkazu:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>Testovací připojení

Teď, když jste všechno My instalační program, umožňuje vytvářet prostředky v rámci zásobníku Azure pomocí rozhraní příkazového řádku. Můžete například vytvořit skupinu prostředků pro aplikace a přidat virtuální počítač. Chcete-li vytvořit skupinu prostředků s názvem "MyResourceGroup" použijte následující příkaz:

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Pokud skupina prostředků je úspěšně vytvořil, předchozí příkaz výstupy následující vlastnosti nově vytvořeného prostředku:

![Vytvoření skupiny prostředků výstup](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Známé problémy
Existují některé známé problémy, které je potřeba vědět, když pomocí rozhraní příkazového řádku v zásobníku Azure:

 - Jednofaktorovému interaktivním režimu rozhraní příkazového řádku `az interactive` příkaz není dosud podporován v zásobníku Azure.
 - Seznam dostupných v zásobníku Azure bitové kopie virtuálních počítačů, použijte `az vm images list --all` příkaz místo `az vm image list` příkaz. Určení `--all` možnost zajišťuje, že odpovědi vrátí pouze obrázky, které jsou k dispozici ve vašem prostředí Azure zásobníku.
 - Aliasy bitové kopie virtuálního počítače, které jsou k dispozici v Azure nemusí být k dispozici do protokolů Azure. Pokud pomocí bitové kopie virtuálních počítačů, je nutné použít parametr celý název URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) namísto alias bitové kopie. Tento název URN musí odpovídat specifikace bitové kopie, odvozené z `az vm images list` příkaz.

## <a name="next-steps"></a>Další postup

[Nasazení šablon pomocí rozhraní příkazového řádku Azure](azure-stack-deploy-template-command-line.md)

[Povolit rozhraní příkazového řádku Azure pro uživatele Azure zásobníku (operátor)](..\azure-stack-cli-admin.md)

[Správa uživatelských oprávnění](azure-stack-manage-permissions.md)