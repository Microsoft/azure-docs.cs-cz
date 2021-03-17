---
title: Připojit data firewallu v programu Windows Defender k bráně Azure Sentinel | Microsoft Docs
description: Povolte konektor brány Windows Firewall ve službě Azure Sentinel, abyste mohli snadno streamovat události brány firewall z počítačů s Windows, které mají nainstalované agenty Log Analytics.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: cf7e389fc4a8a8dfa88691dc034611cae3471731
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655336"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>Připojení brány firewall v programu Windows Defender s pokročilým zabezpečením do Azure Sentinel

[Firewall v programu Windows Defender s pokročilým zabezpečením](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) umožňuje službě Azure Sentinel snadno ingestovat firewall v programu Windows Defender s pokročilými protokoly zabezpečení z libovolného počítače s Windows ve vašem pracovním prostoru. Toto připojení umožňuje zobrazit a analyzovat události brány Windows Firewall v sešitech, abyste je mohli používat při vytváření vlastních výstrah, a začlenit je do vašeho bezpečnostního šetření, což vám poskytne lepší přehled o síti vaší organizace a zlepšuje možnosti zabezpečení provozu. 

Řešení shromažďuje události brány Windows Firewall z počítačů se systémem Windows, na kterých je nainstalován agent Log Analytics. 

> [!NOTE]
> - Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.
>
> - Pokud jsou výstrahy v programu Azure Defender z Azure Security Center již shromážděny do pracovního prostoru služby Azure Sentinel, není nutné povolit řešení brány Windows Firewall prostřednictvím tohoto konektoru. Pokud jste to ale povolili, nezpůsobí duplikovaná data. 

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru, ke kterému jsou připojené počítače, které chcete monitorovat.

- K tomuto pracovnímu prostoru se kromě jakýchkoli rolí **Sentinel Azure** musíte přiřadit **Log Analytics roli přispěvatele** v řešení SecurityInsights. [Další informace](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>Povolení konektoru 

1. Na portálu Sentinel Azure vyberte **datové konektory** z navigační nabídky.

1. Z Galerie konektorů vyberte **Brána Windows Firewall** a klikněte na tlačítko **otevřít stránku konektoru**.

### <a name="instructions-tab"></a>Karta pokyny

- **Pokud jsou počítače s Windows v Azure:**

    1. Vyberte **instalovat agenta na virtuálním počítači Azure s Windows**.

    1. Klikněte na tlačítko **stáhnout & nainstalovat agenta pro virtuální počítače Azure s Windows >** odkaz, který se zobrazí.

    1. V seznamu **virtuální počítače** vyberte počítač s Windows, který chcete streamovat do Azure Sentinel. (Pokud chcete zajistit, aby se zobrazily jenom virtuální počítače s Windows, můžete vybrat **Windows** ve filtru sloupců operačního systému).

    1. V okně, které se otevře pro daný virtuální počítač, klikněte na **připojit**.

    1. Vraťte se do podokna **Virtual Machines** a opakujte předchozí dva kroky pro všechny ostatní virtuální počítače, které chcete připojit. Až budete hotovi, vraťte se do podokna **Brána firewall systému Windows** .

- **Pokud Váš počítač s Windows není virtuální počítač Azure:**

    1. Vyberte **instalovat agenta na počítači s Windows mimo Azure**.

    1. Klikněte na **stáhnout & nainstalovat agenta pro počítače s Windows, které nejsou v Azure, >** odkaz, který se zobrazí.

    1. V podokně **Správa agentů** vyberte buď možnost **Stáhnout agenta pro Windows (64 bitů)** , nebo **Stáhnout agenta pro Windows (bit 32) (** podle potřeby).

    1. Zkopírujte řetězce **ID pracovního prostoru**, **primární klíč** a **sekundární klíč** do textového souboru. Zkopírujte tento soubor a stažený instalační soubor do počítače s Windows. Spusťte instalační soubor a po zobrazení výzvy zadejte ID a řetězce klíčů v textovém souboru během instalace.

    1. Vraťte se do podokna **Brána firewall systému Windows** .

1. Klikněte na **instalovat řešení**.

### <a name="next-steps-tab"></a>Karta Další kroky

- Seznamte se s dostupnými sešity a ukázkami dotazů, které jsou připojené k datovým konektorem **brány Windows Firewall** . získáte tak přehled o datech protokolu brány Windows Firewall.

- K dotazování dat brány Windows Firewall v **protokolech** zadejte **WindowsFirewall** do okna dotazu.

## <a name="validate-connectivity"></a>Ověřit připojení
 
Vzhledem k tomu, že protokoly brány Windows Firewall se odesílají do služby Azure Sentinel jenom v případě, že místní soubor protokolu dosáhne kapacity, ponechte v protokolu výchozí velikost 4096 KB, což pravděpodobně způsobí latenci vysokého sběru dat. Snížit latenci můžete snížením velikosti souboru protokolu. Přečtěte si pokyny ke [konfiguraci protokolu brány Windows Firewall](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log). Počítejte s tím, že při definování minimální možné velikosti protokolu (1 KB) bude prakticky eliminovat latence shromažďování, ale může negativně ovlivnit výkon místního počítače. 

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit bránu Windows Firewall ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).