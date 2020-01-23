---
title: Co je služba Personalizace?
titleSuffix: Azure Cognitive Services
description: Přizpůsobování je cloudová služba API, která umožňuje vybrat nejlepší prostředí pro zobrazování vašich uživatelů a seznámit se s jejich chováním v reálném čase.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: 756363d0c46dee6f7d0037fda48ab22dbdaeb0b0
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514295"
---
# <a name="what-is-personalizer"></a>Co je služba Personalizace?

Přizpůsobování Azure je cloudová služba API, která pomáhá klientské aplikaci zvolit nejlepší a jedinou položku _obsahu_ pro zobrazení jednotlivých uživatelů. Služba vybere nejlepší položku z položek obsahu na základě souhrnných informací v reálném čase, které poskytnete o obsahu a kontextu.

Po zobrazení položky obsahu uživateli systém monitoruje chování uživatelů a nahlásí skóre pro odměnu zpátky do přizpůsobené aplikace, aby se zlepšila možnost výběru nejlepšího obsahu na základě informací o kontextu, který obdrží.

**Obsah** může být libovolná jednotka informací, jako je text, obrázky, adresy URL nebo e-maily, které chcete vybrat, aby se uživateli zobrazila.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Jak přizpůsobuje výběr nejlepší položky obsahu?

Přizpůsobování využívá **posílení učení** k výběru nejlepší položky (_Akce_) na základě kolektivního chování a odměňování výsledků napříč všemi uživateli. Akce jsou položky obsahu, jako jsou například články s novinkami, konkrétní filmy nebo produkty, ze kterých si můžete vybrat.

Volání **pořadí** přijímá položku akce, společně s funkcemi akce a funkce kontextu pro výběr nejvyšší položky akce:

* **Akce s funkcemi** – položky obsahu s funkcemi specifickými pro jednotlivé položky
* **Kontextové funkce** – funkce uživatelů, jejich kontext nebo prostředí při použití vaší aplikace

Volání pořadí vrátí ID, které položky obsahu, __Akce__, která se má uživateli zobrazit, v poli **ID akce odměna** .
__Akce__ , která se zobrazí uživateli, je zvolená u modelů strojového učení a snaží se maximalizovat celkové množství nevyužité ceny v průběhu času.

Několik ukázkových scénářů:

