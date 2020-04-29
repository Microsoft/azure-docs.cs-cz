---
title: Průvodce publikováním nabídky aplikací spravovaných aplikací Azure | Azure Marketplace
description: Tento článek popisuje požadavky pro publikování spravované aplikace v Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084868"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Aplikace Azure: požadavky na publikování nabídky spravované aplikace

Tento článek vysvětluje požadavky na typ nabídky spravovaných aplikací, což je jeden ze způsobů, jak publikovat nabídku aplikací Azure v Azure Marketplace. Spravované aplikace jsou nabídky v jazyce Transact, které se nasazují a účtují prostřednictvím Azure Marketplace. Volání akce, které uživatel vidí, je "získat hned".

Pokud jsou potřeba následující podmínky, použijte typ nabídky spravované aplikace:

- K nasazení řešení založeného na předplatném pro zákazníka můžete použít buď virtuální počítač, nebo celé řešení založené na IaaS.
- Vy nebo váš zákazník požadujete, aby bylo řešení spravované partnerem.

>[!NOTE]
>Partner může být například typu SI nebo poskytovatele spravované služby (MSP).  

## <a name="managed-application-offer"></a>Nabídka spravovaných aplikací

|Požadavky |Podrobnosti  |
|---------|---------|
|Nasazené do předplatného Azure zákazníka | Spravované aplikace musí být nasazené v předplatném zákazníka a můžou je spravovat třetí stranou. |
|Fakturace a měření    |  Prostředky se zřídí v předplatném Azure zákazníka. Virtuální počítače s průběžnými platbami (PAYGO) se budou s zákazníky účtovat prostřednictvím Microsoftu, které se účtují prostřednictvím předplatného Azure zákazníka (PAYGO). <br> V případě používání vlastní licence se společnost Microsoft bude účtovat náklady na infrastrukturu, které se účtují v rámci předplatného zákazníka. poplatky za licence na software budete účtovat přímo zákazníkovi.        |
|Virtuální pevný disk kompatibilní s Azure (VHD)    |   Virtuální počítače musí být postavené na Windows nebo Linux.<ul> <ul> <li>Další informace o vytvoření virtuálního pevného disku se systémem Linux najdete v tématu [distribuce systému Linux schválené v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Další informace o vytvoření virtuálního pevného disku s Windows najdete v tématu věnovaném [Vytvoření nabídky aplikací Azure](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Spravované aplikace musí být možné nasadit prostřednictvím webu Marketplace. Pokud je záležitost komunikace s zákazníkem, měli byste se obrátit na zúčastněné zákazníky až po povolení sdílení potenciálních zákazníků.  

>[!Note]
>Výslovný souhlas kanálu pro partnery Cloud Solution Providers (CSP) je teď k dispozici. Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [poskytovatelé Cloud Solution Provider](./cloud-solution-providers.md).

## <a name="next-steps"></a>Další kroky

- Pokud jste to ještě neudělali, [Přečtěte](https://azuremarketplace.microsoft.com/sell) si o Azure Marketplace.
- [Přihlaste se do partnerského centra](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) a vytvořte nebo dokončete vaši nabídku.
- Pokud chcete získat další informace, [vytvořte nabídku aplikace Azure](./partner-center-portal/create-new-azure-apps-offer.md) .
