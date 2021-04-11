---
title: Konfigurace testovacího prostředí pro použití Brána vzdálené plochy v Azure DevTest Labs
description: Naučte se, jak nakonfigurovat testovací prostředí v Azure DevTest Labs pomocí brány vzdálené plochy, která zajišťuje zabezpečený přístup k testovacím virtuálním počítačům bez nutnosti vystavit port RDP.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b15d4d39199c1a30eae292ece67f4553b656f530
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639598"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurace testovacího prostředí v Azure DevTest Labs pro použití brány vzdálené plochy
V Azure DevTest Labs můžete nakonfigurovat bránu vzdálené plochy pro testovací prostředí, aby se zajistil zabezpečený přístup k virtuálním počítačům testovacího prostředí, aniž by bylo nutné vystavit port protokolu RDP. Testovací prostředí poskytuje centrální místo pro uživatele testovacího prostředí pro zobrazení a připojení ke všem virtuálním počítačům, ke kterým mají přístup. Tlačítko **připojit** na stránce **virtuální počítač** vytvoří soubor RDP specifický pro počítač, který můžete otevřít pro připojení k počítači. Připojení RDP můžete dál upravovat a zabezpečovat tak, že testovací prostředí připojíte k bráně vzdálené plochy. 

Tento přístup je bezpečnější, protože se uživatel testovacího prostředí ověřuje přímo na počítači brány nebo může použít přihlašovací údaje společnosti na počítači brány připojeném k doméně pro připojení ke svým počítačům. Testovací prostředí také podporuje ověřování pomocí tokenu pro počítač brány, který umožňuje uživatelům připojit se ke svým testovacím virtuálním počítačům, aniž by byl port RDP přístupný na internetu. V tomto článku se dozvíte, jak nastavit testovací prostředí, které používá ověřování pomocí tokenu pro připojení k testovacím počítačům.

## <a name="architecture-of-the-solution"></a>Architektura řešení

