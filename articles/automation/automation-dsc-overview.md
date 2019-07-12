---
title: Přehled Azure Automation stavu konfigurace
description: Přehled o Azure Automation stavu Configuration (DSC), podmínky a známé problémy
keywords: PowerShell dsc, konfigurace požadovaného stavu prostředí powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a5d4657f87b0a6cbae0699c5a2f95773ff55f633
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798450"
---
# <a name="azure-automation-state-configuration-overview"></a>Přehled Azure Automation stavu konfigurace

Konfigurace stavu Azure Automation je služba Azure, která umožňuje zápis, spravovat a kompilovat PowerShell Desired State Configuration (DSC) [konfigurace](/powershell/dsc/configurations), importovat [prostředky DSC](/powershell/dsc/resources), a přiřadíte konfigurace k cílové uzly, vše v cloudu.

## <a name="why-use-azure-automation-state-configuration"></a>Proč používat Azure Automation stavu konfigurace

Konfigurace stavu Azure Automation poskytuje několik výhod oproti použití DSC mimo Azure.

### <a name="built-in-pull-server"></a>Server integrované o přijetí změn

Konfigurace stavu Azure Automation poskytuje server DSC o přijetí změn, podobně jako [služba DSC funkce Windows](/powershell/dsc/pullserver) tak, aby cílové uzly automaticky zobrazí konfigurace, v souladu s požadovaného stavu a zpětně hlásit jejich dodržování předpisů. Server integrované o přijetí změn ve službě Azure Automation eliminuje potřebu nastavili a spravovali vlastním serverem vyžádané replikace. Azure Automation můžete cílit na virtuálních nebo fyzických Windows nebo Linuxem počítačů, v cloudu nebo místně.

### <a name="management-of-all-your-dsc-artifacts"></a>Správa všechny artefakty DSC

Konfigurace stavu automatizace Azure přináší stejnou úroveň správy do [PowerShell Desired State Configuration](/powershell/dsc/overview) jako nabízí Azure Automation pro skriptování PowerShell.

Na webu Azure Portal nebo prostředí PowerShell můžete spravovat všechny vaše DSC konfigurace, prostředků a cílové uzly.

