---
title: Dostupnost SAP HANA v rámci jedné oblasti Azure | Microsoft Docs
description: Popisuje SAP HANA operací na nativních virtuálních počítačích Azure v jedné oblasti Azure.
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
ms.openlocfilehash: dcabd01cfbda8cd892c82b391bf649b2b464d6fb
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927766"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Dostupnost SAP HANA v rámci jedné oblasti Azure
Tento článek popisuje několik scénářů dostupnosti v rámci jedné oblasti Azure. Azure má spoustu oblastí a šíří se po celém světě. Seznam oblastí Azure najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/). Pro nasazení SAP HANA na virtuálních počítačích v rámci jedné oblasti Azure nabízí Microsoft nasazení jednoho virtuálního počítače s instancí HANA. Pro zvýšení dostupnosti můžete nasadit dva virtuální počítače se dvěma instancemi HANA v rámci [skupiny dostupnosti Azure](../../windows/tutorial-availability-sets.md) , která pro dostupnost používá replikaci systému Hana. 

V současné době Azure nabízí [zóny dostupnosti Azure](../../../availability-zones/az-overview.md). Tento článek podrobně popisuje Zóny dostupnosti. Ale obsahuje obecnou diskuzi o používání skupin dostupnosti versus Zóny dostupnosti.

Oblasti Azure, kde jsou nabízeny Zóny dostupnosti mají více datových center. Datová centra jsou nezávislá na zdroji napájení, chlazení a síti. Důvodem nabídky různých zón v rámci jedné oblasti Azure je nasazení aplikací ve dvou nebo třech Zóny dostupnosti, které jsou nabízeny. Nasazení napříč zónami, problémy v oblasti napájení a sítě, které ovlivňují jenom jednu infrastrukturu zóny dostupnosti Azure, je vaše nasazení aplikací v oblasti Azure pořád funkční. Může dojít k nějaké omezené kapacitě. Například virtuální počítače v jedné zóně se můžou ztratit, ale virtuální počítače v dalších dvou zónách budou pořád v provozu. 
 
Skupina dostupnosti Azure je logická schopnost seskupení, která pomáhá zajistit selhání prostředků virtuálních počítačů, které umístíte do skupiny dostupnosti, při jejich nasazení v datovém centru Azure. Azure zajišťuje, že virtuální počítače, které umístíte do skupiny dostupnosti, se budou spouštět napříč několika fyzickými servery, výpočetními racky, jednotkami úložiště a síťovými přepínači. V některých dokumentaci k Azure se tato konfigurace označuje jako umístění v různých [doménách aktualizace a selhání](../../manage-availability.md). Tato místa se většinou nacházejí v datovém centru Azure. Za předpokladu, že problémy se zdrojem energie a sítí by ovlivnily vaše datacentrum, bude to mít vliv na veškerou kapacitu v jedné oblasti Azure.

Umístění datových center, která představuje Zóny dostupnosti Azure, představuje kompromis mezi poskytováním přijatelné latence sítě mezi službami nasazenými v různých zónách a vzdáleností mezi datovými centry. Přirozené haváriích v ideálním případě by neovlivnily napájení, síťové napájení a infrastrukturu pro všechny Zóny dostupnosti v této oblasti. Jelikož však Monumental přirozené haváriích, Zóny dostupnosti nemusí vždy poskytovat požadovanou dostupnost v rámci jedné oblasti. Zamyslete se na hurikán Marie, který se dorazí na ostrov Portoriko od 20. září 2017. Hurikán v podstatě vyvolal téměř 100% nedostupnosti na úrovni 90-míle.

## <a name="single-vm-scenario"></a>Scénář s jedním virtuálním počítačem

Ve scénáři s jedním virtuálním počítačem vytvoříte virtuální počítač Azure pro instanci SAP HANA. Pomocí služby Azure Premium Storage hostovat disk s operačním systémem a všechny datové disky. Smlouva SLA pro Azure za provozu 99,9% a SLA dalších komponent Azure je dostatečná, abyste mohli splnit Slay dostupnosti pro vaše zákazníky. V tomto scénáři není nutné využívat skupinu dostupnosti Azure pro virtuální počítače, na kterých běží vrstva DBMS. V tomto scénáři spoléháte na dvě různé funkce:

