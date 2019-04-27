---
title: Jak migrovat projekt do rozhraní 3.0 rozhraní API
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak migrovat projekty Custom Vision z předchozí verze rozhraní API do rozhraní 3.0 rozhraní API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816456"
---
# <a name="migrate-to-the-30-api"></a>Migrace na rozhraní 3.0 rozhraní API

Custom Vision dosáhla nyní obecně dostupná a prošel vizuálními aktualizace rozhraní API.
Tato aktualizace zahrnuje několik nových funkcí a co je důležité, několik nejnovější změny:

* Prediction API je teď rozdělit do dvou podle typu projektu.
* Možnost exportu pro zpracování obrazu AI Developer Kit (VAIDK) vyžaduje vytvoření projektu určitým způsobem.
* Výchozí iterace jsme odebrali a místo toho použití publikování / zrušit publikování pojmenované iterace.

Tato příručka se ukazují, jak aktualizovat vaše projekty pro práci s novou verzí rozhraní API. Zobrazit [poznámky k verzi](release-notes.md) zobrazit úplný seznam změn.

## <a name="use-the-updated-prediction-api"></a>Použít aktualizované Prediction API.

2.x rozhraní API používá stejné volání předpovědi pro klasifikátorů obrázků a objekt detektor projekty. Oba typy projektů byla akceptovatelná **PredictImage** a **PredictImageUrl** volání. Od verze 3.0, jsme rozdělili toto rozhraní API tak, aby vám musí odpovídat typu projektu volání:

* Použití **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** a **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** zobrazíte předpovědi pro projekty klasifikace obrázků.
* Použití **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** a **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** zobrazíte předpovědi pro projekty zjišťování objektu.

## <a name="use-the-new-iteration-publishing-workflow"></a>Pomocí nového pracovního postupu publikování iterace

Rozhraní API 2.x používá výchozí iterace nebo ID zadané iteraci zvolit iteraci pro účely předpovědi. Od verze 3.0, jsme přijali publikování tok, které nejprve publikovat iterace pod zadaným názvem z rozhraní API školení. Potom předejte název předpovědi metod k určení, jaké iterace použít.

> [!IMPORTANT]
> Rozhraní 3.0 rozhraní API nepoužívejte funkci výchozí iterace. Dokud jsme přestat používat starší rozhraní API, můžete nadále používat rozhraní API 2.x přepnete iterace jako výchozí. Tato rozhraní API se zachová pro určitou dobu a může volat **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metoda k označení iterace jako výchozí.

### <a name="publish-an-iteration"></a>Publikování iterace

Jakmile se trénuje iteraci, můžete si je k dispozici pro použití předpovědi **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** metody. K publikování iterace, budete potřebovat předpovědi ID prostředku, který je k dispozici na stránce nastavení CustomVision webu.

![Custom Vision stránce Nastavení webu s ID prostředku predikcí uvedené.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Můžete také získat tyto informace z [webu Azure Portal](https://portal.azure.com) prostředku Custom Vision předpovědi a vybrat možnost **vlastnosti**.

Po publikování vaší iterace aplikace můžou používat ho pro předpověď zadáním názvu v telefonickém hovoru při prediktivního rozhraní API. Chcete-li k dispozici pro prediktivních volání iterace, použijte **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** rozhraní API.

## <a name="additional-export-options"></a>Možnosti exportu

Pomocí rozhraní 3.0 exportovat rozhraní API jsme jsou vystaveny další dva cíle: Architektura ARM a sady pro vývojáře AI pro zpracování obrazu.

* Pokud chcete použít ARM, stačí vybrat Compact domény a pak zvolte soubor DockerFile a potom ARM jako možnosti exportu.
* Pro zpracování obrazu AI Dev Kit, je potřeba vytvořit projekt s __obecné (Compact)__ domény a také určit VAIDK v cílové platformy argument exportovat.

## <a name="next-steps"></a>Další postup

* [Školení referenční dokumentace rozhraní API (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Referenční dokumentace prediktivního rozhraní API (REST)](https://go.microsoft.com/fwlink/?linkid=865445)