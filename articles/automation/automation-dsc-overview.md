---
title: Přehled konfigurace stavu Azure Automation
description: Přehled konfigurace stavu Azure Automation (DSC), jejích pojmů a známých problémů
keywords: PowerShell DSC, konfigurace požadovaného stavu, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c192a994ac5398d41e28a35267b922ba98b721cc
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69513615"
---
# <a name="azure-automation-state-configuration-overview"></a>Přehled konfigurace stavu Azure Automation

Konfigurace stavu Azure Automation je služba Azure, která umožňuje zapisovat, spravovat a kompilovat konfigurace požadovaného stavu prostředí PowerShell, importovat [prostředky DSC](/powershell/dsc/resources)a [](/powershell/dsc/configurations)přiřazovat konfigurace cílovým uzlům, a to vše v nástroji. cloudu.

## <a name="why-use-azure-automation-state-configuration"></a>Proč používat konfiguraci stavu Azure Automation

Konfigurace stavu Azure Automation poskytuje několik výhod oproti použití DSC mimo Azure.

### <a name="built-in-pull-server"></a>Integrovaný server pro vyžádání obsahu

Konfigurace stavu Azure Automation poskytuje server vyžádané replikace DSC, který se podobá [funkci Windows DSC-Service](/powershell/dsc/pullserver) , aby cílové uzly automaticky přijímaly konfigurace, splňovaly požadovaný stav a nahlásili zpět na dodržování předpisů. Integrovaný server vyžádané replikace v Azure Automation eliminuje nutnost nastavit a spravovat vlastní server vyžádané replikace. Azure Automation může cílit na virtuální nebo fyzické počítače s Windows nebo Linux, v cloudu i v místním prostředí.

### <a name="management-of-all-your-dsc-artifacts"></a>Správa všech artefaktů DSC

Konfigurace stavu Azure Automation přináší ke [konfiguraci požadovaného stavu prostředí PowerShell](/powershell/dsc/overview) stejnou vrstvu správy jako Azure Automation nabídky pro skriptování prostředí PowerShell.

Z Azure Portal nebo z PowerShellu můžete spravovat všechny konfigurace DSC, prostředky a cílové uzly.

