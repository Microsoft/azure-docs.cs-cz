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
ms.date: 07/05/2019
ms.openlocfilehash: 5a1a5ea39c9c0ed8973e1ecfa46977d2d06f83e7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603612"
---
# <a name="azure-sql-database-serverless-preview"></a>Azure SQL Database bez serveru (preview)

Azure SQL Database, bez serveru (preview) je výpočetní úroveň pro izolované databáze, která se automaticky škáluje výpočetní prostředky na základě zátěži a účtuje velikost výpočetních využívá za sekundu. Databáze na úrovni výpočetní prostředí také automaticky pozastaví neaktivní období, kdy pouze úložiště se účtuje a automaticky obnoví databáze po návratu aktivity.

## <a name="serverless-compute-tier"></a>Bezserverová výpočetní úroveň

Na úrovni výpočetní prostředí pro izolovanou databázi je parametrizován rozsah automatické škálování výpočetních a prodlevu autopause.  Konfigurace tyto parametry obrazce výkon databáze a výpočetní náklady.

![bez serveru fakturace](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Konfigurace výkonu

- **Minimální virtuálních jader** a **virtuálních jader pro maximální** jsou konfigurovatelné parametry, které definují oblasti k dispozici výpočetní kapacity pro databázi. Omezení paměti a vstupně-výstupní operace jsou úměrná zadaný rozsah vCore.  
- **Autopause zpoždění** je konfigurovatelný parametr, který definuje časový interval, databáze musí být neaktivní, než je automaticky pozastaví. Databáze se automaticky obnoví při příštím přihlášení nebo jiné aktivity.  Alternativně je možné zakázat autopausing.

### <a name="cost"></a>Náklady

- Náklady na databáze bez serveru je součtem výpočetní náklady a náklady na úložiště.
- Při využití služby compute se mezi minimální a maximální limity nakonfigurované, výpočetní náklady závisí na vCore a využité paměti.
- Při využití služby compute je nižší než minimální omezení nakonfigurované, náklady na výpočetní je na základě virtuálních jader pro minimální a minimální pamětí nakonfigurovaných.
- Pokud databáze je pozastavený, náklady na výpočetní prostředky je nula a pouze náklady na úložiště se účtují.
- Náklady na úložiště, je určena stejným způsobem jako v zřízených výpočetních vrstvy.

Další podrobnosti o nákladech, naleznete v tématu [fakturace](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scénáře

Bez serveru je optimalizováno pro izolované databáze s přerušovaným, nepředvídatelné využití vzorky, které si může dovolit některých zpoždění při zahřívání výpočetní po období nečinnosti využití cena – výkon. Naproti tomu zřízené výpočetní úroveň je optimalizovaný pro izolované databáze nebo více databází v elastických fondech s vyšší průměrné využití, které nemůžete dovolit případné zpoždění při zahřívání výpočetní cena – výkon.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scénáře, které jsou vhodné pro výpočetní prostředí

- Izolované databáze s nepředvídatelnými vzorům přerušované promíchaný s obdobími nečinnosti a nižší výpočetní průměrné využití v čase.
- Izolované databáze na úroveň zřízených výpočetních, které jsou často měřítka a Zákazníci, kteří dávají přednost delegovat compute, změny měřítka ve službě.
- Nové izolované databáze bez použití historie kde výpočty velikosti je obtížné nebo nebylo možné odhadnout před nasazením ve službě SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scénáře, které jsou vhodné pro zřízených výpočetních

- Izolované databáze s více pravidelné a předvídatelné vzorců používání a vyšší průměr výpočetní využití v čase.
- Databáze, které nemůžou tolerovat výkonu kompromis vyplývající z více časté paměti ořezávání nebo zpozdit autoresuming z pozastaveného stavu.
- Více databází vzorcem využití krátkodobých, nepředvídatelné, může být konsolidovány do elastických fondů na lepší optimalizaci cena – výkon.

## <a name="comparison-with-provisioned-compute-tier"></a>Porovnání s zřízených výpočetních úrovní

Následující tabulka shrnuje rozdíly mezi výpočetní prostředí a na úrovni zřízených výpočetních:

| | **Výpočetní prostředí** | **Zřízených výpočetních** |
|:---|:---|:---|
|**Vzoru používání databáze**| Občasné nepředvídatelné využití s nižší výpočetní průměrné využití v čase. |  Více běžných vzorů využití s vyšší průměr výpočetní využití v čase nebo více databází pomocí elastických fondů.|
| **Úsilí vynaložené na správu výkonu** |Nižší|Vyšší|
|**COMPUTE škálování**|Automatické|Manual|
|**COMPUTE rychlost odezvy**|Nižší za neaktivní období|Okamžité|
|**Členitost fakturace**|Za sekundu|Za hodinu|

## <a name="purchasing-model-and-service-tier"></a>Zakoupení modelů a úrovni služeb

SQL Database bez serveru je aktuálně podporuje jenom v úrovni General Purpose na 5. generace hardwaru ve virtuálních jader, model nákupu.

## <a name="autoscaling"></a>Automatické škálování

### <a name="scaling-responsiveness"></a>Škálování rychlost odezvy

Obecně platí bez serveru databáze jsou spouštěny na počítači s dostatečnou kapacitu k splňují požadavky na prostředky bez přerušení pro jakýkoli objem výpočetní požadovaná v mezích limitů nastavit hodnotu maximální počet virtuálních jader. V některých případech automaticky Vyrovnávání zatížení v případě, že počítač je schopen splnit požadavky na prostředky během několika minut. Například pokud požadavky na prostředky jsou 4 virtuální jádra, ale jsou k dispozici pouze 2 virtuální jádra, pak ho může trvat až několik minut, než předtím, než jsou k dispozici 4 virtuální jádra pro vyrovnávání zatížení. Databáze zůstane online při načítání vyrovnávání s výjimkou během krátké doby na konci operaci připojení se zahodí.

### <a name="memory-management"></a>Správa paměti

Paměť pro databáze bez serveru je uvolněn více často než u databází, zřízených výpočetních. Toto chování je potřeba řízení nákladů ve službě bez serveru a může ovlivnit výkon.

#### <a name="cache-reclamation"></a>Recyklace mezipaměti

Na rozdíl od zřízených výpočetních databází je uvolnit paměť z mezipaměti SQL z databáze bez serveru při nízkém využití procesoru nebo mezipaměť.

- Využití mezipaměti se považuje za nízkou, pokud celková velikost nejčastěji naposledy použité spadající do určité položky mezipaměti pod prahovou hodnotu pro určitou dobu.
- Když se aktivuje mezipaměti recyklaci, cílovou velikost mezipaměti je postupně omezit na zlomek původní velikost a opětovné získání pokračuje pouze pokud zůstává na nízké využití.
- Pokud dojde k mezipaměti recyklaci, zásady pro výběr položky mezipaměti vyřazení je stejné zásady výběru jako u databáze zřízených výpočetních při přetížení paměti je vysoká.
- Velikost mezipaměti je nikdy snížit pod minimální limit paměti definované minimální virtuálních jader, kterého lze nakonfigurovat.

V bez serveru a zřízení výpočetních databází, mezipaměti, kterou položky mohou vyřadit, pokud se používá všechny dostupné paměti.

#### <a name="cache-hydration"></a>Dosazení dat do mezipaměti

Mezipaměti SQL roste, jak načíst data z disku stejným způsobem a se stejnou rychlostí jako zřízené databáze. Když je zaneprázdněná databáze, do mezipaměti může růst bez omezení až po limit maximální paměti.

## <a name="autopausing-and-autoresuming"></a>Autopausing a autoresuming

### <a name="autopausing"></a>Autopausing

Autopausing se aktivuje, pokud všechny následující podmínky platí po dobu trvání zpoždění autopause:

- Počet relací = 0
- Využití procesoru = 0 pro uživatel úlohu běžící ve fondu uživatele

Poskytuje možnost zakázat autopausing v případě potřeby.

Tyto funkce nepodporují autopausing.  To znamená pokud jsou používány některou z následujících funkcí, potom databáze zůstane online bez ohledu na to databáze nečinnosti:

- Geografická replikace (aktivní geografickou replikaci a automatické převzetí služeb při selhání skupiny).
- Dlouhodobé uchovávání záloh (LTR).
- Synchronizace databáze použitý v synchronizaci dat SQL.

Autopausing dočasně nemůže během nasazování některé aktualizace služby, které vyžadují databázi být online.  V takových případech bude autopausing povoleno znovu po dokončení aktualizace služby.

### <a name="autoresuming"></a>Autoresuming

Pokud některý z následujících podmínek jsou splněny, kdykoli se aktivuje Autoresuming:

|Funkce|Autoresume triggeru|
|---|---|
|Ověřování a autorizace|Přihlásit|
|Detekce hrozeb|Povolit nebo zakázat nastavení detekce hrozeb na úrovni databáze nebo serveru.<br>Úprava nastavení detekce hrozeb na úrovni databáze nebo serveru.|
|Zjišťování a klasifikace dat|Přidání, úprava, odstranění nebo zobrazení popisků citlivosti|
|Auditování|Zobrazení záznamů auditu.<br>Aktualizace nebo aktualizace zobrazení zásad auditu.|
|Maskování dat|Přidání, úprava, odstranění nebo zobrazení pravidla maskování dat|
|Transparentní šifrování dat|Zobrazení stavu nebo stavu transparentního šifrování dat|
|Úložiště dat dotazů (výkon)|Úprava nebo zobrazení nastavení úložiště dotazů. Automatické ladění|
|Automatického ladění|Aplikace a ověření doporučení automatického ladění, jako je například automatické indexování|
|Kopírování databáze|Vytvoření databáze jako kopii.<br>Export do souboru BACPAC.|
|Synchronizace dat SQL|Synchronizace mezi databáze centra a člen spouštěné v rámci nakonfigurovatelného plánu nebo jsou prováděny ručně|
|Úprava určitá metadata databáze|Přidání nové značky databáze.<br>Změna maximální počet virtuálních jader, min virtuálních jader nebo autopause zpoždění.|
|SQL Server Management Studio (SSMS)|Pomocí aplikace SSMS verze 18 a otevřete nové okno dotazu pro libovolnou databázi na serveru bude pokračovat, všechny databáze automaticky pozastaví na stejném serveru. K tomuto chování nedojde, pokud pomocí aplikace SSMS verze 17.9.1 s podporou technologie IntelliSense-vypnut.|

Autoresuming se také aktivuje při nasazení některé aktualizace služby, které vyžadují databázi být online.

### <a name="connectivity"></a>Připojení

Pokud databáze bez serveru je pozastavený, bude při prvním přihlášení databázi obnovit a vrátí chybu s informacemi o tom, že databáze je k dispozici s kódem chyby 40613. Po obnovení databáze přihlášení je nutné zopakovat k navázání připojení. Databáze klientů se logika opakovaných pokusů připojení by neměl muset upravit.

### <a name="latency"></a>Latence

Latence autoresume a autopause databáze bez serveru je obecně daly autopause 1 minuta autoresume a 1 až 10 minut.

## <a name="onboarding-into-serverless-compute-tier"></a>Registrace do vrstvy výpočetní prostředí

Vytváří se nová databáze nebo přesunutí že existující databáze do vrstvy výpočetní prostředí používá stejný vzor jako vytváření s novou databází ve výpočetní vrstva zřízený a zahrnuje následující kroky.

1. Zadejte název cíle služby. Cíl služby předepisuje úrovně, generace hardwaru a maximální počet virtuálních jader. V následující tabulce jsou uvedeny možnosti cíle služby:

   |Název cíle služby|Úroveň služeb|Generace hardwaru|Maximální počet virtuálních jader|
   |---|---|---|---|
   |GP_S_Gen5_1|Obecné použití|Gen5|1|
   |GP_S_Gen5_2|Obecné použití|Gen5|2|
   |GP_S_Gen5_4|Obecné použití|Gen5|4|

2. Volitelně můžete Určete minimální virtuálních jader a autopause zpoždění Chcete-li změnit výchozí hodnoty. V následující tabulce jsou uvedeny dostupné hodnoty pro tyto parametry.

   |Parametr|Hodnota možnosti|Výchozí hodnota|
   |---|---|---|---|
   |Min virtuálních jader|{0,5, 1, 2, 4} není větší než maximální počet virtuálních jader|0,5 virtuálních jader|
   |Autopause zpoždění|Minimálně: 60 minut (1 hodina)<br>Maximální počet: 10 080 minut (7 dní)<br>Krocích: 60 minut<br>Zakázat autopause: -1|60 minut|

> [!NOTE]
> Pomocí jazyka T-SQL přesunout existující databázi do bez serveru, nebo změňte jeho velikost výpočetních se aktuálně nepodporuje, ale můžete to udělat pomocí webu Azure portal nebo Powershellu.

### <a name="create-new-database-in-serverless-compute-tier"></a>Vytvořit novou databázi na úrovni výpočetní prostředí 

#### <a name="use-azure-portal"></a>Použití webu Azure Portal

Zobrazit [rychlý start: Vytvoření izolované databáze ve službě Azure SQL Database pomocí webu Azure portal](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>Použití prostředí PowerShell

Následující příklad vytvoří novou databázi na úrovni výpočetní prostředí.  Tento příklad explicitně určuje virtuálních jader pro minimální, maximální počet virtuálních jader a autopause zpoždění.

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

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Přesunutí databáze z úrovně zřízených výpočetních do vrstvy výpočetní prostředí

#### <a name="use-powershell"></a>Použití prostředí PowerShell

Následující příklad přesune databázi z vrstvy zřízených výpočetních do vrstvy výpočetní prostředí. Tento příklad explicitně určuje virtuálních jader pro minimální, maximální počet virtuálních jader a autopause zpoždění.

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

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Přesunutí databáze z úrovně výpočetní prostředí do zřízených výpočetních vrstvy

Databáze bez serveru můžete přesunout do vrstvy zřízených výpočetních stejným způsobem jako přesunutí databáze zřízených výpočetních do vrstvy výpočetní prostředí.

## <a name="modifying-serverless-configuration"></a>Změna konfigurace bez serveru

### <a name="maximum-vcores"></a>Maximální počet virtuálních jader

#### <a name="use-powershell"></a>Použití prostředí PowerShell

Změna maximální počet virtuálních jader je prováděno pomocí [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) příkazu v Powershellu pomocí `MaxVcore` argument.

### <a name="minimum-vcores"></a>Minimální počet virtuálních jader

#### <a name="use-powershell"></a>Použití prostředí PowerShell

Úprava min virtuálních jader je prováděno pomocí [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) příkazu v Powershellu pomocí `MinVcore` argument.

### <a name="autopause-delay"></a>Autopause zpoždění

#### <a name="use-powershell"></a>Použití prostředí PowerShell

Úprava zpoždění autopause pomocí provádí [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) příkazu v Powershellu pomocí `AutoPauseDelayInMinutes` argument.

## <a name="monitoring"></a>Monitorování

### <a name="resources-used-and-billed"></a>Prostředky používá a účtují

Prostředky databáze bez serveru jsou zapouzdřena objektem balíček aplikace, SQL instance a entity fondu prostředků uživatelů.

#### <a name="app-package"></a>Balíček aplikace

Balíček aplikace je vnější hranice většinu prostředků správy pro databázi, bez ohledu na to, jestli je databáze v bez serveru nebo zřízené výpočetní vrstvě. Balíček aplikace obsahuje instanci SQL a externí služby společně oboru všechny uživatele a systémové prostředky využívané třídou databáze ve službě SQL Database. Příklady externích služeb: R a fulltextové vyhledávání. SQL instance obvykle dominuje celkové využití prostředků v balíčku aplikace.

#### <a name="user-resource-pool"></a>Uživatelským fondem zdrojů

Uživatelským fondem zdrojů je vnitřní většina hranice správy prostředků pro databázi, bez ohledu na to, jestli je databáze v bez serveru nebo zřízené výpočetní vrstvě. Uživatel prostředků fondu obory procesoru a vstupů/výstupů pro uživatelské zatížení generované DDL dotazů, jako jsou CREATE a ALTER a jazyk DML dotazy, jako výběr, vložení, aktualizace a odstranění. Tyto dotazy obvykle představují nejčastěji podstatnou část využití v rámci balíčku aplikace.

### <a name="metrics"></a>Metriky

Metriky pro monitorování využití prostředků aplikace balíčku a uživatele fondu bez serveru databáze jsou uvedeny v následující tabulce:

|Entita|Metrika|Popis|Jednotky|
|---|---|---|---|
|Balíček aplikace|app_cpu_percent|Procentuální podíl virtuálních jader, používat aplikace vzhledem k maximální počet virtuálních jader pro povolené aplikace.|Procento|
|Balíček aplikace|app_cpu_billed|Objem výpočtů účtovat aplikace během období vytváření sestav. Placené během této doby je produkt tuto metriku a je cena ze jednotku vCore. <br><br>Hodnoty tato metrika se určují na základě agregace v čase používá maximální využití procesoru a paměti používá každou sekundu. Využitý počet je menší než minimální velikost Zřizuje se jako sada min virtuálních jader a paměti min, se účtuje minimální velikost zřízené. Aby bylo možné porovnat procesoru s pamětí pro účely fakturace, je paměť ve změny měřítka množství paměti v Gigabajtech o 3 GB za vCore normalizován do jednotek virtuálních jader.|vCore sekund|
|Balíček aplikace|app_memory_percent|Procento paměti používá aplikace vzhledem k maximální povolenou paměť pro aplikaci.|Procento|
|Uživatel fondu|cpu_percent|Procentuální podíl virtuálních jader, které používá uživatelské zatížení vzhledem k maximální počet virtuálních jader pro povolené pro uživatelské zatížení.|Procento|
|Uživatel fondu|data_IO_percent|Procento dat vstupně-výstupních operací používají uživatelské zatížení souvisejícího s daty maximální IOPS povolené pro uživatelské zatížení.|Procento|
|Uživatel fondu|log_IO_percent|Procento protokolu pro uživatelské zatížení povoleny MB/s, které používá uživatelské zatížení vzhledem k maximální protokolu MB/s.|Procento|
|Uživatel fondu|workers_percent|Procento pracovních procesů využívaných zatížení uživatelů vzhledem k maximální počet pracovních procesů povolen pro uživatelské zatížení.|Procento|
|Uživatel fondu|sessions_percent|Procento relací použít úlohu uživatele vzhledem k maximálního počtu relací povolená pro uživatelské zatížení.|Procento|

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

Objem výpočtů, účtuje se maximální využití procesoru a paměti používá každou sekundu. Pokud procento využití procesoru a použité paměti je menší než minimální velikost pro všechny zřízené, se účtuje zřízená velikost. Aby bylo možné porovnat procesoru s pamětí pro účely fakturace, je paměť ve změny měřítka množství paměti v Gigabajtech o 3 GB za vCore normalizován do jednotek virtuálních jader.

- **Prostředek účtuje**: Procesor a paměť
- **Fakturována částka**: cena za jednotku – VCORE úrovně * max (min virtuálních jader, používá virtuální jádra, minimální paměť GB * 1/3 paměti využité GB * 1/3) 
- **Četnost fakturace**: Za sekundu

Jednotková cena vCore v náklady na vCore za sekundu. Odkazovat [stránce s cenami za Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) pro konkrétní jednotkové ceny v dané oblasti.

Objem výpočtů účtuje je zveřejněný prostřednictvím následující metriky:

- **Metrika**: app_cpu_billed (vCore v sekundách)
- **Definice**: maximální počet (minutu virtuálních jader, používá virtuální jádra, minimální paměť GB * 1/3 paměti využité GB * 1/3)
- **Četnost hlášení**: Za minutu

Toto množství se počítá každou sekundu a agregovat více než 1 minuta.

Vezměte v úvahu bez serveru databáze nakonfigurované s 1 min vCore a 4 maximální počet virtuálních jader.  To odpovídá přibližně 3 GB paměti min a max 12 GB paměti.  Předpokládejme, že automatického pozastavení zpoždění je nastavená na 6 hodin a databázové úlohy je aktivní během prvních 2 hodin období 24 hodin a jinak neaktivní.    

V takovém případě databáze se účtuje za výpočetní prostředky a úložiště během prvních 8 hodin.  I když je databáze aktivní spuštění po druhé hodiny, se pořád účtuje za výpočetní výkon v dalších 6 hodin, které jsou založené na minimální výpočetní prostředky, které jsou zřízené databáze je online.  Pouze úložiště se účtuje zbytek období 24 hodin, zatímco databáze je pozastavená.

Přesněji řečeno výpočetní faktury v tomto příkladu se vypočítává takto:

|Časový Interval|použít každou sekundu virtuálních jader|Každou sekundu využité GB|Dimenze účtuje COMPUTE|vCore sekund účtuje za časový interval|
|---|---|---|---|---|
|0:00-1:00|4|9|použít virtuální jádra|4 virtuální jádra * 3600 sekund = 14400 vCore sekund|
|1:00-2:00|1|12|Využité paměti|12 GB * 1/3 * 3 600 sekund = 14400 vCore sekund|
|2:00-8:00|0|0|Paměť min, zřízené|3 GB * 1/3 * 21600 sekundy = 21600 vCore sekund|
|8:00-24:00|0|0|Žádné výpočty účtují pozastaveno|0 vCore sekund|
|VCore celkový počet sekund účtovat po dobu 24 hodin||||50400 vCore sekund|

Předpokládejme, že je cena ze jednotku výpočetních je $0.000073/vCore/second.  Výpočetní účtuje za toto období 24 hodin se produkt výpočetní jednotky ceny a vCore sekundy účtuje: $0.000073/vCore/second * $3.68 = 50400 vCore sekund

## <a name="available-regions"></a>Dostupné oblasti

Bez serveru výpočetní úroveň je dostupná po celém světě s výjimkou následujících oblastech: Austrálie – střed, Čína – východ, Čína – sever, Francie – Jih, Německo – střed, Německo – severovýchod, Indie – Západ, Korea – Jih, Jihoafrická republika – Západ, Velká Británie – sever, Velká Británie – Jih, Velká Británie – západ a střed USA – západ.

## <a name="next-steps"></a>Další postup

- Abyste mohli začít, najdete v článku [rychlý start: Vytvoření izolované databáze ve službě Azure SQL Database pomocí webu Azure portal](sql-database-single-database-get-started.md).
- Omezení prostředků najdete v tématu [bez serveru – compute úrovně omezení prostředků](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).
