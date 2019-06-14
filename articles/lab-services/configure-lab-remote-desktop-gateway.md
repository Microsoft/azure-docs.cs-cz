---
title: Konfigurace testovacího prostředí pro použití služby Brána vzdálené plochy v Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat testovacího prostředí ve službě Azure DevTest Labs Brána vzdálené plochy zajistit zabezpečený přístup k testovacímu prostředí virtuálních počítačů, aniž byste museli zveřejnit RDP port.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 430734878c01d10a4e7dd385dc75d8d502a2d82c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078999"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurace testovacího prostředí v Azure DevTest Labs můžete pomocí služby Brána vzdálené plochy
Ve službě Azure DevTest Labs můžete nakonfigurovat Brána vzdálené plochy pro testovací prostředí k zajištění zabezpečeného přístupu k prostředí virtuálních počítačů (VM) bez nutnosti zveřejnit RDP port. Testovací prostředí poskytuje centrální místo, kde uživatelé testovacího prostředí zobrazit a připojit se k všechny virtuální počítače, které mají přístup k. **Připojit** tlačítko **virtuálního počítače** stránka vytvoří soubor protokolu RDP specifické pro počítač, který můžete otevřít pro připojení k počítači. Můžete dále přizpůsobit a zabezpečené připojení RDP propojíte testovacího prostředí služby Brána vzdálené plochy. 

Tento přístup je bezpečnější, protože testovací prostředí uživatele ověří přímo k počítači brány nebo pomocí přihlašovacích údajů společnosti na počítači připojeném k doméně brány pro připojení ke svým počítačům. Testovací prostředí podporuje také použití ověřování pomocí tokenu k počítači brány, který umožňuje uživatelům připojit se ke svým virtuálním počítačům testovacího prostředí bez nutnosti port RDP, který je přístupný z Internetu. Tento článek vás provede příkladem o tom, jak nastavení testovacího prostředí, která používá ověřování pomocí tokenu pro připojení k testovacích počítačů.

## <a name="architecture-of-the-solution"></a>Architektury řešení

