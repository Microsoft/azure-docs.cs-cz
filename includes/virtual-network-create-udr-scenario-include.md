---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170877"
---
## <a name="scenario"></a>Scénář
Abychom vám lépe předvedli vytvoření trasy definované uživatelem, tento dokument používá následující scénář:

![POPISEK OBRÁZKU](./media/virtual-network-create-udr-scenario-include/figure1.png)

V tomto scénáři vytvoříte jeden uživatelem definovaná TRASA pro *front-endové podsítě* a jiné uživatelem definovaná TRASA pro *Back endové podsítě*, následujícím způsobem: 

* **Uživatelem definovaná TRASA front-endu**. Front-endu UDR se použije na *front-endu* podsítě a obsahují tras:    
  * **RouteToBackend**. Tato trasa odesílá veškerý provoz do back endové podsítě **FW1** virtuálního počítače.
* **Uživatelem definovaná TRASA back-endu**. Uživatelem definovaná TRASA back-end se použije pro *back-endu* podsítě a obsahují tras:    
  * **RouteToFrontend**. Tato trasa odesílá veškerý provoz do front-endové podsítě **FW1** virtuálního počítače.

Kombinací tyto trasy se zajistí, že veškerý provoz směřující z jedné podsítě do druhé prochází k **FW1** virtuální počítač, který je používán jako virtuální zařízení. Musíte také zapnout pro předávání IP **FW1** virtuálního počítače, aby může přijímat provoz směřující do ostatních virtuálních počítačů.

