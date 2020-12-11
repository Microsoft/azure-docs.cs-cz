---
title: Co je služba Personalizace?
description: Přizpůsobování je cloudová služba, která umožňuje vybrat nejlepší prostředí pro zobrazení uživatelů a seznámit se s jeho chováním v reálném čase.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: přizpůsobování, přizpůsobování v Azure, Machine Learning
ms.openlocfilehash: 28aae130d062eaf57a66a9b90a6602c3874f1494
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094144"
---
# <a name="what-is-personalizer"></a>Co je služba Personalizace?

Přizpůsobování Azure je cloudová služba, která umožňuje vašim aplikacím zvolit nejlepší položku obsahu pro zobrazení uživatelů. Pomocí služby přizpůsobené aplikace můžete určit, který produkt má navrhnout, aby se nakupující, nebo zjistit optimální pozici reklamy. Po zobrazení obsahu uživatelem systém monitoruje chování uživatele v reálném čase a oznamuje skóre nevrácení se službou přizpůsobené službě. Tím se zajistí nepřetržité vylepšování modelu Machine Learning a možnost přizpůsobování výběru nejlepší položky obsahu na základě kontextových informací, které obdrží.

> [!TIP]
> Obsah je libovolná jednotka informací, například text, obrázky, adresa URL, e-maily nebo cokoli jiného, co chcete vybrat a zobrazit pro uživatele.

