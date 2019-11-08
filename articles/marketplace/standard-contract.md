---
title: Smlouva Standard | Azure
description: Smlouva Standard Azure Marketplace a AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819500"
---
# <a name="standard-contract"></a>Standardní kontrakt

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní šablonu smlouvy, aby usnadnila transakci na webu Marketplace. Místo vytváření vlastních podmínek a ujednání můžou Azure Marketplace vydavatelé zvolit, že mají nabízet svůj software v rámci standardní smlouvy, které zákazníci potřebují, aby je Vet a přijali jenom jednou. Standardní kontrakt najdete tady: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178). 

Podmínky a ujednání pro nabídku jsou definovány na kartě Marketplace při vytváření nabídky v portál partnerů cloudu. Možnost standardní smlouva je povolená změnou nastavení na Ano.

![Povolení možnosti standardní kontrakt](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Pokud se rozhodnete použít standardní smlouvu, jsou pro kanál [Cloud Solution Provider](./cloud-solution-providers.md) stále vyžadovány samostatné podmínky a ujednání.

## <a name="standard-contract-amendments"></a>Změny smlouvy Standard

Standardní změny smlouvy umožňují vydavatelům vybrat standardní kontrakt pro jednoduchost a s přizpůsobenými podmínkami pro svůj produkt nebo firmu.  Zákazníci si musí jenom projít změny smlouvy, pokud už zkontrolovali a přijali standardní kontrakt společnosti Microsoft.

Existují dva druhy změn, které jsou k dispozici pro Azure Marketplace vydavatelé:

* Univerzální změny: tyto změny se aplikují všeobecně na standardní smlouvu pro všechny zákazníky. Univerzální změny se zobrazují každému zákazníkovi produktu v toku nákupu.

![Univerzální změny](media/marketplace-publishers-guide/universal-amendaments.png)

* Vlastní změny: Azure Marketplace má taky zřízení pro vlastní změny, které cílí na klienty. Jedná se o zvláštní změny standardní smlouvy, které se zaměřují jenom na konkrétní zákazníky. Vydavatelé můžou zvolit tenanta, na který chtějí cílit. Zákazníci z tohoto tenanta si kupují produkt v rámci smlouvy Standard a cílené změny.

![Vlastní změny](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Zákazníci, na které cílíte vlastní změny, získají při nákupu také univerzální změnu standardních podmínek.

>[!Note]
>Následující typy nabídek podporují standardní smlouvy o změnách: aplikace Azure (šablony řešení a spravované aplikace), Virtual Machines, kontejnery a aplikace typu kontejner.

### <a name="customer-experience"></a>Prostředí pro zákazníky

Během procesu nákupu v Azure Portal budou zákazníci moci zjistit, jaké jsou informace spojené s produktem, jako standardní kontrakt společnosti Microsoft a změny.

![Prostředí pro zákazníky Azure Portal.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>Rozhraní API

Zákazníci mohou pomocí `Get-AzureRmMarketplaceTerms` načíst podmínky nabídky a přijmout je. Ve výstupu rutiny se vrátí standardní smlouva a související změny.

---
