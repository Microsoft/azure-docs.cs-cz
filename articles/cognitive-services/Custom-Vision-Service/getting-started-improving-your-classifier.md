---
title: Zlepšení vaší třídění pomocí vlastní vize služby - kognitivní služeb Azure | Microsoft Docs
description: Zjistěte, jak ke zlepšení kvality třídění vaše vlastní vize služby.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342861"
---
# <a name="how-to-improve-your-classifier"></a>Jak ke zlepšení vaší třídění

Zjistěte, jak ke zlepšení kvality třídění vaše vlastní vize služby. Kvalitu vaší třídění je závislá na kvalitu s popiskem data, která zadáte na ni. 

## <a name="train-more-varied-images"></a>Cvičení více různým bitové kopie

Poskytování různých úhlů, pozadí, velikost objektu s příznakem bitové kopie, skupiny fotografie a ostatní varianty zlepšuje třídění. Fotografie v kontextu jsou lepší, než fotografie před neutrální pozadí. Zahrnout bitové kopie, které jsou zástupce co bude odeslána do třídění během normálního použití.

Další informace o přidávání obrázků, najdete v článku [sestavení třídění](getting-started-build-a-classifier.md) dokumentu.

> [!IMPORTANT]
> Mějte na paměti, ke cvičení třídění po přidání bitových kopií.

## <a name="use-images-submitted-for-prediction"></a>Používat Image odeslané pro předpověď

Službu vize vlastní ukládá odeslané ke koncovému bodu předpovědi bitové kopie. Chcete-li zlepšit třídění pomocí těchto bitových kopií, použijte následující kroky:

1. Chcete-li zobrazit obrázky odeslané do třídění, otevřete [vize vlastní webové stránky](https://customvision.ai) a vyberte __předpovědi__ kartě.

    ![Obrázek na kartě předpovědi](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > Výchozí zobrazení ukazuje bitové kopie z na aktuální iteraci. Můžete použít __iterace__ rozevírací pole, které chcete zobrazit obrázky odeslaných během předchozí iterací.

2. Podržte ukazatel nad najdete v části značky, které byly předpovědět třídění bitovou kopii.

    > [!TIP]
    > Bitové kopie, jsou seřazeny, tak, aby bitové kopie, které můžete zahrnout dostane většině k třídění v horní části. Pokud chcete vybrat jiný řazení, použijte __řazení__ části.

    Chcete-li přidat bitovou kopii do Cvičná data, vyberte bitovou kopii, vyberte značky a pak vyberte __uložte a zavřete__. Obrázek se odebere z __předpovědi__ a přidat do bitové kopie školení. Můžete ji zobrazit výběrem __školení image__ kartě.

    ![Obrázek označování stránky](./media/getting-started-improving-your-classifier/tag-image.png)

3. Použití __Train__ tlačítko přeučování třídění.

## <a name="visually-inspect-predictions"></a>Vizuální kontrola předpovědi

Chcete-li prověřit předpovědi bitové kopie, vyberte __školení image__ a pak vyberte __iterace historie__. Bitové kopie, které jsou uvedeny s červeným polem byly nesprávně předpovědět.

![Obrázek historie iterace](./media/getting-started-improving-your-classifier/iteration-history.png)

Někdy visual kontroly můžete identifikovat vzorů, které pak můžete vyřešit tak, že přidáte další Cvičná data. Například třídění pro růže oproti daises může nesprávně popisku všechny bílé růže jako daises. Je možné, že tento problém opravili přidáním a poskytování školení dat, která obsahuje s příznakem bitových kopií bílé růží.

## <a name="unexpected-classification"></a>Neočekávané klasifikace

Někdy třídění zjišťuje charakteristiky, které mají společnou obrázků. Například chcete vytvořit třídění pro růže oproti Tulipány. Můžete zadat Image Tulipány v polích a růží v red váza před blue wall. Zadané tato data, může pro pole oproti wall + váza místo růže oproti Tulipány cvičení třídění.

Chcete-li tento problém opravili, použijte pokyny v školení s více nejrůznější bitové kopie: poskytnout různých úhlů, pozadí, velikost objektu, skupiny a další variant bitové kopie.

## <a name="negative-image-handling"></a>Zpracování záporné obrázku

Služba vlastní vize podporuje zpracování některé automatické záporné obrázku. Pokud vytváříte cat oproti PSA třídění a odeslání image čelisti pro předpověď, třídění musí stanovení skóre této bitové kopie jako blížící se 0 % cat a PSA. 

> [!WARNING]
> Automatické přístup funguje pro jasně záporné bitové kopie. Se nemusí fungovat i v případech, kdy záporné bitové kopie jsou právě varianta obrázků použitých v školení. 
>
> Například pokud máte husky oproti corgi třídění a kanál v bitové kopie Pomeranian, může dosáhnout Pomeranian jako Husky. Pokud vaše záporné Image této povahy, vytvořte novou značku (například "ostatní") a použijte ho pro obrázky záporné školení.

## <a name="next-steps"></a>Další postup

[Použít předpovědi rozhraní API](use-prediction-api.md)