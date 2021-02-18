---
title: Zabezpečte svůj původ pomocí privátního odkazu v Azure front-Premium Standard/Premium (Preview).
description: Tato stránka poskytuje informace o tom, jak zabezpečit připojení ke svému zdroji pomocí privátního odkazu.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: dead60b9d8e0872f3d46b1f223ccf5e6697cbd90
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099202"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Zabezpečte svůj původ pomocí privátního odkazu v Azure front-Premium Standard/Premium (Preview).

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Prohlédněte si [dokumentaci ke službě Azure front dveří](../front-door-overview.md).

## <a name="overview"></a>Přehled

[Privátní odkaz Azure](../../private-link/private-link-overview.md) umožňuje přístup ke službám Azure PaaS a hostovaným službám Azure přes privátní koncový bod ve vaší virtuální síti. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

SKU služby Azure front-Premium se může připojit k vašemu zdroji pomocí služby privátního propojení. Vaše aplikace se můžou hostovat ve vaší privátní virtuální síti nebo za službou PaaS, takže nejsou přístupné z veřejného Internetu.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Architektura privátních koncových bodů pro přední dvířka":::

Pokud povolíte privátní propojení s vaším zdrojem v konfiguraci služby Azure front-Premium, vytvoří přední dveře vaším jménem privátní koncový bod z regionální privátní sítě předních dveří. Tento koncový bod spravuje služba Azure front-dveří. Pro zprávu o schválení od svého původu obdržíte požadavek privátního koncového bodu služby Azure front-dveří. Po schválení žádosti se privátní IP adresa přiřadí z virtuální sítě front-dveří a přenos mezi předními dveřmi Azure a zdrojem projde vytvořeným privátním odkazem s páteřní sítí Azure. Příchozí provoz do vašeho zdroje je teď zabezpečený, když přichází z vašich front Azure.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Povolit privátní koncový bod":::

Přední vrátka Azure podporuje různé typy zdrojů. Pokud je váš zdroj hostovaný na sadě virtuálních počítačů v privátní síti, musíte nejdřív vytvořit interní nástroj pro vyrovnávání zatížení, povolit službu privátního propojení na standardní nástroj pro vyrovnávání zatížení a pak vybrat vlastní typ původu. V části konfigurace privátního propojení vyberte Microsoft. Network/PrivateLinkServices jako typ prostředku. U služeb PaaS Services, jako je webová aplikace Azure a účet úložiště, můžete nejdřív povolit službu privátních odkazů z odpovídajících služeb a vybrat Microsoft. Web/Web pro webovou aplikaci a Microsoft. Storage/StorageAccounts pro typy služeb privátního propojení pro účet úložiště.

## <a name="limitations"></a>Omezení

V rámci veřejné verze Preview jsou v těchto oblastech k dispozici privátní koncové body služby Azure pro front-end: Východní USA, západ 2 USA a Střed USA – jih.

Pro zajištění nejlepší latence byste měli vždycky vybrat oblast Azure, která je nejblíže zdroji, když se rozhodnete povolit koncový bod privátního odkazu na přední dveře.

Veřejné koncové body předních dveří Azure se spravují platformou a v rámci předplatného front-dveří Azure. Přední dvířka Azure umožňují připojení pomocí privátního propojení se stejným zákaznickým předplatným, které se používá k vytvoření profilu front-dveří.

## <a name="next-steps"></a>Další kroky

* Pokud chcete připojit frontu Premium Azure k Virtual Machines pomocí služby privátního propojení, přečtěte si téma [Vytvoření privátního koncového bodu](../../private-link/create-private-endpoint-portal.md).
* Pokud chcete připojit frontu Premium Azure k webové aplikaci prostřednictvím služby privátního propojení, přečtěte si téma [připojení k webové aplikaci pomocí privátního koncového bodu](../../private-link/tutorial-private-endpoint-webapp-portal.md).
* Pokud chcete připojit frontu Premium Azure k účtu úložiště prostřednictvím služby privátního propojení, přečtěte si téma [připojení k účtu úložiště pomocí privátního koncového bodu](../../private-link/tutorial-private-endpoint-storage-portal.md).
