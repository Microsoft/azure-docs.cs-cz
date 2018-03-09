---
title: "SAP HANA dostupnosti v rámci jedné oblasti Azure | Microsoft Docs"
description: "Operace na virtuálních počítačích Azure nativní SAP HANA"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/5/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28fe9bc7c8cb1d59df404b7dd7429def54648a18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA dostupnosti v rámci jedné oblasti Azure
V této části uvádíme několik scénářů, které se popisují scénáře dostupnosti v rámci jedné oblasti Azure. Azure má mnoho oblastí, které se šíří po celém světě. Seznam oblastí Azure, naleznete [oblasti Azure](https://azure.microsoft.com/regions/) článku. Nasazení SAP HANA na virtuálních počítačích v rámci jedné oblasti Azure, společnost Microsoft nabízí nasazení jeden virtuální počítač s instancí HANA. Nebo k zajištění vyšší dostupnosti můžete nasadit dva virtuální počítače se dvěma instancemi HANA v rámci [sadu dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) HANA systému replikace, se používá pro účely dostupnosti. Azure má public preview systému [Azure dostupnost zóny](https://docs.microsoft.com/azure/availability-zones/az-overview). Tyto zóny dostupnosti nebudete mít podrobněji ještě. S výjimkou některé obecné nápady kolem použití skupiny dostupnosti a dostupnost zóny.

Jaký je rozdíl mezi skupinami dostupnosti služby Azure a dostupnost zóny? Oblasti pro oblasti Azure, kde se chystáte dostupnost zóny nabídnuta, mají několik datových center, které jsou nezávislé na v napájení zdroj energie, chlazení a sítě. Důvod pro nabídky různým zónám v rámci jedné oblasti Azure je umožnit nasazení aplikací v rámci kabelu nebo nabízí tři dostupnost zóny. Za předpokladu, že problémy v zdroje napájení nebo sítě by mohly ovlivnit jenom jeden dostupnost zóny infrastruktury, je stále plně funkční nasazení vaší aplikace v rámci oblasti Azure. Nakonec s omezenou kapacitou, které se od některých virtuálních počítačů v jedné zóně mohou být ztraceny. Ale virtuální počítače v jiných dvě zóny jsou stále spuštěny. 
 
Zatímco skupiny dostupnosti Azure je logické seskupení funkce, můžete v Azure, abyste ověřili, že prostředky virtuálních počítačů, které umístíte v rámci jsou selhání izolované od sebe navzájem, když jsou nasazeny v rámci datového centra Azure. Azure zajišťuje, že virtuální počítače, které umístíte do skupiny dostupnosti, se budou spouštět napříč několika fyzickými servery, výpočetními racky, jednotkami úložiště a síťovými přepínači. Nebo jako některé další dokumentace k Azure, uvádí se jako umísťováním v různých [aktualizace a domén selhání](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Tyto umísťováním jsou obvykle v rámci datového centra Azure. Za předpokladu, že problémy v zdroje napájení nebo sítě by mohly ovlivnit datovém centru, které jste nasadili, by mít vliv na vaše kapacita v jedné oblasti Azure.

Umístění datových center, která představovat Azure dostupnost zóny je to kompromis mezi doručování latence sítě mezi službami nasadit v různých zón, které jsou přijatelné pro většinu aplikací a určité vzdálenosti mezi v datacentrech. Tak, aby přirozené catastrophes v ideálním případě nebude mít vliv na napájení a zadejte síť a infrastrukturu pro všechny zóny dostupnosti v této oblasti. Však jako monumental přirozené catastrophes vám ukázal, dostupnost zóny nemusí být vždy možné k zajištění dostupnosti v rámci jedné oblasti podle potřeby. Vezměte v úvahu hurikán Marie, stiskněte tlačítko Portoriko island na 20/08/2017 a v podstatě způsobila blížit 100 % černé out na široké island 90 miles.   
  


## <a name="single-vm-scenario"></a>Jeden scénář virtuálních počítačů
V tomto scénáři jste vytvořili Azure virtuálního počítače pro instanci SAP HANA. Azure Premium Storage můžete použít pro hostování disku operačního systému a všech datových disků. Doby provozu SLA 99,9 % Azure a SLA jiných součástí Azure je dostačující pro vás ke splnění dostupnost SLA směrem k vašim zákazníkům. V tomto scénáři máte není nutné využívat skupiny dostupnosti Azure pro virtuální počítače se systémem vrstvě databázového systému. V tomto scénáři byste tedy spoléhat na dvou různých funkcí:

- Azure virtuální počítač automaticky restartuje (také odkazovat jako opravy služby Azure)
- SAP HANA automatické restartování

Azure virtuálního počítače automaticky restartovat nebo službou opravy je funkce v Azure, který funguje na dvě úrovně:

- Kontrola stavu virtuálního počítače hostované na hostitele serveru hostitele serveru Azure
- Sledování stavu a dostupnosti hostitelského serveru Azure Kontroleru prostředků infrastruktury

Pro každý virtuální počítač hostovaný na serveru Azure hostiteli je funkce kontroly stavu monitoruje stav hostované virtuální počítače. V případě, že virtuální počítače spadají do stavu není v pořádku, restartování virtuálního počítače lze inicializovat pomocí agenta Azure hostitele, který kontroluje stav virtuálního počítače. Probíhá kontrola stavu hostitele kontrolou mnoho různých parametrů svědčící o problémech s na hostitelský hardware Kontroleru prostředků infrastruktury Azure, ale také zkontroluje na usnadnění hostitele přes síť. To znamenat problémy s hostitelem může vést k akcí, například:

- Počítač hostitele a restartujte virtuálních počítačů, které byly spuštěny v hostiteli, pokud hostitel signály chybný stav
- Restartování hostitele a restartovat virtuální počítače, které byly původně hostovány na hostiteli v pořádku hostiteli v případě, že hostitel není v pořádku po restartování. V takovém případě je hostitel má být označeny jako není v pořádku a nepoužívá se pro další nasazení, dokud vymazán nebo nahrazena.
- Okamžitou restartování virtuálních počítačů na hostiteli v pořádku v případech, kdy není v pořádku hostitele obsahuje problémy v procesu restartování. 

S hostiteli a monitorování virtuálních počítačů poskytuje Azure jsou virtuální počítače Azure, které sníží na problémy s hostitelem automaticky restartuje v pořádku hostitele Azure 

Druhý funkce, kterou byste tedy spoléhat na v takové situaci je skutečnost, že automaticky spouští služby HANA, který běží v restartovat počítač po restartování virtuálního počítače. [HANA služby Automatické restartování](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) možné nakonfigurovat pomocí služby sledovací zařízení různých služeb HANA.

Tento scénář jeden virtuální počítač může získat lepší přidáním uzlu studenou převzetí služeb při selhání do konfigurace aplikace SAP HANA. Nebo pokud to se označuje jako v dokumentaci k SAP HANA jako [hostitele Auto-převzetí](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Tato konfigurace může mít smysl v místním nasazením situacích, kdy je omezený hardware serveru a vyhradit uzlu jeden server jako hostitele Auto-převzetí uzel pro sadu produkční hostitele. Ale pro situace, jako je například Azure, kde základní infrastruktura Azure poskytuje pořádku cílový server pro úspěšné restartování virtuálního počítače, scénář SAP HANA hostitele Auto-převzetí služeb při selhání nemá smysl pro nasazení. 

V důsledku toho nemáme žádné referenční architektura, která předpokládá pohotovostní uzel pro HANA hostitele Auto-převzetí služeb při selhání. To platí také pro SAP HANA konfigurace Škálováním na více systémů.


## <a name="availability-scenarios-involving-two-different-vms"></a>Scénáře dostupnosti obsahující dvě různé virtuální počítače
Pomocí dva virtuální počítače Azure v rámci skupiny dostupnosti Azure umožňují zvýšit doby provozu mezi tyto dva virtuální počítače těchto virtuálních počítačů umístěných v skupiny dostupnosti Azure, v rámci jedné oblasti Azure. Základní instalace v Azure bude vypadat grafiky znázorněno zde: ![dva virtuální počítače s všechny vrstvy](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Chcete-li znázornění dostupnosti různé scénáře, několik vrstev výše jsou vyjmutí a grafiky omezené na vrstvy virtuálních počítačů, hostitelů, skupiny dostupnosti a oblasti Azure. Azure virtuálních sítí, skupiny prostředků a předplatná nebudou dostupné role pro popsané scénáře.

### <a name="replicating-backups-to-second-virtual-machine"></a>Replikace zálohy do druhého virtuálního počítače
Jedním z nejvíce elementární nastavení je mít zálohy, zejména zálohování transakčního protokolu odeslaná z jednoho virtuálního počítače do jiného Azure virtuální počítače. Máte možnost volby libovolného typu úložiště Azure. Vám bude zodpovídat za skriptování kopii naplánovaných záloh prováděné na první virtuální počítač do druhého virtuálního počítače. V případě použití vyžadování instance druhý virtuální počítač, by potřeba obnovení úplné, přírůstkové nebo rozdílového a zálohování transakčního protokolu do bodu, které potřebujete. Bude vypadat architektura: ![dva virtuální počítače s replikací úložiště](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Tato instalace není příliš vhodný k dosažení skvělé plánovaný bod obnovení a RTO časy. Zejména RTO časy by dochází z důvodu potřeba dokončení databáze s zkopírovaný zálohy úplného obnovení. Tato instalace se ale použitelná pro obnovení z odstranění nezamýšleným dat v rámci hlavní instancí. s tímto nastavením, které je možné kdykoli obnovit do určité míry v čase extrahovat data a importovat odstraněná data do vaší hlavní instance. Proto se může mít smysl použít takové metoda záložní kopii v kombinaci s další funkce vysoké dostupnosti. Po dobu, pokud jsou kopírovány pouze zálohování, může získat spolu s virtuální počítač menší než hlavní SAP HANA instance běží v. Ale mějte na paměti, které menší virtuální počítače mají nižší počet virtuálních pevných disků, které je možné připojit. Zkontrolujte [velikostí pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) pro omezení jednotlivých typů virtuálních počítačů.

### <a name="using-sap-hana-system-replication-without-automatic-failover"></a>Použití replikace systému SAP HANA bez automatické převzetí služeb při selhání
Pro následující scénáře používáte replikaci systému SAP HANA. SAP vydané dokumentaci naleznete počínaje článek [replikaci systému](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Mezi dvěma virtuálními počítači Azure v jedné oblasti Azure existují dva různé konfigurace, které mají určité rozdíly v plánovanou dobu obnovení. Obecně platí scénáře s nemá automatické převzetí služeb při selhání, nemusí být příliš relevantní pro scénáře v rámci jedné oblasti Azure. Důvod k tomu je, že ve většině případů selhání v infrastruktuře Azure, Azure Service opravy bude restartovat primárního virtuálního počítače na jiného hostitele. Jsou pouze hraniční případy, kdy taková konfigurace mohou pomoci z hlediska selhání scénáře. Nebo někdy jako zákazník chcete mějte na paměti, zejména kolem efektivitu.

#### <a name="using-hana-system-replication-without-auto-failover-and-without-data-pre-load"></a>Použití replikace systému HANA bez automatické převzetí služeb při selhání a bez předběžně načíst data 
Toto je scénář, kde používáte replikaci systému SAP HANA za účelem přesouvání dat synchronním způsobem k dosažení obnovení bodu cíl (RPO) 0. Na druhé straně, máte dostatečně dlouhé obnovení čas cíl (RTO), tak, že nepotřebujete převzetí služeb při selhání nebo předběžně načíst data do mezipaměti instance HANA. V takovém případě máte možnosti pro další jednotky ekonomické ukazatele do vaší konfigurace podle:

- Jiná instance SAP HANA můžete spustit v druhý virtuální počítač, který přebírá většinu paměti virtuálního počítače. Obvykle bude tato instance instanci, která v případě převzetí služeb při selhání do druhého virtuálního počítače může být vypnuté. Ano, který je možné načíst replikovaných dat do mezipaměti cílové instance HANA do druhého virtuálního počítače.
- Menší velikost virtuálního počítače můžete použít jako druhý virtuální počítač. V případě selhání by měla mít krok před ruční převzetí služeb při selhání kde změníte velikost virtuálního počítače na velikost zdrojového virtuálního počítače. Tento scénář vypadá takto:

![Dva virtuální počítače s replikací úložiště](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> I bez předběžně načíst data v cílové replikaci HANA systému, budete potřebovat minimálně 64 GB paměti a nad rámec tohoto dostatek paměti pro rowstore data mějte na paměti cílové instance.

#### <a name="using-hana-system-replication-without-auto-failover-and-with-data-pre-load"></a>Pomocí replikace systému HANA bez použití automatické převzetí služeb při selhání a předběžné načtení dat
Rozdíl ve scénáři zavedená před je, že je předem načtená data, která budou replikována do Instance HANA do druhého virtuálního počítače. K omezení dvě výhody, které máte se scénářem není předem načíst data. V takovém případě nelze spustit jiného systému SAP HANA do druhého virtuálního počítače. Ani může použít menší velikost virtuálního počítače. Proto jedná o scénář špatně implementováno s zákazníků


### <a name="using-sap-hana-system-replication-with-automatic-failover"></a>Pomocí automatické převzetí služeb při selhání replikace systému SAP HANA

Konfigurace standardní dostupnosti v rámci jedné oblasti Azure, většina zákazníků jsou implementace s SAP HANA, je konfigurace, které mají dva virtuální počítače Azure s Linuxem SLES clusteru s podporou převzetí služeb při selhání definované. Je na základě clusteru Linux SLES [kardiostimulátor](http://www.linux-ha.org/wiki/Pacemaker) framework ve spojení s [STONITH](http://linux-ha.org/wiki/STONITH) zařízení. Z hlediska SAP HANA použít režim replikace je synchronizován a automatické převzetí služeb při selhání je nakonfigurovaný. Druhý virtuální počítač instance SAP HANA funguje jako aktivní pohotovostní uzlu, který přijímá datový proud s synchronní změnu záznamů z primární instance SAP HANA. Jak získat transakcí potvrzených aplikací na primárním uzlu HANA, čeká primárního uzlu HANA, potvrdit potvrzení k aplikaci, dokud sekundární uzel SAP HANA potvrzen, po obdržení potvrzení záznamu. Režimy SAP HANA dva různé synchronní replikace. Podrobnosti a rozdíly v těchto dvou režimů synchronní replikace, najdete v článku [režimy replikace pro replikaci systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)

Vypadá na celkové konfiguraci

![Dva virtuální počítače s úložiště replikace a převzetí služeb při selhání](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Toto řešení je zvolen, protože umožňuje dosáhnout RPO = 0 a velmi nízkou RTO časy. Konfigurovat připojení klientů SAP HANA tak, aby klienti SAP HANA používat virtuální IP adresy pro připojení ke konfiguraci replikace systému HANA. Tím se eliminuje potřeba překonfigurovat aplikace v případě selhání sekundárního uzlu. V tomto řešení SKU virtuálních počítačů Azure pro primární nebo sekundární musí být stejné.  


## <a name="next-steps"></a>Další kroky
Pokud potřebujete podrobné pokyny o tom, jak nastavit konfiguraci v Azure, přečtěte si články:

- [Nastavení replikace systému SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Vaše SAP na Azure – část 4 – vysoká dostupnost pro použití replikace systému SAP HANA](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Pokud potřebujete další informace o dostupnosti SAP HANA přes Azure oblasti, přečtěte si:

- [SAP HANA dostupnosti v oblastech Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

