---
title: Požadavky na registraci pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Požadavky na registraci pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16608facab27fc18924cf3a5c6da7e35db048f76
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675616"
---
# <a name="onboarding-requirements"></a>Požadavky na onboarding

Tento seznam uvádí požadavky na spouštění SAP HANA v Azure (větší instance).

**Microsoft Azure**

- Předplatné Azure, které se dá propojit s SAP HANA v Azure (velké instance).
- Smlouvu Microsoft Premier Support. Konkrétní informace související s provozem SAP v Azure najdete v článku o [podpoře SAP poznámka #2015553 – SAP v Microsoft Azure: požadavky na podporu](https://launchpad.support.sap.com/#/notes/2015553). Pokud používáte velké jednotky instancí HANA s 384 a více procesory, musíte také pro smlouvu Premier Support zvětšit, aby zahrnovala Azure Rapid Response.
- Povědomí o SKU velkých instancí HANA, které potřebujete, po provedení cvičení změny velikosti pomocí SAP.

**Připojení k síti**

- ExpressRoute mezi místním prostředím a Azure: Pokud chcete připojit vaše místní datové centrum k Azure, ujistěte se, že je u poskytovatele internetových služeb nutné objednat aspoň jedno připojení s 1 GB/s. Připojení mezi jednotkami velkých instancí HANA a Azure využívá i technologii ExpressRoute. Toto připojení ExpressRoute mezi jednotkami velkých instancí HANA a Azure je zahrnuté v ceně jednotek velkých instancí HANA včetně všech poplatků za příchozí a odchozí přenos dat tohoto konkrétního okruhu ExpressRoute. Proto jste jako zákazník nenarazili na další náklady za propojení ExpressRoute mezi místními a Azure.

**Operační systém**

- Licence pro SUSE Linux Enterprise Server 12 pro aplikace SAP.

   > [!NOTE] 
   > Operační systém dodaný Microsoftem není zaregistrován u SUSE. Není připojený k instanci nástroje pro správu předplatného.

- Nástroj pro správu předplatných SUSE Linux nasazený v Azure na virtuálním počítači. Tento nástroj poskytuje možnost SAP HANA v Azure (velké instance), která se má zaregistrovat a aktualizovat pomocí SUSE. (K dispozici není žádný přístup k Internetu v rámci datového centra velké instance HANA.) 
- Licence pro Red Hat Enterprise Linux 6,7 nebo 7. x pro SAP HANA.

   > [!NOTE]
   > Operační systém dodaný Microsoftem není zaregistrovaný ve Red Hat. Není připojený k instanci správce předplatného Red Hat.

- Správce předplatného Red Hat byl nasazený v Azure na virtuálním počítači. Správce předplatného Red Hat nabízí možnost SAP HANA v Azure (velké instance), která se má zaregistrovat a v aktualizovaném případě Red Hat. (Není k dispozici přímý přístup k Internetu z klienta nasazeného v rámci razítka velké instance Azure.)
- SAP vyžaduje, abyste měli smlouvu o podpoře i s vaším poskytovatelem Linux. Tento požadavek se neodebere z řešení velké instance HANA ani ze skutečnosti, že na Azure spouštíte Linux. Na rozdíl od některých imagí z Galerie Azure pro Linux se v nabídce řešení velké instance HANA *nezahrne poplatek* za služby. Je vaší zodpovědností splnit požadavky SAP týkající se smluv o podpoře s distributorem Linux. 
   - V případě systému SUSE Linux vyhledejte požadavky na smlouvy o podpoře v části [SAP note #1984787-SUSE Linux Enterprise Server 12: poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787) a zpráva [SAP poznámky #1056161-SUSE priorita pro aplikace SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - V případě systému Red Hat Linux musíte mít správné úrovně předplatného, které zahrnují podporu a aktualizace služeb pro operační systémy velké instance HANA. Red Hat doporučuje Red Hat Enterprise Linux předplatné pro řešení SAP. Odkaz https://access.redhat.com/solutions/3082481 . 

Pro matrici podpory různých verzí SAP HANA s různými verzemi systému Linux najdete informace v tématu [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Pro matrici kompatibility operačního systému a verze firmwaru nebo ovladače HLI se podívejte na [upgrade operačního systému pro HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Pro jednotky typu II se v tomto okamžiku podporuje pouze verze operačního systému SLES 12 SP2. 


**Databáze**

- Licence a součásti instalace softwaru pro SAP HANA (platforma nebo Enterprise Edition).

**Aplikace**

- Licence a součásti instalace softwaru pro jakékoli aplikace SAP, které se připojují k SAP HANA a související smlouvy SAP Support.
- Součásti pro licence a instalace softwaru pro jakékoli aplikace bez SAP používané v prostředí SAP HANA v Azure (velké instance) a související smlouvy o podpoře.

**Dovednosti**

- Využijte a znalosti Azure IaaS a jejích komponent.
- Vyzkoušejte a Naučte se, jak nasadit úlohu SAP v Azure.
- SAP HANA instalaci certifikovaného osobního zařízení.
- Dovednosti v architektech SAP pro návrh vysoké dostupnosti a zotavení po havárii SAP HANA.

**SAP**

- Očekává se, že jste zákazníkem SAP a máte smlouvu o podpoře s SAP.
- Zejména u implementací třídy II třídy typu SKU velkých instancí služby HANA si projděte článek SAP ve verzích SAP HANA a případné konfigurace pro velký hardware s horizontálním škálováním.

**Další kroky**
- Informace [o architektuře SAP Hana (velké instance) v Azure](hana-architecture.md)