![Snímek obrazovky Azure Automation stránky](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importovat data sestavy do protokolů Azure Monitor

Uzly spravované pomocí konfigurace stavu Azure Automation odesílají podrobné údaje o stavu vytváření sestav na integrovaný server vyžádané replikace. Konfiguraci stavu Azure Automation můžete nakonfigurovat tak, aby odesílala tato data do pracovního prostoru Log Analytics. Informace o tom, jak odeslat údaje o stavu konfigurace stavu do pracovního prostoru Log Analytics, najdete v tématu předávat data pro sestavy o stavu [dopřed Azure Automation do protokolů Azure monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Požadavky

Při použití konfigurace stavu Azure Automation (DSC) Vezměte v úvahu následující požadavky.

### <a name="operating-system-requirements"></a>Požadavky na operační systém

Pro uzly s Windows se podporují tyto verze:

- Windows Server. 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

SKU samostatného produktu [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) neobsahuje implementaci požadovaného stavu konfigurací, takže ji nejde spravovat pomocí prostředí PowerShell DSC nebo konfigurace stavu Azure Automation.

Pro uzly se systémem Linux jsou podporovány následující distribuce/verze:

Rozšíření DSC pro Linux podporuje všechny distribuce systému Linux [schválené v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) s výjimkou těchto:

Distribuce | Version
-|-
Debian  | všechny verze
Ubuntu  | 18,04

### <a name="dsc-requirements"></a>Požadavky DSC

Pro všechny uzly Windows běžící v Azure se během připojování nainstaluje [WMF 5,1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) .  Pro uzly se systémem Windows Server 2012 a Windows 7 [bude služba WinRM povolena](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Pro všechny uzly Linux běžící v Azure se během připojování nainstaluje [POWERSHELL DSC pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) .

### <a name="network-planning"></a>Konfigurace privátních sítí

Pokud jsou uzly umístěny v privátní síti, pro konfiguraci stavu (DSC) pro komunikaci se službou Automation jsou vyžadovány následující porty a adresy URL:

* Port: Pro odchozí přístup k Internetu se vyžaduje jenom TCP 443.
* Globální adresa URL: *. azure-automation.net
* Globální adresa URL US Gov – Virginie: *. azure-automation.us
* Služba agenta: https://\<ID pracovního prostoru\>. agentsvc.Azure-Automation.NET

To umožňuje síťové připojení pro spravovaný uzel ke komunikaci s Azure Automation.
Pokud používáte prostředky DSC, které komunikují mezi uzly, například s [prostředky WAITFOR *](https://docs.microsoft.com/powershell/dsc/reference/resources/windows/waitForAllResource), budete také muset u těchto uzlů povolený přenos.
Seznamte se s požadavky na síť v dokumentaci ke každému prostředku DSC.

#### <a name="proxy-support"></a>Podpora proxy serveru

Podpora proxy serveru pro agenta DSC je dostupná ve Windows verze 1809 a novějším.
Chcete-li konfigurovat tuto možnost, nastavte hodnotu pro **ProxyURL** a **ProxyCredential** v rámci [metaconfiguration skriptu](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) používaného k registraci uzlů.
Proxy server není v DSC k dispozici pro předchozí verze Windows.

V případě uzlů se systémem Linux agent DSC podporuje proxy a k určení adresy URL použije proměnnou http_proxy.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Síťové rozsahy a obory názvů konfigurace Azure State

Doporučuje se používat adresy uvedené při definování výjimek. Pro IP adresy můžete stáhnout [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor se aktualizuje týdně a má aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres.

Pokud máte účet Automation, který je definovaný pro konkrétní oblast, můžete omezit komunikaci s tímto oblastním datacentrem. Následující tabulka uvádí záznam DNS pro jednotlivé oblasti:

| **Oblast** | **Záznam DNS** |
| --- | --- |
| Západní střed USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Střed USA – jih |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| East US   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
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

Seznam IP adres oblastí, nikoli názvy oblastí, si můžete stáhnout ze služby [](https://www.microsoft.com/download/details.aspx?id=41653) stažení softwaru ve službě Stažení softwaru.

> [!NOTE]
> Soubor XML IP adresy datacentra Azure obsahuje seznam rozsahů IP adres, které se používají v datových centrech Microsoft Azure. Soubor zahrnuje výpočetní prostředky, SQL a rozsahy úložiště.
>
>Aktualizovaný soubor je zveřejněný týdně. Tento soubor odráží aktuálně nasazené rozsahy a všechny nadcházející změny v rozsahu IP adres. Nové rozsahy, které se zobrazí v souboru, se v datových centrech nepoužijí aspoň na jeden týden.
>
> Každý týden je vhodné stáhnout nový soubor XML. Pak aktualizujte svůj web tak, aby správně identifikoval služby běžící v Azure. Uživatelé Azure ExpressRoute by si měli všimnout, že se tento soubor používá k aktualizaci inzerce protokolu BGP (Border Gateway Protocol) v Azure Space v první týden v měsíci.

## <a name="introduction-video"></a>Úvodní video

Raději se díváte, než čtete? Podívá se na následující video od května 2015, pokud byla konfigurace Azure Automation stavu poprvé oznámena.

> [!NOTE]
> I když jsou koncepty a životní cyklus popsané v tomto videu správné, konfigurace Azure Automation stavu po nahrání tohoto videa uplynula hodně. Je teď všeobecně dostupná, má v Azure Portal mnohem rozsáhlejší uživatelské rozhraní a podporuje spoustu dalších možností.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md) .
- Další informace o připojování uzlů najdete v tématu věnovaném [připojování počítačů ke správě podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md) .
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md)
- Referenční informace k rutinám PowerShellu najdete v tématu [rutiny konfigurace stavu Azure Automation](/powershell/module/azurerm.automation/#automation) .
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/) .
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu průběžné [nasazování pomocí Azure Automation konfigurace stavu a čokolády](automation-dsc-cd-chocolatey.md) .
