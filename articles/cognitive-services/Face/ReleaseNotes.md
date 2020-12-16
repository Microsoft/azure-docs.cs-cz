---
title: Co je nového ve službě Face?
titleSuffix: Azure Cognitive Services
description: Zpráva k vydání verze pro službu obličeje obsahuje historii změn verzí pro různé verze.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8a409d66065a7ccd2b34365bd565202f207ac0e8
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560742"
---
# <a name="whats-new-in-face-service"></a>Co je nového ve službě Face?

Služba Azure Faceer se průběžně aktualizuje. Tento článek vám umožní udržovat aktuální informace o vylepšeních, opravách a dokumentaci k funkcím.

## <a name="november-2020"></a>Listopad 2020
* Publikovali jsme aplikaci pro registraci ukázkového obličeje, která předvádí osvědčené postupy pro stanovení smysluplného souhlasu a vytváření vysoce přesností systémů rozpoznávání obličeje pomocí vysoce kvalitních registrací. Ukázku Open Source najdete v průvodci [vytvořením aplikace pro zápis](build-enrollment-app.md) a na [GitHubu](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample), která je připravená pro vývojáře k nasazení nebo přizpůsobení.  

## <a name="june-2019"></a>Červen 2019

* Přidali jsme nový model detekce plochy s vylepšenou přesností na malém, bočním, zastíněna a rozmazaných plochách. Můžete ji použít prostřednictvím [obličeje –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) [FaceList – přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – přidat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)obličej, osoba [osoby – přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a [LargePersonGroup osobu – přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) zadáním názvu nového modelu detekce obličeje `detection_02` v `detectionModel` parametru. Další podrobnosti najdete v tématu [Určení modelu detekce](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>Duben 2019

* Zvýšila se celková přesnost `age` `headPose` atributů a. `headPose`Atribut je také aktualizován `pitch` hodnotou Enabled Now. Použijte tyto atributy jejich zadáním v `returnFaceAttributes` parametru parametru [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` . 

* Vylepšená rychlost pro [rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – přidání obličeje](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – přidat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)obličej, osoba [osoby – přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a [LargePersonGroup osobu – přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2019"></a>Březen 2019

* Přidali jsme nový model rozpoznávání tváře s vyšší přesností. Použijte ji prostřednictvím [obličeje –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) [FaceList – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [Person-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) a [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) zadáním názvu nového modelu rozpoznávání obličeje `recognition_02` v `recognitionModel` parametru. Další podrobnosti najdete v tématu [Určení modelu rozpoznávání](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>Leden 2019

* Byla přidána funkce snímku pro podporu migrace dat mezi předplatnými: [snímek](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Další podrobnosti [o tom, jak migrovat data vaší plochy na jiný obličejový odběr](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>Říjen 2018

* Popis upřesnění pro `status` , `createdDateTime` , `lastActionDateTime` a `lastSuccessfulTrainingDateTime` v [Persone – získat stav školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup stav školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)a [LargeFaceList – získat stav školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

## <a name="may-2018"></a>Květen 2018

* Vylepšený `gender` atribut významně a také vylepšili,,, `age` `glasses` `facialHair` `hair` , `makeup` atributy. Použijte je pomocí parametru [pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` . 

* Vyšší omezení velikosti souboru pro vstupní image ze 4 MB na 6 MB v [tváři – detekce](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – přidání](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)obličeje, [LargeFaceList – přidat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)obličej, osoba [osoby – přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a [LargePersonGroup osobu – přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)

## <a name="march-2018"></a>Březen 2018

* Přidání kontejneru Million-Scale: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) a [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Podrobnější informace najdete v tématu [Jak používat velkou škálu funkcí](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Zvýšila se hodnota parametru [Face-identifikuje](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` se z [1, 5] na [1, 100] a na hodnotu 10.

## <a name="may-2017"></a>Květen 2017

* Přidání `hair` `makeup` atributů,, `accessory` ,,, a `occlusion` `blur` `exposure` `noise` v parametru [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .

* Podporuje se 10 000 osob ve vaší osobě a v [identifikaci obličeje](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Podporované stránkování na [straně osoby – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) s nepovinnými parametry: `start` a `top` .

* Podporovaná souběžnost při přidávání a odstraňování plošek s různými FaceLists a různými osobami ve službě person

## <a name="march-2017"></a>Březen 2017
* `emotion`Do parametru [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) byl přidán atribut `returnFaceAttributes` .

* Opravili jsme plošku, která se vrátila z [obličeje](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) , jako `targetFace` v [FaceList – přidejte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) obličej a osobu [osoby – přidejte obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Opravili jsme zjistitelnou velikost obličeje, abyste se ujistili, že je striktně mezi 36x36 a 4096x4096 pixely.

## <a name="november-2016"></a>Listopad 2016
* Přidání předplatného Úložiště tvářího standard pro ukládání dalších trvalých plošek při použití [osoby osoby – přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) nebo [FaceList – přidejte obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) pro identifikaci nebo porovnání podobnosti. Za 1 000 uložených obrázků tváří se účtuje 0,5 USD. Tato sazba se každý den poměrně přepočítává. Předplatná úrovně Free se nadále omezuje na 1 000 celkových osob.

## <a name="october-2016"></a>Říjen 2016
* Změnila se chybová zpráva o více než jedné ploškě v targetFace z ' je více než jedna ploška v obrázku ' ' na ' ' je více než jedna ploška v obraze ' v [FaceList – přidejte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) plochu a osobu [– Přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="july-2016"></a>Červenec 2016
* Podporovaná plocha pro ověřování objektu osoby na [úrovni obličeje – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Přidání volitelného `mode` parametru, který umožňuje výběr dvou pracovních režimů: `matchPerson` a `matchFace` na [ploše, podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a výchozí, je `matchPerson` .

* Přidání volitelného `confidenceThreshold` parametru pro uživatele k nastavení prahové hodnoty, ať už jedna ploška patří objektu person v [rámci identifikace obličeje](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Přidání volitelných `start` `top` parametrů a v [seznamu osob – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) umožní uživateli zadat počáteční bod a celkové objektů persongroup číslo pro výpis.

## <a name="v10-changes-from-v0"></a>Změny v 1.0 od v0

* Kořenový koncový bod služby z ```https://westus.api.cognitive.microsoft.com/face/v0/``` na se aktualizoval ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` . Změny, které se aplikují na: [plošky](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [plošky](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), obličeje a tváře – [najde podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a [plošky](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Velikost minimální zjistitelné plochy se aktualizovala na 36x36 pixelů. Plošky menší než 36x36 pixelů nebudou zjišťovány.

* Údaje o osobě a osobě se zastaraly v tváři v0. K těmto datům nelze přidružit službu FACET V 1.0.

* Konec platnosti koncového bodu v0 Face API dne 30. června 2016.
