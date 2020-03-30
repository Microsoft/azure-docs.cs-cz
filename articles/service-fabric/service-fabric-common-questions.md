---
title: Časté otázky týkající se služby Microsoft Azure Fabric
description: Nejčastější dotazy týkající se service fabric, včetně možností, případy použití a běžné scénáře.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: bf61858b446c1ac6d4a0210571fffaa721ad0166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254887"
---
# <a name="commonly-asked-service-fabric-questions"></a>Nejčastější dotazy ke službě Service Fabric

Existuje mnoho často kladené otázky o tom, co Service Fabric může dělat a jak by měl být použit. Tento dokument se zabývá mnoha z těchto běžných otázek a jejich odpovědí.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Nastavení a správa clusteru

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Jak lze vrátit zpět svůj certifikát clusteru Service Fabric?

Vrácení zpět všechny upgrade na vaši aplikaci vyžaduje zjištění selhání stavu před clusteru clusteru Service Fabric potvrzení změny; potvrzené změny lze pouze posunout dopředu. Eskalace inženýrprostřednictvím služeb podpory zákazníků, může být nutné obnovit clusteru, pokud byla zavedena nemonitorované porušení změny certifikátu.  [Upgrade aplikace Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) použije [parametry upgradu aplikace](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)a přináší příslib upgradu nulových prostojů.  V návaznosti na náš doporučený režim monitorování upgradu aplikace je automatický průběh prostřednictvím aktualizačních domén založen na průchodu kontroly stavu, automatické vrácení zpět, pokud se nezdaří aktualizace výchozí služby.
 
Pokud váš cluster stále využívá klasickou vlastnost Kryptografického otisku certifikátu v šabloně Správce prostředků, doporučujeme [změnit cluster z kryptografického otisku certifikátu na běžný název](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn), abyste využili funkce správy moderních tajných klíčů.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Můžu vytvořit cluster, který zahrnuje víc oblastí Azure nebo vlastních datových center?

Ano. 

Základní technologie clusteringu Service Fabric lze kombinovat počítače běžící kdekoli na světě, pokud mají připojení k síti k sobě navzájem. Vytváření a spouštění takového clusteru však může být složité.

