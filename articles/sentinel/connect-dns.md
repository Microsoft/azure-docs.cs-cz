---
title: Připojení dat DNS ve službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit data DNS ve službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588310"
---
# <a name="connect-your-domain-name-server"></a>Připojit váš názvový server domény

> [!IMPORTANT]
> Datový konektor DNS ve službě Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete připojit libovolný server DNS (Domain Name Server), který běží v systému Windows, do Azure Sentinel. K tomu slouží instalace agenta na počítač DNS. Pomocí protokolů DNS můžete získat přehledy o zabezpečení, výkonu a operacích souvisejících s provozem v infrastruktuře DNS vaší organizace tím, že shromažďujete, analyzujete a korelujete protokoly o analýze a auditu a další související data ze serverů DNS.

Pokud povolíte připojení protokolu DNS, můžete:
- Identifikujte klienty, kteří se pokoušejí přeložit názvy škodlivých domén.
- Identifikace zastaralých záznamů o prostředcích
- Identifikace často dotazovaných názvů domén a klientů DNS prahová
- Zobrazit zatížení žádostí na serverech DNS
- Zobrazit selhání dynamické registrace DNS

## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje, které podporují toto řešení:

| **Připojený zdroj** | **Podpora** | **Popis** |
| --- | --- | --- |
| [Agenti systému Windows](../azure-monitor/platform/agent-windows.md) | Ano | Řešení shromažďuje informace DNS z agentů Windows. |
| [Agenti systému Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Ne | Řešení neshromažďuje informace DNS z přímí agenti systému Linux. |
| [System Center Operations Manager skupina pro správu](../azure-monitor/platform/om-agents.md) | Ano | Řešení shromažďuje informace DNS z agentů v připojené skupině pro správu nástroje Operations Manager. Přímé připojení od agenta Operations Manager k Azure Monitor není vyžadováno. Data se přesměrovávají ze skupiny pro správu do pracovního prostoru Log Analytics. |
| [Účet služby Azure Storage](../azure-monitor/platform/collect-azure-metrics-logs.md) | Ne | Úložiště Azure se nepoužívá tímto řešením. |

### <a name="data-collection-details"></a>Podrobné informace o shromažďování dat

Řešení shromažďuje inventář DNS a dat týkajících se události DNS ze serverů DNS instalaci agenta Log Analytics. Shromažďuje data související s inventářem, jako je například počet serverů DNS, zóny a záznamy o prostředcích, spuštěním rutin Powershellu DNS. Data se aktualizují jednou každé dva dny. Data související s událostmi se shromažďují prakticky v reálném čase z [protokolů analýzy a auditu](https://technet.microsoft.com/library/dn800669.aspx#enhanc) , které nabízí rozšířené protokolování a diagnostika DNS ve Windows Serveru 2012 R2.


## <a name="connect-your-dns-appliance"></a>Připojení zařízení DNS

1. Na portálu Sentinel Azure vyberte **datové konektory** a klikněte na dlaždici **DNS (Preview)** .
1. Pokud jsou vaše počítače DNS v Azure:
    1. Klikněte na **nainstalovat agenta na virtuální počítač Azure s Windows**.
    1. V seznamu **virtuální počítače** vyberte počítač DNS, který chcete streamovat do Azure Sentinel. Ujistěte se, že se jedná o virtuální počítač s Windows.
    1. V okně, které se otevře pro daný virtuální počítač, klikněte na **připojit**.  
    1. V okně **konektoru DNS** klikněte na **Povolit** . 

2. Pokud Váš počítač DNS není virtuální počítač Azure:
    1. Klikněte na **instalovat agenta na počítačích mimo Azure**.
    1. V okně **přímý agent** vyberte buď možnost **Stáhnout agenta pro Windows (64 bitů)** , nebo **stáhnout agenta pro Windows (32 bitů)** .
    1. Nainstalujte agenta na počítač DNS. Zkopírujte **ID pracovního prostoru**, **primární klíč**a **sekundární klíč** a použijte je při zobrazení výzvy během instalace.

3. Pokud chcete pro protokoly DNS použít příslušné schéma v Log Analytics, vyhledejte **DnsEvents**.

## <a name="validate"></a>Ověření 

V Log Analytics vyhledejte **DnsEvents** schématu a ujistěte se, že existují události.

## <a name="troubleshooting"></a>Řešení potíží

Pokud se vyhledávací dotazy nezobrazují ve službě Azure Sentinel, postupujte podle těchto kroků, aby se dotazy zobrazovaly správně:
1. Zapněte [DNS Analytics protokoly na serverech](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Ujistěte se, že se DNSEvents zobrazí v seznamu kolekce Log Analytics.
3. Zapněte [Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md).
4. V Azure DNS Analytics v části **Konfigurace**změňte nastavení, uložte ho a pak ho znovu změňte, pokud potřebujete, a pak ho znovu uložte.
5. Zkontrolujte Azure DNS Analytics a ujistěte se, že jsou dotazy nyní zobrazovány.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit místní zařízení DNS ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
