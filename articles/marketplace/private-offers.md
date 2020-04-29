---
title: Soukromé nabídky | Azure Marketplace
description: Soukromé nabídky v Azure Marketplace pro vydavatele aplikací a služeb.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: efda9fb66a65f1f2cd40de8bf32a8a3fa99aea7f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681435"
---
# <a name="private-offers"></a>Soukromé nabídky

Soukromé nabídky [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) umožňují vydavatelům vytvářet skladové jednotky, které jsou viditelné pouze cílovým zákazníkům.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odemknutí podnikových obchodů pomocí soukromých nabídek

Podnikoví zákazníci budou k hledání, zkoušení a nákupu cloudových řešení stále používat online tržiště. Díky soukromým nabídkám můžou vydavatelé využít Marketplace k soukromému sdílení přizpůsobených řešení s cílovými zákazníky s možnostmi, které podniky potřebují:

- *Vysjednané ceny* umožňují vydavatelům rozšířené slevy a ceny mimo seznam z veřejně dostupných nabídek.
- *Soukromé podmínky a ujednání* umožňují vydavatelům přizpůsobit podmínky a ujednání na konkrétního zákazníka.
- *Specializované konfigurace* umožňují vydavatelům přizpůsobit své Virtual Machines, aplikace Azure a aplikace SaaS nabídky potřebám jednotlivých zákazníků. Tato možnost také umožňuje vydavatelům poskytovat přístup k novým funkcím produktu před širším spouštěním pro všechny zákazníky.

Soukromé nabídky umožňují vydavatelům využít výhod škálování a globální dostupnosti veřejného webu Marketplace s flexibilitou a kontrolou potřebnou k vyjednání a doručování vlastních obchodů a konfigurací. Tyto funkce společně otevřou dvířka pro silné podnikové přijetí cloudových tržišť. Podniky si teď můžou koupit a prodávat způsobem, který očekávají a požadují.

Pro virtuální počítač jsou nyní k dispozici soukromé nabídky, aplikace Azure (implementované jako šablony řešení nebo spravované aplikace) a nabídky aplikací SaaS. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>Vytváření privátních nabídek pomocí SKU a plánů

Pro *nové nebo stávající nabídky s veřejnými SKU nebo plány*můžou vydavatelé snadno vytvořit nové a soukromé odchylky vytvořením nových SKU nebo plánů a jejich označením jako soukromé.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

Soukromé SKU a plány jsou komponenty nabídky a jsou viditelné pouze cílovými zákazníky. Soukromé SKU a plány můžou znovu použít základní Image nebo metadata nabídky, která už jsou publikovaná pro veřejnou SKU nebo plán. Tato možnost umožňuje vydavatelům vytvořit několik soukromých variant veřejné nabídky bez nutnosti publikování více verzí stejné základní image a metadat nabídky. Pro virtuální počítače a aplikace Azure nabízí, když soukromá SKU sdílí základní image s veřejnou SKU, všechny změny základní image nabídky se šíří napříč všemi veřejnými a soukromými SKU pomocí této základní image.

Pro *nové nabídky, které zahrnují jenom soukromé SKU nebo plány*, můžou vydavatelé vytvořit své nabídky jako jakoukoli jinou nabídku a pak označit SKU nebo plány jako soukromé. Nabídky, které mají pouze soukromé SKU nebo plány, nebudou zjistitelné ani přístupné prostřednictvím komerčního tržiště Microsoftu nebo [Azure Portal](https://azure.microsoft.com/features/azure-portal/) zákazníky, kteří nejsou přidruženi k této nabídce.

## <a name="targeting-customers-with-private-offers"></a>Cílení na zákazníky pomocí soukromých nabídek

Pro nové i stávající soukromé nabídky můžou vydavatelé cílit na zákazníky pomocí identifikátorů předplatného. Vydavatelé, kteří používají virtuální počítač nebo nabídku aplikací Azure, můžou omezit dostupnost privátní SKU na individuální ID předplatného Azure nebo nahrát CSV s až 20 000 ID předplatných Azure. Při použití soukromé nabídky aplikace SaaS můžou vydavatelé přidružit buď ID tenanta, aby se omezila dostupnost privátního plánu, a to buď pomocí manuálního, nebo přístupu pro nahrávání ve formátu CSV.

Jakmile je nabídka certifikovaná a publikovaná, můžou se zákazníci aktualizovat nebo odebrat z SKU nebo plánu během několika minut pomocí funkce synchronizace privátních předplatných. Tato možnost umožňuje vydavatelům rychle a snadno aktualizovat seznam zákazníků, na které se soukromá SKU nebo plán prezentuje, a to bez certifikace nebo opětovného publikování této nabídky.

## <a name="deploying-private-offers"></a>Nasazení privátních nabídek

Soukromé nabídky jsou pouze zjistitelné prostřednictvím [Azure Portal](https://azure.microsoft.com/features/azure-portal/) a nejsou uvedeny v [Microsoft AppSource](https://appsource.microsoft.com/) nebo [Azure Marketplace](https://azuremarketplace.microsoft.com). Další informace o publikování do různých komerčních prodejní místa na webu Marketplace najdete v tématu [Určení možnosti publikování](./determine-your-listing-type.md).

Po přihlášení k Azure Portal můžou zákazníci vybrat navigační prvek Marketplace pro přístup ke svým soukromým nabídkám. Soukromé nabídky se také zobrazí ve výsledcích hledání a dají se nasadit prostřednictvím příkazového řádku a šablony Azure Resource Manager jako jakékoli jiné nabídky.

![[Soukromé nabídky]](./media/marketplace-publishers-guide/private-offer.png)

Ve výsledcích hledání se zobrazí také soukromé nabídky. Stačí vyhledat "soukromou" BADGE.

>[!Note]
>Soukromé nabídky nejsou podporovány u předplatných vytvořených prostřednictvím prodejce programu Cloud Solution Provider (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->