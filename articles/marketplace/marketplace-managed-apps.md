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
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: ae50562a9354b9c1e5b2711e5bbeec7b1e0fbbb8
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078792"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplikace Azure: Nabídka spravované aplikace, Průvodce publikováním

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
|Kompatibilní s Azure virtuálního pevného disku (VHD)    |   Virtuální počítače musí být sestaveny jako ve Windows nebo Linuxu.<ul> <ul> <li>Další informace o vytváření linuxového virtuálního pevného disku najdete v tématu [Linuxových distribucí doporučených pro na Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Další informace o vytváření virtuálního pevného disku Windows najdete v tématu [vytvoření virtuálního pevného disku kompatibilního s Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Spravované aplikace musí být nasaditelný prostřednictvím webu Marketplace. Pokud zákazník komunikace je problém, pak by se kontaktovat zúčastněným zákazníkům po povolení sdílení potenciálních zákazníků.  


## <a name="next-steps"></a>Další postup
Pokud jste tak již neučinili, 

- [Zaregistrujte](https://azuremarketplace.microsoft.com/sell) na webu Marketplace.

Pokud budete zaregistrováni a vytvoření nové nabídky nebo pracující na existující,

- [Přihlaste se na portál Cloud Partner](https://cloudpartner.azure.com) k vytvoření nebo dokončení vaší nabídky.
