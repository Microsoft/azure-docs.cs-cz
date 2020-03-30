---
title: Vytvoření koncových bodů webové služby
titleSuffix: ML Studio (classic) - Azure
description: Vytvořte koncové body webové služby v Azure Machine Learning Studio (klasické). Každý koncový bod ve webové službě je nezávisle adresován, omezen a spravován.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 980ed1e54de30ec8a2dc0c1fdac6546d31f48a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218206"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Vytvoření koncových bodů pro nasazené webové služby Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> Toto téma popisuje techniky použitelné pro webovou službu **Classic** Machine Learning.

Po nasazení webové služby je pro tuto službu vytvořen koncový bod. Výchozí koncový bod je možné volat pomocí klíče rozhraní API. Můžete přidat další koncové body s vlastními klíči z portálu webových služeb.
Každý koncový bod ve webové službě je nezávisle adresován, omezen a spravován. Každý koncový bod je jedinečná adresa URL s autorizačním klíčem, který můžete distribuovat zákazníkům.

## <a name="add-endpoints-to-a-web-service"></a>Přidání koncových bodů do webové služby

Koncový bod webové služby můžete přidat pomocí portálu Azure Machine Learning Web Services. Po vytvoření koncového bodu jej můžete spotřebovat prostřednictvím synchronních api, dávkových api a listů aplikace Excel.

> [!NOTE]
> Pokud jste do webové služby přidali další koncové body, nelze odstranit výchozí koncový bod.

1. V Machine Learning Studio (klasické) v levém navigačním sloupci klikněte na Webové služby.
2. V dolní části řídicího panelu webové služby klikněte na **spravovat koncové body**. Portál Azure Machine Learning Web Services se otevře na stránce koncových bodů pro webovou službu.
3. Klepněte na tlačítko **Nový**.
4. Zadejte název a popis nového koncového bodu. Názvy koncových bodů musí mít délku 24 znaků nebo méně a musí být tvořeny nižšími písmeny nebo čísly. Vyberte úroveň protokolování a zda jsou povolena ukázková data. Další informace o protokolování naleznete v [tématu Enable logging for Machine Learning web services](web-services-logging.md).

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a>Škálování webové služby přidáním dalších koncových bodů

Ve výchozím nastavení je každá publikovaná webová služba nakonfigurována tak, aby podporovala 20 souběžných požadavků a může být až 200 souběžných požadavků. Azure Machine Learning Studio (classic) automaticky optimalizuje nastavení tak, aby poskytovalo nejlepší výkon pro vaši webovou službu a hodnota portálu je ignorována.

Pokud máte v plánu volat rozhraní API s vyšším zatížením než maximální hodnota souběžných volání 200 bude podporovat, měli byste vytvořit více koncových bodů ve stejné webové službě. Potom můžete náhodně distribuovat zatížení mezi všechny z nich.

Škálování webové služby je běžný úkol. Některé důvody škálování jsou pro podporu více než 200 souběžných požadavků, zvýšení dostupnosti prostřednictvím více koncových bodů nebo poskytnout samostatné koncové body pro webovou službu. Škálování můžete zvýšit přidáním dalších koncových bodů pro stejnou webovou službu prostřednictvím portálu [Azure Machine Learning Web Service.](https://services.azureml.net/)

Mějte na paměti, že použití vysoké souběžnosti počet může být škodlivé, pokud nejste volání rozhraní API s odpovídajícím způsobem vysokou sazbou. Může se zobrazit sporadické časové osy nebo špičky v latenci, pokud vložíte relativně nízké zatížení na rozhraní API nakonfigurované pro vysoké zatížení.

Synchronní api se obvykle používají v situacích, kde je požadována nízká latence. Latence zde znamená čas potřebný pro rozhraní API k dokončení jednoho požadavku a nezohledňuje žádné zpoždění sítě. Řekněme, že máte rozhraní API s latencí 50 ms. Chcete-li plně využívat dostupnou kapacitu s úrovní omezení Vysoké a Maximální souběžné volání = 20, musíte volat toto rozhraní API 20 * 1000 / 50 = 400 krát za sekundu. Rozšíření tohoto dalšího, Maximální souběžná volání 200 umožňuje volat rozhraní API 4000 krát za sekundu, za předpokladu, že 50 ms latence.

## <a name="next-steps"></a>Další kroky

[Jak využívat webovou službu Azure Machine Learning](consume-web-services.md).
