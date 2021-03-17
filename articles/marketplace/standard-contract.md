---
title: Smlouva standard pro komerční tržiště Microsoftu
description: Smlouva standard pro Azure Marketplace a AppSource v partnerském centru
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/20/2020
ms.openlocfilehash: 20a257bde6022249fd7b2ab875b94f356234b490
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488872"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Smlouva standard pro komerční tržiště Microsoftu

Microsoft nabízí standardní smlouvu pro komerční tržiště Microsoftu. To pomáhá zjednodušit proces nákupu pro zákazníky, snížit právní složitost pro dodavatele softwaru a usnadnit transakce na webu Marketplace. Místo toho, abyste mohli vlastní podmínky a ujednání, jako vydavatel komerčního tržiště, si můžete vybrat, abyste si software nabídli v rámci [standardní smlouvy](https://go.microsoft.com/fwlink/?linkid=2041178), které zákazníci potřebují Vet a přijímat jenom jednou.

Podmínky a ujednání pro nabídku jsou definovány při vytváření nabídky v partnerském centru. Místo poskytování vlastních podmínek a ujednání můžete zvolit, že se má používat standardní smlouva pro komerční tržiště Microsoftu.

>[!Note]
>Po publikování nabídky s použitím standardní smlouvy pro komerční tržiště Microsoftu nebudete moct používat vlastní podmínky a ujednání. Vaše řešení si buď nabídnete v rámci standardní smlouvy, *nebo* podle vašich vlastních podmínek. Vlastní podmínky a ujednání jsou definovány na úrovni nabídky a platí pro všechny plány; na stránce **vlastností** vaší nabídky v partnerském centru napište vlastní podmínky a ujednání. Pokud chcete upravit podmínek standardní smlouvy, můžete to udělat prostřednictvím standardních změn smlouvy.

## <a name="standard-contract-amendments"></a>Změny smlouvy Standard

Standardní změny smlouvy umožňují vydavatelům vybrat standardní kontrakt pro jednoduchost a s přizpůsobenými podmínkami pro svůj produkt nebo firmu. Zákazníci si musí jenom projít změny smlouvy, pokud už zkontrolovali a přijali standardní kontrakt společnosti Microsoft.

K dispozici jsou dva druhy změn pro komerční vydavatele na webu Marketplace:

* Univerzální změny: tyto změny se aplikují všeobecně na standardní smlouvu pro všechny zákazníky. Univerzální změny se zobrazují každému zákazníkovi nabídky v toku nákupu. Zákazníci musí přijmout podmínky standardní smlouvy a dodatku, aby mohli používat vaši nabídku.

* Vlastní změny: tyto změny představují zvláštní změny standardní smlouvy, které jsou zaměřené na konkrétní zákazníky jenom prostřednictvím ID tenanta Azure. Vydavatelé můžou zvolit tenanta, na který chtějí cílit. S vlastními podmínkami změny v nákupním toku nabídky se zobrazí pouze zákazníci z tenanta.  Zákazníci musí přijmout podmínky smlouvy Standard a jejich změny, aby mohli používat vaši nabídku.

>[!Note]
>Tyto dva typy změn zásobníků jsou navzájem navzájem. Zákazníci, na které cílíte vlastní změny, získají při nákupu také univerzální změnu standardní smlouvy. Změny jsou omezené na 4000 znaků včetně mezer.

Standardní smlouvu pro komerční tržiště společnosti Microsoft můžete využít pro následující typy nabídek: aplikace Azure (šablony řešení a spravované aplikace), Virtual Machines a SaaS.

## <a name="customer-experience"></a>Prostředí pro zákazníky

Během zjišťování v Azure Marketplace nebo AppSource budou zákazníci moci zobrazit podmínky spojené s nabídkou jako standardní smlouvu pro komerční tržiště Microsoftu a všechny univerzální změny.

![Azure Portal možnosti zjišťování zákazníků.](media/marketplace-publishers-guide/azure-discovery-process.png)

Během procesu nákupu v Azure Portal budou zákazníci moci zjistit, jaké jsou informace spojené s nabídkou, jako standardní smlouvu pro komerční tržiště Microsoftu a všechny obecné a/nebo změny specifické pro tenanta.

![Azure Portal možností nákupu zákazníků.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>Rozhraní API

Zákazníci mohou pomocí Get-AzureRmMarketplaceTerms načíst podmínky nabídky a přijmout je. Ve výstupu rutiny se vrátí standardní smlouva a související změny.
