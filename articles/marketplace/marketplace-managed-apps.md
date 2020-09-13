---
title: Průvodce publikováním nabídky aplikací spravovaných aplikacemi Azure – Azure Marketplace
description: Tento článek popisuje požadavky pro publikování spravované aplikace v Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: 10ca7602dd3cf5a80e371ceda845977bcd8f218a
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484235"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Průvodce publikováním pro spravované aplikace Azure

Nabídka *spravované aplikace* Azure je jedním ze způsobů, jak publikovat aplikaci azure v Azure Marketplace. Spravované aplikace jsou nabídky v jazyce Transact, které se nasazují a účtují prostřednictvím Azure Marketplace. Možnost výpisu, kterou uživatel vidí, se *teď získá*.

Tento článek popisuje požadavky na typ nabídky spravované aplikace.

Typ nabídky spravovaných aplikací použijte při těchto podmínkách:

- Nasazujete řešení založené na předplatném pro zákazníka pomocí virtuálního počítače (VM) nebo celého řešení založeného na infrastruktuře jako služby (IaaS).
- Vy nebo váš zákazník vyžaduje, aby bylo řešení spravované partnerem.

>[!NOTE]
>Partnerem může být například systémový integrátor nebo poskytovatel spravované služby (MSP).  

## <a name="managed-application-offer-requirements"></a>Požadavky na nabídku spravované aplikace

|Požadavky |Podrobnosti  |
|---------|---------|
|Předplatné Azure | Spravované aplikace musí být nasazeny do předplatného zákazníka, ale mohou být spravovány třetí stranou. |
|Fakturace a měření    |  Prostředky jsou k dispozici v předplatném Azure zákazníka. Virtuální počítače, které používají platební model průběžných plateb, se účtují u zákazníka přes Microsoft a účtují se prostřednictvím předplatného Azure zákazníka. <br><br> V případě virtuálních počítačů využívajících vlastní licenci společnost Microsoft účtuje náklady na infrastrukturu, které se účtují v rámci předplatného zákazníka, ale poplatky za licence na software se zákazníky účtují přímo.        |
|Virtuální pevný disk kompatibilní s Azure (VHD)    |   Virtuální počítače musí být postavené na Windows nebo Linux.<br><br>Další informace o vytvoření virtuálního pevného disku se systémem Linux najdete v tématu [distribuce systému Linux schválené v Azure](../virtual-machines/linux/endorsed-distros.md).<br><br>Další informace o vytvoření virtuálního pevného disku s Windows najdete v tématu věnovaném [Vytvoření nabídky aplikací Azure](./partner-center-portal/create-new-azure-apps-offer.md). |

---

> [!NOTE]
> Spravované aplikace je potřeba nasadit prostřednictvím Azure Marketplace. Pokud je komunikace s zákazníky zájmem, zajímá se zákazníkům, kteří si zapnuli sdílení potenciálních zákazníků.  

> [!Note]
> Nyní je k dispozici souhlas kanálu pro partnery poskytovatele Cloud Solution Provider (CSP). Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [poskytovatelé Cloud Solution Provider](./cloud-solution-providers.md).

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, přečtěte si, jak [rozšířit cloudovou firmu pomocí Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrace a zahájení práce v partnerském centru:

- [Přihlaste se do partnerského centra](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) a vytvořte nebo dokončete vaši nabídku.
- Další informace najdete v tématu [Vytvoření nabídky aplikací Azure](./partner-center-portal/create-new-azure-apps-offer.md) .
