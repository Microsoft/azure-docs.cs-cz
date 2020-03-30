---
title: Azure Spring Cloud geo-zotavení po havárii | Dokumenty společnosti Microsoft
description: Zjistěte, jak chránit aplikaci Spring Cloud před regionálními výpadky
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279141"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Obnovení po havárii Azure Spring Cloud

Tento článek vysvětluje některé strategie, které můžete použít k ochraně aplikací Azure Spring Cloud před prostojům.  V jakékoli oblasti nebo datovém centru může dojít k prostojům způsobeným regionálními katastrofami, ale pečlivé plánování může zmírnit dopad na vaše zákazníky.

## <a name="plan-your-application-deployment"></a>Plánování nasazení aplikace

Aplikace Azure Spring Cloud běží v určité oblasti.  Azure funguje v několika zeměpisných oblastech po celém světě. Azure geografie je definovaná oblast světa, která obsahuje alespoň jednu oblast Azure. Oblast Azure je oblast v rámci zeměpisné oblasti, obsahující jedno nebo více datových center.  Každá oblast Azure je spárována s jinou oblastí v rámci stejné zeměpisné oblasti a společně vytváří místní pár. Azure serializuje aktualizace platformy (plánovaná údržba) napříč regionálními páry a zajišťuje, že se aktualizuje pouze jedna oblast v každé dvojici najednou. V případě výpadku ovlivňujícího více oblastí bude alespoň jedna oblast v každé dvojici upřednostněna pro obnovení.

Zajištění vysoké dostupnosti a ochrany před katastrofami vyžaduje nasazení aplikací Spring Cloud do více oblastí.  Azure poskytuje seznam [spárovaných oblastí,](../best-practices-availability-paired-regions.md) takže můžete plánovat nasazení Spring Cloud na regionální páry.  Při navrhování architektury mikroslužeb doporučujeme zvážit tři klíčové faktory: dostupnost oblasti, spárované oblasti Azure a dostupnost služeb.

*  Dostupnost oblasti: Zvolte geografickou oblast v blízkosti uživatelů, abyste minimalizovali zpoždění sítě a čas přenosu.
*  Spárované oblasti Azure: Vyberte spárované oblasti ve zvolené geografické oblasti, abyste zajistili koordinované aktualizace platformy a v případě potřeby upřednostnili úsilí o obnovení.
*  Dostupnost služeb: Rozhodněte se, zda mají spárované oblasti běžet horké/horké, horké/teplé nebo teplé/studené.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Použití Azure Traffic Manageru ke směrování provozu

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) poskytuje vyrovnávání zatížení provozu na základě DNS a může distribuovat síťový provoz napříč několika oblastmi.  Azure Traffic Manager slouží k nasměrování zákazníků na nejbližší instanci služby Azure Spring Cloud.  Chcete-li dosáhnout nejlepšího výkonu a redundance, nasměrujte veškerý provoz aplikací prostřednictvím Azure Traffic Manageru před jeho odesláním do služby Azure Spring Cloud.

Pokud máte aplikace Azure Spring Cloud ve více oblastech, použijte Azure Traffic Manager k řízení toku provozu do vašich aplikací v každé oblasti.  Definujte koncový bod Azure Traffic Manager u každé služby pomocí IP služby. Zákazníci by se měli připojit k názvu DNS Azure Traffic Manageru, který ukazuje na službu Azure Spring Cloud.  Azure Traffic Manager vyrovnává zatížení provoz přes definované koncové body.  Pokud dojde k havárii datového centra, Azure Traffic Manager nasměruje provoz z této oblasti do jeho dvojice, zajištění kontinuity služeb.