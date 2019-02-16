---
title: Vylepšení klasifikátoru – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak zlepšit kvalitu klasifikátoru.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.openlocfilehash: 54faf8b37afa953aac7d411df2f539188dc5b451
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310208"
---
# <a name="how-to-improve-your-classifier"></a>K vylepšení klasifikátoru

V této příručce se dozvíte, jak vylepšit kvalitu klasifikátoru Custom Vision Service. Kvalita klasifikátoru závisí na množství, kvality a řadu s popisky dat, které zadáte, a jak vyváženého celkovou datová sada je. Dobré třídění obsahuje vyvážené trénovací datovou sadu, která dobře vystihuje co bude odeslán do třídění. Proces vytváření těchto třídění je iterativní; je běžné trvat pár zaokrouhlí školení k dosažení očekávané výsledky.

Toto je obecný vzor, které vám pomůžou vytvářet přesnější třídění:

1. První kruhové školení
1. Přidat další Image a Vyrovnat data; přeučování
1. Přidání obrázků s různou na pozadí, osvětlení, velikost objektu, úhel kamery a styl; přeučování
1. Použít nové bitové kopie k otestování predikcí
1. Upravit existující trénovací data podle výsledků předpovědí

## <a name="overfitting"></a>Overfitting

V některých případech bude klasifikátor zjistěte, jak vytvářet předpovědi na základě libovolného charakteristik, které váš obrázky mají společnou. Například pokud vytváříte třídění pro jablka vs. citrusových a jste použili Image jablka do rukou a citrus na bílý talíře, třídění může udělit zbytečného význam do rukou vs. talíře, nikoli jablka vs. citrusových.

![Obrázek neočekávané klasifikace](./media/getting-started-improving-your-classifier/unexpected.png)

Chcete-li tento problém vyřešit, použijte následující pokyny pro vzdělávání s větším různorodé Image: Zadejte Image pomocí různých úhlů, pozadí, velikost objektu, skupiny a další varianty konfigurací.

## <a name="data-quantity"></a>Množství dat

Počet trénovacích obrázků je nejdůležitějším faktorem. Jsme recomment pomocí alespoň 50 obrázků na popisek jako výchozí bod. S menším počtem obrázky je vyšší riziko overfitting a zatímco vaše čísla výkonu může navrhnout kvalitní, může váš model zápolí s dat z reálného světa. 

## <a name="data-balance"></a>Data zůstatek

Je také důležité vzít v úvahu relativní množství trénovací data. Například použití 500 imagí pro jeden popisek a 50 bitových kopií pro jiné návěstí je pro datovou sadu imbalanced školení. To způsobí, že model bude předpovídat jeden popisek než jiný přesnější. Budete pravděpodobně uvidíte lepší výsledky, pokud chcete zachovat nejméně s 1:2 poměr mezi popisek s nejmenším počtem bitových kopií a popisek se většina imagí. Například pokud má popisek s většina imagí 500 imagí, popisek s nejnižší Image by měl mít alespoň 250 obrázků k trénování.

## <a name="data-variety"></a>Celé řady dat

Nezapomeňte použít Image, které jsou zástupce co bude odeslán do třídění během normálního používání. V opačném případě může klasifikátoru zjistěte, jak vytvářet předpovědi na základě libovolného charakteristik, které váš obrázky mají společnou. Například pokud vytváříte třídění pro jablka vs. citrusových a jste použili Image jablka do rukou a citrus na bílý talíře, třídění může udělit zbytečného význam do rukou vs. talíře, nikoli jablka vs. citrusových.

![Obrázek neočekávané klasifikace](./media/getting-started-improving-your-classifier/unexpected.png)

Chcete-li tento problém, zahrnují celou řadu imagí k zajištění, že můžete také zobecnit klasifikátoru. Tady jsou některé způsoby, které provedete trénování nastavené různorodější:

* __Na pozadí:__ Zadejte image objektu před různá pozadí. Fotografie v kontextech přirozené jsou lepší než fotografie před neutrální pozadími, jako poskytují další informace pro třídění.

    ![Obrázek pozadí ukázky](./media/getting-started-improving-your-classifier/background.png)