Než začnete, můžete si vyzkoušet [přizpůsobování pomocí této interaktivní ukázky](https://personalizationdemo.azurewebsites.net/).

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Jak přizpůsobuje výběr nejlepší položky obsahu?

Přizpůsobování využívá **posílení učení** k výběru nejlepší položky (_Akce_) na základě kolektivního chování a odměňování výsledků napříč všemi uživateli. Akce jsou položky obsahu, jako jsou například články s novinkami, konkrétní filmy nebo produkty.

Volání **pořadí** přijímá položku akce, společně s funkcemi akce a funkce kontextu pro výběr nejvyšší položky akce:

* **Akce s funkcemi** – položky obsahu s funkcemi specifickými pro jednotlivé položky
* **Kontextové funkce** – funkce uživatelů, jejich kontext nebo prostředí při použití vaší aplikace

Volání pořadí vrátí ID, které položky obsahu, __Akce__, která se má uživateli zobrazit, v poli **ID akce odměna** .

__Akce__ zobrazená uživateli se volí pomocí modelů strojového učení, které se snaží maximalizovat celkovou částku v průběhu času.

### <a name="sample-scenarios"></a>Ukázkové scénáře

Pojďme se podívat na několik scénářů, ve kterých se dá použít k výběru nejlepšího obsahu, který se má pro uživatele vykreslit.

|Typ obsahu|Akce (s funkcemi)|Kontextové funkce|Vráceno ID akce odměna<br>(Zobrazit tento obsah)|
|--|--|--|--|
|Seznam zpráv|a. `The president...` (National, politika, [text])<br>b. `Premier League ...` (globální, sportovní, [text, obrázek, video])<br> c. `Hurricane in the ...` (oblastní, počasí, [text, obrázek]|Zprávy ze zařízení se čtou z<br>Měsíc nebo období<br>|určitého `The president...`|
|Seznam filmů|1. `Star Wars` (1977, [akce, Adventure, virtuální aplikace], Jiří Lucas)<br>2. `Hoop Dreams` (1994, [dokumentace, Sport], Steve James<br>3. `Casablanca` (1942, [románské drama, války], Michael Curtiz)|Video ze zařízení se bude sledovat<br>Velikost obrazovky<br>Typ uživatele<br>|1. `Casablanca`|
|Seznam produktů|i. `Product A` (3 kg, $ $ $ $, doručování za 24 hodin)<br>ii. `Product B` (20 kg, $ $, 2 týdny expedice s celním clem)<br>iii. `Product C` (3 kg, $ $ $, doručování za 48 hodin)|Nákupy zařízení se čtou z<br>Úroveň útraty uživatele<br>Měsíc nebo období|ii. `Product B`|

Přizpůsobené přizpůsobování, pomocí kterého se dá vybrat jedna nejlepší akce, se označuje jako _ID akce_ oddálení. Model Machine Learning používá: 

* Vyškolený model – informace dříve přijaté ze služby přizpůsobení používané ke zlepšení modelu Machine Learning
* Aktuální akce specifické pro data s funkcemi a funkcemi kontextu

## <a name="when-to-use-personalizer"></a>Kdy použít přizpůsobeného přizpůsobování

[Rozhraní API](https://go.microsoft.com/fwlink/?linkid=2092082) pro **řazení** přizpůsobeného objektu se volá pokaždé, když vaše aplikace prezentuje obsah. To se označuje jako **událost** zaznamenaná s _ID události_.

[Rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) pro **vlastní** nastavení se dá volat v reálném čase nebo opožděně, aby lépe vyhovovalo vaší infrastruktuře. Můžete určit skóre odměňování podle vašich obchodních potřeb. Skóre odměňování je mezi 0 a 1. To může být jedna hodnota, například 1 pro dobrý, a 0 pro chybné nebo číslo vytvořené algoritmem, který vytvoříte s ohledem na obchodní cíle a metriky.

## <a name="content-requirements"></a>Požadavky na obsah

Použít přizpůsobeného přizpůsobování obsahu:

* Pro výběr z má omezená sada položek (max. ~ 50). Pokud máte větší seznam, [použijte modul doporučení](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) , který zmenší seznam na 50 položek.
* Obsahuje informace popisující obsah, který chcete seřadit: _akce s funkcemi_ a _funkcemi kontextu_.
* Má minimálně ~ 1 tisíc události související s obsahem pro přizpůsobení, aby byly účinné. Pokud přizpůsobený modul neobdrží potřebný minimální provoz, služba trvá déle a určí jednu nejlepší položku obsahu.

Vzhledem k tomu, že přidaný modul používá k vrácení jediné nejlepší položky obsahu informace v reálném čase téměř v reálném čase, služba nebude:
* Zachovat a spravovat informace o profilu uživatele
* Protokolovat Předvolby nebo historii jednotlivých uživatelů
* Vyžadovat vyčištěný a označený obsah

## <a name="how-to-design-for-and-implement-personalizer"></a>Postup pro návrh a implementaci přizpůsobeného přizpůsobování

1. [Navrhněte](concepts-features.md) a Naplánujte obsah, **_Akce_** a **_kontext_**. Určete algoritmus odměna pro skóre **_odměňování_** .
1. Každý [prostředek](how-to-settings.md) přizpůsobeného přizpůsobeného prostředku se považuje za jednu výukovou smyčku. Smyčka získá jak pořadí, tak i možnosti pro daný obsah nebo uživatelské prostředí.

    |Typ prostředku| Účel|
    |--|--|
    |[Začátečnický režim](concept-apprentice-mode.md) `E0`|Využijte model přizpůsobeného přizpůsobování, aniž by to ovlivnilo vaši stávající aplikaci, a pak nasaďte do online výukového procesu do provozního prostředí|
    |Standardní `S0`|Chování online učení v produkčním prostředí|
    |Dost `F0`| Vyzkoušejte online chování učení v neprodukčním prostředí|

1. Přidejte do aplikace, webu nebo systému individuální nastavení:
    1. Přidejte volání **pořadí** do přizpůsobené aplikace, webu nebo systému a určete nejlepší, jednotlivou položku _obsahu_ před zobrazením obsahu uživateli.
    1. Zobrazí nejlepší, jednotlivou položku _obsahu_ , což je vráceno _ID akce odměna_ pro uživatele.
    1. Použití _obchodní logiky_ na shromážděné informace o tom, jak se uživatel chová, aby bylo možné určit skóre **odměňování** , například:

    |Chování|Vypočtené skóre odměny|
    |--|--|
    |Uživatel zvolil nejlepší, jednu položku _obsahu_ (ID akce pro odměnu).|**1**|
    |Uživatel vybral jiný obsah.|**0**|
    |Uživatel byl pozastaven, je před tím nerozhodující, aby bylo možné vybrat nejlepší, jednu položku _obsahu_ (ID akce odměna).|**0,5**|

    1. Přidejte volání **odměňování** , které posílá skóre odměňování od 0 do 1.
        * Hned po zobrazení obsahu
        * Nebo někdy později v offline systému
    1. [Vyhodnoťte smyčku](concepts-offline-evaluation.md) pomocí offline zkušebního období po určité době použití. Testování v režimu offline umožňuje testovat a hodnotit efektivitu služby přizpůsobeného službou bez změny kódu nebo ovlivnění uživatelského prostředí.

## <a name="complete-a-quickstart"></a>Dokončení rychlého startu

Nabízíme rychlé zprovoznění v jazycích C#, JavaScript a Python. Každý rychlý Start je navržený tak, aby vám poučení základních vzorů návrhu a abyste spustili kód za méně než 10 minut. 

* [Rychlý Start: jak používat klientskou knihovnu pro přizpůsobování](./quickstart-personalizer-sdk.md)

Až budete mít možnost začít se službou pro přizpůsobování, vyzkoušejte si naše kurzy a Naučte se, jak používat přizpůsobení ve webových aplikacích, chat roboty nebo poznámkovém bloku Azure.

* [Kurz: použití přizpůsobení ve webové aplikaci .NET](tutorial-use-personalizer-web-app.md)
* [Kurz: použití přizpůsobeného prostředí v robotu chatu pro .NET](tutorial-use-personalizer-chat-bot.md)
* [Kurz: použití individuálního nastavení v poznámkovém bloku Azure](tutorial-use-azure-notebook-generate-loop-data.md)

## <a name="reference"></a>Referenční informace 

* [Sada/.NET SDK pro přizpůsobení C#](/dotnet/api/overview/azure/cognitiveservices/client/personalizer?view=azure-dotnet)
* [Sada SDK pro přizpůsobování](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview)
* [Sada přizpůsobování JavaScript SDK](/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)
* [Sada SDK pro přizpůsobování Pythonu](/python/api/overview/azure/cognitiveservices/personalizer?view=azure-python)
* [Rozhraní REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak přizpůsobovat práci](how-personalizer-works.md) 
>  [Co je posílení učení?](concepts-reinforcement-learning.md)