---
title: Nepodporovaný jazyk nasazení – vlastní Translator
titleSuffix: Azure Cognitive Services
description: Jak nasadit dvojice nepodporovaný jazyk v Translatoru Custom.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: v-pawal
ms.openlocfilehash: 09fbd771d945646fe385508779d38e4abb2ee293
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476503"
---
# <a name="unsupported-language-deployments"></a>Nepodporovaný jazyk nasazení

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Se chystá vyřazení centra Microsoft Translator bude Microsoft undeploying všechny modely, které jsou aktuálně nasazené prostřednictvím centra. Řada z vás má modelů nasazení v centru, jehož páry jazyk nepodporuje vlastní překladač.  Jsme nechcete, aby uživatelé v této situaci žádné obrátit pro převod jejich obsah.

Nyní je k dispozici proces, který vám umožní nasadit nepodporované modelů prostřednictvím vlastní překladač.  Tento proces umožní vám pokračujte pro převod obsahu pomocí nejnovější rozhraní API V3.  Tyto modely bude hostována, dokud se rozhodnete zrušit je nasazení nebo dvojici jazyků bude k dispozici ve vlastní Translator.  Tento článek vysvětluje postup nasazení modelů s páry nepodporovaný jazyk.

## <a name="prerequisites"></a>Požadavky

Aby vlastní modely a jednat o kandidáty na nasazení musí splňovat následující kritéria:
* Projekt, který obsahuje model musí se migrovaly z centra pro vlastní Translator, pomocí nástroje migrace.  Proces migrace projektů a pracovní prostory můžete najít [tady](how-to-migrate.md).
* Model musí být v nasazeném stavu, pokud dojde k migraci.  
* Pár jazyk modelu musí být páru nepodporovaný jazyk v Translatoru Custom.  Dvojice jazyků, ve kterých jazyk se podporuje do nebo z angličtina, ale pár samotné nezahrnuje angličtina, jsou kandidáty pro nasazení nepodporovaný jazyk.  Například považuje model rozbočovače pro francouzštinu, němčinu spárovat nepodporovaný jazyk pár dokonce i když francouzské pro angličtinu a angličtině německé jsou podporované jazykové páru.

## <a name="process"></a>Proces
Po migraci modely z centra, které jsou kandidáty pro nasazení, najdete je tak, že přejdete **nastavení** pro váš pracovní prostor a posouvání na konec stránky, kde se zobrazí stránka **nepodporováno Překladač rozbočovače školení** oddílu.  Tato část se zobrazí, jenom Pokud máte projekty, které splňují požadavky uvedenými výše.

![Jak migrovat z centra](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

V rámci **nepodporované Translator Centrum školení** stránka pro výběr, **nevyžádanou školení** karta obsahuje modely, které jsou vhodné pro nasazení.  Vyberte modely, které chcete nasadit a odešlete žádost.   Před 30. dubna konečný termín nasazení můžete vybrat libovolný počet modelů, kolik chcete, aby pro nasazení.
 
![Jak migrovat z centra](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Po odeslání modelu již nebude k dispozici na **nevyžádanou školení** kartu a zobrazí se místo toho na **požadované školení** kartu.  Kdykoli můžete zobrazit požadované školení.

![Jak migrovat z centra](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Co dále?

Modelů, které jste vybrali pro nasazení se uloží, jakmile dojde k deaktivaci centra a jsou všechny modely nasazení.  Je nutné do 24 může odesílat požadavky na nasazení nepodporované modelů.  Nasadíme těchto modelů na 15. červnem. v tomto okamžiku budou přístupné prostřednictvím rozhraní Translator API V3.  Kromě toho budou k dispozici prostřednictvím rozhraní API V2 až do 1 dne.  

Další informace o důležitá data v vyřazení kontroly centra [tady](https://www.microsoft.com/translator/business/hub/).
Jakmile nasazené, normální hostování budou poplatky.  Zobrazit [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) podrobnosti.  

Na rozdíl od standardních Translator vlastní modely modely centra budou k dispozici pouze v jedné oblasti a tak se nevztahuje hostitelské nákladů ve více oblastech.  Po nasazení, bude moct zrušení modelu centra kdykoli přes migrovaného projektu vlastní překladač.

## <a name="next-steps"></a>Další postup

- [Trénování modelu](how-to-train-model.md).
- Začněte používat váš model nasazený vlastní překlad prostřednictvím [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
