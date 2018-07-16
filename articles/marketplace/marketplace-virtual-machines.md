---
title: Průvodce pro Azure Marketplace publikováním nabídky virtuálních počítačů
description: Tento článek popisuje požadavky na virtuální počítač a bezplatnou zkušební verzi softwaru k nasazení z webu Marketplace publikovat.
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
ms.openlocfilehash: 5508b5943e116545297d91e85621d2a11a635299
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059764"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Průvodce publikováním nabídky virtuálních počítačů

Image virtuálních počítačů jsou jedním z hlavních způsobů, jak publikování řešení na webu Azure Marketplace. Tento průvodce vám porozumět všem požadavkům pro v rámci této nabídky. 

Jedná se o transakci nabídek, které se nasazují a prostřednictvím webu Marketplace účtují. Volání akce, které uživatel uvidí je "Získat."

## <a name="free-trial"></a>Bezplatná zkušební verze 

Můžete uspořádat uživatelům přístup k omezené období softwarových licencí, při použití modelu používání vlastní licence (BYOL) blling otestovat vaši nabídku. Níže jsou uvedeny požadavky pro nasazení v rámci této nabídky. 

|Požadavky  |Podrobnosti  |
|---------|---------|
|Období bezplatné zkušební verze a zkušební verze     |   Zákazník může aplikaci vyzkoušet zdarma po omezenou dobu. Váš zákazník není potřeba platit žádné poplatky za licence nebo předplatné pro vaši nabídku. Vaši zákazníci nejsou nutné k úhradě základní Microsoft první strany produktu nebo službě. Všechny možnosti zkušebních verzí se nasadí do vašeho předplatného Azure. Máte výhradní kontrolu nad optimalizaci nákladů a správu. Můžete si zvolit bezplatnou zkušební verzi nebo interaktivní ukázku. Bez ohledu na to, které zvolíte musí si bezplatnou zkušební verzi poskytují zákazník přednastaveným množství čas na vyzkoušení nabídky bez dalších poplatků.|
|Dají se snadno konfigurovat a připravené k použití řešení    |  Snadné a rychlé ke konfiguraci a nastavení, musí být vaše aplikace.       |
|Dostupnost / doba provozu    |    Aplikace SaaS nebo platformy musí mít minimálně 99,9 % dostupnost.     |
|Azure Active Directory     |    Nabídky musíte povolit, že Azure Active Directory (Azure AD) federované jednotné přihlašování (SSO) (Azure AD federované jednotné přihlašování) pomocí souhlasu povolena.     |

## <a name="test-drive"></a>Test Drive

Můžete nasadit jeden nebo více virtuálních počítačů prostřednictvím infrastructure-as-a-service(IaaS) nebo aplikací SaaS. Výhoda test jednotky, kterou možnost publikování je automatického zřizování virtuálního počítače nebo celé řešení vedené partnerem hostované průvodce. Testovací verze poskytuje zákazníkům vyhodnocení bez dalších poplatků. Zákazník nemusí být stávající zákazník Azure provozovat zkušební prostředí s. 

E-mailu amp-testdrive@microsoft.com začít. 

|Požadavky  |Podrobnosti |
|---------|---------|
| Budete mít aplikace Marketplace   |    Jeden nebo více virtuálním počítačům prostřednictvím IaaS nebo platformám SaaS.      |

## <a name="interactive-demo"></a>Interaktivní ukázka

Zajištění asistovaného prostředí vašeho řešení zákazníkům s použitím interaktivní ukázku. Výhodou interaktivní ukázka publikování možnost je poskytnout zkušební prostředí, aniž by složité zřizování komplexní řešení. 

## <a name="virtual-machine-offer"></a>Nabídky virtuálních počítačů

Když nasadíte virtuální zařízení na předplatné spojené s vaší zákaznickou, použijte typ nabídky virtuálního počítače. Virtuální počítače jsou plně commerce povolit pomocí s průběžnými platbami nebo přeneste svůj – používání vlastní licence (BYOL) licenční modely. Společnost Microsoft je hostitelem transakce obchodování a vaše zákazníkovi vaším jménem. Získáte výhody pomocí upřednostňované platby vztahu mezi zákazníkem a společnosti Microsoft, včetně žádné smlouvy Enterprise.

>[!NOTE]
>V tuto chvíli podporuje peněžní závazky spojené se smlouvou Enterprise budou moct použít proti využití Azure pro váš virtuální počítač, ale ne před vaší licenční poplatky za software.  

>[!NOTE]
>Budete moct omezit zjišťování a nasazení virtuálního počítače na konkrétní sadu odběratelů tak, že publikování image a ceny soukromé nabídky. Soukromé nabídky odemknout možnost pro vytvoření exkluzivní nabídky pro vaše zákazníky nejvíce a nabízí vlastní software a podmínky. Vlastní podmínky umožňují zvýrazněte širokou škálu scénářů, včetně vedené pole se zabývá speciální ceny a podmínky stejně jako dřívější přístup k softwaru omezené vydání. Soukromé nabídky Povolit vám umožní poskytovat konkrétní cenu nebo produkty na omezenou sadu zákazníků tím, že vytvoříte novou skladovou Položku s těmito podrobnostmi.  
*   Další informace o soukromých nabídky, navštivte soukromé nabídky na webu Azure Marketplace stránky umístěného v [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Požadavek | Podrobnosti |  
|:--- |:--- | 
| K fakturaci a měření | Váš virtuální počítač musí podporovat BYOL či s průběžnými platbami měsíční fakturací. |  
| Kompatibilní s Azure virtuálního pevného disku (VHD) | Virtuální počítače musí být sestaveny jako ve Windows nebo Linuxu.<ul> <li>Další informace o vytváření linuxového virtuálního pevného disku najdete vytvořením kompatibilní s Azure virtuálního pevného disku (založené na Linuxu) části umístění [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Další informace o vytváření virtuálního pevného disku Windows najdete vytvořením kompatibilní s Azure virtuálního pevného disku (založený na Windows) části umístění [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Další kroky

Pokud jste tak již neučinili, 

- [Zaregistrujte](https://azuremarketplace.microsoft.com/sell) na webu Marketplace

Pokud budete zaregistrováni a vytvoření nové nabídky nebo pracující na existující,

- [Přihlaste se na portál Cloud Partner](https://cloudpartner.azure.com) k vytvoření nebo dokončení vaší nabídky
