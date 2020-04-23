---
title: 'SAP v Azure: Jaký software SAP je v Azure podporovaný'
description: Vysvětluje, jaký software SAP je podporován pro nasazení v Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b2f4e7a16c967b26b545d1405f973bf8b8afaae
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086126"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Jaký software SAP je podporován pro nasazení Azure
Tento článek popisuje, jak můžete zjistit, jaký software SAP je podporován pro nasazení Azure a jaké jsou nezbytné verze operačního systému nebo verze DBMS.

Vyhodnocení, zda je podporován aktuální software SAP a jaké verze Operačního systému a DBMS jsou podporovány s vaším softwarem SAP v Azure, budete potřebovat přístup k:

- Poznámky podpory SAP
- Sap Dostupnost produktu Matrix



## <a name="general-restrictions-for-sap-workload"></a>Obecná omezení pro pracovní vytížení SAP
Služby Azure IaaS, které se můžou používat pro úlohy SAP, jsou omezené na hardware x86-64 nebo x64. Neexistuje žádné nabídky založené na procesoru Sparc nebo Napájení, které se vztahují na zatížení SAP. Zákazníci, kteří běží na svých aplikacích v operačních systémech proprietárních hardwarovým architekturám, jako jsou sálové počítače IBM nebo AS400, nebo kde se používají operační systémy HP-UX, Solaris nebo AIX, musí změnit své aplikace SAP včetně DBMS na jeden z následujících operačních systémů:

- Windows server 64bit pro platformu x86-64
- SUSE linux 64bit pro platformu x86-64
- Red hat Linux 64Bit pro platformu x86-64
- Oracle Linux 64bit pro platformu x86-64

V kombinaci se softwarem SAP nejsou podporovány žádné další verze operačního systému nebo distribuce Linuxu. Přesné podrobnosti o konkrétních verzích a případech jsou popsány dále v dokumentu.


