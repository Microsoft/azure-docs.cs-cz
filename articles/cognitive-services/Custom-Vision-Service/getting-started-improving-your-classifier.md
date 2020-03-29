---
title: Vylepšete svůj klasifikátor - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak množství, kvalita a rozmanitost dat může zlepšit kvalitu vašeho třídění ve službě Vlastní vize.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: c2858d5f9bca662cbbcd48b2345a7dc2c7ae48b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718548"
---
# <a name="how-to-improve-your-classifier"></a>Jak zlepšit klasifikátor

V této příručce se dozvíte, jak zlepšit kvalitu vašeho klasifikátoru služby Custom Vision Service. Kvalita klasifikátoru závisí na množství, kvalitě a rozmanitosti označených dat, která zadáte, a na vyváženosti celkové datové sady. Dobrý třídění má vyváženou trénovací datovou sadu, která je reprezentativní pro co bude odeslána třídění. Proces budování takového třídění je iterativní; je běžné absolvovat několik kol tréninku, abyste dosáhli očekávaných výsledků.

Následuje obecný vzor, který vám pomůže vytvořit přesnější třídění:

1. Trénink v prvním kole
1. Přidejte další obrázky a vyvažte data; Rekvalifikaci
1. Přidejte obrázky s různým pozadím, osvětlením, velikostí objektu, úhlem kamery a stylem; Rekvalifikaci
1. Použití nových obrázků k testování předpovědi
1. Úprava existujících trénovacích dat podle výsledků předpovědi

## <a name="prevent-overfitting"></a>Zabránit nadměrnému vybavení

Někdy třídění se naučí, aby předpovědi založené na libovolné charakteristiky, které vaše obrázky mají společné. Pokud například vytváříte klasifikátor pro jablka vs. citrusy a použili jste obrázky jablek v rukou a citrusů na bílých deskách, může klasifikátor dát nepatřičný význam rukou vs. talíře, spíše než jablkům vs. citrusům.

![Obrázek neočekávané klasifikace](./media/getting-started-improving-your-classifier/unexpected.png)

Chcete-li tento problém vyřešit, použijte následující pokyny pro školení s rozmanitějšími obrázky: poskytněte obrázky s různými úhly, pozadí, velikost objektu, skupiny a další varianty.

## <a name="data-quantity"></a>Množství dat

Počet tréninkových obrázků je nejdůležitějším faktorem. Jako výchozí bod doporučujeme použít alespoň 50 obrázků na štítek. S menším počtem obrázků existuje vyšší riziko nadměrného vybavení, a zatímco vaše čísla výkonu mohou naznačovat dobrou kvalitu, váš model může mít potíže s reálnými daty. 

## <a name="data-balance"></a>Bilance dat

Je také důležité vzít v úvahu relativní množství dat školení. Například použití 500 bitových kopií pro jeden popisek a 50 obrázků pro jiný popisek umožňuje nevyváženou trénovací datovou sadu. To způsobí, že model bude přesnější při předpovídání jeden popisek než jiný. Je pravděpodobné, že se vám zobrazí lepší výsledky, pokud zachováte alespoň poměr 1:2 mezi popiskem s nejmenším počtem obrázků a popiskem s největším počtem obrazů. Pokud má například popisek s největším početem obrázků 500 obrázků, popisek s nejmenším početem obrázků by měl mít alespoň 250 obrázků pro školení.

## <a name="data-variety"></a>Odrůda dat

Ujistěte se, že používáte obrázky, které jsou reprezentativní pro to, co bude odesláno třídění při běžném používání. V opačném případě se klasifikátor může naučit provádět předpovědi založené na libovolných charakteristikách, které mají vaše obrázky společné. Pokud například vytváříte klasifikátor pro jablka vs. citrusy a použili jste obrázky jablek v rukou a citrusů na bílých deskách, může klasifikátor dát nepatřičný význam rukou vs. talíře, spíše než jablkům vs. citrusům.

![Obrázek neočekávané klasifikace](./media/getting-started-improving-your-classifier/unexpected.png)

Chcete-li tento problém vyřešit, zahrňte různé obrázky, abyste zajistili, že třídění může dobře generalizovat. Níže jsou uvedeny některé způsoby, jak můžete svůj tréninkový set rozmanitější:

* __Souvislosti:__ Poskytněte obrázky objektu před různými pozadími. Fotografie v přirozeném kontextu jsou lepší než fotografie před neutrálním pozadím, protože poskytují více informací pro třídění.

    ![Obrázek vzorků pozadí](./media/getting-started-improving-your-classifier/background.png)

* __Osvětlení:__ Poskytněte snímky s různým osvětlením (tj. pořízeným bleskem, vysokou expozicí a tak dále), zejména pokud mají snímky použité pro předpověď jiné osvětlení. Je také užitečné použít obrázky s různou sytostí, odstínem a jasem.

    ![Obrázek vzorků osvětlení](./media/getting-started-improving-your-classifier/lighting.png)

* __Velikost objektu:__ Poskytněte obrázky, ve kterých se objekty liší velikostí a počtem (například fotografie svazků banánů a detailní snímek jednoho banánu). Různé velikosti pomáhá klasifikátoru generalizovat lépe.

    ![Obrázek vzorků velikosti](./media/getting-started-improving-your-classifier/size.png)

