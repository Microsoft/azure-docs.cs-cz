---
title: Průvodce publikováním nabídky nabídky šablony řešení Azure Aplikace | Azure Marketplace
description: Tento článek popisuje požadavky na publikování šablony řešení na Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 9/25/2019
ms.author: dsindona
ms.openlocfilehash: c84436015ad37b57f6603551f1d328ac76181836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288729"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure Aplikace: Šablona řešení Nabídka publikování Průvodce

Šablony řešení jsou jedním z hlavních způsobů publikování řešení na marketplace. V této příručce porozumíte požadavkům na tuto nabídku. 

Použijte typ nabídky aplikace Azure: šablona řešení, když vaše řešení vyžaduje další automatizaci nasazení a konfigurace nad rámec jednoho virtuálního počítače. Zřizování jednoho nebo více virtuálních počítačů můžete automatizovat pomocí aplikací Azure: šablon řešení. Můžete také zřídit síťové prostředky a prostředky úložiště. Aplikace Azure: šablony řešení nabízejí typ nabídky poskytuje výhody automatizace pro jednotlivé virtuální počítače a celá řešení založená na IaaS.

Tyto šablony řešení nejsou nabídky transakcí, ale lze použít k nasazení nabídek placeného virtuálního počítače účtovaných prostřednictvím komerčního trhu společnosti Microsoft. Výzva k akci, kterou uživatel vidí, je "Get It Now".


## <a name="requirements-for-solution-templates"></a>Požadavky na šablony řešení

| **Požadavky** | **Podrobnosti**  |
| ---------------  | -----------  |
|Fakturace a měření    |  Prostředky se zřídí v předplatném Azure zákazníka. Virtuální počítače s průběžnými platbami (PAYGO) budou se zákazníkem obchodovány prostřednictvím Microsoftu, které se budou účtovat prostřednictvím předplatného Azure (PAYGO) zákazníka.  <br/> V případě bring-your-own-license (BYOL), zatímco společnost Microsoft bude účtovat náklady na infrastrukturu vzniklé v rámci předplatného zákazníka, budete obchodovat licenční poplatky za software přímo zákazníkovi.   |
|Virtuální pevný disk kompatibilní s Azure (VHD)  |   Virtuální počítače musí být postavené na Windows nebo Linuxu.  Další informace naleznete [v tématu Vytvoření virtuálního pevného disku kompatibilního s Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Přiřazení využití zákazníka | Povolení přiřazení využití zákazníků je vyžadováno u všech šablon řešení publikovaných na Azure Marketplace. Další informace o atribuce využití zákazníků a o tom, jak ho povolit, najdete [v tématu Přiřazení k využití zákazníků partnera Azure](./azure-partner-customer-usage-attribution.md).  |
| Použití spravovaných disků | [Spravované disky](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) je výchozí možnost pro trvalé disky virtuálních počítačů IaaS v Azure. Spravované disky je nutné použít v šablonách řešení. <br> <br> 1. Postupujte [podle pokynů](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) a [ukázek](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) pro použití spravovaných disků v šablonách Azure ARM k aktualizaci šablon řešení. <br> <br> 2. Podle následujících pokynů importujte základní virtuální disky spravovaných disků do účtu úložiště a publikujte virtuální pevný disk jako bitovou kopii na webu Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Cli](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Další kroky
Pokud jste tak ještě neučinili, [zaregistrujte se](https://azuremarketplace.microsoft.com/sell) na trhu.

Pokud jste zaregistrováni a vytváříte novou nabídku nebo pracujete na stávající nabídce, přihlaste se na [portál partnerů cloudu](https://cloudpartner.azure.com) a vytvořte nebo dokončete svou nabídku.
