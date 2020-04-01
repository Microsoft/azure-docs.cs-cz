---
title: Bez serveru
description: Tento článek popisuje novou výpočetní vrstvu bez serveru a porovnává ji s existující zřízenývýpočetní vrstvou
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 3/11/2020
ms.openlocfilehash: 8a72d3dc7f7b3fddf66df8111b1e92116a62883a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474022"
---
# <a name="azure-sql-database-serverless"></a>Bez serveru Azure SQL Database

Azure SQL Database serverless je výpočetní vrstva pro jednotlivé databáze, která automaticky škáluje výpočetní prostředky na základě požadavku na pracovní vytížení a účty za množství výpočetních prostředků spoužívaných za sekundu. Výpočetní úroveň bez serveru také automaticky pozastaví databáze během neaktivních období, kdy se účtuje pouze úložiště a automaticky obnoví databáze, když se vrátí aktivita.

## <a name="serverless-compute-tier"></a>Bezserverová výpočetní úroveň

Výpočetní úroveň bez serveru pro jednu databázi je parametrizována rozsahem automatického škálování výpočetních prostředků a zpožděním automatického pozastavení.  Konfigurace těchto parametrů utvářet prostředí výkonu databáze a výpočetní náklady.

![bezserverová fakturace](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Konfigurace výkonu

- **Minimální virtuální jádra** a **maximální virtuální jádra** jsou konfigurovatelné parametry, které definují rozsah výpočetní kapacity, které jsou k dispozici pro databázi. Limity paměti a vi jsou úměrné zadanému rozsahu virtuálních jader.  
- **Zpoždění automatického pozastavení** je konfigurovatelný parametr, který definuje dobu, po kterou musí být databáze neaktivní, než bude automaticky pozastavena. Databáze se automaticky obnoví, když dojde k dalšímu přihlášení nebo jiné aktivitě.  Automatické pozastavení lze také zakázat.

### <a name="cost"></a>Náklady

- Náklady na databázi bez serveru je součtem nákladů na výpočetní výkon a nákladů na úložiště.
- Když je využití výpočetních prostředků mezi nakonfigurovanými maximálními limity, výpočetní náklady jsou založeny na virtuálních jádrech a použité paměti.
- Pokud je využití výpočetních prostředků nižší než nakonfigurované limity min, výpočetní náklady jsou založeny na nakonfigurovaných virtuálních jádrech min a min paměti.
- Když je databáze pozastavena, náklady na výpočetní prostředky jsou nulové a vznikají pouze náklady na úložiště.
- Náklady na úložiště se určují stejným způsobem jako v zřízené výpočetní vrstvě.

Další podrobnosti o nákladech najdete v [tématu Fakturace](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scénáře

Bez serveru je cena a výkon optimalizovaný pro jednotlivé databáze s přerušované, nepředvídatelné vzorce využití, které si mohou dovolit určité zpoždění v zahřívání výpočetních po dobu nečinnosti využití. Naproti tomu zřízená výpočetní vrstva je cenový výkon optimalizovaný pro jednotlivé databáze nebo více databází v elastických fondech s vyšší průměrnou využití, která si nemůže dovolit žádné zpoždění v zahřívání výpočetních prostředků.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scénáře vhodné pro bezserverové výpočty

- Jednotlivé databáze s přerušovanými, nepředvídatelnými vzory využití proloženými obdobími nečinnosti a nižším průměrným využitím výpočetních prostředků v čase.
- Jediné databáze v zřízené výpočetní vrstvě, které jsou často škálované a zákazníci, kteří dávají přednost delegování přestupnice výpočetních prostředků na službu.
- Nové jediné databáze bez historie využití, kde je obtížné velikosti výpočetních prostředků nebo není možné odhadnout před nasazením v databázi SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scénáře vhodné pro zřízené výpočetní prostředky

- Jednotlivé databáze s pravidelnějšími a předvídatelnějšími vzory využití a vyšším průměrným využitím výpočetních prostředků v průběhu času.
- Databáze, které nemohou tolerovat kompromisy výkonu vyplývající z častějšího oříznutí paměti nebo zpoždění při autorizaci z pozastaveného stavu.
- Více databází s přerušované, nepředvídatelné vzorce využití, které lze konsolidovat do elastických fondů pro lepší optimalizaci ceny a výkonu.

## <a name="comparison-with-provisioned-compute-tier"></a>Porovnání s zřízenou výpočetní vrstvou

Následující tabulka shrnuje rozdíly mezi výpočetní vrstvou bez serveru a zřízenou výpočetní vrstvou:

| | **Bezserverové výpočetní prostředí** | **Zřízené výpočetní prostředky** |
|:---|:---|:---|
|**Vzor využití databáze**| Přerušované, nepředvídatelné využití s nižším průměrným využitím výpočetních prostředků v čase. |  Více pravidelné vzory využití s vyšší průměrné využití výpočetních prostředků v čase nebo více databází pomocí elastických fondů.|
| **Úsilí v oblasti řízení výkonnosti** |Lower|Vyšší|
|**Výpočetní měřítko**|Automaticky|Ruční|
|**Vypočítat odezvu**|Nižší po neaktivních obdobích|Okamžité|
|**Rozlišovací schopnost fakturace**|Sekundu|Za hodinu|

## <a name="purchasing-model-and-service-tier"></a>Nákupní model a úroveň služeb

SQL Database bez serveru je aktuálně podporována pouze ve vrstvě obecnéúčely na generaci 5 hardware v modelu nákupu virtuálních jader.

## <a name="autoscaling"></a>Automatické škálování

### <a name="scaling-responsiveness"></a>Změna rychlosti odezvy

Obecně platí, že databáze bez serveru jsou spouštěny v počítači s dostatečnou kapacitou pro uspokojení poptávky po zdrojích bez přerušení pro jakékoli množství výpočetních prostředků požadovaných v rámci limitů stanovených hodnotou max virtuálních jader. V některých případě dojde k automatickému vyrovnávání zatížení, pokud počítač není schopen uspokojit poptávku po zdrojích během několika minut. Například pokud je poptávka po prostředku 4 virtuální jádra, ale jsou k dispozici pouze 2 virtuální jádra, pak může trvat až několik minut, než budou k dispozici 4 virtuální jádra. Databáze zůstane online během vyrovnávání zatížení s výjimkou krátké ho období na konci operace při vynechání připojení.

### <a name="memory-management"></a>Správa paměti

Paměť pro databáze bez serveru je rekultivována častěji než pro zřízené výpočetní databáze. Toto chování je důležité pro řízení nákladů bez serveru a může mít vliv na výkon.

#### <a name="cache-reclamation"></a>Rekultivace mezipaměti

Na rozdíl od zřízených výpočetních databází je paměť z mezipaměti SQL vyvolána z databáze bez serveru, když je nízké využití procesoru nebo mezipaměti.

- Využití mezipaměti je považováno za nízké, pokud celková velikost naposledy použitých položek mezipaměti klesne po určitou dobu pod prahovou hodnotu.
- Při aktivaci rekultivace mezipaměti je velikost cílové mezipaměti postupně snížena na zlomek předchozí velikosti a rekultivace pokračuje pouze v případě, že využití zůstane nízké.
- Dojde-li k rekultivaci mezipaměti, zásady pro výběr položek mezipaměti k vystěhovávání je stejné zásady výběru jako pro zřízené výpočetní databáze, když je vysoký tlak paměti.
- Velikost mezipaměti se nikdy nesnižuje pod mezní hodnotu min paměti, jak je definováno min vCores, které lze konfigurovat.

V bezserverových i zřízených výpočetních databázích mohou být položky mezipaměti vyřazeny, pokud se používá všechna dostupná paměť.

#### <a name="cache-hydration"></a>Hydratace mezipaměti

Mezipaměť SQL roste s daty načíst z disku stejným způsobem a stejnou rychlostí jako pro zřízené databáze. Když je databáze zaneprázdněna, je povoleno, aby mezipaměť rostla bez omezení až do maximálního limitu paměti.

## <a name="autopausing-and-autoresuming"></a>Automatické pozastavení a automatické provádění

### <a name="autopausing"></a>Automatické pozastavení

Automatické pozastavení se aktivuje, pokud platí všechny následující podmínky po dobu trvání zpoždění automatického pozastavení:

- Počet relací = 0
- CPU = 0 pro zatížení uživatelů spuštěné ve fondu uživatelů

Možnost je k dispozici zakázat automatické pomíjení v případě potřeby.

Následující funkce nepodporují automatické pomíjení.  To znamená, že pokud jsou použity některé z následujících funkcí, databáze zůstane online bez ohledu na dobu trvání nečinnosti databáze:

- Geografická replikace (aktivní skupiny geografické replikace a automatické převzetí služeb při selhání).
- Dlouhodobé uchovávání záloh (LTR).
- Synchronizační databáze používaná v synchronizaci dat SQL.  Na rozdíl od synchronizace databází, hub a členské databáze podporují automatické poklepání.
- Databáze úloh používaných v elastických úlohách.

Automatické pozastavení je dočasně zabráněno během nasazení některých aktualizací služby, které vyžadují databázi online.  V takových případech automatické pominování se stane povoleno znovu po dokončení aktualizace služby.

### <a name="autoresuming"></a>Autoresuming

Autorizace se spustí, pokud je kdykoli splněna některá z následujících podmínek:

|Funkce|Aktivační událost automatického obnovení|
|---|---|
|Ověřování a autorizace|Přihlásit|
|Detekce hrozeb|Povolení nebo zakázání nastavení detekce hrozeb na úrovni databáze nebo serveru.<br>Změna nastavení zjišťování hrozeb na úrovni databáze nebo serveru.|
|Zjišťování a klasifikace dat|Přidání, úprava, odstranění nebo zobrazení popisků citlivosti|
|Auditování|Zobrazení záznamů auditování.<br>Aktualizace nebo zobrazení zásad auditování.|
|Maskování dat|Přidání, úprava, odstranění nebo zobrazení pravidel maskování dat|
|Transparentní šifrování dat|Zobrazení stavu nebo stavu transparentního šifrování dat|
|Úložiště dat dotazu (výkonu)|Úprava nebo zobrazení nastavení úložiště dotazů|
|Automatické ladění|Aplikace a ověření doporučení pro automatické ladění, jako je automatické indexování|
|Kopírování databáze|Vytvořte databázi jako kopii.<br>Exportovat do souboru BACPAC.|
|Synchronizace dat SQL|Synchronizace mezi databázemi rozbočovače a členy, které běží podle konfigurovatelného plánu nebo jsou prováděny ručně|
|Úprava určitých metadat databáze|Přidání nových značek databáze.<br>Změna maximálnívirtuální jádra, min virtuální jádra nebo automatické pozastavení zpoždění.|
|SQL Server Management Studio (SSMS)|Použití verzí SSMS starších než 18.1 a otevření nového okna dotazu pro libovolnou databázi na serveru obnoví všechny automaticky pozastavené databáze na stejném serveru. K tomuto chování nedochází, pokud používáte SSMS verze 18.1 nebo novější.|

Autorizace se také aktivuje během nasazení některých aktualizací služby, které vyžadují, aby databáze byla online.

### <a name="connectivity"></a>Připojení

Pokud je databáze bez serveru pozastavena, bude první přihlášení pokračovat v databázi a vrátí chybu oznamující, že databáze není k dispozici s kódem chyby 40613. Po obnovení databáze musí být přihlášení opakováno, aby bylo možné navázat připojení. Databázové klienty s logikou opakování připojení by neměly být nutné měnit.

### <a name="latency"></a>Latence

Latence automatického obnovení a automatického pozastavení databáze bez serveru je obvykle řádná od 1 minuty na automatické obnovení a 1-10 minut na automatické pozastavení.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Transparentní šifrování dat spravované zákazníkem (BYOK)

Pokud je při odstranění nebo odvolání klíče automaticky pozastaveno šifrování [transparentních dat](transparent-data-encryption-byok-azure-sql.md) spravované zákazníkem (BYOK) a databáze bez serveru je automaticky pozastavena, zůstane databáze ve stavu automatického pozastaveného.  V tomto případě při další obnovení databáze databáze zůstane online, dokud jeho stav přechody na nepřístupné po přibližně 10 minut nebo méně.  Jakmile databáze stane nepřístupné, proces obnovení je stejný jako pro zřízené výpočetní databáze.  Pokud je databáze bez serveru online, když dojde k odstranění nebo odvolání klíče, pak databáze také stane nepřístupné po přibližně 10 minut nebo méně stejným způsobem jako u zřízených výpočetních databází.

## <a name="onboarding-into-serverless-compute-tier"></a>Registrace do výpočetní úrovně bez serveru

Vytvoření nové databáze nebo přesunutí existující databáze do výpočetní vrstvy bez serveru se řídí stejným vzorem jako vytvoření nové databáze v zřízené výpočetní vrstvě a zahrnuje následující dva kroky.

1. Zadejte název cíle služby. Cíl služby předepisuje úroveň služby, generování hardwaru a maximální virtuální jádra. V následující tabulce jsou uvedeny možnosti cíle služby:

   |Název cíle služby|Úroveň služeb|Generování hardwaru|Maximální virtuální jádra|
   |---|---|---|---|
   |GP_S_Gen5_1|Pro obecné účely|Gen5 (Gen5)|1|
   |GP_S_Gen5_2|Pro obecné účely|Gen5 (Gen5)|2|
   |GP_S_Gen5_4|Pro obecné účely|Gen5 (Gen5)|4|
   |GP_S_Gen5_6|Pro obecné účely|Gen5 (Gen5)|6|
   |GP_S_Gen5_8|Pro obecné účely|Gen5 (Gen5)|8|
   |GP_S_Gen5_10|Pro obecné účely|Gen5 (Gen5)|10|
   |GP_S_Gen5_12|Pro obecné účely|Gen5 (Gen5)|12|
   |GP_S_Gen5_14|Pro obecné účely|Gen5 (Gen5)|14|
   |GP_S_Gen5_16|Pro obecné účely|Gen5 (Gen5)|16|

2. Volitelně můžete zadat min vCores a zpoždění automatického pozastavení, chcete-li změnit výchozí hodnoty. V následující tabulce jsou uvedeny dostupné hodnoty pro tyto parametry.

   |Parametr|Volby hodnoty|Výchozí hodnota|
   |---|---|---|---|
   |Min virtuální jádra|Závisí na maximálních nakonfigurovaných virtuálních jádrech – viz [omezení prostředků](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 virtuálních jader|
   |Zpoždění automatického pozastavení|Minimální: 60 minut (1 hodina)<br>Maximálně: 10080 minut (7 dní)<br>Přírůstky: 60 minut<br>Zakázat automatické pozastavení: -1|60 minut|


### <a name="create-new-database-in-serverless-compute-tier"></a>Vytvoření nové databáze v výpočetní vrstvě bez serveru 

Následující příklady vytvořit novou databázi v výpočetní vrstvě bez serveru. Příklady explicitně určují min vCores, max vCores a zpoždění automatického pozastavení.

#### <a name="use-azure-portal"></a>Použití webu Azure Portal

Viz [Úvodní příručka: Vytvoření jedné databáze v Azure SQL Database pomocí portálu Azure](sql-database-single-database-get-started.md).


#### <a name="use-powershell"></a>Použití prostředí PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>Použití Azure CLI

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Použití Transact-SQL (T-SQL)

Následující příklad vytvoří novou databázi v výpočetní vrstvě bez serveru.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Podrobnosti naleznete v tématu [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Přesunutí databáze z zřízené výpočetní vrstvy na výpočetní úroveň bez serveru

Následující příklady přesunout databázi z zřízeného výpočetní vrstvy do výpočetní vrstvy bez serveru. Příklady explicitně určují min vCores, max vCores a zpoždění automatického pozastavení.

#### <a name="use-powershell"></a>Použití prostředí PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>Použití Azure CLI

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Použití Transact-SQL (T-SQL)

Následující příklad přesune databázi z zřízené výpočetní vrstvy do výpočetní vrstvy bez serveru.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Podrobnosti naleznete v tématu [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Přesunutí databáze z výpočetní vrstvy bez serveru do zřízené výpočetní vrstvy

Databázi bez serveru lze přesunout do zřízené výpočetní vrstvy stejným způsobem jako přesunutí zřízené výpočetní databáze do výpočetní vrstvy bez serveru.

## <a name="modifying-serverless-configuration"></a>Úprava konfigurace bez serveru

### <a name="use-powershell"></a>Použití prostředí PowerShell

Změna maximální nebo minimální virtuální jádra a zpoždění automatické podržení, se provádí pomocí [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) příkaz v prostředí PowerShell pomocí `MaxVcore`, `MinVcore`, a `AutoPauseDelayInMinutes` argumenty.

### <a name="use-azure-cli"></a>Použití Azure CLI

Úprava maximální nebo minimální virtuální jádra a zpoždění automatické podržení, se provádí pomocí příkazu `min-capacity` [az sql db update](/cli/azure/sql/db#az-sql-db-update) v Azure CLI pomocí `capacity`, , a `auto-pause-delay` argumenty.


## <a name="monitoring"></a>Monitorování

### <a name="resources-used-and-billed"></a>Použité a fakturované zdroje

Prostředky databáze bez serveru jsou zapouzdřeny podle balíčku aplikace, instance SQL a entit fondu uživatelských prostředků.

#### <a name="app-package"></a>Balíček aplikace

Balíček aplikace je vnější hranice správy prostředků pro databázi, bez ohledu na to, zda je databáze v bezserverové nebo zřízené výpočetní vrstvy. Balíček aplikace obsahuje instanci SQL a externí služby, které společně obor všechny uživatelské a systémové prostředky používané databáze v databázi SQL. Příklady externích služeb zahrnují R a fulltextové vyhledávání. Instance SQL obecně dominuje celkové využití prostředků v rámci balíčku aplikace.

#### <a name="user-resource-pool"></a>Fond uživatelských zdrojů

Fond prostředků uživatele je vnitřní hranice správy prostředků pro databázi, bez ohledu na to, zda je databáze v bezserverové nebo zřízené výpočetní vrstvě. Obory fondu prostředků uživatele CPU a IO pro zatížení uživatelů generované dotazy DDL, jako jsou například dotazy CREATE a ALTER a DML, jako je SELECT, INSERT, UPDATE a DELETE. Tyto dotazy obecně představují nejpodstatnější podíl využití v rámci balíčku aplikace.

### <a name="metrics"></a>Metriky

Metriky pro sledování využití prostředků balíčku aplikace a fondu uživatelů databáze bez serveru jsou uvedeny v následující tabulce:

|Entita|Metrika|Popis|Jednotky|
|---|---|---|---|
|Balíček aplikace|app_cpu_percent|Procento virtuálních jader používaných aplikací vzhledem k maximálnímu počtu virtuálních jader povolené pro aplikaci.|Procento|
|Balíček aplikace|app_cpu_billed|Množství výpočetních prostředků fakturovaných za aplikaci během vykazovaného období. Částka zaplacená během tohoto období je součinem této metriky a jednotkové ceny virtuálního jádra. <br><br>Hodnoty této metriky jsou určeny agregací v čase maximální využití procesoru a paměti používané každou sekundu. Pokud je použitá částka menší než minimální zřízené množství stanovené minimálními virtuálními jádry a minimální pamětí, bude se účtovat minimální zřízené množství.Za účelem porovnání procesoru s pamětí pro účely fakturace je paměť normalizována na jednotky virtuálních jader změnou měřítka velikosti paměti v GB o 3 GB na virtuální jádro.|virtuální jádro sekund|
|Balíček aplikace|app_memory_percent|Procento paměti používané aplikací vzhledem k maximální paměti povolené pro aplikaci.|Procento|
|Fond uživatelů|cpu_percent|Procento virtuálních jader používaných zatížením uživatelů vzhledem k maximálnímu počtu virtuálních jader povolené pro zatížení uživatelů.|Procento|
|Fond uživatelů|data_IO_percent|Procento vipos dat používaných zatížení uživatele vzhledem k maximální datové vipoje povoleno pro zatížení uživatele.|Procento|
|Fond uživatelů|log_IO_percent|Procento protokolu MB/s používané zatížení uživatele vzhledem k maximální log MB/s povoleno pro zatížení uživatele.|Procento|
|Fond uživatelů|workers_percent|Procento pracovníků používaných zatížením uživatelů vzhledem k maximálnímu počtu pracovníků povolených pro zatížení uživatelů.|Procento|
|Fond uživatelů|sessions_percent|Procento relací používaných zatížením uživatelů vzhledem k maximálnímu počtu relací povolených pro zatížení uživatelů.|Procento|

### <a name="pause-and-resume-status"></a>Pozastavení a obnovení stavu

Na webu Azure Portal se stav databáze zobrazí v podokně přehledu serveru se seznamem databází, které obsahuje. Stav databáze se také zobrazí v podokně přehledu databáze.

Pomocí následujících příkazů se dotazuje na stav pozastavení a obnovení databáze:

#### <a name="use-powershell"></a>Použití prostředí PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>Použití Azure CLI

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Omezení prostředků

Omezení prostředků naleznete v tématu [výpočetní vrstva bez serveru](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Fakturace

Množství účtovaných výpočetních prostředků je maximální využití procesoru a paměti používané každou sekundu. Pokud je množství použitého procesoru a využité paměti menší než minimální částka zřízená pro každou z nich, bude se účtuje zřízené množství. Za účelem porovnání procesoru s pamětí pro účely fakturace je paměť normalizována na jednotky virtuálních jader změnou měřítka velikosti paměti v GB o 3 GB na virtuální jádro.

- **Účtovaný prostředek**: PROCESOR a paměť
- **Fakturovaná částka**: jednotková cena virtuálního jádra * max (min vCore, použitá virtuální jádra, min paměť GB * 1/3, paměť GB použité * 1/3) 
- **Frekvence fakturace**: Za sekundu

Jednotková cena virtuálního jádra je cena za virtuální jádro za sekundu. Konkrétní jednotkové ceny v dané oblasti najdete na [stránce s cenami databáze Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/) Database.

Množství účtovaných výpočetních prostředků je vystaveno následující metrikou:

- **Metrika:** app_cpu_billed (virtuální jádro sekund)
- **Definice:** max (min vCores, použitá virtuální jádra, min paměť GB * 1/3, paměť GB použité * 1/3)
- **Frekvence hlášení**: Za minutu

Toto množství se vypočítá každou sekundu a agreguje se za 1 minutu.

Zvažte databázi bez serveru nakonfigurovanou s 1 min virtuálními jádry a 4 max virtuálními jádry.  To odpovídá přibližně 3 GB min paměti a 12 GB maximální paměti.  Předpokládejme, že zpoždění automatického pozastavení je nastaveno na 6 hodin a zatížení databáze je aktivní během prvních 2 hodin 24 hodin a jinak neaktivní.    

V tomto případě se databáze účtuje pro výpočetní prostředky a úložiště během prvních 8 hodin.  I když databáze je neaktivní začíná po druhé hodině, stále se účtuje pro výpočetní prostředky v následujících 6 hodin na základě minimální výpočetní zřízené, zatímco databáze je online.  Pouze úložiště se účtuje během zbývající části období 24 hodin, zatímco databáze je pozastavena.

Přesněji řečeno, účet za výpočetní výkon v tomto příkladu se vypočítá takto:

|Časový interval|virtuální jádra používaná každou sekundu|GB používá každou sekundu|Vypočítat dimenzi fakturované|vCore sekund y účtované v časovém intervalu|
|---|---|---|---|---|
|0:00-1:00|4|9|použitá virtuální jádra|4 virtuální jádra * 3600 sekund = 14400 virtuálních jader|
|1:00-2:00|1|12|Využitá paměť|12 GB * 1/3 * 3600 sekund = 14400 virtuálních jader sekund|
|2:00-8:00|0|0|Min paměti zřízené|3 GB * 1/3 * 21600 sekund = 21600 virtuálních jader sekund|
|8:00-24:00|0|0|Při pozastavení se neúčtují žádné výpočty.|0 sekund virtuálního jádra|
|Celkový počet sekund virtuálního jádra fakturovaných za 24 hodin||||50400 virtuálních jader sekund|

Předpokládejme, že cena výpočetní jednotky je $0.000145/vCore/second.  Pak výpočetní účtované za toto 24hodinové období je součin výpočetní jednotkové ceny a sekund virtuálního jádra: $0.000145/vCore/second * 50400 vCore sekund ~ $7.31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Hybridní výhody Azure a rezervovaná kapacita

Azure Hybrid Benefit (AHB) a rezervované kapacity slevy se nevztahují na výpočetní vrstvu bez serveru.

## <a name="available-regions"></a>Dostupné oblasti

Výpočetní úroveň bez serveru je dostupná po celém světě kromě následujících oblastí: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, Velká Británie – sever, Velká Británie – jih 2, Střed USA – západ a US Gov Central (Iowa).

## <a name="next-steps"></a>Další kroky

- Další informace najdete v [tématu Úvodní příručka: Vytvoření jedné databáze v Azure SQL Database pomocí webu Azure Portal](sql-database-single-database-get-started.md).
- Omezení prostředků naleznete v [tématu Limity prostředků výpočetní úrovně bez serveru](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
