---
title: Běžné otázky týkající se Microsoft Azure Service Fabric
description: Nejčastější dotazy týkající se Service Fabric, včetně možností, případů použití a běžných scénářů.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: 95463865c52ee501ceca22cae60d19a089236fd1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048676"
---
# <a name="commonly-asked-service-fabric-questions"></a>Nejčastější dotazy ke službě Service Fabric

Existuje mnoho nejčastějších otázek, které Service Fabric můžou dělat a jak se mají použít. Tento dokument obsahuje mnoho běžných otázek a jejich odpovědi.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Instalace a Správa clusteru

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Návody vrátit Service Fabric certifikát clusteru?

Vrácení jakéhokoli upgradu do vaší aplikace vyžaduje detekci selhání stavu před tím, než vaše Service Fabric kvorum clusteru tuto změnu provedla. potvrzené změny lze provést pouze posunutím. Pokud byla zavedena nemonitorovaná změna nemonitorovaného certifikátu, může být nutné obnovit váš cluster prostřednictvím služeb podpory pro eskalace prostřednictvím služeb zákaznické podpory.  [Upgrade aplikace Service Fabric](./service-fabric-application-upgrade.md) aplikuje [parametry upgradu aplikace](./service-fabric-application-upgrade-parameters.md)a poskytuje příslib upgradu s žádným výpadkem.  Po doporučeném monitorovaném režimu upgradu aplikace je automatický průběh prostřednictvím aktualizačních domén založený na kontrolách stavu, které se provedou, pokud dojde k chybě při aktualizaci výchozí služby.
 
Pokud váš cluster stále využívá vlastnost s klasickým kryptografickým otiskem certifikátu v šabloně Správce prostředků, doporučujeme [změnit cluster z kryptografického otisku certifikátu na běžný název](./service-fabric-cluster-change-cert-thumbprint-to-cn.md), abyste využili moderní funkce pro správu tajných kódů.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Můžu vytvořit cluster, který zahrnuje několik oblastí Azure nebo vlastní datová centra?

Ano. 

Technologie clusteringu na základní Service Fabric se dá použít ke kombinování počítačů běžících kdekoli na světě, pokud mají navzájem připojení k síti. Sestavení a spuštění takového clusteru ale může být složité.

