---
title: Horizontální navýšení kapacity služby Azure Analysis Services| Dokumenty společnosti Microsoft
description: Replikujte servery Služby Azure Analysis Services pomocí horizontálního navýšení kapacity. Dotazy klientů pak mohou být distribuovány mezi více replik dotazů ve fondu dotazů s horizontálním navýšením kapacity.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3ea304d038618fc428f20e7ad72b398f593d09a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247994"
---
# <a name="azure-analysis-services-scale-out"></a>Škálování služby Azure Analysis Services na více instancí

S horizontálnínavýšení kapacity klienta dotazy mohou být distribuovány mezi více *replik dotazů* ve *fondu dotazů*, zkrácení doby odezvy během úlohy vysoké dotazu. Můžete také oddělit zpracování z fondu dotazů, zajištění klientdotazy nejsou nepříznivě ovlivněny operace zpracování. Horizontální navýšení kapacity lze nakonfigurovat na webu Azure Portal nebo pomocí rozhraní API REST analysis services.

Horizontální navýšení kapacity je k dispozici pro servery v cenové úrovni Standard. Každá replika dotazu se účtuje stejnou rychlostí jako váš server. Všechny repliky dotazů jsou vytvořeny ve stejné oblasti jako server. Počet replik dotazů, které můžete nakonfigurovat, je omezen oblastí, ve které se server nachází. Další informace najdete v [tématu Dostupnost podle oblasti](analysis-services-overview.md#availability-by-region). Horizontální navýšení kapacity nezvyšuje množství dostupné paměti pro server. Chcete-li zvýšit paměť, je třeba upgradovat plán. 

## <a name="why-scale-out"></a>Proč se vynožit?

Při typickém nasazení serveru slouží jeden server jako server pro zpracování i jako dotazovací server. Pokud počet dotazů klientů na modely na serveru překročí jednotky zpracování dotazů (QPU) pro plán serveru nebo ke zpracování modelu dochází současně s vysokými úlohami dotazů, může se snížit výkon. 

S horizontálnínavýšení kapacity, můžete vytvořit fond dotazů s až sedm dalších prostředků repliky dotazu (osm celkem, včetně *primárního* serveru). Můžete škálovat počet replik ve fondu dotazů tak, aby splňovaly požadavky QPU v kritických časech a můžete kdykoli oddělit server zpracování z fondu dotazů. 

Bez ohledu na počet replik dotazů, které máte ve fondu dotazů, nejsou úlohy zpracování distribuovány mezi repliky dotazů. Primární server slouží jako server pro zpracování. Repliky dotazů slouží pouze k dotazům na databáze modelů synchronizované mezi primárním serverem a každou replikou ve fondu dotazů. 

Při horizontálním navýšení kapacity může trvat až pět minut, než se do fondu dotazů postupně přidají nové repliky dotazů. Když jsou všechny nové repliky dotazů v provozu, nová klientská připojení jsou vyrovnávána zatížením mezi prostředky ve fondu dotazů. Existující klientská připojení se nezmění z prostředku, ke které jsou aktuálně připojena. Při škálování v, všechny existující připojení klienta k prostředku fondu dotazů, který je odebírán z fondu dotazů jsou ukončeny. Klienti se mohou znovu připojit k prostředku fondu zbývajících dotazů.

## <a name="how-it-works"></a>Jak to funguje

Při první konfiguraci horizontálního navýšení kapacity jsou databáze modelů na primárním serveru *automaticky* synchronizovány s novými replikami v novém fondu dotazů. Automatická synchronizace probíhá pouze jednou. Během automatické synchronizace se datové soubory primárního serveru (zašifrované v úložišti objektů blob) zkopírují do druhého umístění, které je také zašifrováno v klidovém stavu v úložišti objektů blob. Repliky ve fondu dotazů jsou pak *hydratovány daty* z druhé sady souborů. 

Zatímco automatická synchronizace se provádí pouze při horizontálnínavýšení kapacity serveru poprvé, můžete také provést ruční synchronizaci. Synchronizace zajišťuje, že data na replikách ve fondu dotazů se shodují s daty primárního serveru. Při zpracování (aktualizovat) modely na primárním serveru, synchronizace musí být provedena *po* dokončení operací zpracování. Tato synchronizace zkopíruje aktualizovaná data ze souborů primárního serveru v úložišti objektů blob do druhé sady souborů. Repliky ve fondu dotazů jsou pak hydratovány s aktualizovanými daty z druhé sady souborů v úložišti objektů blob. 

Při provádění následné operace horizontálnínavýšení kapacity, například zvýšení počtu replik ve fondu dotazů ze dvou na pět, nové repliky jsou hydratovány s daty z druhé sady souborů v úložišti objektů blob. Neexistuje žádná synchronizace. Pokud potom provedete synchronizaci po horizontálnínavýšení kapacity, nové repliky ve fondu dotazů by být hydratovány dvakrát - redundantní hydratace. Při provádění následné operace horizontálního navýšení kapacity je důležité mít na paměti:

* Proveďte synchronizaci *před operací horizontálnínavýšení kapacity,* abyste se vyhnuli redundantní hydrataci přidaných replik. Souběžná synchronizace a operace horizontálního navýšení kapacity spuštěné současně nejsou povoleny.

* Při automatizaci operací zpracování *i* horizontálního navýšení kapacity je důležité nejprve zpracovat data na primárním serveru, potom provést synchronizaci a potom provést operaci horizontálního navýšení kapacity. Tato sekvence zajišťuje minimální dopad na QPU a paměťové prostředky.

* Synchronizace je povolena i v případě, že ve fondu dotazů nejsou žádné repliky. Pokud horizontální navýšení kapacity z nuly na jednu nebo více replik s novými daty z operace zpracování na primárním serveru, proveďte synchronizaci nejprve bez replik ve fondu dotazů a potom horizontální navýšení kapacity. Synchronizace před horizontálním navýšením kapacity zabraňuje redundantní hydrataci nově přidaných replik.

* Při odstraňování databáze modelu z primárního serveru se automaticky neodstraní z replik ve fondu dotazů. Operaci synchronizace je nutné provést pomocí příkazu [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell, který odebere soubor/soubory pro tuto databázi ze sdíleného umístění úložiště objektů blob repliky a odstraní databázi modelů replik ve fondu dotazů. Chcete-li zjistit, zda existuje databáze modelu na replikách ve fondu dotazů, ale ne na primárním serveru, ujistěte se, že **nastavení Oddělit zpracování serveru od dotazování je** nastavení fondu **dotazů**na Ano . Potom použijte SSMS pro připojení k `:rw` primárnímu serveru pomocí kvalifikátoru, abyste zjistili, zda databáze existuje. Pak se připojte k replikám ve fondu `:rw` dotazů připojením bez kvalifikátoru a zjistěte, zda existuje také stejná databáze. Pokud databáze existuje na replikách ve fondu dotazů, ale ne na primárním serveru, spusťte operaci synchronizace.   

* Při přejmenování databáze na primárním serveru je k dispozici další krok, který je nutný k zajištění správné synchronizace databáze s replikami. Po přejmenování proveďte synchronizaci pomocí příkazu [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) určujícím `-Database` parametr se starým názvem databáze. Tato synchronizace odebere databázi a soubory se starým názvem ze všech replik. Potom proveďte další synchronizaci určující `-Database` parametr s novým názvem databáze. Druhá synchronizace zkopíruje nově pojmenovanou databázi do druhé sady souborů a hydratuje všechny repliky. Tyto synchronizace nelze provést pomocí příkazu Synchronizovat model na portálu.

### <a name="synchronization-mode"></a>Režim synchronizace

Ve výchozím nastavení jsou repliky dotazů rehydratovány v plném rozsahu, nikoli postupně. Rehydratace probíhá postupně. Jsou odpojeny a připojeny dva najednou (za předpokladu, že existují alespoň tři repliky) zajistit alespoň jednu repliku je udržována online pro dotazy v daném okamžiku. V některých případech může být nutné, aby se klienti znovu připojili k jedné z replik online během tohoto procesu. Pomocí nastavení **Replika SyncMode** (ve verzi Preview) můžete nyní zadat paralelní synchronizaci replik dotazu. Paralelní synchronizace poskytuje následující výhody: 

- Významné zkrácení času synchronizace. 
- Data mezi replikami jsou pravděpodobně konzistentní během procesu synchronizace. 
- Vzhledem k tomu, že databáze jsou udržovány online na všech replikách v průběhu procesu synchronizace, klienti nemusí znovu připojit. 
- Mezipaměť v paměti je aktualizována postupně pouze se změněnými daty, která mohou být rychlejší než úplné opětovné hydratace modelu. 

#### <a name="setting-replicasyncmode"></a>Nastavení režimu Replikasyncmode

Pomocí ssms nastavit ReplicaSyncMode v rozšířené vlastnosti. Možné hodnoty jsou: 

- `1`(výchozí): Rehydratace databáze úplné repliky ve fázích (přírůstkové). 
- `2`: Optimalizovaná synchronizace paralelně. 

![RelicaSyncMode, nastavení](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

Při nastavování **ReplicaSyncMode=2**, v závislosti na tom, kolik mezipaměti je třeba aktualizovat, může být repliky dotazu spotřebovány další paměti. Chcete-li zachovat databázi online a k dispozici pro dotazy, v závislosti na tom, jak velká část dat se změnila, operace může vyžadovat až *zdvojnásobení paměti* v replice, protože staré i nové segmenty jsou uloženy v paměti současně. Uzly replik mají stejné přidělení paměti jako primární uzel a obvykle je další paměť na primární uzel pro operace aktualizace, takže je nepravděpodobné, že repliky by nedostatek paměti. Kromě toho běžný scénář je, že databáze je postupně aktualizován na primární uzel, a proto požadavek na dvojité paměti by měla být neobvyklé. Pokud operace synchronizace dojde k chybě nedostatku paměti, bude opakovat pomocí výchozí techniky (připojit/odpojit dva najednou). 

### <a name="separate-processing-from-query-pool"></a>Samostatné zpracování z fondu dotazů

Pro maximální výkon pro zpracování i operace dotazu můžete oddělit server zpracování od fondu dotazů. Při oddělení jsou nová klientská připojení přiřazena pouze replikám dotazů ve fondu dotazů. Pokud operace zpracování zabírají pouze krátkou dobu, můžete oddělit server zpracování od fondu dotazů pouze po dobu, kterou trvá provedení operací zpracování a synchronizace, a pak jej zahrnout zpět do fondu dotazů. Při oddělení serveru zpracování od fondu dotazů nebo jeho přidání zpět do fondu dotazů může trvat až pět minut pro dokončení operace.

## <a name="monitor-qpu-usage"></a>Sledování využití QPU

Chcete-li zjistit, zda je nutné horizontální navýšení kapacity pro váš server, [sledujte server](analysis-services-monitor.md) na webu Azure Portal pomocí metrik. Pokud vaše QPU pravidelně maxes ven, to znamená, že počet dotazů proti vašim modelům je vyšší limit QPU pro váš plán. Metrika délky fronty úlohfondu dotazů se také zvyšuje, když počet dotazů ve frontě fondu vláken dotazu překročí dostupné QPU. 

Další dobrou metrikou ke sledování je průměrná QPU podle ServerResourceType. Tato metrika porovnává průměrné QPU pro primární server s fondem dotazů. 

![Metriky horizontálního navýšení kapacity dotazu](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**Konfigurace QPU podle typu ServerResourceType**

1. V spojnicovém grafu Metriky klikněte na **Přidat metriku**. 
2. V **aplikaci RESOURCE**vyberte server a pak v **metrickém oboru NAMESPACE**vyberte **standardní metriky služby Analysis Services**, pak v **metrickách**, vyberte **QPU**a potom v **agregaci**možnost **Avg**. 
3. Klepněte na **tlačítko Použít rozdělení**. 
4. V **posuzovacím okně HODNOTY**vyberte **položku ServerResourceType**.  

### <a name="detailed-diagnostic-logging"></a>Podrobné diagnostické protokolování

Protokoly monitorování Azure slouží k podrobnější diagnostice škálovaných prostředků serveru. Pomocí protokolů můžete pomocí dotazů Log Analytics rozdělit QPU a paměť podle serveru a repliky. Další informace naleznete v ukázkových dotazech v [protokolování diagnostiky služby Analysis Services](analysis-services-logging.md#example-queries).


## <a name="configure-scale-out"></a>Konfigurace škálování na více instancí

### <a name="in-azure-portal"></a>Na portálu Azure

1. Na portálu klikněte na **horizontální navýšení kapacity**. Pomocí posuvníku vyberte počet serverů replik dotazů. Počet replik, které zvolíte, je navíc k existujícímu serveru.  

2. V **části Oddělit server pro zpracování od fondu dotazů**vyberte možnost ANO, chcete-li vyloučit server pro zpracování z dotazovacích serverů. Připojení [connections](#connections) klientů pomocí výchozího `:rw`připojovacího řetězce (bez) jsou přesměrována na repliky ve fondu dotazů. 

   ![Jezdec horizontálního navýšení kapacity](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Chcete-li zřídit nové servery replik dotazů, klepněte na tlačítko **Uložit.** 

Při první konfiguraci horizontálního navýšení kapacity pro server jsou modely na primárním serveru automaticky synchronizovány s replikami ve fondu dotazů. K automatické synchronizaci dochází pouze jednou při první konfiguraci horizontálního navýšení kapacity na jednu nebo více replik. Následné změny počtu replik na stejném serveru *nespustí další automatickou synchronizaci*. Automatická synchronizace se znovu neuskuteční ani v případě, že server nastavíte na nulové repliky a znovu horizontální navýšení kapacity na libovolný počet replik. 

## <a name="synchronize"></a>Synchronizace 

Synchronizační operace musí být provedeny ručně nebo pomocí rozhraní REST API.

### <a name="in-azure-portal"></a>Na portálu Azure

V **přehledu** > modelu > **synchronizačnímodel**.

![Jezdec horizontálního navýšení kapacity](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

Použijte operaci **synchronizace.**

#### <a name="synchronize-a-model"></a>Synchronizace modelu   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Získání stavu synchronizace  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Stavové kódy pro vrácení:


|kód  |Popis  |
|---------|---------|
|-1     |  Neplatný       |
|0     | Replikace        |
|1     |  Rehydratace       |
|2     |   Dokončeno       |
|3     |   Failed      |
|4     |    Dokončení     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před použitím [PowerShellu nainstalujte nebo aktualizujte nejnovější modul Azure PowerShellu](/powershell/azure/install-az-ps). 

Chcete-li spustit synchronizaci, použijte [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Chcete-li nastavit počet replik dotazů, použijte [set-azAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Zadejte `-ReadonlyReplicaCount` volitelný parametr.

Chcete-li oddělit server pro zpracování od fondu dotazů, použijte [set-azAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Zadejte `-DefaultConnectionMode` volitelný parametr, který se má použít `Readonly`.

Další informace naleznete [v tématu Použití instančního objektu pomocí modulu Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Připojení

Na stránce Přehled serveru jsou dva názvy serverů. Pokud jste ještě nenakonfigurovali horizontální navýšení kapacity pro server, oba názvy serverů fungují stejně. Po konfiguraci horizontálního navýšení kapacity pro server je třeba zadat příslušný název serveru v závislosti na typu připojení. 

Pro připojení klientů koncových uživatelů, jako je Power BI Desktop, Excel a vlastní aplikace, použijte **název serveru**. 

Pro SSMS, Visual Studio a připojovací řetězce v PowerShellu, aplikacích Azure Function a AMO použijte **název serveru pro správu**. Název serveru pro správu obsahuje speciální `:rw` kvalifikátor (čtení a zápis). Všechny operace zpracování probíhají na (primární) serveru pro správu.

![Názvy serverů](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Škálování nahoru, škálování dolů vs. horizontální navýšení kapacity

Cenovou úroveň můžete změnit na serveru s více replikami. Stejná cenová úroveň platí pro všechny repliky. Operace škálování nejprve znese všechny repliky najednou a pak zobrazí všechny repliky na nové cenové úrovni.

## <a name="troubleshoot"></a>Řešení potíží

**Problém:** Chyba uživatele **Nelze najít\<server ' Název instance server> ' v režimu připojení "ReadOnly".**

**Řešení:** Při výběru **možnosti Oddělit server pro zpracování od** fondu dotazů jsou `:rw`připojení klientů používající výchozí připojovací řetězec (bez) přesměrována na repliky fondu dotazů. Pokud repliky ve fondu dotazů ještě nejsou online, protože synchronizace ještě nebyla dokončena, může přesměrovaná připojení klientů selhat. Chcete-li zabránit selhání připojení, musí být alespoň dva servery ve fondu dotazů při provádění synchronizace. Každý server je synchronizován jednotlivě, zatímco ostatní zůstávají online. Pokud se rozhodnete nemít server pro zpracování ve fondu dotazů během zpracování, můžete jej odebrat z fondu pro zpracování a poté jej přidat zpět do fondu po dokončení zpracování, ale před synchronizací. Ke sledování stavu synchronizace použijte metriky Paměť a QPU.



## <a name="related-information"></a>Související informace

[Sledování metrik serveru](analysis-services-monitor.md)   
[Správa služby Azure Analysis Services](analysis-services-manage.md) 
