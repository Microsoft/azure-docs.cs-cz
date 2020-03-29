---
title: Dostupnost SAP HANA v rámci jedné oblasti Azure | Dokumenty společnosti Microsoft
description: Popisuje operace SAP HANA na nativních virtuálních počítačích Azure v jedné oblasti Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ef7161e653ec582708f242b67c643d960d75e27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255465"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Dostupnost SAP HANA v rámci jedné oblasti Azure
Tento článek popisuje několik scénářů dostupnosti v rámci jedné oblasti Azure. Azure má mnoho oblastí, které jsou rozmístěny po celém světě. Seznam oblastí Azure najdete v tématu [Oblasti Azure](https://azure.microsoft.com/regions/). Pro nasazení SAP HANA na virtuálních počítačích v rámci jedné oblasti Azure, Microsoft nabízí nasazení jednoho virtuálního počítače s instancí HANA. Pro zvýšení dostupnosti můžete nasadit dva virtuální počítače se dvěma instancemi HANA v rámci [skupiny dostupnosti Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) která používá replikaci systému HANA pro dostupnost. 

Azure v současné době nabízí [zóny dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Tento článek nepopisuje zóny dostupnosti podrobně. Ale zahrnuje obecnou diskusi o použití skupiny dostupnosti versus zóny dostupnosti.

Oblasti Azure, kde jsou nabízeny zóny dostupnosti mají více datových center. Datová centra jsou nezávislá v dodávkách zdroje napájení, chlazení a sítě. Důvodem pro nabízení různých zón v rámci jedné oblasti Azure je nasazení aplikací ve dvou nebo třech zónách dostupnosti, které jsou nabízeny. Nasazení napříč zónami, problémy v napájení a sítě ovlivňující jenom jednu infrastrukturu zóny dostupnosti Azure, nasazení aplikace v rámci oblasti Azure je stále funkční. Může dojít k určité snížené kapacitě. Například virtuální chody v jedné zóně může dojít ke ztrátě, ale virtuální chody v dalších dvou zónách by stále v provozu. 
 
Sada dostupnosti Azure je logická funkce seskupení, která pomáhá zajistit, aby prostředky virtuálních počítače, které umístíte v rámci sady dostupnosti, byly od sebe navzájem izolované selháním, když jsou nasazeny v rámci datového centra Azure. Azure zajišťuje, že virtuální počítače, které umístíte do skupiny dostupnosti, se budou spouštět napříč několika fyzickými servery, výpočetními racky, jednotkami úložiště a síťovými přepínači. V některých dokumentaci Azure se tato konfigurace označuje jako umístění v různých [doménách aktualizací a selhání](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Tato umístění se obvykle nacházejí v datovém centru Azure. Za předpokladu, že by problémy se zdrojem energie a sítí ovlivnily datové centrum, které nasazujete, byla by ovlivněna veškerá vaše kapacita v jedné oblasti Azure.

Umístění datových center, které představují zóny dostupnosti Azure, je kompromisem mezi poskytováním přijatelné latence sítě mezi službami nasazenými v různých zónách a vzdáleností mezi datovými centry. Přírodní katastrofy by v ideálním případě neovlivnily napájení, síťové dodávky a infrastrukturu pro všechny zóny dostupnosti v této oblasti. Jak však ukázaly monumentální přírodní katastrofy, zóny dostupnosti nemusí vždy poskytovat požadovanou dostupnost v rámci jedné oblasti. Přemýšlejte o hurikánu Maria, který zasáhl ostrov Puerto Rico v září 20, 2017. Hurikán v podstatě způsobil téměř 100% výpadek na 90-mílovém ostrově.

## <a name="single-vm-scenario"></a>Scénář jednoho virtuálního počítače

Ve scénáři s jedním virtuálním počítačem vytvoříte virtuální počítač Azure pro instanci SAP HANA. Azure Premium Storage slouží k hostování disku operačního systému a všech datových disků. Smlouvy o dostupnosti Azure ve výši 99,9 % a smlouvy SLA jiných součástí Azure stačí k tomu, abyste splnili smlouvy SLA dostupnosti pro vaše zákazníky. V tomto scénáři nemáte potřeba využít sadu dostupnosti Azure pro virtuální počítače, které spouštějí vrstvu DBMS. V tomto scénáři můžete spoléhat na dvě různé funkce:

- Automatické restartování virtuálního počítače Azure (označované také jako retušování služby Azure)
- Automatické restartování SAP HANA

Automatické restartování virtuálního počítače Azure nebo ozdravování služeb je funkce v Azure, která funguje na dvou úrovních:

- Hostitel serveru Azure kontroluje stav virtuálního počítače, který je hostovaný na hostiteli serveru.
- Řadič infrastruktury Azure monitoruje stav a dostupnost hostitele serveru.

Funkce kontroly stavu monitoruje stav každého virtuálního počítače, který je hostovaný na hostiteli serveru Azure. Pokud virtuální počítač spadá do stavu není v pořádku, restartování virtuálního počítače můžete inicializovat agenta hostitele Azure, který kontroluje stav virtuálního počítače. Řadič prostředků infrastruktury kontroluje stav hostitele kontrolou mnoha různých parametrů, které mohou znamenat problémy s hardwarem hostitele. Kontroluje také přístupnost hostitele prostřednictvím sítě. Označení problémů s hostitelem může vést k následujícím událostem:

- Pokud hostitel signalizuje špatný stav, spustí se restartování hostitele a restartování virtuálních počítačích, které byly spuštěny na hostiteli.
- Pokud hostitel není v pořádku stavu po úspěšném restartování, je zahájeno opětovné nasazení virtuálních počítače, které byly původně na uzlu není v pořádku na hostitelský server v pořádku. V tomto případě je původní hostitel označen jako není v pořádku. Nebude použit pro další nasazení, dokud nebude vymazán nebo nahrazen.
- Pokud hostitel není v pořádku má problémy během procesu restartování, je aktivováno okamžité restartování virtuálních počítačích na hostiteli v pořádku. 

Díky monitorování hostitele a virtuálních počítačích poskytovaným Azure se virtuální počítače Azure, které mají problémy s hostiteli, automaticky restartují na vpořádku na vpořádku hostitele Azure. 

>[!IMPORTANT]
>Azure service healing nerestartuje virtuální počítače Linuxu, kde je hostovaný operační systém v stavu paniky jádra. Výchozí nastavení běžně používaných verzí Linuxu není automatické restartování virtuálních počítačů nebo serveru, kde je jádro Linuxu v panice. Místo toho výchozí předpokládá, aby OS v jádru panic stavu, aby bylo možné připojit ladicí program jádra k analýze. Azure toto chování respektuje tím, že automaticky nerestartuje virtuální počítač s hostem operačního prohlížeče v takovém stavu. Předpokladem je, že takové výskyty jsou extrémně vzácné. Můžete přepsat výchozí chování povolit restartování virtuálního počítače. Chcete-li změnit výchozí chování, povolte parametr kernel.panic v souboru /etc/sysctl.conf. Čas nastavený pro tento parametr je v sekundách. Běžné doporučené hodnoty jsou čekat 20-30 sekund před aktivací restartování prostřednictvím tohoto parametru. Viz <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>také .

Druhá funkce, na kterou se v tomto scénáři spoléháte, je skutečnost, že služba HANA, která se spouští v restartování virtuálního počítače, se spustí automaticky po restartování virtuálního počítače. Můžete nastavit [hana služby automatické restartování](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) prostřednictvím služby hlídacího psa různých služeb HANA.

Tento scénář jednoho virtuálního počítače můžete vylepšit přidáním studeného uzlu převzetí služeb při selhání do konfigurace SAP HANA. V dokumentaci SAP HANA se toto nastavení nazývá [automatické převzetí služeb při selhání hostitele](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Tato konfigurace může mít smysl v situaci místního nasazení, kde je omezený hardware serveru a vyučujete uzel s jedním serverem jako uzel automatického převzetí služeb při selhání hostitele pro sadu produkčních hostitelů. Ale v Azure, kde základní infrastruktura Azure poskytuje zdravý cílový server pro úspěšné restartování virtuálního počítače, nemá smysl nasadit automatické převzetí služeb při selhání hostitele SAP HANA. Z důvodu ozdravování služby Azure neexistuje žádná referenční architektura, která předpokládá pohotovostní uzel pro automatické převzetí služeb při selhání hostitele HANA. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Zvláštní případ sap hana horizontálnínavýšení kapacity konfigurace v Azure
Vysoká dostupnost pro konfigurace sap hana horizontálnínavýšení kapacity se spoléhá na service healing virtuálních počítačů Azure a restartování instance SAP HANA jako virtuální počítač je znovu v provozu. Architektury s vysokou dostupností založené na replikaci systému HANA budou zavedeny později. 


## <a name="availability-scenarios-for-two-different-vms"></a>Scénáře dostupnosti pro dva různé virtuální hody

Pokud používáte dva virtuální počítače Azure v rámci azure dostupnosti, můžete zvýšit dobu provozumezi těmito dvěma virtuálními počítači, pokud jsou umístěny v Azure Dostupnost Set v rámci jedné oblasti Azure. Základní nastavení v Azure bude vypadat takto:

![Diagram dvou virtuálních připojení se všemi vrstvami](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Pro ilustraci různých scénářů dostupnosti jsou vynechány několik vrstev v diagramu. Diagram zobrazuje jenom vrstvy, které zobrazují virtuální počítače, hostitele, sady dostupnosti a oblasti Azure. Instance virtuální sítě Azure, skupiny prostředků a předplatná nehrají roli ve scénářích popsaných v této části.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikace záloh do druhého virtuálního počítače

Jedním z nejzákladnějších nastavení je použití záloh. Zejména můžete mít zálohy protokolu transakcí dodané z jednoho virtuálního počítače do jiného virtuálního počítače Azure. Můžete zvolit typ úložiště Azure. V tomto nastavení jste zodpovědní za skriptování kopii naplánované zálohy, které jsou prováděny na prvním virtuálním počítači do druhého virtuálního počítače. Pokud potřebujete použít druhé instance virtuálních počítače, musíte obnovit úplné, přírůstkové/rozdílové a transakční protokol zálohy do bodu, který potřebujete. 

Architektura vypadá takto:

![Diagram dvou virtuálních připojení s replikací úložiště](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Toto nastavení není vhodné pro dosažení velké cíle bodu obnovení (RPO) a cíle doby obnovení (RTO) krát. RTO časy zejména by trpět kvůli nutnosti plně obnovit kompletní databázi pomocí zkopírovaných záloh. Toto nastavení je však užitečné pro obnovení z neúmyslného odstranění dat v hlavních instancích. S tímto nastavením můžete kdykoli obnovit do určitého bodu v čase, extrahovat data a importovat odstraněná data do hlavní instance. Proto může mít smysl použít metodu záložní kopie v kombinaci s dalšími funkcemi vysoké dostupnosti. 

Při kopírování záloh můžete použít menší virtuální počítač než hlavní virtuální počítač, na který je spuštěná instance SAP HANA. Mějte na paměti, že k menším virtuálním virtuálním kvitům můžete připojit menší počet virtuálních discích. Informace o limitech jednotlivých typů virtuálních počítačů najdete [v tématu Velikosti pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replikace systému SAP HANA bez automatického převzetí služeb při selhání

Scénáře popsané v této části používají replikaci systému SAP HANA. Dokumentace SAP naleznete v [tématu Replikace systému](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Scénáře bez automatického převzetí služeb při selhání nejsou běžné pro konfigurace v rámci jedné oblasti Azure. Konfigurace bez automatického převzetí služeb při selhání, i když se vyhnete nastavení kardiostimulátoru, zavazuje vás sledovat a převzetí služeb při selhání ručně. Vzhledem k tomu, že to vyžaduje a úsilí také, většina zákazníků se spoléhají na azure service healing místo. Existují některé hraniční případy, kdy tato konfigurace může pomoci z hlediska scénáře selhání. Nebo, v některých případech, zákazník může chtít realizovat větší efektivitu.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replikace systému SAP HANA bez automatického převzetí služeb při selhání a bez předběžného načtení dat

V tomto scénáři použijete replikaci systému SAP HANA k přesunutí dat synchronním způsobem k dosažení rpo 0. Na druhou stranu máte dostatečně dlouhé RTO, že nepotřebujete převzetí služeb při selhání nebo předběžné načtení dat do mezipaměti instance HANA. V tomto případě je možné dosáhnout další hospodárnosti ve vaší konfiguraci provedením následujících opatření:

- Spusťte jinou instanci SAP HANA v druhém virtuálním jevu. Instance SAP HANA v druhém virtuálním počítači přebírá většinu paměti virtuálního počítače. V případě, že převzetí služeb při selhání na druhý virtuální ms, budete muset vypnout spuštěnou instanci SAP HANA, která má data plně načtená v druhém virtuálním jevu, aby se replikovaná data dala načíst do mezipaměti cílové instance HANA v druhém virtuálním jevu.
- Použijte menší velikost virtuálního počítače na druhém virtuálním počítači. Pokud dojde k převzetí služeb při selhání, máte další krok před ruční převzetí služeb při selhání. V tomto kroku změníte velikost virtuálního počítače na velikost zdrojového virtuálního počítače. 
 
Scénář vypadá takto:

![Diagram dvou virtuálních připojení s replikací úložiště](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> I v případě, že nepoužíváte přednačtení dat v cíli replikace systému HANA, potřebujete alespoň 64 GB paměti. Potřebujete také dostatek paměti kromě 64 GB, aby data rowstore v paměti cílové instance.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replikace systému SAP HANA bez automatického převzetí služeb při selhání a s přednačtením dat

V tomto scénáři jsou předinstalována data, která se replikují do instance HANA v druhém virtuálním počítače. Tím se eliminuje dvě výhody není předběžné načítání dat. V takovém případě nelze spustit jiný systém SAP HANA na druhém virtuálním počítači. Taky nemůžete použít menší velikost virtuálního počítače. Proto zákazníci zřídka implementovat tento scénář.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replikace systému SAP HANA s automatickým převzetím služeb při selhání

Ve standardní a nejběžnější konfiguraci dostupnosti v rámci jedné oblasti Azure mají dva virtuální počítače Azure se systémem SLES Linux definovaný cluster s podporou převzetí služeb při selhání. Cluster SLES Linux je založen na frameworku [Pacemaker](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) ve spojení se zařízením [STONITH.](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) 

Z hlediska SAP HANA je synchronizován použitý režim replikace a je nakonfigurováno automatické převzetí služeb při selhání. V druhém virtuálním jevu instance SAP HANA funguje jako hot pohotovostní uzel. Pohotovostní uzel obdrží synchronní datový proud záznamů změn z primární instance SAP HANA. Jako transakce jsou potvrzeny aplikací v primárním uzlu HANA, primární uzel HANA čeká na potvrzení potvrzení aplikace, dokud sekundární uzel SAP HANA nepotvrdí, že obdržel záznam potvrzení. SAP HANA nabízí dva synchronní režimy replikace. Podrobnosti a popis rozdílů mezi těmito dvěma synchronními režimy replikace naleznete v článku SAP [Režimy replikace pro replikaci systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Celková konfigurace vypadá takto:

![Diagram dvou virtuálních připojení s replikací úložiště a převzetím služeb při selhání](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Toto řešení můžete zvolit, protože umožňuje dosáhnout RPO= 0 a nízké RTO. Nakonfigurujte připojení klienta SAP HANA tak, aby klienti SAP HANA používali virtuální IP adresu pro připojení ke konfiguraci replikace systému HANA. Taková konfigurace eliminuje potřebu překonfigurovat aplikaci, pokud dojde k převzetí služeb při selhání do sekundárního uzlu. V tomto scénáři virtuálních zařízení Azure virtuálních zařízení pro primární a sekundární virtuální počítače musí být stejné.

## <a name="next-steps"></a>Další kroky

Podrobné pokyny k nastavení těchto konfigurací v Azure najdete v následujících tématech:

- [Nastavení replikace systému SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Vysoká dostupnost pro SAP HANA pomocí replikace systému](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Další informace o dostupnosti SAP HANA v oblastech Azure najdete v tématu:

- [Dostupnost SAP HANA v různých oblastech Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

