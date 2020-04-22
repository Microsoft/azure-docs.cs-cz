---
title: Průvodce publikováním nabídek virtuálních strojů pro Azure Marketplace
description: Tento článek popisuje požadavky na publikování virtuálního počítače a zkušební verze bez softwaru, které mají být nasazeny z webu Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687503"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Průvodce publikováním nabídek virtuálních strojů

Image virtuálních strojů jsou jedním z hlavních způsobů publikování řešení na Azure Marketplace. V této příručce porozumíte požadavkům na tuto nabídku. 

Jedná se o nabídky transakcí, které jsou nasazeny a účtovány prostřednictvím webu Marketplace. Výzva k akci, kterou uživatel vidí, je "Get It Now".

## <a name="free-trial"></a>Bezplatná zkušební verze 

Můžete zajistit, aby uživatelé otestovali vaši nabídku, a to tak, že při použití fakturačního modelu Bring Your Own License (BYOL) získáte přístup k omezeným softwarovým licencím s omezenou platností. 

## <a name="test-drive"></a>Testovací verze

Nasadíte jeden nebo více virtuálních počítačů prostřednictvím infrastruktury jako služby (IaaS) nebo softwaru jako služby (SaaS). Výhodou možnosti publikování testovací jednotky je automatické zřizování virtuálního počítače nebo celého řešení vedeného prohlídkou s průvodcem hostovací s partnerem. Zkušební jízda poskytuje hodnocení bez dalších nákladů pro zákazníka. Váš zákazník nemusí být stávající zákazník Azure, aby se zapojil do zkušebního prostředí. 

Kontaktujte nás na [amp-testdrive,](mailto:amp-testdrive@microsoft.com) abyste mohli začít. 

|Požadavky  |Podrobnosti |
|---------|---------|
| Máte aplikaci Marketplace   |    Jeden nebo více virtuálních počítačů přes IaaS nebo SaaS.      |

## <a name="interactive-demo"></a>Interaktivní ukázka

Pomocí interaktivní ukázky poskytujete zákazníkům řízené prostředí vašeho řešení. Výhodou možnosti interaktivního demo publikování je, že poskytujete zkušební prostředí bez složitého zřizování komplexního řešení. 

## <a name="virtual-machine-offer"></a>Nabídka virtuálního počítače

Typ nabídky virtuálního počítače použijte při nasazení virtuálního zařízení do předplatného přidruženého k zákazníkovi. Virtuální počítače jsou plně dostupné pro commerce pomocí licenčních modelů s průběžnými platbami nebo s vlastní licencí (BYOL). Společnost Microsoft hostuje obchodní transakci a účtuje zákazníkovi vaším jménem. Můžete využít upřednostňovaný platební vztah mezi zákazníkem a společností Microsoft, včetně všech smluv Enterprise.

> [!NOTE]
> V současné době se peněžní závazky spojené se smlouvou Enterprise mohou použít proti využití virtuálního počítače azure, ale ne proti licenčním poplatkům za software.  
> 
> [!NOTE]
> Zjišťování a nasazování virtuálního počítače můžete omezit na konkrétní sadu zákazníků publikováním image a ceny jako soukromou nabídku. Soukromé nabídky odemknout možnost vytvářet exkluzivní nabídky pro své nejbližší zákazníky a nabídnout vlastní software a podmínky. Přizpůsobené termíny umožňují zvýraznit celou řadu scénářů, včetně dohod vedených v terénu se specializovanými cenami a podmínkami, stejně jako včasný přístup k softwaru s omezenou verzí. Soukromé nabídky umožňují poskytnout konkrétní ceny nebo produkty omezené sadě zákazníků vytvořením nové skladové položky s těmito podrobnostmi.  
> *   Další informace o soukromých nabídkách najdete na stránce Soukromé nabídky na Azure Marketplace, která se nachází na [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Požadavek | Podrobnosti |  
|:--- |:--- | 
| Fakturace a měření | Váš virtuální počítač musí podporovat měsíční fakturaci BYOL nebo průběžných plateb. |  
| Virtuální pevný disk kompatibilní s Azure (VHD) | Virtuální počítače musí být postavené na Windows nebo Linuxu. <ul> <li>Další informace o vytváření virtuálního pevného disku Linuxu najdete v [tématu Linux distribuce schválila v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Další informace o vytvoření virtuálního pevného disku systému Windows naleznete [v tématu Vytvoření virtuálního pevného disku kompatibilního s Azure](./partner-center-portal/azure-vm-create-offer.md).</li> </ul> |  

>[!Note]
>Přihlášení partnerského kanálu zprostředkovatelů cloudových řešení (CSP) je nyní k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím partnerských kanálů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení.](./cloud-solution-providers.md)

## <a name="next-steps"></a>Další kroky

Pokud jste tak ještě neučinili, 

- [Přečtěte si](https://azuremarketplace.microsoft.com/sell) informace o tržišti.

Pokud jste zaregistrováni a vytváříte novou nabídku nebo pracujete na stávající nabídce,

- [Chcete-li](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) vytvořit nebo dokončit svou nabídku, přihlaste se do Centra partnerů.
- Další informace najdete [v tématu vytvoření nabídky virtuálních strojů.](./partner-center-portal/azure-vm-create-offer.md)
