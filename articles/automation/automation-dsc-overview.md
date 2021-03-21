---
title: Přehled konfigurace stavu Azure Automation
description: Tento článek poskytuje přehled konfigurace stavu Azure Automation.
keywords: PowerShell DSC, konfigurace požadovaného stavu, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 01/26/2021
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 752d7f86941967c218b3a57fa163698b9f502057
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897016"
---
# <a name="azure-automation-state-configuration-overview"></a>Přehled konfigurace stavu Azure Automation

Konfigurace stavu Azure Automation je služba správy konfigurace Azure, která umožňuje psát, spravovat a kompilovat [Konfigurace](/powershell/scripting/dsc/configurations/configurations) (DSC) prostředí PowerShell pro uzly v jakémkoli cloudovém nebo místním datacentru. Služba také importuje [prostředky DSC](/powershell/scripting/dsc/resources/resources)a přiřazuje konfigurace cílovým uzlům, a to vše v cloudu. Můžete získat přístup k konfiguraci stavu Azure Automation v Azure Portal výběrem **Konfigurace stavu (DSC)** v části **Správa konfigurace**.

Ke správě různých počítačů můžete použít konfiguraci stavu Azure Automation:

- Virtuální počítače Azure
- Virtuální počítače Azure (klasické)
- Fyzické nebo virtuální počítače s Windows v místním prostředí nebo v jiném cloudu než Azure (včetně instancí AWS EC2)
- Fyzické nebo virtuální počítače se systémem Linux v místním prostředí, v Azure nebo v jiném cloudu než Azure

Pokud nejste připraveni ke správě konfigurace počítače z cloudu, můžete použít konfiguraci Azure Automationho stavu jako koncový bod pouze sestavy. Tato funkce umožňuje nastavení (push) konfigurací prostřednictvím DSC a zobrazení podrobností o vytváření sestav v Azure Automation.

> [!NOTE]
> Správa virtuálních počítačů Azure s konfigurací stavu Azure Automation je zahrnutá bez dalších poplatků, pokud je nainstalovaná verze rozšíření konfigurace požadovaného stavu virtuálního počítače Azure větší než 2,70. Další informace najdete na [**stránce s cenami pro automatizaci**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Proč používat konfiguraci stavu Azure Automation

Konfigurace stavu Azure Automation poskytuje několik výhod oproti použití DSC mimo Azure. Tato služba umožňuje snadno a rychle využít škálovatelnost v tisících počítačů z centrálního a bezpečného umístění. Můžete snadno povolit počítače, přiřazovat jim deklarativní konfigurace a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který zadáte.

Služba konfigurace stavu Azure Automation je DSC, které Azure Automation Runbooky využívají ke skriptování prostředí PowerShell. Jinými slovy, a to stejným způsobem, jakým Azure Automation pomáhá spravovat skripty prostředí PowerShell, vám také pomůže spravovat konfigurace DSC.

### <a name="built-in-pull-server"></a>Integrovaný server pro vyžádání obsahu

Konfigurace stavu Azure Automation poskytuje server vyžádané replikace DSC podobný [službě Windows Feature DSC](/powershell/scripting/dsc/pull-server/pullserver). Cílové uzly mohou automaticky přijímat konfigurace, odpovídat požadovanému stavu a hlásit dodržování předpisů. Integrovaný server vyžádané replikace v Azure Automation eliminuje nutnost nastavit a spravovat vlastní server vyžádané replikace. Azure Automation může cílit na virtuální nebo fyzické počítače s Windows nebo Linux, v cloudu i v místním prostředí.

### <a name="management-of-all-your-dsc-artifacts"></a>Správa všech artefaktů DSC

Konfigurace stavu Azure Automation přináší stejnou vrstvu správy pro [konfiguraci požadovaného stavu prostředí PowerShell](/powershell/scripting/dsc/overview/overview) , protože nabízí skriptování prostředí PowerShell. Z Azure Portal nebo z PowerShellu můžete spravovat všechny konfigurace DSC, prostředky a cílové uzly.

![Snímek obrazovky Azure Automation stránky](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Import dat pro sestavy do protokolů Azure Monitor

Uzly spravované pomocí konfigurace stavu Azure Automation odesílají podrobné údaje o stavu vytváření sestav na integrovaný server vyžádané replikace. Konfiguraci stavu Azure Automation můžete nakonfigurovat tak, aby odesílala tato data do pracovního prostoru Log Analytics. Přečtěte si informace [o konfiguraci stavu Dopředné Azure Automation do protokolů Azure monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Předpoklady

Při použití konfigurace stavu Azure Automation Vezměte v úvahu požadavky v této části.

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

Pro uzly se systémem Linux podporuje rozšíření DSC Linux všechna distribuce systému Linux uvedená v [dokumentaci k prostředí POWERSHELL DSC](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

### <a name="dsc-requirements"></a>Požadavky DSC

Pro všechny uzly Windows běžící v Azure se při povolení počítačů nainstaluje [WMF 5,1](/powershell/scripting/wmf/setup/install-configure) . Pro uzly se systémem Windows Server 2012 a Windows 7 je povolená [Služba WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) .

Pro všechny uzly Linux běžící v Azure se v případě povolení počítačů nainstaluje [POWERSHELL DSC pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) .

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Konfigurace privátních sítí

Ověřte [Azure Automation konfiguraci sítě](automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) , kde najdete podrobné informace o portech, adresách URL a dalších podrobných informacích o sítích vyžadovaných pro uzly v privátní síti.

#### <a name="proxy-support"></a>Podpora proxy serveru

Podpora proxy serveru pro agenta DSC je dostupná ve Windows verze 1809 a novějším. Tato možnost je povolena nastavením hodnot `ProxyURL` `ProxyCredential` vlastností a v [metaconfiguration skriptu](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) , který slouží k registraci uzlů.

>[!NOTE]
>Konfigurace stavu Azure Automation neposkytuje podporu proxy serveru DSC pro předchozí verze Windows.

V případě uzlů se systémem Linux agent DSC podporuje proxy a používá `http_proxy` proměnnou k určení adresy URL. Další informace o podpoře proxy serveru najdete v tématu věnovaném [generování DSC metaconfigurations](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="dns-records-per-region"></a>Záznamy DNS na oblast

Při definování výjimek doporučujeme použít adresy uvedené v tabulce [záznamů DNS na oblast](how-to/automation-region-dns-records.md) .

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu Začínáme [s konfigurací stavu Azure Automation](automation-dsc-getting-started.md).
- Další informace o povolení uzlů najdete v tématu [povolení konfigurace stavu Azure Automation](automation-dsc-onboarding.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací DSC v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Příklad použití konfigurace stavu Azure Automation v kanálu nepřetržitého nasazení najdete v tématu [Nastavení průběžného nasazování s čokoládou](automation-dsc-cd-chocolatey.md).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