![Architektury řešení](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. [Obsah souboru RDP získat](/rest/api/dtl/virtualmachines/getrdpfilecontents) akce je volána, když vyberete **připojit** button.1. 
1. Vyvolá akci obsah souboru RDP získat `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` k vyžádání tokenu ověření.
    1. `{gateway-hostname}` je zadaný v názvu hostitele brány **nastavení testovacího prostředí** stránky pro vaše testovací prostředí na webu Azure Portal. 
    1. `{lab-machine-name}` je název počítače, který se snažíte připojit.
    1. `{port-number}` je port, na kterém musí být přijata připojení. Obvykle je tento port 3389. Pokud testovací prostředí používá virtuální počítač [sdílených IP](devtest-lab-shared-ip.md) funkcí ve službě DevTest Labs, port, který se bude lišit.
1. Brána vzdálené plochy odloží volání z `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` k funkci Azure pro vygenerování ověřovacího tokenu. Služba DevTest Labs automaticky zahrnuje funkční klávesy v hlavičce požadavku. Funkční klávesy má být uložena ve službě key vault testovacího prostředí. Název pro tento tajný kód, který se zobrazí jako **tajný klíč tokenu služby brány** na **nastavení testovacího prostředí** stránky pro testovací prostředí.
1. Funkce Azure Functions se očekává navrácení token pro token ověřování pomocí certifikátů na počítači brány.  
1. Soubor RDP získat obsah akce a vrátí úplný soubor RDP, včetně informací o ověřování.
1. Otevřete soubor RDP pomocí upřednostňované programu připojení RDP. Mějte na paměti, že ne všechny programy připojení RDP podporují ověřování pomocí tokenu. Ověřovací token mají datum vypršení platnosti, nastavením aplikace function app. Vytvoření připojení k testovací virtuální počítač, před vypršením platnosti tokenu.
1. Jakmile počítač služby Brána vzdálené plochy se ověří token v souboru RDP, připojení je předán do počítače testovacího prostředí.

### <a name="solution-requirements"></a>Požadavky na řešení
Pro práci s funkcí ověřování pomocí tokenu DevTest Labs, existuje pár požadavky na konfiguraci pro počítače brány, služeb názvů domén (DNS) a funkce.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Požadavky na počítače se Brána vzdálené plochy
- Certifikát SSL musí být nainstalována na počítači brány pro zpracování komunikaci přes protokol HTTPS. Certifikát musí odpovídat plně kvalifikovaný název domény (FQDN) nástroje pro vyrovnávání zatížení pro farmy brány nebo plně kvalifikovaný název domény celý počítač, pokud existuje jenom jeden počítač. Zástupný znak – certifikáty SSL nefungují.  
- Podpisový certifikát nainstalovat na počítače brány. Vytvoření podpisový certifikát s použitím [vytvořit SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) skriptu.
- Nainstalujte [PAM](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) modul, který podporuje ověřování pomocí tokenu pro služby Brána vzdálené plochy. Příkladem takové modul je `RDGatewayFedAuth.msi` , který je součástí [System Center Virtual Machine Manager (VMM) imagí](/system-center/vmm/install-console?view=sc-vmm-1807). Další informace o produktu System Center najdete v tématu [dokumentace pro System Center](https://docs.microsoft.com/system-center/) a [podrobnosti o cenách](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Server brány dokáže zpracovat požadavky na `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`.

    Název hostitele brány se plně kvalifikovaný název domény nástroje pro vyrovnávání zatížení farmy brány nebo plně kvalifikovaný název domény počítače, vlastní, pokud existuje jenom jeden počítač. `{lab-machine-name}` Je název počítače testovacího prostředí, který se snažíte připojit, a `{port-number}` je port, na kterém bude proveden připojení.  Ve výchozím nastavení je tento port 3389.  Nicméně pokud virtuální počítač používá [sdílených IP](devtest-lab-shared-ip.md) funkcí ve službě DevTest Labs, port, který se bude lišit.
- [Směrování žádostí na aplikace](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) modulu pro Internet Information Server (IIS) je možné přesměrovat `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` žádosti o funkce azure Functions, která zpracovává požadavek na získání tokenu pro ověření.


## <a name="requirements-for-azure-function"></a>Požadavky pro Azure – funkce
Funkce Azure Functions obslužné rutiny žádosti s formátem `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` a vrátí token ověřování založené na stejné podpisový certifikát nainstalován v počítačích brány. `{function-app-uri}` Je identifikátor uri pro přístup k funkci. Klíč funkce je automaticky předávat v hlavičce požadavku. Ukázkové funkce, najdete v části [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs). 


## <a name="requirements-for-network"></a>Požadavky na síť

- Služba DNS pro plně kvalifikovaný název domény přidružený k certifikátu SSL nainstalovaném na počítačích bránu musí směrovat provoz na počítači brány nebo nástroje pro vyrovnávání zatížení počítače farmy brány.
- Pokud počítač testovacího prostředí používá privátní IP adresy, musí být síťová cesta z počítače brány k počítači testovacího prostředí, buď prostřednictvím sdílení stejné virtuální síti, nebo pomocí partnerských virtuálních sítích.

## <a name="configure-the-lab-to-use-token-authentication"></a>Konfigurace testovacího prostředí a pomocí ověření tokenu 
Tato část ukazuje postup při konfiguraci testovacího prostředí použít počítač služby Brána vzdálené plochy, který podporuje ověřování pomocí tokenu. Tato část nezahrnuje jak vytvořit farmu služby Brána vzdálené plochy, samotného. Informace najdete v tématu [vzorek k vytvoření služby Brána vzdálené plochy](#sample-to-create-a-remote-desktop-gateway) oddílu na konci tohoto článku. 

Než budete aktualizovat nastavení testovacího prostředí, uložení klíče potřebné k úspěšné spuštění funkce k vrácení tokenu ověření ve službě key vault testovacího prostředí. Hodnota klíče funkce můžete získat **spravovat** stránky pro funkce na webu Azure Portal. Další informace o tom, jak uložit tajného klíče v trezoru klíčů, naleznete v tématu [do služby Key Vault přidat tajný klíč](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault). Uložte název tajného klíče pro pozdější použití.

Pokud chcete najít ID služby key vault testovacího prostředí, spusťte následující příkaz rozhraní příkazového řádku Azure: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Konfigurace testovacího prostředí a ověřování tokenem pomocí těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí, vyberte vaše **lab**.
1. Na stránce testovacího prostředí, vyberte možnost **konfigurace a zásad**.
1. V nabídce vlevo v **nastavení** vyberte **nastavení testovacího prostředí**.
1. V **vzdálené plochy** části, zadejte plně kvalifikovaný název domény (FQDN) nebo IP adresu počítače brány vzdálené plochy nebo farmy pro **název hostitele brány** pole. Tato hodnota musí odpovídat plně kvalifikovaný název domény certifikát SSL používaný na počítače brány.

    ![Možnosti vzdáleného klasické pracovní plochy v nastavení testovacího prostředí](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. V **vzdálené plochy** části pro **token brány** tajný klíč, zadejte název tajného kódu vytvořili dříve. Tato hodnota není funkční klávesy, samotný, ale název tajného klíče v trezoru klíčů testovacího prostředí, který obsahuje klíč funkce.

    ![Tajný klíč tokenu brány v nastavení testovacího prostředí](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Uložit** změny.

    > [!NOTE] 
    > Kliknutím na **Uložit**, vyjadřujete souhlas s [Brána vzdálené plochy na licenční podmínky](https://www.microsoft.com/licensing/product-licensing/products). Další informace o vzdálené brány najdete v tématu [Vítá vás služba Vzdálená plocha](https://aka.ms/rds) a [nasazení prostředí vzdálené plochy](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Konfigurace testovacího prostředí pomocí automatizace, je-li zobrazit [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) vzorový skript prostředí PowerShell k nastavení **název hostitele brány** a **tajný klíč tokenu brány**nastavení. [Úložiště Azure DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) poskytuje také šablonu Azure Resource Manageru, která vytvoří nebo aktualizuje testovacího prostředí se **název hostitele brány** a **tajný klíč tokenu brány**nastavení.

## <a name="configure-network-security-group"></a>Nakonfigurovat skupinu zabezpečení sítě
Zabezpečit ještě víc, testovací prostředí, můžete přidat skupinu zabezpečení sítě (NSG) k virtuální síti, které jsou používány virtuálními počítači testovacího prostředí. Pokyny k nastavení skupiny zabezpečení sítě, naleznete v tématu [vytvořit, změnit nebo odstranit skupinu zabezpečení sítě](../virtual-network/manage-network-security-group.md).

Tady je příklad NSG, která povoluje jenom provoz, který nejprve prochází přes bránu pro dosažení testovacích počítačů. Zdroj v toto pravidlo je na IP adresu počítače jednu bránu nebo IP adresu nástroje pro vyrovnávání zatížení před počítače brány.

![Skupina zabezpečení sítě – pravidla](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Vzorek k vytvoření služby Brána vzdálené plochy

> [!NOTE] 
> Pomocí ukázkových šablon, vyjadřujete souhlas s [Brána vzdálené plochy na licenční podmínky](https://www.microsoft.com/licensing/product-licensing/products). Další informace o vzdálené brány najdete v tématu [Vítá vás služba Vzdálená plocha](https://aka.ms/rds) a [nasazení prostředí vzdálené plochy](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

[Úložiště Azure DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) najdete několik vzorových usnadňují instalační program prostředky potřebné pro použití ověřování pomocí tokenu a služby Brána vzdálené plochy s DevTest Labs. Tyto ukázky zahrnují šablony Azure Resource Manageru pro počítače brány, nastavení testovacího prostředí a aplikace function app.

Postupujte podle těchto pokynů nastavte si ukázkové řešení pro farmu služby Brána vzdálené plochy.

1. Vytvoření podpisového certifikátu.  Spustit [vytvořit SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Uložte kryptografický otisk, heslo a kódování Base64 vytvořeného certifikátu.
2. Získejte certifikát SSL. Plně kvalifikovaný název domény přidružený certifikát SSL musí být pro doménu, kterou řídíte. Uložte kryptografický otisk, heslo a tento certifikát kódování Base64. Pokud chcete získat kryptografický otisk pomocí Powershellu, použijte následující příkazy.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Chcete-li získat kódování Base64, pomocí Powershellu, použijte následující příkaz.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Stažení souborů ze [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway).

    Šablona vyžaduje přístup k několika šablon Resource Manageru a související prostředky na stejný základní identifikátor URI. Zkopírujte všechny soubory z [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) a RDGatewayFedAuth.msi na kontejner objektů blob v účtu úložiště.  
4. Nasazení **azuredeploy.json** z [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). Šablony mají následující parametry:
    - adminUsername – povinné.  Uživatelské jméno správce pro počítače brány.
    - adminPassword – povinné. Heslo pro účet správce pro počítače brány.
    - instanceCount – počet počítače brány k vytvoření.  
    - alwaysOn – uvádí, zda budou vytvořenou aplikaci Azure Functions v záložním stavu, nebo ne. Zachovat aplikaci Azure Functions zabrání zpoždění, když uživatelé se nejprve pokusí připojit k jejich testovací virtuální počítač, ale nemá vliv na náklady.  
    - tokenLifetime – délka dobu, po kterou bude vytvořený token platný. Formát je HH: mm:.
    - sslCertificate – The Base64 kódování certifikátu protokolu SSL pro počítač brány.
    - heslo certifikátu SSL – heslo certifikátu SSL pro počítač brány.
    - sslCertificateThumbprint – kryptografický otisk certifikátu pro identifikaci v místním úložišti certifikátů certifikátu protokolu SSL.
    - signCertificate – The Base64 kódování pro podepsání certifikátu pro počítač brány.
    - signCertificatePassword – heslo pro podpisový certifikát pro počítač brány.
    - signCertificateThumbprint – kryptografický otisk certifikátu pro identifikaci v místním úložišti certifikátů podpisového certifikátu.
    - _artifactsLocation – identifikátor URI umístění, kde můžete najít všechny podpůrné prostředky. Tato hodnota musí být plně kvalifikovaný UIR, nikoli relativní cestu.
    - _artifactsLocationSasToken – token the sdíleného přístupového podpisu (SAS) pro přístup k podpůrné prostředky, pokud je umístění účtu úložiště Azure.

    Šablona se dá nasadit pomocí rozhraní příkazového řádku Azure s použitím následujícího příkazu:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation=”{storage-account-endpoint}/{container-name}” -–parameters _artifactsLocationSasToken = “?{sas-token}”
    ```

    Tady jsou popisy parametrů:

    - {– Účet – koncový bod úložiště} můžete získat spuštěním `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`.  {-Acct – Název úložiště} je název účtu úložiště, který obsahuje soubory, které jste nahráli.  
    - {Container-name} je název kontejneru v {-acct – Název úložiště}, která obsahuje soubory, které jste nahráli.  
    - {-Sas token} můžete získat spuštěním `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`. 
        - {-Acct – Název úložiště} je název účtu úložiště, který obsahuje soubory, které jste nahráli.  
        - {Container-name} je název kontejneru v {-acct – Název úložiště}, která obsahuje soubory, které jste nahráli.  
        - {Utc datum vypršení platnosti-} je datum ve formátu UTC, kdy vyprší platnost tokenu SAS a SAS token je už nebude možné pro přístup k účtu úložiště.

    Poznamenejte si hodnoty pro gatewayFQDN a gatewayIP z výstupu šablony nasazení. Také budete muset uložit hodnotu funkční klávesy pro nově vytvořené funkci, který se nachází v [fungovat nastavení aplikace](../azure-functions/functions-how-to-use-azure-function-app-settings.md) kartu.
5. Konfigurace DNS, takže tento certifikát SSL plně kvalifikovaný název domény se odkazovalo na IP adresu gatewayIP z předchozího kroku.

    Po vytvoření farmy služby Brána vzdálené plochy a provádějí příslušné aktualizace služby DNS, je připravená k použití v testovacím prostředí v DevTest Labs. **Název hostitele brány** a **tajný klíč tokenu služby brány** nastavení musí být nakonfigurován pro použití brány počítače jste nasadili. 

    > [!NOTE]
    > Pokud počítač testovacího prostředí používá privátní IP adresy, musí být síťová cesta z počítače brány k počítači testovacího prostředí, buď prostřednictvím sdílení stejné virtuální síti, nebo pomocí partnerské virtuální síti.

    Po nakonfigurování brány a lab vytvoří soubor připojení při testovacím uživatel klikne na **připojit** budou automaticky zahrnovat informace potřebné pro připojení pomocí ověřování tokenu.     

## <a name="next-steps"></a>Další postup
Zobrazit další informace o službě Vzdálená plocha v následujícím článku: [Dokumentace k vzdálené ploše](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


