---
title: Po propojení dat DNS ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Zjistěte, jak připojit data DNS v Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 4e6ed18a49a77f8061c975bdf3ecb085ebf71317
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190769"
---
# <a name="connect-your-domain-name-server"></a>Připojení serveru název domény

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Libovolný Server DNS (Domain Name) běží na Windows Azure Sentinelu se můžete připojit. To provádí instalací agenta na počítači DNS. Protokoly pomocí DNS, můžete získat zabezpečení, výkon a operace související informace o infrastruktuře DNS vaší organizace pomocí shromažďování, analýza, a korelace analýzy a protokoly auditu a dalších souvisejících dat ze serverů DNS.

Když povolíte připojení protokolu DNS můžete:
- Identifikace klientů, které se pokoušejí přeložit škodlivé domény názvy
- Identifikace zastaralých záznamů
- Identifikujte často doménový názvy a prahová klienti DNS
- Zobrazení žádosti o zatížení serverů DNS
- Selhání registrace DNS dynamického zobrazení

## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje, které podporují toto řešení:

| **Připojený zdroj** | **Podpora** | **Popis** |
| --- | --- | --- |
| [Agenti systému Windows](../azure-monitor/platform/agent-windows.md) | Ano | Řešení shromažďuje informace DNS z agentů Windows. |
| [Agenti systému Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Ne | Řešení neshromažďuje informace DNS z přímí agenti systému Linux. |
| [Skupina pro správu System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Ano | Řešení shromažďuje informace DNS z agentů v připojené skupině pro správu nástroje Operations Manager. Přímé připojení z agenta nástroje Operations Manager do Azure monitoru se nevyžaduje. Data se přesměrovávají ze skupiny pro správu do pracovního prostoru Log Analytics. |
| [Účet služby Azure Storage](../azure-monitor/platform/collect-azure-metrics-logs.md) | Ne | Úložiště Azure se nepoužívá tímto řešením. |

### <a name="data-collection-details"></a>Podrobné informace o shromažďování dat

Řešení shromažďuje inventář DNS a dat týkajících se události DNS ze serverů DNS instalaci agenta Log Analytics. Shromažďuje data související s inventářem, jako je například počet serverů DNS, zóny a záznamy o prostředcích, spuštěním rutin Powershellu DNS. Data se aktualizují jednou každé dva dny. Událost související data se shromažďují v reálném čase z [analýzy a protokoly auditu](https://technet.microsoft.com/library/dn800669.aspx#enhanc) poskytuje rozšířené protokolování DNS a Diagnostika ve Windows serveru 2012 R2.


## <a name="connect-your-dns-appliance"></a>Připojit zařízení DNS

1. Na portálu Azure Sentinelu vyberte **datové konektory** a zvolte **DNS** dlaždici.
1. Pokud jsou vaše počítače DNS v Azure:
    1. Klikněte na tlačítko **instalace agenta na virtuálním počítači Windows Azure**.
    1. V **virtuálních počítačů** seznamu, vyberte počítač DNS chcete Streamovat do ověřovacích Azure. Ujistěte se, že jde o virtuálního počítače s Windows.
    1. V okně, které se otevře pro tento virtuální počítač, klikněte na tlačítko **připojit**.  
    1. Klikněte na tlačítko **povolit** v **DNS konektor** okna. 

2. Pokud váš počítač DNS není virtuální počítač Azure:
    1. Klikněte na tlačítko **instalace agenta do počítače mimo Azure**.
    1. V **přímý agent** okna, vyberte buď **Windows stáhnout agenta (64bitová verze)** nebo **Windows stáhnout agenta (32 bitů)** .
    1. Nainstalujte agenta na počítači DNS. Kopírovat **ID pracovního prostoru**, **primární klíč**, a **sekundární klíč** a využít při zobrazení výzvy v průběhu instalace.

3. Chcete-li použít příslušné schéma v Log Analytics pro protokoly DNS, vyhledejte **DnsEvents**.

## <a name="validate"></a>Ověření 

Ve službě Log Analytics vyhledejte schématu **DnsEvents** a ujistěte se, že existují události.

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit na místním zařízení DNS k Sentinelu Azure. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).
