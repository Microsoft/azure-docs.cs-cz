---
title: Uzly datových vrstev a rozšíření pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Uzly datových vrstev a rozšíření pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb780d10996cb22f5e6fe5bc8889e897e8c3854d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666768"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Použití SAP HANAch uzlů pro vrstvení dat a rozšíření

SAP podporuje model datové vrstvy pro SAP BW různých verzí SAP NetWeaver a SAP BW/4HANA. Další informace o modelu datové vrstvy najdete v dokumentu SAP [SAP BW/4HANA a SAP BW na Hana s uzly rozšíření SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
S velkou instancí HANA můžete použít konfiguraci možností SAP HANA s příponou Option-1, jak je vysvětleno v dokumentech na blogu s nejčastějšími dotazy a SAP. Konfigurace Option-2 se dá nastavit s následujícími SKU velkých instancí HANA: S72m, S192, S192m, S384 a S384m. 

Když se podíváte na dokumentaci, výhoda se nemusí zobrazit hned. Ale když se podíváte na pokyny pro určení velikosti SAP, uvidíte výhodu pomocí uzlů rozšíření Option-1 a Option-2 SAP HANA. Tady jsou příklady:

- Pokyny pro změny velikosti SAP HANA obvykle vyžadují dvojnásobek objemu objemů dat jako paměti. Při spuštění instance SAP HANA s aktivními daty máte jenom 50% nebo méně paměti vyplněnou daty. Zbývající část paměti je ideální pro SAP HANA provádění její práce.
- To znamená, že S192 jednotka velké instance v HANA má 2 TB paměti a spustí databázi SAP BW, stačí mít 1 TB jako datový svazek.
- Pokud použijete další uzel rozšíření SAP HANA s možností-1, také SKU velké instance S192 HANA, získáte další 2 TB kapacity objemu dat. V konfiguraci možnosti 2 získáte další 4 TB pro teplý objem dat. V porovnání s aktivním uzlem je možné použít úplnou kapacitu paměti uzlu "teplého" rozšíření pro ukládání dat pro možnost-1. Dvojnásobnou velikost paměti lze použít pro datový svazek v konfiguraci uzlu Option-2 SAP HANA rozšíření.
- Pro vaše data máte kapacitu o kapacitě 3 TB a poměr Hot-to-teplu 1:2 pro možnost 1. Máte 5 TB dat a poměr 1:4 s konfigurací uzlu Option-2.

Čím vyšší je objem dat, který je v porovnání s pamětí, tím větší je pravděpodobnost, že se v úložišti na disku ukládají data, která požadujete pro.

**Další kroky**
- Informace [o architektuře SAP Hana (velké instance) v Azure](hana-architecture.md)