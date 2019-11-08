---
title: Průvodce publikováním nabídky šablon řešení Azure Applications | Azure Marketplace
description: Tento článek popisuje požadavky pro publikování šablony řešení v Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 9/25/2019
ms.author: ellacroi
ms.openlocfilehash: 934a5e050e190c9a1f90bb3a22c2d1323a3ccecf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808289"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplikace Azure: Průvodce publikováním nabídky šablon řešení

Šablony řešení jsou jedním z hlavních způsobů publikování řešení na webu Marketplace. Tento průvodce vám pomůže pochopit požadavky této nabídky. 

Použijte nabídku aplikace Azure: typ nabídky šablona řešení, když vaše řešení vyžaduje další automatizaci nasazení a konfigurace než jeden virtuální počítač. Můžete automatizovat zřizování jednoho nebo více virtuálních počítačů pomocí aplikací Azure: šablony řešení. Můžete taky zřídit prostředky sítě a úložiště. Aplikace Azure: typ nabídky šablony řešení poskytuje výhody pro automatizaci pro jednotlivé virtuální počítače a celá řešení založená na IaaS.

Tyto šablony řešení nejsou nabídkami transakcí, ale dají se použít k nasazení placených nabídek virtuálních počítačů, které se účtují prostřednictvím komerčního tržiště Microsoftu. Volání akce, které uživatel vidí, je "získat hned".


## <a name="requirements-for-solution-templates"></a>Požadavky na šablony řešení

| **Požadavky** | **Podrobnosti**  |
| ---------------  | -----------  |
|Fakturace a měření    |  Prostředky se zřídí v předplatném Azure zákazníka. Virtuální počítače s průběžnými platbami (PAYGO) se budou s zákazníky účtovat prostřednictvím Microsoftu, které se účtují prostřednictvím předplatného Azure zákazníka (PAYGO).  <br/> V případě používání vlastní licence (BYOL), zatímco společnost Microsoft účtuje náklady na infrastrukturu, které se účtují v rámci předplatného zákazníka, budete poplatky za licence k softwaru účtovat přímo zákazníkovi.   |
|Virtuální pevný disk kompatibilní s Azure (VHD)  |   Virtuální počítače musí být postavené na Windows nebo Linux.  Další informace [najdete v tématu Vytvoření virtuálního pevného disku kompatibilního s Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Přidělení zákaznického využití | U všech šablon řešení publikovaných na Azure Marketplace se vyžaduje povolení přidělení zákaznického využití. Další informace o tom, kdo vydaný zákazník využíváním a jak ho povolit, najdete v tématu věnovaném [přidělení zákaznického využívání Azure partnerovi](./azure-partner-customer-usage-attribution.md).  |
| Použití spravovaných disků | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) je výchozí možností pro trvalé disky virtuálních počítačů s IaaS v Azure. Je nutné použít Managed Disks v šablonách řešení. <br> <br> 1. postupujte podle [pokynů](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) a [ukázek](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) pro použití Managed disks v šablonách Azure ARM k aktualizaci šablon řešení. <br> <br> 2. podle níže uvedených pokynů importujte základní virtuální pevný disk Managed Disks do účtu úložiště, aby se virtuální pevný disk publikoval jako image na webu Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Rozhraní příkazového řádku](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Další kroky
Pokud jste to ještě neudělali, [Zaregistrujte](https://azuremarketplace.microsoft.com/sell) se na webu Marketplace.

Pokud jste zaregistrovali a vytváříte novou nabídku nebo pracujete na existující nabídce, přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com) a vytvořte nebo dokončete vaši nabídku.
