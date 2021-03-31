---
title: Ukázky šablon Azure Resource Manager
description: Vyhledání ukázek šablon Azure Resource Manager k nasazení Azure Container Instances v různých konfiguracích
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: 6c487087b39244178643fd81364150ceb3ac4a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169642"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Šablony Azure Resource Manager pro Azure Container Instances

Následující ukázkové šablony nasazují instance kontejneru v různých konfiguracích.

Možnosti nasazení najdete v části věnované [nasazení](#deployment) . Chcete-li vytvořit vlastní šablony, šablona Azure Container Instances [Správce prostředků šablony referenční][ref] podrobnosti a vlastnosti k dispozici.

## <a name="sample-templates"></a>Ukázkové šablony

| Template (Šablona) | Popis |
|-|-|
| **Aplikace** ||
| [WordPress][app-wp] | Vytvoří web WordPress a jeho databázi MySQL ve skupině kontejnerů. Obsah webu WordPress a databáze MySQL jsou trvalé ve sdílené složce souborů Azure. Vytvoří také aplikační bránu pro zpřístupnění přístupu k veřejné síti pro WordPress. |
| [MS NAV s SQL Server a službou IIS][app-nav] | Nasadí jeden kontejner Windows s plně vybaveným samoobslužným podnikovým prostředím Dynamics NAV/Dynamics 365. |
| **Svazky** ||
| [emptyDir][vol-emptydir] | Nasadí dva kontejnery Linux, které sdílejí svazek emptyDir. |
| [gitRepo][vol-gitrepo] | Nasadí kontejner pro Linux, který naklonuje úložiště GitHubu a připojí ho jako svazek. |
| [Tajný kód][vol-secret] | Nasadí kontejner pro Linux s certifikátem PFX připojeným jako tajný svazek. |
| **Sítě** ||
| [Kontejner vystavený UDP][net-udp] | Nasadí kontejner systému Windows nebo Linux, který zveřejňuje port UDP. |
| [Kontejner pro Linux s veřejnou IP adresou][net-publicip] | Nasadí jeden kontejner pro Linux přístupný prostřednictvím veřejné IP adresy. |
| [Nasazení skupiny kontejnerů pomocí virtuální sítě][net-vnet] | Nasadí novou virtuální síť, podsíť, síťový profil a skupinu kontejnerů. |
| **Prostředky Azure** ||
| [Vytvoření sdílené složky Azure Storage účtu a souborů][az-files] | Pomocí Azure CLI v instanci kontejneru vytvoří účet úložiště a sdílenou složku služby soubory Azure.

## <a name="deployment"></a>Nasazení

K nasazení prostředků pomocí šablon Správce prostředků máte několik možností:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

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
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
