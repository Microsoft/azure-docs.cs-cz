---
title: App Service v Azure stacku update 1 poznámky k verzi | Dokumentace Microsoftu
description: Další informace o tom, co je v aktualizaci, jeden pro službu App Service ve službě Azure Stack, známé problémy a kde se stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: anwestg
ms.reviewer: sethm
ms.openlocfilehash: 632cf506477bdc6f35c66a473963168f81e22351
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52971891"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>App Service v Azure stacku update 1 poznámky

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a oprav ve službě Azure App Service v Azure Stack Update 1 a známých problémech. Známé problémy jsou rozděleny do týkající se přímo k nasazení, aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Instalaci aktualizace 1802 do služby Azure Stack integrované systému nebo nasadit nejnovější sady Azure Stack development kit před nasazením služby Azure App Service.
>
>

## <a name="build-reference"></a>Referenční informace o buildu

App Service na číslo sestavení aktualizace 1 pro Azure Stack je **69.0.13698.9**

### <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Nyní vyžadují nové nasazení služby Azure App Service ve službě Azure Stack [certifikát se zástupným znakem tři subjektu](azure-stack-app-service-before-you-get-started.md#get-certificates) z důvodu vylepšení způsobem, ve kterém je jednotné přihlašování pro Kudu teď provádí ve službě Azure App Service. Je nový předmět  **\*. sso.appservice.\< oblast\>.\< domainname\>.\< rozšíření\>**
>
>

Odkazovat [před zahájením práce dokumentaci](azure-stack-app-service-before-you-get-started.md) před zahájením nasazení.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v Azure Stack Update 1 zahrnuje následující vylepšení a opravy:

- **Vysoká dostupnost pro Azure App Service** – The Azure Stack 1802 aktualizace povolena úloh k nasazení napříč domén selhání. Infrastruktura služby App Service je proto může být odolné proti chybám, protože se nasadí napříč doménami selhání. Všechna nová nasazení služby Azure App Service ve výchozím nastavení má tato funkce, ale nasazení dokončeno před Azure Stack 1802 se zavádějí aktualizace najdete [dokumentaci doména selhání App Service](azure-stack-app-service-fault-domain-update.md)

