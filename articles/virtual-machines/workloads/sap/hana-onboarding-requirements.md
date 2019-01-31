---
title: Požadavků na připojení pro SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Požadavků na připojení pro SAP HANA v Azure (velké instance).
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
ms.openlocfilehash: 9a445e5a7d230195472c2202d3f40458a23883e9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477844"
---
# <a name="onboarding-requirements"></a>Požadavky na připojení

Tento seznam sestaví požadavky pro spuštění SAP HANA v Azure (větší instance).

**Microsoft Azure**

- Předplatné Azure, které lze propojit k SAP HANA v Azure (velké instance).
- Smlouvu o podpoře Microsoft Premier. Konkrétní informace související se systémem SAP v Azure najdete v tématu [SAP Support Poznámka #2015553 – SAP v Microsoft Azure: Požadavky pro podporu](https://launchpad.support.sap.com/#/notes/2015553). Pokud používáte jednotky velká Instance HANA s 384 a více procesorů, potřebujete také rozšířit na Premier Azure Rapid Response zahrnout smlouvu o podpoře.
- Sledování HANA velké Instance SKU je nutné po provedení nastavení velikosti cvičení s řešením SAP.

**Připojení k síti**

- ExpressRoute mezi místní do Azure: Pro připojení k Azure v místním datovém centru, ujistěte se, že k seřazení alespoň 1 GB/s připojení od svého poskytovatele internetových služeb. Připojení mezi velká Instance HANA jednotky a Azure používá také technologii ExpressRoute. Toto připojení ExpressRoute mezi jednotkami velká Instance HANA a Azure je zahrnutá v ceně jednotek velká Instance HANA. Proto se zákazníky, nevyskytnou dodatečné poplatky nad rámec vašeho spojení ExpressRoute mezi místní a Azure.

**Operační systém**

- Licence pro SUSE Linux Enterprise Server 12 pro aplikace SAP.

   > [!NOTE] 
   > Operační systém od společnosti Microsoft není zaregistrován SUSE. Není připojen k instanci nástroje pro správu předplatného.

- SUSE Linux předplatné nástroj pro správu nasazených v Azure na virtuálním počítači. Tento nástroj poskytuje možnosti pro SAP HANA v Azure (velké instance) zaregistrované a v uvedeném pořadí aktualizoval SUSE. (Není v rámci datového centra velká Instance HANA bez připojení k Internetu.) 
- Licence pro Red Hat Enterprise Linux 6.7 nebo 7.x pro SAP HANA.

   > [!NOTE]
   > Operační systém od společnosti Microsoft není zaregistrován Red Hat. Není připojen k instanci správce předplatného Red Hat.

- Red Hat správce předplatného nasazené v Azure na virtuálním počítači. Správce předplatného Red Hat poskytuje možnosti pro SAP HANA v Azure (velké instance) zaregistrované a aktualizovat v uvedeném pořadí by Red Hat. (Neexistuje žádný přímý přístup k Internetu z v rámci tenanta nasazené na razítku velkých instancí Azure.)
- SAP vyžaduje podporu kontrakt s poskytovatele Linuxu. Tento požadavek se neodebere řešení pro velké Instance HANA nebo skutečnost, že spouštíte Linux v Azure. Na rozdíl od některých z Galerie imagí Azure pro Linux, poplatky za službu se *není* součástí nabídky řešení velké instance HANA. Je vaší odpovědností pro splnění požadavků SAP ohledně smlouvy o podpoře s distributora Linuxu. 
   - SUSE Linux, vyhledejte požadavky Smlouvy o podpoře v [SAP Poznámka #1984787 - operačním systémem SUSE Linux Enterprise Server 12: Poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787) a [SAP Poznámka #1056161 – SUSE prioritní podporu pro aplikace SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Pro Red Hat Linux musíte mít správné předplatné úrovně, které zahrnují podporu a služby aktualizace pro operační systémy velká Instance HANA. Red Hat doporučuje předplatného Red Hat Enterprise Linuxu pro řešení SAP. Přečtěte si https://access.redhat.com/solutions/3082481. 

Matice podpory různých verzí SAP HANA s různými verzemi systému Linux, najdete v části [2235581 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2235581).

Matice kompatibility operačního systému a verze firmwaru a ovladače HLI, najdete v tématu [Upgrade operačního systému pro HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Typ II jednotek pouze SLES 12 SP2 operačního systému verze se v tuto chvíli nepodporuje. 


**Database**

- Licence a software instalační komponenty pro SAP HANA (platformy nebo enterprise edition).

**Aplikace**

- Licence a software instalační komponenty pro všechny aplikace SAP, které se připojují k SAP HANA a SAP související podporu kontraktů.
- Licence a software instalační komponenty pro všechny aplikace mimo systém SAP použít se SAP HANA v prostředí Azure (velké instance) a související smlouvy o podpoře.

**Dovednosti**

- Zkušenosti s a znalosti Azure IaaS a jeho součástí.
- Prostředí s a znalosti o tom, jak nasadit úloh SAP v Azure.
- Certifikace osobní instalace SAP HANA.
- SAP architekt dovednosti: pište návrh vysoké dostupnosti a zotavení po havárii kolem SAP HANA.

**SAP**

- Očekává se, že jste je zákazníkem v systému SAP a mají podporu kontraktu s řešením SAP.
- Zejména pro implementace třídy typu II SKU velké Instance HANA konzultujte s řešením SAP verze SAP HANA a konečný výsledek konfigurace na velkých a velkých vertikálně navýšit kapacitu hardwaru.

**Další kroky**
- Přečtěte si [architektura SAP HANA (velké instance) v Azure](hana-architecture.md)