![Snímek obrazovky stránky Azure Automation.](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importovat data pro generování sestav do protokolů Azure Monitor

Uzly, které se spravují pomocí Azure Automation State Configuration odesílání podrobná data sestav stavu na server integrované o přijetí změn. Konfigurace stavu Azure Automation k odesílání dat do pracovního prostoru Log Analytics můžete nakonfigurovat. Zjistěte, jak odesílat data o stavu konfigurace stavu do pracovního prostoru Log Analytics, najdete v článku [vpřed Azure Automation stavu konfigurační data pro generování sestav na protokoly Azure monitoru](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Požadavky

Při použití Azure Automation stavu Configuration (DSC), zvažte následující požadavky.

### <a name="operating-system-requirements"></a>Požadavky na operační systém

Pro uzly s Windows se podporují následující verze:

- Windows Server. 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

Pro uzly s Linuxem jsou podporovány následující distribuce a verze:

Rozšíření DSC Linuxu podporuje Linuxové distribuce [v Azure se schválenou sadou](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) s výjimkou:

Distribuce | Version
-|-
Debian  | Všechny verze
Ubuntu  | 18.04

### <a name="dsc-requirements"></a>Požadavky na DSC

Pro všechny uzly Windows běží v Azure [WMF 5.1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) je možné nainstalovat během registrace.  Pro uzly, které běží Windows Server 2012 a Windows 7 [bude povolená služba WinRM](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Pro všechny uzly s Linuxem v Azure [PowerShell DSC pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) je možné nainstalovat během registrace.

### <a name="network-planning"></a>Konfigurovat privátní sítě

Pokud uzly jsou umístěny v privátní síti, jsou požadovány pro stav konfigurace (DSC) ke komunikaci s automatizací následující portu a adresách URL:

* Port: Vyžádáním jenom TCP 443 pro odchozí přístup k Internetu.
* Global URL: *.azure-automation.net
* Global URL of US Gov Virginia: *.azure-automation.us
* Služba agenta: https://\<ID pracovního prostoru\>.agentsvc.azure-automation.net

To poskytuje připojení k síti pro spravovaných uzlů pro komunikaci s Azure Automation.
Pokud používáte prostředky DSC, které komunikaci mezi uzly, například [WaitFor * prostředky](https://docs.microsoft.com/powershell/dsc/reference/resources/windows/waitForAllResource), musíte také povolit přenos mezi uzly.
Najdete v dokumentaci pro jednotlivé prostředky DSC pochopit tyto požadavky na síť.

#### <a name="proxy-support"></a>Podpora proxy

Podpora proxy agenta DSC je dostupná ve Windows verze 1809 a novější.
Pokud chcete nakonfigurovat tuto možnost, nastavte hodnotu pro **adresa URL proxy serveru** a **ProxyCredential** v [metaconfiguration skript](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) použili k registraci uzly.
Proxy serveru není k dispozici v DSC pro předchozí verze systému Windows.

Pro uzly s Linuxem agenta DSC podporuje proxy serveru a bude využívat proměnnou http_proxy určit adresu url.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Stav Azure konfigurace síťové rozsahy a obor názvů

Doporučuje se použít adresy, které uvedete při definování výjimky. Pro IP adresy, které si můžete stáhnout [Microsoft Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor se každý týden aktualizuje a má aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres.

Pokud máte účet Automation, který je definován pro konkrétní oblasti, můžete omezit komunikaci s místní stejné datové centrum. Následující tabulka obsahuje záznam DNS pro každou oblast:

| **Oblast** | **DNS record** |
| --- | --- |
| Západní střed USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Střed USA – jih |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Východní USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada – střed |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Západní Evropa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Severní Evropa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Jihovýchodní Asie |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Střed Indie |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonsko – východ |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Austrálie – jihovýchod |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Velká Británie – jih | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| USA (Gov) – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Seznam oblastí IP adres místo názvů oblast, stáhněte si [IP adresy Datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653) XML soubor z webu Microsoft Download Center.

> [!NOTE]
> Soubor XML adres Azure Datacenter IP obsahuje rozsahy IP adres, které se používají v datacentrech Microsoft Azure. Soubor obsahuje rozsahy compute, SQL a úložiště.
>
>Aktualizovaný soubor každý týden se zveřejňuje. Soubor odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres. Nové rozsahy, které se zobrazují v souboru nejsou používány v datových centrech alespoň jeden týden.
>
> Je vhodné Stáhněte nový soubor XML každý týden. Potom aktualizujte váš web pro zajištění správné identifikace služeb spuštěných v Azure. Uživatelé Azure ExpressRoute upozorňujeme ale, že tento soubor se používá k aktualizaci inzerování protokolu BGP (Border Gateway) prostoru Azure probíhá první týden každého měsíce.

## <a name="introduction-video"></a>Úvodní video

Raději se díváte, než čtete? Podíváme se na následující video z května 2015, kdy bylo poprvé oznámena konfigurace stavu služby Azure Automation.

> [!NOTE]
> Koncepty a životního cyklu, které jsou popsané v tomto videu jsou sice správné, konfigurace služby Azure Automation stavu provedení určité mnohem od tohoto videa. Je teď obecně dostupná, má mnohem rozsáhlejší uživatelské rozhraní na webu Azure Portal a podporuje řadu dalších funkcí.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Další postup

- Abyste mohli začít, najdete v článku [Začínáme s Azure Automation stavu konfigurace](automation-dsc-getting-started.md)
- Další informace jak připojit uzlů najdete v článku [připojování počítačů pro správu podle konfigurace stavu služby Azure Automation](automation-dsc-onboarding.md)
- Další informace o kompilaci konfigurace DSC, takže můžete je přiřadit k cílové uzly, naleznete v tématu [kompilace konfigurací v konfiguraci stavu služby Azure Automation](automation-dsc-compile.md)
- Reference k rutinám Powershellu najdete v části [rutiny Azure Automation stavu konfigurace](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v tématu [ceny konfigurace stavu služby Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití Azure Automation stav konfigurace v kanálu průběžného nasazování najdete v tématu [nepřetržité nasazení pomocí Azure Automation konfigurace stavu a Chocolatey](automation-dsc-cd-chocolatey.md)
