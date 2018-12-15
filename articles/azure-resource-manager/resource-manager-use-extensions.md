---
title: Zajištění konfigurace po nasazení s využitím rozšíření – Azure | Dokumentace Microsoftu
description: Další informace o použití rozšíření šablon Azure Resource Manageru k zajištění konfigurace po nasazení.
services: azure-resource-manager
documentationcenter: na
author: mumian
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: eb46966c3a28b3fa4c2b23668109b7c5d23a609b
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414376"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Zajištění konfigurace po nasazení pomocí rozšíření

Rozšíření šablon jsou malých aplikací, které poskytují konfiguraci po nasazení a úlohy automatizace pro prostředky Azure. Nejoblíbenější je rozšíření virtuálních počítačů. Zobrazit [funkcí a rozšíření virtuálních počítačů pro Windows](../virtual-machines/extensions/features-windows.md), a [funkcí a rozšíření virtuálních počítačů pro Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Rozšíření

Existující rozšíření jsou:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/rozšíření](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clustery/rozšíření](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft.Sql servery pro/databází/rozšíření](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Zjistit dostupných rozšíření, přejděte [referenčními informacemi k šablonám](https://docs.microsoft.com/azure/templates/). V **filtrovat podle názvu**, zadejte **rozšíření**.

Další informace o použití těchto rozšíření najdete v tématu:

- [Kurz: Nasazení rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Kurz: Import souborů SQL BACPAC pomocí šablon Azure Resource Manageru](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Nasazení rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru](./resource-manager-tutorial-deploy-vm-extensions.md)