* __Úhel kamery:__ Poskytněte snímky pořízené s různými úhly kamery. Případně, pokud musí být všechny vaše fotografie pořízeny pomocí pevných kamer (například sledovacích kamer), nezapomeňte každému&mdash;pravidelně se vyskytujícímu objektu přiřadit jiný štítek, aby se zabránilo nadměrnému přizpůsobení interpretace nesouvisejících objektů (například sloupů veřejného osvětlení) jako klíčové funkce.

    ![Obrázek úhlových vzorků](./media/getting-started-improving-your-classifier/angle.png)

* __Styl:__ Poskytněte obrázky různých stylů stejné třídy (například různé odrůdy stejného ovoce). Pokud však máte objekty drasticky odlišných stylů (například Mickey Mouse vs. reálná myš), doporučujeme je označit jako samostatné třídy, abyste lépe reprezentovali jejich odlišné funkce.

    ![Obrázek vzorků stylů](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Negativní obrázky

V určitém okamžiku projektu může být nutné přidat _negativní vzorky,_ které vám pomohou zpřesnit klasifikátor. Negativní vzorky jsou ty, které neodpovídají žádné jiné značky. Když tyto obrázky nahrajete, použijte na ně speciální **negativní** štítek.

> [!NOTE]
> Služba Custom Vision Service podporuje některé automatické negativní zpracování obrazu. Například pokud vytváříte hroznové vs banán třídění a odeslat obrázek boty pro předpověď, třídění by měl skóre, že obraz jako blízko 0% pro hrozny a banán.
> 
> Na druhou stranu, v případech, kdy negativní obrazy jsou jen variace obrazů používaných v tréninku, je pravděpodobné, že model bude klasifikovat negativní obrazy jako označené třídy vzhledem k velké podobnosti. Například, pokud máte orange vs grapefruit klasifikátor, a krmíte v obraze klementinky, může skóre clementine jako pomeranč, protože mnoho rysů klementinky se podobají těm pomerančů. Pokud jsou vaše negativní obrázky tohoto druhu, doporučujeme vytvořit jednu nebo více dalších značek (například **Ostatní)** a označit negativní obrázky touto značkou během školení, aby model lépe rozlišoval mezi těmito třídami.

## <a name="use-prediction-images-for-further-training"></a>Použití predikčních obrazů pro další školení

Při použití nebo testování klasifikátoru bitových kopií odesláním bitové kopie do koncového bodu předpověď, vlastní vize služba ukládá tyto bitové kopie. Potom je můžete použít ke zlepšení modelu.

1. Chcete-li zobrazit obrázky odeslané klasifikátoru, otevřete [webovou stránku Vlastní vize](https://customvision.ai), přejděte do projektu a vyberte kartu __Předpovědi.__ Výchozí zobrazení zobrazuje obrázky z aktuální iterace. Rozevírací nabídku __Iterace__ můžete použít k zobrazení obrázků odeslaných během předchozích iterací.

    ![snímek obrazovky s kartou předpovědi s obrázky v zobrazení](./media/getting-started-improving-your-classifier/predictions.png)

2. Najeďte nad obrázek zobrazíte značky, které byly předpovězeny třídění. Obrázky jsou seřazeny tak, aby ty, které mohou přinést nejvíce vylepšení třídění jsou uvedeny v horní části. Chcete-li použít jinou metodu řazení, proveďte výběr v části __Seřadit.__ 

    Pokud chcete přidat obrázek do existujících trénovacích dat, vyberte ho, nastavte správnou značku a klikněte na __Uložit a zavřít__. Obrázek bude odebrán z __předpovědi__ a přidán do sady tréninkových obrázků. Můžete ji zobrazit výběrem karty __Trénující obrázky.__

    ![Obrázek stránky tagování](./media/getting-started-improving-your-classifier/tag.png)

3. Potom pomocí tlačítka __Train__ přeškolte klasifikátor.

## <a name="visually-inspect-predictions"></a>Vizuální kontrola předpovědí

Chcete-li zkontrolovat předpovědi obrázků, přejděte na kartu __Trénování obrázků,__ vyberte předchozí iteraci školení v rozevírací nabídce **Iterace** a zkontrolujte jednu nebo více značek v části **Tagy.** Zobrazení by nyní mělo zobrazit červené pole kolem každého z obrázků, pro které model nedokázal správně předpovědět danou značku.

![Obrázek historie iterace](./media/getting-started-improving-your-classifier/iteration.png)

Vizuální kontrola může někdy identifikovat vzory, které pak můžete opravit přidáním dalších trénovacích dat nebo úpravou existujících trénovacích dat. Například třídění pro jablka vs. limes může nesprávně označit všechna zelená jablka jako limetky. Tento problém pak můžete vyřešit přidáním a poskytnutím trénovacích dat, která obsahují označené obrázky zelených jablek.

## <a name="next-steps"></a>Další kroky

V této příručce jste se naučili několik technik, aby byl váš vlastní model klasifikace obrázků přesnější. Dále se dozvíte, jak testovat obrázky programově jejich odesláním do rozhraní PREdikce ROZHRANÍ API.

> [!div class="nextstepaction"]
> [Použití prediktivního rozhraní API](use-prediction-api.md)
