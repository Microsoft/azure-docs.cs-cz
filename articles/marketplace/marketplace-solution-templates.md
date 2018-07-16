---
title: Nabídka šablony řešení Azure aplikace Průvodce publikováním
description: Tento článek popisuje požadavky na webu Marketplace publikovat šablony řešení
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
ms.openlocfilehash: 44d081a0666aa37ec0bf8eeac540b7a7f4f4f904
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059631"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplikace Azure: Nabídka šablony řešení Průvodce publikováním

Šablony řešení jsou jedním z hlavních způsobů, jak publikování řešení na webu Marketplace. Tento průvodce vám porozumět všem požadavkům pro v rámci této nabídky. 

Jedná se o transakci nabídek, které se nasazují a prostřednictvím webu Marketplace účtují. Volání akce, které uživatel uvidí je "Získat."

Pomocí aplikace Azure: typ nabídky šablony řešení, když vaše řešení nevyžaduje další automatizaci nasazení a konfigurace nad rámec jednoduchý virtuální počítač. Může automatizovat zřizování jeden nebo více virtuálních počítačů pomocí aplikace Azure: šablony řešení. Může také zřizovat prostředky sítě a úložiště. Aplikace Azure: šablony řešení nabízí typ poskytuje výhody služby automation jednotlivých virtuálních počítačů a celé řešení založeného na IaaS.

## <a name="requirements-for-solution-templates"></a>Požadavky pro šablony řešení

|Požadavky |Podrobnosti  |
|---------|---------|
|K fakturaci a měření    |  Prostředky se zřídí v rámci předplatného Azure zákazníka. Průběžné platby (PAYGO) virtuálních počítačů se nepodporuje transakce u zákazníka prostřednictvím Microsoft účtuje prostřednictvím předplatného Azure zákazníkem (PAYGO) 
V případě používání – vlastní licence zatímco Microsoft vám bude účtovat náklady na infrastrukturu v předplatném zákazníka můžete bude transact vaší licenční poplatky zákazníkovi přímo software        |
|Kompatibilní s Azure virtuálního pevného disku (VHD)    |   Virtuální počítače musí být sestaveny jako ve Windows nebo Linuxu.<ul> <li>Další informace o vytváření linuxového virtuálního pevného disku najdete vytvořením kompatibilní s Azure virtuálního pevného disku (založené na Linuxu) části umístění [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Další informace o vytváření virtuálního pevného disku Windows najdete vytvořením kompatibilní s Azure virtuálního pevného disku (založený na Windows) části umístění [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |



## <a name="next-steps"></a>Další kroky
Pokud jste tak již neučinili, 

- [Zaregistrujte](https://azuremarketplace.microsoft.com/sell) na webu Marketplace

Pokud budete zaregistrováni a vytvoření nové nabídky nebo pracující na existující,

- [Přihlaste se na portál Cloud Partner](https://cloudpartner.azure.com) k vytvoření nebo dokončení vaší nabídky
