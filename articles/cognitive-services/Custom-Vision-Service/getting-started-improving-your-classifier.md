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
ms.date: 07/05/2018
ms.author: pafarley
ms.openlocfilehash: ae8a96c07467a4c8617b8e7983e8dd7ad6e70be4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883453"
---
# <a name="how-to-improve-your-classifier"></a>K vylepšení klasifikátoru

Zjistěte, jak zlepšit kvalitu klasifikátoru Custom Vision Service. Kvalita klasifikátor je závislá na množství, kvality a řadu s popisky dat, které poskytnete a jak vyvážené tato datová sada je. Dobré třídění obvykle obsahuje vyvážené trénovací datovou sadu, která dobře vystihuje co bude odeslán do třídění. Často je iterativní proces vytváření těchto třídění. Je běžné trvat pár zaokrouhlí školení k dosažení očekávané výsledky.

Tady jsou běžné kroky k vylepšení klasifikátoru. Tyto kroky nejsou pevné a rychlé pravidla, ale heuristickými metodami, který vám pomůže vytvářet lepší třídění.

1. První kruhové školení
1. Přidat další Image a vyvážení dat
1. Přeučování
1. Přidání obrázků s různou na pozadí, osvětlení, velikost objektu, úhel kamery a stylu
1. Přeučování & kanálu obrázku k předpovědi
1. Zkontrolujte výsledky předpovědí
1. Upravit existující trénovacích dat

## <a name="data-quantity-and-data-balance"></a>Množství a data vyvážení dat

Nejdůležitějším principem je nahrát dostatek obrázků k trénování třídění. Jako výchozí bod doporučují se nejméně 50 obrázků na popisek pro trénovací sady. S menším počtem obrázky je silné riziko, že jste overfitting. Zatímco vaše čísla výkonu může navrhnout kvalitní, může potýkají s daty v reálném světě. Školení třídění s další Image se obecně zvýšíte přesnost tohoto predikované výsledky.

Je potřeba zamyslet se, měli byste zajistit, že jsou vaše data rovnoměrně. Například 500 bitových kopií pro jeden popisek a 50 bitových kopií pro jiné návěstí vytvoří imbalanced trénovací datové sady, způsobí modelu, který má být přesnější předpovídat jeden popisek než jiný. Budete pravděpodobně uvidíte lepší výsledky, pokud chcete zachovat nejméně s 1:2 poměr mezi popisek s nejmenším počtem bitových kopií a popisek se většina imagí. Například pokud má popisek s nejvyšší počet imagí 500 imagí, popisek s nejnižší imagí musí má alespoň 250 obrázků k trénování.

## <a name="train-more-diverse-images"></a>Trénování více různorodých obrázků

Zadejte bitové kopie, které jsou zástupce co bude odeslán do třídění během normálního používání. Například pokud se supervizí trénujete "apple" třídění, klasifikátoru nemusí být přesné, pokud pouze trénování fotografie jablka talířů ale zkontrolujte předpovědi na fotografie jablek na stromové struktury. Včetně širokou škálu imagí budete mít jistotu, že klasifikátoru není tendenční a můžete také zobecnit. Tady jsou některé způsoby, které provedete trénování nastavené různorodější:

__Na pozadí:__ Zadejte image objektu před různá pozadí (to znamená, ovoce na desce oproti ovoce v kontejneru a blízkým). Fotografie v kontextu jsou lepší než fotografie před neutrální pozadími, jako poskytují další informace pro třídění.

![Obrázek pozadí ukázky](./media/getting-started-improving-your-classifier/background.png)

__Osvětlení:__ Bitové kopie poskytnout různé osvětlení (to znamená, vytvořených ve vystavení flash, vysoká, atd.), zejména v případě imagí použitých pro předpověď mají různé osvětlení. Také je užitečné zahrnout i obrázky pomocí různých sytost, hue a jas.

![Obrázek ukázky osvětlení](./media/getting-started-improving-your-classifier/lighting.png)

__Velikost objektu:__ Zadejte bitové kopie, ve kterých jsou objekty pohyblivá podle velikosti zachytávání různé části objektu. Například fotku svazcích bananas a Detail jeden banánu. Různé velikosti pomáhá třídění generalize lépe.

![Obrázek ukázky velikost](./media/getting-started-improving-your-classifier/size.png)

__Úhel kamery:__ Zadejte Image pořízené různých kamer. Pokud vaše fotografie pocházejí sadu pevné kamery (například bezpečnostních kamer), nezapomeňte že přiřadit jiný popisek pro každou kameru i v případě, že dojde k zachycení stejné objekty, aby se zabránilo overfitting – modelování nesouvisející objekty (například lampposts) jako klíčovou funkcí.

![Obrázek ukázky úhel](./media/getting-started-improving-your-classifier/angle.png)

