---
title: Stav možnosti úložiště v Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Přečtěte si o spolehlivé ukládání stavu v Service Fabric mřížky aplikace běžící na Azure Service Fabric mřížky.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 6aa268cf56bfb8be9c27a9e0d9e5c9f4464b0c9d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076084"
---
# <a name="state-management-with-service-fabric"></a>Správa stavu s využitím Service Fabric
Service Fabric podporuje spoustu různých možností pro úložiště stavu. Koncepční přehled vzorce správy stavu a Service Fabric najdete v části [koncepty služby Service Fabric: stav](/azure/service-fabric/service-fabric-concepts-state). Všechny tyto stejné koncepty platí, jestli vaše služby běžet uvnitř nebo vně sítě pro Service Fabric. 

## <a name="state-storage-options-in-azure-service-fabric-mesh"></a>Možnosti stavu úložiště v Azure Service Fabric mřížky
Pomocí Service Fabric sítě můžete snadno nasadit novou aplikaci a připojte ho k existujícímu úložišti dat hostovaném v Azure. Kromě použití libovolné vzdálené databázi, existuje několik možností pro ukládání dat, v závislosti na tom, jestli služba chce místní nebo vzdálené úložiště. 

* Místně uložené replikovaných dat
  * Reliable Collections (není k dispozici ve verzi preview)
    * Knihovnu, která implementuje datové struktury, jako jsou fronty a páry klíč hodnota pro použití ve službě
    * Díky tomu nejjednodušší a nejrychlejší způsob, jak pracovat s daty, současně snadno oddíl směrování v kombinaci s inteligentního směrování v Service Fabric mřížky
  * Service Fabric svazku ovladače (není k dispozici ve verzi preview)
    * Ovladač dockeru svazek připojit místní svazek do kontejneru
    * To umožňuje maximální flexibilitu při ukládání dat místně, prostřednictvím libovolného rozhraní API, která podporuje úložiště file.

* Vzdálené úložiště
  * Ovladač Azure svazku souborů
    * Ovladač dockeru svazku pro připojení sdílené složky služby soubory Azure do kontejneru
    * Nabízí méně výkonné, ale možnost levnější úplně také flexibilní a spolehlivé dat prostřednictvím jakékoliv rozhraní API, která podporuje úložiště file.
    * [Postupy: Průvodce: nasazení aplikace pomocí Azure Files svazku](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)
    
## <a name="next-steps"></a>Další postup

Informace o modelu aplikací najdete v tématu [prostředky Service Fabric](service-fabric-mesh-service-fabric-resources.md)
