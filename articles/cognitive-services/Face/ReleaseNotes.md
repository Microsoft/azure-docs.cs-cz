---
title: Poznámky k verzi - Face service
titleSuffix: Azure Cognitive Services
description: Poznámky k verzi pro službu Face obsahují historii změn verzí pro různé verze.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165876"
---
# <a name="face-release-notes"></a>Poznámky k verzi obličeje

Tento článek se týkajících se služby Face verze 1.0.

### <a name="release-changes-in-june-2019"></a>Změny vydání v červnu 2019

* Byl přidán nový model detekce obličeje s vylepšenou přesností na malých, bočních, uzavřených a rozmazaných tvářích. Použít prostřednictvím [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Přidat tvář](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a [LargePersonGroup Osoba - Přidat plochu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) zadáním nového názvu `detection_02` modelu detekce tváří v `detectionModel` parametru. Další podrobnosti v [části Jak určit model detekce](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Změny vydání v dubnu 2019

* Vylepšená celková `age` přesnost `headPose` atributů a. Atribut `headPose` je také aktualizován `pitch` s hodnotou povolenou nyní. Tyto atributy použijte tak, `returnFaceAttributes` že je určíte v parametru [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametr. 

* Vylepšená rychlost [obličeje - Detekce](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Přidat tvář](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a [LargePersonGroup Person - Přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Změny vydání v březnu 2019

* Přidán nový model rozpoznávání obličejů s vylepšenou přesností. Použít prostřednictvím [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - Vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - Vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) a [LargePersonGroup - Vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) zadáním nového názvu `recognition_02` modelu rozpoznávání obličejů v `recognitionModel` parametru. Další podrobnosti v [části Jak zadat model rozpoznávání](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Změny vydání v lednu 2019

* Přidána funkce Snímek pro podporu migrace dat mezi odběry: [Snímek](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Další podrobnosti v části [Jak migrovat data o obličeji do jiného předplatného face](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Změny vydání v říjnu 2018

* Upřesněný `status`popis `createdDateTime` `lastActionDateTime`pro `lastSuccessfulTrainingDateTime` , , a v [PersonGroup - získat stav školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - získat stav školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)a [LargeFaceList - Získat stav školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Změny vydání v květnu 2018

* Výrazně `gender` vylepšený atribut `age` `glasses`a `facialHair` `hair`také `makeup` vylepšený , , , atributy. Použijte je prostřednictvím [face - detekovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametr. 

* Zvýšenlimit velikosti souboru vstupního obrazu ze 4 MB na 6 MB v [obličeji - Detekovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Přidat tvář](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a [LargePersonGroup Person - Přidat tvář](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Změny vydání v březnu 2018

* Přidán kontejner v milionovém měřítku: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) a [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Další podrobnosti v části Použití funkce [ve velkém měřítku](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Zvýšená [plocha - Identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` parametru z [1, 5] na [1, 100] a výchozí na 10.

### <a name="release-changes-in-may-2017"></a>Změny vydání v květnu 2017

* Přidány `hair` `makeup`, `accessory` `occlusion`, `blur` `exposure`, `noise` , , a atributy v [Face - Detekovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametr.

* Podporované osoby 10K v PersonGroup a [Face - Identifikovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Podporované stránkování v [persongroup person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) seznam `start` `top`s volitelnými parametry: a .

* Podporovaná souběžnost při přidávání nebo odstranění tváří proti různým facelists a různým osobám v PersonGroup.

### <a name="release-changes-in-march-2017"></a>Změny vydání v březnu 2017
* Přidán `emotion` atribut v [face - detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametr.

* Opravena plocha nemohla být znovu zjištěna s `targetFace` obdélníkem vráceným z plochy - [Rozpoznat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jako v [FaceList - Přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) a osobu [PersonGroup - Přidat obličej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Opravena zjistitelná velikost obličeje, aby bylo zajištěno, že je přísně mezi 36x36 až 4096x4096 pixelů.

### <a name="release-changes-in-november-2016"></a>Změny vydání v listopadu 2016
* Přidáno předplatné Face Storage Standard pro ukládání dalších trvalých tváří při použití [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) nebo [FaceList - Přidat tvář](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) pro identifikaci nebo párování podobnosti. Za 1 000 uložených obrázků tváří se účtuje 0,5 USD. Tato sazba se každý den poměrně přepočítává. Bezplatné předplatná úrovně jsou i nadále omezena na 1 000 celkových osob.

### <a name="release-changes-in-october-2016"></a>Změny vydání v říjnu 2016
* Chybová zpráva více než jedné tváře v targetFace z 'Existuje více než jedna tvář v obrázku' na 'Existuje více než jedna tvář v obrázku' v [FaceList - Přidat tvář](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) a [PersonGroup Person - Přidat tvář](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Změny vydání v červenci 2016
* Podporované ověřování objektů tváří v ploše [– ověřit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Přidán `mode` volitelný parametr umožňující výběr `matchPerson` dvou `matchFace` pracovních režimů: a `matchPerson`v Face - Najít [podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a výchozí je .

* Přidán `confidenceThreshold` volitelný parametr pro uživatele pro nastavení prahové hodnoty, zda jedna plocha patří k objektu Person v [oblasti Face - Identifikovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Přidány `start` volitelné `top` a parametry v [PersonGroup - Seznam,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) který umožňuje uživateli zadat počáteční bod a celkové číslo PersonGroups do seznamu.

### <a name="v10-changes-from-v0"></a>Změny V1.0 z V0
* Aktualizovaný koncový bod ```https://westus.api.cognitive.microsoft.com/face/v0/``` kořenového adresáře služby z do . ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` Změny použité pro: [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), Face - [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), Face - [Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a Face [- Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Byla aktualizována minimální zjistitelná velikost obličeje na 36 x 36 pixelů. Plochy menší než 36 x 36 pixelů nebudou rozpoznány.

* Zastaralá data PersonGroup a Person v obličeji V0. Tato data nelze získat přístup pomocí služby Face V1.0.

* 30. června 2016 byl zastaral koncový bod V0 rozhraní API pro rozpoznávání tváře.
