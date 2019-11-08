---
title: Průvodce publikováním nabídky aplikací spravovaných aplikacemi Azure
description: Tento článek popisuje požadavky na publikování spravované aplikace na webu Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/14/2018
ms.author: v-qiwe
ms.openlocfilehash: bdb2a54748c734d7b44dfa0b1f106d37e817f80e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822931"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplikace Azure: Průvodce publikováním nabídky spravovaných aplikací

Spravovaná aplikace je jedním z hlavních způsobů publikování řešení na webu Marketplace. Tento průvodce vám pomůže pochopit požadavky této nabídky. 

Jedná se o nabídky transakcí, které se nasazují a účtují prostřednictvím webu Marketplace. Volání akce, které uživatel vidí, je "získat hned".

Použití aplikace Azure: typ nabídky spravované aplikace, pokud jsou potřeba následující podmínky:
- K nasazení řešení založeného na předplatném pro zákazníka můžete použít buď virtuální počítač, nebo celé řešení založené na IaaS.
- Vy nebo váš zákazník požadujete, aby bylo řešení spravované partnerem.

>[!NOTE]
>Partner může být například typu SI nebo poskytovatele spravované služby (MSP).  

## <a name="managed-application-offer"></a>Nabídka spravovaných aplikací

|Požadavky |Podrobnosti  |
|---------|---------|
|Nasazené do předplatného Azure zákazníka | Spravované aplikace musí být nasazené v předplatném zákazníka a můžou je spravovat třetí stranou. | 
|Fakturace a měření    |  Prostředky se zřídí v předplatném Azure zákazníka. Virtuální počítače s průběžnými platbami (PAYGO) se budou s zákazníky účtovat prostřednictvím Microsoftu, které se účtují prostřednictvím předplatného Azure zákazníka (PAYGO). <br> V případě používání vlastní licence se společnost Microsoft bude účtovat náklady na infrastrukturu, které se účtují v rámci předplatného zákazníka. poplatky za licence na software budete účtovat přímo zákazníkovi.        |
|Virtuální pevný disk kompatibilní s Azure (VHD)    |   Virtuální počítače musí být postavené na Windows nebo Linux.<ul> <ul> <li>Další informace o vytvoření virtuálního pevného disku se systémem Linux najdete v tématu [distribuce systému Linux schválené v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Další informace o vytvoření virtuálního pevného disku s Windows najdete v tématu [Vytvoření virtuálního pevného disku kompatibilního s Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Spravované aplikace musí být možné nasadit prostřednictvím webu Marketplace. Pokud je záležitost komunikace s zákazníkem, měli byste se obrátit na zúčastněné zákazníky až po povolení sdílení potenciálních zákazníků.  

>[!Note]
>Výslovný souhlas kanálu pro partnery Cloud Solution Providers (CSP) je teď k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení](./cloud-solution-providers.md) .

## <a name="next-steps"></a>Další kroky
Pokud jste to ještě neudělali, 

- [Zaregistrujte](https://azuremarketplace.microsoft.com/sell) se na webu Marketplace.

Pokud jste zaregistrováni a vytváříte novou nabídku nebo pracujete na stávajícím typu,

- [Přihlaste se k portál partnerů cloudu](https://cloudpartner.azure.com) a vytvořte nebo dokončete vaši nabídku.
