---
title: Ukázkové šablony Azure Resource Manager – Azure Container Instances
description: Ukázkové šablony Azure Resource Manageru pro Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: 3d73d05c64f4b4867c69a15089c19ab8c320b9a8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006475"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Šablony Azure Resource Manageru pro službu Azure Container Instances

Následující ukázkové šablony nasadit kontejner instancí v různých konfiguracích.

Možnosti nasazení, najdete v článku [nasazení](#deployment) oddílu. Pokud chcete vytvořit vlastní šablony, Azure Container Instances [referenčními informacemi k šablonám Resource Manageru] [ ref] podrobnosti o formátu šablony a k dispozici vlastnosti.

## <a name="sample-templates"></a>Ukázkové šablony

| | |
|-|-|
| **Aplikace** ||
| [WordPress][app-wp] | Vytvoří web ve Wordpressu a jeho databázi MySQL ve skupině kontejnerů. Obsah webu WordPress a databáze MySQL se ukládají do soubory Azure sdílet. Také vytvoří službu application gateway k vystavení přístup k veřejné síti wordpressu. |
| [MS NAV přes SQL Server a služby IIS][app-nav] | Nasadí jeden kontejner Windows s plně funkční samostatná Dynamics NAV nebo Dynamics 365 Business Central prostředí. |
| **Svazky** ||
| [emptyDir][vol-emptydir] | Nasadí dva kontejnery Linuxu, které sdílejí svazku emptyDir. |
| [gitRepo][vol-gitrepo] | Nasazení kontejneru Linuxu, která duplicity úložiště GitHub a připojí ji jako svazek. |
| [secret][vol-secret] | Nasazení kontejneru Linuxu s certifikátem PFX připojit jako tajný svazek. |
| **Sítě** ||
| [Vystavené UDP kontejneru][net-udp] | Nasazení kontejneru Windows nebo Linux, který zpřístupňuje UDP port. |
| [Kontejner Linuxu s veřejnou IP adresu][net-publicip] | Nasadí jednoho kontejneru Linuxu přístupné přes veřejnou IP adresu. |
| [Nasazení kontejneru skupiny s virtuální sítí (preview)][net-vnet] | Nasadí novou virtuální síť, podsíť, profil sítě a skupinu kontejnerů. |
| **Prostředky Azure** ||
| [Vytvoření účtu Azure Storage a soubory sdílené složky][az-files] | Chcete-li vytvořit účet úložiště a sdílené složky služby soubory Azure pomocí Azure CLI v instanci kontejneru.

## <a name="deployment"></a>Nasazení

Máte několik možností pro nasazení prostředků pomocí šablon Resource Manageru:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure portal][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
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
