---
title: Nasazení OpenShift v Azure Stack
description: Nasaďte OpenShift do Azure Stack.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: decc66d2d5abd16c084aa19443dbac54e87a9560
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667221"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Nasazení kontejnerové platformy OpenShift nebo OKD v Azure Stack

OpenShift se dá nasadit v Azure Stack. Mezi Azure a Azure Stack je několik klíčových rozdílů, takže se nasazení mírně liší a možnosti se taky mírně liší.

Poskytovatel cloudu Azure v současné době nefunguje v Azure Stack. Z tohoto důvodu nebudete moct k trvalému uložení v Azure Stack použít připojení k disku. Místo toho můžete nakonfigurovat další možnosti úložiště, jako je třeba NFS, iSCSI, GlusterFS atd. Alternativně můžete povolit propojené sítě a používat GlusterFS pro trvalé úložiště. Pokud je povolený CNS, nasadí se tři další uzly s dodatečným úložištěm pro použití GlusterFS.

K nasazení OpenShift kontejnerové platformy nebo OKD v Azure Stack můžete použít některou z několika metod:

- Je možné ručně nasadit nezbytné součásti infrastruktury Azure a pak dodržet [dokumentaci k platformě OpenShift Container](https://docs.openshift.com/container-platform) a [dokumentaci OKD](https://docs.okd.io).
- Můžete také použít stávající [šablonu správce prostředků](https://github.com/Microsoft/openshift-container-platform/) , která zjednodušuje nasazení clusteru OpenShift Container Platform.
- Můžete také použít stávající [šablonu správce prostředků](https://github.com/Microsoft/openshift-origin) , která zjednodušuje nasazení clusteru OKD.

Pokud používáte šablonu Správce prostředků, vyberte správnou větev (azurestack-Release-3. x). Šablony pro Azure nebudou fungovat, protože verze rozhraní API se v Azure a Azure Stack liší. Odkaz na obrázek RHEL je v současné době pevně zakódovaný jako proměnná v souboru azuredeploy.jsa bude nutné ji změnit tak, aby odpovídala vašemu obrázku.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Pro všechny možnosti se vyžaduje předplatné Red Hat. Během nasazování je instance Red Hat Enterprise Linux zaregistrovaná v rámci předplatného Red Hat a připojená k ID fondu, které obsahuje nároky na OpenShift kontejnerové platformy.
Ujistěte se, že máte platné uživatelské jméno, heslo a ID fondu pro Red Hat Subscription Manager (RHSM). Alternativně můžete použít aktivační klíč, ID organizace a ID fondu.  Tyto informace můžete ověřit tak, že se přihlásíte k https://access.redhat.com .

## <a name="azure-stack-prerequisites"></a>Azure Stack předpoklady

RHEL Image (OpenShift Container Platform) nebo Image CentOS (OKD) se musí do prostředí Azure Stack přidat, aby bylo možné nasadit cluster OpenShift. Pokud chcete přidat tyto image, obraťte se na správce Azure Stack. Pokyny najdete tady:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Nasazení pomocí OpenShift kontejnerové platformy nebo šablony OKD Správce prostředků

Chcete-li nasadit pomocí šablony Správce prostředků, použijte soubor parametrů k zadání vstupních parametrů. Pro další přizpůsobení nasazení, rozvětvete úložiště GitHub a změňte příslušné položky.

Mezi běžné možnosti přizpůsobení patří, ale nejsou omezené na:

- Velikost virtuálního počítače bastionu (proměnná v azuredeploy.jszapnutá)
- Zásady vytváření názvů (proměnné v azuredeploy.jszapnuté)
- OpenShift konkrétní clustery, upravené prostřednictvím souboru Hosts (deployOpenShift.sh)
- Odkaz na obrázek RHEL (proměnná v azuredeploy.jszapnutá)

Postup nasazení pomocí Azure CLI najdete v příslušné části v části [OpenShift Container Platform](./openshift-container-platform-3x.md) nebo v části [OKD](./openshift-okd.md) .

## <a name="next-steps"></a>Další kroky

- [Úkoly po nasazení](./openshift-container-platform-3x-post-deployment.md)
- [Řešení potíží s nasazením OpenShift v Azure](./openshift-container-platform-3x-troubleshooting.md)