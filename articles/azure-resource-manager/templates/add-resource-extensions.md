---
title: Konfigurace po nasazení pomocí rozšíření
description: Naučte se používat rozšíření šablon Azure Resource Manager k poskytování konfigurací po nasazení.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: be55138a2aa6dc0552c7556438ffd43705687c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86055039"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Poskytování konfigurací po nasazení pomocí rozšíření

Rozšíření šablon jsou malé aplikace, které poskytují konfiguraci po nasazení a úlohy automatizace v prostředcích Azure. Nejoblíbenějším z nich jsou rozšíření virtuálních počítačů. Viz [rozšíření a funkce virtuálních počítačů pro Windows](../../virtual-machines/extensions/features-windows.md)a [rozšíření a funkce virtuálních počítačů pro Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Rozšíření

Stávající rozšíření jsou:

- [Microsoft. COMPUTE/virtualMachines/Extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft. COMPUTE virtualMachineScaleSets/Extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Clustery/rozšíření Microsoft. HDInsight](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft. SQL servery/databáze/rozšíření](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft. Web/weby/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Dostupná rozšíření zjistíte tak, že přejdete na [odkaz šablony](/azure/templates/). V části **filtrovat podle názvu**zadejte **příponu**.

Další informace o tom, jak tato rozšíření použít, najdete tady:

- [Kurz: nasazení rozšíření virtuálních počítačů pomocí šablon Azure Resource Manager](template-tutorial-deploy-vm-extensions.md).
- [Kurz: Import souborů SQL BACPAC pomocí šablon Azure Resource Manageru](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Nasazování rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru](template-tutorial-deploy-vm-extensions.md)
