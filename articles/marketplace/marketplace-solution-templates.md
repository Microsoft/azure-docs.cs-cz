---
title: Průvodce publikováním řešení aplikací Azure | Azure Marketplace
description: Tento článek popisuje požadavky na publikování šablony řešení na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084851"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Aplikace Azure: Šablona řešení nabízí požadavky na publikování

Tento článek vysvětluje požadavky na typ nabídky šablony řešení, což je jeden ze způsobů, jak publikovat nabídku aplikace Azure na Azure Marketplace. Typ nabídky šablony řešení vyžaduje [šablonu Azure Resource Manager (šablona ARM)](../azure-resource-manager/templates/overview.md) k automatickému nasazení infrastruktury řešení.

Typ nabídky šablony aplikačního řešení Azure použijte v případě, že jsou požadovány následující podmínky:

- Vaše řešení vyžaduje další automatizaci nasazení a konfigurace nad rámec jednoho virtuálního počítače, jako je například kombinace virtuálních počítačů, sítí a prostředků úložiště.
- Váš zákazník bude řešení spravovat sám.

Výzva k akci, kterou uživatel vidí pro tento typ nabídky, je "Get It Now".

## <a name="requirements-for-solution-template-offers"></a>Požadavky na nabídky šablon řešení

| **Požadavky** | **Podrobnosti**  |
| ---------------  | -----------  |
|Fakturace a měření    |  Nabídky šablon řešení nejsou nabídky pro transakt, ale lze je použít k nasazení nabídek placeného virtuálního počítače účtovaných prostřednictvím komerčního trhu microsoftu. Prostředky, které nasadí šablona ARM řešení se zřídí v předplatném Azure zákazníka. Virtuální počítače s průběžnými platbami (PAYGO) budou se zákazníkem obchodovány prostřednictvím Microsoftu, které se budou účtovat prostřednictvím předplatného Azure zákazníka.<br/> V případě bring-your-own-license (BYOL), zatímco společnost Microsoft bude účtovat náklady na infrastrukturu vzniklé v rámci předplatného zákazníka, budete obchodovat licenční poplatky za software přímo zákazníkovi.   |
|Virtuální pevný disk kompatibilní s Azure (VHD)  |   Virtuální počítače musí být postavené na Windows nebo Linuxu. Další informace naleznete v tématu: <ul> <li>[Vytvoření nabídky aplikace Azure](./partner-center-portal/create-new-azure-apps-offer.md)(pro virtuální počítače s Windows)</li><li>[Linuxové distribuce potvrzené v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (pro Linux VD).</li></ul> |
| Přiřazení využití zákazníka | Povolení přiřazení využití zákazníků je vyžadováno u všech šablon řešení publikovaných na Azure Marketplace. Další informace o atribuce využití zákazníků a o tom, jak ho povolit, najdete [v tématu Přiřazení k využití zákazníků partnera Azure](./azure-partner-customer-usage-attribution.md).  |
| Použití spravovaných disků | [Spravované disky](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) je výchozí možnost pro trvalé disky virtuálních počítačů IaaS v Azure. Spravované disky je nutné použít v šablonách řešení. <br> <br> 1. Postupujte [podle pokynů](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) a [ukázek](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) pro použití spravovaných disků v šablonách Azure ARM k aktualizaci šablon řešení. <br> <br> 2. Podle následujících pokynů importujte základní virtuální disky spravovaných disků do účtu úložiště a publikujte virtuální pevný disk jako bitovou kopii na webu Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Rozhraní příkazového řádku](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Další kroky

- Pokud jste to ještě neudělali, [přečtěte si informace](https://azuremarketplace.microsoft.com/sell) o Azure Marketplace.
- [Chcete-li](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) vytvořit nebo dokončit svou nabídku, přihlaste se do Centra partnerů.
- [Vytvořte nabídku aplikací Azure](./partner-center-portal/create-new-azure-apps-offer.md) pro další informace.
