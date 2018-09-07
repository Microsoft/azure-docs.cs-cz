---
title: Certifikace SAP Hana v Azure (velké instance) | Dokumentace Microsoftu
description: Certifikace SAP Hana v Azure (velké instance).
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
ms.openlocfilehash: 1346018d99a1c26290ad8928d9794f3b55050910
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027972"
---
# <a name="certification"></a>Certifikace

Kromě certifikační NetWeaver SAP vyžaduje speciální certifikaci pro SAP HANA a SAP HANA podporují určité infrastruktury, jako je Azure IaaS.

Je základní Poznámka SAP na systému NetWeaver a SAP HANA stupně certifikace [SAP Poznámka #1928533 – aplikace SAP v Azure: podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533).

Záznamy certifikaci pro SAP HANA na jednotky Azure (velké instance) najdete v [platformách IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) lokality. 

SAP HANA v Azure (velké instance) typů uvedených v SAP HANA s certifikací platformy IaaS lokality poskytuje Microsoft a SAP zákazníkům možnost nasazení velké SAP Business Suite, SAP BW, s/4 HANA, BW/4HANA nebo jiné úlohy SAP HANA v Azure. Toto řešení je založeno na razítku certifikovaných vyhrazený hardware SAP HANA ([SAP HANA přizpůsobená data center integrace – TDI](https://scn.sap.com/docs/DOC-63140)). Pokud spouštíte řešení SAP HANA TDI nakonfigurované, všechny aplikace pro systém SAP HANA (jako je SAP Business Suite v SAP HANA, SAP BW on SAP HANA S4/HANA a BW4/HANA) funguje na hardwarové infrastruktury.

Porovnání se spouštěním SAP HANA ve virtuálních počítačích, toto řešení má výhodu. Poskytuje větší objemy paměti. Pokud chcete povolit toto řešení, je potřeba pochopit následující klíčové aspekty:

- SAP aplikací vrstvy a mimo systém SAP aplikace běží ve virtuálních počítačích, které jsou hostované v razítka obvykle hardware Azure.
- Zákazník v místní infrastruktuře, datových center, a nasazení aplikací jsou připojené k platformě cloud prostřednictvím ExpressRoute (doporučeno) nebo virtuální privátní sítě (VPN). Služba Active Directory a DNS také se rozšíří do Azure.
- Instance databáze SAP HANA pro pracovní vytížení HANA běží na systému SAP HANA v Azure (velké instance). Velká Instance razítka je připojen do sítě Azure, aby software spuštěný na virtuálních počítačích můžete pracovat s instance HANA používané velká Instance HANA.
- Hardware systému SAP HANA v Azure (velké instance) je k dispozici v IaaS s operačním systémem SUSE Linux Enterprise Server nebo Red Hat Enterprise Linux a předinstalovaným vyhrazený hardware. Stejně jako u virtuálních počítačů, další aktualizace a údržba pro operační systém je na vás.
- Instalace HANA nebo jakékoli další komponenty potřebné ke spuštění SAP HANA na jednotkách velká Instance HANA je vaší povinností. Všechny příslušné probíhající operace a správa SAP HANA v Azure jsou také vaší povinností.
- Kromě řešení je zde popsáno můžete nainstalovat další komponenty ve vašem předplatném Azure, která se připojuje k SAP HANA v Azure (velké instance). Příklady jsou komponenty, které umožňují komunikaci s, nebo přímo do systému SAP HANA databáze, jako jsou třeba jump servery, servery protokolu RDP, SAP HANA Studio, SAP Data Services pro SAP BI scénáře, nebo řešení pro monitorování sítě.
- Velká Instance HANA stejně jako v Azure, nabízí podporu pro vysokou dostupnost a funkce pro zotavení po havárii.

**Další kroky**
- Přečtěte si [dostupných skladových položek pro HLI](hana-available-skus.md) 