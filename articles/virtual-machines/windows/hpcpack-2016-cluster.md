---
title: Cluster HPC Pack 2016 v Azure | Microsoft Docs
description: "Postup nasazení clusteru HPC Pack 2016 v Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 03/09/2018
ms.author: danlep
ms.openlocfilehash: c26dd85d896445e19efb9906d953fd535fc1fb5c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Nasazení clusteru HPC Pack 2016 v Azure

Postupujte podle kroků v tomto článku k nasazení [Microsoft HPC Pack 2016 Update 1](https://technet.microsoft.com/library/cc514029) clusteru ve virtuálních počítačích Azure. HPC Pack je společnosti Microsoft volné HPC řešení založen na technologiích Microsoft Azure a Windows Server a podporuje úlohy HPC široký rozsah.

Použijte jednu z [šablon Azure Resource Manageru](https://github.com/MsHpcPack/HPCPack2016) k nasazení clusteru HPC Pack 2016. Máte několik možností topologie clusteru s různá čísla a typy clusteru hlavní uzly a výpočetní uzly.

## <a name="prerequisites"></a>Požadavky

### <a name="pfx-certificate"></a>Certifikát PFX

Cluster s podporou Microsoft HPC Pack 2016 vyžaduje certifikát Personal Information Exchange (PFX) k zabezpečení komunikace mezi uzly HPC. Certifikát musí splňovat následující požadavky:

* Musí mít privátní klíč umožňuje výměnu klíčů
* Použití klíče zahrnuje digitální podpis a šifrování klíče
* Rozšířené použití klíče obsahuje ověření klienta a ověření serveru

Pokud již nemáte certifikát, který splňuje tyto požadavky, můžete požádat o certifikát od certifikační autority. Můžete taky použijte následující příkazy ke generování certifikát podepsaný svým držitelem, který je založený na operačním systému, na kterém jste spustili příkaz. Poté exportujte certifikát jako soubor PFX chráněný heslem s privátním klíčem.

* **Pro Windows 10 nebo Windows Server 2016**, spusťte předdefinovaný **New-SelfSignedCertificate** rutiny prostředí PowerShell následujícím způsobem:

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **Pro operační systémy starší než Windows 10 nebo Windows Server 2016**, stáhněte si [certifikát podepsaný svým držitelem generátor](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) z webu Microsoft Script Center. Rozbalte jeho obsah a spusťte následující příkazy v příkazovém řádku prostředí PowerShell:

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

Po vytvoření certifikátu v úložišti aktuálního uživatele pomocí modulu snap-in Certifikáty a vyexportovat si certifikát jako soubor PFX chráněný heslem s privátním klíčem. Můžete také exportovat certifikát pomocí [Export Pfxcertificate](/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) rutiny prostředí PowerShell.

### <a name="upload-certificate-to-an-azure-key-vault"></a>Nahrajte certifikát do trezoru služby Azure klíče

Před nasazením clusteru HPC, nahrajte certifikát PFX, který chcete [Azure trezoru klíčů](../../key-vault/index.md) jako tajný klíč a záznam následující informace pro použití při nasazení: **název trezoru**, **trezoru Skupina prostředků**, **adresa URL certifikátu**, a **kryptografický otisk certifikátu**.

Následuje ukázkový skript Powershellu pro nahrání certifikátu, vytvoření trezoru klíčů a generovat požadované informace. Další informace o certifikátu se nahrávají na klíče trezoru služby Azure najdete v tématu [Začínáme s Azure Key Vault](../../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Podporované topologie

Vyberte jednu z [šablon Azure Resource Manageru](https://github.com/MsHpcPack/HPCPack2016) k nasazení clusteru HPC Pack 2016. Následují základní architektury tři příklad topologií clusteru. Topologie vysoké dostupnosti zahrnují více hlavních uzlech clusteru.

1. Vysoká dostupnost clusteru s doménou služby Active Directory

    ![Clusteru HA v doméně AD](./media/hpcpack-2016-cluster/haad.png)


2. Vysoká dostupnost clusteru bez domény služby Active Directory

    ![Clusteru HA bez domény služby AD](./media/hpcpack-2016-cluster/hanoad.png)

3. Cluster s jeden hlavní uzel

   ![Cluster s jeden hlavní uzel](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Nasazení clusteru

Pokud chcete vytvořit cluster, vyberte šablonu a klikněte na tlačítko **nasadit do Azure**. V [portál Azure](https://portal.azure.com), zadejte parametry šablony, jak je popsáno v následujících krocích. Každá šablona vytvoří všechny prostředky Azure potřebné pro infrastruktura clusteru HPC. Prostředky zahrnují virtuální síť Azure, veřejné IP adresy, Vyrovnávání zatížení (pouze pro vysokou dostupnost clusteru), síťová rozhraní, skupiny dostupnosti, účty úložiště a virtuálních počítačů.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>Krok 1: Vyberte předplatné, umístění a skupiny prostředků

**Předplatné** a **umístění** musí být stejná, kterou jste zadali, když jste nahráli vašeho certifikátu PFX (viz požadavky). Doporučujeme, abyste vytvořili jiné **skupiny prostředků** pro nasazení.

### <a name="step-2-specify-the-parameter-settings"></a>Krok 2: Zadejte nastavení parametru

Zadejte nebo upravte hodnoty pro parametry šablony. Klikněte na ikonu vedle jednotlivých parametrů pro informace nápovědy. Viz také pokyny pro [dostupných velikostí virtuálních počítačů](sizes.md).

Zadejte hodnoty, které jste si poznamenali v požadavky pro tyto parametry: **název trezoru**, **skupiny prostředků trezoru**, **adresa URL certifikátu**, a **kryptografický otisk certifikátu**.

### <a name="step-3-review-terms-and-create"></a>Krok 3. Přečtěte si podmínky a vytvořit
Přečtěte si podmínky a ujednání, které jsou přidružené k šabloně. Pokud souhlasíte, klikněte na tlačítko **nákupu** ke spuštění nasazení.

V závislosti na topologii clusteru, nasazení může trvat 30 minut nebo déle.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru
1. Po nasazení clusteru HPC Pack, přejděte na [portál Azure](https://portal.azure.com). Klikněte na tlačítko **skupiny prostředků**a najít skupinu prostředků, ve kterém byl nasazen clusteru. Můžete získat virtuální počítače hlavního uzlu.

    ![Head uzly clusteru na portálu](./media/hpcpack-2016-cluster/clusterhns.png)

2. Klikněte na jeden hlavní uzel (v clusteru s podporou vysokou dostupností, klikněte na některé z hlavních uzlech). V **přehled**, můžete vyhledat veřejnou IP adresu nebo úplný název DNS clusteru.

    ![Nastavení připojení clusteru](./media/hpcpack-2016-cluster/clusterconnect.png)

3. Klikněte na tlačítko **Connect** chcete přihlásit k některé z hlavních uzlech pomocí vzdálené plochy s svoje uživatelské jméno zadané správce. Pokud jste nasadili cluster nachází v doméně služby Active Directory, je uživatelské jméno ve tvaru \<privateDomainName >\\\<adminUsername > (například hpc.local\hpcadmin).

## <a name="next-steps"></a>Další kroky
* Odesílání úloh do clusteru. V tématu [odeslání úlohy HPC HPC Pack clusteru v Azure](hpcpack-cluster-submit-jobs.md) a [spravovat cluster služby HPC Pack 2016 v Azure pomocí Azure Active Directory](hpcpack-cluster-active-directory.md).

