---
title: Průvodce publikováním nabídek aplikací pro aplikace Azure | Azure Marketplace
description: Tento článek popisuje požadavky na publikování spravované aplikace na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084868"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Aplikace Azure: Požadavky na publikování nabídky spravovaných aplikací

Tento článek vysvětluje požadavky na typ nabídky spravované aplikace, což je jeden ze způsobů, jak publikovat nabídku aplikace Azure na Azure Marketplace. Spravované aplikace jsou nabídky pro transakt, které se nasazují a účtují prostřednictvím Azure Marketplace. Výzva k akci, kterou uživatel vidí, je "Get It Now".

Typ nabídky spravované aplikace použijte v případě, že jsou požadovány následující podmínky:

- Nasadíte řešení založené na předplatném pro zákazníka pomocí virtuálního počítače nebo celéřešení založené na IaaS.
- Vy nebo váš zákazník požadujete, aby řešení bylo řízeno partnerem.

>[!NOTE]
>Partnerem může být například zprostředkovatel si nebo spravovaných služeb (MSP).  

## <a name="managed-application-offer"></a>Nabídka spravovaných aplikací

|Požadavky |Podrobnosti  |
|---------|---------|
|Nasazené do předplatného Azure zákazníka | Spravované aplikace musí být nasazeny v předplatném zákazníka a mohou být spravovány třetí stranou. |
|Fakturace a měření    |  Prostředky se zřídí v předplatném Azure zákazníka. Virtuální počítače s průběžnými platbami (PAYGO) budou se zákazníkem obchodovány prostřednictvím Microsoftu, které se budou účtovat prostřednictvím předplatného Azure (PAYGO) zákazníka. <br> V případě vlastní licence, zatímco společnost Microsoft bude účtovat náklady na infrastrukturu vzniklé v rámci předplatného zákazníka, provedete převod licenčních poplatků za software přímo zákazníkovi.        |
|Virtuální pevný disk kompatibilní s Azure (VHD)    |   Virtuální počítače musí být postavené na Windows nebo Linuxu.<ul> <ul> <li>Další informace o vytváření virtuálního pevného disku Linuxu najdete v [tématu Linux distribuce schválila v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Další informace o vytvoření virtuálního pevného disku systému Windows najdete [v tématu vytvoření nabídky aplikací Azure](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Spravované aplikace musí být nasaditelné prostřednictvím webu Marketplace. Pokud se jedná o komunikaci se zákazníky, měli byste se po povolení sdílení zájemců ooslovit se zájemci.  

>[!Note]
>Přihlášení partnerského kanálu zprostředkovatelů cloudových řešení (CSP) je nyní k dispozici. Další informace o marketingu vaší nabídky prostřednictvím partnerských kanálů Microsoft CSP naleznete v [tématu Poskytovatelé cloudových řešení](./cloud-solution-providers.md).

## <a name="next-steps"></a>Další kroky

- Pokud jste to ještě neudělali, [přečtěte si informace](https://azuremarketplace.microsoft.com/sell) o Azure Marketplace.
- [Chcete-li](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) vytvořit nebo dokončit svou nabídku, přihlaste se do Centra partnerů.
- [Vytvořte nabídku aplikací Azure](./partner-center-portal/create-new-azure-apps-offer.md) pro další informace.
