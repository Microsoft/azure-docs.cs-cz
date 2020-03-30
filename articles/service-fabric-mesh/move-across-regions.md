---
title: Přesunutí aplikace Service Fabric Mesh do jiné oblasti
description: Prostředky sítě Service Fabric můžete přesunout nasazením kopie aktuální šablony do nové oblasti Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908160"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Přesunutí aplikace Service Fabric Mesh do jiné oblasti Azure

Tento článek popisuje, jak přesunout aplikaci Service Fabric Mesh a její prostředky do jiné oblasti Azure. Prostředky můžete přesunout do jiné oblasti z mnoha důvodů. Například v reakci na výpadky, získat funkce nebo služby dostupné pouze v konkrétních regionech, splnit požadavky na vnitřní politiku a zásady správného řízení nebo v reakci na požadavky na plánování kapacity.

 [Service Fabric Mesh nepodporuje](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) možnost přímého přesunutí prostředků napříč oblastmi Azure. Prostředky však můžete přesunout nepřímo nasazením kopie aktuální šablony Azure Resource Manager do nové cílové oblasti a pak přesměrovat příchozí přenosy a závislosti na nově vytvořenou aplikaci Service Fabric Mesh.

## <a name="prerequisites"></a>Požadavky

* Řadič příchozího přenosu dat (například [aplikační brána)](https://docs.microsoft.com/azure/application-gateway/)slouží jako zprostředkovatel pro směrování provozu mezi klienty a aplikací Service Fabric Mesh
* Dostupnost sítě Service Fabric (Preview) v`westus` `eastus`cílové `westeurope`oblasti Azure ( , nebo )

## <a name="prepare"></a>Příprava

1. Pořiďte "snímek" aktuálního stavu aplikace Service Fabric Mesh exportem šablony Azure Resource Manager a parametrů z nejnovějšího nasazení. Chcete-li to provést, postupujte podle kroků v [šabloně Export po nasazení](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) pomocí portálu Azure. Můžete taky použít [Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)nebo [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate).

2. Pokud je to možné, [exportujte další prostředky ve stejné skupině prostředků](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) pro opětovné nasazení v cílové oblasti.

3. Zkontrolujte (a v případě potřeby upravte) exportovnou šablonu, abyste zajistili, že existující hodnoty vlastností jsou ty, které chcete použít v cílové oblasti. Nový `location` (oblast Azure) je parametr, který zadáte během opětovného nasazení.

## <a name="move"></a>Přesunout

1. Vytvořte novou skupinu prostředků (nebo použijte existující) v cílové oblasti.

2. S exportovnou šablonou postupujte podle pokynů v [části Nasazení prostředků z vlastní šablony](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) pomocí portálu Azure. Můžete taky použít [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli), [Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)nebo [REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest).

3. Pokyny k přesunutí souvisejících prostředků, jako jsou [účty Azure Storage](../storage/common/storage-account-move.md), najdete v pokynech pro jednotlivé služby uvedené v tématu Přesunutí prostředků Azure [napříč oblastmi](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Ověřit

1. Po dokončení nasazení otestujte koncové body aplikace a ověřte funkčnost aplikace.

2. Stav aplikace můžete také ověřit kontrolou stavu aplikace[(az mesh app show)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)a kontrolou protokolů aplikací a[(az mesh code-package-log)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)pomocí [rozhraní PŘÍKAZU Azure Service Fabric Mesh CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Potvrzení

Po potvrzení ekvivalentní chod aplikace Service Fabric Mesh v cílové oblasti nakonfigurujte řadič příchozího přenosu dat (například [aplikační brána)](../application-gateway/redirect-overview.md)tak, aby přesměroval provoz do nové aplikace.

## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků

Chcete-li dokončit přesun aplikace Service Fabric Mesh, [odstraňte zdrojovou aplikaci nebo nadřazenou skupinu prostředků](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Další kroky

* [Přesunutí prostředků Azure napříč oblastmi](../azure-resource-manager/management/move-region.md)
* [Podpora přesouvání prostředků Azure napříč oblastmi](../azure-resource-manager/management/region-move-support.md)
* [Přesun prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Podpora operace přesunutí pro prostředky](../azure-resource-manager/management/move-support-resources.md
)
