---
title: Ukázky šablony Azure Resource Manager - instancí kontejnerů Azure
description: Ukázek šablony Azure Resource Manager pro instance kontejner Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Šablony Azure Resource Manageru pro instance kontejner Azure

Následující ukázkové šablony nasaďte kontejner instancí v různých konfiguracích.

Možnosti nasazení najdete v tématu [nasazení](#deployment) části. Pokud chcete vytvořit vlastní šablony, instancí kontejnerů Azure [odkaz na šablonu Resource Manager] [ ref] podrobnosti formátu šablony a dostupné vlastnosti.

## <a name="sample-templates"></a>Ukázka šablony

| | |
|-|-|
| **Aplikace** ||
| [WordPress][app-wp] | V instanci kontejner vytvoří web WordPress a její databází MySQL. Jsou trvalé obsah webu WordPress a databáze MySQL na Azure Files sdílet. |
| [NAV MS s SQL serverem a služby IIS][app-nav] | Nasadí jediný kontejner Windows s plně funkční nezávislý Dynamics NAV nebo Dynamics 365 Business centrální prostředí. |
| **Svazky** ||
| [emptyDir][vol-emptydir] | Nasadí dvě Linux kontejnery, které sdílejí svazek emptyDir. |
| [GitRepo][vol-gitrepo] | Nasadí kontejner Linux, který provede klonování úložiště GitHub a připojí jako svazek. |
| [tajný klíč][vol-secret] | Nasadí kontejner Linux s připojit jako svazek tajný certifikát PFX. |
| **Sítě** ||
| [Kontejner zveřejněné UDP][net-udp] | Nasadí kontejner systému Windows nebo Linux, který zveřejňuje UDP port. |
| [Kontejner Linux s veřejnou IP adresu][net-publicip] | Nasadí jediný kontejner Linux přístupné přes veřejnou IP adresu. |
| **Prostředky Azure** ||
| [Vytvořit účet úložiště Azure a soubory sdílené složky][az-files] | Používá rozhraní příkazového řádku Azure v instanci kontejneru k vytvoření účtu úložiště a sdílené složky Azure Files.

## <a name="deployment"></a>Nasazení

Máte několik možností pro nasazení prostředků pomocí šablony Resource Manageru:

[Azure CLI][deploy-cli]

[Prostředí Azure PowerShell][deploy-powershell]

[Portál Azure][deploy-portal]

[ROZHRANÍ REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups