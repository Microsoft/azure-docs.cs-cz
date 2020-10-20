---
title: Nepodporovaná nasazení jazyků – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak nasadit nepodporované páry jazyků v Azure Cognitive Services vlastního překladatele.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: a5918adfb6724c984f4a59fda802e2f4c5cde7c1
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221192"
---
# <a name="unsupported-language-deployments"></a>Nasazení nepodporovaného jazyka

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Díky nadcházejícímu vyřazení centra Microsoft Translator bude Microsoft odsazovat všechny modely aktuálně nasazené prostřednictvím centra. Mnoho z nich má nasazené modely v centru, jejichž páry jazyků nejsou ve vlastním překladateli podporované.  Nechcete, aby uživatelé v této situaci neměli žádné žádné další pro překlad jejich obsahu.

Nyní máme proces, který umožňuje nasadit nepodporované modely prostřednictvím vlastního překladatele.  Tento proces vám umožní pokračovat v překladu obsahu pomocí nejnovějšího rozhraní API v3.  Tyto modely budou hostovány, dokud se nerozhodnete je zrušit nebo že se dvojice jazyků bude k dispozici ve vlastním překladateli.  Tento článek vysvětluje proces nasazení modelů s nepodporovanými páry jazyků.

## <a name="prerequisites"></a>Předpoklady

Aby byly vaše modely kandidátem na nasazení, musí splňovat následující kritéria:
* Projekt obsahující model musí být z centra migrován do vlastního překladatele pomocí nástroje pro migraci.  Proces migrace projektů a pracovních prostorů najdete [tady](how-to-migrate.md).
* Model musí být v nasazeném stavu, když dojde k migraci.  
* Dvojice jazyka modelu musí být ve vlastním překladateli nepodporovanou dvojici jazyků.  Páry jazyků, ve kterých je jazyk podporovaný nebo z angličtiny, ale samotná dvojice nezahrnuje angličtinu, jsou kandidátem na Nepodporovaná nasazení jazyků.  Například model rozbočovače pro pár francouzštiny a němčiny se považuje za nepodporovaný pár jazyků, a to i v případě, že je angličtina až angličtina a angličtina až němčina, podporuje dvojice jazyků.

## <a name="process"></a>Proces
Po migraci modelů z centra, které jsou kandidáty na nasazení, je můžete najít tak, že přejdete na stránku **Nastavení** pro váš pracovní prostor a přejdete na konec stránky, kde se zobrazí **Nepodporovaná část školení centra překladatelů** .  Tato část se zobrazí pouze v případě, že máte projekty, které splňují výše zmíněné požadavky.

![Snímek obrazovky, který zvýrazní oddíl nepodporovaného školení centra překladatelů.](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Na stránce s výběrem **nepodporovaného výběru školení centra překladatelů** obsahuje karta **nežadatelé školení** modely, které mají nárok na nasazení.  Vyberte modely, které chcete nasadit, a odešlete žádost.   Před uplynutím 30. dubna můžete vybrat tolik modelů, kolik chcete pro nasazení.
 
![Snímek obrazovky, na které se zobrazuje karta nežádosti o školení](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Po odeslání nebude model na kartě **nevyžádaných** **školení** dostupný a místo toho se zobrazí na kartě požadované školení.  Požadované školení můžete kdykoli zobrazit.

![Postup migrace z centra](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Co dále?

Modely, které jste vybrali pro nasazení, se uloží až po vyřazení centra a všechny modely se odsadí.  Dokud nebudete moct odesílat žádosti o nasazení nepodporovaných modelů, budete muset 24.  Tyto modely budeme nasazovat do 15. června, od tohoto okamžiku budou přístupné prostřednictvím rozhraní API pro překladatele v3.  Kromě toho budou k dispozici prostřednictvím rozhraní v2 API až do 1. července.  

Další informace o důležitých datech v vyřazení centra najdete [tady](https://www.microsoft.com/translator/business/hub/).
Po nasazení budou platit normální poplatky za hostování.  Podrobnosti najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) .  

Na rozdíl od standardních vlastních modelů překladatelů budou modely rozbočovačů dostupné jenom v jedné oblasti, takže poplatky za hostování ve více oblastech se nepoužijí.  Po nasazení budete moct model rozbočovače kdykoli nasadit pomocí migrovaného projektu vlastního překladatele.

## <a name="next-steps"></a>Další kroky

- [Výuka modelu](how-to-train-model.md).
- Začněte používat nasazený vlastní model překladu přes [Microsoft Translator text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
