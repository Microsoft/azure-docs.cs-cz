---
title: Zobrazit protokoly kontejnerů v Azure Service Fabric | Microsoft Docs
description: Popisuje, jak zobrazit protokoly kontejner pro spuštěné služby kontejneru Service Fabric pomocí Service Fabric Exploreru.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Zobrazit protokoly pro službu kontejneru Service Fabric
Azure Service Fabric je kontejner orchestrator a podporuje obě [Linux a Windows kontejnery](service-fabric-containers-overview.md).  Tento článek popisuje, jak zobrazit protokoly kontejneru spuštěné služby kontejneru, aby mohli diagnostice a řešení problémů.

## <a name="access-container-logs"></a>Přístup k protokolům kontejneru
Kontejner protokoly lze přistupovat pomocí [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Ve webovém prohlížeči, otevřete Service Fabric Explorer z koncového bodu správy clusteru tak, že přejdete na [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Kontejner protokoly jsou umístěny na uzlu clusteru, který běží instance služby kontejneru na. Jako příklad získání protokolů kontejneru front-endu webové služby [Linux hlasování ukázkovou aplikaci](service-fabric-quickstart-containers-linux.md). Ve stromovém zobrazení, rozbalte položku **clusteru**>**aplikace**>**VotingType**>**fabric: / Voting / azurevotefront**.  Potom rozbalte oddíl (d1aa737e-f22a-e347-be16-eec90be24bc1, v tomto příkladu) a zda kontejner je spuštěn na uzlu clusteru *_lnxvm_0*.

Ve stromovém zobrazení najít balíček kódu na *_lnxvm_0* uzlu rozšířením **uzly**>**_lnxvm_0**>**fabric: / hlasování**  > **azurevotfrontPkg**>**kód balíčky**>**kód**.  Vyberte **kontejneru protokoly** možnosti se zobrazí v protokolech kontejneru.

![Platforma Service Fabric][Image1]


## <a name="next-steps"></a>Další postup
- Fungovat prostřednictvím [vytvořit kurz Linux kontejneru aplikace](service-fabric-tutorial-create-container-images.md).
- Další informace o [Service Fabric a kontejnery](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
