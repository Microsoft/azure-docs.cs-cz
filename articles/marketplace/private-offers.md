---
title: Soukromé nabídky na komerčním webu Microsoft Marketplace
description: Soukromé nabídky na komerčním tržišti Microsoftu pro aplikace a vydavatele služeb.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 07/01/2020
ms.openlocfilehash: fdd8c752e8cf610ef3c32e1af91b3ad6454c0813
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971714"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Soukromé nabídky na komerčním webu Microsoft Marketplace

Soukromé nabídky v [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) umožňují vydavatelům vytvářet plány, které jsou viditelné pouze cílovým zákazníkům. Tento článek popisuje možnosti a výhody soukromých nabídek.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odemknutí podnikových obchodů pomocí soukromých nabídek

Podnikoví zákazníci budou k hledání, zkoušení a nákupu cloudových řešení stále používat online tržiště. Díky soukromým nabídkám můžou vydavatelé využít Marketplace k soukromému sdílení přizpůsobených řešení s cílovými zákazníky s možnostmi, které podniky potřebují:

- *Vysjednané ceny* umožňují vydavatelům rozšířené slevy a ceny mimo seznam z veřejně dostupných nabídek.
- *Soukromé podmínky a ujednání* umožňují vydavatelům přizpůsobit podmínky a ujednání na konkrétního zákazníka.
- *Specializované konfigurace* umožňují vydavatelům přizpůsobit své Virtual Machines, aplikace Azure a aplikace SaaS nabídky potřebám jednotlivých zákazníků. Tato možnost také umožňuje vydavatelům poskytovat přístup k novým funkcím produktu před širším spouštěním pro všechny zákazníky.

Soukromé nabídky umožňují vydavatelům využít výhod škálování a globální dostupnosti veřejného webu Marketplace s flexibilitou a kontrolou potřebnou k vyjednání a doručování vlastních obchodů a konfigurací. Tyto funkce společně otevřou dvířka pro silné podnikové přijetí cloudových tržišť. Podniky si teď můžou koupit a prodávat způsobem, který očekávají a požadují.

Pro virtuální počítač jsou nyní k dispozici soukromé nabídky, aplikace Azure (implementované jako šablony řešení nebo spravované aplikace) a nabídky aplikací SaaS.

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](). Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-plans"></a>Vytváření privátních nabídek pomocí plánů

Pro *nové nebo stávající nabídky s plány* můžou vydavatelé snadno vytvořit nové a soukromé odchylky vytvořením nových plánů (dřív označovaných jako SKU) a jejich označení jako soukromé. Každá nabídka může mít až 45 privátních plánů.

<!--- [Private SKUs]() --->

Soukromé plány jsou komponenty nabídky a jsou viditelné pouze cílovými zákazníky. Privátní plány můžou znovu použít základní Image nebo metadata nabídky, která už jsou publikovaná pro veřejný plán. Tato možnost umožňuje vydavatelům vytvořit několik soukromých variant veřejné nabídky bez nutnosti publikování více verzí stejné základní image a metadat nabídky. Pro virtuální počítače a aplikace Azure nabízí jenom v případě, že privátní plán sdílí základní image s veřejným plánem, všechny změny základní image nabídky se šíří napříč všemi veřejnými a soukromými plány pomocí této základní image.

Pro *nové nabídky, které zahrnují pouze soukromé plány*, mohou vydavatelé vytvořit své nabídky jako jakoukoli jinou nabídku a pak označit plány jako soukromé. Nabídky, které mají pouze privátní plány, nebudou zjistitelné nebo přístupné prostřednictvím komerčního webu Microsoft Marketplace nebo [Azure Portal](https://azure.microsoft.com/features/azure-portal/) zákazníky, kteří nejsou přidruženi k této nabídce.

Soukromé plány jsou k dispozici pro globální i Azure Government v Azure.

>[!NOTE]
>Nabídka, která obsahuje pouze soukromé plány, nebude viditelná ve veřejném Azure Marketplace nebo AppSource.

## <a name="targeting-customers-with-private-offers"></a>Cílení na zákazníky pomocí soukromých nabídek

Pro nové i stávající soukromé nabídky můžou vydavatelé cílit na zákazníky pomocí identifikátorů předplatného. Vydavatelé, kteří používají virtuální počítač nebo nabídku aplikací Azure, můžou omezit dostupnost privátního plánu na individuální ID předplatného Azure nebo nahrát CSV s až 20 000 ID předplatných Azure. Při použití soukromé nabídky aplikace SaaS můžou vydavatelé přidružit buď ID tenanta, aby se omezila dostupnost privátního plánu, a to buď pomocí manuálního, nebo přístupu pro nahrávání ve formátu CSV.

Jakmile je nabídka certifikovaná a publikovaná, můžou se zákazníci aktualizovat nebo odebrat z plánu během několika minut pomocí funkce synchronizace privátních předplatných. Tato možnost umožňuje vydavatelům rychle a snadno aktualizovat seznam zákazníků, na které se soukromý plán zobrazuje, bez certifikace nebo publikování této nabídky.

>[!NOTE]
>Použitelnost Azure gov: soukromé nabídky (nebo přesněji: soukromé plány) jsou k dispozici pro Cloud gov, stejně jako jakékoli jiné nabídky. Plán může také posunout stav z privátní na veřejné a naopak. Ovládací prvek, na kterém bude nabídka dostupná, je v rukou partnera a dá se nakonfigurovat přes Partnerské centrum. 

## <a name="deploying-private-offers"></a>Nasazení privátních nabídek

Soukromé nabídky jsou pouze zjistitelné prostřednictvím [Azure Portal](https://azure.microsoft.com/features/azure-portal/) a nejsou uvedeny v [Microsoft AppSource](https://appsource.microsoft.com/) nebo [Azure Marketplace](https://azuremarketplace.microsoft.com). Další informace o publikování v různých obchodech online obchodu na webu Marketplace najdete v tématu [Určení možnosti publikování](./determine-your-listing-type.md).

Po přihlášení k Azure Portal mohou zákazníci vybrat navigační prvek Marketplace pro přístup ke svým soukromým nabídkám. Soukromé nabídky se také zobrazí ve výsledcích hledání a dají se nasadit prostřednictvím příkazového řádku a šablony Azure Resource Manager jako jakékoli jiné nabídky.

![[Soukromé nabídky]](./media/marketplace-publishers-guide/private-offer.png)

Ve výsledcích hledání se zobrazí také soukromé nabídky. Stačí vyhledat **soukromou** badge.

>[!Note]
>Soukromé nabídky nejsou podporovány u předplatných vytvořených prostřednictvím prodejce programu Cloud Solution Provider (CSP).


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
