---
title: Standardní smlouvy | Azure
description: Standardní smlouvy v Azure Marketplace a AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620384"
---
# <a name="standard-contract"></a>Standardní kontrakt

Zjednodušit proces zajišťování pro zákazníky a snižuje složitost právní pro dodavatele softwaru, společnost Microsoft nabízí standardní smluvní šablony k usnadnění transakce na webu Marketplace. Místo vytváření vlastních podmínek a ujednání, můžete zvolit standardní smlouvou, který zákazníci potřebují pouze jednou přijmout a ověřte svůj software nabízet vydavatelům Azure Marketplace. Standardní smlouvy najdete tady: [ https://go.microsoft.com/fwlink/?linkid=2041178 ](https://go.microsoft.com/fwlink/?linkid=2041178). 

Podmínky a ujednání pro nabídku jsou definované na kartě Marketplace při vytváření nabídky v portál partnerů cloudu. Změnou nastavení na Ano je povolena možnost Standardní kontraktu.

![Povolení možnosti standardní kontraktu](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Pokud se rozhodnete použít standardní smlouvy, se stále vyžadují pro zvláštní podmínky a ujednání [poskytovatele Cloud Solution Provider](./cloud-solution-providers.md) kanálu.

## <a name="standard-contract-amendments"></a>Standardní smlouvy změny

Standardní smlouvy změny povolit vydavatele vyberte standardní smlouvy, pro jednoduchost a s vlastní podmínky jejich produktu nebo firmě.  Zákazníkům stačí zkontrolovat změny kontrakt, pokud jste už zkontrolovali a přijmout kontraktu standardní Microsoft.

Pro vydavatele Azure Marketplace k dispozici jsou dva druhy změny:

* Univerzální změny: Tyto změny se použijí univerzálně se standardní smlouvou pro všechny zákazníky. Univerzální změny jsou uvedeny na každý zákazník produktu v toku nákupu.

![Univerzální změny](media/marketplace-publishers-guide/universal-amendaments.png)

* Vlastní změny: Azure Marketplace má také zřizování pro vlastní změny, které jsou cíleny na klienty. Jsou speciální změny se standardní smlouvou, na kterou cílí jen některým zákazníkům. Vydavatelé můžete vybrat tenanta, které chtějí zaměřit. Zákazníci z tohoto tenanta by nákupu produktu v rámci standardní kontrakt a cílové změny.

![Vlastní změny](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Cílem vlastní změny zákazníci získají univerzální dodatku standardní podmínky při nákupu.

>[!Note]
>Následující typy nabídek podporují standardní změny smlouvy: Aplikace Azure (řešení šablony a spravované aplikace), virtuální počítače, kontejnery, aplikace typu kontejner.

### <a name="customer-experience"></a>Prostředí pro zákazníky

Zákazníci budou moci zobrazit podmínky spojené s tímto produktem jako standardní smlouvy Microsoft a změn během procesu nákupu na webu Azure Portal.

![Zkušenosti zákazníků Azure portal.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>rozhraní API

Zákazníci můžou využít `Get-AzureRmMarketplaceTerms` načtení podmínky nabídky a přijmout. Standardní kontrakt a související změny se vrátí ve výstupu rutiny.

---
