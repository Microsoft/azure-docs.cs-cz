---
title: Připojit nezpracovaná data programu Microsoft 365 Defender do Azure Sentinel | Microsoft Docs
description: Naučte se přijímat nezpracovaná data událostí z Microsoft 365 Defenderu do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 756c245fe06ae81545a125dd98f30fb27fdff2dd
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655575"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Připojení dat z Microsoft 365 Defenderu k Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** byl dřív známý jako **Microsoft Threat Protection** nebo **MTP**.
>
> **Microsoft Defender pro koncové body** se dřív jmenoval jako **Rozšířená ochrana před internetovými útoky v programu Microsoft Defender** nebo **MDATP**.
>
> Můžou se zobrazovat staré názvy, které se v časovém intervalu pořád používají.

## <a name="background"></a>Pozadí

Nový konektor [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) umožňuje streamovat rozšířené protokoly o **loveckí** – typ nezpracovaných dat událostí – od Microsoft 365 Defenderu až po Azure Sentinel. 

Díky integraci programu [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) do bezpečnostního kolektoru v Microsoft 365 Defenderu teď můžete shromáždit [Rozšířené lovecké](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) události v programu Microsoft Defender pro koncové body pomocí konektoru Microsoft 365 Defenderu a streamovat je přímo do nových účelových tabulek v pracovním prostoru Sentinel Azure. Tyto tabulky jsou postavené na stejném schématu, které se používá na portálu Microsoft 365 Defenderu. získáte tak úplný přístup k celé sadě pokročilých protokolů pro lov a vy budete moct provádět tyto akce:

- Vaše stávající analytické dotazy v programu Microsoft Defender můžete snadno kopírovat do Azure Sentinel.

- Protokoly nezpracovaných událostí vám poskytnou další přehled o výstrahách, jejich vzájemném využívání a vyšetřování a korelují s daty z dalších zdrojů dat ve službě Azure Sentinel.

- Uložte protokoly se zvýšeným uchováváním, a to nad rámec programu Microsoft Defender pro koncový bod nebo výchozí uchování Microsoft 365 Defenderu po dobu 30 dnů. Můžete to udělat tak, že nakonfigurujete uchovávání pracovního prostoru nebo nakonfigurujete uchovávání podle jednotlivých tabulek v Log Analytics.

> [!IMPORTANT]
>
> Konektor programu Microsoft 365 Defender je aktuálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

- Musíte mít platnou licenci pro Microsoft Defender pro koncový bod, jak je popsáno v tématu [Nastavení programu Microsoft Defender pro nasazení koncového bodu](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- Uživateli musí být přiřazena role globálního správce klienta (v Azure Active Directory).

## <a name="connect-to-microsoft-365-defender"></a>Připojení k Microsoft 365 Defenderu

Pokud je Microsoft Defender pro koncový bod nasazený a ingestuje vaše data, můžete protokoly událostí snadno streamovat do služby Azure Sentinel.

1. V Azure Sentinel vyberte **datové konektory**, v galerii vyberte **Microsoft 365 Defender (Preview)** a vyberte **otevřít stránku konektor**.

1. Z odpovídajících pokročilých tabulek pro lov se můžou shromažďovat tyto typy událostí. Zaškrtněte políčka u typů událostí, které chcete shromáždit:

    | Typ událostí | Název tabulky |
    |-|-|
    | Informace o počítači (včetně informací o operačním systému) | DeviceInfo |
    | Vlastnosti sítě počítačů | DeviceNetworkInfo |
    | Vytváření procesů a související události | DeviceProcessEvents |
    | Síťové připojení a související události | DeviceNetworkEvents |
    | Vytváření, úpravy a jiné události systému souborů | DeviceFileEvents |
    | Vytváření a úpravy položek registru | DeviceRegistryEvents |
    | Přihlášení a jiné události ověřování | DeviceLogonEvents |
    | Události načítání DLL | DeviceImageLoadEvents |
    | Další typy událostí | DeviceEvents |
    |

1. Klikněte na **použít změny**. 

1. Chcete-li zadat dotaz na pokročilé tabulky pro lov v Log Analytics, zadejte název tabulky ze seznamu výše v okně dotazu.

## <a name="verify-data-ingestion"></a>Ověřit přijímání dat

Datový graf na stránce konektor označuje, že přijímáte data. Všimněte si, že se zobrazí jedna čára, která agreguje objem událostí napříč všemi povolenými tabulkami. Po povolení konektoru můžete pomocí následujícího dotazu KQL vygenerovat podobný graf svazku události pro jednu tabulku (změňte tabulku *DeviceEvents* na požadovanou tabulku, kterou zvolíte):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

Na kartě **Další kroky** najdete několik zahrnutých ukázkových dotazů. Můžete je spustit na místě, nebo je upravit a uložit.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak získat data nezpracovaných událostí z programu Microsoft Defender pro koncové body do Azure Sentinel pomocí konektoru Microsoft 365 Defenderu. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).