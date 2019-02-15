---
title: Vytváření koncových bodů webové služby
titleSuffix: Azure Machine Learning Studio
description: Vytváření koncových bodů webové služby ve službě Azure Machine Learning. Každý koncový bod webové služby je nezávisle na sobě zákazníky a vyřešené, omezení a spravované.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/12/2019
ms.openlocfilehash: a6945ac7bfb750916e229ae04376f895f2b3d506
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267264"
---
# <a name="creating-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>Vytváření koncových bodů pro nasazené webové služby Azure Machine Learning Studio

> [!NOTE]
> Toto téma popisuje postupy aplikovatelné **Classic** Machine Learning webové služby.

Po nasazení webové služby je pro tuto službu vytvořen koncový bod. Výchozí koncový bod je možné volat pomocí klíče rozhraní API. Můžete přidat další koncové body s vlastními klíči z portálu webových služeb.
Každý koncový bod webové služby je nezávisle na sobě zákazníky a vyřešené, omezení a spravované. Každý koncový bod je jedinečnou adresu URL pomocí autorizačního klíče, které můžete distribuovat zákazníkům.

## <a name="adding-endpoints-to-a-web-service"></a>Přidání koncových bodů webové služby

Přidání koncového bodu webové služby pomocí portálu Azure Machine Learning Web Services. Po vytvoření koncového bodu můžete využívat přes synchronního rozhraní API, rozhraní API pro dávkové a listy aplikace excel.

> [!NOTE]
> Pokud jste přidali další koncové body webové služby, nelze odstranit výchozí koncový bod.

1. V nástroji Machine Learning Studio v levém navigačním sloupci klikněte na webové služby.
2. V dolní části řídicího panelu webové služby, klikněte na tlačítko **spravovat koncové body**. Na portálu Azure Machine Learning Web Services se otevře na stránce koncové body pro webové služby.
3. Klikněte na možnost **Nové**.
4. Zadejte název a popis pro nový koncový bod. Názvy koncových bodů musí být 24 znaků nebo méně délku a musí být tvořen malá písmena a číslice. Vyberte úroveň protokolování a povolení ukázková data. Další informace o protokolování naleznete v tématu [povolení protokolování pro Machine Learning Web services](web-services-logging.md).

## <a name="next-steps"></a>Další postup

[Jak využívat Azure Machine Learning webové služby](consume-web-services.md).
