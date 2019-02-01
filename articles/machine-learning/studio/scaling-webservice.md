---
title: Škálování webové služby Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Zjistěte, jak zvýšit souběžnost webové služby Azure Machine Learning Studio tak, že přidáte další koncové body.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/23/2017
ms.openlocfilehash: c5874029e17b0ad7c9787beb0177b8211cbf6e6b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512070"
---
# <a name="scaling-an-azure-machine-learning-studio-web-service-by-adding-additional-endpoints"></a>Škálování webové služby Azure Machine Learning Studio tak, že přidáte další koncové body
> [!NOTE]
> Toto téma popisuje postupy aplikovatelné **Classic** webové služby Machine Learning. 
> 
> 

Ve výchozím nastavení každou publikovanou webovou službu je nakonfigurována pro podporu 20 souběžných požadavků a může být až 200 souběžných požadavků. Azure Machine Learning se automaticky optimalizuje nastavení můžete poskytovat nejlepší výkon pro webové služby a portálu hodnota je ignorována. 

Pokud bude podporovat plán pro volání rozhraní API se zatížením vyšší než hodnota maximálního počtu současných volání 200, měli byste vytvořit několik koncových bodů na stejné webové služby. Potom můžete náhodně distribuovat zatížení napříč jimi.

Škálování webové služby je běžný úkol. Jsou některé důvody pro škálování podporovat více než 200 souběžných požadavků, zvyšují dostupnost prostřednictvím více koncových bodů nebo poskytovat samostatný koncových bodů webové služby. Škálování můžete zvýšit tak, že přidáte další koncové body pro službu Web prostřednictvím [webové služby Azure Machine Learning](https://services.azureml.net/) portálu.

Další informace o přidání nové koncové body, naleznete v tématu [vytváření koncových bodů](create-endpoint.md).

Nezapomínejte, že pomocí souběžnosti vysoký počet může být škodlivé, pokud nejsou volání rozhraní API s odpovídajícím způsobem vysoký. Sporadické vypršení časových limitů a/nebo provozní špičky může zobrazit v latenci, když vložíte relativně nízký zatížení na rozhraní API nakonfigurovaný pro vysokého zatížení.

Synchronní rozhraní API se obvykle používá v situacích, kdy je žádoucí s nízkou latencí. Tady latence znamená doba potřebná pro rozhraní API pro jeden požadavek na dokončení a nebude účet pro žádné zpoždění v síti. Řekněme, že máte rozhraní API s latencí 50 ms. Chcete-li plnohodnotně pracovat s úroveň omezování prostředků vysoce dostupnou kapacitu a maximálního počtu současných volání = 20, potřebné k volání tohoto rozhraní API 20 * 1 000 / 50 = 400 vyprší za sekundu. Tato další rozšíření, maximálního počtu současných volání 200 umožňuje volat časy 4000 rozhraní API za sekundu, za předpokladu, že latence 50 ms.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
