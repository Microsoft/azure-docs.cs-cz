---
title: Přesunutí aplikace Service Fabric mřížky do jiné oblasti
description: Můžete přesunout Service Fabric prostředky sítě tak, že nasadíte kopii aktuální šablony do nové oblasti Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: c842a065f108a924c6bffd70d6c2edbbd31b6dff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260157"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Přesunutí aplikace Service Fabric sítě do jiné oblasti Azure

Tento článek popisuje, jak přesunout aplikaci Service Fabric sítě a její prostředky do jiné oblasti Azure. Prostředky můžete přesunout do jiné oblasti z řady důvodů. Například v reakci na výpadky můžete získat funkce nebo služby, které jsou k dispozici pouze v konkrétních oblastech, pro splnění požadavků na požadavky na interní zásady a zásady správného řízení nebo v reakci na požadavky na plánování kapacity.

 [Service Fabricová síť](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) nepodporuje možnost přímého přesouvání prostředků napříč oblastmi Azure. Prostředky je ale možné nepřímo přesunout tím, že do nové cílové oblasti nasadíte kopii aktuální Azure Resource Manager šablony a potom přesměrujete příchozí přenos dat a závislosti na nově vytvořenou aplikaci Service Fabric sítě.

## <a name="prerequisites"></a>Požadavky

* Řadič příchozího přenosu dat (například [Application Gateway](../application-gateway/index.yml)), který slouží jako prostředník pro směrování provozu mezi klienty a aplikací Service Fabric sítě
* Dostupnost Service Fabric sítě (Preview) v cílové oblasti Azure ( `westus` , `eastus` nebo `westeurope` )

## <a name="prepare"></a>Příprava

1. Vyžádejte si "snímek" aktuálního stavu aplikace Service Fabric sítě a exportujte Azure Resource Manager šablonu a parametry z nejnovějšího nasazení. Pokud to chcete provést, postupujte podle kroků v části [Export šablony po nasazení](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) pomocí Azure Portal. Můžete použít také rozhraní příkazového [řádku Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)nebo [REST API](/rest/api/resources/resourcegroups/exporttemplate).

2. V případě potřeby [exportujte jiné prostředky ve stejné skupině prostředků](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) pro opětovné nasazení v cílové oblasti.

3. Zkontrolujte (a v případě potřeby upravte) exportovanou šablonu, abyste zajistili, že existující hodnoty vlastností jsou ty, které chcete použít v cílové oblasti. Nová `location` (oblast Azure) je parametr, který budete poskytovat během opětovného nasazení.

## <a name="move"></a>Přesunout

1. Vytvořte novou skupinu prostředků (nebo použijte existující) v cílové oblasti.

2. S vaší exportovanou šablonou použijte postup v části [nasazení prostředků z vlastní šablony](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) pomocí Azure Portal. Můžete použít také rozhraní příkazového [řádku Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)nebo [REST API](../azure-resource-manager/templates/deploy-rest.md).

3. Pokyny k přesunu souvisejících prostředků, jako jsou [účty Azure Storage](../storage/common/storage-account-move.md), najdete v pokynech k jednotlivým službám, které jsou uvedené v tématu [Přesunutí prostředků Azure do různých oblastí](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Ověření

1. Po dokončení nasazení otestujte koncové body aplikace a ověřte funkčnost vaší aplikace.

2. Stav aplikace můžete také ověřit tak, že zkontrolujete stav aplikace ([AZ mřížka App show](/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)) a zkontrolujete protokoly aplikací a ([AZ oky Code-Package-log](/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)) pomocí rozhraní příkazového [řádku Azure Service Fabric](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Potvrzení

Jakmile ověříte, že jsou ekvivalentní funkce aplikace Service Fabric sítě v cílové oblasti, nakonfigurujte svůj kontroler příchozího přenosu dat (například [Application Gateway](../application-gateway/redirect-overview.md)), který přesměruje provoz do nové aplikace.

## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků

Pokud chcete dokončit přesun aplikace Service Fabric sítě, [odstraňte zdrojovou aplikaci nebo nadřazenou skupinu prostředků](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Další kroky

* [Přesun prostředků Azure napříč oblastmi](../azure-resource-manager/management/move-region.md)
* [Podpora přesunu prostředků Azure napříč oblastmi](../azure-resource-manager/management/region-move-support.md)
* [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Podpora operace přesunutí pro prostředky](../azure-resource-manager/management/move-support-resources.md
)
