---
title: Nabídka spravované aplikace Azure aplikace, Průvodce publikováním
description: Tento článek popisuje požadavky pro publikování spravované aplikace na webu Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: c8ead3dc34faefce0f113dee2074960fddfa11a1
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144587"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplikace Azure: Nabídka spravované aplikace Průvodce publikováním

Spravované aplikace je jedním z hlavních způsobů, jak publikování řešení na webu Marketplace. Tento průvodce vám porozumět všem požadavkům pro v rámci této nabídky. 

Jedná se o transakci nabídek, které se nasazují a prostřednictvím webu Marketplace účtují. Volání akce, které uživatel uvidí je "Získat."

Pomocí aplikace Azure: spravovaného typu nabídky aplikace, pokud vyžaduje splnění následujících podmínek:
- Nasadit buď řešení na základě předplatného pro vaše zákazníky pomocí virtuálního počítače nebo celé řešení založeného na IaaS.
- Vy nebo váš zákazník vyžadují, aby řešení spravuje partner.

>[!NOTE]
>Partnerský server může být například Incidentech nebo poskytovatelé spravované služby (MSP).  

## <a name="managed-application-offer"></a>Spravované aplikace nabídky

|Požadavky |Podrobnosti  |
|---------|---------|
|Nasazení s předplatným Azure zákazníka | Spravované aplikace musí být nasazen v rámci předplatného zákazníka a je možné spravovat pomocí 3. stran | 
|K fakturaci a měření    |  Prostředky se zřídí v rámci předplatného Azure zákazníka. Průběžné platby (PAYGO) virtuálních počítačů se nepodporuje transakce u zákazníka prostřednictvím Microsoft účtuje prostřednictvím předplatného Azure zákazníkem (PAYGO) 
V případě používání – vlastní licence zatímco Microsoft vám bude účtovat náklady na infrastrukturu v předplatném zákazníka můžete bude transact vaší licenční poplatky zákazníkovi přímo software        |
|Kompatibilní s Azure virtuálního pevného disku (VHD)    |   Virtuální počítače musí být sestaveny jako ve Windows nebo Linuxu.<ul> <li>Další informace o vytváření linuxového virtuálního pevného disku najdete vytvořením kompatibilní s Azure virtuálního pevného disku (založené na Linuxu) části umístění [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Další informace o vytváření virtuálního pevného disku Windows najdete vytvořením kompatibilní s Azure virtuálního pevného disku (založený na Windows) části umístění [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |

>[!NOTE]
> Spravované aplikace musí být nasaditelný prostřednictvím webu Marketplace. Pokud zákazník komunikace je problém, pak by se kontaktovat zúčastněným zákazníkům po povolení sdílení potenciálních zákazníků.  


## <a name="next-steps"></a>Další kroky
Pokud jste tak již neučinili, 

- [Zaregistrujte](https://azuremarketplace.microsoft.com/sell) na webu Marketplace

Pokud budete zaregistrováni a vytvoření nové nabídky nebo pracující na existující,

- [Přihlaste se na portál Cloud Partner](https://cloudpartner.azure.com) k vytvoření nebo dokončení vaší nabídky