- **Nasazení v existující virtuální síť** -mohou nyní zákazníci nasadit služby App Service ve službě Azure Stack v rámci existující virtuální sítě. Nasazení v existující virtuální síť umožňuje zákazníkům připojit se k serveru SQL Server a souborový Server, přes privátní porty vyžadované pro služby Azure App Service. Během nasazování, Zákazníci si můžou vybrat k nasazení v existující virtuální síť, ale [podsítě pro použití službou App Service, musíte vytvořit](azure-stack-app-service-before-you-get-started.md#virtual-network) před jejich nasazením.

- Aktualizace **aplikace služeb pro klienty, Admin, portály funkce a nástroje Kudu**. Konzistentní s verzí sady SDK portálu Azure Stack.

- **Aktualizace následujících aplikační architektury a nástroje**:
    - Přidání **.Net Core 2.0** podpory
    - Přidání **Node.JS** verze:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Přidání **NPM** verze:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Přidání **PHP** aktualizace:
        - 5.6.32
        - 7.0.26 (x86 a x64)
        - 7.1.12 (x86 a x64)
    - Aktualizovat **Git pro Windows** až v 2.14.1
    - Aktualizovat **Mercurial** k v4.5.0

  - Přidání podpory pro **pouze HTTPS** funkce v rámci funkce vlastní domény v Tenantovi portálu služby App Service. 

  - Přidání ověření připojení úložiště při výběru vlastního úložiště pro službu Azure Functions 

#### <a name="fixes"></a>Opravy

- Po vytvoření balíčku pro nasazení v režimu offline, zákazníci už nebudete dostávat zpráva Přístup byl odepřen Chyba při otevírání složky z instalačního programu služby App Service

- Vyřešení problémů při práci ve funkci vlastních domén v Tenantovi portálu služby App Service.

- Zabránit zákazníci, kteří používají názvy vyhrazené správce během instalace

- Povoleno nasazení služby App Service s **připojených k doméně** souborového serveru

- Vylepšené načítání služby Azure Stack kořenových certifikátů ve skriptu a nyní ověřit kořenový certifikát v instalačním programu sady služby App Service.

- Oprava nesprávný stav se vrací do Azure Resource Manageru, když se předplatné odstranit tento obsažených prostředků v oboru názvů Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Známé problémy s procesem nasazení

- Chyby ověření certifikátu

Zákazníci, kteří se setkali problémy při poskytování certifikátů do služby App Service instalačního programu při nasazování na integrovaný systém, kvůli příliš omezující ověřování v instalačním programu. Instalační program služby App Service byla vydána znovu, by měl zákazníkům [stáhnout aktualizované instalační program](https://aka.ms/appsvconmasinstaller). Pokud budete nadále dochází k problémům s ověřování certifikátů pomocí aktualizovaný instalační program, obraťte se na podporu.

- Potíže při načítání kořenového certifikátu služby Azure Stack z integrovaného systému.

Chyba v Get-AzureStackRootCert.ps1 způsobila zákazníkům selhání načtení kořenového certifikátu služby Azure Stack při spuštění skriptu v počítači, který nemá nainstalován kořenový certifikát. Skript také nyní byla vydána znovu, řeší tento problém a zákazníci požadavek [stáhnout aktualizovaný pomocné skripty](https://aka.ms/appsvconmashelpers). Pokud budete nadále dochází k problémům s načítání kořenový certifikát s aktualizovaný skript, obraťte se na podporu.

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

- Nejsou žádné známé problémy pro aktualizace služby Azure App Service v Azure stacku Update 1.

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Prohození slotů nefunguje.

Prohození slotů webu je v této verzi fungovat. Pokud chcete obnovit funkce, proveďte tyto kroky:

1. Upravit skupinu zabezpečení sítě ControllersNSG k **povolit** připojení ke vzdálené ploše instance kontroleru služby App Service. Nahraďte názvem skupiny prostředků, které jste nasadili služby App Service v AppService.local.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

2. Procházet **CN0 virtuálního počítače** v části virtuální počítače na portálu Správce služby Azure Stack a **kliknete na připojit** otevřít relaci vzdálené plochy se instance kontroleru. Použijte pověření zadaná při nasazení služby App Service.
3. Spustit **prostředí PowerShell jako správce** a spusťte následující skript

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Ukončete relaci vzdálené plochy.
5. Obnovit skupinu zabezpečení sítě ControllersNSG k **Odepřít** připojení ke vzdálené ploše instance kontroleru služby App Service. Nahraďte názvem skupiny prostředků, které jste nasadili služby App Service v AppService.local.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

6. Pracovní procesy se nám kontaktovat souborového serveru při nasazení služby App Service v existující virtuální sítě a souborový server je k dispozici v privátní síti.

Pokud jste se rozhodli nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, je nutné přidat odchozí pravidlo zabezpečení, povolení provozu SMB mezi podsítě pracovního procesu a souborový server. Chcete-li to provést, přejděte na WorkersNsg v portálu pro správu a přidat odchozí pravidlo zabezpečení s následujícími vlastnostmi:

- Zdroj: žádné
- Zdrojový rozsah portů: *
- Cíl: IP adresy
- Rozsah cílových IP adres: rozsah IP adres pro souborový server
- Rozsah cílových portů: 445
- Protocol: TCP
- Akce: Povolit
- Priorita: 700
- Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure App Service ve službě Azure Stack

Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack 1802](azure-stack-update-1802.md)

## <a name="next-steps"></a>Další postup

- Přehled služby Azure App Service najdete v tématu [Azure App Service na Přehled služby Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení služby App Service ve službě Azure Stack najdete v tématu [před zahájením práce s App Service ve službě Azure Stack](azure-stack-app-service-before-you-get-started.md).
