---
title: Zlepšení klasifikátoru – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak množství, kvalita a různá data mohou zlepšit kvalitu třídění ve službě Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: a77d3d5c1225fdd85e27db20cdae23e0c77a5e28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91271354"
---
# <a name="how-to-improve-your-classifier"></a>Jak vylepšit třídění

V této příručce se dozvíte, jak vylepšit kvalitu Custom Vision Service třídění. Kvalita vašeho klasifikátoru závisí na množství, kvalitě a nejrůznějších datech, která zadáte, a na tom, jak vyrovnávat celou datovou sadu. Dobrý klasifikátor má datovou sadu s vyrovnáváním školení, která je zavedená o to, co bude do klasifikátoru odesláno. Proces sestavování takového klasifikátoru je iterativní; pro dosažení očekávaných výsledků je běžné, že vybereme několik zaokrouhlení školení.

Následuje obecný vzor, který vám pomůže sestavovat přesnější klasifikátor:

1. První a kulaté školení
1. Přidat další obrázky a vyrovnávat data; Přeučování
1. Přidat obrázky s proměnlivým pozadím, osvětlením, velikostí objektu, úhlem kamery a stylem; Přeučování
1. Použít nové image k testování předpovědi
1. Úprava stávajících školicích dat podle výsledků předpovědi

## <a name="prevent-overfitting"></a>Zabránit přebudování

V některých případech se klasifikátor naučí, aby předpovědi na základě libovolných vlastností, které mají vaše image společné. Například pokud vytváříte klasifikátor pro jablka vs. Citrus a jste použili obrázky jablek v rukou a v citrusech na bílých plátech, třídění může mít nepatřičný význam pro ruce vs. pláty, nikoli pro jablka a citrusy.

![Obrázek neočekávané klasifikace](./media/getting-started-improving-your-classifier/unexpected.png)

Chcete-li tento problém vyřešit, použijte následující pokyny pro školení s více různými různými obrázky: Zadejte obrázky s různými úhly, pozadí, velikostí objektů, skupinami a dalšími variacemi.

## <a name="data-quantity"></a>Množství dat

Počet školicích imagí je nejdůležitějším faktorem. Jako výchozí bod doporučujeme použít minimálně 50 imagí na popisek. S menším počtem imagí je lepším rizikem přebudování a zatímco vaše čísla výkonu mohou nabídnout dobrou kvalitu, váš model se může bojovat s daty ze skutečného světa. 

## <a name="data-balance"></a>Zůstatek dat

Je také důležité vzít v úvahu relativní množství vašich školicích dat. Například použití imagí 500 pro jednu jmenovku a 50 imagí pro jiný popisek vytvoří datovou sadu pro obdobu nevyváženého školení. Výsledkem bude, že model bude přesnější při předpovědi jednoho popisku než jiného. Pravděpodobně uvidíte lepší výsledky, pokud udržujete alespoň poměr 1:2 mezi popiskem a s nejmenším počtem obrázků a popiskem s největší imagí. Například pokud má popisek s největší imagí 500 obrázků, musí mít popisek s minimálními obrázky alespoň 250 obrázků pro školení.

## <a name="data-variety"></a>Odrůda dat

Nezapomeňte použít obrázky, které jsou v rámci normálního použití odesílány do třídění. V opačném případě by se vám klasifikátor mohl naučit, aby předpovědi na základě libovolných vlastností, které mají vaše image společné. Například pokud vytváříte klasifikátor pro jablka vs. Citrus a jste použili obrázky jablek v rukou a v citrusech na bílých plátech, třídění může mít nepatřičný význam pro ruce vs. pláty, nikoli pro jablka a citrusy.

![Obrázek neočekávané klasifikace](./media/getting-started-improving-your-classifier/unexpected.png)

Chcete-li tento problém vyřešit, zahrňte celou řadu imagí, abyste měli jistotu, že se třídění může zobecnit správně. Tady je několik způsobů, jak můžete nastavit, aby se vaše školicí sada lépe rozvedla:

* __Pozadí:__ Poskytněte obrázky objektu před různými pozadími. Fotografie v přirozených kontextech jsou lepší než fotky před neutrálními pozadí, protože poskytují více informací pro třídění.

    ![Obrázek ukázek na pozadí](./media/getting-started-improving-your-classifier/background.png)

* __Osvětlení:__ Poskytněte image se proměnlivým osvětlením (to znamená při použití Flash, vysokou expozici atd.), zejména v případě, že obrázky používané pro předpověď mají jiný osvětlení. Je také užitečné použít obrázky s proměnlivým sytostem, odstínem a jasem.

    ![Obrázek ukázek světel](./media/getting-started-improving-your-classifier/lighting.png)

* __Velikost objektu:__ Poskytněte obrázky, ve kterých se objekty liší velikost a číslo (například fotografie ze svazků banánů a closeup jednoho banánu). Různé velikosti pomáhají klasifikátoru zobecnit lépe.

    ![Obrázek s ukázkami velikosti](./media/getting-started-improving-your-classifier/size.png)

