---
title: Služba peering Service – Nejčastější dotazy
titleSuffix: Azure
description: Služba peering Service – Nejčastější dotazy
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75775469"
---
# <a name="peering-service---faqs"></a>Služba peering Service – Nejčastější dotazy

Pro obecné otázky si můžete přečíst následující informace.

**Může dopravce používat stávající přímé partnerské vztahy s Microsoftem k podpoře služby partnerského vztahu?**

Ano, dopravce může využít svůj stávající PNI k podpoře služby partnerského vztahu. Služba peering Service PNI vyžaduje rozmanitost pro podporu HA. Pokud již stávající PNI mají různou rozmanitost, není nutná žádná nová infrastruktura. Pokud stávající PNI potřebuje rozmanitost, pak ho můžete rozšířit.

**Může dopravce použít nové přímé partnerské vztahy s Microsoftem k podpoře služby partnerského vztahu?**

Ano, to je také možné. Microsoft bude spolupracovat s dopravcem, aby vytvořil nový přímý partnerský vztah pro podporu služby partnerského vztahu.  

**Proč je přímým partnerským vztahem požadavek na podporu služby partnerského vztahu?**

Jedním z primárních ovladačů za službu peering Service je poskytování připojení k Microsoft online služby prostřednictvím dobře připojeného SP. PNI jsou vždycky v rozsahu GB/s a tudíž základní stavební blok pro konektivitu s vysokou propustností mezi dopravcem a Microsoftem.

**Jaké jsou požadavky na rozmanitost v přímém partnerském vztahu pro podporu služby partnerského vztahu?**

PNI musí podporovat místní redundanci a geografickou redundanci. Místní redundance je definována jako dvě různé sady cest v konkrétní lokalitě partnerských vztahů. Geografická redundance vyžaduje, aby měl dopravce další připojení na jiné lokalitě Microsoft Edge pro případ, že primární lokalita nebude úspěšná. V případě, že je možné přesměrovat provoz prostřednictvím záložní lokality, může být operátor trvání krátkého selhání.

**Dopravce už nabízí smlouvu SLA a podnikovou úroveň, jak se tato nabídka liší?**

Někteří poskytovatelé nabízejí na své straně sítě smlouvu SLA a podnikové úrovně na internetu. V rámci služby peering Service nabízí společnost Microsoft provoz nabídky SLA v rámci sítě Microsoftu. Výběrem možnosti zákazník služby peering Service se dostanete k koncovým smlouvám SLA. Smlouva SLA od jejich lokality po Microsoft Edge na síti poskytovatele internetových služeb může být pokrytá poskytovatelem internetových služeb. Smlouva SLA v globální síti Microsoftu od Microsoft Edge po koncové uživatele je teď pokrytá Microsoftem.

**Pokud již poskytovatel služeb s Microsoftem používá PNI, pak bude pro podporu služby peering Service nutný typ změn?**

* Změny softwaru k identifikaci uživatele partnerské služby a jeho provozu. Může vyžadovat změny zásad směrování pro výměnu provozu uživatele s nejbližším Microsoft Edge prostřednictvím připojení služby partnerského vztahu.
* Ujistěte se, že konektivita má místní redundanci a geografickou redundanci.
