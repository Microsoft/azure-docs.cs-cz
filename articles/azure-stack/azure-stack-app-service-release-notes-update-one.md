---
title: Aplikační služby na Azure zásobníku update 1 poznámky k verzi | Microsoft Docs
description: Další informace o tom, co je v aktualizaci, jeden pro službu App Service v Azure zásobníku známé problémy a toho, kde se stáhnout aktualizaci.
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
ms.reviewer: brenduns
ms.openlocfilehash: fedf511e06243d5c0652e422b397bb00da3b42c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Aplikační služby na Azure zásobníku update 1 poznámky

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Tyto poznámky k verzi popisují vylepšení a opravy v Azure App Service na Azure zásobníku Update 1 a všech známých problémů. Známé problémy se dál dělí na problémy, které jsou přímo týkající se nasazení, proces aktualizace a problémy s sestavení (po instalaci).

> [!IMPORTANT]
> Použitím 1802 aktualizace v zásobníku Azure integrované systému nebo nasadit nejnovější development kit zásobník Azure před nasazením služby Azure App Service.
>
>

## <a name="build-reference"></a>Odkaz na sestavení

App Service na číslo sestavení Azure zásobníku Update 1 je **69.0.13698.9**

### <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Nová nasazení služby Azure App Service v zásobníku Azure nyní vyžadují [certifikát se zástupným znakem tři subjektu](azure-stack-app-service-before-you-get-started.md#get-certificates) z důvodu vylepšení způsobem, ve kterém se nyní zpracovává jednotného přihlašování pro Kudu ve službě Azure App Service. Nové subjektem je ** *.sso.appservice.<region>.<domainname>.<extension>**
>
>

Odkazovat [před zahájením práce dokumentaci](azure-stack-app-service-before-you-get-started.md) před zahájením nasazení.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service na Azure zásobníku Update 1 obsahuje následující vylepšení a opravy:

- **Vysoká dostupnost služby Azure App Service** -poruch 1802 zásobníku Azure povolená aktualizace úloh k nasazení napříč doménami. Infrastruktura služby App Service je proto může být odolné proti chybám, jak bude nasazen napříč doménami selhání. Ve výchozím nastavení všechna nová nasazení služby Azure App Service má tato funkce ale nasazení před 1802 zásobník Azure dokončil aktualizace, které bylo použito najdete [dokumentace doména selhání aplikace služby](azure-stack-app-service-fault-domain-update.md)

- **Nasazení v existující virtuální síť** -můžou nyní zákazníci nasadit služby App Service v zásobníku Azure v rámci existující virtuální síť. Nasazení v existující virtuální síť umožňuje zákazníkům pro připojení k systému SQL Server a souborový Server, vyžaduje se pro Azure App Service přes privátní porty. Během nasazení, můžete vybrat zákazníků k nasazení v existující virtuální síť, ale [musíte vytvořit podsítě pro použití službou App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) před jejich nasazením.

- Aktualizace **klienta služby aplikace, správce, portálů funkce a nástroje Kudu**. Konzistentní s verzí Azure SDK portálu zásobníku.

- **Aktualizace na tyto architektury aplikace a nástroje pro**:
    - Přidat **základní rozhraní .net 2.0** podporu
    - Přidat **Node.JS** verze:
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
    - Přidat **NPM** verze:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Přidat **PHP** aktualizace:
        - 5.6.32
        - 7.0.26 (x86 a x64)
        - 7.1.12 (x86 a x64)
    - Aktualizovat **Git pro Windows** až v 2.14.1
    - Aktualizovat **Mercurial** k v4.5.0

  - Přidání podpory pro **pouze HTTPS** funkce v rámci vlastní domény funkce na portálu aplikace služby klienta. 

  - Přidání ověření připojení úložiště v dialogu pro výběr vlastní úložiště pro Azure Functions 

#### <a name="fixes"></a>Opravy

- Když vytváříte balíček offline nasazení, zákazníků se už nebude přístup odepřen chybová zpráva při otevření složky z instalačního programu služby App Service

- Vyřešení problémů při práci ve funkci vlastní domény na portálu aplikace služby klienta.

