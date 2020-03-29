---
title: Průvodce publikováním nabídek aplikací Azure aplikací
description: Tento článek popisuje požadavky na publikování spravované aplikace na webu Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: dsindona
ms.openlocfilehash: 38323ecfee69460b16542a3e004513f6a7389c41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288406"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure Aplikace: Průvodce publikováním nabídek spravovaných aplikací

Spravovaná aplikace je jedním z hlavních způsobů publikování řešení na marketplace. V této příručce porozumíte požadavkům na tuto nabídku. 

Jedná se o nabídky transakcí, které jsou nasazeny a účtovány prostřednictvím webu Marketplace. Výzva k akci, kterou uživatel vidí, je "Get It Now".

Použijte aplikaci Azure: typ nabídky spravované aplikace, když jsou vyžadovány následující podmínky:
- Nasadíte buď řešení založené na předplatném pro zákazníka pomocí virtuálního počítače nebo celé řešení založené na IaaS.
- Vy nebo váš zákazník požadujete, aby řešení bylo řízeno partnerem.

>[!NOTE]
>Partnerem může být například zprostředkovatel si nebo spravovaných služeb (MSP).  

## <a name="managed-application-offer"></a>Nabídka spravovaných aplikací

|Požadavky |Podrobnosti  |
|---------|---------|
|Nasazené do předplatného Azure zákazníka | Spravované aplikace musí být nasazeny v předplatném zákazníka a mohou být spravovány třetí stranou. | 
|Fakturace a měření    |  Prostředky se zřídí v předplatném Azure zákazníka. Virtuální počítače s průběžnými platbami (PAYGO) budou se zákazníkem obchodovány prostřednictvím Microsoftu, které se budou účtovat prostřednictvím předplatného Azure (PAYGO) zákazníka. <br> V případě vlastní licence, zatímco společnost Microsoft bude účtovat náklady na infrastrukturu vzniklé v rámci předplatného zákazníka, provedete převod licenčních poplatků za software přímo zákazníkovi.        |
|Virtuální pevný disk kompatibilní s Azure (VHD)    |   Virtuální počítače musí být postavené na Windows nebo Linuxu.<ul> <ul> <li>Další informace o vytváření virtuálního pevného disku Linuxu najdete v [tématu Linux distribuce schválila v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Další informace o vytvoření virtuálního pevného disku systému Windows naleznete [v tématu Vytvoření virtuálního pevného disku kompatibilního s Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Spravované aplikace musí být nasaditelné prostřednictvím webu Marketplace. Pokud se jedná o komunikaci se zákazníky, měli byste se po povolení sdílení zájemců ooslovit se zájemci.  

>[!Note]
>Přihlášení partnerského kanálu zprostředkovatelů cloudových řešení (CSP) je nyní k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím partnerských kanálů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení.](./cloud-solution-providers.md)

## <a name="next-steps"></a>Další kroky
Pokud jste tak ještě neučinili, 

- [Zaregistrujte se](https://azuremarketplace.microsoft.com/sell) na trhu.

Pokud jste zaregistrováni a vytváříte novou nabídku nebo pracujete na stávající nabídce,

- [Přihlaste se k portálu cloudových partnerů](https://cloudpartner.azure.com) a vytvořte nebo dokončete svou nabídku.
