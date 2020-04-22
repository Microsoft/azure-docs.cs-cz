---
title: Průvodce publikováním nabídek aplikací Azure aplikací
description: Tento článek popisuje požadavky na publikování spravované aplikace na webu Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 56281ff8faba6b4b950334e2b0018d48c8e7aeb3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687561"
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
|Virtuální pevný disk kompatibilní s Azure (VHD)    |   Virtuální počítače musí být postavené na Windows nebo Linuxu.<ul> <ul> <li>Další informace o vytváření virtuálního pevného disku Linuxu najdete v [tématu Linux distribuce schválila v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Další informace o vytvoření virtuálního pevného disku systému Windows najdete [v tématu vytvoření nabídky aplikací Azure](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Spravované aplikace musí být nasaditelné prostřednictvím webu Marketplace. Pokud se jedná o komunikaci se zákazníky, měli byste se po povolení sdílení zájemců ooslovit se zájemci.  

>[!Note]
>Přihlášení partnerského kanálu zprostředkovatelů cloudových řešení (CSP) je nyní k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím partnerských kanálů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení.](./cloud-solution-providers.md)

## <a name="next-steps"></a>Další kroky

Pokud jste tak ještě neučinili, 

- [Přečtěte si](https://azuremarketplace.microsoft.com/sell) informace o tržišti.

Chcete-li se zaregistrovat v Partnerském centru, začněte vytvářet novou nabídku nebo pracovat na existující nabídce:

- [Chcete-li](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) vytvořit nebo dokončit svou nabídku, přihlaste se do Centra partnerů.
- Další informace najdete [v tématu vytvoření nabídky aplikací Azure.](./partner-center-portal/create-new-azure-apps-offer.md)
