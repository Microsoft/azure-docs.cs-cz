---
title: SAP HANA dostupnosti v rámci jedné oblasti Azure | Microsoft Docs
description: Popisuje SAP HANA operací na virtuálních počítačích Azure nativní v jedné oblasti Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47dba73a6c22d11953485a69435000d3d2fe6f55
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA dostupnosti v rámci jedné oblasti Azure
Tento článek popisuje několik scénářů dostupnosti v rámci jedné oblasti Azure. Azure má v mnoha oblastech šíří po celém světě. Seznam oblastí Azure najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/). Pro nasazení SAP HANA na virtuálních počítačích v rámci jedné oblasti Azure, společnost Microsoft nabízí nasazení jeden virtuální počítač s instancí HANA. K zajištění vyšší dostupnosti, můžete nasadit dva virtuální počítače se dvěma instancemi HANA v rámci [sady dostupnosti. Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) používající HANA systému replikace pro dostupnost. 

V současné době Azure nabízí public preview systému [Azure dostupnost zóny (preview)](https://docs.microsoft.com/azure/availability-zones/az-overview). V tomto článku nepopisujeme dostupnost zóny podrobně. Ale jsme obsahují Obecná diskuse o pomocí nastavení dostupnosti a dostupnost zóny.

Jaký je rozdíl mezi skupinu dostupnosti a dostupnost zóny v Azure? Azure oblasti, kde jsou nabízena dostupnost zóny mají několik datových center. Zadejte zdroj energie, chlazení a sítě nezávisí v datacentrech. Z důvodu nabídnout různé zóny v rámci jedné oblasti Azure je, takže můžete nasazovat aplikace mezi dva nebo tři dostupnosti zón, které nabízí. Za předpokladu, že zdroj nebo síťové problémy power by mohly ovlivnit jenom jeden infrastruktury zóny dostupnosti, je stále plně funkční, pokud používáte zóny dostupnosti nasazení vaší aplikace v rámci oblasti Azure. Může dojít k některé menší kapacitu. Například virtuální počítače v jedné zóně může dojít ke ztrátě, ale virtuální počítače v jiných dvě zóny bude stále spuštěná. 
 
Nastavení Azure dostupnosti je logické seskupení funkci, která pomáhá zajistěte, aby byly prostředky virtuálních počítačů, které umístíte v rámci sady dostupnosti selhání izolované od sebe navzájem při jejich nasazení v rámci datového centra Azure. Azure zajistí, že virtuální počítače umístíte v rámci dostupnosti nastavit spuštění napříč více fyzických serverů, výpočetní stojany, jednotky úložiště a síťové přepínače. V některé dokumentaci Azure, tato konfigurace se označuje jako umísťováním v různých [aktualizace a selhání domén](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Tyto umísťováním jsou obvykle v rámci datového centra Azure. Za předpokladu, že problémy power zdroje a sítě by mohly ovlivnit datacenter, který nasazujete, by mít vliv na vaše kapacita v jedné oblasti Azure.

Umístění datových center, která představovat Azure dostupnost zóny je to kompromis mezi doručování latence sítě mezi službami nasadit v různých zón, které je přijatelné pro většinu aplikací a určité vzdálenosti mezi datovými centry. Přirozené catastrophes by v ideálním případě vliv na výkon, zadejte síť a infrastrukturu pro všechny zóny dostupnosti v této oblasti. Ale mít znázorněné monumental přirozené catastrophes dostupnost zóny nemusí vždy poskytovat dostupnosti, které chcete v rámci jedné oblasti. Vezměte v úvahu Marie Hurikán, který dosáhl Portoriko island na 20. září 2017. Hurikán v podstatě způsobila téměř 100 procent přerušení spojení na 90. míle celou island.

## <a name="single-vm-scenario"></a>Scénář Single-VM

Ve scénáři s jednou VM vytvoříte virtuální počítač Azure pro instance SAP HANA. Azure Premium Storage můžete použít k hostování disku operačního systému a všechny datové disky. Azure provozu SLA 99,9 % a SLA jiných součástí Azure je dostačující pro vás splnit vaše dostupnost SLA pro vaše zákazníky. V tomto scénáři máte není nutné využívat Azure sadu dostupnosti pro virtuální počítače, které spustit vrstvě databázového systému. V tomto scénáři byste tedy spoléhat na dvou různých funkcí:

- Azure virtuálních počítačů automatické restartování (také označované jako opravy služby Azure)
- Automatické restartování SAP HANA

Azure automatické restartování virtuálního počítače nebo služby opravy, je funkce v Azure, který funguje na dvě úrovně:

- Hostitel serveru Azure kontroluje stav virtuálního počítače, který je hostován na hostitele serveru.
- Kontroler prostředků infrastruktury Azure sleduje stav a dostupnost hostitelského serveru.

Je funkce kontroly stavu monitoruje stav každý virtuální počítač, který je hostován na hostiteli serveru Azure. Virtuální počítač patří do nonhealthy stavu, restartování virtuálního počítače lze inicializovat pomocí agenta Azure hostitele, který kontroluje stav virtuálního počítače. Kontroleru prostředků infrastruktury kontroluje stav hostitele kontrolou mnoho jiné parametry, které může znamenat problémy s hardwarem hostitele. Také zkontroluje na usnadnění hostitele přes síť. To znamenat problémy s hostitelem může vést k následujícím událostem:

- Pokud hostitel signály špatném stavu v pořádku, restartování hostitele a restartování virtuálních počítačů, které byly spuštěny v hostiteli.
- Pokud hostitel není v pořádku po restartování, restartování hostitele a restartování virtuálních počítačů, které byly původně hostovány na hostiteli v pořádku hostiteli. V takovém případě hostitele je označena jako není v pořádku. Ho se nepoužije pro další nasazení, dokud má vymazán nebo nahrazena.
- Pokud hostitel není v pořádku, má potíže během procesu restartování okamžité restartování virtuálních počítačů na hostiteli v pořádku. 

S hostiteli a monitorování virtuálních počítačů poskytuje Azure jsou virtuální počítače Azure, které zaznamenat problémy s hostitelem automaticky restartuje v pořádku hostitele Azure. 

Druhý funkce, která závisí na v tomto scénáři je skutečnost, že služba HANA, který se spustí v restartovat virtuální počítač se spustí automaticky po virtuální počítač se restartuje. Můžete nastavit [HANA služby Automatické restartování](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) prostřednictvím služeb sledovací zařízení jednotlivých služeb HANA.

Tento scénář single-VM může zvýšit přidáním uzlu studenou převzetí služeb při selhání do konfigurace aplikace SAP HANA. V dokumentaci k SAP HANA, se nazývá tato instalace [-automatické převzetí služeb při selhání hostitele](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Tato konfigurace může být vhodné v situacích místní nasazení, kde hardware serveru je omezená, a vyhradit jeden server uzlu jako hostitel-automatické převzetí služeb při selhání uzlu pro sadu produkční hostitele. Ale v Azure, kde základní infrastruktura Azure poskytuje pořádku cílový server pro úspěšné restartování virtuálního počítače, nemá smysl pro nasazení SAP HANA hostitele auto-převzetí služeb při selhání. Z toho důvodu nemáme žádné referenční architektura, která předpokládá pohotovostní uzel HANA hostitele auto-převzetí služeb při selhání. To platí také pro SAP HANA konfigurace Škálováním na více systémů.

## <a name="availability-scenarios-for-two-different-vms"></a>Scénáře dostupnosti pro dva různé virtuální počítače

Pokud chcete použít dva virtuální počítače Azure v rámci skupiny dostupnosti Azure, můžete zvýšit provozu mezi tyto dva virtuální počítače, pokud jste umístili v Azure dostupnosti nastavit v rámci jedné oblasti Azure. Základní instalace v Azure bude vypadat takto:

![Diagram dva virtuální počítače s všechny vrstvy](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Pro ilustraci dostupnosti různé scénáře, byly vynechány několik vrstev v diagramu. Diagram zobrazuje pouze vrstvy znázorňující virtuálních počítačů, hostitele, skupiny dostupnosti a oblastech Azure. Azure instancí virtuální sítě, skupiny prostředků a předplatná nebudou dostupné roli ve scénářích popsaných v této části.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikovat zálohy na druhý virtuální počítač

Jeden z nejvíce elementární nastavení je použití zálohy. Konkrétně můžete mít zálohování transakčního protokolu odeslaná z jednoho virtuálního počítače do jiného virtuálního počítače Azure. Můžete si zvolit typ úložiště Azure. V této instalaci jste zodpovědní za skriptování kopii naplánované zálohování, které jsou prováděny v první virtuální počítač do druhého virtuálního počítače. Pokud budete muset použít druhé instance virtuálních počítačů, je nutné obnovit úplné, přírůstkové nebo rozdílového a zálohování transakčního protokolu na bod, který potřebujete. 

Architektura vypadá takto:

![Diagram dva virtuální počítače s replikací úložiště](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Tato instalace není skvěle hodí k dosažení skvělé časů cíl bodu obnovení (RPO) a cíl doba obnovení (RTO). Časy RTO by zejména dochází z důvodu potřeba plně obnovit databázi dokončení pomocí zkopírovaný zálohy. Tato instalace je však vhodné pro obnovení z odstranění nezamýšleným dat v rámci hlavní instancí. S tímto nastavením, kdykoli můžete obnovit do určité míry v čase, extrahovat data a importovat odstraněná data do vaší hlavní instance. Proto může být vhodné pro použití metody záložní kopii v kombinaci s další funkce vysoké dostupnosti. 

Při zálohování se kopírují, je možné použít virtuální počítač menší než hlavní počítač, spuštěné v instanci SAP HANA. Uvědomte si, že můžete připojit menší počet virtuálních pevných disků na menší virtuální počítače. Informace o omezení jednotlivé typy virtuálních počítačů najdete v tématu [velikostí pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replikace systému SAP HANA bez automatické převzetí služeb při selhání

Scénáře popsaných v této části použijte replikaci systému SAP HANA. SAP dokumentaci najdete v tématu [replikaci systému](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Dva virtuální počítače Azure v jedné oblasti Azure mají různé konfigurace, takže existují určité rozdíly v RTO. Obecně platí nemusí scénáře bez automatické převzetí služeb při selhání platí výslovně pro virtuální počítače v jedné oblasti Azure. Je to proto, že většina selhání v infrastruktuře Azure, Azure službou opravy restartuje primárního virtuálního počítače na jiného hostitele. Existují případy edge, kde mohou pomoci při této konfiguraci z hlediska selhání scénáře. Nebo v některých případech může zákazník chcete mějte na paměti další efektivitu.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replikace systému SAP HANA bez automatické převzetí služeb při selhání a bez předběžného načítání dat

V tomto scénáři použijete pro přesun dat v synchronním způsobem zajistit RPO 0 replikaci systému SAP HANA. Na druhé straně máte dostatečně dlouhé RTO, která nepotřebujete převzetí služeb při selhání nebo data předběžného načítání do mezipaměti instance HANA. V takovém případě je možné dosáhnout další odvětví v konfiguraci provedením následujících akcí:

- Spusťte jiná instance SAP HANA do druhého virtuálního počítače. Instance SAP HANA v druhý virtuální počítač má většina paměti virtuálního počítače. To je obvykle v případě, že dojde k převzetí služeb při selhání do druhého virtuálního počítače. Druhý virtuální počítač můžete vypnout tak, aby replikovaná data je možné načíst do mezipaměti cílové instance HANA do druhého virtuálního počítače.
- Použijte menší velikost virtuálního počítače do druhého virtuálního počítače. Pokud dojde k selhání, musíte na další krok před ruční převzetí služeb při selhání. V tomto kroku můžete velikost virtuálního počítače na velikost zdrojového virtuálního počítače. Tento scénář vypadá takto:

![Diagram dva virtuální počítače s replikací úložiště](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> I když nepoužijete předběžného načítání dat v cílem replikace systému HANA, potřebujete minimálně 64 GB paměti. Budete také potřebovat dostatek paměti kromě 64 GB dat rowstore mějte na paměti cílové instance.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replikace systému SAP HANA bez automatické převzetí služeb při selhání a s předběžného načítání dat

V tomto scénáři se předem načtou data, která se replikují do instance HANA do druhého virtuálního počítače. Tím se eliminuje není předběžného načítání dat dvě výhody. V takovém případě nelze spustit jiného systému SAP HANA do druhého virtuálního počítače. Také nelze použít menší velikost virtuálního počítače. Proto zákazníky zřídka implementovat tento scénář.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replikace systému SAP HANA se automatické převzetí služeb při selhání

Konfigurace dostupnosti v rámci jedné oblasti Azure, dva virtuální počítače Azure se systémem SLES Linux mít clusteru s podporou převzetí služeb při selhání definované ve standardu a nejběžnější. SLES Linux clusteru je založena na [kardiostimulátor](http://www.linux-ha.org/wiki/Pacemaker) framework ve spojení s [STONITH](http://linux-ha.org/wiki/STONITH) zařízení. 

Z hlediska SAP HANA je synchronizovaný režim replikace, který se používá a je nakonfigurovaný automatické převzetí služeb při selhání. Druhý virtuální počítač instance SAP HANA funguje jako pohotovostní aktivního uzlu. Pohotovostní uzel přijímá datový proud s synchronní změnu záznamů z primární instance SAP HANA. Jak transakce jsou zapsána aplikací na primárním uzlu HANA, čeká primárního uzlu HANA, potvrdit potvrzení k aplikaci, dokud sekundární uzel SAP HANA potvrdí obdržel potvrzení záznamu. SAP HANA nabízí dva režimy synchronní replikace. Podrobnosti a popis rozdílů mezi tyto dva režimy synchronní replikace, najdete v článku SAP [režimy replikace pro replikaci systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Na celkové konfiguraci vypadá takto:

![Diagram dvou virtuálních počítačů pomocí úložiště replikace a převzetí služeb při selhání](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Toto řešení můžete zvolit, protože umožňuje dosáhnout RPO = 0 a velmi nízkou RTO. Konfigurovat připojení klientů SAP HANA tak, aby klienti SAP HANA používat virtuální IP adresy pro připojení ke konfiguraci replikace systému HANA. Tím se eliminuje potřeba změnit konfiguraci aplikace, pokud dojde k převzetí služeb při selhání sekundárního uzlu. V tomto scénáři SKU virtuálních počítačů Azure pro primární a sekundární virtuální počítače musí být stejné.

## <a name="next-steps"></a>Další postup

Podrobné pokyny o nastavení těchto konfigurací v Azure najdete v tématu:

- [Nastavení replikace systému SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Vysoká dostupnost pro SAP HANA za použití replikace systému](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Další informace o dostupnosti SAP HANA v oblastech Azure najdete v tématu:

- [Dostupnost SAP HANA v rámci oblasti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

