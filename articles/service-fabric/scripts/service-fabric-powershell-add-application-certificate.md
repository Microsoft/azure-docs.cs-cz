---
title: Ukázkový skript Azure PowerShellu – Přidání certifikátu aplikace do clusteru | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Přidání certifikátu aplikace do clusteru Service Fabric
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 313a1b7e4f0109a0a5cb94214918d97c25b5cf5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66153847"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Přidání certifikátu aplikace do clusteru Service Fabric

Tento ukázkový skript vytvoří v zadaném trezoru klíčů Azure certifikát podepsaný svým držitelem a nainstaluje ho do všech uzlů clusteru Service Fabric. Certifikát se také stáhne do místní složky. Název staženého certifikátu je stejný jako název certifikátu v trezoru klíčů. Podle potřeby upravte parametry.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate) | Přidá nový certifikát aplikace do škálovací sady virtuálních počítačů, která tvoří cluster.  |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
