---
title: Nasazení OpenShiftu v Azure Stacku
description: Nasazení OpenShiftu v Azure Stacku.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: d6c73b8cd33aa85793a2ce839410065e03b97be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035531"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Nasazení platformy KontejneropenShift nebo OKD v Azure Stacku

OpenShift lze nasadit v Azure Stacku. Existují některé klíčové rozdíly mezi Azure a Azure Stack, takže nasazení se bude mírně lišit a možnosti se také mírně liší.

V současné době Azure Cloud Provider nefunguje v Azure Stack. Z tohoto důvodu nebudete moct použít připojení disku pro trvalé úložiště v Azure Stacku. Místo toho můžete nakonfigurovat další možnosti úložiště, jako jsou NFS, iSCSI, GlusterFS atd. Alternativně můžete povolit CNS a použít GlusterFS pro trvalé ukládání. Pokud je povolena funkce CNS, budou nasazeny tři další uzly s dalším úložištěm pro použití GlusterFS.

K nasazení platformy OpenShift Container Platform nebo OKD v Azure Stacku můžete použít jednu z několika metod:

- Můžete ručně nasadit potřebné součásti infrastruktury Azure a potom postupovat podle [dokumentace platformy OpenShift Container Platform](https://docs.openshift.com/container-platform) nebo dokumentace [OKD](https://docs.okd.io).
- Můžete také použít existující [šablonu Správce prostředků,](https://github.com/Microsoft/openshift-container-platform/) která zjednodušuje nasazení clusteru platformy Kontejnerové platformy OpenShift.
- Můžete také použít existující [šablonu Správce prostředků,](https://github.com/Microsoft/openshift-origin) která zjednodušuje nasazení clusteru OKD.

Pokud používáte šablonu Správce prostředků, vyberte správnou větev (azurestack-release-3.x). Šablony pro Azure nebudou fungovat, protože verze rozhraní API se liší mezi Azure a Azure Stack. Odkaz na bitovou kopii RHEL je aktuálně pevně zakódován jako proměnná v souboru azuredeploy.json a bude nutné změnit tak, aby odpovídaly vaší image.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Pro všechny možnosti je vyžadováno předplatné Red Hat. Během nasazení je instance Red Hat Enterprise Linux registrována k předplatnému Red Hat a připojena k ID fondu, které obsahuje nároky pro platformu OpenShift Container.
Ujistěte se, že máte platné uživatelské jméno, heslo a ID fondu Správce předplatného (RHSM). Případně můžete použít aktivační klíč, ID organizace a ID fondu.  Tyto informace můžete ověřit přihlášením k programu https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Předpoklady zásobníku Azure

K nasazení clusteru OpenShift je třeba přidat bitovou kopii RHEL (OpenShift Container Platform) nebo image CentOS (OKD). Chcete-li přidat tyto bitové kopie, obraťte se na správce služby Azure Stack. Pokyny naleznete zde:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Nasazení pomocí platformy OpenShift Container Platform nebo šablony SPRÁVCE PROSTŘEDKŮ OKD

Chcete-li nasadit pomocí šablony Správce prostředků, použijte soubor parametrů k zadání vstupních parametrů. Chcete-li dále přizpůsobit nasazení, rozviněte úložiště GitHub a změňte příslušné položky.

Některé běžné možnosti vlastního nastavení zahrnují, ale nejsou omezeny na:

- Velikost virtuálního počítače bašty (proměnná v azuredeploy.json)
- Konvence pojmenování (proměnné v azuredeploy.json)
- Specifika clusteru OpenShift, upravená prostřednictvím souboru hosts (deployOpenShift.sh)
- Odkaz na obrázek RHEL (proměnná v azuredeploy.json)

Postup nasazení pomocí příkazového příkazu k řešení Azure postupujte podle příslušné části v části [Platforma kontejneru OpenShift](./openshift-container-platform-3x.md) nebo v části [OKD.](./openshift-okd.md)

## <a name="next-steps"></a>Další kroky

- [Úlohy po nasazení](./openshift-container-platform-3x-post-deployment.md)
- [Poradce při potížích s nasazením OpenShift v Azure](./openshift-container-platform-3x-troubleshooting.md)