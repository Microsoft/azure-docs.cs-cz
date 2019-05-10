---
title: Azure SQL Database bez serveru (ve verzi preview) | Dokumentace Microsoftu
description: Tento článek popisuje nové úrovni výpočetní prostředí a porovná ho s existující zřízených výpočetních úrovní
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 7f850f309034d128efef89ea842db41d35b8491e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235737"
---
# <a name="sql-database-serverless-preview"></a>Bez serveru SQL Database (preview)

## <a name="what-is-the-serverless-compute-tier"></a>Co je výpočetní prostředí úroveň

Bez serveru SQL Database (preview) je výpočetní úroveň, která kusovník pro částku compute používané izolované databáze na základě za sekundu. Bez serveru je cena – výkon – optimalizované pro izolované databáze s vzorcům používání vzory, které si může dovolit některých zpoždění při zahřívání výpočetní po období nečinnosti využití.
Naopak veřejně dostupným nabídkám v SQL Database pro částku z informací o dnešku vyúčtování výpočetní zřízené po hodinách. Tato zřízené výpočetní úroveň je cena – výkon – optimalizovaný pro izolované databáze nebo elastické fondy s vyšší průměrné využití, které nemůžete dovolit případné zpoždění při zahřívání výpočetní prostředky.

Databáze ve vrstvě počítače bez serveru je ve výpočetní oblasti, kterou můžete použít a prodlevu autopause parametrizované.

