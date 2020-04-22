---
title: Standardní smlouva | Azure Marketplace
description: Standardní kontrakt pro Azure Marketplace a AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681451"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardní smlouva pro komerční trh společnosti Microsoft

Aby se zjednodušil proces zásobování zákazníků a snížila se právní složitost pro dodavatele softwaru, nabízí společnost Microsoft standardní smlouvu pro komerční tržiště společnosti Microsoft, která usnadňuje transakce na trhu. Namísto vytváření vlastních smluvních podmínek se vydavatelé komerčního trhu mohou rozhodnout nabízet svůj software podle standardní smlouvy, kterou zákazníci potřebují pouze jednou projet a přijmout. Standardní smlouvu naleznete zde: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

Podmínky nabídky jsou definovány při vytváření nabídky v Partnerském centru. Místo poskytování vlastních smluvních podmínek můžete zvolit použití standardní smlouvy pro komerční tržiště společnosti Microsoft.

>[!Note]
>Jakmile publikujete nabídku pomocí smlouvy Standard pro komerční tržiště společnosti Microsoft, nebudete moci používat vlastní smluvní podmínky. Je to "nebo" scénář. Buď nabízíte své řešení podle standardní *smlouvy, nebo* vlastních podmínek. Chcete-li změnit podmínky standardní smlouvy, můžete tak učinit prostřednictvím standardních změn smlouvy.

## <a name="standard-contract-amendments"></a>Standardní změny smlouvy

Standardní změny smlouvy umožňují vydavatelům vybrat standardní smlouvu pro jednoduchost a s přizpůsobenými podmínkami pro jejich produkt nebo podnikání. Zákazníci potřebují zkontrolovat změny smlouvy pouze v případě, že již přezkoumali a přijali standardní smlouvu společnosti Microsoft.

Existují dva druhy změn k dispozici pro komerční trh vydavatelů:

* Univerzální změny: Tyto změny jsou uplatňovány univerzálně na standardní smlouvu pro všechny zákazníky. Univerzální změny jsou zobrazeny každému zákazníkovi nabídky v nákupním toku. Zákazníci musí před použitím vaší nabídky přijmout podmínky standardní smlouvy a dodatku.

* Vlastní změny: Tyto změny jsou speciální změny standardní smlouvy, které jsou určeny pro konkrétní zákazníky pouze prostřednictvím ID klienta Azure. Vydavatelé si můžou vybrat klienta, na kterého chtějí cílit. Pouze zákazníci z nájemce budou prezentovány s vlastní změny podmínky v nabídce nákupní tok.  Zákazníci musí před použitím vaší nabídky přijmout podmínky standardní smlouvy a dodatků.

>[!Note]
>Tyto dva typy změn zásobníku na sebe. Zákazníci, na které se zaměňují s vlastními změnami, dostanou také univerzální změnu standardní smlouvy během nákupu.

Standardní kontrakt pro komerční trh Microsoftu můžete využít pro následující typy nabídek: Azure Applications (šablony řešení a spravované aplikace), virtuální počítače, kontejnery, kontejnerové aplikace, moduly IoT Edge a SaaS.

## <a name="customer-experience"></a>Zákaznická zkušenost

Během zjišťování na azure marketplace nebo AppSource budou zákazníci moci zobrazit podmínky spojené s nabídkou jako standardní smlouvu pro komerční tržiště Microsoftu a všechny univerzální změny.

![Prostředí zjišťování zákazníků na portálu Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Během procesu nákupu na portálu Azure budou zákazníci moci zobrazit podmínky spojené s nabídkou jako standardní smlouvu pro komerční tržiště Microsoftu a všechny univerzální změny nebo změny specifické pro klienta.

![Prostředí pro nákup zákazníků na portálu Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>rozhraní API

Zákazníci mohou pomocí get-AzureRmMarketplaceTerms načíst podmínky nabídky a přijmout ji. Standardní smlouva a související změny budou vráceny ve výstupu rutiny.
