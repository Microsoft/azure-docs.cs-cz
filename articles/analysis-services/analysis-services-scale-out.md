---
title: Azure Analysis Services škálování na více instancí | Microsoft Docs
description: Replikace Azure Analysis Services serverů s možností horizontálního navýšení kapacity Dotazy klientů je pak možné distribuovat mezi několik replik dotazů ve fondu dotazů se škálováním na více instancí.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2385cb811e322bd44daefa03d821b2ae47e0652
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769243"
---
# <a name="azure-analysis-services-scale-out"></a>Škálování služby Azure Analysis Services na více instancí

Díky škálování na více instancí je možné klientské dotazy distribuovat mezi několik *replik dotazů* ve *fondu dotazů* a zkrátit tak dobu odezvy během úloh s vysokým počtem dotazů. Můžete také oddělit zpracování z fondu dotazů a zajistit, aby dotazy klienta nepříznivě ovlivnily operace zpracování. Horizontální navýšení kapacity lze nakonfigurovat v Azure Portal nebo pomocí REST API Analysis Services.

Horizontální navýšení kapacity je k dispozici pro servery v cenové úrovni Standard. Každá replika dotazu se účtuje stejnou sazbou jako váš server. Všechny repliky dotazů se vytvoří ve stejné oblasti jako váš server. Počet replik dotazů, které můžete nakonfigurovat, je omezený oblastí, ve které je váš server. Další informace najdete v tématu [dostupnost podle oblasti](analysis-services-overview.md#availability-by-region). Horizontální navýšení kapacity nezvyšuje velikost dostupné paměti pro váš server. Chcete-li zvýšit velikost paměti, je třeba upgradovat svůj plán. 

## <a name="why-scale-out"></a>Proč horizontální navýšení kapacity?

V typickém nasazení serveru slouží jeden server jako server pro zpracování i pro dotazování. Pokud počet dotazů klientů u modelů na serveru překročí jednotky pro zpracování dotazů (QPU) pro plán vašeho serveru nebo ke zpracování modelu dojde současně s vysokými úlohami dotazování, může dojít ke snížení výkonu. 

Díky škálování na více instancí můžete vytvořit fond dotazů s až sedmi dalšími prostředky repliky dotazů (celkem osm, včetně vašeho *primárního* serveru). Počet replik ve fondu dotazů můžete škálovat tak, aby splňoval požadavky QPU v kritickém čase, a můžete kdykoli oddělit výpočetní Server od fondu dotazů. 

Bez ohledu na počet replik dotazů, které máte ve fondu dotazů, se úlohy zpracování nedistribuují mezi replikami dotazů. Primární server slouží jako server pro zpracování. Repliky dotazů slouží pouze k vytváření dotazů na databáze modelů synchronizovaných mezi primárním serverem a každou replikou ve fondu dotazů. 

Při horizontálním navýšení kapacity může trvat až pět minut, než se nové repliky dotazu postupně přidají do fondu dotazů. Když jsou všechny nové repliky dotazů v provozu a jsou spuštěné nové připojení klientů, vyrovnávají se zatížení mezi prostředky ve fondu dotazů. Existující připojení klientů se nemění z prostředku, ke kterému jsou aktuálně připojeni. Při škálování se ukončí všechna existující připojení klientů k prostředku fondu dotazů, který se odebírá z fondu dotazů. Klienti se mohou znovu připojit k zbývajícímu prostředku fondu dotazů.

## <a name="how-it-works"></a>Jak to funguje

Při první konfiguraci horizontálního navýšení kapacity se databáze modelů na primárním serveru *automaticky* synchronizují s novými replikami v novém fondu dotazů. Automatická synchronizace probíhá pouze jednou. Během automatické synchronizace se datové soubory primárního serveru (šifrované v klidovém úložišti objektů BLOB) zkopírují do druhého umístění, které se taky zašifrují v klidovém úložišti objektů BLOB. Repliky ve fondu dotazů jsou následně *vysušeny* daty z druhé sady souborů. 

Automatická synchronizace se provádí jenom při prvním horizontálním navýšení kapacity serveru. můžete také provést ruční synchronizaci. Při synchronizaci se zajišťují, že se data replik ve fondu dotazů shodují s primárním serverem. Při zpracování (obnovení) modelů na primárním serveru musí být provedena synchronizace *po* dokončení operací zpracování. Tato synchronizace kopíruje aktualizovaná data ze souborů primárního serveru v úložišti objektů blob do druhé sady souborů. Repliky ve fondu dotazů se pak procházejí aktualizovanými daty z druhé sady souborů v úložišti objektů BLOB. 

Při provádění následných operací škálování, například zvýšení počtu replik ve fondu dotazů od dvou na pět, jsou nové repliky vysušeny daty z druhé sady souborů v úložišti objektů BLOB. Není k dispozici žádná synchronizace. Pokud po horizontálním navýšení kapacity provádíte synchronizaci, nové repliky ve fondu dotazů by byly vysušeny dvakrát – redundantním vysazováním. Při provádění následných operací škálování je důležité mít na paměti:

* *Před operací horizontálního* navýšení kapacity proveďte synchronizaci, aby nedocházelo k redundantnímu vysazování přidaných replik. Souběžná synchronizace a běžící operace škálování na více instancí se nepovolují ve stejnou dobu.

* Při automatizaci operací zpracování *i* škálování je důležité nejprve zpracovat data na primárním serveru, pak provést synchronizaci a pak provést operaci škálování na více instancí. Tato sekvence zaručuje minimální dopad na QPU a paměťové prostředky.

* Během operací škálování na více instancí jsou všechny servery ve fondu dotazů, včetně primárního serveru, dočasně offline.

* Synchronizace je povolená i v případě, že ve fondu dotazů nejsou žádné repliky. Pokud provádíte horizontální navýšení kapacity z nuly na jednu nebo více replik s novými daty z operace zpracování na primárním serveru, proveďte nejprve synchronizaci bez replik ve fondu dotazů a pak navýšení kapacity. Synchronizace před změnou kapacity zabrání redundantnímu vysazování nově přidaných replik.

* Při odstraňování modelu databáze z primárního serveru se automaticky neodstraní z replik ve fondu dotazů. Operaci synchronizace musíte provést pomocí příkazu [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) prostředí PowerShell, který odebere soubory pro tuto databázi z umístění sdíleného úložiště objektů BLOB repliky a pak odstraní databázi modelů v replikách ve fondu dotazů. Chcete-li zjistit, zda databáze modelů existuje ve replikách ve fondu dotazů, ale ne na primárním serveru, zajistěte, aby byl **samostatný server pro zpracování dotazů od fondu** dotazů nastaven na **hodnotu Ano**. Pak pomocí SSMS se připojte k primárnímu serveru pomocí `:rw` kvalifikátoru a zjistěte, jestli databáze existuje. Pak se připojte k replikám ve fondu dotazů, a to tak, že se připojíte bez `:rw` kvalifikátoru a zjistíte, jestli existuje i stejná databáze. Pokud databáze existuje na replikách ve fondu dotazů, ale ne na primárním serveru, spusťte operaci synchronizace.   

* Při přejmenování databáze na primárním serveru je potřeba další krok, který zajistí, že je databáze správně synchronizovaná na všechny repliky. Po přejmenování proveďte synchronizaci pomocí příkazu [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) , který určuje `-Database` parametr se starým názvem databáze. Tato synchronizace odebere ze všech replik databázi a soubory se starým názvem. Pak proveďte další synchronizaci, která určuje `-Database` parametr s novým názvem databáze. Druhá synchronizace zkopíruje nově pojmenovanou databázi do druhé sady souborů a napředá všechny repliky. Tyto synchronizace nelze provést pomocí příkazu synchronizovat model na portálu.

### <a name="synchronization-mode"></a>Režim synchronizace

Ve výchozím nastavení jsou repliky dotazů v plném rozsahu dehydratované, nikoli přírůstkově. K dehydrataci dochází ve fázích. Jsou odpojeny a připojeny dvakrát (za předpokladu, že jsou k dispozici alespoň tři repliky), aby bylo zajištěno, že nejméně jedna replika bude pro dotazy v daném okamžiku udržována online. V některých případech se může stát, že se klienti budou muset při provádění tohoto procesu znovu připojit k jedné z online replik. Když použijete nastavení **ReplicaSyncMode** , můžete teď zadat synchronizaci repliky dotazů paralelně. Paralelní synchronizace přináší následující výhody: 

- Významné snížení doby synchronizace. 
- Data napříč replikami jsou pravděpodobně během procesu synchronizace konzistentní. 
- Vzhledem k tomu, že databáze jsou v průběhu procesu synchronizace online, je nutné, aby se klienti znovu nepřipojovali. 
- Mezipaměť v paměti je přírůstkově aktualizována pouze změněnými daty, což může být rychlejší než plně prokládání modelu. 

#### <a name="setting-replicasyncmode"></a>Nastavení ReplicaSyncMode

Pomocí SSMS můžete nastavit ReplicaSyncMode v upřesňujících vlastnostech. Možné hodnoty jsou: 

- `1` (výchozí): úplné vysazování databáze ve fázích (přírůstkově). 
- `2`: Optimalizovaná synchronizace paralelně. 

![Nastavení RelicaSyncMode](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

Při nastavení **ReplicaSyncMode = 2** v závislosti na tom, kolik mezipaměti je potřeba aktualizovat, můžou repliky dotazů spotřebovat další paměť. Aby databáze zůstala online a dostupná pro dotazy v závislosti na tom, kolik dat se změnilo, může operace vyžadovat až *dvojnásobek paměti* v replice, protože staré i nové segmenty jsou v paměti současně zachované. Uzly repliky mají stejné přidělení paměti jako primární uzel a v primárním uzlu je obvykle k dispozici další paměť pro operace obnovení, takže může být nepravděpodobné, že by repliky vyčerpaly paměť. Běžným scénářem je navíc přírůstkově aktualizovaný databáze na primárním uzlu, a proto by požadavek na zdvojnásobení paměti měl být neobvyklý. Pokud při operaci synchronizace dojde k chybě při nedostatku paměti, pokusí se znovu použít výchozí techniku (připojit nebo odpojit dvakrát). 

### <a name="separate-processing-from-query-pool"></a>Samostatné zpracování z fondu dotazů

Pro maximální výkon operací zpracování a dotazování se můžete rozhodnout oddělit svůj server pro zpracování od fondu dotazů. Když se oddělí, přiřadí se nová připojení klientů k replikám dotazů ve fondu dotazů. Pokud se zpracování operací zahájí jenom krátkou dobu, můžete zvolit oddělení zpracovatelského serveru od fondu dotazů jenom po dobu potřebnou k provedení operací zpracování a synchronizace a pak ho zahrnout zpátky do fondu dotazů. Při oddělení zpracovatelského serveru z fondu dotazů nebo jeho přidání zpět do fondu dotazů může trvat až pět minut, než se operace dokončí.

## <a name="monitor-qpu-usage"></a>Monitorování využití QPU

Pokud chcete zjistit, jestli je na serveru potřeba horizontální navýšení kapacity, [monitorujte svůj server](analysis-services-monitor.md) v Azure Portal pomocí metrik. Pokud se vaše QPU pravidelně navyšuje, znamená to, že počet dotazů na vaše modely přesahuje limit QPU pro váš plán. Metrika délky fronty úloh fondu dotazů se zvyšuje také v případě, že počet dotazů ve frontě fondu vláken dotazu překročí dostupné QPU. 

Další užitečnou metrikou ke sledování je průměrná QPUa podle ServerResourceType. Tato metrika porovnává průměrnou QPU primárního serveru s fondem dotazů. 

![Metriky horizontálního navýšení kapacity dotazu](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**Konfigurace QPU pomocí ServerResourceType**

1. V spojnicovém grafu metriky klikněte na **Přidat metriku**. 
2. V **prostředku** vyberte váš server, potom v **oboru názvů metriky** vyberte **Analysis Services standardní metriky**, potom v **Metrikě** vyberte **QPU** a potom v **agregaci** vyberte **AVG**. 
3. Klikněte na **použít rozdělení**. 
4. V **hodnoty** vyberte **ServerResourceType**.  

### <a name="detailed-diagnostic-logging"></a>Podrobné protokolování diagnostiky

Podrobnější diagnostiku prostředků serveru s horizontálním navýšení kapacity použijte Azure Monitor protokoly. Pomocí protokolů můžete pomocí Log Analyticsch dotazů přerušit QPU a paměť podle serveru a repliky. Další informace najdete v tématu Příklady dotazů v [protokolování diagnostiky Analysis Services](analysis-services-logging.md#example-queries).


## <a name="configure-scale-out"></a>Konfigurace škálování na více instancí

### <a name="in-azure-portal"></a>V Azure Portal

1. Na portálu klikněte na horizontální navýšení **kapacity**. Pomocí posuvníku vyberte počet serverů repliky dotazů. Počet replik, které jste si zvolili, je navíc k vašemu stávajícímu serveru.  

2. V **samostatném serveru pro zpracování z fondu dotazování** vyberte možnost Ano, pokud chcete server pro zpracování vyloučit ze serverů dotazů. [Připojení](#connections) klienta pomocí výchozího připojovacího řetězce (bez `:rw` ) se přesměrují na repliky ve fondu dotazů. 

   ![Posuvník horizontálního navýšení kapacity](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kliknutím na **Uložit** zřídíte nové servery repliky dotazů. 

Při prvním nastavování horizontálního škálování serveru se modely na primárním serveru automaticky synchronizují s replikami ve fondu dotazů. Automatická synchronizace nastane jenom jednou, když nakonfigurujete horizontální navýšení kapacity na jednu nebo víc replik. Následné změny počtu replik na stejném serveru nebudou *aktivovat další automatickou synchronizaci*. Automatická synchronizace se znovu neprojeví, i když nastavíte server na nulové repliky a pak znovu nasadíte na libovolný počet replik. 

## <a name="synchronize"></a>Synchronizace 

Operace synchronizace se musí provádět ručně nebo pomocí REST API.

### <a name="in-azure-portal"></a>V Azure Portal

V **přehledu** > model > **synchronizovat model**.

![Ikona synchronizace](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>Rozhraní REST API

Použijte operaci **synchronizace** .

#### <a name="synchronize-a-model"></a>Synchronizace modelu   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Získat stav synchronizace  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Vrátit stavové kódy:


|Kód  |Description  |
|---------|---------|
|-1     |  Neplatný       |
|0     | Replikaci        |
|1     |  Dosazování dat       |
|2     |   Dokončeno       |
|3     |   Neúspěšný      |
|4     |    Dokončuje     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před použitím PowerShellu [nainstalujte nebo aktualizujte nejnovější modul Azure PowerShell](/powershell/azure/install-az-ps). 

Chcete-li spustit synchronizaci, použijte rutinu [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

K nastavení počtu replik dotazů použijte [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver). Zadejte volitelný `-ReadonlyReplicaCount` parametr.

K oddělení zpracovatelského serveru z fondu dotazů použijte [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver). Zadejte volitelný `-DefaultConnectionMode` parametr, který se má použít `Readonly` .

Další informace najdete v tématu [použití instančního objektu s modulem AZ. AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Připojení

Na stránce Přehled vašeho serveru jsou k dispozici dva názvy serverů. Pokud jste u serveru ještě nenakonfigurovali horizontální navýšení kapacity, názvy obou serverů fungují stejně. Jakmile nakonfigurujete horizontální navýšení kapacity pro server, je nutné zadat příslušný název serveru v závislosti na typu připojení. 

Pro klientská připojení koncových uživatelů, jako jsou Power BI Desktop, Excel a vlastní aplikace, použijte **název serveru**. 

V případě SSMS, sady Visual Studio a připojovacích řetězců v prostředí PowerShell, Azure Function Apps a AMO použijte **název serveru pro správu**. Název management server obsahuje speciální `:rw` kvalifikátor (pro čtení i zápis). Všechny operace zpracování se vyskytují na primárním management server.

![Názvy serverů](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Horizontální navýšení kapacity, horizontální navýšení kapacity vs. škálování na více instancí

Cenovou úroveň můžete na serveru změnit několika replikami. Stejná cenová úroveň se vztahuje na všechny repliky. Operace škálování nejprve odnese všechny repliky najednou a pak zobrazí všechny repliky v nové cenové úrovni.

## <a name="troubleshoot"></a>Řešení potíží

**Problém:** Při načítání došlo **\<Name of the server> k chybě. v režimu připojení se nepodařilo najít instanci serveru.**

**Řešení:** Při výběru **samostatného serveru pro zpracování z možnosti fond dotazování** se připojení klienta pomocí výchozího připojovacího řetězce (bez `:rw` ) přesměrují na repliky fondu dotazů. Pokud repliky ve fondu dotazů ještě nejsou online, protože synchronizace ještě není dokončená, přesměrovaná připojení klienta můžou selhat. Aby nedocházelo k neúspěšným připojením, musí být ve fondu dotazů při provádění synchronizace k dispozici alespoň dva servery. Každý server se synchronizuje jednotlivě, zatímco ostatní zůstávají online. Pokud se rozhodnete, že během zpracování nebude mít server pro zpracování ve fondu dotazů, můžete jej odebrat z fondu ke zpracování a pak jej přidat zpátky do fondu po dokončení zpracování, ale před synchronizací. K monitorování stavu synchronizace použijte metriky paměti a QPU.



## <a name="related-information"></a>Související informace

[Monitorování metrik serveru](analysis-services-monitor.md)   
[Správa Azure Analysis Services](analysis-services-manage.md)