![bez serveru fakturace](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Výkon

- `MinVcore` a `MaxVcore` jsou konfigurovatelné parametry, které definují oblasti k dispozici výpočetní kapacity pro databázi. Omezení paměti a vstupně-výstupní operace jsou úměrná zadaný rozsah vCore.  
- Autopause zpoždění je konfigurovatelný parametr, který definuje dobu, po který databáze musí být neaktivní, než je automaticky pozastaví. Databáze se automaticky obnoví při dalším přihlášení.

### <a name="pricing"></a>Ceny

- Celkový účet za databázi bez serveru je souhrn faktury výpočetní prostředky a faktura za úložiště.
Fakturace za výpočetní prostředky podle množství používá virtuálních jader a paměti operací za sekundu.
- Minimální výpočetní prostředky, které se účtují podle min virtuálních jader a paměti min.
- Zatímco databáze je pozastavený, účtuje se pouze úložiště.

## <a name="scenarios"></a>Scénáře

Bez serveru je optimalizováno pro izolované databáze s vzorcům používání vzory, které si může dovolit některých zpoždění při zahřívání výpočetní po období nečinnosti využití cena – výkon. Zřízené výpočetní úroveň je optimalizovaná pro jeden, nebo součástí fondu databází s vyšší průměrné využití, které nemůžete dovolit případné zpoždění při zahřívání výpočetní cena – výkon.

Následující tabulka porovnává úroveň výpočetní prostředí s zřízených výpočetních úrovní:

||Bezserverové výpočetní prostředí|Zřízené výpočetní prostředky|
|---|---|---|
|**Typické použití**|Databáze s využitím vzorcům, nepředvídatelné proložená neaktivní období|Databáze nebo elastické fondy s více pravidelné používání|
|**Úsilí vynaložené na správu výkonu**|Nižší|Vyšší|
|**COMPUTE škálování**|Automaticky|Ručně|
|**COMPUTE rychlost odezvy**|Nižší za neaktivní období|Okamžitý|
|**Členitost fakturace**|Za sekundu|Za hodinu|
|

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scénáře, které jsou vhodné pro výpočetní prostředí

- Izolované databáze s vzory vzorcům používání spolu s obdobími nečinnosti může přinést cena úspory se počítají pro fakturaci za sekundu množství využitých výpočtů.
- Izolované databáze s požadavky na prostředky, které je těžké předpovědět a Zákazníci, kteří dávají přednost delegovat velikosti výpočetní prostředky ve službě.
- Izolované databáze na úrovni zřízených výpočetních, které často měnit úrovně výkonu.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scénáře, které jsou vhodné pro zřízených výpočetních

- Izolované databáze s více běžných a další podstatné výpočetní využití v čase.
- Databáze, které nemůžou tolerovat výkonu kompromis vyplývající z více časté paměti ořezávání nebo zpozdit autoresuming z pozastaveného stavu.
- Více databázím s vzorcům používání vzory, které může být konsolidovány do jednoho serveru a použít na lepší optimalizaci ceny elastických fondů.


## <a name="purchasing-model-and-service-tier"></a>Zakoupení modelů a úrovni služeb

SQL Database bez serveru je aktuálně podporuje jenom v úrovni General Purpose na 5. generace hardwaru ve virtuálních jader, model nákupu.

## <a name="autoscaling"></a>Automatické škálování

### <a name="scaling-responsiveness"></a>Škálování rychlost odezvy

Obecně jsou databáze spustit na počítači s dostatečnou kapacitu k splňují požadavky na prostředky bez přerušení pro jakýkoli objem výpočetní požadovaná v rámci omezení `maxVcores` hodnotu. V některých případech automaticky Vyrovnávání zatížení v případě, že počítač je schopen splnit požadavky na prostředky během několika minut. Databáze zůstane online při načítání vyrovnávání s výjimkou během krátké doby na konci operaci připojení se zahodí.

### <a name="memory-management"></a>Správa paměti

Paměť pro databáze bez serveru je uvolněn více často pro zřízené databáze. Toto chování je potřeba řízení nákladů ve službě bez serveru. Na rozdíl od zřízených výpočetních je uvolnit paměť z mezipaměti SQL z databáze bez serveru při nízkém využití procesoru nebo mezipaměť.

## <a name="autopause-and-autoresume"></a>Autopause a autoresume

### <a name="autopause"></a>Autopause

Autopause se aktivuje, pokud všechny následující podmínky platí po dobu trvání zpoždění autopause:

- Počet relací = 0
- Využití procesoru = 0 (pro uživatel úlohu běžící ve fondu uživatele)

Poskytuje možnost zakázat autopause v případě potřeby.

### <a name="autoresume"></a>Autoresume

Pokud některý z následujících podmínek jsou splněny, kdykoli se aktivuje Autoresume:

|Funkce|Autoresume triggeru|
|---|---|
|Ověřování a autorizace|Přihlásit|
|Detekce hrozby|Povolit nebo zakázat nastavení detekce hrozeb na úrovni databáze nebo serveru<br>Úprava nastavení detekce hrozeb na úrovni databáze nebo serveru|
|Zjišťování a klasifikace dat|Přidání, úprava, odstranění nebo zobrazení popisků citlivosti|
|Auditování|Zobrazení záznamů auditu.<br>Aktualizace nebo aktualizace zobrazení zásady auditu|
|Maskování dat|Přidání, úprava, odstranění nebo zobrazení pravidla maskování dat|
|Transparentní šifrování dat|Zobrazení stavu nebo stavu transparentního šifrování dat|
|Úložiště dat dotazů (výkon)|Úprava nebo zobrazení nastavení úložiště dotazů. Automatické ladění|
|Automatického ladění|Aplikace a ověření doporučení automatického ladění, jako je například automatické indexování|
|Kopírování databáze|Vytvoření databáze jako kopii<br>Export do souboru BACPAC|
|Synchronizace dat SQL|Synchronizace mezi databáze centra a člen spouštěné v rámci nakonfigurovatelného plánu nebo jsou prováděny ručně|
|Úprava určitá metadata databáze|Přidání nové značky databáze<br>Změna maximální počet virtuálních jader, za minutu virtuálních jader, autopause zpoždění|
|SQL Server Management Studio (SSMS)|Pomocí aplikace SSMS verze 18 a otevřete nové okno dotazu pro libovolnou databázi na serveru bude pokračovat, všechny databáze automaticky pozastaví na stejném serveru. K tomuto chování nedojde, pokud pomocí aplikace SSMS verze 17.9.1 s podporou technologie IntelliSense-vypnut.|

### <a name="connectivity"></a>Možnosti připojení

Když bez serveru databáze je pozastavená, bude při prvním přihlášení databázi obnovit a vrátí chybu s informacemi o tom, že databáze je k dispozici. Po obnovení databáze přihlášení je nutné zopakovat k navázání připojení. Databáze klientů se logika opakovaných pokusů připojení by neměl muset upravit.

### <a name="latency"></a>Latence

Latence autopause nebo autoresume databáze bez serveru je obecně v řádu 1 minuta.

### <a name="feature-support"></a>Podpora funkce

Tyto funkce nepodporují autopausing a autoresuming. To znamená pokud jsou používány některou z následujících funkcí, potom databáze zůstane online bez ohledu na to databáze nečinnosti:

- Geografická replikace (aktivní geografickou replikaci a automatické převzetí služeb při selhání skupiny)
- Dlouhodobé uchovávání záloh (LTR)
- Synchronizace databáze použitý v synchronizaci dat SQL.


## <a name="on-boarding-into-the-serverless-compute-tier"></a>V rámci zprovozňování do vrstvy výpočetní prostředí

Vytváří se nová databáze nebo přesunutí že existující databáze do vrstvy výpočetní prostředí používá stejný vzor jako vytváření s novou databází ve zřízené výpočetní vrstvě a zahrnuje následující kroky:

1. Zadejte název cíle služby. V následující tabulce jsou uvedeny vrstvy služby k dispozici a velikostí výpočetních aktuálně k dispozici ve verzi public preview.

   |Úroveň služby|Vypočítat velikost|
   |---|---|
   |Obecné použití|GP_S_Gen5_1|
   |Obecné použití|GP_S_Gen5_2|
   |Obecné použití|GP_S_Gen5_4|

2. Volitelně můžete Určete minimální virtuálních jader a autopause zpoždění, chcete-li změnit výchozí hodnoty. V následující tabulce jsou uvedeny dostupné hodnoty pro tyto parametry.

   |Parametr|Hodnota možnosti|Výchozí hodnota|
   |---|---|---|---|
   |Minimální počet virtuálních jader|{0,5, 1, 2, 4} není větší než maximální počet virtuálních jader|0,5 virtuálních jader|
   |Autopause zpoždění|Min: 360 minut (6 hodin)<br>Max: 10 080 minut (7 dní)<br>Krocích: 60 minut<br>Zakázat autopause: -1|360 minut|

> [!NOTE]
> Pomocí jazyka T-SQL přesunout existující databázi do bez serveru, nebo změňte jeho velikost výpočetních se aktuálně nepodporuje, ale můžete to udělat pomocí webu Azure portal nebo Powershellu.

### <a name="create-new-database-using-the-azure-portal"></a>Vytvořit novou databázi pomocí webu Azure portal

Zobrazit [rychlý start: Vytvoření izolované databáze ve službě Azure SQL Database pomocí webu Azure portal](sql-database-single-database-get-started.md).

### <a name="create-new-database-using-powershell"></a>Vytvoření nové databáze pomocí Powershellu

Následující příklad vytvoří novou databázi na úrovni výpočetní prostředí určené cíl služby s názvem GP_S_Gen5_4 s použitím výchozích hodnot pro zpoždění virtuálních jader a autopause min.

Bez serveru vyžaduje novější verzi Powershellu než je aktuálně ve galerii, takže spuštění `Update-Module Az.Sql` zobrazíte nejnovější rutiny bez serveru povolené.

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
  -AutoPauseDelay 720
```

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>Přesunout existující databázi do vrstvy výpočetní prostředí

Následující příklad přesune existující jedné databáze z úrovně zřízených výpočetních do vrstvy výpočetní prostředí. Tento příklad používá výchozí hodnoty pro virtuální jádra minimální, maximální počet virtuálních jader a autopause zpoždění.

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
  -AutoPauseDelay 1440
```

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>Přesunutí databáze mimo úroveň výpočetní prostředí

Databáze bez serveru můžete přesunout do vrstvy zřízených výpočetních stejným způsobem jako přesunutí databáze zřízených výpočetních do vrstvy výpočetní prostředí.

## <a name="modify-serverless-configuration-parameters"></a>Upravit parametry konfigurace bez serveru

### <a name="maximum-vcores"></a>Maximální počet virtuálních jader

Změna maximální virtuálních jader je prováděno pomocí [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) příkazu v Powershellu pomocí `MaxVcore` argument.

### <a name="minimum-vcores"></a>Minimální počet virtuálních jader

Změna maximální virtuálních jader je prováděno pomocí [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) příkazu v Powershellu pomocí `MinVcore` argument.

### <a name="autopause-delay"></a>Autopause zpoždění

Změna maximální virtuálních jader je prováděno pomocí [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) příkazu v Powershellu pomocí `AutoPauseDelay` argument.

## <a name="monitor-serverless-database"></a>Monitorování databáze bez serveru

### <a name="resources-used-and-billed"></a>Prostředky používá a účtují

Prostředky databáze bez serveru jsou zapouzdřena objektem následující entity:

#### <a name="app-package"></a>Balíček aplikace

Balíček aplikace je vnější hranice většinu prostředků správy pro databázi, bez ohledu na to, jestli je databáze v bez serveru nebo zřízené výpočetní vrstvě. Balíček aplikace obsahuje instanci SQL a externí služby společně oboru všechny uživatele a systémové prostředky využívané třídou databáze ve službě SQL Database. Příklady externích služeb: R a fulltextové vyhledávání. SQL instance obvykle dominuje celkové využití prostředků v balíčku aplikace.

#### <a name="user-resource-pool"></a>Uživatelským fondem zdrojů

Uživatelským fondem zdrojů je vnitřní většina hranice správy prostředků pro databázi, bez ohledu na to, jestli je databáze v bez serveru nebo zřízené výpočetní vrstvě. Uživatelským fondem zdrojů obory procesoru a vstupů/výstupů uživatelské zatížení generovaných dotazů DDL (například CREATE, ALTER, atd.) a dotazech DML (třeba SELECT, INSERT, UPDATE, DELETE, atd.). Tyto dotazy obvykle představují nejčastěji podstatnou část využití v rámci balíčku aplikace.

### <a name="metrics"></a>Metriky

|Entita|Metrika|Popis|Jednotky|
|---|---|---|---|
|Balíček aplikace|app_cpu_percent|Procentuální podíl virtuálních jader, používat aplikace vzhledem k maximální počet virtuálních jader pro povolené aplikace.|Procento|
|Balíček aplikace|app_cpu_billed|Objem výpočtů účtovat aplikace během období vytváření sestav. Placené během této doby je produkt tuto metriku a je cena ze jednotku vCore.<br>Hodnoty tato metrika se určují na základě agregace v čase používá maximální využití procesoru a paměti používá každou sekundu.<br>Využitý počet je menší než minimální velikost Zřizuje se jako sada min virtuálních jader a paměti min, se účtuje minimální velikost zřízené.  Aby bylo možné porovnat procesoru s pamětí pro účely fakturace, je paměť ve změny měřítka množství paměti v Gigabajtech o 3 GB za vCore normalizován do jednotek virtuálních jader.|vCore sekund|
|Balíček aplikace|app_memory_percent|Procento paměti používá aplikace vzhledem k maximální povolenou paměť pro aplikaci.|Procento|
|Uživatel fondu|cpu_percent|Procentuální podíl virtuálních jader, které používá uživatelské zatížení vzhledem k maximální počet virtuálních jader pro povolené pro uživatelské zatížení.|Procento|
|Uživatel fondu|data_IO_percent|Procento dat vstupně-výstupních operací používají uživatelské zatížení souvisejícího s daty maximální IOPS povolené pro uživatelské zatížení.|Procento|
|Uživatel fondu|log_IO_percent|Procento protokolu pro uživatelské zatížení povoleny MB/s, které používá uživatelské zatížení vzhledem k maximální protokolu MB/s.|Procento|
|Uživatel fondu|workers_percent|Procento pracovních procesů využívaných zatížení uživatelů vzhledem k maximální počet pracovních procesů povolen pro uživatelské zatížení.|Procento|
|Uživatel fondu|sessions_percent|Procento relací použít úlohu uživatele vzhledem k maximálního počtu relací povolená pro uživatelské zatížení.|Procento|
____

> [!NOTE]
> Metriky na webu Azure Portal jsou k dispozici v podokně databáze pro izolované databáze podle **monitorování**.

### <a name="pause-and-resume-status"></a>Pozastavit a obnovit stav

Na webu Azure Portal zobrazí se stav databáze v podokně s přehledem serveru, který obsahuje seznam databází, které obsahuje. Stav databáze se také zobrazí v podokně s přehledem pro databázi.

Dotazování pozastavení a obnovení stavu databáze pomocí následujícího příkazu Powershellu:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Omezení prostředků

Omezení prostředků najdete v tématu [úroveň výpočetní prostředí](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>Fakturace

Objem výpočtů fakturovat každou sekundu je maximální využití procesoru a paměti používá každou sekundu. Pokud procento využití procesoru a použité paměti je menší než minimální velikost pro všechny zřízené, se účtuje zřízená velikost. Aby bylo možné porovnat procesoru s pamětí pro účely fakturace, je paměť ve změny měřítka množství paměti v Gigabajtech o 3 GB za vCore normalizován do jednotek virtuálních jader.

- **Prostředek účtuje**: Procesor a paměť
- **Fakturována částka ($)**: cena za jednotku – VCORE úrovně * max (min virtuálních jader, používá virtuální jádra, minimální paměť GB * 1/3 paměti využité GB * 1/3) 
- **Četnost fakturace**: Za sekundu

Objem výpočtů účtuje je zveřejněný prostřednictvím následující metriky:

- **Metrika**: app_cpu_billed (vCore v sekundách)
- **Definice**: maximální počet (minutu virtuálních jader, používá virtuální jádra, minimální paměť GB * 1/3 paměti využité GB * 1/3) *
- **Četnost hlášení**: Za minutu

> [!NOTE]
> \* Toto množství se počítá každou sekundu a agregovat více než 1 minuta.

**Příklad**: Vezměte v úvahu databázi GP_S_Gen5_4 pomocí následujícího využití po dobu jedné hodiny:

|Čas (hodiny: minuty)|app_cpu_billed (vCore v sekundách)|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1255|
||Celkem: 1631|

Předpokládejme, že je cena ze jednotku výpočetních je $0.2609/vCore/hour. Pak výpočetní účtuje za toto období hodinu se určuje pomocí následujícího vzorce: **$0.2609/vCore/hour * 1631 vCore sekund * 1 hodinu a 3 600 sekund = $0.1232**

## <a name="available-regions"></a>Dostupné oblasti

Bez serveru výpočetní úroveň je dostupná ve všech oblastech kromě následujících oblastech: Austrálie – střed, Čína – východ, Čína – sever, Francie – Jih, Německo – střed, Německo – severovýchod, Indie – Západ, Korea – Jih, Jihoafrická republika – Západ, Velká Británie – sever, Velká Británie – Jih, Velká Británie – západ a střed USA – západ

## <a name="next-steps"></a>Další postup

Omezení prostředků najdete v tématu [bez serveru – compute úrovně omezení prostředků](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).