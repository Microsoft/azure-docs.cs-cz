---
title: Převzetí služeb při selhání a škálování aplikace kontejnerů
description: V tomto kurzu se dozvíte, jak se v aplikaci Azure Service Fabric typu kontejner zpracovává převzetí služeb při selhání.  Také se naučíte škálovat kontejnery a služby spuštěné v clusteru.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: f51a195f9ad43c7facb7dc413a4da83fc6b202b7
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531390"
---
# <a name="tutorial-demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Kurz: Ukázka převzetí služeb při selhání a škálování služeb kontejneru pomocí Service Fabric

Tento kurz je třetí částí série. V tomto kurzu se dozvíte, jak se v aplikacích Service Fabric typu kontejner zpracovává převzetí služeb při selhání. Kromě toho se naučíte škálovat kontejnery. V tomto kurzu jste:

> [!div class="checklist"]
> * Seznámili jste se s převzetím služeb při selhání kontejneru v clusteru Service Fabric.
> * Škálovali jste v aplikaci kontejnery webového front-endu.

## <a name="prerequisites"></a>Předpoklady

Aplikace z [části 2](service-fabric-tutorial-package-containers.md) je spuštěná na aktivním clusteru Service Fabric.

## <a name="fail-over-a-container-in-a-cluster"></a>Převzetí služeb při selhání kontejneru v clusteru

Service Fabric zajišťuje v případě selhání automatický přesun instancí kontejneru do jiných uzlů clusteru. Kontejnery můžete z uzlu také ručně vyprázdnit a řádně je přesunout do jiných uzlů v clusteru. Služby můžete škálovat několika způsoby, v tomto příkladu používáme Service Fabric Explorer.

Pokud chcete převzít služby při selhání front-end kontejneru, proveďte následující kroky:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Ve stromovém zobrazení klikněte na uzel **fabric:/TestContainer/azurevotefront** a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID). Všimněte si názvu uzlu ve stromovém zobrazení, které zobrazuje uzly, na kterých je kontejner právě spuštěný – například `_nodetype_1`.
3. Ve stromovém zobrazení rozbalte uzel **Uzly**. Klikněte na tři tečky vedle uzlu, na kterém je kontejner spuštěný.
4. Pokud chcete tento uzel restartovat, zvolte **Restartovat** a potvrďte akci restartování. Restartování způsobí převzetí služeb při selhání kontejneru do jiného uzlu v clusteru.

![noderestart][noderestart]

Všimněte si, jak se název uzlu (který značí, kde jsou spuštěné kontejnery front-endu) změní na jiný uzel v clusteru. Po chvíli byste opět měli mít k aplikaci přístup. Uvidíte, že je teď spuštěná na jiném uzlu.

## <a name="scale-containers-and-services-in-a-cluster"></a>Škálování kontejnerů a služeb v clusteru

Kontejnery Service Fabric je možné škálovat napříč clusterem a vyřešit tak zatížení služeb. Kontejnery se škálují změnou počtu instancí spuštěných v clusteru.

Pokud chcete škálovat webový front-end, proveďte následující kroky:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Ve stromovém zobrazení klikněte na tři tečky vedle uzlu **fabric:/TestContainer/azurevotefront** a zvolte **Škálovat službu**.

![sfxscale][sfxscale]

Nyní můžete škálovat počet instancí webového front-endu.

1. Změňte počet na **2** a klikněte na **Škálovat službu**.
1. Ve stromovém zobrazení klikněte na uzel **fabric:/TestContainer/azurevotefront** a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID).

![sfxscaledone][sfxscaledone]

Teď je vidět, že má služba dvě instance. Ve stromovém zobrazení vidíte, na kterých uzlech jsou instance spuštěné.

Touto jednoduchou úlohou správy jsme zdvojnásobili prostředky, které má naše front-end služba k dispozici pro zpracování uživatelské zátěže. Je důležité si uvědomit, že pro spolehlivý provoz služby nepotřebujete více jejích instancí. Pokud služba selže, Service Fabric zajistí v clusteru spuštění nové instance služby.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jsme předvedli převzetí služeb při selhání kontejneru a také škálování aplikace. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Seznámili jste se s převzetím služeb při selhání kontejneru v clusteru Service Fabric.
> * Škálovali jste v aplikaci kontejnery webového front-endu.

V této sérii kurzů jste se naučili:
> [!div class="checklist"]
> * Vytváření imagí kontejneru
> * Nahrávání imagí kontejneru do služby Azure Container Registry
> * Balení kontejnerů pro Service Fabric pomocí Yeomanu
> * Sestavení a spuštění aplikace Service Fabric s kontejnery
> * Jak se zpracovává převzetí služeb při selhání a škálování v Service Fabric

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png