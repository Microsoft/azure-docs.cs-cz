---
title: Konfigurace po nasazení Azure pomocí rozšíření
description: Naučte se používat rozšíření šablon Azure Resource Manager k poskytování konfigurací po nasazení.
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
ms.openlocfilehash: ccac91553d43a98bdef20abe451e7abec139dbaf
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533747"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Poskytování konfigurací po nasazení pomocí rozšíření

Rozšíření šablon jsou malé aplikace, které poskytují konfiguraci po nasazení a úlohy automatizace v prostředcích Azure. Nejoblíbenějším z nich jsou rozšíření virtuálních počítačů. Viz [rozšíření a funkce virtuálních počítačů pro Windows](../virtual-machines/extensions/features-windows.md)a [rozšíření a funkce virtuálních počítačů pro Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Rozšíření

Stávající rozšíření jsou:

- [Microsoft. COMPUTE/virtualMachines/Extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft. COMPUTE virtualMachineScaleSets/Extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Clustery/rozšíření Microsoft. HDInsight](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft. SQL servery/databáze/rozšíření](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft. Web/weby/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Dostupná rozšíření zjistíte tak, že přejdete na [odkaz šablony](https://docs.microsoft.com/azure/templates/). V části **filtrovat podle názvu**zadejte **příponu**.

Další informace o tom, jak tato rozšíření použít, najdete tady:

- [Kurz: nasazení rozšíření virtuálních počítačů pomocí šablon Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Kurz: Import souborů SQL BACPAC pomocí šablon Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: nasazení rozšíření virtuálních počítačů pomocí šablon Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
