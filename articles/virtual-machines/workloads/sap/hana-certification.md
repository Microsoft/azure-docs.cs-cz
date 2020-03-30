---
title: Certifikace SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Certifikace SAP HANA v Azure (velké instance).
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
ms.openlocfilehash: 2a02f0e1b05b9de8105126d1c9e4e3f79057285f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617232"
---
# <a name="certification"></a>Certifikace

Kromě certifikace NetWeaver vyžaduje SAP speciální certifikaci pro SAP HANA pro podporu SAP HANA na určitých infrastrukturách, jako je Azure IaaS.

Základní poznámka SAP na NetWeaver a do jisté míry certifikace SAP HANA je [SAP Note #1928533 – SAP aplikace v Azure: Podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533).

Certifikační záznamy pro jednotky SAP HANA v Azure (velké instance) najdete na webu [platformy IaaS s certifikací SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 

Typy SAP HANA v Azure (velké instance), uvedené v sap hana certifikovaných platformách IaaS, poskytují zákazníkům Microsoftu a SAP možnost nasadit velké SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA nebo jiné úlohy SAP HANA v Azure. Řešení je založeno na vyhrazeném hardwarovém razítku s certifikací SAP-HANA ([integrace datového centra na míru SAP HANA – TDI](https://scn.sap.com/docs/DOC-63140)). Pokud spustíte řešení nakonfigurované SAP HANA TDI, všechny aplikace založené na SAP HANA (například SAP Business Suite na SAP HANA, SAP BW na SAP HANA, S4/HANA a BW4/HANA) fungují na hardwarové infrastruktuře.

Ve srovnání se spuštěním SAP HANA ve virtuálních vás má toto řešení výhodu. Poskytuje mnohem větší paměťové svazky. Chcete-li povolit toto řešení, musíte pochopit následující klíčové aspekty:

- Aplikační vrstva SAP a aplikace, které nejsou sap, se spouštějí ve virtuálních počítačích, které jsou hostované v obvyklých hardwarových razítkách Azure.
- Místní infrastruktura zákazníků, datová centra a nasazení aplikací jsou propojeny s cloudovou platformou prostřednictvím ExpressRoute (doporučeno) nebo virtuální privátní sítě (VPN). Služba Active Directory a SLUŽBA DNS se také rozšiřují do Azure.
- Instance databáze SAP HANA pro úlohy HANA běží na SAP HANA v Azure (velké instance). Razítko velké instance je připojené k síti Azure, takže software spuštěný ve virtuálních počítačích může pracovat s instancí HANA spuštěnou ve velké instanci HANA.
- Hardware SAP HANA v Azure (Velké instance) je vyhrazený hardware poskytovaný v IaaS s Předinstalovaným Serverem SUSE Linux Enterprise Nebo Red Hat Enterprise Linux. Stejně jako u virtuálních počítačů, další aktualizace a údržba operačního systému je vaší odpovědností.
- Za instalaci HANA nebo dalších součástí potřebných ke spuštění SAP HANA na jednotkách velké instance HANA odpovídáte. Všechny příslušné probíhající operace a správa SAP HANA v Azure jsou také vaší odpovědností.
- Kromě řešení popsaných zde můžete nainstalovat další součásti ve vašem předplatném Azure, které se připojí k SAP HANA v Azure (velké instance). Příklady jsou komponenty, které umožňují komunikaci s databází SAP HANA nebo přímo s ní, jako jsou servery pro přeskoce, servery RDP, SAP HANA Studio, datové služby SAP pro scénáře SAP BI nebo řešení monitorování sítě.
- Stejně jako v Azure hana velká instance nabízí podporu pro vysokou dostupnost a funkce zotavení po havárii.

**Další kroky**
- Odkazovat [dostupné skum pro HLI](hana-available-skus.md) 