* __Osvětlení:__ Bitové kopie poskytnout různé osvětlení (, která se provede s flash a vysoce vystavení) a tak dále, zejména v případě imagí použitých pro předpověď mají různé osvětlení. Je také užitečné, abyste mohli používat Image s různou sytost, hue a jas.

    ![Obrázek ukázky osvětlení](./media/getting-started-improving-your-classifier/lighting.png)

* __Velikost objektu:__ Zadejte bitové kopie, které objekty se liší v velikosti a počtu (například fotku svazcích bananas a Detail jeden banánu). Různé velikosti pomáhá třídění generalize lépe.

    ![Obrázek ukázky velikost](./media/getting-started-improving-your-classifier/size.png)

* __Úhel kamery:__ Zadejte Image pořízené různých kamer. Případně, pokud všechny fotografií musí být přijata s pevnou kamery (například bezpečnostních kamer), je nutné přiřadit jiný popisek pro každý objekt pravidelně dochází, aby se zabránilo overfitting&mdash;interpretace nesouvisející objekty (například lampposts) jako klíčovou funkcí.

    ![Obrázek ukázky úhel](./media/getting-started-improving-your-classifier/angle.png)

* __Styl:__ Zadejte bitové kopie různé styly stejné třídy (například různé typy prvků stejného výsledku). Pokud máte objektů výrazně různých stylů (například Mickey myši vs. reálné myši), doporučujeme však, že je označit jako samostatné třídy pro lepší reprezentaci jejich různé prvky.

    ![Obrázek ukázky styl](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-prediction-images-for-further-training"></a>Použití imagí předpovědi pro další školení

Při použití nebo testování třídění image odešlete Image do koncového bodu predikcí, ukládá Custom Vision service těchto imagí. Pak je můžete použít se model vylepšit.

1. Chcete-li zobrazit Image odeslat do třídění, otevřete [vizi vlastní webovou stránku](https://customvision.ai), přejděte na svůj projekt a vyberte __předpovědi__ kartu. Výchozí zobrazení zobrazuje obrázky z aktuální iteraci. Můžete použít __iterace__ rozevírací nabídce pro zobrazení obrázků odeslaných během předchozími iteracemi.

    ![snímek obrazovky s předpovědí kartě s obrázky v zobrazení](./media/getting-started-improving-your-classifier/predictions.png)

2. Najeďte myší na obrázek zobrazíte značky, které byly předpovídané pomocí třídění. Bitové kopie jsou seřazeny tak, aby ty, které můžete použít maximum vylepšení klasifikátoru jsou uvedené nahoře. Pokud chcete použít jinou metodu řazení, proveďte výběr v __řazení__ oddílu. 

    Přidání obrázku na existující trénovací data, vyberte bitovou kopii, nastavit správný počet značek: a klikněte na tlačítko __uložte a zavřete__. Na obrázku se odebere z __předpovědi__ a přidat do sady trénovacích obrázků. Můžete ho zobrazit výběrem __Trénovacích obrázků__ kartu.

    ![Obrázek označení stránky](./media/getting-started-improving-your-classifier/tag.png)

3. Potom použijte __trénování__ tlačítko přeučování třídění.

## <a name="visually-inspect-predictions"></a>Vizuálně zkoumat predikcí

Ke kontrole predikcí image, přejděte na __Trénovacích obrázků__ kartu, vyberte váš předchozí iteraci školení v **iterace** rozevírací nabídky a zaškrtněte políčko v rámci jedné nebo více značek **značky** oddílu. Zobrazení nyní zobrazeno červeným rámečkem okolo položky všech imagí, pro které modelu se nepodařilo správně předpovědět danou značku.

![Snímek historie iterace](./media/getting-started-improving-your-classifier/iteration.png)

Někdy vizuální kontrolu můžete rozpoznat vzorce, které potom můžete opravit přidáním další trénovacích dat nebo úpravou existující trénovací data. Například třídění pro jablka vs. vápna mohou nesprávně popisek všechny zelené jablka jako vápna. Potom můžete tento problém vyřešit přidáním a poskytuje trénovací data, která obsahuje označené obrázky zelené jablka.

## <a name="next-steps"></a>Další postup

V této příručce jste zjistili, řadu technik k zpřesnit model klasifikace vlastní image. Dále se naučíte k otestování Image prostřednictvím kódu programu, odešlete je do rozhraní API pro předpověď.

> [!div class="nextstepaction"]
[Použití prediktivního rozhraní API](use-prediction-api.md)
