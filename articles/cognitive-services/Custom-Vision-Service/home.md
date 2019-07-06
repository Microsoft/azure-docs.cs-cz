---
title: Co je Azure Custom Vision?
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí služby Custom Vision vytvářet vlastní klasifikátory obrázků v cloudu Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 8a5dd16948724e3a79863450212702aa8aeb2347
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605146"
---
# <a name="what-is-azure-custom-vision"></a>Co je Azure Custom Vision?

Azure Custom Vision je služby cognitive Services vám umožňuje vytvořit, nasadit a zlepšit vlastních klasifikátorů obrázků. Obrázku třídění je služba AI, která se použije popisky (které představují _třídy_) na Image, podle jejich vizuální vlastnosti. Na rozdíl od [pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) service, vlastní zpracování obrazu umožňuje určit popisky použít.

## <a name="what-it-does"></a>Co dělá

Custom Vision service pomocí algoritmu strojového učení použije popisky na obrázky. Vás jako na vývojáři, musíte odeslat skupiny imagí, které běží na procesorech a nemají dotyčný charakteristiky. Označování imagí sami v okamžiku odeslání. Pak algoritmus trénovat na tato data a vypočítává samotného testování na těchto imagí stejné vlastní přesnost. Jakmile se trénuje algoritmus, testování a přeučování nakonec použijete ke klasifikaci nových imagí podle potřeb vaší aplikace. Samotný model můžete také exportovat pro offline použití.

### <a name="classification-and-object-detection"></a>Klasifikace a detekce objektů

Funkce služby Custom Vision je možné rozdělit do dvou funkcí. **Klasifikace obrázků** jeden nebo více štítků platí pro bitovou kopii. **Objekt detekce** je podobné, ale také vrátí souřadnice na obrázku kde lze nalézt použité názvy.

### <a name="optimization"></a>Optimalizace

Custom Vision service je optimalizovaná na hlavní rozdíly mezi bitové kopie, abyste je mohli začít vytváření prototypů modelu s menším objemem dat rychle rozpoznat. 50 obrázků na popisek jsou obecně o dobrý začátek. Služba však není ideální pro zjišťování lišila imagí (třeba rozpoznání menší praskliny nebo dents ve scénářích kvality assurance).

Kromě toho můžete vybrat z několika typy prvků Custom Vision algoritmus, které jsou optimalizovány pro Image některé materiály, které subjektu&mdash;například zajímavá nebo maloobchodního prodeje položek. Další informace najdete v tématu [sestavení klasifikátoru](getting-started-build-a-classifier.md) průvodce.

## <a name="what-it-includes"></a>Co zahrnuje

Služba Custom Vision je dostupná jako sada nativních sad SDK i prostřednictvím webového rozhraní na [domovské stránce služby Custom Vision](https://customvision.ai/). Můžete vytvořit, otestovat a trénování modelu až buď rozhraní nebo použít společně.

![Domovská stránka služby Custom Vision v okně prohlížeče Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Ochrany osobních údajů a zabezpečení

Jako se všemi služeb Cognitive Services, vývojáře, kteří používají Custom Vision service měli vědět zásady společnosti Microsoft na zákaznická data. Zobrazit [stránku služeb Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na webu Microsoft Trust Center další informace.

## <a name="next-steps"></a>Další postup

Postupujte podle [sestavení klasifikátoru](getting-started-build-a-classifier.md) průvodce a začněte využívat na webu Custom Vision nebo dokončení [kurz klasifikace obrázků](csharp-tutorial.md) základní scénář implementovat v kódu.
