---
title: Průvodce publikováním pro nabídky virtuálního počítače na Azure Marketplace
description: Tento článek popisuje požadavky pro publikování virtuálního počítače a bezplatné zkušební verze softwaru pro nasazení z Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: e0f0f0fdb75961f3ea9ed90e1684ff5ecd2a792d
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961536"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>Průvodce publikováním pro nabídky virtuálních počítačů

Publikování imagí virtuálních počítačů je jedním z hlavních způsobů publikování řešení do Azure Marketplace. Tento průvodce vám pomůže pochopit požadavky tohoto typu nabídky. 

Nabídky virtuálních počítačů jsou nabídky transakcí, které se nasazují a účtují prostřednictvím Azure Marketplace. Volání akce, které uživatel vidí, se *teď dostane*.

## <a name="free-trial"></a>Bezplatná zkušební verze 

Pokud chcete uživatelům zajistit, aby vaši nabídku otestovali, měli byste při použití modelu fakturace BYOL (Přineste si vlastní licenci) získat přístup k omezeným softwarovým licencím. 

## <a name="test-drive"></a>Testovací jednotka

Jeden nebo více virtuálních počítačů můžete nasadit prostřednictvím aplikací IaaS (infrastruktura jako služba) nebo softwaru jako služby (SaaS). Výhodou možnosti publikování *testovacích jednotek* je automatizovaná instalace virtuálního počítače nebo celého řešení, které vedlo průvodcem hostovaným partnerem. Testovací jednotka umožňuje zákazníkům vyhodnotit virtuální počítače bez dalších nákladů. Zákazník nemusí být stávající zákazník Azure, který by se mohl zapojit do zkušebního prostředí. 

Pokud chcete začít, kontaktujte nás e-mailem na adrese [amp-testdrive](mailto:amp-testdrive@microsoft.com). 

|Požadavky  |Podrobnosti |
|---------|---------|
| Máte aplikaci Azure Marketplace   |  Jeden nebo více virtuálních počítačů prostřednictvím IaaS nebo SaaS.      |

## <a name="interactive-demo"></a>Interaktivní ukázka

Pomocí této nabídky poskytnete svým zákazníkům zkušenosti s vaším řešením pomocí interaktivní ukázky. Výhodou interaktivního možnosti publikování ukázek je, že můžete nabízet zkušební prostředí, aniž byste museli poskytovat složitou instalaci komplexního řešení. 

## <a name="virtual-machine-offer"></a>Nabídka virtuálních počítačů

Typ nabídky *virtuálního počítače* použijte, když nasadíte virtuální zařízení do předplatného, které je přidružené k vašemu zákazníkovi. Virtuální počítače jsou plně aktivní a využívají licenční modely (BYOL) s průběžnými platbami nebo s vlastními licencemi. Společnost Microsoft hostuje transakci obchodu a účtuje vaše zákazníky vaším jménem. Výhody používání upřednostňovaného platebního vztahu mezi zákazníkem a Microsoftem, včetně všech smluv Enterprise, získáte s výhodou.

> [!NOTE]
> V tuto chvíli se finanční závazky spojené s smlouva Enterprise dají použít k používání Azure vašeho virtuálního počítače, ale ne k licenčním poplatkům za software.  
> 
> [!NOTE]
> Zjišťování a nasazování virtuálního počítače můžete omezit na konkrétní sadu zákazníků publikováním image a cen jako soukromé nabídky. Soukromý nabízí možnost vytvářet exkluzivní nabídky pro nejbližší zákazníky a nabízet přizpůsobený software a požadavky. Přizpůsobené výrazy vám umožňují zvýraznit celou řadu scénářů, včetně jejich využívání se specializovanými cenami a podmínkami, a také prvotním přístupem k omezenému softwaru pro vydání. Soukromé nabídky vám umožňují poskytovat konkrétní ceny nebo produkty omezené sadě zákazníků tím, že pomocí těchto údajů vytvoří novou SKLADOVOU položku.  
>
> Další informace najdete v tématu [soukromé nabídky na Azure Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Požadavek | Podrobnosti |  
|:--- |:--- | 
| Fakturace a měření | Váš virtuální počítač musí podporovat BYOL nebo měsíční fakturaci s průběžnými platbami. |  
| Virtuální pevný disk kompatibilní s Azure (VHD) | Virtuální počítače musí být postavené na Windows nebo Linux. Další informace o vytvoření virtuálního pevného disku najdete v tématu: <ul> <li>[Distribuce systému Linux schválená v Azure](../virtual-machines/linux/endorsed-distros.md) (pro virtuální pevné disky se systémem Linux).</li> <li>[Vytvořte virtuální pevný disk kompatibilní s Azure](./partner-center-portal/azure-vm-create-offer.md) (pro virtuální pevné disky Windows).</li> </ul> |  

>[!Note]
>Výslovný souhlas kanálu partner Cloud Solution Provider (CSP) je nyní k dispozici. Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [poskytovatelé Cloud Solution Provider](./cloud-solution-providers.md).

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, přečtěte si, jak [rozšířit cloudovou firmu pomocí Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrace a zahájení práce v partnerském centru:

- [Přihlaste se do partnerského centra](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) a vytvořte nebo dokončete vaši nabídku.
- Další informace najdete v tématu [Vytvoření nabídky virtuálního počítače](./partner-center-portal/azure-vm-create-offer.md) .