* __Úhel kamery:__ Poskytněte obrázky, které se vyberou v různých úhlech kamery. Případně, pokud se všechny vaše fotky musí pokládat s pevnými fotoaparáty (jako jsou například kamery pro sledování), nezapomeňte přiřadit jiný popisek každému často vyskytujícímu objektu, abyste se vyhnuli přeložení &mdash; nesouvisejících objektů (například lampposts) jako klíčové funkce.

    ![Obrázek ukázek úhlů](./media/getting-started-improving-your-classifier/angle.png)

* __Styl:__ Poskytněte obrázky různých stylů stejné třídy (například různé odrůdy stejného ovoce). Pokud však máte objekty drasticky odlišných stylů (například myš Mickey a myš pro reálný výkon), doporučujeme je označit jako samostatné třídy, aby lépe představovaly jejich samostatné funkce.

    ![Obrázek ukázek stylu](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Negativní obrázky

V určitém okamžiku projektu může být nutné přidat _negativní vzorky_ , aby bylo možné přesnější třídění. Záporné vzorky jsou ty, které neodpovídají žádné z ostatních značek. Při nahrávání těchto imagí použijte pro ně speciální **negativní** popisek.

> [!NOTE]
> Custom Vision Service podporuje některé automatické negativní zpracování obrazu. Pokud například vytváříte klasifikátor v hroznovém moštu vs. a odešlete obrázek botu k předpovědi, měl by klasifikátor určit, že obrázek je blízko 0% pro hrozny i banány.
> 
> Na druhé straně, v případech, kdy jsou negativní obrázky jenom variací imagí používaných při výuce, je pravděpodobný, že model klasifikuje negativní image jako třídu s popiskem z důvodu skvělých podobností. Pokud například máte klasifikátor a třídění grapefruitu a zadáváte ho v obrazci Clementine, může být Clementine jako oranžová, protože mnoho funkcí Clementine se podobá těm pomerančům. Pokud jsou tyto negativní image z této povahy, doporučujeme vytvořit jednu nebo více dalších značek (například **jiné**) a označit negativní obrázky pomocí této značky během školení, aby model lépe rozlišil mezi těmito třídami.

## <a name="use-prediction-images-for-further-training"></a>Použití předpovědí k dalšímu školení

Když použijete nebo otestujete třídění imagí odesláním imagí do koncového bodu předpovědi, služba Custom Vision tyto image uloží. Pak je můžete použít ke zlepšení modelu.

1. Chcete-li zobrazit obrázky odeslané do klasifikátoru, otevřete [Custom Vision webové stránky](https://customvision.ai), přejít do projektu a vyberte kartu __předpovědi__ . Výchozí zobrazení zobrazuje obrázky z aktuální iterace. Pomocí rozevírací nabídky __iterace__ můžete zobrazit obrázky odeslané během předchozích iterací.

    ![snímek obrazovky s kartou předpovědi se zobrazením obrázků](./media/getting-started-improving-your-classifier/predictions.png)

2. Najeďte ukazatelem myši na obrázek a zobrazí se značky, které třídění vypoví. Obrázky jsou seřazené tak, aby se v horní části zobrazovaly ty, které můžou do klasifikátoru přinášet nejvíc vylepšení. Chcete-li použít jinou metodu řazení, proveďte výběr v oddílu __řazení__ . 

    Chcete-li přidat obrázek do stávajících školicích dat, vyberte obrázek, nastavte správné značky a klikněte na tlačítko __Uložit a zavřít__. Bitová kopie bude odebrána z __předpovědi__ a přidána do sady školicích imagí. Můžete ji zobrazit výběrem karty __školicích imagí__ .

    ![Obrázek stránky označování](./media/getting-started-improving-your-classifier/tag.png)

3. Pak použijte tlačítko __výuka__ k přeučení třídění.

## <a name="visually-inspect-predictions"></a>Vizuálně kontrolovat předpovědi

Pokud chcete zkontrolovat předpovědi image, přejděte na kartu __školicích imagí__ , v rozevírací nabídce **iterace** vyberte předchozí iterace cvičení a v části **značky** zaškrtněte jednu nebo více značek. Zobrazení by nyní mělo zobrazit červené pole kolem každého obrázku, pro který model nedokázal správně odhadnout danou značku.

![Obrázek historie iterace](./media/getting-started-improving-your-classifier/iteration.png)

V některých případech vizuální kontrola může identifikovat vzory, které pak můžete opravit přidáním dalších školicích dat nebo úpravou existujících školicích dat. Například třídění pro jablka vs. vápna může nesprávně označovat všechny zelené jablka jako vápna. Tento problém pak můžete vyřešit přidáním a poskytnutím školicích dat, která obsahují tagované obrázky zelených jablek.

## <a name="next-steps"></a>Další kroky

V této příručce jste se seznámili s několika technikami, které vám pomohou zajistit přesnější model klasifikace vlastních imagí. Dále se naučíte, jak testovat image programově jejich odesláním do prediktivního rozhraní API.

> [!div class="nextstepaction"]
> [Použití prediktivního rozhraní API](use-prediction-api.md)
