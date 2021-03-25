---
title: Požádat o autorizační klíč pro ExpressRoute
description: Postup pro vyžádání autorizačního klíče pro ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 99d9fba33d64fca1d9c5b960041fbabe1f9060db
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026969"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. V Azure Portal v části **připojení** na kartě **ExpressRoute** vyberte **+ požádat o autorizační klíč**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Snímek obrazovky ukazující, jak si vyžádat autorizační klíč ExpressRoute" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Zadejte název a vyberte **vytvořit**. 
      
   Vytvoření klíče může trvat přibližně 30 sekund. Po vytvoření se nový klíč zobrazí v seznamu autorizačních klíčů pro privátní cloud.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Snímek obrazovky s ExpressRoute Global Reach autorizační klíč":::
  
1. Zkopírujte autorizační klíč a ID ExpressRoute. Použijete je k dokončení partnerského vztahu.  

   > [!NOTE]
   > Autorizační klíč zmizí po nějaké době, takže ho zkopírujte hned po jeho zobrazení.