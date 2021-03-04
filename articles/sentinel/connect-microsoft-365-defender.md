---
title: Připojení dat Microsoft 365 Defenderu k Azure Sentinel | Microsoft Docs
description: Naučte se přijímat incidenty, výstrahy a data nezpracovaných událostí z Microsoft 365 Defenderu do Azure Sentinel.
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
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709338"
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

Konektor služby Azure Sentinel pro [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) s integrací incidentů umožňuje streamovat všechny incidenty M365D a výstrahy do Azure Sentinel a udržuje incidenty synchronizované mezi oběma portály. M365D incidenty zahrnují všechny výstrahy, entity a další relevantní informace, které jsou obohaceny a seskupují výstrahy ze služby M365D's Component Services **Microsoft Defender for Endpoint**, **Microsoft Defender for identity**, **Microsoft defender pro Office 365** a **Microsoft Cloud App Security**.

Konektor také umožňuje streamovat **Pokročilé** události v programu Microsoft Defender pro koncové body do Azure Sentinel, což vám umožní zkopírovat do Azure Sentinel pokročilé dotazy, vylepšit výstrahy Sentinel s daty s nezpracovanými událostmi MDE a zajistit další přehledy a ukládat protokoly se zvýšeným uchováním v Log Analytics.

Další informace o integraci incidentů a rozšířené shromažďování událostí pro lov, najdete v tématu [integrace programu Microsoft 365 Defender s funkcí Sentinel Azure](microsoft-365-defender-sentinel-integration.md).

> [!IMPORTANT]
>
> Konektor programu Microsoft 365 Defender je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

## <a name="prerequisites"></a>Požadavky

- Musíte mít platnou licenci pro Microsoft 365 Defender, jak je popsáno v tématu [požadavky Microsoft 365 Defenderu](/microsoft-365/security/mtp/prerequisites). 

- V Azure Active Directory musíte být **globálním správcem** nebo **správcem zabezpečení** .

## <a name="connect-to-microsoft-365-defender"></a>Připojení k Microsoft 365 Defenderu

1. V Azure Sentinel vyberte **datové konektory**, v galerii vyberte **Microsoft 365 Defender (Preview)** a vyberte **otevřít stránku konektor**.

1. V části **Konfigurace** v části **výstrahy & události připojit** výstrahy klikněte na tlačítko **připojit incidenty & výstrahy** .

1. Aby nedocházelo k duplicitám incidentů, doporučujeme, abyste označili zaškrtávací políčko s označením vypnout **všechna pravidla vytváření incidentů pro tyto produkty.**

    > [!NOTE]
    > Když povolíte konektor Microsoft 365 Defenderu, všechny konektory součástí M365D (ty, které jsou uvedené na začátku tohoto článku) se automaticky připojí na pozadí. Aby bylo možné odpojit jednu z konektorů komponent, je nutné nejprve odpojit konektor programu Microsoft 365 Defender.

1. K dotazování na data incidentů v M365 Defenderu použijte následující příkaz v okně dotazu:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Pokud chcete shromáždit pokročilé lovecké události od programu Microsoft Defender pro koncový bod, můžete shromáždit následující typy událostí z odpovídajících pokročilých tabulek pro lov.

    1. Zaškrtněte políčka v tabulkách s typy událostí, které chcete shromáždit:

       | Název tabulky | Typ událostí |
       |-|-|
       | DeviceInfo | Informace o počítači (včetně informací o operačním systému) |
       | DeviceNetworkInfo | Vlastnosti sítě počítačů |
       | DeviceProcessEvents | Vytváření procesů a související události |
       | DeviceNetworkEvents | Síťové připojení a související události |
       | DeviceFileEvents | Vytváření, úpravy a jiné události systému souborů |
       | DeviceRegistryEvents | Vytváření a úpravy položek registru |
       | DeviceLogonEvents | Přihlášení a jiné události ověřování |
       | DeviceImageLoadEvents | Události načítání DLL |
       | DeviceEvents | Další typy událostí |
       | DeviceFileCertificateInfo | Informace o certifikátu podepsaných souborů |
       |

    1. Klikněte na **použít změny**.

    1. Chcete-li zadat dotaz na pokročilé tabulky pro lov v Log Analytics, zadejte název tabulky ze seznamu výše v okně dotazu.

## <a name="verify-data-ingestion"></a>Ověřit přijímání dat

Datový graf na stránce konektor označuje, že přijímáte data. Všimněte si, že se zobrazí jedna řádka pro incidenty, výstrahy a události a řádek události je agregace objemu událostí napříč všemi povolenými tabulkami. Po povolení konektoru můžete pomocí následujících dotazů KQL vygenerovat další konkrétní grafy.

Pro graf příchozích incidentů M365 Defenderu použijte následující dotaz KQL:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Pomocí následujícího dotazu KQL vygenerujte graf svazku událostí pro jednu tabulku (změňte tabulku *DeviceEvents* na požadovanou tabulku, kterou zvolíte):

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

Na kartě **Další kroky** najdete některé užitečné sešity, Ukázkové dotazy a šablony analytického pravidla, které byly zahrnuty. Můžete je spustit na místě nebo je upravit a uložit.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak integrovat incidenty Microsoft 365 Defenderu a pokročilá data o loveckých událostech z Microsoft Defenderu pro koncový bod do Azure Sentinel pomocí konektoru Microsoft 365 Defenderu. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).
