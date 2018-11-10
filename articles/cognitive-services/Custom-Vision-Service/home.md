---
title: Co je Azure Custom Vision?
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí služby Custom Vision vytvářet vlastní klasifikátory obrázků v cloudu Azure.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 10/26/2018
ms.author: anroth
ms.openlocfilehash: 5e8e675b32bfd4c741b82b1ab341a80adbb0529d
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741636"
---
# <a name="what-is-azure-custom-vision"></a>Co je Azure Custom Vision?

Rozhraní API služby Azure Custom Vision je služba Cognitive Services, která umožňuje vytvářet, nasazovat a vylepšovat vlastní klasifikátory obrázků. Klasifikátor obrázků je služba AI, která v závislosti na určitých vlastnostech uspořádává obrázky do tříd (značek). Na rozdíl od služby [Počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) umožňuje služba Custom Vision vytváření vlastních klasifikací.

## <a name="what-it-does"></a>Co dělá

Služba Custom Vision s využitím algoritmu strojového učení klasifikuje obrázky. Jako vývojáři musíte odeslat skupiny obrázků, které obsahují nebo kterým chybí příslušné klasifikace. Při odesílání zadáte správné značky obrázků. Algoritmus se pak podle těchto dat natrénuje a vypočítá vlastní přesnost tím, že se otestuje se stejnými daty. Jakmile se model natrénuje, můžete ho otestovat, přetrénovat a nakonec použít ke klasifikaci nových obrázků v souladu s požadavky vaší aplikace. Samotný model můžete také exportovat pro offline použití.

### <a name="classification-and-object-detection"></a>Klasifikace a detekce objektů

Funkce služby Custom Vision je možné rozdělit do dvou funkcí. **Klasifikace obrázků** přiřadí ke každému obrázku distribuci klasifikací. **Detekce objektů** funguje podobně, ale také vrací souřadnice na obrázku, kde se vyskytují použité značky.

### <a name="optimization"></a>Optimalizace

Obecně platí, že metody, které služba Custom Vision využívá, jsou odolné proti rozdílům, a proto vám k zahájení vytváření prototypů stačí malé množství dat. Obvykle je dobré začít s 50 obrázky pro každou značku. To však znamená, že služba není ideální pro rozpoznávání drobných rozdílů na obrázcích (například menší praskliny nebo promáčknutí ve scénářích kontroly kvality).

Kromě toho máte na výběr několik variant algoritmu služby Custom Vision optimalizovaných pro určité objekty &mdash; například památky nebo maloobchodní zboží. Další informace najdete v příručce věnované [sestavení klasifikátoru](getting-started-build-a-classifier.md).

## <a name="what-it-includes"></a>Co zahrnuje
Služba Custom Vision je dostupná jako sada nativních sad SDK i prostřednictvím webového rozhraní na [domovské stránce služby Custom Vision](https://customvision.ai/). Obě rozhraní umožňují vytvářet, testovat a trénovat modely.

![Domovská stránka služby Custom Vision v okně prohlížeče Chrome](media/browser-home.png)

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat službu Custom Vision na webu, postupujte podle příručky věnované [sestavení klasifikátoru](getting-started-build-a-classifier.md), nebo absolvujte [kurz klasifikace obrázků](csharp-tutorial.md), kde se dozvíte, jak scénář implementovat v kódu.
