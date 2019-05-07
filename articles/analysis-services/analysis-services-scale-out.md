---
title: Horizontální navýšení kapacity Azure Analysis Services | Dokumentace Microsoftu
description: Replikace služby Azure Analysis Services serverů se Škálováním
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5524645153db0468076cc9b567965bff79d915cb
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192323"
---
# <a name="azure-analysis-services-scale-out"></a>Horizontální navýšení kapacity Azure Analysis Services

S horizontálním navýšením dotazy klientů můžou distribuovat mezi více *replikami dotazů* v *dotazování fondu*, snížení doby odezvy během dotazu vysoké zatížení. Můžete oddělit i zpracování od fondu dotazů, zajistit, že dotazy klientů neměly nepříznivý vliv na zpracování. Horizontální navýšení kapacity se dá nakonfigurovat na webu Azure portal nebo pomocí rozhraní REST API pro Analysis Services.

Horizontální navýšení kapacity je k dispozici pro servery v cenovou úroveň Standard. Každé repliky dotazů se účtuje stejná sazba jako váš server. Všechny repliky dotazu se vytvoří ve stejné oblasti jako váš server. Počet replik dotazu, které můžete nakonfigurovat se uplatňuje limit vycházející oblast, kterou je server v. Další informace najdete v tématu [dostupnost podle oblasti](analysis-services-overview.md#availability-by-region). Horizontální navýšení kapacity nezvyšuje velikost dostupné paměti pro váš server. Pro zvýšení paměti, budete muset upgradovat svůj plán. 

## <a name="why-scale-out"></a>Proč horizontální navýšení kapacity?

V typické server nasazení jednoho serveru slouží jako server pro zpracování a dotazu serveru. Počet klientů dotazy na modely na vašem serveru překračuje dotaz zpracování jednotky (QPU) pro váš server plán nebo zpracování modelu nastane ve stejnou dobu jako dotaz vysoké zatížení, může se snížit výkon. 

S horizontálním navýšením můžete vytvořit fond dotazů s až sedmi dalších dotazů repliky prostředky (osm celkový, včetně vašeho *primární* serveru). Můžete škálovat počet replik ve fondu dotazů k uspokojení požadavků QPU v časech kritické, a server pro zpracování od fondu dotazů můžete oddělit kdykoli. 

Bez ohledu na počet replik dotazu, které máte ve fondu dotazů zpracování úloh nejsou distribuovány mezi replikami dotazu. Primární server slouží jako server pro zpracování. Repliky dotazů sloužit pouze dotazy na databáze modelu synchronizovat mezi primárním serverem a každou repliku ve fondu dotazů. 

Při horizontálním navýšení kapacity, může trvat až pět minut, než nové repliky dotazů postupně přidat do fondu dotazů. Když všechny nové repliky dotazů nastavené a spuštěné, nová připojení klientů jsou vyrovnávat zatížení napříč prostředky ve fondu dotazů. Existující připojení klienta se nezmění z prostředků, které jsou aktuálně připojené k. Při horizontálním škálování v, jsou ukončeny všechny existující připojení klienta pro prostředek fondu dotaz, který má být odebrán z fondu dotazů. Klienti mohli připojit ke zbývající prostředky fondu dotazů.

## <a name="how-it-works"></a>Jak to funguje

Když konfigurujete poprvé horizontální navýšení kapacity, model databáze na primárním serveru jsou *automaticky* synchronizovat se službou nové repliky do nového fondu dotazů. Automatická synchronizace se vyskytuje jen jednou. Při automatické synchronizaci se primární server datových souborů (šifrují při nečinnosti v úložišti objektů blob) zkopírují do druhé místo, také šifrují při nečinnosti v úložišti objektů blob. Repliky ve fondu dotazů jsou pak *HYDRATOVANÝ* s daty z druhé sady souborů. 

Při automatické synchronizace se provádí pouze v případě, že jste horizontální navýšení kapacity serveru poprvé, můžete provést také ruční synchronizaci. Synchronizace zajišťuje, že data repliky ve fondu dotaz odpovídat primárního serveru. Při zpracování (aktualizace) modely na primárním serveru, musíte provést synchronizaci *po* dokončení operace zpracování. Tato synchronizace zkopíruje aktualizovaná data z primárního serveru souborů v úložišti objektů blob pro druhou sadu souborů. Repliky ve fondu dotazů jsou zvlhčí s aktualizovanými daty z druhé sady souborů v úložišti objektů blob. 

Při provádění následná operace škálování na víc systémů, například zvýšení počtu repliky ve fondu dotaz ze dvou pět, nové repliky jsou HYDRATOVANÝ s daty z druhé sady souborů v úložišti objektů blob. Neexistuje žádná synchronizace. Pokud byste chtěli provést synchronizaci po horizontální navýšení kapacity, nové repliky ve fondu dotaz by HYDRATOVANÝ dvakrát - redundantní dosazování. Při provádění následná operace škálování na víc systémů, je potřeba mít na paměti:

* Provádět synchronizaci *před provedením operace škálování na víc systémů* , aby redundantní dosazování přidání repliky. Nepovolují se souběžnou synchronizací a operace škálování na víc systémů spuštěné ve stejnou dobu.

* Při zpracování i automatizaci *a* operace škálování na víc systémů, je potřeba nejprve zpracování dat na primárním serveru, pak proveďte synchronizaci a poté proveďte operaci škálování na víc systémů. Toto pořadí zajistí minimální dopad na QPU a paměťové prostředky.

* Synchronizace je povolená i v případě, že nejsou žádné repliky ve fondu dotazů. Pokud se horizontální navýšení kapacity od nuly do jednoho nebo více replik s novými daty z operace zpracování na primárním serveru, nejprve provést synchronizaci s žádnou repliku ve fondu dotaz a pak horizontální navýšení kapacity. Synchronizace před horizontálním navýšením kapacity se vyhnete redundantní dosazení dat do nově přidané replik.

* Při odstraňování databáze modelu z primárního serveru, to není automaticky odstraněn z replik ve fondu dotazů. Musíte provést operaci synchronizace pomocí [synchronizace AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) příkaz prostředí PowerShell, který odebere soubor/s pro tuto databázi z umístění úložiště objektů blob sdíleného repliky a pak odstraní modelu databáze na replik ve fondu dotazů. Chcete-li zjistit, jestli existuje modelové databáze repliky ve fondu dotazů, ale ne na primárním serveru, ověřte **oddělte server pro zpracování od fondu dotazů na** nastavení se **Ano**. Poté použití SSMS k připojení k používání primárního serveru `:rw` kvalifikátor jestli databáze existuje. Připojte se k repliky ve fondu dotaz připojením bez `:rw` kvalifikátor, pokud se stejná databáze také existuje. Pokud databáze existuje u replik ve fondu dotazů, ale ne na primárním serveru, spusťte operaci synchronizace.   

* Při přejmenování databáze na primárním serveru, je nutné zajistit, že databáze je správně synchronizovat na všechny repliky další krok. Po přejmenování, provést synchronizaci s použitím [synchronizace AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) zadáním příkazu `-Database` parametr s názvem starého databáze. Tato synchronizace odebere z všechny repliky databáze a soubory se starým názvem. Pak proveďte zadáním jiného synchronizace `-Database` parametr s názvem nové databáze. Druhý synchronizace zkopíruje nově pojmenovanou databázi pro druhou sadu souborů a hydráty případné repliky. Tato synchronizace nelze provést pomocí příkazu modelu synchronizovat na portálu.

### <a name="separate-processing-from-query-pool"></a>Samostatné zpracování od fondu dotazů

Pro maximální výkon pro zpracování a operace dotazů můžete oddělit serveru zpracování od fondu dotazů. V případě oddělení, nová připojení klientů přiřazených k replikami dotazu ve fondu dotazů. Pokud operace zpracování trvat pouze krátkou dobu, můžete oddělit serveru zpracování od fondu dotazů objem čas potřebný k provedení operace zpracování a synchronizaci a pak ho zahrňte zpět do fondu dotazů. Při oddělení server pro zpracování od fondu dotazů nebo přidání zpět do fondu dotazů může trvat až pět minut, než se operace dokončí.

## <a name="monitor-qpu-usage"></a>Monitorování využití QPU

Chcete-li zjistit, zda horizontální navýšení kapacity pro váš server, je nezbytné, sledování serveru na webu Azure portal pomocí metrik. Pokud vaše QPU pravidelně navyšuje navýšení kapacity, znamená to, že počet dotazů vůči vašich modelů je překročení limitu QPU pro váš plán. Délka metrika dotazu fondu úlohy fronty také zvýší v případě, že k dispozici QPU překračuje počet dotazů ve frontě fondu vláken dotazů. 

Jiné dobrou metrikou a sledujte je průměrná QPU podle ServerResourceType. Tato metrika porovnává průměrné QPU pro primární server s ním fondu dotazů. 

![Dotaz pro horizontální navýšení kapacity metriky](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>Ke konfiguraci QPU podle ServerResourceType
1. Ve spojnicovém grafu metriky, klikněte na tlačítko **přidat metriku**. 
2. V **prostředků**, vyberte svůj server pak **METRIKY oboru názvů**vyberte **standardních metrik služby Analysis Services**, pak v **METRIKA**, Vyberte **QPU**a potom v **AGREGACE**vyberte **Avg**. 
3. Klikněte na tlačítko **použít rozdělení**. 
4. V **hodnoty**vyberte **ServerResourceType**.  

Další informace najdete v tématu [Monitorování metrik serveru](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Konfigurace škálování na více instancí

### <a name="in-azure-portal"></a>Na webu Azure portal

1. Na portálu klikněte na tlačítko **horizontální navýšení kapacity**. Pomocí posuvníku vyberte počet serverů replik dotazu. Počet replik, které zvolíte, je kromě existující server.

2. V **oddělte server pro zpracování od fondu dotazů**, vyberte Ano. Pokud chcete vyloučit ze serverů dotazu serveru zpracování. Klient [připojení](#connections) pomocí výchozí připojovací řetězec (bez `:rw`) se přesměrují do replik ve fondu dotazů. 

   ![Horizontální navýšení kapacity posuvníku](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klikněte na tlačítko **Uložit** ke zřízení nových serverů repliky dotazu. 

Při konfiguraci horizontální navýšení kapacity pro server poprvé, modely na primárním serveru jsou automaticky synchronizovat s replikami ve fondu dotazů. Automatická synchronizace dochází pouze jednou, při první konfiguraci horizontální navýšení kapacity na jeden nebo více replikami. Následné změny počet replik na stejném serveru *neaktivují jiného automatickou synchronizaci*. Automatická synchronizace nedojde znovu, i když nastavíte serveru na nulu repliky a pak znovu škálovat na libovolný počet replik. 

## <a name="synchronize"></a>Synchronizovat 

Operace synchronizace je nutné provést ručně nebo pomocí rozhraní REST API.

### <a name="in-azure-portal"></a>Na webu Azure portal

V **přehled** > model > **synchronizovat model**.

![Horizontální navýšení kapacity posuvníku](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

Použití **synchronizace** operace.

#### <a name="synchronize-a-model"></a>Synchronizovat model   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Získat stav synchronizace  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Návratové kódy stavu:


|Kód  |Popis  |
|---------|---------|
|-1     |  Neplatný       |
|0     | Replikování        |
|1     |  Rehydratace       |
|2     |   Dokončeno       |
|3     |   Selhalo      |
|4     |    Finalizace     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před použitím prostředí PowerShell, [nainstalovat nebo aktualizovat nejnovější modul Azure PowerShell](/powershell/azure/install-az-ps). 

Chcete-li spustit synchronizaci, použijte [synchronizace AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Pokud chcete nastavit počet replik dotazu, použijte [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Zadejte nepovinný `-ReadonlyReplicaCount` parametru.

Chcete-li oddělte server pro zpracování od fondu dotazů, použijte [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Zadejte nepovinný `-DefaultConnectionMode` parametr, který se `Readonly`.

Další informace najdete v tématu [pomocí instančního objektu s modulem Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Připojení

Na stránce přehled vašeho serveru jsou dva názvy serverů. Pokud jste ještě nenakonfigurovali horizontální navýšení kapacity pro server, oba názvy serverů fungují stejně. Jakmile nakonfigurujete horizontální navýšení kapacity pro server, musíte zadat název příslušného serveru v závislosti na typu připojení. 

Pro připojení klienta koncového uživatele, jako jsou Power BI Desktopu, Excelu a vlastní aplikace, použijte **název serveru**. 

Pro aplikace SSMS, SSDT a připojovací řetězce v prostředí PowerShell, aplikace Azure Function App a nástroji AMO, použijte **název serveru pro správu**. Název serveru pro správu obsahuje speciální `:rw` kvalifikátor (čtení a zápis). Všechny operace zpracování, ke kterým došlo u (primární) server pro správu.

![Názvy serverů](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Řešení potíží

**Problém:** Uživatelé získají chyba **nelze nalézt server '\<název serveru > "instance v režimu připojení 'ReadOnly'.**

**Řešení:** Při výběru **oddělte server pro zpracování od fondu dotazů** možnost připojení klienta pomocí výchozí připojovací řetězec (bez `:rw`) se přesměrují na fond replikami dotazu. Pokud repliky ve fondu dotazů jsou online, ale protože synchronizace nebyl dosud nebylo dokončeno, přesměrované klientská připojení může selhat. Pokud chcete zabránit neúspěšná připojení, musí existovat alespoň dva servery ve fondu dotaz při provedení synchronizace. Každý server se synchronizuje jednotlivě, ostatní budou i nadále online. Pokud se rozhodnete, že nebudete chtít server zpracování ve fondu dotaz během zpracování, můžete ho odebrat z fondu pro zpracování a pak ho přidat zpět do fondu, jakmile se zpracování dokončí, ale před synchronizací. Použití paměti a QPU metriky pro monitorování stavu synchronizace.



## <a name="related-information"></a>Související informace

[Monitorování metrik serveru](analysis-services-monitor.md)   
[Správa služby Azure Analysis Services](analysis-services-manage.md) 
