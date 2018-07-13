---
title: Přehled strojového učení s využitím služby Custom Vision Service – Azure Cognitive Services | Microsoft Docs
description: Custom Vision Service je služba Microsoft Cognitive Services, která umožňuje vytvářet vlastní klasifikátory obrázků na platformě Azure.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087572"
---
# <a name="what-is-the-custom-vision-service"></a>Co je Custom Vision Service?

Custom Vision Service je služba Microsoft Cognitive Services, která umožňuje vytvářet vlastní klasifikátory obrázků. Umožňuje rychle a snadno vytvořit, nasadit a vylepšovat klasifikátor obrázků. Custom Vision Service pro nahrávání obrázků a trénování klasifikátoru poskytuje rozhraní REST API a webové rozhraní.

## <a name="what-does-custom-vision-service-do-well"></a>K čemu je služba Custom Vision Service vhodná?

Custom Vision Service funguje nejlépe, když je položka, kterou se pokoušíte klasifikovat, na obrázku ta hlavní. 

K vytvoření klasifikátoru nebo detektoru stačí několik obrázků. Ke spuštění prototypu vám stačí 50 obrázků pro každou třídu. Metody, které služba Custom Vision Service využívá, jsou odolné proti rozdílům a proto vám k zahájení vytváření prototypů stačí tak málo dat. To znamená, že služba Custom Vision Service není vhodná pro scénáře, kdy chcete rozpoznávat drobné rozdíly. Například menší praskliny nebo promáčknutí ve scénářích kontroly kvality.

## <a name="release-notes"></a>Poznámky k verzi

### <a name="may-7-2018"></a>7. května 2018
- Zavedení funkce detekce objektů ve verzi Preview pro projekty využívající omezenou zkušební verzi
- Upgrade na rozhraní API verze 2.0
- Rozšíření úrovně S0 až na 250 značek a 50 000 obrázků 
- Významná vylepšení back-endu kanálu strojového učení pro projekty klasifikace obrázků. Výhody těchto aktualizací budou moct využít projekty natrénované po 27. dubnu 2018.
- Přidání možnosti exportu modelů do ONNX pro použití s Windows ML
- Přidání možnosti exportu modelů do souboru Dockerfile. Díky tomu si můžete stáhnout artefakty pro sestavení vlastních kontejnerů Windows nebo Linuxu, včetně souboru Dockerfile, modelu TensorFlow a kódu služby. 
- Pro zajištění konzistence napříč všemi projekty jsou teď pro nově natrénované modely exportované do TensorFlow v doménách General (Compact) (Obecné – kompaktní) a Landmark (Compact) (Památka – kompaktní) [střední hodnoty (0, 0, 0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample). 

### <a name="march-1-2018"></a>1. března 2018
- Příchod placené verze Preview a registrace na web Azure Portal. Projekty je teď možné připojit k prostředkům Azure s úrovní F0 (Free) nebo S0 (Standard). Zavedení projektů úrovně S0, které umožňují až 100 značek a 25 000 obrázků. 
- Změny back-endu kanálu strojového učení a parametru normalizace. Zákazníci tak získají lepší kontrolu nad kompromisy mezi přesností a opětovným voláním při úpravě prahové hodnoty pravděpodobnosti. Součástí těchto změn je nastavení prahové hodnoty pravděpodobnosti na portálu CustomVision.ai na 50 %.

### <a name="december-19-2017"></a>19. prosince 2017

- Přidání možnosti exportu na Android (TensorFlow) navíc k dříve vydané možnosti exportu na iOS (CoreML). To umožňuje exportovat natrénovaný kompaktní model pro offline spouštění v aplikaci.
- Přidání kompaktních domén Retail (Maloobchod) a Landmark (Památka) umožňujících export modelů pro tyto domény.
- Vydání [rozhraní API pro trénování verze 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) a [rozhraní API pro predikce verze 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Aktualizovaná rozhraní API podporují export modelů a novou operaci predikce, která neukládá obrázky do složky Predictions. V rozhraní API pro trénování se také zavedly dávkové operace.
- Vylepšení uživatelského rozhraní, včetně možnosti zobrazit, která doména se použila k trénování iterace.
- Aktualizace sady [SDK pro jazyk C# a ukázky](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

## <a name="next-steps"></a>Další kroky

[Informace o vytvoření klasifikátoru](getting-started-build-a-classifier.md)