Pokud máte zájem o tento scénář, doporučujeme vám kontaktovat buď prostřednictvím [seznamu problémů Service Fabric GitHub](https://github.com/azure/service-fabric-issues) nebo prostřednictvím zástupce podpory, abyste získali další pokyny. Tým Service Fabric pracuje na poskytnutí další jasnost, pokyny a doporučení pro tento scénář. 

Některé věci, aby zvážila: 

1. Prostředek clusteru Service Fabric v Azure je dnes místní, stejně jako škálovací sady virtuálních strojů, na kterých je cluster založený. To znamená, že v případě selhání místní může ztratit možnost spravovat clusteru prostřednictvím Správce prostředků Azure nebo portálazure. K tomu může dojít i v případě, že cluster zůstane spuštěn a budete s ním moci pracovat přímo. Kromě toho Azure dnes nenabízí možnost mít jednu virtuální síť, která je použitelná napříč oblastmi. To znamená, že cluster s více oblastmi v Azure vyžaduje buď [veřejné IP adresy pro každý virtuální počítač ve škálovacích sadách virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) nebo v gateway Azure [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Tyto možnosti vytváření sítí mají různé dopady na náklady, výkon a do určité míry návrh aplikace, takže před tím, než se takové prostředí postavíte, je nutná pečlivá analýza a plánování.
2. Údržba, správa a monitorování těchto počítačů může být komplikovaná, zejména pokud se rozprostírá napříč _typy_ prostředí, například mezi různými poskytovateli cloudu nebo mezi místními prostředky a Azure. Je třeba dbát na to, aby upgrady, monitorování, správa a diagnostika byly pochopeny pro cluster i aplikace před spuštěním produkčních úloh v takovém prostředí. Pokud už máte zkušenosti s řešením těchto problémů v Azure nebo v rámci vlastních datových center, pak je pravděpodobné, že stejná řešení lze použít při vytváření nebo spuštění clusteru Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Přijímají uzly Service Fabric automaticky aktualizace operačního systému?

Můžete použít [virtuální stroj Scale Set Automatické Aktualizace obrazu operačního systému](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) obecně dostupné funkce dnes.

Pro clustery, které nejsou spuštěny v Azure, jsme [poskytli aplikaci](service-fabric-patch-orchestration-application.md) pro opravu operačních systémů pod uzly Service Fabric.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Můžu v clusteru SF používat škálovací sady velkých virtuálních strojů? 

**Stručná odpověď** - Ne. 

**Dlouhá odpověď** – i když škálovací sady velkých virtuálních strojů umožňují škálovat škálování virtuálního počítače na 1000 instancí virtuálních počítače, činí tak pomocí skupin umístění (PGs). Domény selhání (FD) a upgradovací domény (UD) jsou konzistentní pouze v rámci skupiny umístění Service Fabric používá FDs a UDs k rozhodování o umístění replik y služby nebo instance služby. Vzhledem k tomu, fds a UDs jsou srovnatelné pouze v rámci skupiny umístění, SF nelze použít. Například Pokud VM1 v PG1 má topologii FD = 0 a VM9 v PG2 má topologii FD = 4, neznamená to, že VM1 a VM2 jsou na dvou různých hardwarových racků, proto SF nelze použít fd hodnoty v tomto případě k rozhodování o umístění.

Existují další problémy s velkými sadami škálování virtuálních strojů v současné době, jako je nedostatek podpory vyrovnávání zatížení úrovně 4. Podrobnosti o [rozsáhlých sadách](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) naleznete v části



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Jaká je minimální velikost clusteru Service Fabric? Proč nemůže být menší?

Minimální podporovaná velikost clusteru Service Fabric, který spouštějí produkční úlohy, je pět uzlů. Pro vývojové scénáře podporujeme jeden uzel (optimalizovaný pro rychlé vývojové prostředí v sadě Visual Studio) a pět clusterů uzlů.

Vyžadujeme, aby produkční cluster měl alespoň 5 uzlů z následujících tří důvodů:
1. I v případě, že nejsou spuštěny žádné uživatelské služby, cluster Service Fabric spustí sadu stavových systémových služeb, včetně služby pojmenování a služby správce převzetí služeb při selhání. Tyto systémové služby jsou nezbytné pro to, aby cluster zůstal funkční.
2. Vždy umístíme jednu repliku služby na uzel, takže velikost clusteru je horní limit pro počet replik, které může mít služba (ve skutečnosti oddíl).
3. Vzhledem k tomu, že upgrade clusteru sníží alespoň jeden uzel, chceme mít vyrovnávací paměť alespoň jednoho uzlu, proto *in addition* chceme, aby produkční cluster měl kromě minimálního minima alespoň dva uzly. Minimum je velikost kvora systémové služby, jak je vysvětleno níže.  

Chceme, aby byl cluster k dispozici tváří v tvář současnému selhání dvou uzlů. Aby byl cluster Service Fabric k dispozici, musí být k dispozici systémové služby. Stavové systémové služby, jako je služba pojmenování a služba správce převzetí služeb při selhání, které sledují, jaké služby byly nasazeny do clusteru a kde jsou aktuálně hostované, závisí na silné konzistenci. Tato silná konzistence zase závisí na schopnosti získat *kvorum* pro danou aktualizaci stavu těchto služeb, kde kvorum představuje striktní většinu replik (N/2 +1) pro danou službu. Proto pokud chceme být odolné proti současné ztrátě dvou uzlů (tedy souběžné ztráty dvou replik systémové služby), musíme mít ClusterSize - QuorumSize >= 2, který vynutí minimální velikost pět. Chcete-li to vidět, zvažte, že cluster má N uzly a existují N repliky systémové služby -- jeden na každém uzlu. Velikost kvora pro systémovou službu je (N/2 + 1). Výše uvedená nerovnost vypadá jako N - (N/2 + 1) >= 2. Existují dva případy, aby zvážila: když N je sudý a když N je lichý. Pokud n je sudý,\*řekněme N = 2 m kde\*m >\*= 1, nerovnost vypadá jako 2 m - (2 m/2 + 1) >= 2 nebo m >= 3. Minimum pro N je 6 a toho je dosaženo, když m = 3. Na\*druhou stranu, pokud n je lichý, řekněme N = 2 m\*+1 kde m\*>= 1, nerovnost vypadá jako 2\*m+1 - ( (2 m+1)/2 + 1 ) >= 2 nebo 2 m+1 - (m+1) >= 2 nebo m >= 2. Minimum pro N je 5 a toho je dosaženo, když m = 2. Proto mezi všechny hodnoty N, které splňují nerovnost ClusterSize - QuorumSize >= 2, minimum je 5.

Všimněte si, že ve výše uvedeném argumentu jsme předpokládali, že každý uzel má repliku systémové služby, proto je velikost kvora vypočítána na základě počtu uzlů v clusteru. Však změnou *TargetReplicaSetSize* můžeme provést velikost kvora menší než (N/2 +1), což může vyvolat dojem, že bychom mohli mít cluster menší než 5 uzlů a stále mají 2 další uzly nad velikostí kvora. Například v clusteru 4 uzlu, pokud nastavíme TargetReplicaSetSize na 3, velikost kvora založená na TargetReplicaSetSize je (3/2 + 1) nebo 2, tak máme ClusterSize - QuorumSize = 4-2 >= 2. Nemůžeme však zaručit, že systémová služba bude na nebo nad kvorem, pokud ztratíme libovolný pár uzlů současně, může se vrátit k tomu, že dva uzly, které jsme ztratili, byly hostující dvě repliky, takže systémová služba přejde do ztráty kvora (zbývá pouze jedna replika) a stane se nedostupným.

S tímto pozadím prozkoumáme některé možné konfigurace clusteru:

**Jeden uzel**: tato možnost neposkytuje vysokou dostupnost, protože ztráta jednoho uzlu z jakéhokoli důvodu znamená ztrátu celého clusteru.

**Dva uzly**: kvorum pro službu nasazenou ve dvou uzlech (N = 2) je 2 (2/2 + 1 = 2). Při ztrátě jedné repliky není možné vytvořit kvorum. Vzhledem k tomu, že provedení upgradu služby vyžaduje dočasné zrušení repliky, nejedná se o užitečnou konfiguraci.

**Tři uzly**: se třemi uzly (N = 3), požadavek na vytvoření kvora je stále dva uzly (3/2 + 1 = 2). To znamená, že můžete ztratit jednotlivé uzly a zachovat kvorum, ale současné selhání dvou uzlů způsobí, že systémové služby dojde ke ztrátě kvora a způsobí, že cluster přestane být nedostupný.

**Čtyři uzly**: se čtyřmi uzly (N = 4), požadavek na vytvoření kvora je tři uzly (4/2 + 1 = 3). To znamená, že můžete ztratit jednotlivé uzly a zachovat kvorum, ale současné selhání dvou uzlů způsobí, že systémové služby dojde ke ztrátě kvora a způsobí, že cluster přestane být nedostupný.

**Pět uzlů**: s pěti uzly (N = 5), požadavek na vytvoření kvora je stále tři uzly (5/2 + 1 = 3). To znamená, že můžete ztratit dva uzly současně a stále udržovat kvorum pro systémové služby.

Pro produkční úlohy musíte být odolné vůči současnéselhání alespoň dva uzly (například jeden z důvodu upgradu clusteru, jeden z jiných důvodů), takže je vyžadováno pět uzlů.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Mohu vypnout cluster v noci/o víkendech, abych ušetřil náklady?

Obecně to možné není. Service Fabric ukládá stav na místních dočasných discích, což znamená, že pokud je virtuální počítač přesunut na jiného hostitele, data se s ním nepřesunou. Při normálním provozu to není problém, protože nový uzel je aktuální jinými uzly. Pokud však zastavíte všechny uzly a restartujete je později, existuje významná možnost, že většina uzlů začne na nových hostitelích a systém nebude schopen obnovit.

Pokud chcete vytvořit clustery pro testování aplikace před nasazením, doporučujeme dynamicky vytvořit tyto clustery jako součást [průběžné integrace nebo průběžné nasazení kanálu](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Jak lze upgradovat operační systém (například ze systému Windows Server 2012 na Systém Windows Server 2016)?

Zatímco pracujeme na vylepšeném prostředí, dnes jste zodpovědní za upgrade. Je nutné upgradovat image operačního systému na virtuálních počítačích clusteru jeden virtuální počítač najednou. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Lze šifrovat připojené datové disky v typu uzlu clusteru (škálovací sada virtuálních počítačů)?
Ano.  Další informace naleznete [v tématu Vytvoření clusteru s připojenými datovými disky](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) a [Azure Disk Encryption for Virtual Machine Scale Sets](../virtual-machine-scale-sets/disk-encryption-overview.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Můžu použít virtuální počítače s nízkou prioritou v typu uzlu clusteru (škálovací sada virtuálních strojů)?
Ne. Virtuální aplikace s nízkou prioritou nejsou podporované. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Jaké adresáře a procesy je třeba vyloučit při spuštění antivirového programu v clusteru?

| **Antivirové vyloučené adresáře** |
| --- |
| Soubory programů\Aplikace Microsoft Service Fabric |
| FabricDataRoot (z konfigurace clusteru) |
| FabricLogRoot (z konfigurace clusteru) |

| **Procesy vyloučené antivirovým programem** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| Soubor FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| Soubor StoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Jak může moje aplikace ověřit KeyVault získat tajné klíče?
Následující prostředky jsou prostředky pro vaši aplikaci získat pověření pro ověřování KeyVault:

A. Během úlohy sestavení a balení aplikací můžete získat certifikát do datového balíčku aplikace SF a použít jej k ověření na KeyVault.
B. Pro hostitele s povolenými škálováním virtuálních strojů msi můžete vyvinout jednoduchý PowerShell SetupEntryPoint pro vaši aplikaci SF, abyste získali [přístupový token z koncového bodu MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)a pak [načetli tajné klíče z KeyVault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Návrh aplikace

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Jaký je nejlepší způsob, jak dotazovat data napříč oddíly spolehlivé kolekce?

Spolehlivé kolekce jsou obvykle [rozděleny na oddíly](service-fabric-concepts-partitioning.md) povolit horizontální navýšení kapacity pro vyšší výkon a propustnost. To znamená, že stát pro danou službu může být rozložen do desítek nebo stovek strojů. Chcete-li provést operace s celou sadou dat, máte několik možností:

- Vytvořte službu, která se dotazuje všech oddílů jiné služby na vyžádat požadovaná data.
- Vytvořte službu, která může přijímat data ze všech oddílů jiné služby.
- Pravidelně nabízená data z každé služby do externího úložiště. Tento přístup je vhodný pouze v případě, že dotazy, které provádíte, nejsou součástí základní obchodní logiky, protože data externího úložiště budou zastaralá.
- Případně uložte data, která musí podporovat dotazování napříč všemi záznamy přímo v úložišti dat, nikoli ve spolehlivé kolekci. To eliminuje problém se zastaralými daty, ale neumožňuje výhody spolehlivé kolekce, které mají být využity.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Jaký je nejlepší způsob dotazování na data mezi herci?

Objekty actor jsou navrženy tak, aby byly nezávislé jednotky stavu a výpočetní, takže se nedoporučuje provádět obecné dotazy stavu objektu actor za běhu. Pokud máte potřebu dotazu v celé sadě stavu objektu actor, měli byste zvážit buď:

- Nahrazení služby objektu actor stavové spolehlivé služby tak, aby počet síťových požadavků na shromažďování všech dat z počtu objektů actor na počet oddílů ve vaší službě.
- Navrhování vašich aktérů pravidelně tlačit jejich stav do externího úložiště pro snadnější dotazování. Jak je uvedeno výše, tento přístup je životaschopný pouze v případě, že dotazy, které provádíte, nejsou vyžadovány pro vaše chování za běhu.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Kolik dat mohu uložit do spolehlivé kolekce?

Spolehlivé služby jsou obvykle rozděleny na oddíly, takže částka, kterou můžete uložit, je omezena pouze počtem počítačů, které máte v clusteru, a množstvím paměti, která je v těchto počítačích k dispozici.

Jako příklad předpokládejme, že máte spolehlivou kolekci ve službě se 100 oddíly a 3 repliky, ukládání objektů, které v průměru 1 kb ve velikosti. Nyní předpokládejme, že máte cluster 10 počítače s 16 GB paměti na počítač. Pro jednoduchost a být konzervativní, předpokládejme, že operační systém a systémové služby, Service Fabric runtime a vaše služby spotřebovávají 6 gb, takže 10 gb k dispozici na počítač nebo 100 GB pro cluster.

Mějte na paměti, že každý objekt musí být uložentřikrát (jedna primární a dvě repliky), budete mít dostatek paměti pro přibližně 35 milionů objektů v kolekci při provozu na plnou kapacitu. Doporučujeme však být odolný vůči současné ztrátě domény selhání a upgradu domény, která představuje asi 1/3 kapacity a sníží počet na zhruba 23 milionů.

Všimněte si, že tento výpočet také předpokládá:

- Že distribuce dat mezi oddíly je zhruba jednotná nebo že hlásíte metriky zatížení Správci prostředků clusteru. Ve výchozím nastavení service fabric načte rovnováhu na základě počtu replik. V předchozím příkladu, který by dal 10 primární repliky a 20 sekundární repliky na každém uzlu v clusteru. To funguje dobře pro zatížení, které je rovnoměrně rozloženo mezi oddíly. Pokud zatížení není sudé, musíte hlásit zatížení tak, aby Správce prostředků můžete zabalit menší repliky dohromady a umožnit větší repliky využívat více paměti na jednotlivém uzlu.

- Že se jedná o spolehlivou službu, je jediným stavem ukládání v clusteru. Vzhledem k tomu, že můžete nasadit více služeb do clusteru, musíte mít na paměti prostředky, které každý potřebuje ke spuštění a správě jeho stavu.

- Že samotný cluster neroste ani se nezmenšuje. Pokud přidáte další počítače, Service Fabric bude vyvážit repliky využít další kapacitu, dokud počet počítačů překročí počet oddílů ve vaší službě, protože jednotlivé repliky nemůže span počítače. Naopak pokud zmenšíte velikost clusteru odebráním počítačů, repliky jsou zabaleny těsněji a mají menší celkovou kapacitu.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Kolik dat lze uložit do herce?

Stejně jako u spolehlivých služeb je množství dat, které lze uložit do služby objektu actor, omezeno pouze celkovým prostorem na disku a pamětí, které jsou k dispozici v uzlech v clusteru. Jednotlivé objekty actor jsou však nejúčinnější, pokud se používají k zapouzdření malé množství stavu a přidružené obchodní logiky. Obecně platí, že jednotlivé objekty actor by měl mít stav, který se měří v kilobajtech.

## <a name="other-questions"></a>Další otázky

### <a name="how-does-service-fabric-relate-to-containers"></a>Jak service fabric souvisí s kontejnery?

Kontejnery nabízejí jednoduchý způsob, jak balíček služeb a jejich závislosti tak, aby běžely konzistentně ve všech prostředích a mohou pracovat v izolovaném způsobem na jednom počítači. Service Fabric nabízí způsob, jak nasadit a spravovat služby, včetně [služeb, které byly zabaleny v kontejneru](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Plánujete open-source Service Fabric?

Máme open source části Service Fabric[(spolehlivé služby framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [spolehlivé actors framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.NET knihovny integrace Jádra](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)a Service [Fabric CLI](https://github.com/Azure/service-fabric-cli)) na GitHubu a přijímat příspěvky komunity k těmto projektům. 

Nedávno jsme [oznámili,](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) že plánujeme open-source service fabric runtime. V tomto okamžiku máme [service fabric repo](https://github.com/Microsoft/service-fabric/) na GitHubu s linuxovými nástroji pro sestavení a testování, což znamená, že můžete klonovat úložiště, vytvářet service fabric pro Linux, spouštět základní testy, otevírat problémy a odesílat žádosti o přijetí uživatelů. Usilovně pracujeme na tom, aby se prostředí sestavení systému Windows také přesunulo, spolu s kompletním prostředím CI.

Další podrobnosti navazujte na [blog Service Fabric,](https://blogs.msdn.microsoft.com/azureservicefabric/) který je ohlášen.

## <a name="next-steps"></a>Další kroky

Informace o [základních konceptech service fabric](service-fabric-technical-overview.md) a [osvědčených postupech](service-fabric-best-practices-overview.md)
