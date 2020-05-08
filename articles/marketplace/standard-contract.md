---
title: Smlouva standard pro komerční tržiště Microsoftu
description: Smlouva standard pro Azure Marketplace a AppSource v partnerském centru
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 3886b29a47edcfb36114cedf8b8edb3799e0c345
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871430"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Smlouva standard pro komerční tržiště Microsoftu

Microsoft nabízí standardní smlouvu pro komerční tržiště Microsoftu. To pomáhá zjednodušit proces nákupu pro zákazníky, snížit právní složitost pro dodavatele softwaru a usnadnit transakce na webu Marketplace. Místo toho, abyste mohli vlastní podmínky a ujednání, jako vydavatel komerčního tržiště, si můžete vybrat, abyste si software nabídli v rámci [standardní smlouvy](https://go.microsoft.com/fwlink/?linkid=2041178), které zákazníci potřebují Vet a přijímat jenom jednou.

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

Během zjišťování v Azure Marketplace nebo AppSource budou zákazníci moci zobrazit podmínky spojené s nabídkou jako standardní smlouvu pro komerční tržiště Microsoftu a všechny univerzální změny.

![Azure Portal možnosti zjišťování zákazníků.](media/marketplace-publishers-guide/azure-discovery-process.png)

Během procesu nákupu v Azure Portal budou zákazníci moci zjistit, jaké jsou informace spojené s nabídkou, jako standardní smlouvu pro komerční tržiště Microsoftu a všechny obecné a/nebo změny specifické pro tenanta.

![Azure Portal možností nákupu zákazníků.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>Rozhraní API

Zákazníci mohou pomocí Get-AzureRmMarketplaceTerms načíst podmínky nabídky a přijmout je. Ve výstupu rutiny se vrátí standardní smlouva a související změny.
