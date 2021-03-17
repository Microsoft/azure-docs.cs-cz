---
title: 'SAP v Azure: který software SAP je podporován v Azure'
description: Vysvětluje, který software SAP se podporuje pro nasazení v Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/12/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dfd560754d92c97b435ff7643f6da4d3e62e8cfd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673528"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Který software SAP se podporuje pro nasazení Azure
Tento článek popisuje, jak zjistit, který software SAP je podporován pro nasazení Azure a jaké jsou nezbytné verze operačního systému nebo verze DBMS.

Vyhodnotili jste aktuální software SAP a informace o tom, jaké verze operačních systémů a systémů DBMS jsou pro váš software SAP v Azure podporované, budete potřebovat přístup k těmto akcím:

- Poznámky k podpoře SAP
- Matice dostupnosti produktu SAP



## <a name="general-restrictions-for-sap-workload"></a>Obecná omezení pro úlohy SAP
Služby Azure IaaS, které se dají použít pro úlohy SAP, jsou omezené na hardware X86-64 nebo x64. Pro úlohy SAP nejsou k dispozici žádné SPARC ani nabídky založené na Power CPU. Zákazníci, kteří běží na svých aplikacích v operačních systémech, které jsou součástí hardwarových architektur, jako je například IBM sálový nebo AS400, nebo kde se používají operační systémy HP-UX, Solaris nebo AIX, potřebují změnit jejich aplikace SAP včetně systému DBMS na jeden z následujících operačních systémů:

- 64bitová verze Windows serveru pro platformu x86
- SUSE Linux 64 pro platformu X86-64
- Red Hat Linux 64 pro platformu X86-64
- Oracle Linux 64bitová pro platformu X86-64

V kombinaci se softwarem SAP nejsou podporovány žádné další verze operačního systému ani distribuce systému Linux. Přesné podrobnosti o konkrétních verzích a případech jsou zdokumentovány později v dokumentu.


