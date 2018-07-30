---
title: Dostupnost SAP HANA v rámci jedné oblasti Azure | Dokumentace Microsoftu
description: Popisuje operace SAP HANA na virtuálních počítačích Azure nativní v jedné oblasti Azure.
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
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4f2defd60ec6b835ec856c9253a92f1d6817e861
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325999"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Dostupnost SAP HANA v rámci jedné oblasti Azure
Tento článek popisuje několik scénářů dostupnost v rámci jedné oblasti Azure. Azure má řadu oblastí, rozložit po celém světě. Seznam oblastí Azure najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/). Pro nasazení SAP HANA na virtuálních počítačích v rámci jedné oblasti Azure, Microsoft nabízí nasazení jeden virtuální počítač pomocí HANA instance. Pro zajištění vyšší dostupnosti, nasadíte dva virtuální počítače se dvě instance HANA v rámci [dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) systémové replikace HANA, která používá pro dostupnost. 

V současné době je nabídka Azure [zóny dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Tento článek nepopisuje podrobně zóny dostupnosti. Ale obsahuje Obecná diskuse o použití skupin dostupnosti srovnání zón dostupnosti.

Oblasti Azure, kde se zónami dostupnosti nabízejí mít několik datových center. Tato datová centra jsou nezávislé na poskytování zdroji napájení, chlazení a sítě. Důvod nabízí různé zóny v rámci jedné oblasti Azure je k nasazení aplikací napříč zónami dostupnosti dva nebo tři, které jsou k dispozici. Nasazení napříč zónami, vystaví v Power BI a sítě, které mají vliv jenom jedna infrastruktura zóny dostupnosti Azure, nasazení vaší aplikace v rámci oblasti Azure je stále funkční. Může dojít k některé snížení kapacity. Například virtuální počítače v jedné zóně může dojít ke ztrátě, ale virtuální počítače v jiných dvě zóny stále by šlo pracovat. 
 
Skupině dostupnosti Azure je funkce logického seskupení, který pomáhá zajistit, že prostředky virtuálních počítačů, které umístíte do skupiny dostupnosti jsou selhání izolovaná od sebe navzájem nasazené v rámci datového centra Azure. Azure zajišťuje, že virtuální počítače, které umístíte do skupiny dostupnosti, se budou spouštět napříč několika fyzickými servery, výpočetními racky, jednotkami úložiště a síťovými přepínači. V některé dokumentaci Azure, tato konfigurace se označuje jako umístění v různých [aktualizace a domény selhání](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Tato umístění jsou obvykle v rámci datového centra Azure. Za předpokladu, že problémy power zdroje a sítě by vliv datového centra, kterou nasazujete, by se ovlivnil vaší kapacity v jedné oblasti Azure.

Umístění datacentra, které představují zóny dostupnosti Azure je to kompromis mezi doručování přijatelné síťová latence mezi služeb nasazených v různých oblastech a vzdálenost mezi datovými centry. Přirozené catastrophes v ideálním případě nebude mít vliv na výkon, zadejte sítě a infrastrukturu pro všechny zóny dostupnosti v této oblasti. Však mít uvedeno monumental catastrophes fyzické zóny dostupnosti nemusí vždy poskytnout dostupnosti, které chcete v rámci jedné oblasti. Představte si Marie Hurikán, který přístupů Portoriko ostrov 20. září 2017. Hurikán v podstatě způsobená téměř 100 % jeho obsahu nedostupnosti na 90 mil celou ostrov.

## <a name="single-vm-scenario"></a>Scénář jednoho virtuálního počítače

V případě jednoho virtuálního počítače vytvořit virtuální počítač Azure pro instance SAP HANA. Použijete k hostování disk s operačním systémem a všechny datové disky Azure Premium Storage. Azure provozu SLA 99,9 % a SLA dalšími komponentami Azure, stačí si můžete splnit vaše dostupnosti smlouvy SLA pro vaše zákazníky. V tomto scénáři nemáte žádné požadavky pro využití dostupnosti Azure pro virtuální počítače, na kterých běží systém DBMS vrstvy. V tomto scénáři se spoléháte na dvě různé funkce:

- Azure VM automatické restartování (také označované jako opravy služby Azure)
- Automatické restartování SAP HANA

Azure automatické restartování virtuálního počítače nebo služby opravy, je funkce v Azure, která funguje na dvou úrovních:

- Hostitelský Azure server kontroluje stav virtuálního počítače, který je hostován na hostitelském serveru.
- Kontroler prostředků infrastruktury Azure monitoruje stav a dostupnost serveru hostitele.

Funkce kontroly stavu monitoruje stav každému virtuálnímu počítači, který je hostován na hostiteli, který Azure server. Virtuální počítač patří do stavu není v pořádku, restartování virtuálního počítače můžete inicializovat agentem Azure, která kontroluje stav virtuálního počítače. Kontroler prostředků infrastruktury kontroluje stav hostitele tak, že zkontrolujete velký počet různých parametrů, které může znamenat problémy s hostitelský hardware. Také kontroluje na přístupnost hostitele prostřednictvím sítě. Značit problémy s hostitelem může vést k následujícím událostem:

- Pokud hostitel signály špatný stav, se aktivuje restartování hostitele a restartování virtuálních počítačů, které byly spuštěny v hostiteli.
- Pokud hostitel není v pořádku po úspěšném restartování, je zahájeno opětovné nasazení virtuálních počítačů, které byly původně uzlu teď není v pořádku na serveru hostitele v pořádku. V takovém případě původního hostitele je označen jako není v pořádku. To se nepoužije pro další nasazení dokud má vymazán nebo nahradit.
- Pokud není v pořádku, hostitel problémy během procesu restartování, se aktivuje okamžité restartování virtuálních počítačů na hostitele služby v pořádku. 

S hostiteli a monitorování virtuálních počítačů, které poskytuje Azure se virtuální počítače Azure, ke které dochází k problémům s hostiteli automaticky restartují na v pořádku hostitele Azure. 

>[!IMPORTANT]
>Opravou služeb Azure nerestartuje virtuální počítače s Linuxem ve kterém je hostovaný operační systém ve stavu tísňový jádra. Výchozí nastavení pro položky běžně používané verze systému Linux, nejsou automaticky restartuje se virtuální počítače nebo serveru, kde jádro Linuxu je ve stavu tísňový. Místo toho výchozí předpokládá mít operační systém na tísňový stav jádra být schopni připojit ladicí program jádra k analýze. Azure je dodržením toto chování automaticky restartováním virtuálního počítače pomocí hostovaného operačního systému v takové stavu. Předpokladem je, že takové výskyty jsou velmi vzácné. Může přepsat výchozí chování, které chcete povolit restartování virtuálního počítače. Chcete-li změnit výchozí chování povolit parametr "kernel.panic" v /etc/sysctl.conf. Čas, kdy jste nastavili pro tento parametr je v sekundách. Běžné doporučené hodnoty jsou čekat 20 – 30 sekund před aktivací restartování prostřednictvím tohoto parametru. Viz také <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

Druhá funkce, která závisí na v tomto scénáři je skutečnost, že služba HANA, která běží na virtuálním počítači restartován automaticky spustí po virtuální počítač restartuje. Můžete nastavit [HANA služba automatické restartování](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) prostřednictvím služeb sledovacích různých služeb HANA.

Tento scénář jednoho virtuálního počítače může zlepšit přidáním uzlu studenou převzetí služeb při selhání do konfigurace SAP HANA. V dokumentaci k SAP HANA, toto nastavení nazývá [hostovat automatické převzetí služeb při selhání](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Tato konfigurace může mít smysl v situacích v místním nasazení, kde je omezený hardware serveru a vyhradit uzlu jeden server jako hostitele uzlu automatické převzetí služeb při selhání pro sadu hostitelům ve výrobě. Ale v Azure, kde základní infrastruktury Azure poskytuje v dobrém stavu cílového serveru k úspěšné restartování virtuálního počítače, nemá smysl pro nasazení SAP HANA hostitele auto-převzetí služeb při selhání. Z důvodu opravy služby Azure, neexistuje žádná referenční architekturu, která předpokládá pohotovostní uzel pro HANA hostitele auto-převzetí služeb při selhání. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Zvláštní případ horizontální navýšení kapacity konfigurací SAP HANA v Azure
Vysoká dostupnost pro horizontální navýšení kapacity konfigurací SAP HANA se spoléhá na službu opravy restartování instance SAP HANA a virtuálních počítačů Azure jako virtuální počítač je v provozu a znovu spustit. Vysoká dostupnost architektury založené na systémové replikace HANA se chystáte zavést později. 


## <a name="availability-scenarios-for-two-different-vms"></a>Scénáře dostupnosti pro dvě různé virtuální počítače

Pokud používáte dva virtuální počítače Azure do skupiny dostupnosti Azure, můžete zvýšit dobu provozu mezi těmito dvěma virtuálními počítači, pokud přejdou na skupině dostupnosti Azure v rámci jedné oblasti Azure. Základní nastavení v Azure vypadat nějak takto:

![Diagram dva virtuální počítače s všechny vrstvy](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Pro ilustraci různých dostupnost scénáře, jsou vynechány některé z vrstev v diagramu. Diagram znázorňuje pouze vrstvy znázorňující virtuálních počítačů, hostitelů, skupiny dostupnosti a oblastmi Azure. Azure Virtual Network instancí, skupiny prostředků a předplatná není hrají roli ve scénářích popsaných v této části.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikace zálohy do druhého virtuálního počítače

Jednou z nejvíce základní nastavení je použití zálohy. Konkrétně můžete mít zálohy transakčního protokolu odeslaná z jednoho virtuálního počítače k jinému virtuálnímu počítači Azure. Můžete zvolit typ služby Azure Storage. V tomto nastavení zodpovídáte za skriptování kopii plánované zálohy, které jsou prováděny na prvním virtuálním počítači na druhý virtuální počítač. Budete muset použít druhé instance virtuálních počítačů, je nutné obnovit úplné a přírůstkové/rozdílové zálohování a zálohy transakčního protokolu na bod, který potřebujete. 

Tato architektura vypadá takto:

![Diagram dvou virtuálních počítačů pomocí úložiště replikace](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Toto nastavení není vhodné pro dosažení skvělé časy cíl bodu obnovení (RPO) a cíl času obnovení (RTO). RTO časy zejména utrpí z důvodu potřeba plně obnovení kompletní databáze pomocí zkopírovaného zálohy. Toto nastavení je však vhodné pro obnovení z odstranění nežádoucích dat na hlavní instance. S tímto nastavením v každém okamžiku můžete obnovit do určitého bodu v čase, extrahovat data a importovat odstraněná data do hlavní instanci. Proto může mít smysl použít metodu záložní kopii v kombinaci s další funkce vysoké dostupnosti. 

Při zálohování se kopírují, může být možné použít virtuálního počítače s menší než hlavní virtuálního počítače, na kterém běží instance SAP HANA na. Uvědomte si, že menší počet virtuálních pevných disků můžete připojit k menších virtuálních počítačů. Informace o omezeních jednotlivé typy virtuálních počítačů najdete v tématu [velikostí pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replikace systému SAP HANA bez automatického převzetí služeb při selhání

Scénáře popsané v této části použít systémové replikace SAP HANA. Dokumentaci k SAPU naleznete v tématu [systémové replikace](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Scénářů bez automatického převzetí služeb při selhání nejsou běžné pro konfigurace v rámci jedné oblasti Azure. Konfigurace bez automatického převzetí služeb při selhání, když Pacemaker nastavení, jak se vyhnout obligates s monitorováním a převzetí služeb při selhání ručně. Od této přijímá a úsilí a většina zákazníků spoléhá na služby Azure, místo toho opravy. Existují některé hraniční případy, kdy tato konfigurace může pomoci z hlediska scénáře selhání. Nebo v některých případech může být vhodné zákazník mohli využívat další efektivitu.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replikace systému SAP HANA bez automatického převzetí služeb při selhání a bez předběžného načítání dat

V tomto scénáři použít systémové replikace SAP HANA pro přesun dat v podobě synchronní k dosažení plánovaný bod obnovení z 0. Na druhé straně máte dostatečně dlouhé RTO, kterou nepotřebujete převzetí služeb při selhání nebo data předběžného načítání do mezipaměti instance HANA. V takovém případě je možné dosáhnout ještě konfigurace vaší ekonomiky provedením následujících akcí:

- Spusťte jiná instance SAP HANA v druhý virtuální počítač. Instance SAP HANA v druhý virtuální počítač má nejvíce paměti virtuálního počítače. V případě, že převzetí služeb při selhání do druhého virtuálního počítače, musíte pro vypnutí spuštěné instance SAP HANA, který obsahuje data plně načtený v druhý virtuální počítač, tak, aby se replikovaná data je možné načíst do mezipaměti cílové instance HANA v druhý virtuální počítač.
- Použijte menší velikost virtuálního počítače na druhý virtuální počítač. Pokud dojde k selhání, je nutné na další krok před ruční převzetí služeb při selhání. V tomto kroku změníte velikost virtuálního počítače na velikost zdrojového virtuálního počítače. 
 
Scénář vypadá takto:

![Diagram dvou virtuálních počítačů pomocí úložiště replikace](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> I v případě, že nepoužíváte předběžné načtení dat v cílové systémové replikace HANA, potřebujete minimálně 64 GB paměti. Budete také potřebovat dostatek paměti kromě 64 GB dat rowstore mějte na paměti cílová instance.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replikace systému SAP HANA bez automatického převzetí služeb při selhání a s daty přednačtení

V tomto scénáři se předem načtou data, která se replikuje do instance HANA v druhý virtuální počítač. Tím se eliminují dvěma výhodami není předběžné načítání data. V tomto případě nelze spustit jiného systému SAP HANA na druhý virtuální počítač. Také nelze použít menší velikost virtuálního počítače. Proto zákazníci zřídka tento scénář implementovat.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replikace systému SAP HANA pomocí automatického převzetí služeb při selhání

Ve standardu a nejběžnější Konfigurace dostupnosti v rámci jedné oblasti Azure, dva virtuální počítače Azure s Linuxem SLES mít cluster převzetí služeb při selhání definované. Je na základě clusteru SLES Linux [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) framework ve spojení s [využitím techniky STONITH](http://linux-ha.org/wiki/STONITH) zařízení. 

Z hlediska SAP HANA se synchronizuje režim replikace, který se používá a nakonfigurovat automatické převzetí služeb při selhání. Do druhého virtuálního počítače instanci SAP HANA funguje jako aktivní pohotovostní uzel. Pohotovostní uzel přijme synchronní proud záznamy řešení change z primární instance SAP HANA. Jako transakce usilujeme o to aplikací ve primárního uzlu HANA, a primární uzel HANA čeká na potvrzení potvrzení do aplikace, dokud sekundární uzel SAP HANA potvrdí, že přijatá záznam o zápisu. SAP HANA nabízí dva režimy synchronní replikace. Podrobnosti a popis rozdílů mezi těmito dvěma režimy synchronní replikace, najdete v článku SAP [režimech replikace pro systémové replikace SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Na celkové konfiguraci vypadá takto:

![Diagram dvou virtuálních počítačů pomocí úložiště replikace a převzetí služeb při selhání](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Toto řešení může zvolit, protože umožňuje dosahovat plánovaný bod obnovení = 0 a nízké RTO. Konfigurace připojení k SAP HANA klienta tak, aby klienti SAP HANA používat virtuální IP adresu pro připojení k HANA konfigurace replikace systému. Taková konfigurace není potřeba změnit konfiguraci aplikace, pokud dojde k převzetí služeb při selhání sekundárního uzlu. V tomto scénáři skladové položky virtuálních počítačů Azure pro primárního a sekundárního virtuálního počítače musí být stejné.

## <a name="next-steps"></a>Další postup

Podrobné pokyny k nastavení těchto konfigurací v Azure najdete v tématu:

- [Nastavení systémové replikace SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Vysoká dostupnost pro SAP HANA s využitím systémové replikace](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Další informace o dostupnosti SAP HANA v různých oblastech Azure najdete v tématu:

- [Dostupnost SAP HANA v různých oblastech Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

