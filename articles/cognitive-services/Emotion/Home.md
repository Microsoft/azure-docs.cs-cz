---
title: Přehled rozpoznávání emocí úrovně rozhraní API | Microsoft Docs
description: Algoritmus Microsoft nejmodernější, založené na cloudu rozpoznávání emocí úrovně rozpoznávání použijte k tvorbě více přizpůsobené aplikací, s rozhraním API pro rozpoznávání emocí úrovně v kognitivní služby.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 8383370cba3f78060e809f444f4ad3dab7380f4e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342758"
---
# <a name="what-is-emotion-api"></a>Co je rozhraní API pro rozpoznávání emocí úrovně?

> [!IMPORTANT]
> Rozpoznávání emocí úrovně rozhraní API se považovat za zastaralou na 30 říjen 2017. Funkce je nyní součástí [vzhled API](https://docs.microsoft.com/en-us/azure/cognitive-services/face/).

Vítá vás rozpoznávání emocí úrovně rozhraní API společnosti Microsoft, které umožňuje vytvářet více přizpůsobené aplikace s algoritmem rozpoznávání rozpoznávání emocí úrovně založené na cloudu společnosti Microsoft.

### <a name="emotion-recognition"></a>Rozpoznávání rozpoznávání emocí úrovně

Na používání beta verze rozhraní API pro rozpoznávání emocí úrovně trvá bitovou kopii jako vstup a vrátí důvěru mezi sadu emoce pro každý řez v bitové kopii, jakož i ohraničující pole pro tučné z rozhraní API řez. Emoce zjistil jsou štěstí, sadness, neočekávaném, anger, obavy, cestou, působící nebo neutrální. Tyto emoce se předávají ve všech jazykových verzích a všeobecně prostřednictvím stejné základní obličeje výrazy, kde jsou určeny rozhraní API pro rozpoznávání emocí úrovně. 

**Interpretace výsledků:** 

Při interpretaci výsledky z rozhraní API pro rozpoznávání emocí úrovně, rozpoznávání emocí úrovně zjistil by měl interpretovat jako rozpoznávání emocí úrovně s nejvyšší skóre, jako jsou normalized skóre mají sečíst na jedno. Uživatelé mohou určit prahovou hodnotu vyšší spolehlivosti v rámci své aplikace, v závislosti na jejich potřeb. 

Další informace o emocí najdete v tématu referenční dokumentace rozhraní API: 
  * Základní: Pokud je uživatel již volána rozhraní API řez, můžou odeslání vzhled rámečku jako vstup a používat základní vrstvě. [Referenční materiály k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standardní: Uživatel nebudou odeslány vzhled rámečku, se musí používat standardní režim.  [Referenční materiály k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Příklad, jak číst streamování videa s rozhraním API pro rozpoznávání emocí úrovně, najdete v části [jak analyzovat videa v reálném čase](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