- Zabránit zákazníky používající názvy vyhrazené správce během instalace

- Povoleno nasazení aplikace služby s **připojený k doméně** souborového serveru

- Vylepšené načítání zásobník Azure kořenových certifikátů ve skriptu a nyní ověřit kořenového certifikátu v instalačním programu služby App Service.

- Pevný nesprávný stav nevrátila do Azure Resource Manageru, pokud je předplatné odstranit tento obsažených prostředků v oboru názvů Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Známé problémy v procesu nasazení

- Chyby ověření certifikátu

Někteří zákazníci došlo problémy při poskytování certifikáty instalační program služby App Service při nasazování na integrovaný systém, z důvodu příliš omezující ověření v instalačním programu. Instalační program služby App Service byl vydán znovu, musí zákazníkům [stáhnout aktualizovaný instalační program](https://aka.ms/appsvconmasinstaller). Pokud můžete mít problémy ověřování certifikátů s aktualizovaný instalační program bude zobrazovat i nadále, obraťte se na podporu.

- Potíže při načítání zásobník Azure kořenový certifikát z integrovaného systému.

Chyba v Get-AzureStackRootCert.ps1 způsobila zákazníkům nepodaří načíst kořenový certifikát zásobník Azure při provádění skriptu na počítači, který nemá nainstalován kořenový certifikát. Skript nyní také byl vydán znovu, vyřešte tento problém a požadavek zákazníci [stáhnout aktualizované pomocná skripty](https://aka.ms/appsvconmashelpers). Pokud budete pokračovat v načítání kořenový certifikát aktualizované skript s problémy, kontaktujte podporu.

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

- Nejsou žádné známé problémy pro aktualizaci služby Azure App Service na Azure zásobníku Update 1.

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Prohození slotů nefunguje.

V této verzi je rozděleno prohození slotů lokality. Obnovit funkčnost, proveďte tyto kroky:

1. Změnit skupinu zabezpečení sítě ControllersNSG k **povolit** připojení ke vzdálené ploše na řadič instance služby App Service. Nahraďte název skupiny prostředků, které jste nasadili App Service v AppService.local.

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

2. Vyhledejte **CN0-VM** v části virtuální počítače na portálu Azure zásobníku správce a **kliknete na připojit** otevřít relaci vzdálené plochy se instance kontroleru. Pomocí přihlašovacích údajů zadaných v průběhu nasazování služby App Service.
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

4. Zavřete relaci vzdálené plochy.
5. Obnovit skupinu zabezpečení sítě ControllersNSG k **Odepřít** připojení ke vzdálené ploše na řadič instance služby App Service. Nahraďte název skupiny prostředků, které jste nasadili App Service v AppService.local.

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
- Jsou možné vás zastihnout souborového serveru při nasazení aplikace služby v existující virtuální síť a souborový server je k dispozici v soukromé síti pouze zaměstnanci.
 
Pokud jste vybrali k nasazení do existující virtuální síť a interní IP adresu pro připojení k vaší souborovém serveru, je nutné přidat pravidlo odchozí zabezpečení povolení přenosy SMB mezi podsíť pracovního procesu a souborový server. Chcete-li to provést, přejděte do WorkersNsg v portálu pro správu a přidejte pravidlo odchozí zabezpečení s následujícími vlastnostmi:
 * Zdroj: žádné
 * Zdroj rozsah portů: *
 * Cíl: IP adresy
 * Rozsah cílových IP adres: rozsah IP adres pro souborový server
 * Rozsah cílových portů: 445
 * Protokol: TCP
 * Akce: Povolit
 * Priorita: 700
 * Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro správce cloudu operační Azure App Service v Azure zásobníku

V dokumentaci v [poznámky k verzi 1802 zásobník Azure](azure-stack-update-1802.md)

## <a name="next-steps"></a>Další postup

- Přehled služby Azure App Service najdete v tématu [Azure App Service na přehled Azure zásobníku](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení služby App Service v zásobníku Azure najdete v tématu [před zahájením práce s App Service v Azure zásobníku](azure-stack-app-service-before-you-get-started.md).
