---
title: Zpráva k vydání verze služby Custom Vision
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.date: 08/28/2018
ms.author: anroth
ms.openlocfilehash: 8423051fa5169eb8acddc7297e36188e297cb9e4
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288673"
---
# <a name="custom-vision-service-release-notes"></a>Zpráva k vydání verze služby Custom Vision

## <a name="august-14-2018"></a>14. srpna 2018
- Přidání "Začínáme" widget, který se customvision.ai webu provedou uživatele školení projektu. 
- Další vylepšení kanálu strojového učení prospěch multilabel projektů (Nová vrstva ztráty).

## <a name="june-28-2018"></a>28. června 2018
- Opravy chyb a back-endu vylepšení.
- Enabeled Multiclass klasifikace pro projekty, kde mají přesně jeden popisek Image. V předpovědi pro režim s více třídami, bude do jednoho součet pravděpodobnosti (všechny bitové kopie jsou klasifikovány mezi zadaným značkám).

## <a name="june-13-2018"></a>13. června 2018
- Aktualizace uživatelského rozhraní, zaměřuje na snadnost použití a přístupnost. 
- Vylepšení kanálu strojového učení prospěch multilabel projektů s velkým množstvím značek.
- Oprava chyby v TensorFlow exportu. Exportovat Enabeled model správy verzí, tak iterace je možné exportovat více než jednou. 

## <a name="may-7-2018"></a>7. května 2018
- Zavedení funkce detekce objektů ve verzi Preview pro projekty využívající omezenou zkušební verzi
- Upgrade na rozhraní API verze 2.0
- Rozšíření úrovně S0 až na 250 značek a 50 000 obrázků 
- Významná vylepšení back-endu kanálu strojového učení pro projekty klasifikace obrázků. Výhody těchto aktualizací budou moct využít projekty natrénované po 27. dubnu 2018.
- Přidání možnosti exportu modelů do ONNX pro použití s Windows ML
- Přidání možnosti exportu modelů do souboru Dockerfile. Díky tomu si můžete stáhnout artefakty pro sestavení vlastních kontejnerů Windows nebo Linuxu, včetně souboru Dockerfile, modelu TensorFlow a kódu služby. 
- Pro zajištění konzistence napříč všemi projekty jsou teď pro nově natrénované modely exportované do TensorFlow v doménách General (Compact) (Obecné – kompaktní) a Landmark (Compact) (Památka – kompaktní) [střední hodnoty (0, 0, 0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample). 

## <a name="march-1-2018"></a>1. března 2018
- Příchod placené verze Preview a registrace na web Azure Portal. Projekty je teď možné připojit k prostředkům Azure s úrovní F0 (Free) nebo S0 (Standard). Zavedení projektů úrovně S0, které umožňují až 100 značek a 25 000 obrázků. 
- Změny back-endu kanálu strojového učení a parametru normalizace. Zákazníci tak získají lepší kontrolu nad kompromisy mezi přesností a opětovným voláním při úpravě prahové hodnoty pravděpodobnosti. Součástí těchto změn je nastavení prahové hodnoty pravděpodobnosti na portálu CustomVision.ai na 50 %.

## <a name="december-19-2017"></a>19. prosince 2017

- Přidání možnosti exportu na Android (TensorFlow) navíc k dříve vydané možnosti exportu na iOS (CoreML). To umožňuje exportovat natrénovaný kompaktní model pro offline spouštění v aplikaci.
- Přidání kompaktních domén Retail (Maloobchod) a Landmark (Památka) umožňujících export modelů pro tyto domény.
- Vydání [rozhraní API pro trénování verze 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) a [rozhraní API pro predikce verze 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Aktualizovaná rozhraní API podporují export modelů a novou operaci predikce, která neukládá obrázky do složky Predictions. V rozhraní API pro trénování se také zavedly dávkové operace.
- Vylepšení uživatelského rozhraní, včetně možnosti zobrazit, která doména se použila k trénování iterace.
- Aktualizace sady [SDK pro jazyk C# a ukázky](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

