---
title: Požádat o autorizační klíč pro ExpressRoute
description: Postup pro vyžádání autorizačního klíče pro ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 54a610c8b0f3f3fe9d3ebe39291bba7767007839
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491828"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. V Azure Portal v části **připojení** na kartě **ExpressRoute** vyberte **+ požádat o autorizační klíč**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Snímek obrazovky ukazující, jak si vyžádat autorizační klíč ExpressRoute" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Zadejte název a vyberte **vytvořit**. 
      
   Vytvoření klíče může trvat přibližně 30 sekund. Po vytvoření se nový klíč zobrazí v seznamu autorizačních klíčů pro privátní cloud.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Snímek obrazovky s ExpressRoute Global Reach autorizační klíč":::
  
1. Poznamenejte si autorizační klíč a ID ExpressRoute. Použijete je k dokončení partnerského vztahu.  

   > [!NOTE]
   > Autorizační klíč zmizí po nějaké době, takže ho zkopírujte hned po jeho zobrazení.