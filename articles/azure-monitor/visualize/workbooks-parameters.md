---
title: Sešity Azure Monitor vytváření parametrů
description: Přečtěte si, jak parametry umožňují autorům sešitu shromažďovat vstup od spotřebitelů a odkazovat na ně v jiných částech sešitu.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 95e810373ac5a591c530004e8d1175c3300656f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700554"
---
# <a name="workbook-parameters"></a>Parametry sešitu

Parametry umožňují autorům sešitu shromažďovat vstup od spotřebitelů a odkazovat na ně v jiných částech sešitu – obvykle se zabývají rozsahem sady výsledků dotazu nebo nastavením správného vizuálu. Jedná se o klíčovou funkci, která umožňuje autorům vytvářet interaktivní sestavy a prostředí. 

Pracovní sešity umožňují řídit způsob, jakým jsou ovládací prvky parametrů prezentovány uživatelům – textové pole vs. rozevírací seznam, jednoduché vs. vícenásobný výběr, hodnoty z textu, JSON, KQL nebo Azure Resource Graph atd.  

Mezi podporované typy parametrů patří:
* [Čas](workbooks-time.md) – umožní uživateli vybrat z předem vyplněných časových rozsahů nebo vybrat vlastní rozsah.
* [Rozevírací seznam](workbooks-dropdowns.md) – umožňuje uživateli vybrat z hodnoty nebo sady hodnot.
* [Text](workbooks-text.md) – umožní uživateli zadat libovolný text.
* [Prostředek](workbooks-resources.md) – umožní uživateli vybrat jeden nebo víc prostředků Azure.
* [Předplatné](workbooks-resources.md) – umožní uživateli vybrat jeden nebo víc prostředků předplatného Azure.
* Typ prostředku – umožní uživateli vybrat jednu nebo víc hodnot typu prostředku Azure.
* Umístění – umožní uživateli vybrat jednu nebo víc hodnot umístění Azure.

Na tyto hodnoty parametrů se dá odkazovat v jiných částech sešitů, a to prostřednictvím rozšíření vazby nebo rozšíření hodnoty.

## <a name="creating-a-parameter"></a>Vytvoření parametru
1. Začněte s prázdným sešitem v režimu úprav.
2. Vyberte možnost _přidat parametry_ z odkazů v rámci sešitu.
3. Klikněte na modré tlačítko _Přidat parametr_ .
4. V podokně nového parametru, které se objeví, zadejte:
    1. Název parametru: `TimeRange` *(Všimněte si, že __názvy__ parametrů **nemůžou** obsahovat mezery ani speciální znaky.)*
    2. Zobrazované jméno: `Time Range` *(ale __zobrazované názvy__ můžou obsahovat mezery, speciální znaky, Emoji atd.)*  
    2. Typ parametru: `Time range picker`
    3. Požadovanou `checked`
    4. Dostupné časové rozsahy: poslední hodina, posledních 12 hodin, posledních 24 hodin, poslední 48 hodin, poslední 3 dny, posledních 7 dní a povolený výběr vlastního časového rozsahu
5. Pokud chcete vytvořit parametr, klikněte na tlačítko Uložit na panelu nástrojů.

   ![Obrázek znázorňující vytvoření parametru časového rozsahu](./media/workbooks-parameters/time-settings.png)

V takovém případě bude sešit vypadat jako v režimu čtení, ve stylu "Pills".

   ![Obrázek znázorňující parametr časového rozsahu v režimu čtení](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Odkazování na parametr
### <a name="via-bindings"></a>Přes vazby
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. Otevřete rozevírací seznam _časový rozsah_ a vyberte možnost v `Time Range` části parametry v dolní části.
3. Tím se naváže parametr časového rozsahu na časový rozsah grafu. Časový rozsah ukázkového dotazu je nyní za posledních 24 hodin.
4. Spusťte dotaz, aby se zobrazily výsledky.

    ![Obrázek znázorňující parametr časového rozsahu, na který se odkazuje pomocí vazeb](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>V KQL
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. Do KQL zadejte filtr oboru času pomocí parametru: `| where timestamp {TimeRange}`
3. Tím se rozšíří doba vyhodnocování dotazu na `| where timestamp > ago(1d)` , což je hodnota časového rozsahu parametru.
4. Spusťte dotaz, aby se zobrazily výsledky.

    ![Obrázek znázorňující časový rozsah, na který odkazuje KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>V textu 
1. Přidejte textový ovládací prvek do sešitu.
2. Do Markdownu zadejte `The chosen time range is {TimeRange:label}`
3. Zvolit _hotové úpravy_
4. Ovládací prvek text zobrazí text: _zvolený časový rozsah je posledních 24 hodin_ .

## <a name="parameter-options"></a>Možnosti parametrů
Oddíl _v textu_ použil `label` parametr místo jeho hodnoty. Parametry zveřejňují různé možnosti v závislosti na typu, např. výběry časových rozsahů umožňují hodnotu, popisek, dotaz, začátek, konec a zrnitost.

Pomocí `Previews` části v podokně _Upravit parametr_ můžete zobrazit možnosti rozšíření pro váš parametr:

![Obrázek ukazující možnosti parametru časového rozsahu](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Další kroky

* [Začínáme](./workbooks-overview.md#visualizations) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.
* [Řízení](./workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.