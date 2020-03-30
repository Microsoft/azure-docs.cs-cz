---
title: Požadavky na připojení pro SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Požadavky na připojení pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 141a75a75a214ff4a6f136df7570d6e81f7f4e82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617006"
---
# <a name="onboarding-requirements"></a>Požadavky na onboarding

Tento seznam sestavuje požadavky na spuštění SAP HANA v Azure (větší instance).

**Microsoft Azure**

- Předplatné Azure, které se dá propojit se SAP HANA v Azure (velké instance).
- Smlouva o podpoře Microsoft Premier. Konkrétní informace týkající se spuštění SAP v Azure najdete v [tématu SAP Support Note #2015553 – SAP v Microsoft Azure: Požadavky na podporu](https://launchpad.support.sap.com/#/notes/2015553). Pokud používáte jednotky velké instance HANA s procesory 384 a více, musíte také rozšířit smlouvu premier podpory tak, aby zahrnovala Azure Rapid Response.
- Povědomí o hana velké instance skum, které potřebujete po provedení cvičení velikosti s SAP.

**Připojení k síti**

- ExpressRoute mezi místním i Azure: Chcete-li připojit místní datové centrum k Azure, nezapomeňte si objednat připojení alespoň 1 Gb/s od svého isp. Připojení mezi jednotkami velké instance HANA a Azure také používá technologii ExpressRoute. Toto připojení ExpressRoute mezi jednotkami velké instance HANA a Azure je zahrnuto v ceně jednotek velké instance HANA, včetně všech poplatků za příchozí přenos dat a odchozí přenos dat pro tento konkrétní okruh ExpressRoute. Proto vy jako zákazník nenarazíte na dodatečné náklady nad rámec vašeho ExpressRoute propojení mezi místní a Azure.

**Operační systém**

- Licence pro SUSE Linux Enterprise Server 12 pro aplikace SAP.

   > [!NOTE] 
   > Operační systém dodaný společností Microsoft není registrován u suse. Není připojen k instanci nástroje pro správu předplatného.

- Nástroj pro správu předplatného SUSE Linux nasazený v Azure na virtuálním počítači. Tento nástroj poskytuje možnost pro SAP HANA v Azure (velké instance), které mají být registrovány a příslušné suse. (V datovém centru velké instance HANA není přístup k internetu.) 
- Licence pro Red Hat Enterprise Linux 6.7 nebo 7.x pro SAP HANA.

   > [!NOTE]
   > Operační systém dodaný společností Microsoft není registrován u společnosti Red Hat. Není připojen k instanci Správce předplatného Red Hat.

- Správce předplatného Red Hat nasazený v Azure na virtuálním počítači. Správce předplatného Red Hat poskytuje funkci pro SAP HANA v Azure (velké instance), které mají být registrovány a respektive aktualizovány Red Hat. (Neexistuje žádný přímý přístup k internetu z v rámci klienta nasazené na razítko Azure Velké instance.)
- SAP vyžaduje, abyste měli smlouvu o podpoře se svým poskytovatelem Linuxu. Tento požadavek není odebrána řešení HANA velké instance nebo skutečnost, že spuštění Linuxu v Azure. Na rozdíl od některých bitových kopií galerie Linux Azure *není* servisní poplatek zahrnut v nabídce řešení HANA Large Instance. Je vaší odpovědností splnit požadavky společnosti SAP týkající se smluv o podpoře s distributorem Linuxu. 
   - Pro SUSE Linux vyhledejte požadavky na smlouvy podpory v [SAP Note #1984787 - SUSE Linux Enterprise Server 12: Poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787) a SAP Note #1056161 - prioritní podpora [SUSE pro aplikace SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Pro Red Hat Linux, musíte mít správné úrovně předplatného, které zahrnují podporu a aktualizace služeb pro operační systémy HANA Velké instance. Red Hat doporučuje předplatné Red Hat Enterprise Linux pro řešení SAP. Viz https://access.redhat.com/solutions/3082481. 

Matice podpory různých verzí SAP HANA s různými verzemi Linuxu najdete v [tématu SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Matice kompatibility operačního systému a verzí firmwaru/ovladače HLI naleznete v části [Upgrade operačního systému hli](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Pro jednotky typu II je v tomto okamžiku podporována pouze verze operačního systému SLES 12 SP2. 


**Databáze**

- Licence a součásti instalace softwaru pro SAP HANA (platforma nebo enterprise edition).

**Aplikace**

- Licence a součásti instalace softwaru pro všechny aplikace SAP, které se připojují k SAP HANA a souvisejícím smlouvám podpory SAP.
- Licence a součásti instalace softwaru pro všechny aplikace, které nejsou sap používané s prostředím SAP HANA v Azure (velké instance) a souvisejícími smlouvami podpory.

**Dovednosti**

- Zkušenosti s Azure IaaS a jeho součástmi a jejich znalostmi.
- Zkušenosti s a znalosti o tom, jak nasadit úlohy SAP v Azure.
- SAP HANA instalace certifikovaná osobní.
- Sap architekt dovednosti navrhnout vysokou dostupnost a zotavení po havárii kolem SAP HANA.

**SAP**

- Očekává se, že jste zákazníkem SAP a máte smlouvu o podpoře se společností SAP.
- Zejména pro implementace třídy Type II velkých instancí HANA, konzultovat s SAP na verze SAP HANA a případné konfigurace na velkém měřítku-up hardware.

**Další kroky**
- Odkazování [architektury SAP HANA (velké instance) v Azure](hana-architecture.md)