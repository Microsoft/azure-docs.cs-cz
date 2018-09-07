---
title: Datové rozšíření a ovládání datových vrstev na uzly pro SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Datové uzly pro SAP HANA v Azure (velké instance) do vrstev a rozšíření.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 87c2cb3e373b76685fca09eb0cfeefdc9216df77
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028064"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Použití rozšíření a ovládání datových vrstev na data uzlů SAP HANA

SAP podporuje datový model vrstev pro SAP BW dvě různé verze SAP Netweaveru a SAP BW/4HANA. Další informace o modelu vrstev dat, naleznete v dokumentu SAP [SAP BW/4HANA a SAP BW on HANA se SAP HANA rozšíření uzly](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
S velká Instance HANA můžete použít možnost 1 konfiguraci uzlů rozšíření SAP HANA, jak je popsáno v dokumentech blogu – nejčastější dotazy a SAP. Možnost 2 konfigurace můžete nastavit s následující SKU velké Instance HANA: S72m S192, S192m, S384 a S384m. 

Při pohledu na dokumentaci výhodu v podobě nemusí zobrazovat okamžitě. Ale když se podíváte na pokyny pro nastavení velikosti SAP, můžete zobrazit výhodu pomocí možnosti-1 a 2 možnost rozšíření uzly SAP HANA. Tady jsou příklady:

- Pokyny pro nastavení velikosti SAP HANA obvykle vyžadují dvojnásobek objem dat jako paměť. Při spuštění vaší instance SAP HANA s výměně dat máte pouze 50 % nebo méně paměti naplněný daty. Zbývající část paměti se nachází v ideálním případě pro SAP HANA dělat svou práci.
- To znamená v jednotce S192 velké Instance HANA 2 TB paměti, s databázi SAP BW máte pouze 1 TB jako datový svazek.
- Pokud používáte další uzel výrazu SAP HANA možnost-1, také S192 HANA velké Instance SKU, poskytuje dodatečnou kapacitu 2 TB pro datový svazek. V konfiguraci možnost 2 získáte další 4 TB pro teplé datový svazek. Ve srovnání s aktivní uzel, kapacita paměti úplné "teplé" rozšíření uzlu je možné pro ukládání dat pro možnost-1. Double paměti lze použít pro datový svazek v konfiguraci uzlu Rozšíření možnost 2 SAP HANA.
- Můžete skončit s kapacitou 3 TB pro vaše data a horkou záložním poměr 1:2 pro možnost-1. Máte 5 TB dat a poměru 1:4 s konfigurací uzlu rozšíření pro možnost-2.

Čím vyšší objem dat ve srovnání s pamětí, vyšší pravděpodobnost jsou uložená data warm vyžadujete pro pro službu disk storage.

**Další kroky**
- Přečtěte si [architektura SAP HANA (velké instance) v Azure](hana-architecture.md)