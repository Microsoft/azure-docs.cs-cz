---
title: Nepodporovaná jazyková nasazení – vlastní překladač
titleSuffix: Azure Cognitive Services
description: Tento článek ukazuje, jak nasadit nepodporované jazykové páry v Azure Cognitive Services Vlastní překladač.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837451"
---
# <a name="unsupported-language-deployments"></a>Nasazení nepodporovaného jazyka

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

S nadcházejícím vyřazením z Centra Microsoft Translator Hub bude Microsoft zrušit nasazení všech modelů aktuálně nasazených prostřednictvím centra. Mnozí z vás mají modely nasazené v centru, jehož jazykové dvojice nejsou podporovány v vlastní překladač.  Nechceme, aby uživatelé v této situaci neměli žádný postih pro překlad jejich obsahu.

Nyní máme proces, který vám umožní nasadit nepodporované modely prostřednictvím vlastního překladače.  Tento proces umožňuje pokračovat v překladu obsahu pomocí nejnovějšího rozhraní API V3.  Tyto modely budou hostovány, dokud se rozhodnete zrušit jejich nasazení nebo dvojice jazyků bude k dispozici v custom translator.  Tento článek vysvětluje proces nasazení modelů s nepodporovanými jazykovými páry.

## <a name="prerequisites"></a>Požadavky

Aby byly vaše modely kandidáty pro nasazení, musí splňovat následující kritéria:
* Projekt obsahující model musí být migrován z centra do vlastního překladače pomocí nástroje migrace.  Proces migrace projektů a pracovních prostorů naleznete [zde](how-to-migrate.md).
* Model musí být v nasazeném stavu, když dojde k migraci.  
* Dvojice jazyků modelu musí být nepodporovaná dvojice jazyků v aplikaci Custom Translator.  Jazykové páry, ve kterých je jazyk podporován do nebo z angličtiny, ale dvojice sama o sobě neobsahuje angličtinu, jsou kandidáty pro nepodporované jazykové nasazení.  Například model hubu pro dvojici francouzština do němčiny je považován za nepodporovaný jazykový pár, i když je jazyková dvojice z francouzštiny a angličtiny do němčiny podporována.

## <a name="process"></a>Proces
Po migraci modelů z centra, které jsou kandidáty pro nasazení, můžete je najít na stránce **Nastavení** pracovního prostoru a posouváním na konec stránky, kde se zobrazí část **Nepodporovaná školení translatorového centra.**  Tato část se zobrazí pouze v případě, že máte projekty, které splňují výše uvedené požadavky.

![Jak migrovat z centra](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Na stránce výběru **centra nepodporovaných překladačů** obsahuje karta **Unrequested trainings** modely, které jsou způsobilé pro nasazení.  Vyberte modely, které chcete nasadit, a odešlete žádost.   Před termínem nasazení 30.
 
![Jak migrovat z centra](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Po odeslání již nebude model k dispozici na kartě **Nežádoucí školení** a místo toho se zobrazí na kartě **Požadovaná školení.**  Požadovaná školení si můžete kdykoli prohlédnout.

![Jak migrovat z centra](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Co dále?

Modely, které jste vybrali pro nasazení, se uloží, jakmile je centrum vyřazeno z provozu a všechny modely jsou nenasazené.  Máte čas do 24 května odeslat žádosti o nasazení nepodporovaných modelů.  Tyto modely nasadíme 15.  Kromě toho budou k dispozici prostřednictvím V2 API až do července 1.  

Pro další informace o důležitých datech v odsuzování Hub zkontrolujte [zde](https://www.microsoft.com/translator/business/hub/).
Po nasazení budou účtovány běžné poplatky za hostování.  Podrobnosti najdete v [tématu ceny.](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)  

Na rozdíl od standardních modelů vlastní překladač, hub modely budou k dispozici pouze v jedné oblasti, takže více oblastí hostování poplatky nebudou platit.  Po nasazení budete moci kdykoli zrušit nasazení modelu centra prostřednictvím migrovaného projektu Vlastní překladač.

## <a name="next-steps"></a>Další kroky

- [Trénování modelu](how-to-train-model.md).
- Začněte používat nasazený vlastní model překladu prostřednictvím [rozhraní Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
