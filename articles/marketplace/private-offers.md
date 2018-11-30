---
title: Soukromé nabídky | Azure
description: Soukromé nabídky na webu Azure Marketplace pro vydavatele aplikace a služby.
services: Azure, Marketplace, Compute
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/1/2018
ms.author: qianw211
ms.openlocfilehash: 1efe65feaac6e71437958451e8c1a44027495fce
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620009"
---
# <a name="private-offers"></a>Soukromé nabídky

Privátní nabízí na [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) povolit vydavatelé vytvořit skladových položek, které jsou viditelné pouze pro cílové zákazníků.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odemknout organizace se zabývá soukromé nabídky

Podnikoví zákazníci stále používat online tržišť s řešeními najít, zkoušet a kupovat Cloudová řešení. Nyní soukromé nabídky vydavatelé pomocí marketplace soukromě sdílet přizpůsobené solutions cílové zákazníků s možností, které podniky vyžadují:

- *Vyjedná ceny* umožňuje rozšířit slevy a cenách z veřejně dostupných nabídek vypnout seznam vydavatelů.
- *Privátní podmínky a ujednání* povolit vydavatelé přizpůsobení podmínky a ujednání pro konkrétního zákazníka.
- *Specializované konfigurace* nechat vydavatelé přizpůsobit jejich nabídky virtuálních počítačů, aplikací Azure a aplikace SaaS pro potřeby jednotlivých zákazníků. Tato možnost taky dovoluje aby vydavatel poskytl přístup k verzi preview na nové funkce produktu před spuštěním šířeji pro všechny zákazníky.

Soukromé nabídky umožňuje vydavatelům Využijte výhod škálování a globální dostupnosti je z veřejného marketplace, flexibility a kontroly, které jsou potřeba k vyjednávání a poskytovat vlastní zakázek a konfigurace. Společně tyto funkce otevřete dveře k přechodu na podnikový silné z cloudových tržištích.  Podniky nyní můžete kupovat a prodávat, kterou zákazníci očekávají a vyžádání.

Soukromé nabídky jsou teď k dispozici pro virtuální počítač, aplikace Azure (implementovaná jako šablony řešení nebo spravované aplikace) a nabízejí aplikace SaaS. Jako jsou nabídky veřejné, soukromé nabídky můžete vytvořit a spravovat prostřednictvím [portál partnerů cloudu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  Zákazníci lze udělit nebo odvolat přístup k soukromé nabídky během několika minut.

## <a name="creating-private-offers-using-skus-and-plans"></a>Vytvoření soukromého nabízí pomocí skladové položky a plány

Pro *nové nebo existující nabídek s SKU veřejné nebo plány*, vydavatelů můžete snadno vytvářet nové, privátní variace vytvořením nové skladové položky nebo plány a označit jako soukromé.  [Privátní SKU](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) a plány jsou součástí nabídky a jsou jenom viditelné a prodejní cílové zákazníky. Privátní SKU a plány můžete znovu použít základní Image a/nebo nabízejí metadat již publikována pro veřejné SKU nebo plán. Tato možnost umožňuje vydavatelům vytvořit několik privátních variant veřejné nabídka bez nutnosti publikování několika verzí stejné základní image a nabízejí metadat. Pro virtuální počítač Azure aplikace nabídky a pouze když privátní SKU sdílí základní image veřejné SKU, se všechny změny základní image nabídky rozšíří do všechny SKU veřejné a soukromé použití této základní image.

Pro *nových nabídkách, které obsahují pouze privátní SKU nebo plány*, vydavatelů můžete vytvořit jeho nabídky jako žádnou jinou nabídkou a označit jako soukromé plány nebo jednotky SKU. Nabídky, které mají pouze privátní SKU nebo plány nebudou zjistitelné nebo zpřístupněno [Azure Marketplace](https://azuremarketplace.microsoft.com) nebo [webu Azure portal](https://azure.microsoft.com/features/azure-portal/) zákazníky, které nejsou přidružené nabídky.

## <a name="targeting-customers-with-private-offers"></a>Cílení na zákazníky s soukromé nabídky
Pro nové i stávající soukromé nabídky mohou vydavatelé cílit na zákazníky, kteří používají identifikátory předplatného. Vydavatelé pomocí nabídky virtuálního počítače nebo aplikace Azure může omezit dostupnost soukromých SKU pro ID samostatného předplatného Azure nebo nahrát soubor CSV až 20 000 ID předplatného Azure. Při používání soukromý nabídky SaaS aplikace, vydavatele můžete přidružit k omezení dostupnosti privátní plán, ID předplatného Azure nebo ID tenanta pomocí ruční nebo CSV nahrávání přístup.

Po nabídky byl certifikaci a publikování, zákazníci mohou být aktualizovány nebo odebrat z SKU nebo plán během několika minut pomocí funkce synchronizace privátní předplatných. Tato možnost umožňuje vydavatelům rychle a snadno aktualizovat seznam zákazníků, na které se zobrazí privátní SKU nebo plán bez recertifying a opětovné publikování nabídky.

## <a name="deploying-private-offers"></a>Nasazení privátní nabízí

Soukromé nabídky jsou pouze objevitelný prostřednictvím [webu Azure portal](https://azure.microsoft.com/features/azure-portal/) a nejsou k dispozici prostřednictvím [Azure Marketplace](https://azuremarketplace.microsoft.com). Po přihlášení na portálu Azure, Zákazníci si můžou vybrat prvek navigace Marketplace pro přístup k jejich soukromé nabídky. Soukromé nabídky se také zobrazí ve výsledcích hledání a je možné nasadit prostřednictvím příkazového řádku a šablony Azure Resource Manageru, jako je žádnou další nabídkou.

![[Soukromé nabídky]](./media/marketplace-publishers-guide/private-offer.png)

Soukromé nabídky se také zobrazí ve výsledcích hledání. Právě hledejte "Privátní" oznámení "BADGE".

## <a name="next-steps"></a>Další postup

Pokud chcete využít tyto nové možnosti, můžete začít prodej na [Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