Pokud vás zajímá tento scénář, doporučujeme vám, abyste se dostali v kontaktu buď prostřednictvím [seznamu problémů s Service Fabric GitHubem](https://github.com/azure/service-fabric-issues) , nebo prostřednictvím zástupce podpory, abyste získali další doprovodné materiály. Tým Service Fabric pracuje na poskytnutí dodatečné jasnosti, pokynů a doporučení pro tento scénář. 

Zvažte několik věcí, které je potřeba vzít v úvahu: 

1. Prostředek clusteru Service Fabric v Azure je dnes regionální, jako je služba Virtual Machine Scale Sets, na které je cluster integrovaný. To znamená, že v případě regionálního selhání může dojít ke ztrátě schopnosti spravovat cluster prostřednictvím Azure Resource Manager nebo Azure Portal. K tomu může dojít i v případě, že cluster zůstává spuštěný a vy budete moct s ním pracovat přímo. Kromě toho Azure ještě nenabízí možnost mít jednu virtuální síť, která je použitelná v různých oblastech. To znamená, že cluster s více oblastmi v Azure vyžaduje buď [veřejné IP adresy pro každý virtuální počítač ve službě Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) nebo [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Tyto možnosti sítě mají různé dopady na náklady, výkon a určitý návrh aplikace, takže je nutné provést pečlivou analýzu a plánování před tím, než takové prostředí sestaví.
2. Údržba, Správa a monitorování těchto počítačů se můžou stát složitě, _zejména v případě_ , že jsou rozložená mezi různými poskytovateli cloudu nebo mezi místními prostředky a Azure. Před spuštěním produkčních úloh v takovém prostředí je třeba dbát na to, aby se zajistilo, že upgrady, monitorování, Správa a diagnostika jsou srozumitelné pro cluster i aplikace. Pokud již máte zkušenosti s řešením těchto problémů v Azure nebo v rámci vašich vlastních Datacenter, je pravděpodobně vhodné použít stejná řešení při sestavování nebo spouštění clusteru Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Budou uzly Service Fabric automaticky dostávat aktualizace operačního systému?

V dnešní době můžete používat [automatickou funkci aktualizace image sady škálování virtuálního počítače](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) , která je všeobecně dostupná.

Pro clustery, které neběží v Azure, [poskytujeme aplikaci](service-fabric-patch-orchestration-application.md) , která umožňuje opravovat operační systémy pod uzly Service Fabric.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Můžu v mém clusteru SF použít velké sady virtuálních počítačů s měřítkem pro virtuální počítače? 

**Krátká odpověď** – ne. 

**Dlouhá odpověď** – i když velké sady škálování virtuálních počítačů umožňují škálovat škálování virtuálního počítače na 1000 instancí virtuálních počítačů, provede to pomocí skupin umístění (PGS). Domény selhání (doménami selhání) a upgradované domény (UDs) jsou konzistentní jenom v rámci skupiny umístění Service Fabric, která používá doménami selhání a UDs k rozhodování o umístění replik služby/instancí služby. Vzhledem k tomu, že doménami selhání a UDs jsou srovnatelné pouze v rámci skupiny umístění, SF ji nemůže použít. Například pokud VM1 v SO1 má topologii FD = 0 a VM9 v SO2 má topologii FD = 4, neznamená to, že VM1 a VM2 jsou ve dvou různých skříních hardwaru, takže SF nemůže v tomto případě použít hodnoty FD k rozhodnutí o umístění.

V tuto chvíli existují další problémy s velkými sadami škálování virtuálních počítačů, jako je třeba podpora vyrovnávání zatížení úrovně 4. [Podrobnosti o velkých sadách škálování najdete v](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) tématu.



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Jaká je minimální velikost clusteru Service Fabric? Proč není možné ji zmenšit?

Minimální podporovaná velikost pro Service Fabric cluster s produkčními úlohami je pět uzlů. Pro scénáře vývoje podporujeme jeden uzel (optimalizovaný pro rychlé vývojové prostředí v rámci sady Visual Studio) a pět clusterů uzlů.

Pro následující tři důvody potřebujeme, aby měl provozní cluster aspoň pět uzlů:
1. I když nejsou spuštěny žádné uživatelské služby, Cluster Service Fabric spouští sadu stavových služeb, včetně služby pojmenování a služby správce převzetí služeb při selhání. Tyto systémové služby jsou nezbytné pro to, aby cluster zůstal v provozu.
2. Vždycky ukládáme jednu repliku služby na uzel, takže velikost clusteru je horním limitem počtu replik, které služba (ve skutečnosti oddíl) může mít.
3. Vzhledem k tomu, že upgrade clusteru bude obsahovat alespoň jeden uzel, chceme mít vyrovnávací paměť aspoň jeden uzel. proto chceme, aby měl provozní cluster *kromě* minimálního minima aspoň dva uzly. Minimální hodnota je velikost kvora systémové služby, jak je vysvětleno níže.  

Chceme, aby byl cluster dostupný na tváři souběžného selhání dvou uzlů. Aby byl cluster Service Fabric k dispozici, musí být systémové služby k dispozici. Služby stavového systému jako služba pojmenování služeb a správce převzetí služeb při selhání, které sledují, které služby jsou v clusteru nasazené a kde se aktuálně hostují, závisí na silné konzistenci. Tato silná konzistence pak závisí na schopnosti získat *kvorum* pro jakoukoli danou aktualizaci do stavu těchto služeb, kde kvorum představuje striktní většinu replik (N/2 + 1) pro danou službu. Takže pokud chceme být odolný proti souběžné ztrátě dvou uzlů (tedy současně současně se dvěma replikami systémové služby), musí mít ClusterSize-QuorumSize >= 2, což vynutí minimální velikost 5. Pokud to chcete vidět, zvažte, že cluster má N uzlů a v každém uzlu je N replik služby System Service--One. Velikost kvora systémové služby je (N/2 + 1). Výše uvedená nerovnost vypadá jako N-(N/2 + 1) >= 2. Je třeba vzít v úvahu dva případy, kdy je N i v případě, že N je liché. Pokud N je i, řekněme N = 2 \* m, kde m >= 1, nerovnosti vypadá 2 \* m-(2 \* m/2 + 1) >= 2 nebo m >= 3. Minimum pro N je 6 a je dosaženo v případě m = 3. Na druhé straně platí, že pokud N je lichá, řekněme N = 2 \* m + 1, kde m >= 1, nerovnost by vypadala 2 \* m + 1-((2 \* m + 1)/2 + 1) >= 2 nebo 2 \* m + 1-(m + 1) >= 2 nebo m >= 2. Minimum pro N je 5 a je dosaženo při m = 2. Proto ze všech hodnot N, které odpovídají nerovnosti ClusterSize-QuorumSize >= 2, je minimum 5.

Všimněte si, že ve výše uvedeném argumentu jsme předpokládali, že každý uzel má repliku systémové služby, takže se velikost kvora vypočítá na základě počtu uzlů v clusteru. Změnou *TargetReplicaSetSize* však můžeme zmenšit velikost kvora menší než (N/2 + 1), což by mohlo znamenat, že by mohlo být cluster menší než 5 uzlů a stále mít 2 Navíc uzly nad velikost kvora. Například v případě clusteru se čtyřmi uzly, pokud nastavíme TargetReplicaSetSize na 3, bude velikost kvora založená na TargetReplicaSetSize (3/2 + 1) nebo 2, proto máme ClusterSize-QuorumSize = 4-2 >= 2. Nicméně nemůžeme zaručit, že systémová služba bude v kvoru nebo vyšší, pokud ztratíte všechny dvojice uzlů současně, může to být tím, že dva uzly, které jsme ztratili, nahrály dvě repliky, takže systémová služba přejde do ztráty kvora (bude mít jenom jednu repliku) a stane se nedostupnou.

Pomocí tohoto pozadí si probereme několik možných konfigurací clusteru:

**Jeden uzel**: Tato možnost neposkytuje vysokou dostupnost, protože ztráta jednoho uzlu z jakéhokoli důvodu znamená ztrátu celého clusteru.

**Dva uzly**: kvorum pro službu nasazené ve dvou uzlech (N = 2) je 2 (2/2 + 1 = 2). Když dojde ke ztrátě jedné repliky, není možné vytvořit kvorum. Vzhledem k tomu, že provedení upgradu služby vyžaduje dočasné provedení repliky, nejedná se o užitečnou konfiguraci.

**Tři uzly**: se třemi uzly (N = 3) je požadavek na vytvoření kvora stále dva uzly (3/2 + 1 = 2). To znamená, že můžete ztratit jednotlivý uzel a zachovat kvorum, ale při současném selhání dvou uzlů dojde ke ztrátě kvora a způsobí to, že cluster přestane být dostupný.

**Čtyři uzly**: se čtyřmi uzly (N = 4) je požadavek na vytvoření kvora tři uzly (4/2 + 1 = 3). To znamená, že můžete ztratit jednotlivý uzel a zachovat kvorum, ale při současném selhání dvou uzlů dojde ke ztrátě kvora a způsobí to, že cluster přestane být dostupný.

**Pět uzlů**: s pěti uzly (N = 5) je požadavek na vytvoření kvora stále tři uzly (5/2 + 1 = 3). To znamená, že můžete přijít o dva uzly současně a zároveň zachovat kvorum pro systémové služby.

U produkčních úloh musíte být odolné vůči současnému selhání minimálně dvou uzlů (například z důvodu upgradu clusteru, z jednoho důvodu z jiných důvodů), takže je potřeba pět uzlů.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Můžu svůj cluster vypnout v noci nebo víkendech, abyste ušetřili náklady?

Obecně to možné není. Service Fabric ukládá stav na místních, dočasných discích, což znamená, že pokud se virtuální počítač přesune na jiného hostitele, data se s ním nepřesunou. Nejedná se o problém, protože nový uzel je v normálním provozu v aktuálním stavu podle jiných uzlů. Pokud ale zastavíte všechny uzly a později je restartujete, je důležité mít možnost, že většina uzlů začíná na nových hostitelích a systém nemůže obnovit.

Pokud chcete vytvořit clustery pro testování aplikace před jejím nasazením, doporučujeme tyto clustery dynamicky vytvářet jako součást [kanálu průběžné integrace nebo průběžného nasazování](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Návody upgradovat operační systém (například ze systému Windows Server 2012 na systém Windows Server 2016)?

V současné době pracujeme na vylepšeném prostředí, ale zodpovídáte za upgrade. Bitovou kopii operačního systému musíte upgradovat na virtuálních počítačích clusteru na jednom virtuálním počítači v daném okamžiku. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Můžu šifrovat připojené datové disky v typu uzlu clusteru (sada škálování virtuálního počítače)?
Ano.  Další informace najdete v tématu [Vytvoření clusteru s připojenými datovými disky](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) a [Azure Disk Encryption pro Virtual Machine Scale Sets](../virtual-machine-scale-sets/disk-encryption-overview.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Je možné použít virtuální počítače s nízkou prioritou v typu uzlu clusteru (sada škálování virtuálního počítače)?
No. Virtuální počítače s nízkou prioritou se nepodporují. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Jaké jsou adresáře a procesy, které je potřeba vyloučit při spuštění antivirového programu v mém clusteru?

| **Vyloučené adresáře antivirové ochrany** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (z konfigurace clusteru) |
| FabricLogRoot (z konfigurace clusteru) |

| **Vyloučené procesy antivirové ochrany** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-key-vault-to-get-secrets"></a>Jak se má aplikace ověřit, aby Key Vaulta získání tajných kódů?
Níže jsou uvedené možnosti pro vaši aplikaci k získání přihlašovacích údajů pro ověřování Key Vault:

A. Během sestavování/balení vaší aplikace můžete načíst certifikát do balíčku dat aplikace SF a použít ho k ověření Key Vault.
B. Pro hostitele s podporou MSI sady škálování pro virtuální počítače můžete vyvinout jednoduché prostředí PowerShell SetupEntryPoint pro vaši aplikaci SF a získat [přístupový token z koncového bodu MSI](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)a pak [načíst tajné kódy z Key Vault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Návrh aplikace

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Jaký je nejlepší způsob, jak zadávat dotazy na data napříč oddíly spolehlivé kolekce?

Spolehlivé kolekce jsou obvykle [rozdělené](service-fabric-concepts-partitioning.md) tak, aby umožňovaly vyšší výkon a propustnost při škálování. To znamená, že stav dané služby může být rozložený mezi desítky nebo stovky počítačů. K provedení operací s touto úplnou datovou sadou máte několik možností:

- Vytvořte službu, která se dotazuje na všechny oddíly jiné služby, aby se vyžádala z požadovaných dat.
- Vytvořte službu, která může přijímat data ze všech oddílů jiné služby.
- Pravidelně doručovat data z každé služby do externího úložiště. Tento přístup je vhodný jenom v případě, že dotazy, které provádíte, nejsou součástí vaší základní obchodní logiky, protože data externího obchodu budou zastaralá.
- Případně můžete ukládat data, která musí podporovat dotazování napříč všemi záznamy přímo v úložišti dat, a ne ve spolehlivé kolekci. Tím se eliminují problémy se zastaralými daty, ale neumožňují využití výhod spolehlivých kolekcí.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Jaký je nejlepší způsob, jak zadávat dotazy na data napříč objekty actor?

Objekty actor jsou navržené tak, aby byly nezávislými jednotkami stavu a COMPUTE, takže nedoporučujeme provádět v době běhu široké dotazy stavu objektu actor. Pokud budete potřebovat dotaz na celou sadu stavů objektu actor, měli byste zvážit jednu z těchto akcí:

- Výměna služeb objektu actor se stavem Reliable Services tak, aby počet síťových požadavků pro shromáždění všech dat z počtu objektů actor na počet oddílů ve vaší službě.
- Navrhujte své aktéry, aby pravidelně nabízely svůj stav do externího úložiště pro snazší dotazování. Jak je uvedeno výše, tento přístup je možné realizovat pouze v případě, že dotazy, které provádíte, nejsou požadovány pro vaše běhové chování.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Kolik dat je možné uložit do spolehlivé kolekce?

Spolehlivé služby se obvykle dělí na oddíly, takže velikost, kterou můžete ukládat, je omezená jenom počtem počítačů, které máte v clusteru, a množství paměti dostupné na těchto počítačích.

Předpokládejme například, že máte v rámci služby spolehlivou kolekci s 100 oddíly a 3 replikami a ukládáte objekty, které mají průměrnou velikost 1 KB. Nyní předpokládejme, že máte cluster s 10 počítači s 16gb paměti na jeden počítač. V zájmu jednoduchosti a přístupnosti je vhodné předpokládat, že operační systém a systémové služby, Service Fabric runtime a vaše služby, budou spotřebovávat 6 GB, což zabere 10 GB na jeden počítač nebo 100 GB pro cluster.

Mějte na paměti, že každý objekt musí být uložen třikrát (jedna primární a druhá replika), měli byste mít dostatek paměti pro přibližně 35 000 000 objektů v kolekci, pokud pracujete s plnou kapacitou. Doporučujeme však, abyste nepřišli o souběžnou ztrátu domény selhání a upgradovací domény, která představuje přibližně 1/3 kapacity a snížila číslo přibližně na 23 000 000.

Tento výpočet také předpokládá:

- Rozdělení dat mezi oddíly je zhruba rovnoměrné nebo že se metriky zatížení hlásí do clusteru Správce prostředků. Ve výchozím nastavení Service Fabric načítá rovnováhu na základě počtu replik. V předchozím příkladu by se do každého uzlu v clusteru vložily 10 primárních replik a 20 sekundárních replik. To funguje dobře pro zatížení, které je rovnoměrně distribuováno napříč oddíly. Pokud zatížení ještě není, musíte vykázat zatížení, aby Správce prostředků mohl zabalit menší repliky společně a umožnit větším replikám využívat více paměti na jednotlivých uzlech.

- Tato spolehlivá služba je jediným jediným stavem ukládání v clusteru. Vzhledem k tomu, že je možné nasadit více služeb do clusteru, je nutné mít na vědomí prostředky, které musí každý z nich spouštět a spravovat jejich stav.

- Samotný cluster se nezvětšuje ani nezmenšuje. Pokud přidáte více počítačů, Service Fabric bude znovu vyrovnávat vaše repliky, aby využila další kapacitu, až počet počítačů překročí počet oddílů ve vaší službě, protože jednotlivé repliky nemohou rozbírat počítače. Naopak pokud zmenšíte velikost clusteru odebráním počítačů, repliky se zabalí těsněji a mají méně celkovou kapacitu.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Kolik dat je možné uložit v objektu actor?

Stejně jako u spolehlivých služeb je množství dat, které můžete ukládat ve službě objektu actor, omezené jenom z celkového místa na disku a paměti dostupné napříč uzly v clusteru. Jednotlivé objekty actor jsou však nejefektivnější, pokud jsou použity k zapouzdření malého množství stavu a přidružené obchodní logiky. V rámci obecného pravidla by měl mít jednotlivec actor stav, který se měří v kilobajtech.


### <a name="where-does-azure-service-fabric-resource-provider-store-customer-data"></a>Kam poskytovatel prostředků Azure Service Fabric ukládá zákaznická data?

Poskytovatel prostředků Azure Service Fabric nepřesouvá ani neukládá zákaznická data mimo oblast, ve které je nasazená.


## <a name="other-questions"></a>Další dotazy

### <a name="how-does-service-fabric-relate-to-containers"></a>Jak se Service Fabric vztahují k kontejnerům?

Kontejnery nabízejí jednoduchý způsob, jak zabalit služby a jejich závislosti tak, aby běžely konzistentně ve všech prostředích a mohly fungovat izolovaným způsobem na jednom počítači. Service Fabric nabízí způsob, jak nasadit a spravovat služby, včetně [služeb, které byly zabaleny do kontejneru](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Plánujete otevření Service Fabric zdrojového kódu?

Máme Open Source součásti Service Fabric ([Reliable Services Framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [Reliable actors](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.NET Core Integration](https://github.com/Azure/service-fabric-aspnetcore)Library, [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)a [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)) na GitHubu a přijměte příspěvky komunity na tyto projekty. 

[Nedávno jsme oznámili](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) , že plánujeme otevřít zdroj Service Fabric runtime. V tuto chvíli máme [Service Fabric](https://github.com/Microsoft/service-fabric/) nakládat na GitHubu pomocí nástrojů pro sestavení a testování pro Linux, což znamená, že můžete klonovat úložiště, sestavovat Service Fabric pro Linux, spouštět základní testy, otevírat problémy a odesílat žádosti o přijetí změn. Těžko pracujeme na tom, aby se prostředí Windows buildu migrovali i v rámci kompletního prostředí CI.

Další podrobnosti najdete na [blogu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) .

## <a name="next-steps"></a>Další kroky

Další informace o [základních Service Fabric konceptech](service-fabric-technical-overview.md) a [osvědčených postupech](./service-fabric-best-practices-security.md)