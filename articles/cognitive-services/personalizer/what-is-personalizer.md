---
title: Co je služba Personalizace?
description: Personalizátor je cloudová služba rozhraní API, která vám umožní vybrat nejlepší prostředí, které se zobrazí vašim uživatelům, a učit se z jejich chování v reálném čase.
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 3ae425479d764c0a6bf6c63bdd54a964c48af8b6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687267"
---
# <a name="what-is-personalizer"></a>Co je služba Personalizace?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Personalizer je cloudová služba rozhraní API, která pomáhá klientské aplikaci vybrat nejlepší položku s jedním _obsahem,_ která se zobrazí každému uživateli. Služba vybere nejlepší položku z položek obsahu na základě kolektivních informací v reálném čase, které poskytnete o obsahu a kontextu.

Po prezentaci položky obsahu uživateli systém sleduje chování uživatelů a hlásí skóre odměny zpět personalistovi, aby zlepšil jeho schopnost vybrat nejlepší obsah na základě kontextových informací, které obdrží.

**Obsahem** může být libovolná jednotka informací, jako je text, obrázky, adresy URL nebo e-maily, ze kterých chcete vybrat, aby se zobrazila uživateli.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Jak personalizátor vybírá nejlepší položku obsahu?

Personalizár používá **posilování učení** vybrat nejlepší položku (_akce_) na základě kolektivního chování a odměnit skóre na příčku všech uživatelů. Akce jsou položky obsahu, jako jsou zpravodajské články, konkrétní filmy nebo produkty, ze kterých si můžete vybrat.

Volání **Pořadí** přebírá položku akce spolu s funkcemi akce a kontextovými funkcemi pro výběr nejvyšší položky akce:

* **Akce s funkcemi** – položky obsahu s funkcemi specifickými pro každou položku
* **Kontextové funkce** – funkce uživatelů, jejich kontext nebo prostředí při používání aplikace

Volání Pořadí vrátí ID, která položka obsahu, __akce__, se uživateli zobrazí v poli **ID akce odměny.**
__Akce__ zobrazená uživateli je vybrána pomocí modelů strojového učení a snaží se maximalizovat celkovou částku odměn v průběhu času.

Několik příkladů scénářů jsou:

