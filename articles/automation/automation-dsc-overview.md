---
title: Přehled konfigurace stavu automatizace Azure
description: Přehled konfigurace stavu automatizace Azure (DSC), jeho termíny a známé problémy
keywords: powershell dsc, požadovaná konfigurace stavu, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 46cf0d6a12ffbc836db7bd79c0f2738a94e23085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283187"
---
# <a name="azure-automation-state-configuration-overview"></a>Přehled konfigurace stavu automatizace Azure

Azure Automation State Configuration je služba Azure, která umožňuje psát, spravovat a kompilovat [konfigurace](/powershell/scripting/dsc/configurations/configurations)konfigurace konfigurace požadovaného stavu prostředí PowerShell (DSC). Služba také importuje [prostředky DSC](/powershell/scripting/dsc/resources/resources)a přiřazuje konfigurace cílovým uzlům, to vše v cloudu.

## <a name="why-use-azure-automation-state-configuration"></a>Proč používat konfiguraci stavu automatizace Azure

Konfigurace stavu automatizace Azure poskytuje několik výhod oproti použití DSC mimo Azure.

### <a name="built-in-pull-server"></a>Vestavěný server pro vytahovací server

Konfigurace stavu automatizace Azure poskytuje server pro vyžádat dsc podobný [službě DSC funkce systému Windows](/powershell/scripting/dsc/pull-server/pullserver). Cílové uzly mohou automaticky přijímat konfigurace, odpovídat požadovanému stavu a podávat zprávy o jejich dodržování předpisů. Integrovaný server pro vyžádat v Azure Automation eliminuje potřebu nastavit a udržovat vlastní server pro vyžádat. Azure Automation můžou cílit na virtuální nebo fyzické počítače s Windows nebo Linuxem v cloudu nebo v místním prostředí.

### <a name="management-of-all-your-dsc-artifacts"></a>Správa všech artefaktů DSC

Konfigurace stavu automatizace Azure přináší stejnou vrstvu správy do [konfigurace požadovaného stavu prostředí PowerShell,](/powershell/scripting/dsc/overview/overview) jakou nabízí pro skriptování prostředí PowerShell. Z portálu Azure nebo z PowerShellu můžete spravovat všechny konfigurace, prostředky a cílové uzly DSC.

![Snímek obrazovky se stránkou Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Import dat sestav do protokolů Azure Monitoru

Uzly, které jsou spravované pomocí konfigurace stavu Azure Automation, odesílají podrobná data o stavu vykazování na integrovaný server pro vyžádat. Konfiguraci stavu Azure Automation můžete nakonfigurovat tak, aby tato data odesílala do pracovního prostoru Analýzy protokolů. Viz [Předávání dat sestav stavu Azure Automation Configuration do protokolů Azure Monitor .](automation-dsc-diagnostics.md)

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Požadavky pro použití konfigurace stavu automatizace Azure

Při použití konfigurace stavu automatizace Azure pro DSC zvažte následující požadavky.

### <a name="operating-system-requirements"></a>Požadavky na operační systém

U uzlů se systémem Windows jsou podporovány následující verze:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Samostatná položka produktu [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) neobsahuje implementaci dsc. Proto ji nelze spravovat pomocí powershellu DSC nebo konfigurace stavu automatizace Azure.

Pro uzly se systémem Linux podporuje rozšíření DSC Linux všechny linuxové distribuce uvedené v části [Podporované linuxové distribuce](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Požadavky DSC

Pro všechny uzly Windows spuštěné v Azure, [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) se nainstaluje během registrace. U uzlů se systémem Windows Server 2012 a Windows 7 je povolena [služba WinRM.](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)

Pro všechny linuxové uzly běžící v Azure se [PowerShell DSC pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) nainstaluje během registrace.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Konfigurace privátních sítí

Pokud jsou uzly umístěny v privátní síti, je vyžadován následující port a adresy URL. Tyto prostředky poskytují připojení k síti pro spravovaný uzel a umožňují DSC komunikovat s Azure Automation.

* Port: Pro odchozí přístup k internetu je vyžadován pouze protokol TCP 443
* Globální adresa URL: ***.azure-automation.net**
* Globální URL americké vlády Virginie: ***.azure-automation.us**
* Služba agenta: **https://\<\>workspaceId .agentsvc.azure-automation.net**

Pokud používáte prostředky DSC, které komunikují mezi uzly, jako jsou [například prostředky WaitFor*](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), je také nutné povolit provoz mezi uzly. Informace o těchto požadavcích na síť naleznete v dokumentaci ke každému prostředku DSC.

#### <a name="proxy-support"></a>Podpora pro proxy server

Podpora proxy pro agenta DSC je k dispozici v systému Windows verze 1809 a novější. Tato možnost je povolena `ProxyURL` nastavením hodnot pro a `ProxyCredential` v [metakonfiguračním skriptu](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) používaném k registraci uzlů.

>[!NOTE]
>Konfigurace stavu automatizace Azure neposkytuje podporu proxy serveru DSC pro předchozí verze systému Windows.

Pro linuxové uzly agent DSC `http_proxy` podporuje proxy server a používá proměnnou k určení adresy URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Rozsahy a obor názvů konfigurace stavu Azure Automation

Při definování výjimek doporučujeme používat níže uvedené adresy. Pro IP adresy si můžete stáhnout [rozsahy IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor je aktualizován každý týden a má aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres.

Pokud máte účet Automation, který je definován pro konkrétní oblast, můžete omezit komunikaci na toto regionální datové centrum. V následující tabulce je uveden záznam DNS pro každou oblast:

| **Oblasti** | **Záznam DNS** |
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

Seznam adres IP oblasti namísto názvů oblastí si stáhněte soubor XML [IP adresy Datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653) ze služby Stažení softwaru.

> [!NOTE]
> V souboru XML ip adresy Datového centra Azure jsou uvedeny rozsahy IP adres, které se používají v datových centrech Microsoft Azure. Soubor obsahuje výpočetní, SQL a rozsahy úložiště.
>
>Aktualizovaný soubor je zaúčtován každý týden. Soubor odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres. Nové oblasti, které se zobrazí v souboru, se v datových centrech nepoužívají alespoň jeden týden. Je vhodné stáhnout nový soubor XML každý týden. Pak aktualizujte web, abyste správně identifikovali služby spuštěné v Azure. 

Uživatelé Azure ExpressRoute by si měli uvědomit, že tento soubor se používá k aktualizaci reklamy protokolu BGP (Border Gateway Protocol) na místo Azure v prvním týdnu každého měsíce.

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít používat DSC v konfiguraci stavu automatizace Azure, najdete [v tématu Začínáme s azure automation state configuration](automation-dsc-getting-started.md).
- Další informace o tom, jak napalubě uzly, najdete [v tématu onboarding počítače pro správu pomocí konfigurace stavu automatizace Azure](automation-dsc-onboarding.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md).
- Odkaz na rutinu prostředí PowerShell najdete [v tématu Rutiny konfigurace stavu azure automatizace](/powershell/module/azurerm.automation/#automation).
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu Průběžné nasazení pomocí konfigurace stavu azure automatizace a Chocolatey](automation-dsc-cd-chocolatey.md).