- Automatické restartování virtuálního počítače Azure (také označované jako Azure Service retušovací)
- SAP HANA automatické restartování

Automatické restartování virtuálního počítače Azure, neboli služba Service retušova, je funkce v Azure, která funguje na dvou úrovních:

- Hostitel serveru Azure kontroluje stav virtuálního počítače, který je hostovaný na hostiteli serveru.
- Kontroler prostředků infrastruktury Azure monitoruje stav a dostupnost hostitele serveru.

Funkce kontroly stavu monitoruje stav každého virtuálního počítače, který je hostovaný na hostiteli Azure serveru. Pokud se virtuální počítač nachází v nesprávném stavu, bude možné iniciovat restartování virtuálního počítače agentem hostitele Azure, který kontroluje stav virtuálního počítače. Kontroler prostředků infrastruktury kontroluje stav hostitele kontrolou mnoha různých parametrů, které mohou indikovat problémy s hardwarem hostitele. Kontroluje také přístupnost hostitele přes síť. Označení problémů s hostitelem může vést k následujícím událostem:

- Pokud hostitel signalizuje špatný stav, spustí se restart hostitele a restartování virtuálních počítačů, které byly spuštěny na hostiteli.
- Pokud hostitel není v dobrém stavu po úspěšném restartování, iniciuje se opětovné nasazení virtuálních počítačů, které byly původně v uzlu stav není v pořádku, na hostitelský server, který je v pořádku. V takovém případě je původní hostitel označený jako není v pořádku. Nebude se používat pro další nasazení, dokud se nevymaže nebo nenahradí.
- Pokud má poškozený hostitel během procesu restartování problémy, spustí se okamžité restartování virtuálních počítačů na hostiteli, který je v pořádku. 

Díky monitorování hostitele a virtuálního počítače, které poskytuje Azure, se virtuální počítače Azure, na kterých dochází k problémům s hostiteli, automaticky restartují v dobrém hostiteli Azure. 

>[!IMPORTANT]
>Azure Service retušova nerestartuje virtuální počítače se systémem Linux, kde je hostovaný operační systém v nenouzovém stavu jádra. Výchozí nastavení běžně používaných verzí systému Linux nerestartují virtuální počítače nebo servery, na kterých je jádro systému Linux v nenouzovém stavu. Místo toho výchozí předpokládá, že operační systém v jádru jádra má být schopný připojit ladicí program jádra k analýze. Azure respektuje toto chování tím, že nerestartuje automaticky virtuální počítač s hostovaným operačním systémem v takovém stavu. Předpokladem je, že tyto výskyty jsou extrémně vzácné. Výchozí chování můžete přepsat tak, aby bylo možné restartovat virtuální počítač. Pokud chcete změnit výchozí chování, povolte v/etc/sysctl.conf. parametr ' kernel. nouzové '. Čas, který jste nastavili pro tento parametr, je v sekundách. Běžné Doporučené hodnoty jsou před aktivací restartování pomocí tohoto parametru počkat na 20-30 sekund. Viz také <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf> .

Druhá funkce, na kterou se spoléháte v tomto scénáři, je fakt, že se služba HANA, která běží v restartování virtuálního počítače, spouští automaticky po restartování virtuálního počítače. Službu Hana můžete nastavit tak, aby se [automaticky restartovala](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) přes sledovací služby různých služeb Hana.

