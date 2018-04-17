---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: aa513d63a2af0fe994b8ab1ed7335a30998ff8ce
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Scénář
Chcete-li lépe předvedli, jak vytvořit udr, tento dokument používá následující scénář:

![POPISEK OBRÁZKU](./media/virtual-network-create-udr-scenario-include/figure1.png)

V tomto scénáři vytvoříte jeden UDR pro *Front-end podsíť* a jiné UDR pro *podsítě Back-end*, a to takto: 

* **UDR front-endu**. Front-endu UDR se použije na *front-endu* podsítě a obsahovat jeden postup:    
  * **RouteToBackend**. Tato trasa back-end podsítě, která se odesílá veškerý provoz **FW1** virtuálního počítače.
* **UDR back-end**. Back-end UDR se použije na *back-end* podsítě a obsahovat jeden postup:    
  * **RouteToFrontend**. Tato trasa front-end podsítě, která se odesílá veškerý provoz **FW1** virtuálního počítače.

Kombinace tyto trasy zajišťuje, že se veškerý provoz, jehož z jedné podsítě do jiné směruje na **FW1** virtuální počítač, který je používán jako virtuální zařízení. Musíte také zapnout předávání protokolu IP pro **FW1** virtuální počítač, aby mohl přijímat přenosy určené do ostatních virtuálních počítačů.