## <a name="you-start-here"></a>Začnete zde.
Výchozím bodem pro vás je [poznámka podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Při procházení této poznámky SAP shora dolů se zobrazí několik oblastí podporovaného softwaru a virtuálních počítače.

V první části jsou uvedeny minimální požadavky na provozní verze, které jsou podporovány softwarem SAP ve virtuálních počítačích Azure obecně. Pokud nedosahujete těchto minimálních požadavků a spouštíte starší verze těchto operačních systémů, je třeba upgradovat verzi operačního systému na tak minimální verzi nebo ještě novější verze. Je pravda, že Azure obecně by podporoval starší verze některých z těchto operačních systémů. Ale omezení nebo minimální verze, jak jsou uvedeny, jsou založeny na testech a kvalifikacích provedených a nebudou dále prodlouženy zpět. 


> [!NOTE]
>Existují některé konkrétní typy virtuálních počítačů, hana velké instance nebo sap úlohy, které budou vyžadovat novější verze operačního systému. Podobné případy budou zmíněny v celém dokumentu. Podobné případy jsou jasně zdokumentovány buď v poznámkách SAP, nebo v jiných publikacích SAP.

V následující části jsou uvedeny obecné platformy SAP, které jsou podporovány s podporovanými verzemi a důležitější mise SAP, která jsou podporována. Obsahuje seznam zásobníků NetWeaver/ABAP nebo Java, které jsou podporovány and, které vyžadují minimální verze jádra. Novější zásobníky ABAP jsou podporované v Azure, ale nepotřebují minimální verze jádra, protože změny pro Azure byly implementovány od začátku vývoje novějších zásobníků

Musíte zkontrolovat:

- Na minimální verze, které jsou uvedeny, se vztahuje, zda jsou spuštěny aplikace SAP. Pokud ne, musíte definovat novou cílovou verzi, zkontrolovat matici dostupnosti produktu SAP, jaké sestavení operačního systému a kombinace DBMS jsou podporovány s novou cílovou verzí. Tak, že si můžete vybrat správnou verzi operačního systému a DBMS vydání
- Ať už potřebujete aktualizovat jádra SAP při přechodu do Azure
- Zda potřebujete aktualizovat balíčky podpory SAP. Zejména balíčky základní podpory, které mohou být vyžadovány v případech, kdy jste povinni přejít na novější verzi DBMS


Další část se zabývá dalšími podrobnostmi o dalších produktech SAP a verzích DBMS, které sap podporuje v Azure pro Windows a Linux. 

> [!NOTE]
> Minimální verze různých DBMS je pečlivě vybrána a nemusí vždy odrážet celé spektrum vydání DBMS různé dodavatele DBMS podporu v Azure obecně. Mnoho sap zatížení související úvahy byly vzaty v úvahu definovat tyto minimální verze. Neexistuje žádné úsilí o testování a kvalifikaci starších verzí DBMS. 

> [!NOTE]
> Minimální uvedené verze představují starší verzi operačních systémů a vydání databáze. Důrazně doporučujeme používat nejnovější verze operačního systému a vydání databáze. V mnoha případech vzaly novější verze operačního systému a databází v úvahu případ využití spuštěného ve veřejném cloudu a připravil kód pro optimalizaci pro provoz ve veřejném cloudu nebo konkrétněji v Azure.

## <a name="oracle-dbms-support"></a>Podpora oracle DBMS
Operační systém, verze Oracle DBMS a funkce Oracle podporované v Azure jsou konkrétně uvedeny v [poznámce podpory SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Podstata z této poznámky lze shrnout takto:

- Minimální verze Oracle podporovaná na virtuálních počítačích Azure, které jsou certifikované pro NetWeaver, je Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- Jako hostované operační systémy se kvalifikují pouze Windows a Oracle Linux. Přesné verze operačního systému a související minimální verze DBMS jsou uvedeny v poznámce
- Podpora oracle linuxu se vztahuje i na klienta Oracle DBMS. To znamená, že všechny součásti SAP, jako jsou instance dialogů abp nebo Java Stack, musí být spuštěny také na Oracle Linuxu. Pouze komponenty SAP v rámci takového systému SAP, které by se nepřipojily k systému Oracle DBMS, by mohly provozovat jiný operační systém Linux
- Oracle RAC není podporován 
- Oracle ASM je podporován pro některé případy. Podrobnosti jsou uvedeny v poznámce
- Systémy SAP, které nejsou typu Unicode, jsou podporovány pouze u aplikačních serverů se systémem Windows guest OS. Hostem operačního systému DBMS může být Oracle Linux nebo Windows. Důvod tohoto omezení je zřejmý při kontrole matice dostupnosti produktu SAP (PAM). Pro Oracle Linux SAP nikdy nevydal a nevydání jader SAP, která nejsou unicode

Znalost verzí DBMS, které jsou podporované s cílovou infrastrukturou Azure, je třeba zkontrolovat matici dostupnosti produktu SAP, zda jsou verze operačního systému a dbms podporovány s verzemi produktu SAP, které jste chtěli spustit. 


## <a name="sap-hana-support"></a>Podpora pro SAP HANA
V Azure existují dvě služby, které lze použít ke spuštění databáze HANA:

- Azure Virtual Machines
- [Velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

Pro spuštění SAP HANA, SAP má více a silnější podmínky infrastruktury musí splňovat než pro spuštění NetWeaver nebo jiné aplikace SAP a DBMS. V důsledku toho menší počet virtuálních počítačích Azure nárok pro spuštění SAP HANA DBMS. Seznam podporované infrastruktury Azure podporované pro SAP HANA najdete v takzvaném [hardwarovém adresáři SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> Jednotky začínající písmenem "S" jsou jednotky [velkých instancí HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 

> [!NOTE]
> SAP nemá žádnou specifickou certifikaci v závislosti na hlavních verzích SAP HANA. Na rozdíl od společného názoru, sloupec **Certifikační scénář** v [HANA certifikované Platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), sloupec **neobsahuje žádné prohlášení o HANA hlavní nebo menší verze certifikované**. Je třeba předpokládat, že všechny uvedené jednotky, které lze použít pro HANA 1.0 a HANA 2.0 tak dlouho, dokud certifikované operační systém verze pro konkrétní jednotky jsou podporovány HANA 1.0 verze také. 

Pro použití SAP HANA mohou platit různé minimální verze operačního systému než pro obecné případy NetWeaver. Je třeba rezervovat podporované operační systémy pro každou jednotku jednotlivě, protože ty se mohou lišit. Činíte tak kliknutím na každou jednotku. Zobrazí se další podrobnosti. Jedním z uvedených údajů jsou různé operační systémy podporované pro tuto konkrétní jednotku.

> [!NOTE]
> Jednotky azure hana velké instance jsou více omezující s podporovanými operačními systémy ve srovnání s virtuálními počítači Azure. Na druhou stranu virtuální počítače Azure může vynutit novější provozní verze jako minimální verze. To platí zejména pro některé z větších jednotek virtuálních počítače, které vyžadovaly změny linuxových jader

Znát podporovaný operační systém pro infrastrukturu Azure, je třeba zkontrolovat [poznámku podpory SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) pro přesné sap hana vydání a úrovně oprav, které jsou podporované s jednotkami Azure, na které cílíte. 

> [!IMPORTANT]
> Krok kontroly přesných podporovaných verzí SAP HANA a úrovní oprav je velmi důležitý. V mnoha případech je podpora určité verze operačního systému závislá na konkrétní úrovni opravy spustitelných souborů SAP HANA.

Jak víte, konkrétní hana verze můžete spustit na cílené infrastruktury Azure, je třeba zkontrolovat v matici dostupnosti produktu SAP zjistit, zda existují omezení s verzemi produktu SAP, které podporují HANA verze, které jste odfiltrovali


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Certifikované virtuální počítače Azure a jednotky velkých instancí HANA a propustnost obchodních transakcí
Kromě vyhodnocení podporovaných verzí operačního systému, vydání DBMS a závislých verzí softwaru SAP pro jednotky infrastruktury Azure musíte tyto jednotky kvalifikovat podle propustností obchodních transakcí, což je vyjádřeno v jednotce "SAP" od SAP. Všechny velikosti SAP závisí na výpočtech SAPS. Při vyhodnocování stávajících systémů SAP můžete obvykle s pomocí poskytovatele infrastruktury vypočítat SAPS jednotek. Pro vrstvu DBMS i pro aplikační vrstvu. V ostatních případech, kdy je vytvořena nová funkce, může cvičení velikosti se systémem SAP odhalit požadovaná čísla SAPS pro aplikační vrstvu a vrstvu DBMS. Jako poskytovatel infrastruktury je společnost Microsoft povinna poskytnout propustnost SAP charakterizaci různých jednotek, které jsou certifikovány netweaver em a/nebo HANA.

Pro virtuální počítače Azure jsou tato čísla propustností SAPS popsána v [poznámce podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Pro jednotky velké instance Azure HANA jsou čísla propustnostsaps popsána v [poznámce podpory SAP #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Podíváme-li se do [poznámky podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533), platí následující poznámky:

- **Pro virtuální počítače Azure řady M řady A Mv2 series se platí různé minimální verze operačního systému než pro jiné typy virtuálních počítačů Azure**. Požadavek na novější verze operačního systému je založen na změnách, které museli různí dodavatelé operačních systémů poskytnout ve verzích operačního systému, aby buď povolili své operační systémy spuštěné na konkrétních typech virtuálních počítačů Azure, nebo optimalizovali výkon a propustnost úloh SAP na těchto typech virtuálních počítačů.
- Existují dvě tabulky, které určují různé typy virtuálních počítačů. Druhá tabulka určuje propustnost SAPS pro typy virtuálních počítačů Azure, které podporují jenom standardní úložiště Azure. Nasazení systému DBMS na jednotkách určených v druhé tabulce poznámky není podporováno.


## <a name="other-sap-products-supported-on-azure"></a>Další produkty SAP podporované v Azure
Obecně se předpokládá, že se stavem hyperškálování cloudů, jako je Azure, by většina softwaru SAP měla běžet bez funkčních problémů v Azure. Nicméně a naproti vizualizaci privátního cloudu SAP stále vyjadřuje podporu pro různé produkty SAP výslovně pro různé poskytovatele cloudu hyerpscale. V důsledku toho existují různé poznámky podpory SAP označující podporu pro Azure pro různé produkty SAP. 

Pro platformu Business Objects BI [#2145537 poznámka o podpoře SAP](https://launchpad.support.sap.com/#/notes/2145537) obsahuje seznam produktů SAP Business Objects podporovaných v Azure. Pokud existují otázky týkající se součástí nebo kombinací verzí softwaru a verzí operačního systému, které se zdají být uvedeny nebo podporovány a které jsou novější než minimální uvedené verze, musíte otevřít žádost o podporu SAP proti součásti, o kterou se zeptáte na podporu.

Pro datové služby podnikových objektů [poznámka o podpoře SAP #22288344](https://launchpad.support.sap.com/#/notes/2288344) vysvětluje minimální podporu datových služeb SAP spuštěných v Azure. 

> [!NOTE]
> Jak je uvedeno v poznámce podpory SAP, musíte zkontrolovat SAP PAM k identifikaci správné úrovně balíčku podpory, která má být podporována v Azure

Podpora SAP Datahub/Vora ve službách Azure Kubernetes Services (AKS) je podrobně popsána v [poznámce podpory SAP #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Podpora sap BPC 10.1 SP08 je popsána v [poznámce podpory SAP #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Podpora platformy SAP Hybris Commerce v Azure je podrobně popsána v [dokumentaci k Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html). Jako podporované DBMS pro SAP Hybris Commerce Platform, uvádí jako:

- SQL Server a Oracle na platformě operačního systému Windows. Platí stejné minimální verze jako pro SAP NetWeaver. Podrobnosti [najdete v poznámce podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- SAP HANA na Red Hat a SUSE Linux. Sap HANA certifikované typy virtuálních počítačů jsou povinné jako dokumentované dříve v [tomto dokumentu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure#sap-hana-support). Obchodní platforma SAP (Hybris) je považována za pracovní zátěž OLTP
- SQL Azure DB od SAP (Hybris) Commerce Platform verze 1811




## <a name="next-steps"></a>Další kroky
Přečtěte si další kroky v [plánování a implementaci virtuálních počítačů Azure pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)

