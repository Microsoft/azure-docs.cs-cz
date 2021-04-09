---
title: Certifikace SAP HANA v Azure (velké instance) | Microsoft Docs
description: Certifikace SAP HANA v Azure (velké instance).
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
ms.openlocfilehash: a8d4781ad9ca2a30e8523d0e2446dc3903a7cb8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668860"
---
# <a name="certification"></a>Certifikace

Kromě certifikace NetWeaver vyžaduje SAP speciální certifikaci pro SAP HANA podporu SAP HANA na určitých infrastrukturách, jako je Azure IaaS.

Základní Poznámka ke službě SAP na NetWeaver a SAP HANA certifikace, je [SAP – poznámka #1928533 – aplikace SAP v Azure: podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533).

Záznamy o certifikaci pro SAP HANA v Azure (velké instance) najdete na webu [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . 

SAP HANA v Azure (velké instance), na které se říká SAP HANA certifikovaných platforem IaaS, poskytují zákazníkům Microsoftu a SAP možnost nasadit v Azure rozsáhlou sadu SAP Business Suite, SAP BW, S/4 HANA, ČERNOBÍLe/4HANA nebo jiné úlohy SAP HANA. Řešení je založené na certifikovaném hardwarovém razítku SAP-HANA ([SAP HANA přizpůsobené integraci datového centra – TDI](https://scn.sap.com/docs/DOC-63140)). Pokud spustíte SAP HANA řešení s označením TDI, všechny aplikace založené na SAP HANA (například SAP Business Suite na SAP HANA, SAP BW na SAP HANA, S4/HANA a BW4/HANA) fungují na hardwarové infrastruktuře.

V porovnání se spouštěním SAP HANA na virtuálních počítačích má toto řešení výhodu. Poskytuje mnohem větší paměťové svazky. Chcete-li povolit toto řešení, je nutné pochopit následující klíčové aspekty:

- Aplikační vrstva SAP a aplikace jiného typu než SAP běží na virtuálních počítačích, které jsou hostované v obvyklých hardwarových razítkách Azure.
- Místní infrastruktura, datová centra a nasazení aplikací jsou připojené ke cloudové platformě prostřednictvím ExpressRoute (doporučeno) nebo virtuální privátní sítě (VPN). Služby Active Directory a DNS se taky rozšiřují do Azure.
- Instance databáze SAP HANA pro úlohy HANA běží na SAP HANA v Azure (velké instance). Označení velkých instancí je připojené k síti Azure, takže software běžící na virtuálních počítačích může komunikovat s instancí HANA spuštěnou ve velké instanci HANA.
- Hardware SAP HANA v Azure (velké instance) je vyhrazený hardware, který je k dispozici v IaaS s předinstalovaným SUSE Linux Enterprise Server nebo Red Hat Enterprise Linux. Stejně jako u virtuálních počítačů je vám vaše zodpovědnost za další aktualizace a údržbu operačního systému.
- Instalace HANA nebo jakékoli další součásti, které jsou nutné ke spuštění SAP HANA v jednotkách velké instance HANA, je vaše zodpovědnost. Všechny příslušné probíhající operace a Správa SAP HANA v Azure jsou také vaší zodpovědností.
- Kromě řešení popsaných tady můžete nainstalovat další komponenty v rámci předplatného Azure, které se připojují k SAP HANA v Azure (velké instance). Příklady jsou komponenty, které umožňují komunikaci s SAP HANA nebo přímo do databáze, jako jsou servery odkazů, servery RDP, SAP HANA Studio, SAP Data Services pro scénáře SAP BI nebo řešení monitorování sítě.
- Stejně jako v Azure nabízí velká instance HANA podporu pro funkce vysoké dostupnosti a zotavení po havárii.

**Další kroky**
- Projděte si [dostupné SKU pro HLI](hana-available-skus.md) 