---
title: Služba partnerského vztahu – nejčastější dotazy
titleSuffix: Azure
description: Služba partnerského vztahu – nejčastější dotazy
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775469"
---
# <a name="peering-service---faqs"></a>Služba partnerského vztahu – časté otázky

Níže si můžete prohlédnout níže uvedené informace o obecných otázkách.

**Může operátor pomocí svého stávajícího přímého partnerského vztahu se společností Microsoft podporovat službu partnerského vztahu?**

Ano, dopravce může využít své stávající PNI k podpoře služby Peering Service. Peering Service PNI vyžaduje rozmanitost pro podporu HA. Pokud stávající PNI již má rozmanitost, pak není nutná žádná nová infrastruktura. Pokud stávající PNI potřebuje rozmanitost, pak to může být rozšířena.

**Může operátor používat nový přímý partnerský vztah se společností Microsoft k podpoře služby partnerského vztahu?**

Ano, to je také možné. Společnost Microsoft bude spolupracovat se společností Carrier na vytvoření nového přímého partnerského vztahu pro podporu služby partnerského vztahu.  

**Proč je přímý partnerský vztah požadavkem na podporu služby partnerského vztahu?**

Jedním z primárních ovladačů služby Partnerského vztahu je poskytování připojení k online službám společnosti Microsoft prostřednictvím dobře propojené hospo- PNI jsou vždy v rozsahu Gbps, a proto základní stavební blok pro vysokou propustnost připojení mezi dopravcem a Microsoft.

**Jaké jsou požadavky na rozmanitost přímého partnerského vztahu na podporu služby partnerského vztahu?**

PNI musí podporovat místní redundanci a geografickou redundanci. Místní redundance je definována jako dvě různorodé sady cest v konkrétním partnerskému webu. Geografická redundance vyžaduje, aby společnost Carrier získala další připojení v jiné hraniční síti společnosti Microsoft v případě selhání primární lokality. Pro krátké doby trvání selhání dopravce může směrovat provoz přes záložní lokality.

**Dopravce již nabízí SLA a podnikové třídy internet, jak je to nabídka odlišná?**

Někteří operátoři nabízejí na své části sítě síť SLA a internet podnikové třídy. V peeringové službě bude Microsoft nabízet službu SLA, která nabízí provoz v části sítě společnosti Microsoft. Výběrem služby partnerského vztahu zákazník získá komplexní sla. Program SLA z jejich webu na okraj společnosti Microsoft v síti isp může být pokryt a služeb IsP. Program SLA v microsoft global networku od microsoft edge to end users aplikace je nyní pokrytspolečností Microsoft.

**Pokud poskytovatel služeb již kojené s Microsoft pomocí PNI pak jaký druh změn jsou nutné pro podporu peering služby?**

* Změny softwaru k identifikaci uživatele služby Partnerského vztahu a jeho provozu. Může vyžadovat změny zásad směrování k výměně provozu uživatele na nejbližším okraji společnosti Microsoft prostřednictvím připojení služby Partnerského vztahu.
* Ujistěte se, že připojení má místní redundanci a geografickou redundanci.
