---
title: Nasadit OpenShift v Azure stacku | Dokumentace Microsoftu
description: Nasaďte OpenShift v Azure stacku.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 2b111967548dd90a8795af617a80a23d763861fe
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037665"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Nasadit OpenShift Container Platform nebo OKD ve službě Azure Stack

Je možné nasadit OpenShift v Azure stacku. Nasazení se mírně liší a funkce budou také mírně lišit, existují některé hlavní rozdíly mezi Azure a Azure Stack.

V současné době poskytovatele cloudu Azure nefunguje ve službě Azure Stack. Z tohoto důvodu nebude moct používat disk připojit jako trvalé úložiště ve službě Azure Stack. Místo toho můžete nakonfigurovat další možnosti úložiště, jako je například systém souborů NFS, iSCSI, GlusterFS, atd. Jako alternativu můžete povolit CNS a používat GlusterFS jako trvalé úložiště. Pokud je povolené CNS, tři další uzly se nasadí s další úložiště pro GlusterFS využití.

Můžete použít některou z několika metod nasazení OpenShift Container Platform nebo OKD ve službě Azure Stack:

- Můžete ručně nasadit komponenty potřebnou infrastrukturu Azure a pak postupujte podle [OpenShift Container Platform dokumentaci](https://docs.openshift.com/container-platform) nebo [OKD dokumentaci](https://docs.okd.io).
- Můžete také použít existující [šablony Resource Manageru](https://github.com/Microsoft/openshift-container-platform/) , která zjednodušuje nasazení OpenShift Container Platform clusteru.
- Můžete také použít existující [šablony Resource Manageru](https://github.com/Microsoft/openshift-origin) , která zjednodušuje nasazení OKD clusteru.

Při použití šablony Resource Manageru, vyberte správné větve (azurestack 3.x vydání). Šablony pro Azure nebude fungovat podle verze rozhraní API jsou rozdíly mezi Azure a Azure Stack. Odkaz na image RHEL aktuálně pevně zakóduje jako proměnné v souboru azuredeploy.json a bude nutné změnit tak, aby odpovídala bitové kopie.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Pro všechny možnosti se vyžaduje předplatné Red Hat. Během nasazování instance Red Hat Enterprise Linux je zaregistrované u předplatného Red Hat a připojený k ID fondu, který obsahuje oprávnění pro OpenShift Container Platform.
Ujistěte se, že máte platné uživatelské jméno, heslo a ID fondu Red Hat předplatné správce (RHSM) Alternativně můžete použít aktivační klíč, ID organizace a ID fondu.  Tyto informace můžete ověřit tak, že přihlášení k https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Požadavky služby Azure Stack

Image RHEL (OpenShift Container Platform) nebo image CentOS (OKD) musí být přidán do prostředí Azure Stack pro nasazení OpenShift clusteru. Obraťte se na správce služby Azure Stack pro přidání těchto imagí. Pokyny najdete tady:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Nasazení pomocí šablony OpenShift Container Platform nebo OKD Resource Manageru

Nasazení pomocí šablony Resource Manageru, použijte soubor parametrů slouží k poskytování vstupní parametry. Chcete-li dále přizpůsobit nasazení, rozvětvit úložiště GitHub se vzorovými a změňte příslušné položky.

Některé běžné možnosti vlastního nastavení zahrnují, ale nejsou omezené na:

- Velikost virtuálního počítače bastionu (proměnné v azuredeploy.json)
- Zásady vytváření názvů (proměnné v azuredeploy.json)
- OpenShift specifika clusteru, se mění prostřednictvím souboru hosts (deployOpenShift.sh)
- Odkaz na image RHEL (proměnné v azuredeploy.json)

Postup nasazení pomocí Azure CLI, postupujte podle příslušné části v [OpenShift Container Platform](./openshift-container-platform.md) části nebo [OKD](./openshift-okd.md) oddílu.

## <a name="next-steps"></a>Další postup

- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazení OpenShift v Azure](./openshift-troubleshooting.md)