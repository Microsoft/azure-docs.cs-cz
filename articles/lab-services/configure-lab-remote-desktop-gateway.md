---
title: Konfigurace testovacího prostředí pro použití brány vzdálené plochy v laboratořích Azure DevTest Labs
description: Zjistěte, jak nakonfigurovat testovací prostředí v Azure DevTest Labs s bránou vzdálené plochy, abyste zajistili zabezpečený přístup k virtuálním počítačům testovacího prostředí, aniž byste museli vystavit port RDP.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2cdafa9a36a5f906151ca6946e18ef82bc7f1e01
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529425"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurace testovacího prostředí v Azure DevTest Labs tak, aby používala bránu vzdálené plochy
V Azure DevTest Labs můžete nakonfigurovat bránu vzdálené plochy pro vaše testovací prostředí, abyste zajistili zabezpečený přístup k virtuálním počítačům testovacího prostředí( VM), aniž byste museli vystavit port RDP. Testovací prostředí poskytuje centrální místo pro uživatele testovacího prostředí pro zobrazení a připojení ke všem virtuálním počítačům, ke kterým mají přístup. Tlačítko **Připojit** na stránce **Virtuální počítač** vytvoří soubor RDP specifický pro počítač, který můžete otevřít pro připojení k počítači. Připojení testovacího prostředí k bráně vzdálené plochy můžete dále přizpůsobit a zabezpečit. 

Tento přístup je bezpečnější, protože uživatel testovacího prostředí se ověřuje přímo do počítače brány nebo může použít firemní pověření v počítači brány přilehlé md domény pro připojení k jejich počítačům. Testovací prostředí také podporuje použití ověřování tokenů pro počítač brány, který umožňuje uživatelům připojit se k jejich virtuálním počítačům testovacího prostředí bez vystavení portu RDP internetu. Tento článek vás provede příkladem, jak nastavit testovací prostředí, které používá ověřování tokenů pro připojení k testovacím počítačům.

## <a name="architecture-of-the-solution"></a>Architektura řešení