__Styl:__ Zadejte bitové kopie různé styly stejné třídy (to znamená, že různé druhy citrusových). Ale pokud máte Image objektů výrazně různé styly (to znamená, Mickey myš a reálných zkušeností rat), doporučujeme označit je jako samostatné třídy pro lepší reprezentaci jejich různé prvky.

![Obrázek ukázky styl](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-images-submitted-for-prediction"></a>Použití imagí odeslané pro předpověď

Custom Vision Service ukládá Image odeslat do koncového bodu předpovědi. Pokud chcete zlepšit třídění pomocí těchto bitových kopií, postupujte následovně:

1. Chcete-li zobrazit Image odeslat do třídění, otevřete [vizi vlastní webovou stránku](https://customvision.ai), přejděte na svůj projekt a vyberte __předpovědi__ kartu. Výchozí zobrazení zobrazuje obrázky z aktuální iteraci. Můžete použít __iterace__ rozevírací nabídka pole k zobrazení obrázků odeslaných během předchozími iteracemi.

    ![Obrázek karty predikcí](./media/getting-started-improving-your-classifier/predictions.png)

2. Najeďte myší na obrázek zobrazíte značky, které byly předpovídané pomocí třídění. Bitové kopie jsou seřazené tak, že jsou obrázky, které přinést většina zisky třídění v horní části. Chcete-li vybrat jinou řazení, použijte __řazení__ oddílu. Přidání obrázku na existující trénovací data, vyberte bitovou kopii, vyberte správné značku a klikněte na __uložte a zavřete__. Na obrázku se odebere z __předpovědi__ a přidán do trénovacích obrázků. Můžete ho zobrazit výběrem __Trénovacích obrázků__ kartu.

    ![Obrázek označení stránky](./media/getting-started-improving-your-classifier/tag.png)

3. Použití __trénování__ tlačítko přeučování třídění.

## <a name="visually-inspect-predictions"></a>Vizuálně zkoumat predikcí

Chcete-li prověřit predikcí bitové kopie, vyberte __Trénovacích obrázků__ kartu a potom vyberte __iterace historie__. Bitové kopie, které jsou uvedeny s červeným rámečkem byly správně předpovědět.

![Snímek historie iterace](./media/getting-started-improving-your-classifier/iteration.png)

Někdy vizuální kontrolu můžete rozpoznat vzorce, které potom můžete opravit přidáním dalších trénovacích dat nebo úpravou existující trénovací data. Například třídění pro apple vs. Limetkově mohou nesprávně označit všechny zelené jablka jako vápna. Je možné, že problém vyřešíte přidáním a poskytuje trénovací data, která obsahuje označené obrázky zelené jablka.

## <a name="unexpected-classification"></a>Neočekávané klasifikace

Třídění někdy nesprávně zjišťuje vlastnosti, které váš obrázky mají společnou. Například pokud vytváříte třídění pro jablka vs. citrusových a jsou zadané Image jablka do rukou a citrus v bílé tabulky, může pro rukou vs. bílé talířů místo jablka vs. citrusových trénování třídění.

![Obrázek neočekávané klasifikace](./media/getting-started-improving-your-classifier/unexpected.png)

Tento problém vyřešit, použijte výše uvedené pokyny pro vzdělávání s větším různorodé Image: Zadejte Image pomocí různých úhlů, pozadí, velikost objektu, skupiny a další varianty.

## <a name="negative-image-handling"></a>Zpracování negativní obrázku

Custom Vision Service podporuje zpracování některých automatické negativní obrázku. V případě, kde zodpovídají za tvorbu hroznového vs. banánů třídění a odešlete image bot pro predikci by měl třídění hroznového a banánů skóre této bitové kopie jako blízko 0 %.

Na druhé straně v případech, kdy negativní bitové kopie jsou jen varianta Image použité v školení, je pravděpodobné, že model bude klasifikovat negativní bitové kopie jako třída s popiskem obarvené skvělé. Například pokud máte oranžová vs. grapefruity třídění a kanálu v obraze clementine, může skóre clementine jako oranžová. K tomu může dojít, protože mnoho funkcí clementine (barva, tvar, textury, přirozené prostředí atd.) vypadat podobně jako u oranges.  Pokud jsou záporné imagí z této povaha, doporučujeme vytvořit jeden nebo více klíčová ("ostatní") a popisek negativní obrázky s touto značkou při školení, které umožňují modelu, který má lepší rozlišit mezi těmito třídami.

## <a name="next-steps"></a>Další postup

Zjistěte, jak můžete otestovat imagí prostřednictvím kódu programu, odešlete jim Predikcí rozhraní API.

> [!div class="nextstepaction"]
[Použití prediktivního rozhraní API](use-prediction-api.md)