![Architektura řešení](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Akce [získat obsah souboru RDP](/rest/api/dtl/virtualmachines/getrdpfilecontents) se zavolá, když vyberete tlačítko **připojit** . 1. 
1. Akce získat obsah souboru protokolu RDP vyvolá `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` požadavek na ověřovací token.
    1. `{gateway-hostname}` je název hostitele brány zadaný na stránce **Nastavení testovacího prostředí** v Azure Portal. 
    1. `{lab-machine-name}` je název počítače, ke kterému se pokoušíte připojit.
    1. `{port-number}` je port, na kterém je nutné vytvořit připojení. Obvykle je tento port 3389. Pokud virtuální počítač testovacího prostředí používá funkci [sdílené IP adresy](devtest-lab-shared-ip.md) v DevTest Labs, port se liší.
1. Brána vzdálené plochy odloží volání `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` do funkce Azure, aby vygenerovala ověřovací token. Služba DevTest Labs automaticky obsahuje klíč funkce v hlavičce požadavku. Klíč funkce se uloží do trezoru klíčů testovacího prostředí. Název tohoto tajného klíče, který se má zobrazit jako **tajný klíč tokenu brány** na stránce **Nastavení testovacího** prostředí pro testovací prostředí.
1. Očekává se, že funkce Azure vrátí token pro ověřování tokenů založeného na certifikátech na počítači brány.  
1. Akce získat obsah souboru RDP pak vrátí úplný soubor RDP, včetně ověřovacích informací.
1. Soubor RDP otevřete pomocí upřednostňovaného programu pro připojení RDP. Pamatujte, že ne všechny programy připojení RDP podporují ověřování tokenu. Ověřovací token má datum vypršení platnosti nastavené aplikací Function App. Než vyprší platnost tokenu, vytvořte připojení k virtuálnímu počítači testovacího prostředí.
1. Jakmile počítač brány vzdálené plochy ověří token v souboru RDP, připojení se přepošle do vašeho testovacího počítače.

### <a name="solution-requirements"></a>Požadavky na řešení
Pro práci s funkcí ověřování tokenů DevTest Labs existuje několik požadavků na konfiguraci pro počítače brány, službu DNS (Domain Name Services) a funkce.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Požadavky na počítače brány vzdálené plochy
- Certifikát TLS/SSL musí být nainstalovaný na počítači brány pro zpracování provozu HTTPS. Certifikát musí odpovídat plně kvalifikovanému názvu domény nástroje pro vyrovnávání zatížení pro farmu brány nebo plně kvalifikovaného názvu domény samotného počítače, pokud existuje pouze jeden počítač. Certifikáty TLS/SSL nefungují na zástupných kartách.  
- Podpisový certifikát nainstalovaný na počítačích s bránou. Pomocí skriptu [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) vytvořte podpisový certifikát.
- Nainstalujte modul pro [připojení](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) k síti, který podporuje ověřování tokenu pro bránu Vzdálená plocha. Jedním z příkladů takového modulu je `RDGatewayFedAuth.msi` , který je součástí [System Center Virtual Machine Manager (VMM) imagí](/system-center/vmm/install-console?view=sc-vmm-1807&preserve-view=true). Další informace o produktu System Center najdete v [dokumentaci k nástroji System Center](/system-center/) a v [podrobnostech o cenách](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Server brány může zpracovávat požadavky vytvořené na `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` .

    Brána-hostname je plně kvalifikovaný název domény nástroje pro vyrovnávání zatížení farmy brány nebo plně kvalifikovaný název domény samotného počítače, pokud existuje jenom jeden počítač. `{lab-machine-name}`Je název testovacího počítače, ke kterému se pokoušíte připojit, a `{port-number}` port, na kterém bude vytvořeno připojení.  Ve výchozím nastavení je to port 3389.  Pokud ale virtuální počítač používá funkci [sdílené IP adresy](devtest-lab-shared-ip.md) v DevTest Labs, port se liší.
- Modul [žádosti o směrování aplikace](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) pro Internet Information Server (IIS) se dá použít k přesměrování `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` požadavků do funkce Azure, která zpracovává požadavek na získání tokenu pro ověření.


## <a name="requirements-for-azure-function"></a>Požadavky na funkci Azure Functions
Azure Functions zpracovává požadavek s formátem `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` a vrátí ověřovací token na základě stejného podpisového certifikátu nainstalovaného na počítačích brány. `{function-app-uri}`Je identifikátor URI, který se používá pro přístup k funkci. Klíč funkce se automaticky předává v hlavičce žádosti. Ukázkovou funkci naleznete v tématu [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) . 


## <a name="requirements-for-network"></a>Požadavky na síť

- DNS pro plně kvalifikovaný název domény, který je přidružený k certifikátu TLS/SSL nainstalovanému na počítačích brány, musí směrovat provoz na počítač brány nebo na nástroj pro vyrovnávání zatížení farmy počítačů brány.
- Pokud testovací počítač používá privátní IP adresy, musí existovat síťová cesta z počítače brány k testovacímu počítači, a to buď prostřednictvím sdílení stejné virtuální sítě, nebo pomocí partnerských virtuálních sítí.

## <a name="configure-the-lab-to-use-token-authentication"></a>Konfigurace testovacího prostředí pro použití ověřování pomocí tokenu 
V této části se dozvíte, jak nakonfigurovat testovací prostředí pro použití počítače brány vzdálené plochy, který podporuje ověřování pomocí tokenu. Tato část nepopisuje, jak nastavit samotnou farmu brány vzdálené plochy. Tyto informace najdete v části [Ukázka vytvoření brány vzdálené plochy](#sample-to-create-a-remote-desktop-gateway) na konci tohoto článku. 

Před aktualizací nastavení testovacího prostředí uložte klíč potřebný k úspěšnému provedení funkce a vraťte ověřovací token v trezoru klíčů testovacího prostředí. Hodnotu klíče funkce můžete získat na stránce **Správa** pro funkci v Azure Portal. Další informace o tom, jak uložit tajný klíč v trezoru klíčů, najdete v tématu [Přidání tajného klíče do Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault). Uložte název tajného kódu pro pozdější použití.

ID trezoru klíčů testovacího prostředí zjistíte spuštěním následujícího příkazu rozhraní příkazového řádku Azure: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Nakonfigurujte testovací prostředí tak, aby používalo ověřování tokenu pomocí těchto kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
1. V seznamu cvičení vyberte **testovací prostředí**.
1. Na stránce testovacího prostředí vyberte **Konfigurace a zásady**.
1. V nabídce vlevo v části **Nastavení** vyberte **Nastavení testovacího prostředí**.
1. V části **Vzdálená plocha** zadejte plně kvalifikovaný název domény (FQDN) nebo IP adresu počítače nebo farmy brány vzdálené plochy pro pole **název hostitele brány** . Tato hodnota se musí shodovat s plně kvalifikovaným názvem domény certifikátu TLS/SSL, který se používá na počítačích brány.

    ![Možnosti vzdálené plochy v nastavení testovacího prostředí](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. V části **Vzdálená plocha** pro tajný klíč **tokenu brány** zadejte název tajného klíče, který jste vytvořili dříve. Tato hodnota není samotný klíč funkce, ale název tajného klíče v trezoru klíčů testovacího prostředí, který obsahuje klíč funkce.

    ![Tajný klíč tokenu brány v nastavení testovacího prostředí](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Uložit** Provedeny.

    > [!NOTE] 
    > Kliknutím na **Uložit** vyjadřujete souhlas s [licenčními podmínkami Brána vzdálené plochy](https://www.microsoft.com/licensing/product-licensing/products). Další informace o vzdálené bráně najdete v tématu [Vítá vás služba Vzdálená plocha](/windows-server/remote/remote-desktop-services/Welcome-to-rds) a [nasazení prostředí vzdálené plochy](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Pokud upřednostňujete konfiguraci testovacího prostředí prostřednictvím automatizace, přečtěte si téma [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) ukázkový skript PowerShellu pro nastavení **názvu hostitele brány** a **tajného klíče tokenu brány** . [Úložiště GitHub Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) taky poskytuje šablonu Azure Resource Manager, která vytvoří nebo aktualizuje testovací prostředí s nastavením **názvu hostitele brány** a **tajného klíče tokenu brány** .

## <a name="configure-network-security-group"></a>Konfigurovat skupinu zabezpečení sítě
K dalšímu zabezpečení testovacího prostředí je možné přidat skupinu zabezpečení sítě (NSG) do virtuální sítě používané virtuálními počítači testovacího prostředí. Pokyny, jak nastavit NSG, najdete v tématu [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md).

Tady je příklad NSG, který umožňuje jenom provoz, který nejdřív projde bránou, aby se dosáhlo testovacích počítačů. Zdrojem v tomto pravidle je IP adresa počítače s jedinou bránou nebo IP adresa nástroje pro vyrovnávání zatížení před počítači brány.

![Skupina zabezpečení sítě – pravidla](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Ukázka vytvoření brány vzdálené plochy

> [!NOTE] 
> Pomocí vzorových šablon souhlasíte s [licenčními podmínkami Brána vzdálené plochy](https://www.microsoft.com/licensing/product-licensing/products). Další informace o vzdálené bráně najdete v tématu [Vítá vás služba Vzdálená plocha](/windows-server/remote/remote-desktop-services/Welcome-to-rds) a [nasazení prostředí vzdálené plochy](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

[Úložiště GitHub Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) nabízí několik ukázek, které vám pomůžou nastavit prostředky potřebné k použití ověřování tokenu a brány vzdálené plochy s DevTest Labs. Tyto ukázky zahrnují šablony Azure Resource Manager pro počítače brány, nastavení testovacího prostředí a aplikace Function App.

Pomocí těchto kroků můžete nastavit ukázkové řešení pro farmu služby Brána vzdálené plochy.

1. Vytvořte podpisový certifikát.  Spusťte [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Uložte kryptografický otisk, heslo a kódování Base64 vytvořeného certifikátu.
2. Získejte certifikát TLS/SSL. Plně kvalifikovaný název domény přidružený k certifikátu TLS/SSL musí být pro doménu, kterou ovládáte. Uložte kryptografický otisk, heslo a kódování Base64 pro tento certifikát. Chcete-li získat kryptografický otisk pomocí prostředí PowerShell, použijte následující příkazy.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import('path-to-certificate');
    $hash = $cer.GetCertHashString()
    ```

    Pokud chcete získat kódování Base64 pomocí PowerShellu, použijte následující příkaz.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes('path-to-certificate'))
    ```
3. Stáhnout soubory z [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) .

    Šablona vyžaduje přístup k několika jiným Správce prostředků šablonám a souvisejícím prostředkům na stejném základním identifikátoru URI. Zkopírujte všechny soubory z [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) a RDGatewayFedAuth.msi do kontejneru objektů BLOB v účtu úložiště.  
4. Nasazení **azuredeploy.js** z [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) . Šablona používá následující parametry:
    - adminUsername – povinné.  Uživatelské jméno správce pro počítače brány.
    - adminPassword – povinný. Heslo pro účet správce pro počítače brány.
    - instanceCount – počet počítačů brány, které se mají vytvořit.  
    - alwaysOn – určuje, jestli se má vytvořená Azure Functions aplikace uchovávat v neteplém stavu. Udržování aplikace Azure Functions zabrání prodlevám, když se uživatel poprvé pokusí připojit ke svému testovacímu virtuálnímu počítači, ale má vliv na náklady.  
    - tokenLifetime – doba, po kterou bude vytvořený token platný Formát je HH: MM: SS.
    - sslCertificate – kódování Base64 certifikátu TLS/SSL pro počítač brány.
    - sslCertificatePassword – heslo certifikátu TLS/SSL pro počítač brány.
    - sslCertificateThumbprint – kryptografický otisk certifikátu pro identifikaci v místním úložišti certifikátů certifikátu TLS/SSL.
    - signCertificate – kódování Base64 pro podpisový certifikát pro počítač brány.
    - signCertificatePassword – heslo pro podpisový certifikát pro počítač brány.
    - signCertificateThumbprint – kryptografický otisk certifikátu pro identifikaci v místním úložišti certifikátů podpisového certifikátu.
    - _artifactsLocation – umístění identifikátoru URI, kde se dají najít všechny podpůrné prostředky. Tato hodnota musí být plně kvalifikovaný UIR, ne relativní cesta.
    - _artifactsLocationSasToken – token sdíleného přístupového podpisu (SAS), který se používá pro přístup k podpůrným prostředkům, pokud se jedná o účet úložiště Azure.

    Šablonu je možné nasadit pomocí Azure CLI pomocí následujícího příkazu:

    ```azurecli
    az deployment group create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Tady jsou popisy parametrů:

    - Modul {Storage-Account-Endpoint} lze získat spuštěním `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` .  Pole {Storage-ACCT-Name} je název účtu úložiště, který obsahuje soubory, které jste nahráli.  
    - {Container-Name} je název kontejneru v úložišti {Storage-ACCT-Name}, který obsahuje soubory, které jste nahráli.  
    - Rozhraní {SAS-token} se dá získat spuštěním `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` . 
        - Pole {Storage-ACCT-Name} je název účtu úložiště, který obsahuje soubory, které jste nahráli.  
        - {Container-Name} je název kontejneru v úložišti {Storage-ACCT-Name}, který obsahuje soubory, které jste nahráli.  
        - {UTC-vypršení platnosti} je datum ve standardu UTC, na kterém vyprší platnost tokenu SAS, a token SAS se už nebude moct pro přístup k účtu úložiště použít.

    Zaznamenejte hodnoty pro gatewayFQDN a gatewayIP z výstupu nasazení šablony. Také je nutné uložit hodnotu funkčního klíče pro nově vytvořenou funkci, která je k dispozici na kartě [nastavení aplikace Function App](../azure-functions/functions-how-to-use-azure-function-app-settings.md) .
5. Nakonfigurujte DNS tak, aby se plně kvalifikovaný název domény certifikátu TLS/SSL směroval na IP adresu gatewayIP z předchozího kroku.

    Po vytvoření farmy Brána vzdálené plochy a provedení příslušných aktualizací DNS je budete moct používat v laboratoři v DevTest Labs. Nastavení tajného kódu **hostitele brány** a **tajného klíče tokenu brány** musí být nakonfigurované na používání počítačů s bránou, které jste nasadili. 

    > [!NOTE]
    > Pokud testovací počítač používá privátní IP adresy, musí existovat síťová cesta z počítače brány k testovacímu počítači, a to buď prostřednictvím sdílení stejné virtuální sítě, nebo pomocí partnerské virtuální sítě.

    Po nakonfigurování brány i testovacího prostředí budou soubory připojení vytvořené, když uživatel testovacího prostředí klikne na **připojit** , automaticky zahrnovat informace potřebné k připojení pomocí ověření tokenu.     

## <a name="next-steps"></a>Další kroky
Další informace o službě Vzdálená plocha najdete v následujícím článku. [dokumentace ke službě Vzdálená plocha](/windows-server/remote/remote-desktop-services/Welcome-to-rds)