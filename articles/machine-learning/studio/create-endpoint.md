---
title: Vytvoření koncových bodů webové služby
titleSuffix: ML Studio (classic) - Azure
description: Vytvoření koncových bodů webové služby v Azure Machine Learning Studio (Classic). Každý koncový bod ve webové službě se nezávisle zabývá, omezuje a spravuje.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 57bc5739d8e6b1fa30482de69285c8895294cfa8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454760"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Vytvoření koncových bodů pro nasazené webové služby Azure Machine Learning Studio (Classic)

> [!NOTE]
> Toto téma popisuje techniky, které se vztahují na **klasický** Machine Learning webovou službu.

Po nasazení webové služby je pro tuto službu vytvořen koncový bod. Výchozí koncový bod je možné volat pomocí klíče rozhraní API. Můžete přidat další koncové body s vlastními klíči z portálu webové služby.
Každý koncový bod ve webové službě se nezávisle zabývá, omezuje a spravuje. Každý koncový bod je jedinečná adresa URL s autorizačním klíčem, který můžete distribuovat vašim zákazníkům.

## <a name="add-endpoints-to-a-web-service"></a>Přidání koncových bodů do webové služby

Koncový bod můžete k webové službě přidat pomocí portálu Azure Machine Learning Web Services. Po vytvoření koncového bodu můžete využívat přes synchronního rozhraní API, rozhraní API pro dávkové a listy aplikace excel.

> [!NOTE]
> Pokud jste do webové služby přidali další koncové body, nemůžete výchozí koncový bod odstranit.

1. V Machine Learning Studio (Classic) v levém navigačním sloupci klikněte na webové služby.
2. V dolní části řídicího panelu webové služby klikněte na možnost **Spravovat koncové body**. Portál Azure Machine Learning webové služby se otevře na stránce koncové body webové služby.
3. Klikněte na možnost **Nové**.
4. Zadejte název a popis pro nový koncový bod. Názvy koncových bodů musí být 24 znaků nebo méně délku a musí být tvořen malá písmena a číslice. Vyberte úroveň protokolování a povolení ukázková data. Další informace o protokolování najdete v tématu [Povolení protokolování pro Machine Learning webové služby](web-services-logging.md).

## <a id="scaling"></a>Škálování webové služby přidáním dalších koncových bodů

Ve výchozím nastavení je každá publikovaná webová služba nakonfigurovaná tak, aby podporovala 20 souběžných požadavků a může být stejně vysoká jako 200 souběžných požadavků. Azure Machine Learning Studio (Classic) automaticky optimalizuje nastavení tak, aby poskytovalo nejlepší výkon webové služby, a hodnota portálu se ignoruje.

Pokud máte v úmyslu volat rozhraní API s vyšší zátěží, než je maximální počet souběžných volání 200, bude třeba vytvořit více koncových bodů ve stejné webové službě. Potom můžete zatížení náhodně rozmístit napříč všemi nimi.

Škálování webové služby je běžný úkol. Důvodem pro škálování je podpora více než 200 souběžných požadavků, zvýšení dostupnosti prostřednictvím více koncových bodů nebo poskytnutí samostatných koncových bodů pro webovou službu. Měřítko můžete zvětšit přidáním dalších koncových bodů pro stejnou webovou službu prostřednictvím portálu [Azure Machine Learning webové služby](https://services.azureml.net/) .

Mějte na paměti, že použití vysoké souběžnosti může být škodlivé, pokud nevoláte rozhraní API s odpovídající vysokou mírou. Pokud zadáte relativně nízké zatížení rozhraní API, které je nakonfigurováno pro vysoké zatížení, může se zobrazit občasná prodleva nebo špičky v latenci.

Synchronní rozhraní API se obvykle používají v situacích, kdy je potřeba nízká latence. Latence tady znamená dobu, kterou rozhraní API potřebuje k dokončení jedné žádosti, a nebere v úvahu žádné zpoždění v síti. Řekněme, že máte rozhraní API s latencí 50-ms. Aby bylo možné plně využít dostupnou kapacitu s vysokým a maximálním počtem souběžných volání = 20, je nutné zavolat toto rozhraní API 20 * 1000/50 = 400 krát za sekundu. Další rozšíření: maximální počet souběžných volání 200 umožňuje volat rozhraní API 4000 za sekundu za předpokladu, že je latence 50-ms.

## <a name="next-steps"></a>Další kroky

[Jak využívat Azure Machine Learning webovou službu](consume-web-services.md).
