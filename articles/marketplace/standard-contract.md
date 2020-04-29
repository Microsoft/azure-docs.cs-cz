---
title: Smlouva Standard | Azure Marketplace
description: Smlouva standard pro Azure Marketplace a AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681451"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Smlouva standard pro komerční tržiště Microsoftu

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní smlouvu pro komerční tržiště Microsoftu, která usnadňuje transakce na webu Marketplace. Místo toho, aby si mohli vlastní podmínky a ujednání, můžou si komerční vydavatelé na webu Marketplace vybrat svůj software v rámci standardní smlouvy, kteří zákazníci potřebují Vet a přijmout jenom jednou. Standardní kontrakt najdete tady: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

Podmínky a ujednání pro nabídku jsou definovány při vytváření nabídky v partnerském centru. Místo poskytování vlastních podmínek a ujednání můžete zvolit, že se má používat standardní smlouva pro komerční tržiště Microsoftu.

>[!Note]
>Po publikování nabídky pomocí standardní smlouvy pro komerční tržiště společnosti Microsoft nebudete moci používat vlastní podmínky a ujednání. Jedná se o scénář "nebo". Vaše řešení si buď nabídnete v rámci standardní smlouvy, *nebo* podle vašich vlastních podmínek. Pokud chcete upravit podmínek standardní smlouvy, můžete to udělat prostřednictvím standardních změn smlouvy.

## <a name="standard-contract-amendments"></a>Změny smlouvy Standard

Standardní změny smlouvy umožňují vydavatelům vybrat standardní kontrakt pro jednoduchost a s přizpůsobenými podmínkami pro svůj produkt nebo firmu. Zákazníci si musí jenom projít změny smlouvy, pokud už zkontrolovali a přijali standardní kontrakt společnosti Microsoft.

K dispozici jsou dva druhy změn pro komerční vydavatele na webu Marketplace:

* Univerzální změny: tyto změny se aplikují všeobecně na standardní smlouvu pro všechny zákazníky. Univerzální změny se zobrazují každému zákazníkovi nabídky v toku nákupu. Zákazníci musí přijmout podmínky standardní smlouvy a dodatku, aby mohli používat vaši nabídku.

* Vlastní změny: tyto změny představují zvláštní změny standardní smlouvy, které jsou zaměřené na konkrétní zákazníky jenom prostřednictvím ID tenanta Azure. Vydavatelé můžou zvolit tenanta, na který chtějí cílit. S vlastními podmínkami změny v nákupním toku nabídky se zobrazí pouze zákazníci z tenanta.  Zákazníci musí přijmout podmínky smlouvy Standard a jejich změny, aby mohli používat vaši nabídku.

>[!Note]
>Tyto dva typy změn zásobníků jsou navzájem navzájem. Zákazníci, na které cílíte vlastní změny, získají při nákupu také univerzální změnu standardní smlouvy.

Standardní smlouvu na komerční tržišti společnosti Microsoft můžete využít pro následující typy nabídek: aplikace Azure (šablony řešení a spravované aplikace), Virtual Machines, kontejnery, aplikace kontejneru, IoT Edge moduly a SaaS.

## <a name="customer-experience"></a>Prostředí pro zákazníky

Během zjišťování na Azure Marketplace nebo AppSource budou zákazníci moci zobrazit podmínky spojené s nabídkou jako standardní smlouvu pro komerční tržiště Microsoftu a všechny univerzální změny.

![Azure Portal možnosti zjišťování zákazníků.](media/marketplace-publishers-guide/azure-discovery-process.png)

Během procesu nákupu v Azure Portal budou zákazníci moci zjistit, jaké jsou informace spojené s nabídkou, jako standardní smlouvu pro komerční tržiště Microsoftu a všechny obecné a/nebo změny specifické pro tenanta.

![Azure Portal možností nákupu zákazníků.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>Rozhraní API

Zákazníci mohou pomocí Get-AzureRmMarketplaceTerms načíst podmínky nabídky a přijmout je. Ve výstupu rutiny se vrátí standardní smlouva a související změny.
