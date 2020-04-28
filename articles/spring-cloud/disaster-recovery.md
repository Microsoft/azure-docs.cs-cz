---
title: Azure jaře Cloud – geografická zotavení po havárii | Microsoft Docs
description: Informace o tom, jak chránit svoji jarní cloudovou aplikaci před místními výpadky
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279141"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Zotavení po havárii v cloudu Azure jaře

Tento článek vysvětluje některé strategie, které můžete použít k ochraně vašich aplikací v cloudovém prostředí Azure, aby nedocházelo k výpadkům.  Jakékoli oblasti nebo datové centrum může nastat výpadky způsobené místními katastrofami, ale pečlivé plánování může zmírnit dopad na vaše zákazníky.

## <a name="plan-your-application-deployment"></a>Plánování nasazení aplikace

Aplikace pro jarní Cloud v Azure běží v konkrétní oblasti.  Azure funguje v několika zeměpisných oblastech po celém světě. Zeměpisná oblast Azure je definovaná oblast světa, která obsahuje alespoň jednu oblast Azure. Oblast Azure je oblast v rámci geografické oblasti, která obsahuje jedno nebo více datových center.  Každá oblast Azure je spárovaná s jinou oblastí v rámci stejné geografické oblasti a tvoří tak místní dvojici. Azure serializovat aktualizace platforem (plánovaná údržba) napříč místními páry, aby bylo zajištěno, že se v každém páru aktualizuje jenom jedna oblast. V případě výpadku, který má vliv na více oblastí, bude mít nejméně jedna oblast v každém páru určenou prioritu pro obnovení.

Zajištění vysoké dostupnosti a ochrany před haváriemi vyžaduje, abyste nasadili své jarní cloudové aplikace do více oblastí.  Azure poskytuje seznam [spárovaných oblastí](../best-practices-availability-paired-regions.md) , abyste mohli plánovat nasazování vašich jarních cloudů do regionálních párů.  Při navrhování architektury mikroslužeb doporučujeme vzít v úvahu tři klíčové faktory: dostupnost oblastí, spárované oblasti Azure a dostupnost služeb.

*  Dostupnost oblastí: vyberte geografickou oblast blízko vašim uživatelům, abyste minimalizovali prodlevu sítě a dobu přenosu.
*  Spárované oblasti Azure: Vyberte spárované oblasti v rámci zvolené geografické oblasti, abyste zajistili koordinované aktualizace platforem a v případě potřeby nastavili úsilí v oblasti obnovení s prioritou.
*  Dostupnost služby: Rozhodněte, jestli mají spárované oblasti běžet za horkou, horkou, horkou nebo studenou.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Použití Azure Traffic Manager ke směrování provozu

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) poskytuje vyrovnávání zatížení provozu založené na službě DNS a může distribuovat síťový provoz napříč několika oblastmi.  Využijte Azure Traffic Manager k přímému nasměrování zákazníků do nejbližší instance služby Azure jaře Cloud Service.  Abyste dosáhli nejlepšího výkonu a redundance, přesměrujte veškerý provoz aplikací prostřednictvím Azure Traffic Manager před odesláním do vaší jarní cloudové služby Azure.

Pokud máte ve více oblastech aplikace pro jarní cloudy Azure, použijte Azure Traffic Manager k řízení toku provozu do aplikací v jednotlivých oblastech.  Pro každou službu pomocí IP adresy služby Definujte koncový bod Azure Traffic Manager. Zákazníci by se měli připojit k názvu DNS Azure Traffic Manager, který odkazuje na jarní cloudovou službu Azure.  Azure Traffic Manager vyrovnává zatížení napříč definovanými koncovými body.  Pokud dojde k výpadku datového centra, Azure Traffic Manager přesměruje provoz z této oblasti na svůj pár a zajišťuje kontinuitu služeb.