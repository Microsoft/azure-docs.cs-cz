---
title: Sešity Azure Monitor u tváření parametrů
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658213"
---
# <a name="workbook-parameters"></a>Parametry sešitu

Parametry umožňují autorům sešitu shromažďovat vstupy od spotřebitelů a odkazovat na něj v jiných částech sešitu – obvykle k vymezení sady výsledků nebo nastavení správného vizuálu. Jedná se o klíčovou schopnost, která umožňuje autorům vytvářet interaktivní zprávy a zkušenosti. 

Sešity umožňují řídit, jak jsou ovládací prvky parametrů prezentovány spotřebitelům – textové pole vs. rozevírací seznam, výběr s jedním vs. více násobné, hodnoty z textu, JSON, KQL nebo Azure Resource Graph atd.  

Mezi podporované typy parametrů patří:
* [Čas](workbooks-time.md) - umožňuje uživateli vybrat z předem vyplněných časových rozsahů nebo vybrat vlastní rozsah
* [Rozevírací obsah](workbooks-dropdowns.md) - umožňuje uživateli vybrat z hodnoty nebo sady hodnot
* [Text](workbooks-text.md) - umožňuje uživateli zadat libovolný text
* [Prostředek](workbooks-resources.md) – umožňuje uživateli vybrat jeden nebo více prostředků Azure
* [Předplatné](workbooks-resources.md) – umožňuje uživateli vybrat jeden nebo více prostředků předplatného Azure
* Typ prostředku – umožňuje uživateli vybrat jednu nebo více hodnot typu prostředku Azure.
* Umístění – umožňuje uživateli vybrat jednu nebo více hodnot umístění Azure

Na tyto hodnoty parametrů lze odkazovat v jiných částech sešitů prostřednictvím vazeb nebo rozšíření hodnoty.

## <a name="creating-a-parameter"></a>Vytvoření parametru
1. Začněte s prázdným sešitem v režimu úprav.
2. Z odkazů v sešitu zvolte _Přidat parametry._
3. Klikněte na modré tlačítko _Přidat parametr._
4. V novém podokně parametrů, které se objeví, zadejte:
    1. Název parametru: `TimeRange` *(Všimněte si, že __názvy__ parametrů **nemohou** obsahovat mezery nebo speciální znaky)*
    2. Zobrazovaný `Time Range`název: * __(zobrazované názvy__ však mohou obsahovat mezery, speciální znaky, emodži atd.)*  
    2. Typ parametru:`Time range picker`
    3. Požadované:`checked`
    4. Dostupné časové rozsahy: Poslední hodina, Posledních 12 hodin, Posledních 24 hodin, Posledních 48 hodin, Posledních 3 dní, Posledních 7 dní a Povolit výběr vlastního časového rozsahu
5. Chcete-li vytvořit parametr, zvolte "Uložit".

   ![Obrázek znázorňující vytvoření parametru časového rozsahu](./media/workbooks-parameters/time-settings.png)

Takto bude vypadat sešit v režimu čtení ve stylu "Pilulky".

   ![Obrázek znázorňující parametr časového rozsahu v režimu čtení](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Odkazování na parametr
### <a name="via-bindings"></a>Přes vázání
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. Otevřete rozevírací seznam `Time Range` Časový _rozsah_ a vyberte volbu z části Parametry dole.
3. Tím se sváže parametr časového rozsahu s časovým rozsahem grafu. Časový rozsah ukázkového dotazu je nyní Posledních 24 hodin.
4. Spuštění dotazu pro zobrazení výsledků

    ![Obrázek znázorňující parametr časového rozsahu odkazovaný pomocí vazeb](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>V KQL
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. V kql zadejte filtr časového oboru pomocí parametru:`| where timestamp {TimeRange}`
3. Tím se zrozšiřuje doba `| where timestamp > ago(1d)`vyhodnocení dotazu na , což je hodnota časového rozsahu parametru.
4. Spuštění dotazu pro zobrazení výsledků

    ![Obrázek znázorňující časový rozsah odkazovaný v KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>V textu 
1. Přidání textového ovládacího prvku do sešitu
2. Do markdownu zadejte`The chosen time range is {TimeRange:label}`
3. Zvolit _hotovou úpravu_
4. Ovládací prvek textu zobrazí text: _Zvolený časový rozsah je Posledních 24 hodin_

## <a name="parameter-options"></a>Možnosti parametrů
V části _Text_ `label` používá parametr namísto jeho hodnoty. Parametry zveřejňují různé takové možnosti v závislosti na typu - například výběr časového rozsahu povolit hodnotu, popisek, dotaz, začátek, konec a zrnitost.

V `Previews` části podokna _Upravit parametr_ můžete zobrazit možnosti rozšíření parametru:

![Obrázek znázorňující volby parametrů časového rozsahu](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Další kroky

* [Začínáte](workbooks-visualizations.md) se učit další informace o sešitech, mnoho bohatých možností vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu
