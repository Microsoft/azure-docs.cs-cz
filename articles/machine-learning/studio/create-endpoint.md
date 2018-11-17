---
title: Vytváření koncových bodů webové služby v Machine Learning | Dokumentace Microsoftu
description: Vytváření koncových bodů webové služby ve službě Azure Machine Learning
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.custom: (previous ms.author yahajiza)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.component: studio
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.openlocfilehash: 45115f0a987093d7e3816891fa5f4c242b449fb7
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51819232"
---
# <a name="creating-endpoints"></a>Vytváření koncových bodů
> [!NOTE]
>  Toto téma popisuje postupy aplikovatelné **Classic** Machine Learning webové služby.
> 
> 

Při vytváření webové služby, které prodáváte blíž k vašim zákazníkům musíte poskytovat trénované modely pro jednotlivé zákazníky, které jsou stále spojeny do experimentu, ze kterého byla vytvořena webová služba. Kromě toho všechny aktualizace do experimentu bude použito selektivní do koncového bodu bez přepsání přizpůsobení.

K tomu Azure Machine Learning umožňuje vytvořit několik koncových bodů pro nasazenou webovou službu. Každý koncový bod webové služby je nezávisle na sobě zákazníky a vyřešené, omezení a spravované. Každý koncový bod je jedinečnou adresu URL a autorizačního klíče, které můžete distribuovat zákazníkům.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Přidání koncových bodů webové služby
Existují dva způsoby, jak přidat koncový bod webové služby.

* Prostřednictvím kódu programu
* Na portálu Azure Machine Learning Web Services

Po vytvoření koncového bodu můžete využívat přes synchronního rozhraní API, rozhraní API pro dávkové a listy aplikace excel. Kromě přidání koncových bodů prostřednictvím tohoto uživatelského rozhraní, můžete také použít rozhraní API pro správu koncový bod programově přidat koncové body.

> [!NOTE]
> Pokud jste přidali další koncové body webové služby, nelze odstranit výchozí koncový bod.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Přidání koncového bodu prostřednictvím kódu programu
Můžete přidat koncový bod webové služby prostřednictvím kódu programu pomocí [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) ukázkový kód.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Přidání koncového bodu pomocí portálu Azure Machine Learning Web Services
1. V nástroji Machine Learning Studio v levém navigačním sloupci klikněte na webové služby.
2. V dolní části řídicího panelu webové služby, klikněte na tlačítko **spravovat koncové body**. Na portálu Azure Machine Learning Web Services se otevře na stránce koncové body pro webové služby.
3. Klikněte na možnost **Nové**.
4. Zadejte název a popis pro nový koncový bod. Názvy koncových bodů musí být 24 znaků nebo méně délku a musí být tvořen malá písmena a číslice. Vyberte úroveň protokolování a povolení ukázková data. Další informace o protokolování naleznete v tématu [povolení protokolování pro Machine Learning Web services](web-services-logging.md).

## <a name="next-steps"></a>Další postup
[Jak využívat Azure Machine Learning webové služby](consume-web-services.md).

