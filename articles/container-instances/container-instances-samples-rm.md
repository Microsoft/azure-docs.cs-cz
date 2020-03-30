---
title: Ukázky šablon Azure Resource Manageru
description: Najděte ukázky šablon Azure Resource Manageru pro nasazení instancí kontejnerů Azure v různých konfiguracích
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: a8f3c81c539562a3c56e4822cf6e4df77d04928f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981653"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Šablony Azure Resource Manageru pro instance kontejnerů Azure

Následující ukázkové šablony nasazují instance kontejnerů v různých konfiguracích.

Možnosti nasazení najdete v části [Nasazení.](#deployment) Pokud chcete vytvořit vlastní šablony, šablona šablony Azure Container Instances [Resource Manager odkazuje na][ref] šablonu šablony podrobností a dostupných vlastností.

## <a name="sample-templates"></a>Ukázkové šablony

| | |
|-|-|
| **Aplikace** ||
| [WordPress][app-wp] | Vytvoří webové stránky WordPress a jeho MySQL databáze ve skupině kontejnerů. Obsah webu WordPress a databáze MySQL jsou trvalé do sdílené složky Azure Files. Také vytvoří aplikační bránu pro vystavení přístupu k veřejné síti wordpressu. |
| [MS NAV se serverem SQL A Službou IIS][app-nav] | Nasazuje jeden kontejner Windows s plně vybaveným samostatným prostředím Dynamics NAV / Dynamics 365 Business Central. |
| **Svazky** ||
| [emptyDir][vol-emptydir] | Nasadí dva linuxové kontejnery, které sdílejí prázdný svazek Dir. |
| [gitRepo][vol-gitrepo] | Nasadí linuxový kontejner, který klonuje úložiště GitHub a připojí ho jako svazek. |
| [Tajemství][vol-secret] | Nasazuje linuxový kontejner s certifikátem PFX připojeným jako tajný svazek. |
| **Síťové služby** ||
| [Kontejner vystavený UDP][net-udp] | Nasazuje kontejner Windows nebo Linux, který zveřejňuje port UDP. |
| [Linuxový kontejner s veřejnou IP adresou][net-publicip] | Nasazuje jeden kontejner Linuxu přístupný prostřednictvím veřejné IP adresy. |
| [Nasazení skupiny kontejnerů s virtuální sítí (preview)][net-vnet] | Nasazuje novou virtuální síť, podsíť, profil sítě a skupinu kontejnerů. |
| **Prostředky Azure** ||
| [Vytvoření sdílené položky účtu azure úložiště a sdílení souborů][az-files] | Používá Azure CLI v instanci kontejneru k vytvoření účtu úložiště a sdílené složky Azure Files.

## <a name="deployment"></a>Nasazení

Máte několik možností pro nasazení prostředků pomocí šablon Správce prostředků:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Portál Azure][deploy-portal]

[ROZHRANÍ API PRO ODPOČINEK][deploy-rest]

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
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
