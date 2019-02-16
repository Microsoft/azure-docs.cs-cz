---
title: Vytvoření webového koncové body služby
titleSuffix: Azure Machine Learning Studio
description: Vytvoření webových koncových bodů služby ve službě Azure Machine Learning. Každý koncový bod webové služby je nezávisle na sobě zákazníky a vyřešené, omezení a spravované.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 6759a94a1d1491532e85d1bd1e1a098b2f92ff9e
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329288"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>Vytváření koncových bodů pro nasazené webové služby Azure Machine Learning Studio

> [!NOTE]
> Toto téma popisuje postupy aplikovatelné **Classic** webové služby Machine Learning.

Po nasazení webové služby je pro tuto službu vytvořen koncový bod. Výchozí koncový bod je možné volat pomocí klíče rozhraní API. Můžete přidat další koncové body s vlastními klíči z portálu webových služeb.
Každý koncový bod webové služby je nezávisle na sobě zákazníky a vyřešené, omezení a spravované. Každý koncový bod je jedinečnou adresu URL pomocí autorizačního klíče, které můžete distribuovat zákazníkům.

## <a name="add-endpoints-to-a-web-service"></a>Přidání koncových bodů webové služby

Přidání koncového bodu webové služby pomocí portálu Azure Machine Learning Web Services. Po vytvoření koncového bodu můžete využívat přes synchronního rozhraní API, rozhraní API pro dávkové a listy aplikace excel.

> [!NOTE]
> Pokud jste přidali další koncové body webové služby, nelze odstranit výchozí koncový bod.

1. V nástroji Machine Learning Studio v levém navigačním sloupci klikněte na webové služby.
2. V dolní části řídicího panelu webové služby, klikněte na tlačítko **spravovat koncové body**. Na portálu Azure Machine Learning Web Services se otevře na stránce koncové body pro webové služby.
3. Klikněte na možnost **Nové**.
4. Zadejte název a popis pro nový koncový bod. Názvy koncových bodů musí být 24 znaků nebo méně délku a musí být tvořen malá písmena a číslice. Vyberte úroveň protokolování a povolení ukázková data. Další informace o protokolování naleznete v tématu [povolení protokolování pro webové služby Machine Learning](web-services-logging.md).

## <a id="scaling"></a> Škálování webové služby tak, že přidáte další koncové body

Ve výchozím nastavení každé publikované webové služby je nakonfigurována pro podporu 20 souběžných požadavků a může být až 200 souběžných požadavků. Azure Machine Learning Studio se automaticky optimalizuje nastavení můžete poskytovat nejlepší výkon pro webové služby a portálu hodnota je ignorována.

Pokud bude podporovat plán pro volání rozhraní API se zatížením vyšší než hodnota maximálního počtu současných volání 200, měli byste vytvořit několik koncových bodů na stejné webové služby. Potom můžete náhodně distribuovat zatížení napříč jimi.

Škálování webové služby je běžný úkol. Jsou některé důvody pro škálování podporovat více než 200 souběžných požadavků, zvyšují dostupnost prostřednictvím více koncových bodů nebo poskytovat samostatný koncových bodů webové služby. Škálování můžete zvýšit tak, že přidáte další koncové body pro službu web prostřednictvím [webové služby Azure Machine Learning](https://services.azureml.net/) portálu.

Nezapomínejte, že pomocí souběžnosti vysoký počet může být škodlivé, pokud nejsou volání rozhraní API s odpovídajícím způsobem vysoký. Sporadické vypršení časových limitů a/nebo provozní špičky může zobrazit v latenci, když vložíte relativně nízký zatížení na rozhraní API nakonfigurovaný pro vysokého zatížení.

Synchronní rozhraní API se obvykle používá v situacích, kdy je žádoucí s nízkou latencí. Tady latence znamená doba potřebná pro rozhraní API pro jeden požadavek na dokončení a nebude účet pro žádné zpoždění v síti. Řekněme, že máte rozhraní API s latencí 50 ms. Chcete-li plnohodnotně pracovat s úroveň omezování prostředků vysoce dostupnou kapacitu a maximálního počtu současných volání = 20, potřebné k volání tohoto rozhraní API 20 * 1 000 / 50 = 400 vyprší za sekundu. Tato další rozšíření, maximálního počtu současných volání 200 umožňuje volat časy 4000 rozhraní API za sekundu, za předpokladu, že latence 50 ms.

## <a name="next-steps"></a>Další postup

[Způsob využívání webové služby Azure Machine Learning](consume-web-services.md).