|Typ obsahu|**Akce (s funkcemi)**|**Kontextové funkce**|Vráceno ID akce odměna<br>(Zobrazit tento obsah)|
|--|--|--|--|
|Seznam zpráv|a. `The president...` (National, politika, [text])<br>b. `Premier League ...` (Global, sport, [text, obrázek, video])<br> c. `Hurricane in the ...` (oblastní, počasí, [text, obrázek]|Zprávy ze zařízení se čtou z<br>Měsíc nebo období<br>|`The president...`|
|Seznam filmů|1. `Star Wars` (1977, [Action, Adventure and pohádk]; Jiří Lucas)<br>2. `Hoop Dreams` (1994, [dokumentace, Sport], Steve James<br>3. `Casablanca` (1942, [románské, drama, War], Michael Curtiz)|Video ze zařízení se bude sledovat<br>Velikost obrazovky<br>Typ uživatele<br>|3. `Casablanca`|
|Seznam produktů|i. `Product A` (3 kg, $ $ $ $, doručení za 24 hodin)<br>ii. `Product B` (20 kg, $ $, 2 týdny expedice s celním clem)<br>iii. `Product C` (3 kg, $ $ $, doručování za 48 hodin)|Nákupy zařízení se čtou z<br>Úroveň útraty uživatele<br>Měsíc nebo období|ii. `Product B`|

Přizpůsobené přizpůsobování, pomocí něhož se naučíte vybírat jednu nejlepší akci, která se označuje jako _ID akce_oddálení na základě kombinace:
* Školený model – informace o tom, kterou služba přizpůsobené
* Aktuální akce specifické pro data s funkcemi a funkcemi kontextu

## <a name="when-to-call-personalizer"></a>Kdy volat přizpůsobování

[Rozhraní API](https://go.microsoft.com/fwlink/?linkid=2092082) pro **řazení** přizpůsobeného objektu se volá _pokaždé_ , když prezentujete obsah v reálném čase. To se označuje jako **událost**zaznamenaná s _ID události_.

[Rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) pro **vlastní** nastavení se dá volat v reálném čase nebo opožděně, aby lépe vyhovovalo vaší infrastruktuře. Můžete určit skóre odměňování podle vašich obchodních potřeb. To může být jedna hodnota, například 1 pro dobrý, a 0 pro chybné nebo číslo vytvořené algoritmem, který vytvoříte s ohledem na obchodní cíle a metriky.

## <a name="personalizer-content-requirements"></a>Požadavky na obsah pro přizpůsobení obsahu

Použít přizpůsobeného přizpůsobování obsahu:

* Pro výběr z má omezená sada položek (max. ~ 50). Pokud máte větší seznam, [použijte modul doporučení](where-can-you-use-personalizer.md#use-personalizer-with-recommendation-engines) , který zmenší seznam na 50 položek.
* Obsahuje informace popisující obsah, který chcete seřadit: _akce s funkcemi_ a _funkcemi kontextu_.
* Má minimálně ~ 1 tisíc události související s obsahem pro přizpůsobení, aby byly účinné. Pokud přizpůsobený modul neobdrží potřebný minimální provoz, služba trvá déle a určí jednu nejlepší položku obsahu.

Vzhledem k tomu, že přidaný modul používá k vrácení jediné nejlepší položky obsahu informace v reálném čase téměř v reálném čase, služba nebude:
* Zachovat a spravovat informace o profilu uživatele
* Protokolovat Předvolby nebo historii jednotlivých uživatelů
* Vyžadovat vyčištěný a označený obsah

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Návrh a implementace přizpůsobení pro vaši klientskou aplikaci

1. [Navrhněte](concepts-features.md) a Naplánujte obsah, **_Akce_** a **_kontext_** . Určete algoritmus odměna pro skóre **_odměňování_** .
1. Každý vytvořený prostředek přizpůsobeného přizpůsobeného [prostředku](how-to-settings.md) se považuje za 1 smyčku učení. Smyčka získá jak pořadí, tak i možnosti pro daný obsah nebo uživatelské prostředí.
1. Přidání přizpůsobeného doplňku na web nebo systém obsahu:
    1. Přidejte volání **pořadí** do přizpůsobené aplikace, webu nebo systému a určete nejlepší, jednotlivou položku _obsahu_ před zobrazením obsahu uživateli.
    1. Zobrazí nejlepší, jednotlivou položku _obsahu_ , což je vráceno _ID akce odměna_pro uživatele.
    1. Použijte _algoritmus_ na shromážděné informace o tom, jak se uživatel chová, aby se určilo skóre **odměňování** , například:

        |Chování|Vypočtené skóre odměny|
        |--|--|
        |Uživatel zvolil nejlepší, jednu položku _obsahu_ (ID akce pro odměnu).|**1**|
        |Uživatel vybral jiný obsah.|**0**|
        |Uživatel byl pozastaven, je před tím nerozhodující, aby bylo možné vybrat nejlepší, jednu položku _obsahu_ (ID akce odměna).|**0,5**|

    1. Přidejte volání **odměňování** , které posílá skóre odměňování od 0 do 1.
        * Hned po zobrazení obsahu
        * Nebo někdy později v offline systému
    1. [Vyhodnoťte smyčku](concepts-offline-evaluation.md) pomocí offline zkušebního období po určité době použití. Testování v režimu offline umožňuje testovat a hodnotit efektivitu služby přizpůsobeného službou bez změny kódu nebo ovlivnění uživatelského prostředí.

## <a name="next-steps"></a>Další kroky


* [Jak přizpůsobovat práci](how-personalizer-works.md)
* [Co je posílení učení?](concepts-reinforcement-learning.md)
* [Informace o funkcích a akcích pro požadavek na řazení](concepts-features.md)
* [Informace o určování skóre pro žádost o odměnu](concept-rewards.md)
* [Rychlé starty]()
* [Kurz]()
* [Použití interaktivní ukázky](https://personalizationdemo.azurewebsites.net/)
