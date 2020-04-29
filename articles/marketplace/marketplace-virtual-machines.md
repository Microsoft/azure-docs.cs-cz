---
title: Průvodce publikováním nabídky pro virtuální počítače pro Azure Marketplace
description: Tento článek popisuje požadavky na publikování virtuálního počítače a bezplatné zkušební verze softwaru pro nasazení z webu Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687503"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Průvodce publikováním nabídky virtuálních počítačů

Image virtuálních počítačů jsou jedním z hlavních způsobů publikování řešení v Azure Marketplace. Tento průvodce vám pomůže pochopit požadavky této nabídky. 

Jedná se o nabídky transakcí, které se nasazují a účtují prostřednictvím webu Marketplace. Volání akce, které uživatel vidí, je "získat hned".

## <a name="free-trial"></a>Bezplatná zkušební verze 

Uživatelům můžete zajistit, aby vaši nabídku otestovali, a to tak, že při použití modelu fakturace přináší vlastní licenci (BYOL) přístup k omezeným softwarovým licencím. 

## <a name="test-drive"></a>Testovací verze

Nasadíte jeden nebo víc virtuálních počítačů prostřednictvím IaaS (Infrastructure-as-a-Service) nebo aplikací typu software jako služba (SaaS). Výhodou pro publikování testovacích jednotek je Automatické zřizování virtuálního počítače nebo celého řešení, které vedlo průvodcem hostovaným partnerem. Testovací jednotka poskytuje vašemu zákazníkovi žádné dodatečné náklady. Zákazník nemusí být stávající zákazník Azure, který by se mohl zapojit do zkušebního prostředí. 

Pokud chcete začít, kontaktujte nás na adrese [amp-testdrive](mailto:amp-testdrive@microsoft.com) . 

|Požadavky  |Podrobnosti |
|---------|---------|
| Máte aplikaci Marketplace   |    Jeden nebo více virtuálních počítačů prostřednictvím IaaS nebo SaaS.      |

## <a name="interactive-demo"></a>Interaktivní ukázka

Pomocí interaktivní ukázky můžete svým zákazníkům poskytovat zkušenosti s vaším řešením. Výhodou možnosti publikování interaktivních ukázek je, že zadáváte zkušební prostředí bez komplikovaného zřizování komplexního řešení. 

## <a name="virtual-machine-offer"></a>Nabídka virtuálních počítačů

Typ nabídky virtuálního počítače použijte, když nasadíte virtuální zařízení do předplatného přidruženého k vašemu zákazníkovi. Virtuální počítače jsou díky tomu plně aktivní a využívají licenční modely (BYOL) s průběžnými platbami. Společnost Microsoft hostuje transakci obchodu a účtuje vaše zákazníky vaším jménem. Výhody používání upřednostňovaného platebního vztahu mezi zákazníkem a Microsoftem, včetně všech smluv Enterprise, získáte s výhodou.

> [!NOTE]
> V tuto chvíli se finanční závazky spojené s smlouva Enterprise dají použít k používání Azure na vašem VIRTUÁLNÍm počítači, ale ne k licenčním poplatkům za software.  
> 
> [!NOTE]
> Můžete omezit zjišťování a nasazování virtuálního počítače na konkrétní skupinu zákazníků publikováním image a cen jako soukromé nabídky. Soukromý nabízí možnost vytvářet exkluzivní nabídky pro nejbližší zákazníky a nabízet přizpůsobený software a požadavky. Přizpůsobené výrazy vám umožňují zvýraznit celou řadu scénářů, včetně jejich využívání se specializovanými cenami a podmínkami, a také prvotním přístupem k omezenému softwaru pro vydání. Soukromé nabídky vám umožňují poskytovat konkrétní ceny nebo produkty omezené sadě zákazníků tím, že pomocí těchto údajů vytvoří novou SKLADOVOU položku.  
> *   Další informace o soukromých nabídkách najdete na stránce s informacemi o Azure Marketplace, které najdete na adrese [Azure.Microsoft.com/blog/Private-offers-on-Azure-Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Požadavek | Podrobnosti |  
|:--- |:--- | 
| Fakturace a měření | Váš virtuální počítač musí podporovat BYOL nebo měsíční fakturaci s průběžnými platbami. |  
| Virtuální pevný disk kompatibilní s Azure (VHD) | Virtuální počítače musí být postavené na Windows nebo Linux. <ul> <li>Další informace o vytvoření virtuálního pevného disku se systémem Linux najdete v tématu [distribuce systému Linux schválené v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Další informace o vytvoření virtuálního pevného disku s Windows najdete v tématu [Vytvoření virtuálního pevného disku kompatibilního s Azure](./partner-center-portal/azure-vm-create-offer.md).</li> </ul> |  

>[!Note]
>Výslovný souhlas kanálu pro partnery Cloud Solution Providers (CSP) je teď k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení](./cloud-solution-providers.md) .

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, 

- [Seznamte](https://azuremarketplace.microsoft.com/sell) se s Marketplace.

Pokud jste zaregistrováni a vytváříte novou nabídku nebo pracujete na stávajícím typu,

- [Přihlaste se do partnerského centra](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) a vytvořte nebo dokončete vaši nabídku.
- Další informace najdete v tématu [Vytvoření nabídky virtuálního počítače](./partner-center-portal/azure-vm-create-offer.md) .
