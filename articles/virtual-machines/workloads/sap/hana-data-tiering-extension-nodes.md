---
title: Vrstvení dat a rozšiřující uzly pro SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Vrstvení dat a rozšiřující uzly pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3553ac9704ac26d0bdaae0f93b89f41a87ac716
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617167"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Použití datových vrstvení a rozšiřujících uzlů SAP HANA

SAP podporuje model vrstvení dat pro SAP BW různých verzí SAP NetWeaver a SAP BW/4HANA. Další informace o modelu vrstvení dat naleznete v dokumentu SAP [SAP BW/4HANA a SAP BW na HANA s rozšiřujícími uzly SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
S hana velké instance, můžete použít možnost-1 konfigurace SAP HANA rozšíření uzlů, jak je vysvětleno v faq a SAP blogu dokumenty. Konfigurace option-2 lze nastavit pomocí následujících velkých instancí HANA SKU: S72m, S192, S192m, S384 a S384m. 

Když se podíváte na dokumentaci, výhoda nemusí být viditelné okamžitě. Ale když se podíváte na pokyny pro velikost SAP, můžete vidět výhodu pomocí rozšiřujících uzlů option-1 a option-2 SAP HANA. Tady jsou příklady:

- Sap HANA zásady pro nastavení velikosti obvykle vyžadují dvojnásobné množství objemu dat jako paměti. Při spuštění instance SAP HANA s aktivní data, máte pouze 50 procent nebo méně paměti naplněné daty. Zbytek paměti je ideálně držen pro SAP HANA dělá svou práci.
- To znamená, že v jednotce HANA Large Instance S192 s 2 TB paměti, se spuštěnou databází SAP BW, máte jako datový svazek pouze 1 TB.
- Pokud používáte další rozšíření SAP HANA uzel option-1, také SKU velké instance S192 HANA, poskytuje další kapacitu 2 TB pro objem dat. V konfiguraci option-2 získáte další 4 TB pro teplý objem dat. Ve srovnání s horkým uzlem lze plnou kapacitu paměti "teplého" rozšiřujícího uzlu použít pro ukládání dat pro možnost 1. Dvojnásobná paměť lze použít pro datový svazek v konfiguraci rozšíření OPTION-2 SAP HANA.
- Pro vaše data získáte kapacitu 3 TB a poměr tepla a tepla 1:2 pro volbu 1. Máte 5 TB dat a poměr 1:4 s konfigurací rozšíření option-2.

Čím vyšší je objem dat ve srovnání s pamětí, tím vyšší je pravděpodobnost, že teplá data, o která žádáte, jsou uložena na diskovém úložišti.

**Další kroky**
- Odkazování [architektury SAP HANA (velké instance) v Azure](hana-architecture.md)