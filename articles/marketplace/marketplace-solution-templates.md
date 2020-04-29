---
title: Průvodce publikováním nabídky šablon řešení Azure Applications | Azure Marketplace
description: Tento článek popisuje požadavky pro publikování šablony řešení v Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084851"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Aplikace Azure: Šablona řešení nabídky požadavků na publikování

Tento článek popisuje požadavky pro typ nabídky šablony řešení, což je jeden ze způsobů, jak publikovat nabídku aplikací Azure v Azure Marketplace. Typ nabídky šablony řešení vyžaduje [šablonu Azure Resource Manager (šablona ARM)](../azure-resource-manager/templates/overview.md) k automatickému nasazení vaší infrastruktury řešení.

Pokud jsou potřeba následující podmínky, použijte typ nabídky šablona řešení Azure Application.

- Vaše řešení vyžaduje další automatizaci nasazení a konfigurace po jednom virtuálním počítači, jako je například kombinace virtuálních počítačů, sítí a úložných prostředků.
- Zákazník bude spravovat řešení sami.

Volání akce, které se uživateli zobrazí pro tento typ nabídky je "získat nyní".

## <a name="requirements-for-solution-template-offers"></a>Požadavky pro nabídky šablony řešení

| **Požadavky** | **Zobrazí**  |
| ---------------  | -----------  |
|Fakturace a měření    |  Nabídky šablon řešení nejsou nabídky Transact, ale dají se použít k nasazení placených nabídek virtuálních počítačů, které se účtují prostřednictvím komerčního tržiště Microsoftu. Prostředky, které se nasazují v šabloně ARM řešení, se zřídí v předplatném Azure zákazníka. Virtuální počítače s průběžnými platbami (PAYGO) se budou pomocí zákaznického předplatného Azure účtovat u zákazníka prostřednictvím Microsoftu.<br/> V případě používání vlastní licence (BYOL), zatímco společnost Microsoft účtuje náklady na infrastrukturu, které se účtují v rámci předplatného zákazníka, budete poplatky za licence k softwaru účtovat přímo zákazníkovi.   |
|Virtuální pevný disk kompatibilní s Azure (VHD)  |   Virtuální počítače musí být postavené na Windows nebo Linux. Další informace naleznete v tématu: <ul> <li>[Vytvoření nabídky aplikace Azure](./partner-center-portal/create-new-azure-apps-offer.md)(pro virtuální pevné disky Windows)</li><li>[Distribuce systému Linux schválená v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (pro virtuální pevné disky se systémem Linux).</li></ul> |
| Přidělení zákaznického využití | U všech šablon řešení publikovaných na Azure Marketplace se vyžaduje povolení přidělení zákaznického využití. Další informace o tom, kdo vydaný zákazník využíváním a jak ho povolit, najdete v tématu věnovaném [přidělení zákaznického využívání Azure partnerovi](./azure-partner-customer-usage-attribution.md).  |
| Použití spravovaných disků | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) je výchozí možností pro trvalé disky virtuálních počítačů s IaaS v Azure. Je nutné použít Managed Disks v šablonách řešení. <br> <br> 1. postupujte podle [pokynů](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) a [ukázek](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) pro použití Managed disks v šablonách Azure ARM k aktualizaci šablon řešení. <br> <br> 2. podle níže uvedených pokynů importujte základní virtuální pevný disk Managed Disks do účtu úložiště, aby se virtuální pevný disk publikoval jako image na webu Marketplace: <br> <ul> <li> [Prostředí](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Další kroky

- Pokud jste to ještě neudělali, [Přečtěte](https://azuremarketplace.microsoft.com/sell) si o Azure Marketplace.
- [Přihlaste se do partnerského centra](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) a vytvořte nebo dokončete vaši nabídku.
- Pokud chcete získat další informace, [vytvořte nabídku aplikace Azure](./partner-center-portal/create-new-azure-apps-offer.md) .
