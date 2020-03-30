---
title: Nastavení partnerského vztahu s Microsoftem
titleSuffix: Azure
description: Přehled partnerského vztahu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 576bc3e37711851acd7d6c7ac811a10e40080710
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75908920"
---
# <a name="internet-peering-overview"></a>Přehled partnerského vztahu v Internetu

Partnerský vztah je propojení mezi globální sítí společnosti Microsoft (AS8075) a vaší sítí za účelem výměny internetového provozu z online služeb Microsoftu a služeb Microsoft Azure. Dopravci nebo poskytovatelé služeb mohou požádat o spojení se společností Microsoft v kterékoli z našich lokalit Edge. Každý požadavek je přezkoumánspolečností Microsoft, aby bylo zajištěno, že dodržuje naše zásady partnerského vztahu. Partnerský vztah se sítí Microsoft uvidíte dvěma způsoby:

* **Přímý partnerský vztah:**

    Partnerský vztah je vytvořen přes přímé fyzické připojení mezi sítí Společnosti Microsoft v síti Microsoft Edge a vaší sítě. Relace protokolu BGP jsou konfigurovány v těchto připojeních podle našich zásad směrování a pomocí předem vyjednané smlouvy. To se také označuje jako PNI.

* **Partnerský vztah výměny:**

    Jedná se o standardní veřejná připojení partnerského vztahu na internetových výměnách (IX). Fyzické připojení mezi sítí společnosti Microsoft a sítí jsou prostřednictvím struktury prostředků pro přepínače provozované ix. Relace protokolu BGP jsou konfigurovány pomocí místa IP poskytovaného ix.

## <a name="benefits-of-peering-with-microsoft"></a>Výhody partnerského vztahu se společností Microsoft
* Snižte náklady na dopravu tím, že dodáte provoz Microsoftu pomocí partnerského vztahu se společností Microsoft.
* Zvyšte výkon pro své zákazníky snížením síťového směrování a latence v síti Microsoft Edge.
* Chraňte provoz zákazníků před chybami v síti vašeho poskytovatele sítě nebo poskytovatele dopravy partnerským vztahem s Microsoftem v redundantních umístěních.
* Naučte se metriky výkonu o připojeních partnerského vztahu a využijte přehledy k řešení potíží se sítí.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Výhody použití Azure k nastavení partnerského vztahu

Můžete požádat o partnerský vztah s Microsoftem pomocí Azure PowerShellnebo portálu. Partnerský vztah nastavený tímto způsobem se spravuje jako prostředek Azure a poskytuje následující výhody:
* Zjednodušené a automatizované kroky pro nastavení a správu partnerského vztahu se společností Microsoft.
* Rychlý a snadný způsob zobrazení a správy všech partnerských vrstev na jednom místě.
* Sledujte údaje o stavu a šířce pásma pro všechna připojení.
* Stejné předplatné můžete použít pro přístup ke svým cloudovým službám Azure.

Pokud jste již vytvořili partnerské vztahy se společností Microsoft, jsou označovány jako **starší partnerské vztahy**. Můžete se rozhodnout spravovat takové partnerské partnery jako prostředek Azure, abyste mohli využít výše uvedené výhody. Pokud chcete odeslat nový požadavek partnerského vztahu nebo převést starší partnerský vztah na prostředek Azure, postupujte podle odkazů v části **Další kroky** níže.

## <a name="peering-policy"></a>Zásady peeringu
Společnost Microsoft má selektivní, ale obecně otevřené zásady partnerského vztahu. Partneři jsou vybíráni na základě výkonu, schopností a tam, kde existuje vzájemný prospěch, a podléhají určitým technickým, obchodním a právním požadavkům. Podrobnosti naleznete v [tématu zásady partnerského vztahu](policy.md).

## <a name="faq"></a>Nejčastější dotazy
Nejčastější dotazy týkající se partnerského vztahu naleznete v [tématu Internetový partnerský vztah – časté otázky](faqs.md).

## <a name="next-steps"></a>Další kroky

* Další informace o postupech nastavení přímého partnerského vztahu s Microsoftem postupujte [podle návodu k přímému partnerského vztahu.](walkthrough-direct-all.md)
* Další informace o postupech nastavení partnerského vztahu exchange u Microsoftu postupujte [podle návodu k partnerského vztahu exchange](walkthrough-exchange-all.md)
* Informace o některých dalších klíčových [možnostech sítě](https://docs.microsoft.com/azure/networking/networking-overview) v Azure.
