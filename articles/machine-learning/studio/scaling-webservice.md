---
title: Jak zvýšit souběžnost webové služby Azure Machine Learning | Microsoft Docs
description: Zjistěte, jak zvýšit souběžnost webové služby Azure Machine Learning přidáním další koncové body.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
keywords: Azure machine learningu, webové služby, operationalization, škálování, koncový bod, souběžnosti
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.openlocfilehash: 946e13c42213090fff0cd8648687ee523af51c42
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>Škálování webové služby Azure Machine Learning přidáním další koncové body
> [!NOTE]
> Toto téma popisuje techniky pro **Classic** Machine Learning webové služby. 
> 
> 

Ve výchozím nastavení každý publikované webové služby je nakonfigurován pro podporu 20 souběžnými požadavky a může být až 200 souběžných požadavků. Azure Machine Learning automaticky optimalizuje nastavení pro poskytovat nejlepší výkon pro webové služby a portálu hodnota je ignorována. 

Pokud bude podporovat plán pro volání rozhraní API se zatížením vyšší než maximální počet souběžných volání hodnota 200, měli byste vytvořit několik koncových bodů na stejné webové službě. Potom můžete náhodně distribuovat zatížení napříč všemi z nich.

Škálování webové služby je běžné úlohy. Některé z důvodů škálování jsou pro podporu více než 200 souběžných požadavků, zvýšit dostupnost prostřednictvím několik koncových bodů nebo poskytovat samostatný koncové body pro webovou službu. Měřítko můžete zvýšit tak, že přidáte další koncové body pro stejné webové služby pomocí [webovou službu Azure Machine Learning](https://services.azureml.net/) portálu.

Další informace o přidání nové koncové body, najdete v části [vytváření koncových bodů](create-endpoint.md).

Mějte na paměti, který používá souběžnosti vysoký počet může být škodlivé, pokud nejsou volání rozhraní API s odpovídajícím způsobem vysokou míru. Můžete se setkat ojediněle vypršení časových limitů nebo špičky v latenci, když vložíte relativně nízký zatížení na rozhraní API nakonfigurovaný pro vysokého zatížení.

V situacích, kde je žádoucí s nízkou latencí jsou obvykle používány synchronní rozhraní API. Latence zde znamená doba potřebná pro rozhraní API pro jeden požadavek na dokončení a nemá účet pro všechny zpoždění sítě. Řekněme, že máte rozhraní API s latencí 50 ms. Chcete-li plně využívat s omezení úrovní vysoce dostupné kapacity a maximální počet souběžných volání = 20, je třeba volat toto rozhraní API 20 * 1000 / 50 = 400 times za sekundu. Tato další rozšíření, maximální počet souběžných volání 200 vám umožní volat rozhraní API 4000 časy za sekundu, za předpokladu, že latence 50-ms.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
