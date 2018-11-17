---
title: Postup pro zlepšení souběžnosti webové služby Azure Machine Learning | Dokumentace Microsoftu
description: Zjistěte, jak zvýšit souběžnost webové služby Azure Machine Learning tak, že přidáte další koncové body.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.custom: (previous ms.author yahajiza)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
keywords: Azure, webové služby machine learning, operacionalizace, škálování, koncový bod, souběžnosti
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.component: studio
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.openlocfilehash: f0b639d27dd5114c47bd5a1cfa0f6a72a6d78d83
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824179"
---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>Škálování webové služby Azure Machine Learning tak, že přidáte další koncové body
> [!NOTE]
> Toto téma popisuje postupy aplikovatelné **Classic** Machine Learning webové služby. 
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
