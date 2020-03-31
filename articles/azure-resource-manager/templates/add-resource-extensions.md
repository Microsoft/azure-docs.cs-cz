---
title: Konfigurace po nasazení pomocí rozšíření
description: Zjistěte, jak pomocí rozšíření šablon Azure Resource Manageru poskytovat konfigurace po nasazení.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023493"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Poskytování konfigurací po nasazení pomocí rozšíření

Rozšíření šablon jsou malé aplikace, které poskytují úlohy konfigurace po nasazení a automatizace na prostředky Azure. Nejoblíbenější je rozšíření virtuálních strojů. Viz [Rozšíření a funkce virtuálních strojů pro Windows](../../virtual-machines/extensions/features-windows.md)a Rozšíření a funkce [virtuálních strojů pro Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Rozšíření

Stávající rozšíření jsou:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Clustery/rozšíření Microsoft.HDInsight](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Servery/databáze/rozšíření Microsoft.SQL](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Rozšíření microsoft.web/weby/weby](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Chcete-li zjistit dostupná rozšíření, přejděte k [odkazu na šablonu](https://docs.microsoft.com/azure/templates/). Do **pole Filtrovat podle názvu**zadejte **rozšíření**.

Informace o použití těchto rozšíření naleznete v následujících tématech:

- [Kurz: Nasazení rozšíření virtuálních strojů pomocí šablon Azure Resource Manager .](template-tutorial-deploy-vm-extensions.md)
- [Kurz: Import souborů SQL BACPAC pomocí šablon Azure Resource Manageru](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Nasazování rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru](template-tutorial-deploy-vm-extensions.md)