![Architektura řešení](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Akce [Získat obsah souboru RDP](/rest/api/dtl/virtualmachines/getrdpfilecontents) je volána, když vyberete tlačítko **Připojit.1.** 
1. Akce Získat obsah souboru `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` PROTOKOLU RDP vyvolá žádost o ověřovací token.
    1. `{gateway-hostname}`je název hostitele brány zadaný na stránce **Nastavení testovacího prostředí** pro vaše testovací prostředí na webu Azure Portal. 
    1. `{lab-machine-name}`je název počítače, který se pokoušíte připojit.
    1. `{port-number}`je port, na kterém je třeba navázat připojení. Obvykle je tento port 3389. Pokud virtuální virtuální soud testovacího prostředí používá [sdílenou](devtest-lab-shared-ip.md) funkci IP v devtest labs, port se bude lišit.
1. Brána vzdálené plochy odloží volání z `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` funkce Azure a vygeneruje ověřovací token. Služba DevTest Labs automaticky zahrnuje funkční klávesu v hlavičce požadavku. Funkční klávesa má být uložena v trezoru klíčů v laboratoři. Název tohoto tajného klíče, který se zobrazí jako **tajný klíč tokenu brány** na stránce Nastavení **testovacího prostředí** pro testovací prostředí.
1. Očekává se, že funkce Azure vrátí token pro ověřování tokenů založených na certifikátech proti počítači brány.  
1. Akce Získat obsah souboru PROTOKOLU RDP pak vrátí celý soubor RDP, včetně ověřovacích informací.
1. Soubor RDP otevřete pomocí upřednostňovaného programu připojení RDP. Nezapomeňte, že ne všechny programy připojení RDP podporují ověřování tokenů. Ověřovací token má datum vypršení platnosti nastavené aplikací funkce. Vytvořte připojení k virtuálnímu virtuálnímu virtuálnímu soudu testovacího prostředí před vypršením platnosti tokenu.
1. Jakmile počítač brány vzdálené plochy ověří token v souboru RDP, připojení se přenese do počítače testovacího prostředí.

### <a name="solution-requirements"></a>Požadavky na řešení
Pro práci s funkcí ověřování tokenů DevTest Labs existuje několik konfiguračních požadavků pro počítače brány, služby dns (domain name services) a funkce.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Požadavky na počítače brány vzdálené plochy
- Certifikát TLS/SSL musí být nainstalován v počítači brány, aby bylo možné zpracovávat přenosy HTTPS. Certifikát musí odpovídat plně kvalifikovanému názvu domény (Plně kvalifikovaný název) pro vyrovnávání zatížení pro farmu brány nebo plně kvalifikovaný název domény samotného počítače, pokud existuje pouze jeden počítač. Certifikáty TLS/SSL s divokou kartou nefungují.  
- Podpisový certifikát nainstalovaný v počítačích brány. Vytvořte podpisový certifikát pomocí [skriptu Create-SigningCertificate.ps1.](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)
- Nainstalujte modul [Pluggable Authentication,](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) který podporuje ověřování tokenů pro bránu vzdálené plochy. Jedním z příkladů takového `RDGatewayFedAuth.msi` modulu je, že je dodáván s [Image System Center Virtual Machine Manager (VMM).](/system-center/vmm/install-console?view=sc-vmm-1807) Další informace o centru systému naleznete v [dokumentaci k centru systému](https://docs.microsoft.com/system-center/) a [podrobnosti o cenách](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Server brány může zpracovávat `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`požadavky na soubor .

    Název hostitele brány je hlavní název sítě pro vyrovnávání zatížení vyvyřič u brány farmy nebo fQDN samotného počítače, pokud existuje pouze jeden počítač. Je `{lab-machine-name}` název testovacího počítače, který se pokoušíte připojit, a `{port-number}` je port, na kterém bude připojení.  Ve výchozím nastavení je tento port 3389.  Pokud však virtuální počítač používá [sdílenou](devtest-lab-shared-ip.md) funkci IP v devtest labs, port se bude lišit.
- Modul [Žádosti o směrování aplikací](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) pro internetový informační server (IIS) lze použít k přesměrování `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` požadavků na funkci azure, která zpracovává požadavek na získání tokenu pro ověřování.


## <a name="requirements-for-azure-function"></a>Požadavky na funkci Azure
Azure funkce zpracovává požadavek `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` s formátem a vrátí ověřovací token na základě stejného podpisového certifikátu nainstalovaného na počítačích brány. Je `{function-app-uri}` uri slouží k přístupu k funkci. Funkční klávesa je automaticky předána v záhlaví požadavku. Funkce vzorku naleznete [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)v tématu . 


## <a name="requirements-for-network"></a>Požadavky na síť

- Služba DNS pro hlavní název domény přidružené k certifikátu TLS/SSL nainstalovanému na počítačích brány musí směrovat provoz do počítače brány nebo do nástrojpro vyrovnávání zatížení farmy počítače brány.
- Pokud testovací počítač používá privátní IP adresy, musí existovat síťová cesta z počítače brány do počítače testovacího prostředí, a to buď prostřednictvím sdílení stejné virtuální sítě, nebo pomocí partnerských virtuálních sítí.

## <a name="configure-the-lab-to-use-token-authentication"></a>Konfigurace testovacího prostředí tak, aby používalo ověřování tokenů 
Tato část ukazuje, jak nakonfigurovat testovací prostředí tak, aby používalo počítač brány vzdálené plochy, který podporuje ověřování tokenů. Tato část nepopisuje, jak nastavit samotnou farmu brány vzdálené plochy. Tyto informace naleznete v [části Ukázka pro vytvoření](#sample-to-create-a-remote-desktop-gateway) části brány vzdálené plochy na konci tohoto článku. 

Před aktualizací nastavení testovacího prostředí uložte klíč potřebný k úspěšnému spuštění funkce a vrátit ověřovací token do trezoru klíčů testovacího prostředí. Hodnotu funkčního klíče můžete získat na stránce **Spravovat** pro funkci na webu Azure Portal. Další informace o uložení tajného klíče do trezoru klíčů naleznete v [tématu Přidání tajného klíče do trezoru klíčů](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault). Uložte název tajného klíče pro pozdější použití.

Pokud chcete najít ID trezoru klíčů testovacího prostředí, spusťte následující příkaz k příkazu Azure CLI: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Nakonfigurujte testovací prostředí tak, aby používalo ověřování tokenů pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
1. Ze seznamu testovacích prostředí vyberte **testovací prostředí**.
1. Na stránce testovacího prostředí vyberte **Konfigurace a zásady**.
1. V levé nabídce vyberte v části **Nastavení** **nastavení laboratoře**.
1. V části **Vzdálená plocha** zadejte plně kvalifikovaný název domény (Plně kvalifikovaný název domény) nebo IP adresu počítače brány nebo farmy služby Vzdálená plocha pro pole **Název hostitele brány.** Tato hodnota se musí shodovat s vícenežvitnými názevmi sítě certifikátu TLS/SSL používaného v počítačích brány.

    ![Možnosti vzdálené plochy v nastavení testovacího prostředí](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. V části **Vzdálená plocha** pro tajný klíč **tokenu brány** zadejte název tajného klíče vytvořeného dříve. Tato hodnota není samotný klíč funkce, ale název tajného klíče v trezoru klíčů testovacího prostředí, který obsahuje funkční klíč.

    ![Tajný klíč tokenu brány v nastavení testovacího prostředí](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Uložit** Změny.

    > [!NOTE] 
    > Kliknutím na **uložit**souhlasíte s [licenčními podmínkami služby Remote Desktop Gateway](https://www.microsoft.com/licensing/product-licensing/products). Další informace o vzdálené bráně naleznete [v tématu Vítá vás služba Vzdálená plocha](https://aka.ms/rds) a [Nasazení prostředí vzdálené plochy](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Pokud je upřednostňována konfigurace testovacího prostředí prostřednictvím automatizace, najdete v tématu [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) ukázkový skript PowerShell u nastavení **tajného** názvu hostitele brány a **tokenu brány.** [Úložiště GitHub Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) také poskytuje šablonu Azure Resource Manager, která vytvoří nebo aktualizuje testovací prostředí s **názvem hostitele brány** a nastavením tajného klíče **tokenu brány.**

## <a name="configure-network-security-group"></a>Konfigurace skupiny zabezpečení sítě
K dalšímu zabezpečení testovacího prostředí lze do virtuální sítě používané virtuálními počítači testovacího prostředí přidat skupinu zabezpečení sítě (NSG). Pokyny k nastavení skupiny síťových připojení naleznete v tématu [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md).

Zde je příklad nsg, který umožňuje pouze provoz, který nejprve prochází bránou k dosažení testovacích počítačů. Zdrojem tohoto pravidla je IP adresa počítače s jednou bránou nebo IP adresa vykladače zatížení před počítače brány.

![Skupina zabezpečení sítě – pravidla](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Ukázka vytvoření brány vzdálené plochy

> [!NOTE] 
> Pomocí ukázkových šablon souhlasíte s [licenčními podmínkami služby Remote Desktop Gateway](https://www.microsoft.com/licensing/product-licensing/products). Další informace o vzdálené bráně naleznete [v tématu Vítá vás služba Vzdálená plocha](https://aka.ms/rds) a [Nasazení prostředí vzdálené plochy](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

Úložiště [GitHub Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) poskytuje několik ukázek, které vám pomůžou nastavit prostředky potřebné k použití ověřování tokenů a brány vzdálené plochy s DevTest Labs. Tyto ukázky zahrnují šablony Azure Resource Manager pro počítače brány, nastavení testovacího prostředí a aplikace funkce.

Podle těchto kroků nastavte ukázkové řešení pro farmu brány vzdálené plochy.

1. Vytvořte podpisový certifikát.  [Spusťte soubor Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Uložte kryptografický otisk, heslo a kódování vytvořeného certifikátu Base64.
2. Získejte certifikát TLS/SSL. Vícenežonislý název domény přidružený k certifikátu TLS/SSL musí být určen pro doménu, kterou ovládáte. Uložte kryptografický otisk, heslo a kódování Base64 pro tento certifikát. Chcete-li získat kryptografický otisk pomocí Prostředí PowerShell, použijte následující příkazy.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Chcete-li získat kódování Base64 pomocí PowerShellu, použijte následující příkaz.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Stahování souborů [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)z .

    Šablona vyžaduje přístup k několika dalším šablonám Správce prostředků a souvisejícím prostředkům na stejném základním identifikátoru URI. Zkopírujte všechny [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) soubory z rdgatewayFedAuth.msi do kontejneru objektů blob v účtu úložiště.  
4. Nasazení **souboru azuredeploy.json** z aplikace [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). Šablona má následující parametry:
    - adminUsername – povinné.  Uživatelské jméno správce pro počítače brány.
    - adminPassword – povinné. Heslo pro účet správce pro počítače brány.
    - instanceCount – počet počítačů brány, které chcete vytvořit.  
    - alwaysOn – označuje, zda chcete zachovat vytvořené aplikace Azure Functions v teplém stavu nebo ne. Udržování aplikace Azure Functions zabrání zpoždění při uživatelé se poprvé pokusí připojit k jejich virtuálnípočítač testovacího prostředí, ale má náklady důsledky.  
    - tokenLifetime – doba, po kterou bude vytvořený token platný. Formát je HH:MM:SS.
    - sslCertificate – Base64 kódování certifikátu TLS/SSL pro stroj brány.
    - sslCertificatePassword – heslo certifikátu TLS/SSL pro stroj brány.
    - sslCertificateThumbprint - Kryptografický otisk certifikátu pro identifikaci v místním úložišti certifikátů certifikátu TLS/SSL.
    - signCertificate – Kódování Base64 pro podpisový certifikát pro počítač brány.
    - signCertificatePassword – heslo pro podpisový certifikát pro počítač brány.
    - signCertificateThumbprint - Kryptografický otisk certifikátu pro identifikaci v místním úložišti certifikátů podpisového certifikátu.
    - _artifactsLocation – umístění identifikátoru URI, kde lze nalézt všechny podpůrné prostředky. Tato hodnota musí být plně kvalifikované UIR, nikoli relativní cestu.
    - _artifactsLocationSasToken – token sdíleného přístupového podpisu (SAS) používaný pro přístup k podpůrným prostředkům, pokud je umístění mnoství mj.

    Šablonu lze nasadit pomocí příkazového příkazu Azure CLI pomocí následujícího příkazu:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Zde jsou popisy parametrů:

    - {storage-account-endpoint} lze získat spuštěním `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`.  {storage-acct-name} je název účtu úložiště, který obsahuje soubory, které jste nahráli.  
    - {container-name} je název kontejneru v {storage-acct-name}, který obsahuje soubory, které jste nahráli.  
    - {sas-token} lze získat spuštěním `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`. 
        - {storage-acct-name} je název účtu úložiště, který obsahuje soubory, které jste nahráli.  
        - {container-name} je název kontejneru v {storage-acct-name}, který obsahuje soubory, které jste nahráli.  
        - {utc-expiration-date} je datum v utc, kdy vyprší platnost tokenu SAS a token SAS již nelze použít pro přístup k účtu úložiště.

    Zaznamenejte hodnoty pro gatewayFQDN a gatewayIP z výstupu nasazení šablony. Budete také muset uložit hodnotu funkční klávesy pro nově vytvořenou funkci, kterou najdete na kartě [Nastavení aplikace Funkce.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
5. Nakonfigurujte službu DNS tak, aby reqdn certifikátu TLS/SSL přesměroval na IP adresu gatewayIP z předchozího kroku.

    Po vytvoření farmy brány vzdálené plochy a vytvoření příslušných aktualizací DNS je připravena k použití testovacím prostředím v devtest labs. Nastavení **tajného klíče názvu hostitele brány** a **tokenu brány** musí být nakonfigurováno tak, aby používalo počítače brány, které jste nasadili. 

    > [!NOTE]
    > Pokud testovací počítač používá privátní IP adresy, musí existovat síťová cesta z počítače brány do počítače testovacího prostředí, a to buď prostřednictvím sdílení stejné virtuální sítě, nebo pomocí partnerské virtuální sítě.

    Po konfiguraci brány i testovacího prostředí **bude** soubor připojení vytvořený kliknutím na připojení testovacího prostředí automaticky obsahovat informace potřebné k připojení pomocí ověřování tokenů.     

## <a name="next-steps"></a>Další kroky
Další informace o službě Vzdálená plocha: [Dokumentace ke službě Vzdálená plocha](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


