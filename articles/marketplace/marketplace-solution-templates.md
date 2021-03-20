---
title: Příručka pro publikování pro šablony řešení Azure Applications – Azure Marketplace
description: Tento článek popisuje požadavky na publikování šablon řešení na Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 04/22/2020
ms.openlocfilehash: c7074981c8491460d6f2a8e7d40d086f261dfeb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879339"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Průvodce publikováním pro nabídky šablon řešení pro aplikace Azure

Tento článek popisuje požadavky pro publikování nabídek šablon řešení, což je jeden ze způsobů publikování nabídek aplikací Azure v Azure Marketplace. Typ nabídky šablony řešení vyžaduje [šablonu Azure Resource Manager (šablona ARM)](../azure-resource-manager/templates/overview.md) k automatickému nasazení vaší infrastruktury řešení.

Použijte typ nabídky *Šablona řešení* Azure Application v následujících podmínkách:

- Vaše řešení vyžaduje další automatizaci nasazení a konfigurace než jeden virtuální počítač (VM), jako je například kombinace virtuálních počítačů, sítí a prostředků úložiště.
- Vaši zákazníci budou spravovat řešení sami.

Možnost výpisu, kterou zákazník uvidí pro tento typ nabídky, *teď získá*.

## <a name="requirements-for-solution-template-offers"></a>Požadavky pro nabídky šablony řešení

| **Požadavky** | **Podrobnosti**  |
| ---------------  | -----------  |
|Fakturace a měření    |  Nabídky šablony řešení nejsou nabídky transakcí, ale dají se použít k nasazení placených nabídek virtuálních počítačů, které se účtují prostřednictvím komerčního tržiště Microsoftu. Prostředky, které jsou nasazené šablonou ARM řešení, se nastavují v předplatném Azure zákazníka. Virtuální počítače s průběžnými platbami se účtují u zákazníka prostřednictvím Microsoftu a účtují se prostřednictvím předplatného Azure zákazníka.<br/> V případě fakturace s využitím vlastní licence (BYOL), i když Microsoft účtuje náklady na infrastrukturu, které se účtují v rámci předplatného zákazníka, vyúčtujete poplatky za licence na software přímo pro zákazníka.   |
|Virtuální pevný disk kompatibilní s Azure (VHD)  |   Virtuální počítače musí být postavené na Windows nebo Linux. Další informace naleznete v tématu: <ul> <li>[Vytvořte nabídku aplikace Azure](./create-new-azure-apps-offer.md) (pro virtuální pevné disky Windows).</li><li>[Distribuce systému Linux schválená v Azure](../virtual-machines/linux/endorsed-distros.md) (pro virtuální pevné disky se systémem Linux).</li></ul> |
| Přisuzování využití ze strany zákazníků | U všech šablon řešení, které jsou publikovány na Azure Marketplace, je nutné povolit přidělení zákaznického využití. Další informace o přidaných zákaznických využití a o tom, jak ji povolit, najdete v tématu věnovaném [přidělení zákaznického využívání Azure partnerovi](./azure-partner-customer-usage-attribution.md).  |
| Použití spravovaných disků | [Managed disks](../virtual-machines/managed-disks-overview.md) je výchozí možností pro trvalé disky virtuálních počítačů infrastruktury jako služby (IaaS) v Azure. Spravované disky je nutné použít v šablonách řešení. <ul><li>Pokud chcete aktualizovat šablony řešení, postupujte podle pokynů v části [použití spravovaných disků v šablonách Azure Resource Manager](../virtual-machines/using-managed-disks-template-deployments.md)a použijte uvedené [ukázky](https://github.com/Azure/azure-quickstart-templates).<br><br> </li><li>Pokud chcete publikovat VHD jako image v Azure Marketplace, importujte základní virtuální pevný disk spravovaných disků do účtu úložiště pomocí některé z následujících metod:<ul><li>[Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) </li> <li> [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) </li> </ul></ul> |

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, přečtěte si, jak [rozšířit cloudovou firmu pomocí Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrace a zahájení práce v partnerském centru:

- [Přihlaste se do partnerského centra](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) a vytvořte nebo dokončete vaši nabídku.
- Další informace najdete v tématu [Vytvoření nabídky aplikací Azure](./create-new-azure-apps-offer.md) .