Tento scénář můžete vylepšit tím, že do konfigurace SAP HANA přidáte studený uzel s podporou převzetí služeb při selhání. V dokumentaci SAP HANA se toto nastavení označuje jako [automatické převzetí služeb při selhání hostitele](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Tato konfigurace může být vhodná v místní situaci nasazení, kde je serverový hardware omezený, a vyhradit jeden server uzel jako uzel automatického převzetí služeb při selhání hostitele pro sadu produkčních hostitelů. Ale v Azure, kde základní infrastruktura Azure poskytuje dobrý cílový server pro úspěšné restartování virtuálního počítače, nemá smysl nasadit SAP HANA automatické převzetí služeb při selhání hostitele. Vzhledem k tomu, že služba Azure je k dispozici, neexistuje žádná referenční architektura, která předpokládá pohotovostní uzel pro automatické převzetí služeb při selhání hostitele HANA. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Zvláštní případ SAP HANA konfigurací škálování na více instancí v Azure
Vysoká dostupnost pro SAP HANA konfigurace škálování na více instancí se spoléhá na použití opravy virtuálních počítačů Azure a restartování instance služby SAP HANA, protože virtuální počítač je znovu spuštěný. Architektury vysoké dostupnosti založené na replikaci systému HANA se budou zavádět později. 


## <a name="availability-scenarios-for-two-different-vms"></a>Scénáře dostupnosti pro dva různé virtuální počítače

Pokud používáte dva virtuální počítače Azure v rámci skupiny dostupnosti Azure, můžete prodloužit dobu provozu mezi těmito dvěma virtuálními počítači, pokud jsou umístěné v rámci jedné oblasti Azure v sadě dostupnosti Azure. Základní nastavení v Azure by vypadalo takto:

![Diagram dvou virtuálních počítačů se všemi vrstvami](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Pro ilustraci různých scénářů dostupnosti jsou některé z vrstev v diagramu vynechány. Diagram zobrazuje jenom vrstvy, které zobrazují virtuální počítače, hostitele, skupiny dostupnosti a oblasti Azure. Instance Azure Virtual Network, skupiny prostředků a předplatná nehrají roli ve scénářích popsaných v této části.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikace záloh na druhý virtuální počítač

Jedním z základní nastavení je použít zálohování. Konkrétně můžete mít zálohy transakčního protokolu dodávané z jednoho virtuálního počítače do jiného virtuálního počítače Azure. Můžete zvolit typ Azure Storage. V tomto instalačním programu zodpovídáte za skriptování kopie plánovaných záloh, které jsou prováděny na prvním virtuálním počítači s druhým virtuálním počítačem. Pokud potřebujete použít druhé instance virtuálních počítačů, je nutné obnovit úplné, přírůstkové/rozdílové zálohování a zálohy protokolu transakcí do bodu, který potřebujete. 

Architektura vypadá takto:

![Diagram znázorňující architekturu dvou virtuálních počítačů pomocí replikace úložiště.](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Toto nastavení není vhodné pro dosažení skvělého cíle bodu obnovení (RPO) a doby obnovení (RTO). RTOí času, zejména by znamenalo, že je potřeba plně obnovit úplnou databázi pomocí zkopírovaných záloh. Tato instalace se ale hodí pro obnovu nezamýšlených odstranění dat v hlavních instancích. Pomocí této instalace se můžete kdykoli vrátit k určitému časovému okamžiku, extrahovat data a importovat Odstraněná data do vaší hlavní instance. Proto může být vhodné použít metodu záložního kopírování v kombinaci s jinou funkcí vysoké dostupnosti. 

Při kopírování záloh můžete použít menší virtuální počítač, než je hlavní virtuální počítač, na kterém je spuštěná instance SAP HANA. Mějte na paměti, že k menším virtuálním počítačům můžete připojit menší počet virtuálních pevných disků. Informace o omezeních jednotlivých typů virtuálních počítačů najdete v tématu [velikosti pro virtuální počítače Linux v Azure](../../sizes.md).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replikace systému SAP HANA bez automatického převzetí služeb při selhání

Scénáře popsané v této části se používají SAP HANA systémové replikace. Dokumentaci ke SAP najdete v tématu [replikace systému](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Scénáře bez automatického převzetí služeb při selhání nejsou běžné pro konfigurace v rámci jedné oblasti Azure. Konfigurace bez automatického převzetí služeb při selhání, i když se vyhnete Pacemaker instalaci, vás povede k ručnímu monitorování a převzetí služeb při selhání Vzhledem k tomu, že tato akce trvá a zároveň, většina zákazníků se místo toho spoléhá na Azure Service retušovací. Existují některé hraniční případy, kdy tato konfigurace může pomáhat při scénářích selhání. V některých případech se může stát, že zákazník bude chtít realizovat vyšší efektivitu.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replikace systému SAP HANA bez automatického převzetí služeb při selhání a bez přednačtení dat

V tomto scénáři použijete SAP HANA replikaci systému k přesunu dat synchronním způsobem, aby bylo možné provést RPO bodu obnovení 0. Na druhé straně máte dost dlouhého RTO, že nepotřebujete převzetí služeb při selhání nebo přednačtení dat do mezipaměti instance HANA. V takovém případě je možné dosáhnout další hospodárnosti v konfiguraci provedením následujících akcí:

- V druhém virtuálním počítači spusťte jinou instanci SAP HANA. Instance SAP HANA v druhém VIRTUÁLNÍm počítači přebírá většinu paměti virtuálního počítače. V případě převzetí služeb při selhání druhému VIRTUÁLNÍmu počítači je potřeba vypnout spuštěnou instanci SAP HANA, která má data plně načtená ve druhém virtuálním počítači, aby se replikovaná data mohla načíst do mezipaměti cílové instance HANA v druhém virtuálním počítači.
- Pro druhý virtuální počítač použijte menší velikost virtuálního počítače. Pokud dojde k převzetí služeb při selhání, budete mít další krok před ručním převzetím služeb při selhání. V tomto kroku změníte velikost virtuálního počítače na velikost zdrojového virtuálního počítače. 
 
Scénář vypadá takto:

![Diagram dvou virtuálních počítačů pomocí replikace úložiště](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> I když nepoužíváte předběžné načtení dat v cíli replikace systému HANA, budete potřebovat alespoň 64 GB paměti. K udržení rowstore dat v paměti cílové instance také potřebujete dostatek paměti kromě 64 GB.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replikace systému SAP HANA bez automatického převzetí služeb při selhání a přednačtení dat

V tomto scénáři se data, která se replikují do instance HANA ve druhém virtuálním počítači, načtou. Tím se eliminují dvě výhody, které nenačítá data. V tomto případě nemůžete na druhém virtuálním počítači spustit jiný SAP HANA systém. Nemůžete také použít menší velikost virtuálního počítače. Proto zákazníci zřídka implementují tento scénář.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replikace systému SAP HANA s automatickým převzetím služeb při selhání

Ve standardních a nejběžnějších konfiguracích dostupnosti v rámci jedné oblasti Azure mají dva virtuální počítače Azure se systémem SLES Linux definovaný cluster s podporou převzetí služeb při selhání. Cluster SLES Linux je založený na rozhraní [Pacemaker](./high-availability-guide-suse-pacemaker.md) v kombinaci se zařízením [STONITH](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) . 

Z SAP HANA perspektivy se použije synchronizovaný režim replikace a nakonfiguruje se automatické převzetí služeb při selhání. Ve druhém virtuálním počítači funguje SAP HANA instance jako aktivní pohotovostní uzel. Pohotovostní uzel přijímá synchronní datový proud záznamů změn z primární instance SAP HANA. Vzhledem k tomu, že aplikace potvrdí transakce v primárním uzlu HANA, vyčká primární uzel HANA o potvrzení potvrzení do aplikace, dokud sekundární SAP HANA uzel nepotvrdí, že přijal záznam pro zápis. SAP HANA nabízí dva synchronní režimy replikace. Podrobnosti a popis rozdílů mezi těmito dvěma synchronními režimy replikace najdete v článku režimy replikace článků SAP [pro replikaci SAP HANA systému](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Celková konfigurace vypadá takto:

![Diagram dvou virtuálních počítačů s replikací úložiště a převzetím služeb při selhání](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Toto řešení můžete zvolit, protože umožňuje dosáhnout cíle RPO = 0 a nízkého RTOu. Nakonfigurujte SAP HANA připojení klientů tak, aby klienti SAP HANA používali virtuální IP adresu pro připojení k konfiguraci replikace systému HANA. Taková konfigurace eliminuje nutnost překonfigurovat aplikaci, pokud dojde k převzetí služeb při selhání sekundárního uzlu. V tomto scénáři musí být skladové položky virtuálních počítačů Azure pro primární a sekundární virtuální počítač stejné.

## <a name="next-steps"></a>Další kroky

Podrobné pokyny k nastavení těchto konfigurací v Azure najdete tady:

- [Nastavení replikace systému SAP HANA na virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Vysoká dostupnost pro SAP HANA pomocí replikace systému](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Další informace o dostupnosti SAP HANA napříč oblastmi Azure najdete v těchto tématech:

- [SAP HANA dostupnost napříč oblastmi Azure](./sap-hana-availability-across-regions.md)