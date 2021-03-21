---
title: Power BI výstup z Azure Stream Analytics
description: Tento článek popisuje, jak výstupní data z Azure Stream Analytics do Power BI.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: a94389a075fd62d80345a21e32f1bc977dfdee87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020056"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Power BI výstup z Azure Stream Analytics

[Power BI](https://powerbi.microsoft.com/) můžete použít jako výstup pro úlohu Stream Analytics a poskytnout tak bohatou vizualizaci výsledků analýzy. Tuto možnost můžete použít pro provozní řídicí panely, generování sestav a vytváření sestav řízených metrikami.

Power BI výstup z Stream Analytics v současnosti není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní).

## <a name="output-configuration"></a>Konfigurace výstupu

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro konfiguraci Power BIho výstupu.

| Název vlastnosti | Description |
| --- | --- |
| Alias pro výstup |Zadejte popisný název, který se použije v dotazech k nasměrování výstupu dotazu do tohoto Power BI výstupu. |
| Pracovní prostor skupiny |Pokud chcete povolit sdílení dat s jinými Power BI uživateli, můžete vybrat skupiny v rámci účtu Power BI nebo zvolit **pracovní prostor** , pokud ho nechcete zapisovat do skupiny. Aktualizace existující skupiny vyžaduje obnovení Power BI ověřování. |
| Název datové sady |Zadejte název datové sady, který má výstup Power BI použít. |
| Název tabulky |Pod datovou sadou výstupu Power BI zadejte název tabulky. V současné době může Power BI výstup z úloh Stream Analytics obsahovat pouze jednu tabulku v datové sadě. |
| Autorizovat připojení | Abyste mohli konfigurovat nastavení výstupu, musíte se autorizovat s Power BI. Po udělení tohoto výstupního přístupu vašemu řídicímu panelu Power BI můžete odvolat přístup změnou hesla uživatelského účtu, odstraněním výstupu úlohy nebo odstraněním úlohy Stream Analytics. | 

Návod ke konfiguraci Power BIho výstupu a řídicího panelu najdete v kurzu [Azure Stream Analytics a Power BI](stream-analytics-power-bi-dashboard.md) .

> [!NOTE]
> Datovou sadu a tabulku nevytvářejte explicitně na řídicím panelu Power BI. Datová sada a tabulka se automaticky naplní při zahájení úlohy a úloha začne vyčerpat výstup do Power BI. Pokud dotaz úlohy negeneruje žádné výsledky, datová sada a tabulka se nevytvoří. Pokud Power BI již měla datovou sadu a tabulku se stejným názvem jako v této Stream Analytics úlohy, stávající data budou přepsána.
>

### <a name="create-a-schema"></a>Vytvoření schématu

Azure Stream Analytics vytvoří datovou sadu Power BI a schéma tabulky pro uživatele, pokud ještě neexistují. Ve všech ostatních případech je tabulka aktualizována o nové hodnoty. V současné době může existovat pouze jedna tabulka v rámci datové sady. 

Power BI používá zásady uchovávání dat first in, First-out (FIFO). Data se budou shromažďovat v tabulce, dokud nebude mít 200 000 řádků.

> [!NOTE]
> Nedoporučujeme používat více výstupů k zápisu do stejné datové sady, protože může způsobit několik problémů. Každý výstup se pokusí vytvořit Power BI datovou sadu nezávisle, což může mít za následek více datových sad se stejným názvem. Kromě toho, pokud výstupy nemají konzistentní schémata, datová sada změní schéma u každého zápisu, což vede k příliš velkému počtu žádostí o změnu schématu. I když se tyto problémy vyhnete, Vícenásobné výstupy budou méně výkonné než jeden sloučený výstup.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Převod datového typu z Stream Analytics na Power BI

Pokud se změní výstupní schéma, Azure Stream Analytics aktualizuje datový model dynamicky za běhu. Změny názvu sloupce, změny typu sloupce a přidání nebo odebrání sloupců jsou sledovány.

Tato tabulka popisuje převody datových typů z [Stream Analytics typy dat](/stream-analytics-query/data-types-azure-stream-analytics) do Power BI [model EDM (Entity Data Model) (EDM)](/dotnet/framework/data/adonet/entity-data-model), pokud Power BI datová sada a tabulka neexistuje.

Z Stream Analytics | Pro Power BI
-----|-----
bigint | Int64
nvarchar (max) | Řetězec
datetime | Datum a čas
float | dvojité
Pole záznamu | Typ řetězce, konstantní hodnota "IRecord" nebo "IArray"

### <a name="update-the-schema"></a>Aktualizace schématu

Stream Analytics odvodí schéma datového modelu na základě první sady událostí ve výstupu. Později v případě potřeby se schéma datového modelu aktualizuje tak, aby odpovídal příchozím událostem, které se nemusí vejít do původního schématu.

Nepoužívejte `SELECT *` dotaz, aby nedocházelo k dynamické aktualizaci schématu napříč řádky. Kromě potenciálních dopadů na výkon může být výsledkem nejistota doba trvání výsledků. Vyberte přesná pole, která se musí zobrazit na řídicím panelu Power BI. Kromě toho musí být hodnoty dat kompatibilní se zvoleným datovým typem.

Předchozí/aktuální | Int64 | Řetězec | Datum a čas | dvojité
-----------------|-------|--------|----------|-------
Int64 | Int64 | Řetězec | Řetězec | dvojité
dvojité | dvojité | Řetězec | Řetězec | dvojité
Řetězec | Řetězec | Řetězec | Řetězec | Řetězec 
Datum a čas | Řetězec | Řetězec |  Datum a čas | Řetězec

## <a name="output-batch-size"></a>Velikost výstupní dávky

Velikost výstupní dávky najdete v tématu [Power BI omezení rozhraní REST API](/power-bi/developer/automation/api-rest-api-limitations).

## <a name="next-steps"></a>Další kroky

* [Použití spravované identity k ověření Azure Stream Analytics úlohy do Power BI (Preview)](powerbi-output-managed-identity.md)
* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)