## <a name="you-start-here"></a>Začnete tady
Výchozím bodem pro vás je [podpora SAP – poznámka #1928533](https://launchpad.support.sap.com/#/notes/1928533). Když projdete tuto poznámku SAP shora dolů, zobrazí se několik oblastí podporovaného softwaru a virtuálních počítačů.

V první části jsou uvedené minimální požadavky na operační verze, které jsou obecně podporované softwarem SAP ve virtuálních počítačích Azure. Pokud se vám nedaří tyto minimální požadavky a spustit starší verze těchto operačních systémů, je nutné upgradovat verzi operačního systému na takovou minimální verzi nebo dokonce i novější verze. Je správné, že Azure obecně podporuje starší verze některých z těchto operačních systémů. Ale tato omezení nebo minimální vydání jsou založená na testech a provedených kvalifikacích a neúčtují se později. 


> [!NOTE]
>Existují některé konkrétní typy virtuálních počítačů, velké instance HANA nebo úlohy SAP, které budou vyžadovat novější verze operačních systémů. Podobné případy se budou zmínit v celém dokumentu. Případy, jako které jsou jasně zdokumentovány v poznámkách SAP nebo jiných publikacích SAP.

V následující části jsou uvedené obecné platformy SAP podporované v podporovaných verzích a důležitější jsou podporované jádro SAP. Obsahuje seznam zásobníků NetWeaver/ABAP nebo Java, které jsou podporované a které vyžadují minimální verze jádra. V Azure se podporují novější zásobníky ABAP, ale nevyžadují minimální vydání jádra, protože změny pro Azure, které jsme získali od zahájení vývoje novějších zásobníků, se nevyžadují.

Je nutné ověřit:

- Zda jsou aplikace SAP, které používáte, zahrnuté do uvedených minimálních vydání. V takovém případě je nutné definovat novou cílovou verzi, vrátit se změnami v matici dostupnost produktu SAP, jaké kombinace operačních systémů a DBMS jsou podporovány novou cílovou verzí. Takže si můžete vybrat správnou verzi operačního systému a verzi DBMS.
- Bez ohledu na to, jestli je potřeba aktualizovat jádra SAP v rámci přesunu na Azure
- Bez ohledu na to, zda potřebujete aktualizovat balíčky podpory SAP. Obzvláště základní balíčky podpory, které mohou být požadovány pro případy, kdy je nutné přejít na novější verzi systému DBMS


V další části najdete další podrobnosti o dalších produktech SAP a verzích DBMS, které podporuje SAP v Azure pro Windows a Linux. 

> [!NOTE]
> Minimální verze různých systémů DBMS se pečlivě volí a nemusí vždy odrážet celé spektrum systémů DBMS uvolňuje různé podpory výrobců DBMS v Azure obecně. Bylo zohledněno mnoho aspektů souvisejících s úlohami SAP pro definování těchto minimálních vydání. Neexistují žádné úsilí k otestování a kvalifikaci starších verzí systému DBMS. 

> [!NOTE]
> Minimální vydané verze představují starší verze operačních systémů a verze databáze. Důrazně doporučujeme, abyste používali nejnovější verze operačních systémů a verze databází. V mnoha případech využila více nejnovějších verzí operačního systému a databází případ využití při spuštění ve veřejném cloudu a přizpůsobený kód pro optimalizaci pro spuštění ve veřejném cloudu nebo speciálně na platformě Azure.

## <a name="oracle-dbms-support"></a>Podpora Oracle DBMS
Operační systém, verze Oracle DBMS a funkce Oracle podporované v Azure jsou výslovně uvedené v [poznámkách k podpoře SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Podstata z této poznámky může být shrnuta jako:

- Minimální verze Oracle podporovaná na virtuálních počítačích Azure, které jsou certifikované pro NetWeaver, je Oracle 11g verze 2 patchset 3 (11.2.0.4).
- Jako hostované operační systémy jenom Windows a Oracle Linux kvalifikovat. Přesná vydání a související minimální verze systému DBMS jsou uvedené v poznámce.
- Podpora Oracle Linux se rozšiřuje i na klienta Oracle DBMS. To znamená, že všechny součásti SAP, například instance dialogů ABAP nebo Java, musí běžet i na Oracle Linux. Pouze součásti SAP v rámci takového systému SAP, který by se nemohl připojit ke službě Oracle DBMS, by mohly spustit jiný operační systém Linux.
- Oracle RAC se nepodporuje. 
- V některých případech je podporován Oracle ASM. Podrobnosti jsou uvedené v poznámce.
- Systémy SAP jiné než Unicode jsou podporovány pouze s aplikačními servery, které používají hostovaný operační systém Windows. Hostovaný operační systém DBMS může být Oracle Linux nebo Windows. Důvod pro toto omezení je zjevné při kontrole matice dostupnosti produktů SAP (PAM). Pro Oracle Linux SAP nikdy neuvolnilo jádra SAP bez kódování Unicode.

Znalost verzí DBMS, které jsou podporované s cílovou infrastrukturou Azure, je potřeba, abyste zkontrolovali, jestli jsou vydané verze operačních systémů a systémy DBMS podporované u vydání produktu SAP, které jste chtěli spustit. 

## <a name="oracle-linux"></a>Oracle Linux
Nejvýraznějším dotazům Oracle Linux je, zda SAP podporuje jádro Red Hat, které je nedílnou součástí Oracle Linux. Podrobnosti najdete v [poznámkách k podpoře SAP #1565179](https://launchpad.support.sap.com/#/notes/1565179).

## <a name="other-database-than-sap-hana"></a>Jiná databáze než SAP HANA
Podpora databází nehana pro úlohy SAP je popsána v [poznámkách k podpoře sap #1928533](https://launchpad.support.sap.com/#/notes/1928533).


## <a name="sap-hana-support"></a>Podpora SAP HANA
V Azure jsou k dispozici dvě služby, které se dají použít ke spuštění databáze HANA:

- Azure Virtual Machines
- [Velké instance HANA](./hana-overview-architecture.md)

Pro provozování SAP HANA má SAP více a silnější podmínky infrastruktury, než aby běžely NetWeaver nebo jiné aplikace SAP a DBMS. Výsledkem může být menší počet virtuálních počítačů Azure, které by mohly spouštět SAP HANA DBMS. Seznam podporované infrastruktury Azure podporované pro SAP HANA najdete v části [SAP HANA hardwarový adresář](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> Jednotky, které začínají písmenem, jsou jednotky [velkých instancí Hana](./hana-overview-architecture.md) . 

> [!NOTE]
> Pro SAP není závislá žádná konkrétní certifikace na SAP HANA hlavních verzích. V rozporu se společným stanoviskem je **scénář certifikace** sloupce na [platformách certifikovaných pro IaaS s certifikací Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) **žádný příkaz o certifikaci hlavní verze Hana nebo podverze s certifikací**. Musíte předpokládat, že všechny uvedené jednotky, které je možné použít pro HANA 1,0 a HANA 2,0, pokud jsou podporované vydané verze operačního systému pro konkrétní jednotky, podporuje také verze HANA 1,0. 

Pro použití SAP HANA se můžou použít jiné minimální verze operačního systému než u obecných případů NetWeaver. Je nutné, aby se u každé jednotky jednotlivě kontrolovaly podporované operační systémy, protože se mohou lišit. Provedete to tak, že kliknete na jednotlivé jednotky. Zobrazí se další podrobnosti. Jedna z uvedených podrobností je u této konkrétní jednotky podporovaná pro různé operační systémy.

> [!NOTE]
> Jednotky velkých instancí Azure HANA jsou více omezující s podporovanými operačními systémy v porovnání s virtuálními počítači Azure. Na druhou stranu virtuální počítače Azure můžou vymáhat novější verze těchto vydání jako minimální vydání. To platí hlavně pro některé větší jednotky virtuálních počítačů, které vyžadovaly změny v jádrech systému Linux.

Znalost podporovaného operačního systému pro infrastrukturu Azure vyžaduje, abyste zkontrolovali [poznámce SAP support #2235581](https://launchpad.support.sap.com/#/notes/2235581) pro přesné SAP HANA verze a úrovně oprav, které jsou podporované s jednotkami Azure, na které cílíte. 

> [!IMPORTANT]
> Krok kontroly výše podporovaných verzí SAP HANA a úrovní oprav je velmi důležitý. V mnoha případech je podpora určité verze operačního systému závislá na určité úrovni oprav SAP HANA spustitelných souborů.

Jak znáte konkrétní verze HANA, které můžete spustit na cílené infrastruktuře Azure, je potřeba vrátit se změnami v matici dostupnost produktu SAP, kde zjistíte, jestli existují omezení u vydání produktů SAP, která podporují vydané verze HANA, kterou jste odhlásili.


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Certifikované virtuální počítače Azure a jednotky velkých instancí HANA a propustnost obchodních transakcí
Kromě vyhodnocování podporovaných verzí operačního systému, verzí systému DBMS a závislých aktualizací softwaru SAP pro jednotky infrastruktury Azure je potřeba tyto jednotky kvalifikovat propustností obchodních transakcí, který je vyjádřený v jednotce SAP pomocí SAP. Všechna určení velikosti SAP závisí na výpočtech SAP. Vyhodnocování stávajících systémů SAP vám obvykle může s využitím poskytovatele infrastruktury vypočítat SAP jednotek. Pro vrstvu DBMS i pro aplikační vrstvu. V jiných případech, kdy se vytvoří nové funkce, může při práci s SAP dokrýt požadovaná čísla SAP pro vrstvu aplikace a vrstvu DBMS. Jako poskytovatel infrastruktury Microsoft je povinen poskytnout charakteristiku propustnosti SAP pro různé jednotky, které jsou buď NetWeaver, nebo HANA certifikováno.

Pro virtuální počítače Azure jsou tato čísla propustnosti SAP dokumentována v [poznámkách k podpoře SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Pro jednotky velkých instancí Azure HANA se čísla propustnosti SAP zdokumentují v [poznámkách k podpoře SAP #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Při hledání [poznámky k podpoře SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)platí následující poznámky:

- **Pro virtuální počítače Azure řady M-Series a Mv2-Series virtuálních počítačů Azure se pro jiné typy virtuálních počítačů Azure použijí jiné minimální verze operačního systému**. Požadavek na další poslední vydané verze operačního systému je založený na změnách, které výrobci v operačním systému museli poskytnout, aby povolili jejich operační systémy běžící na specifických typech virtuálních počítačů Azure nebo optimalizoval výkon a propustnost úloh SAP na těchto typech virtuálních počítačů.
- K dispozici jsou dvě tabulky, které určují různé typy virtuálních počítačů. Druhá tabulka určuje propustnost SAP pro typy virtuálních počítačů Azure, které podporují jenom Azure Storage úrovně Standard. Nasazení DBMS na jednotkách uvedených v druhé tabulce poznámky se nepodporuje.


## <a name="other-sap-products-supported-on-azure"></a>Další produkty SAP podporované v Azure
Předpokladem je, že ve stavu cloudů s škálovatelným škálováním, jako je Azure, by měl většina softwaru SAP běžet bez funkčních problémů v Azure. I opak k vizualizaci privátního cloudu ale SAP stále vyjadřuje podporu pro různé produkty SAP explicitně pro různé poskytovatele cloudových hyerpscale. V důsledku toho existují různé poznámky k podpoře SAP, které označují podporu pro Azure pro různé produkty SAP. 

U obchodních objektů – platforma BI – [Poznámka k podpoře sap #2145537](https://launchpad.support.sap.com/#/notes/2145537) poskytuje seznam produktů SAP Business Objects podporovaných v Azure. Pokud se vyskytnou otázky týkající se součástí nebo kombinací verzí softwaru a vydání operačních systémů, které se zdají být neuvedené nebo podporované a které jsou novější než uvedené minimální verze, je potřeba otevřít žádost o podporu SAP na součást, pro kterou se dotazuje podpora.

Pro obchodní objekty Data Services, [poznámková podpora sap #22288344](https://launchpad.support.sap.com/#/notes/2288344) vysvětluje minimální podporu SAP Data Services běžící v Azure. 

> [!NOTE]
> Jak je uvedeno v poznámce k podpoře SAP, je nutné vrátit se změnami do služby SAP PAM, aby bylo možné zjistit, zda je správná úroveň balíčku podpory podporována v Azure.

Podpora SAP DataHub/Vora ve službě Azure Kubernetes Services (AKS) je popsána v [poznámkách k podpoře sap #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Podpora SAP bitů 10,1 SP08 je popsaná v tématu [podpora SAP – poznámka #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Podpora platformy SAP Hybris pro Commerce v Azure je podrobně popsána v [dokumentaci k Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html). Od podporovaného systému DBMS pro platformu SAP Hybris pro Commerce se uvádí jako:

- SQL Server a Oracle na platformě operačního systému Windows. Stejné minimální vydané verze platí jako pro SAP NetWeaver. Podrobnosti najdete v [poznámkách k podpoře SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533) .
- SAP HANA na Red Hat a SUSE Linux. SAP HANA certifikované typy virtuálních počítačů jsou popsány dříve v [tomto dokumentu](#sap-hana-support). Platforma SAP (Hybris) Commerce je považována za úlohu OLTP.
- SQL Azure DB s platformou SAP (Hybris) pro Commerce verze 1811




## <a name="next-steps"></a>Další kroky
Přečtěte si další kroky v tématu [plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver](./planning-guide.md)
