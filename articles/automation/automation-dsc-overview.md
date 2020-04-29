---
title: Přehled konfigurace stavu Azure Automation
description: Přehled konfigurace stavu Azure Automation (DSC), jejích pojmů a známých problémů
keywords: PowerShell DSC, konfigurace požadovaného stavu, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: afceb11180662416aa4953b8b58ef03ffaa70eec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406189"
---
# <a name="state-configuration-overview"></a>Přehled konfigurace stavu

Konfigurace stavu Azure Automation je služba Azure, která umožňuje psát, spravovat a kompilovat [Konfigurace](/powershell/scripting/dsc/configurations/configurations)požadovaných stavů PowerShellu (DSC). Služba také importuje [prostředky DSC](/powershell/scripting/dsc/resources/resources)a přiřazuje konfigurace cílovým uzlům, a to vše v cloudu.

## <a name="why-use-azure-automation-state-configuration"></a>Proč používat konfiguraci stavu Azure Automation

Konfigurace stavu Azure Automation poskytuje několik výhod oproti použití DSC mimo Azure.

### <a name="built-in-pull-server"></a>Integrovaný server pro vyžádání obsahu

Konfigurace stavu Azure Automation poskytuje server vyžádané replikace DSC podobný [službě Windows Feature DSC](/powershell/scripting/dsc/pull-server/pullserver). Cílové uzly mohou automaticky přijímat konfigurace, odpovídat požadovanému stavu a hlásit dodržování předpisů. Integrovaný server vyžádané replikace v Azure Automation eliminuje nutnost nastavit a spravovat vlastní server vyžádané replikace. Azure Automation může cílit na virtuální nebo fyzické počítače s Windows nebo Linux, v cloudu i v místním prostředí.

### <a name="management-of-all-your-dsc-artifacts"></a>Správa všech artefaktů DSC

Konfigurace stavu Azure Automation přináší stejnou vrstvu správy pro [konfiguraci požadovaného stavu prostředí PowerShell](/powershell/scripting/dsc/overview/overview) , protože nabízí skriptování prostředí PowerShell. Z Azure Portal nebo z PowerShellu můžete spravovat všechny konfigurace DSC, prostředky a cílové uzly.

![Snímek obrazovky Azure Automation stránky](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Import dat pro sestavy do protokolů Azure Monitor

Uzly spravované pomocí konfigurace stavu Azure Automation odesílají podrobné údaje o stavu vytváření sestav na integrovaný server vyžádané replikace. Konfiguraci stavu Azure Automation můžete nakonfigurovat tak, aby odesílala tato data do pracovního prostoru Log Analytics. Přečtěte si informace [o konfiguraci stavu Dopředné Azure Automation do protokolů Azure monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Předpoklady pro použití konfigurace stavu Azure Automation

Při použití konfigurace stavu Azure Automation pro DSC Vezměte v úvahu následující požadavky.

### <a name="operating-system-requirements"></a>Požadavky na operační systém

Pro uzly s Windows se podporují tyto verze:

- Windows Server 2019
- Windows Server 2016
- 2012R2 Windows serveru
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>SKU samostatného produktového [serveru Microsoft Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-server-2016) neobsahuje implementaci DSC. Proto ho nejde spravovat pomocí prostředí PowerShell DSC nebo konfigurace stavu Azure Automation.

Pro uzly se systémem Linux podporuje rozšíření DSC Linux všechna distribuce systému Linux uvedená v části [podporovaná distribuce systému Linux](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Požadavky DSC

Pro všechny uzly Windows běžící v Azure se během připojování nainstaluje [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) . Pro uzly se systémem Windows Server 2012 a Windows 7 je povolená [Služba WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) .

Pro všechny uzly Linux běžící v Azure se během připojování nainstaluje [POWERSHELL DSC pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) .

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Konfigurace privátních sítí

Pokud jsou uzly umístěny v privátní síti, je nutné zadat následující port a adresy URL. Tyto prostředky poskytují připojení k síti pro spravovaný uzel a umožňují, aby DSC komunikovala s Azure Automation.

* Port: pro odchozí přístup k Internetu se vyžaduje jenom TCP 443.
* Globální adresa URL: ***. Azure-Automation.NET**
* Globální adresa URL US Gov – Virginie: ***. Azure-Automation.us**
* Služba agenta: **https://\<ID pracovního prostoru\>. agentsvc.Azure-Automation.NET**

Pokud používáte prostředky DSC, které komunikují mezi uzly, například s [prostředky WAITFOR *](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), musíte taky u těchto uzlů povolený přenos. Pochopte tyto požadavky na síť v dokumentaci ke každému prostředku DSC.

#### <a name="proxy-support"></a>Podpora proxy serveru

Podpora proxy serveru pro agenta DSC je dostupná ve Windows verze 1809 a novějším. Tato možnost je povolena nastavením hodnot pro `ProxyURL` a `ProxyCredential` v [metaconfiguration skriptu](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) , který slouží k registraci uzlů.

>[!NOTE]
>Konfigurace stavu Azure Automation neposkytuje podporu proxy serveru DSC pro předchozí verze Windows.

V případě uzlů se systémem Linux agent DSC podporuje proxy a používá `http_proxy` proměnnou k určení adresy URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Azure Automation rozsahy sítě a obor názvů v síti konfigurace stavu

Při definování výjimek doporučujeme použít níže uvedené adresy. Pro IP adresy můžete stáhnout [Microsoft Azure rozsahy IP adres datacentra](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor se aktualizuje týdně a má aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres.

Pokud máte účet Automation, který je definovaný pro konkrétní oblast, můžete omezit komunikaci s tímto oblastním datacentrem. Následující tabulka uvádí záznam DNS pro jednotlivé oblasti:

| **Oblast** | **Záznam DNS** |
| --- | --- |
| USA – středozápad | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA – středojih |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA – východ    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| USA – východ 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Střední Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Západní Evropa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Severní Evropa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Jihovýchodní Asie |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indie – střed |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonsko – východ |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Austrálie – jihovýchod |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Spojené království – jih | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| USA (Gov) – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Seznam IP adres oblastí, nikoli názvy oblastí, si můžete stáhnout ze služby Stažení [softwaru ve službě](https://www.microsoft.com/download/details.aspx?id=41653) stažení softwaru.

> [!NOTE]
> Soubor XML IP adresy datacentra Azure obsahuje seznam rozsahů IP adres, které se používají v datových centrech Microsoft Azure. Soubor zahrnuje výpočetní prostředky, SQL a rozsahy úložiště.
>
>Aktualizovaný soubor je zveřejněný týdně. Tento soubor odráží aktuálně nasazené rozsahy a všechny nadcházející změny v rozsahu IP adres. Nové rozsahy, které se zobrazí v souboru, se v datových centrech nepoužijí aspoň na jeden týden. Každý týden je vhodné stáhnout nový soubor XML. Pak aktualizujte svůj web tak, aby správně identifikoval služby běžící v Azure. 

Uživatelé Azure ExpressRoute by si měli všimnout, že se tento soubor používá k aktualizaci inzerce protokolu BGP (Border Gateway Protocol) v Azure Space v první týden v měsíci.

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít používat DSC v konfiguraci stavu Azure Automation, přečtěte si téma [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md).
- Další informace o připojování uzlů najdete v tématu věnovaném [připojování počítačů ke správě podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu [průběžné nasazování pomocí konfigurace Azure Automation stavu a čokolády](automation-dsc-cd-chocolatey.md).
