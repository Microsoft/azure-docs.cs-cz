---
title: Azure SQL Database bez serveru (Preview) | Microsoft Docs
description: Tento článek popisuje novou výpočetní úroveň bez serveru a porovnává ji se stávající zřízenou výpočetní vrstvou.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: moslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 09/06/2019
ms.openlocfilehash: 5b13c3c93f8b2d6f3eed5a32c49baf1b9d1b201e
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773336"
---
# <a name="azure-sql-database-serverless-preview"></a>Azure SQL Database bez serveru (Preview)

Azure SQL Database bez serveru (Preview) je výpočetní vrstva pro izolované databáze, která automaticky škáluje výpočetní výkon na základě požadavků na úlohy a faktur za množství výpočetní služby za sekundu. Výpočetní vrstva bez serveru taky automaticky pozastaví databáze během neaktivních období, kdy se účtují jenom úložiště, a automaticky obnoví databáze při návratu aktivity.

## <a name="serverless-compute-tier"></a>Bezserverová výpočetní úroveň

Výpočetní vrstva bez serveru pro izolovanou databázi je parametrizovaná rozsahem automatického škálování výpočetního rozsahu a prodlevou automatického pozastavení.  V konfiguraci těchto parametrů se tvarují možnosti výkonu databáze a náklady na výpočetní výkon.

