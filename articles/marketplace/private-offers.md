---
title: Soukromé nabídky na komerčním webu Microsoft Marketplace
description: Soukromé nabídky na komerčním tržišti Microsoftu pro aplikace a vydavatele služeb.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 02/22/2021
ms.openlocfilehash: 7c02c41379cfb886fd7e6f84486d815d3cdbfb5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097172"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Soukromé nabídky na komerčním webu Microsoft Marketplace

Soukromé nabídky, nazývané také soukromé plány, umožňují vydavatelům vytvářet plány, které jsou viditelné pouze cílovým zákazníkům. Tento článek popisuje možnosti a výhody soukromých nabídek.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odemknutí podnikových obchodů pomocí soukromých nabídek

Když vytvoříte soukromé nabídky, můžou vydavatelé soukromě nabídnout přizpůsobená řešení cílovým zákazníkům s možnostmi, které podniky potřebují:

- *Vysjednané ceny* umožňují vydavatelům rozšířené slevy a ceny mimo seznam z veřejně dostupných nabídek.
- *Soukromé podmínky a ujednání* umožňují vydavatelům přizpůsobit podmínky a ujednání na konkrétního zákazníka.
- *Specializované konfigurace* umožňují vydavatelům přizpůsobit své Virtual Machines, aplikace Azure a software jako službu (SaaS) pro potřeby jednotlivých zákazníků. Tato možnost také umožňuje vydavatelům poskytovat přístup ke verzi Preview k novým funkcím produktu před jejich spuštěním pro všechny zákazníky.

Soukromé nabídky umožňují vydavatelům využít výhod škálování a globální dostupnosti veřejného webu Marketplace s flexibilitou a řízením potřebnými k vyjednání a doručování vlastních obchodů a konfigurací. Podniky si teď můžou koupit a prodávat způsobem, který očekávají.

## <a name="create-private-offers-using-plans"></a>Vytváření privátních nabídek pomocí plánů

Pro *nové nebo stávající nabídky s plány* můžou vydavatelé snadno vytvořit nové a soukromé odchylky vytvořením nových plánů (dřív označovaných jako SKU) a jejich označení jako soukromé. Každá nabídka může mít až 45 privátních plánů.

<!--- [Private SKUs]() --->

Soukromé plány jsou k dispozici pro následující typy nabídek:

- Virtuální počítač Azure
- Aplikace Azure (implementované jako šablony řešení nebo spravované aplikace)
- Spravovaná služba
- Nabídky SaaS

Soukromé plány jsou komponenty nabídky a jsou viditelné pouze cílovými zákazníky. Soukromé plány jsou viditelné jenom pro cílové zákazníky. Soukromé plány je možné zpřístupnit zákazníkům v globálním i Azure Government Azure.

Privátní plány můžou znovu použít základní Image nebo metadata nabídky, která už jsou publikovaná pro veřejný plán. Tato možnost umožňuje vydavatelům vytvořit více soukromých variant veřejné nabídky bez nutnosti publikování více verzí stejné základní image a metadat nabídky. Pro virtuální počítače Azure a aplikace Azure nabízí jenom v případě, že privátní plán sdílí základní image s veřejným plánem, všechny změny základní image nabídky se šíří napříč všemi veřejnými a soukromými plány pomocí této základní image.

Pro *nové nabídky, které zahrnují pouze soukromé plány*, mohou vydavatelé vytvořit své nabídky jako jakoukoli jinou nabídku a pak označit plány jako soukromé. Nabídky, které mají jenom soukromé plány, se nedají odhalit nebo zpřístupnit v [Azure Portal](https://azure.microsoft.com/features/azure-portal/) zákazníky, kteří nejsou k této nabídce přidružení.

>[!NOTE]
>Nabídka, která obsahuje pouze soukromé plány, nebude viditelná ve veřejném Azure Marketplace nebo AppSource.

## <a name="target-customers-with-private-offers"></a>Cílové zákazníky s privátními nabídkami

Pro nové i stávající soukromé nabídky můžou vydavatelé cílit na zákazníky pomocí identifikátorů předplatného. Pro virtuální počítače Azure, aplikace Azure a nabídky spravované služby můžou vydavatelé omezit dostupnost privátního plánu na individuální ID předplatného Azure nebo nahrát CSV s až 10 000 ID předplatných Azure. U nabídek SaaS můžou vydavatelé přidružit ID tenanta Azure Active Directory, aby se omezila dostupnost privátního plánu, a to pomocí ručního nebo přístupového nahrávání CSV.

Jakmile je nabídka certifikovaná a publikovaná, můžou se zákazníci aktualizovat nebo odebrat z plánu pomocí funkce synchronizace privátních předplatných. Tato možnost umožňuje vydavatelům rychle a snadno aktualizovat seznam zákazníků, na které se soukromý plán zobrazuje, bez certifikace nebo publikování této nabídky.

## <a name="deploying-private-offers"></a>Nasazení privátních nabídek

Po přihlášení k Azure Portal můžou zákazníci pomocí těchto kroků vybrat vaše soukromé nabídky.

1. Přihlaste se k [Azure Portal](https://ms.portal.azure.com/).
1. V části **služby Azure** vyberte **vytvořit prostředek**.
1. Na **nové** stránce vedle **Azure Marketplace** vyberte **Zobrazit vše**. Zobrazí se stránka Marketplace.
1. V levém navigačním panelu vyberte **soukromé nabídky**.

> [!NOTE]
> Soukromé nabídky jsou pouze zjistitelné v [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Nezobrazují se [Microsoft AppSource](https://appsource.microsoft.com/) ani [Azure Marketplace](https://azuremarketplace.microsoft.com). Další informace o publikování v různých obchodech online na webu Marketplace najdete v tématu [Úvod do možností výpisu](./determine-your-listing-type.md).

Soukromé nabídky se také zobrazí ve výsledcích vyhledávání a mohou být nasazeny prostřednictvím příkazového řádku a šablony Azure Resource Manager, stejně jako jakékoli jiné nabídky.

[![[Veřejné nabídky se zobrazují ve výsledcích hledání.]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

Ve výsledcích hledání se zobrazí také soukromé nabídky. Stačí vyhledat **soukromou** badge.

>[!Note]
>Soukromé nabídky nejsou podporovány u předplatných vytvořených prostřednictvím prodejce programu Cloud Solution Provider (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
