---
title: Připojení dat DNS ve službě Azure Sentinel| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data DNS v Azure Sentinelu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588310"
---
# <a name="connect-your-domain-name-server"></a>Připojení názvového serveru domény

> [!IMPORTANT]
> Datový konektor DNS v Azure Sentinelu je momentálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

K Azure Sentinelu můžete připojit libovolný server DNS (DNS) spuštěný v systému Windows. To se provádí instalací agenta do počítače DNS. Pomocí protokolů DNS můžete získat přehled o zabezpečení, výkonu a operacích infrastruktury DNS vaší organizace shromažďováním, analýzou a korelací protokolů analytických a auditovaných dat a dalších souvisejících dat ze serverů DNS.

Pokud povolíte připojení protokolu DNS, můžete:
- Identifikace klientů, kteří se pokoušejí přeložit škodlivá názvy domén
- Identifikace zastaralých záznamů o prostředcích
- Identifikace často dotazovaných názvů domén a hovorných klientů DNS
- Zobrazení zatížení požadavků na serverech DNS
- Zobrazení dynamických chyb registrace DNS

## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje podporované tímto řešením:

| **Připojený zdroj** | **Podpora** | **Popis** |
| --- | --- | --- |
| [Agenti systému Windows](../azure-monitor/platform/agent-windows.md) | Ano | Řešení shromažďuje informace DNS od agentů systému Windows. |
| [Agenti systému Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Ne | Řešení neshromažďuje informace DNS od přímých agentů Linuxu. |
| [Skupina pro správu nástroje System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Ano | Řešení shromažďuje informace DNS od agentů v připojené skupině pro správu nástroje Operations Manager. Přímé připojení agenta Operations Manager u Azure Monitor není vyžadováno. Data se předávají ze skupiny pro správu do pracovního prostoru Log Analytics. |
| [Účet služby Azure Storage](../azure-monitor/platform/collect-azure-metrics-logs.md) | Ne | Úložiště Azure se nepoužívá řešení. |

### <a name="data-collection-details"></a>Podrobnosti o shromažďování dat

Řešení shromažďuje data inventáře DNS a událostí DNS ze serverů DNS, kde je nainstalován agent Analýzy protokolů. Data související se inventářem, například počet serverů DNS, zón a záznamů o prostředcích, se shromažďují spuštěním rutin dns prostředí PowerShell. Data jsou aktualizována jednou za dva dny. Data související s událostmi jsou shromažďována téměř v reálném čase z [analytických protokolů a protokolů auditu poskytovaných](https://technet.microsoft.com/library/dn800669.aspx#enhanc) rozšířeným protokolováním a diagnostikou DNS v systému Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Připojení zařízení DNS

1. Na portálu Azure Sentinel vyberte **datové konektory** a zvolte dlaždici **DNS (Preview).**
1. Pokud jsou vaše počítače DNS v Azure:
    1. Klikněte **na Nainstalovat agenta na virtuálním počítači Azure Windows**.
    1. V seznamu **Virtuální počítače** vyberte počítač DNS, který chcete streamovat do Azure Sentinelu. Ujistěte se, že se jedná o virtuální hod windows.
    1. V okně, které se otevře pro tento virtuální virtuální ms, klikněte na **Připojit**.  
    1. V okně **konektoru DNS** klikněte na **Povolit.** 

2. Pokud váš počítač DNS není virtuální počítač Azure:
    1. Klikněte **na nainstalovat agenta na počítačích jiného než Azure**.
    1. V okně **Přímého agenta** vyberte **buď stáhnout agenta systému Windows (64 bit)** nebo **stáhnout agenta systému Windows (32 bit)**.
    1. Nainstalujte agenta do počítače DNS. Zkopírujte **ID pracovního prostoru**, **primární klíč**a sekundární **klíč** a použijte je po zobrazení výzvy během instalace.

3. Chcete-li použít příslušné schéma v Log Analytics pro protokoly DNS, vyhledejte **DnsEvents**.

## <a name="validate"></a>Ověření 

V Log Analytics vyhledejte schéma **DnsEvents** a ujistěte se, že jsou události.

## <a name="troubleshooting"></a>Řešení potíží

Pokud se vyhledávací dotazy v Azure Sentinelu nezobrazují, postupujte takto, aby se dotazy správně zobrazily:
1. Zapněte [protokoly DNS Analytics na svých serverech](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Ujistěte se, že dnsevents se zobrazí v seznamu kolekce Log Analytics.
3. Zapněte [Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md).
4. V Azure DNS Analytics v části **Konfigurace**změňte libovolné nastavení, uložte ho, v případě potřeby ho změňte zpět a pak ho znovu uložte.
5. Zkontrolujte Azure DNS analytics a ujistěte se, že dotazy jsou nyní zobrazeny.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit místní zařízení DNS k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