![fakturace bez serveru](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Konfigurace výkonu

- **Minimální virtuální jádra** a **Maximální virtuální jádra** jsou konfigurovatelné parametry, které definují rozsah výpočetní kapacity dostupné pro databázi. Limity paměti a vstupně-výstupních operací jsou úměrné zadanému rozsahu vCore.  
- **Prodleva automatického pozastavení** je konfigurovatelný parametr definující časové období, po které musí být databáze neaktivní, než bude automaticky pozastavena. Databáze se automaticky obnoví, když dojde k dalšímu přihlášení nebo jiné aktivitě.  Alternativně je možné zakázat autopauzu.

### <a name="cost"></a>Náklady

- Náklady na databázi bez serveru jsou součtem nákladů na výpočetní prostředky a náklady na úložiště.
- Pokud je využití COMPUTE mezi nakonfigurovanými minimálními a maximálními limity, náklady na výpočetní výkon vycházejí z vCore a využité paměti.
- Pokud je využití COMPUTE pod nakonfigurovaným minimálním limitem, náklady na výpočetní výkon vycházejí z nakonfigurované minimální virtuální jádra a minimální paměti.
- Při pozastavení databáze jsou výpočetní náklady nulové a účtují se jenom náklady na úložiště.
- Náklady na úložiště se určují stejným způsobem jako ve zřízené výpočetní úrovni.

Další informace o cenách najdete v tématu [fakturace](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scénáře

Možnost bez serveru je cenově vyladěná pro izolované databáze s přerušovaným, nepředvídatelným vzorem použití, který umožňuje určitou prodlevu v výpočetních prostředích zahřívání po nečinných obdobích využití. Naproti tomu zřízená výpočetní úroveň je cenově funkčním výkonem optimalizovaným pro izolované databáze nebo více databází v elastických fondech s vyšším průměrem využití, které neumožňuje žádné zpoždění při zahřívání služby Compute.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scénáře vhodné pro výpočetní prostředky bez serveru

- Izolované databáze s přerušovaným a nepředvídatelným vzorem použití prodávanými po dobu neaktivity a nižším průměrem využití výpočetních prostředků v průběhu času.
- Izolované databáze v zřízené výpočetní úrovni, které se často mění a zákazníci, kteří preferují delegování výpočetního škálování na službu.
- Nové izolované databáze bez historie využití, ve kterých je výpočet velikosti obtížné nebo není možné odhadnout před nasazením v SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scénáře vhodné pro zřízené výpočetní prostředky

- Izolované databáze s pravidelnými a předvídatelnými způsoby použití a vyšším využitím výpočetního využití v průběhu času.
- Databáze, které nemůžou tolerovat kompromisy na výkonu, což vede k častému oříznutí paměti nebo zpoždění při automatického obnovení z pozastaveného stavu.
- Víc databází s přerušovanými a nepředvídatelnými vzorci použití, které se dají konsolidovat do elastických fondů pro lepší optimalizaci cen.

## <a name="comparison-with-provisioned-compute-tier"></a>Porovnání se zřízenou výpočetní vrstvou

Následující tabulka shrnuje rozdíly mezi výpočetní a zřízenou výpočetní úrovní bez serveru:

| | **Výpočetní prostředí bez serveru** | **Zřízené výpočetní prostředky** |
|:---|:---|:---|
|**Vzor využití databáze**| Občasné, nepředvídatelné využití s nižším průměrem využití výpočetních prostředků v průběhu času. |  Efektivnější vzorce použití s vyšším průměrem využití výpočetních prostředků v průběhu času nebo více databází pomocí elastických fondů.|
| **Úsilí řízení výkonu** |Malým|Vyšší|
|**Škálování na výpočetní výkon**|Automatické|Ručně|
|**Výpočetní rychlost odezvy**|Nižší po neaktivních obdobích|Okamžité|
|**Členitost fakturace**|Za sekundu|Za hodinu|

## <a name="purchasing-model-and-service-tier"></a>Nákup modelu a úrovně služeb

SQL Database bez serveru se aktuálně podporuje jenom v Pro obecné účely vrstvě na hardwaru generace 5 v modelu nákupu vCore.

## <a name="autoscaling"></a>Automatické škálování

### <a name="scaling-responsiveness"></a>Škálování rychlosti odezvy

Obecně platí, že databáze bez serveru běží na počítači s dostatečnou kapacitou ke splnění požadavků na prostředky bez přerušení pro jakékoli množství COMPUTE vyžádané v rámci omezení nastavené maximální hodnotou virtuální jádra. Vyrovnávání zatížení se občas může vyskytnout automaticky, pokud počítač nemůže během několika minut splnit požadavky na prostředky. Pokud je třeba požadavek na prostředek 4 virtuální jádra, ale k dispozici je jenom 2 virtuální jádra, může trvat až několik minut, než se doplní 4 virtuální jádra. Databáze zůstane během vyrovnávání zatížení online, s výjimkou krátkého období na konci operace, když jsou připojení vyřazená.

### <a name="memory-management"></a>Správa paměti

Paměť pro databáze bez serveru se uvolní častěji než u zřízených výpočetních databází. Toto chování je důležité řídit náklady bez serveru a může mít vliv na výkon.

#### <a name="cache-reclamation"></a>Recyklace mezipaměti

Na rozdíl od zřízených výpočetních databází je paměť z mezipaměti SQL uvolněna z databáze bez serveru, když je využití procesoru nebo paměti nízké.

- Využití mezipaměti je považováno za nízké, pokud celková velikost naposledy použitých položek mezipaměti v časovém intervalu klesne pod prahovou hodnotu.
- Při aktivaci opětovného získání mezipaměti se cílová velikost mezipaměti zmenší přírůstkově na zlomek její předchozí velikosti a obnovení bude pokračovat pouze v případě, že je využití nízké.
- Když dojde k recyklování mezipaměti, zásada pro výběr položek mezipaměti k vyřazení je stejná jako zásada výběru pro zřízené výpočetní databáze, pokud je tlak paměti vysoký.
- Velikost mezipaměti se nikdy nesnižuje pod minimálním limitem paměti definovaným min virtuální jádra, který se dá nakonfigurovat.

V neserverových a zřízených výpočetních databázích se můžou položky mezipaměti vyřadit, pokud se použije veškerá dostupná paměť.

#### <a name="cache-hydration"></a>Vysazování mezipaměti

Mezipaměť SQL roste, protože data se načítají z disku stejným způsobem a se stejnou rychlostí jako u zřízených databází. Pokud je databáze zaneprázdněná, může být velikost mezipaměti až do maximálního limitu paměti neomezená.

## <a name="autopausing-and-autoresuming"></a>Autopozastavování a autoobnovování

### <a name="autopausing"></a>Autopozastavování

Automatické pauzy se aktivují, pokud jsou splněné všechny následující podmínky po dobu trvání prodlevy při automatickém pozastavení:

- Počet relací = 0
- CPU = 0 pro úlohy uživatele běžící ve fondu uživatelů

V případě potřeby je k dispozici možnost pro vypnutí autopauzy.

Následující funkce nepodporují autopauzu.  To znamená, že pokud použijete některou z následujících funkcí, zůstane databáze online bez ohledu na dobu nečinnosti databáze:

- Geografická replikace (aktivní geografická replikace a skupiny s automatickým převzetím služeb při selhání).
- Dlouhodobé uchovávání záloh (LTR).
- Synchronizovaná databáze použitá v synchronizaci dat SQL  Na rozdíl od synchronizace databází databáze hub a členské databáze podporují automatické pozastavení.
- Databáze úlohy používaná v elastických úlohách.

Při nasazování některých aktualizací služby, které vyžadují databázi online, se dočasně brání v dočasném pozastavení.  V takových případech se po dokončení aktualizace služby znovu povolí opětovné pozastavení.

### <a name="autoresuming"></a>Probíhá autoobnovování

Automatické obnovení se aktivuje, pokud platí kterákoli z následujících podmínek v libovolnou dobu:

|Funkce|Aktivační událost autoresume|
|---|---|
|Ověřování a autorizace|Přihlásit se|
|Detekce hrozeb|Povolení nebo zakázání nastavení detekce hrozeb na úrovni databáze nebo serveru.<br>Úprava nastavení detekce hrozeb na úrovni databáze nebo serveru.|
|Zjišťování a klasifikace dat|Přidávání, úpravy, odstraňování nebo zobrazování popisků citlivosti|
|Auditování|Zobrazení záznamů auditu.<br>Probíhá aktualizace nebo zobrazení zásad auditování.|
|Maskování dat|Přidání, úprava, odstranění nebo zobrazení pravidel maskování dat|
|Transparentní šifrování dat|Zobrazení stavu nebo stavu transparentního šifrování dat|
|Dotaz (výkon) úložiště dat|Úprava nebo zobrazení nastavení úložiště dotazů; Automatické ladění|
|Automatického ladění|Aplikace a ověření doporučení automatického ladění, jako je automatické indexování|
|Kopírování databáze|Vytvoří databázi jako kopii.<br>Exportujte do souboru BACPAC.|
|Synchronizace dat SQL|Synchronizace mezi centrem a členskými databázemi, které se spouští podle konfigurovatelného plánu, nebo se provádí ručně|
|Úprava určitých metadat databáze|Přidávání nových značek databáze.<br>Změna maximálního virtuální jádra, minimální virtuální jádra nebo prodlevy při autopauze.|
|SQL Server Management Studio (SSMS)|Při použití verzí SSMS starších než 18,1 a otevření nového okna dotazu pro všechny databáze na serveru bude obnovena veškerá automaticky pozastavená databáze na stejném serveru. K tomuto chování nedochází, pokud používáte SSMS verze 18,1 nebo novější.|

Automatické obnovení se také aktivuje při nasazení některých aktualizací služby, které vyžadují, aby byla databáze online.

### <a name="connectivity"></a>Připojení

Pokud je databáze bez serveru pozastavená, pak se při prvním přihlášení obnoví databáze a vrátí se chyba s oznámením, že databáze není k dispozici, kód chyby 40613. Po obnovení databáze se přihlašovací jméno musí znovu pokusit o navázání připojení. Klienti databáze s logikou opakování připojení by nemuseli upravovat.

### <a name="latency"></a>Latence

Latence pro autoresume a autopauza databáze bez serveru je obvykle na hodnotu 1 minuta k autoresume a 1-10 minut pro autopauzu.

## <a name="onboarding-into-serverless-compute-tier"></a>Připojování do výpočetní úrovně bez serveru

Vytvoření nové databáze nebo přesunutí existující databáze do výpočetní úrovně bez serveru se řídí stejným vzorem jako vytvoření nové databáze v zřízené výpočetní úrovni a zahrnuje následující dva kroky.

1. Zadejte název cíle služby. Cíl služby stanoví úroveň služby, generování hardwaru a maximální virtuální jádra. V následující tabulce jsou uvedeny možnosti cíle služby:

   |Název cíle služby|Úroveň služby|Generování hardwaru|Maximální počet virtuální jader|
   |---|---|---|---|
   |GP_S_Gen5_1|Obecné použití|Gen5|1|
   |GP_S_Gen5_2|Obecné použití|Gen5|2|
   |GP_S_Gen5_4|Obecné použití|Gen5|4|

2. Volitelně můžete zadat minimální virtuální jádra a prodlevu při pauze pro změnu jejich výchozích hodnot. V následující tabulce jsou uvedeny dostupné hodnoty pro tyto parametry.

   |Parametr|Volby hodnoty|Výchozí hodnota|
   |---|---|---|---|
   |Minimální počet virtuálních jader|Libovolný z těchto {0,5, 1, 2, 4} nepřekračuje maximum virtuální jádra|0,5 virtuální jádra|
   |Prodleva při autopauze|Minimálně 60 minut (1 hodina)<br>Velikosti 10080 minut (7 dní)<br>Zvýší 60 minut<br>Zakázat automatického pozastavení:-1|60 minut|

> [!NOTE]
> Použití jazyka T-SQL k přesunu existující databáze do bez serveru nebo změna její velikosti se momentálně nepodporuje, ale můžete ji provádět pomocí Azure Portal nebo PowerShellu.

### <a name="create-new-database-in-serverless-compute-tier"></a>Vytvořit novou databázi na výpočetní úrovni bez serveru 

#### <a name="use-azure-portal"></a>Použití webu Azure Portal

Další [informace najdete v tématu rychlý Start: Pomocí Azure Portal](sql-database-single-database-get-started.md)vytvořte v Azure SQL Database jednu databázi.

#### <a name="use-powershell"></a>Použití prostředí PowerShell

Následující příklad vytvoří novou databázi na výpočetní úrovni bez serveru.  V tomto příkladu je explicitně určen minimální prodleva virtuální jádra, Max virtuální jádra a interval pro autopauzu.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Přesunout databázi ze zřízené výpočetní vrstvy do výpočetní vrstvy bez serveru

#### <a name="use-powershell"></a>Použití prostředí PowerShell

Následující příklad přesune databázi ze zřízené výpočetní vrstvy do výpočetní vrstvy bez serveru. V tomto příkladu je explicitně určen minimální prodleva virtuální jádra, Max virtuální jádra a interval pro autopauzu.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Přesun databáze z výpočetní vrstvy bez serveru do zřízené výpočetní vrstvy

Databáze bez serveru se dá přesunout do zřízené výpočetní vrstvy stejným způsobem jako přesun zřízené výpočetní databáze do výpočetní vrstvy bez serveru.

## <a name="modifying-serverless-configuration"></a>Úprava konfigurace bez serveru

### <a name="maximum-vcores"></a>Maximální počet virtuálních jader

#### <a name="use-powershell"></a>Použití prostředí PowerShell

Změna maximálního virtuální jádra se provádí pomocí příkazu [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) v PowerShellu pomocí `MaxVcore` argumentu.

### <a name="minimum-vcores"></a>Minimální počet virtuálních jader

#### <a name="use-powershell"></a>Použití prostředí PowerShell

Úprava minimálního virtuální jádra se provádí pomocí příkazu [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) v PowerShellu pomocí `MinVcore` argumentu.

### <a name="autopause-delay"></a>Prodleva při autopauze

#### <a name="use-powershell"></a>Použití prostředí PowerShell

Úprava prodlevy automatického pozastavení se provádí pomocí příkazu [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) v PowerShellu pomocí `AutoPauseDelayInMinutes` argumentu.

## <a name="monitoring"></a>Monitorování

### <a name="resources-used-and-billed"></a>Využité a fakturované prostředky

Prostředky databáze bez serveru jsou zapouzdřeny pomocí balíčku aplikace, instance SQL a entit fondu prostředků uživatele.

#### <a name="app-package"></a>Balíček aplikace

Balíček aplikace je vnější největší hranice správy prostředků pro databázi bez ohledu na to, jestli je databáze v neserverové nebo zřízené výpočetní úrovni. Balíček aplikace obsahuje instanci SQL a externí služby, které společně připadají do oboru všechny uživatelské a systémové prostředky používané databází v SQL Database. Příklady externích služeb zahrnují R a fulltextové vyhledávání. Instance SQL všeobecně rozchází z celkového využití prostředků napříč balíčkem aplikace.

#### <a name="user-resource-pool"></a>Fond zdrojů uživatele

Fond zdrojů uživatele je vnitřní hranice správy prostředků pro databázi bez ohledu na to, jestli je databáze v neserverové nebo zřízené výpočetní úrovni. Fond zdrojů uživatele obor a vstupně-výstupní operace pro uživatelské zatížení generované dotazy DDL, jako je například vytváření a změny a dotazy DML, jako je například SELECT, INSERT, UPDATE a DELETE. Tyto dotazy obvykle reprezentují nejvýznamnější podíl využití v rámci balíčku aplikace.

### <a name="metrics"></a>Metriky

Metriky pro monitorování využití prostředků balíčku aplikace a fondu uživatelů v databázi bez serveru jsou uvedené v následující tabulce:

|Entita|Metrika|Popis|Jednotky|
|---|---|---|---|
|Balíček aplikace|app_cpu_percent|Procento virtuální jádra, které aplikace používá vzhledem k maximálnímu virtuální jádra povolenému pro aplikaci|Procento|
|Balíček aplikace|app_cpu_billed|Množství COMPUTE, které aplikace účtuje během období generování sestav. Částka placená během tohoto období je produktem této metriky a Jednotková cena vCore. <br><br>Hodnoty této metriky se určují tak, že se v průběhu času vyhodnotí maximální využití procesoru a využitá paměť každou sekundu. Pokud je využité množství menší než minimální zajištěné množství, které je stanoveno minimálním virtuální jádra a minimální pamětí, účtuje se minimální stanovený objem. Aby bylo možné porovnat procesor s pamětí pro účely fakturace, je paměť normalizována na jednotky virtuální jádra tím, že převýší množství paměti v GB o 3 GB na vCore.|vCore sekund|
|Balíček aplikace|app_memory_percent|Procentuální podíl paměti používané aplikací vzhledem k maximálnímu povolenému počtu paměti pro aplikaci.|Procento|
|Fond uživatelů|cpu_percent|Procento virtuální jádra, které používá uživatelské zatížení vzhledem k maximálnímu virtuální jádra povolenému pro zatížení uživatele.|Procento|
|Fond uživatelů|data_IO_percent|Procento datových IOPS používaných uživatelskými úlohami vzhledem k maximálnímu počtu datových IOPS povolených pro zatížení uživatele.|Procento|
|Fond uživatelů|log_IO_percent|Procento protokolu MB/s používaného uživatelskými úlohami vzhledem k maximálnímu počtu MB/s povoleného pro zatížení uživatele.|Procento|
|Fond uživatelů|workers_percent|Procentuální podíl pracovníků využívaných uživatelskými úlohami vzhledem k maximálnímu počtu pracovních procesů povolených pro zatížení uživatele.|Procento|
|Fond uživatelů|sessions_percent|Procento relací používaných uživatelskými úlohami vzhledem k maximálnímu počtu relací povolených pro zatížení uživatele.|Procento|

### <a name="pause-and-resume-status"></a>Stav pozastavení a obnovení

V Azure Portal se stav databáze zobrazí v podokně přehledu serveru, kde jsou uvedeny databáze, které obsahuje. Stav databáze je také zobrazen v podokně Přehled pro databázi.

Pomocí následujícího příkazu PowerShellu můžete zadat dotaz na stav pozastavení a obnovení databáze:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Omezení prostředků

Omezení prostředků najdete v tématu [výpočetní vrstva bez serveru](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).

## <a name="billing"></a>Fakturace

Cena za výpočetní náklady je maximální využití procesoru a využité paměti každou sekundu. Pokud je množství využitého procesoru a využité paměti menší než minimální velikost zřízená za každou, účtuje se zřízené množství. Aby bylo možné porovnat procesor s pamětí pro účely fakturace, je paměť normalizována na jednotky virtuální jádra tím, že převýší množství paměti v GB o 3 GB na vCore.

- **Prostředek se fakturoval**: Procesor a paměť
- **Účtovaná částka**: Vcore Unit Price * Max (min virtuální jádra, virtuální jádra použito, min. gb × 1/3, využité paměti gb × 1/3) 
- **Četnost fakturace**: Za sekundu

Jednotková cena vCore za cenu za vCore za sekundu. Konkrétní jednotkové ceny v dané oblasti najdete na [stránce s cenami Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) .

K dispozici je množství COMPUTE, které se účtuje pomocí následující metriky:

- **Metrika**: App_cpu_billed (Vcore sekund)
- **Definice**: max (minimum virtuální jádra, virtuální jádra použito, minimální paměť GB × 1/3, využité paměťové GB × 1/3)
- **Frekvence generování sestav**: Za minutu

Toto množství se počítá každou sekundu a agreguje se za 1 minutu.

Vezměte v úvahu databázi bez serveru nakonfigurovanou s 1 min vCore a 4 max virtuální jádra.  Tato hodnota odpovídá přibližně 3 GB paměti a maximální velikosti paměti 12 GB.  Předpokládejme, že prodleva automatického pozastavení je nastavená na 6 hodin a úloha databáze je aktivní během prvních 2 hodin po dobu 24 hodin a jinak neaktivní.    

V takovém případě se databáze fakturuje za výpočetní výkon a úložiště během prvních 8 hodin.  I když je databáze neaktivní od druhé hodiny, bude se vám v následujících 6 hodinách účtovat za výpočetní výkon na základě minimálního výpočetního zřízeného, zatímco je databáze online.  Během období 24 hodin se fakturuje jenom úložiště, zatímco databáze je pozastavená.

Ve výše uvedeném příkladu je vypočítaná faktura za výpočetní výkon následující:

|Časový interval|Virtuální jádra použité za sekundu|Využité GB za sekundu|Dimenze COMPUTE se fakturuje.|vCore sekund se účtují v časovém intervalu.|
|---|---|---|---|---|
|0:00-1:00|4|9|Virtuální jádra použito|4 virtuální jádra × 3600 sekund = 14400 vCore sekund|
|1:00-2:00|1|12|Využitá paměť|12 GB × 1/3 × 3600 sekund = 14400 vCore sekund|
|2:00-8:00|0|0|Minimální zřízená paměť|3 GB × 1/3 × 21600 sekund = 21600 vCore sekund|
|8:00-24:00|0|0|Při pozastavení se neúčtují žádné výpočetní prostředky.|0 vCore sekund|
|Celkový počet vCore sekund fakturovaných za 24 hodin||||50400 vCore sekund|

Předpokládejme, že cena za výpočetní jednotku je $0.000073/vCore/sekunda.  Pak se za toto 24hodinové období účtuje produkt ceny za výpočetní jednotku a vCore sekundy: $0.000073/vCore/Second × 50400 vCore sekund = $3,68

## <a name="available-regions"></a>Dostupné oblasti

Výpočetní vrstva bez serveru je dostupná po celém světě s výjimkou těchto oblastí: Austrálie – střed, Čína – východ, Čína – sever, Francie – jih, Německo – střed, Německo – západ, Indie – jih, Korea – jih, Jižní Afrika – západ, Velká Británie – sever, Velká Británie – jih, Velká Británie – západ a Středozápadní USA.

## <a name="next-steps"></a>Další postup

- Informace o tom, jak [začít, najdete v tématu rychlý Start: Pomocí Azure Portal](sql-database-single-database-get-started.md)vytvořte v Azure SQL Database jednu databázi.
- Omezení prostředků najdete v tématu [omezení prostředků výpočetní vrstvy bez serveru](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).