|Typ obsahu|**Akce (s funkcemi)**|**Kontextové funkce**|ID vrácené akce odměny<br>(zobrazit tento obsah)|
|--|--|--|--|
|Seznam novinek|a. `The president...`(národní, politika, [text])<br>b. `Premier League ...`(globální, sportovní, [text, obrázek, video])<br> c. `Hurricane in the ...`(regionální, počasí, [text,obrázek]|Zprávy o zařízení se čtou z<br>Měsíc nebo sezóna<br>|A`The president...`|
|Seznam filmů|1. `Star Wars` (1977, [akce, dobrodružství, fantazie], George Lucas)<br>2. `Hoop Dreams` (1994, [dokumentární, sportovní], Steve James<br>3. `Casablanca` (1942, [romantika, drama, válka], Michael Curtiz)|Film o zařízení je zhlédnout z<br>velikost obrazovky<br>Typ uživatele<br>|3.`Casablanca`|
|Seznam produktů|i. `Product A`(3 kg, $$$$, dodání za 24 hodin)<br>ii. `Product B`(20 kg, $$, 2 týdenní přeprava s celnicí)<br>iii. `Product C`(3 kg, $$$, dodání za 48 hodin)|Nakupování zařízení se čte z<br>Úroveň útraty uživatele<br>Měsíc nebo sezóna|ii. `Product B`|

Personalista použil výztuže učení vybrat jednu nejlepší akci, známý jako _odměna akce ID_, na základě kombinace:
* Trénovaný model - minulé informace, které obdržela služba Personalista
* Aktuální data – specifické akce s funkcemi a kontextovými funkcemi

## <a name="when-to-call-personalizer"></a>Kdy zavolat personalistu

[Rozhraní](https://go.microsoft.com/fwlink/?linkid=2092082) API **ranku** personalisty se nazývá _pokaždé, když_ prezentujete obsah v reálném čase. Tento událost se označuje jako **událost**, označená _ID události_.

Rozhraní [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) **pro odměňování** personalisty lze volat v reálném čase nebo zpožděno tak, aby lépe vyhovovalo vaší infrastruktuře. Skóre odměny určujete na základě vašich obchodních potřeb. Skóre odměny se pohybuje mezi 0 a 1. To může být jedna hodnota, například 1 pro dobro a 0 pro špatné, nebo číslo vyrobené algoritmem, který vytvoříte s ohledem na vaše obchodní cíle a metriky.

## <a name="personalizer-content-requirements"></a>Požadavky na personalizaci obsahu

Při použití personalistik a obsahu:

* Má omezenou sadu položek (max ~ 50) z výběru. Pokud máte větší seznam, [použijte modul doporučení](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) snížit seznam až na 50 položek.
* Obsahuje informace popisující obsah, který chcete seřadit: _akce s funkcemi_ a _kontextovými funkcemi_.
* Má minimálně ~ 1k / den obsah-související události pro Personalizace, aby byly účinné. Pokud personalizátor neobdrží minimální požadovaný provoz, služba trvá déle určit jednu nejlepší položku obsahu.

Vzhledem k tomu, že personalista používá kolektivní informace téměř v reálném čase k vrácení jedné nejlepší položky obsahu, služba ne:
* Uchování a správa informací o profilu uživatele
* Protokolovat předvolby nebo historii jednotlivých uživatelů
* Vyžadovat vyčištěný a označený obsah

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Jak navrhnout a implementovat personalizace pro vaši klientskou aplikaci

1. [Návrh](concepts-features.md) a plánování obsahu, **_akcí_** a **_kontextu_**. Určete algoritmus odměny za skóre **_odměny._**
1. Každý [prostředek personalizátoru,](how-to-settings.md) který vytvoříte, je považován za 1 výukový smyčku. Smyčka obdrží výzvy hodnosti i odměny pro tento obsah nebo uživatelské prostředí.
1. Přidejte personalizátor na vaše webové stránky nebo obsahový systém:
    1. Přidejte volání **Pořadí** do personalistu ve vaší aplikaci, webu nebo systému, abyste určili nejlepší položku _obsahu_ před zobrazením obsahu uživateli.
    1. Zobrazit uživateli nejlepší _položku s_ jedním obsahem, což je _ID vrácené akce odměny_.
    1. Použijte _algoritmus_ pro shromážděné informace o tom, jak se uživatel choval, k určení skóre **odměny,** například:

        |Chování|Vypočtené skóre odměny|
        |--|--|
        |Uživatel vybral nejlepší položku s jedním _obsahem_ (ID akce odměny)|**1**|
        |Uživatel vybral jiný obsah|**0**|
        |Uživatel se pozastavil, nerozhodně se posouval, než vybral nejlepší položku _obsahu_ (ID akce odměny)|**0,5**|

    1. Přidání **výzvy k odměňování,** které zasílá skóre odměny mezi 0 a 1
        * Ihned po zobrazení obsahu
        * Nebo někdy později v offline systému
    1. Po určité době používání [vyhodnoťte smyčku](concepts-offline-evaluation.md) pomocí offline vyhodnocení. Offline vyhodnocení umožňuje testovat a vyhodnocovat účinnost služby personalisty bez změny kódu nebo ovlivnění uživatelského prostředí.

## <a name="next-steps"></a>Další kroky


* [Jak služba Personalizace funguje](how-personalizer-works.md)
* [Co je posilování učení?](concepts-reinforcement-learning.md)
* [Informace o funkcích a akcích pro žádost o hodnost](concepts-features.md)
* [Informace o určení skóre žádosti o odměnu](concept-rewards.md)
* [Rychlý start](sdk-learning-loop.md)
* [Tutoriál](tutorial-use-azure-notebook-generate-loop-data.md)
* [Použití interaktivní ukázky](https://personalizationdemo.azurewebsites.net/)
