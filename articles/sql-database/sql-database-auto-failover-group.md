---
title: Skupiny převzetí služeb při selhání – Azure SQL Database | Dokumentace Microsoftu
description: Skupiny automatické převzetí služeb při selhání je funkce SQL Database, která vám umožní spravovat replikace a Automatická / koordinované převzetí služeb při selhání skupiny databází na logickém serveru nebo všechny databáze ve spravované instanci.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: e20b18afb579839343fc4c079c039d7b9e5438f7
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994636"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Povolit transparentní a koordinovaný převzetí služeb při selhání několika databází pomocí skupiny automatické převzetí služeb při selhání

Skupiny automatické převzetí služeb při selhání je funkce SQL Database, která vám umožní spravovat replikaci a převzetí služeb při selhání skupiny databází na logickém serveru nebo všechny databáze ve spravované instanci do jiné oblasti (aktuálně ve verzi public preview pro Managed Instance). Používá stejné základní technologii jako [aktivní geografickou replikaci](sql-database-active-geo-replication.md). Převzetí služeb při selhání můžete spustit ručně nebo ho můžete delegovat na službu SQL Database založené na uživatelem definované zásady. Druhou možnost můžete automaticky obnovit více související databáze v sekundární oblasti po závažnému selhání nebo jiné neplánované události, jehož výsledkem úplné nebo částečné ztrátě dostupnosti služby SQL Database v primární oblasti. Kromě toho můžete použít čitelné sekundární databáze k přesměrování zpracování úlohy dotazu jen pro čtení. Protože-automatické převzetí služeb při selhání skupiny zahrnují více databází, musí být nakonfigurované tyto databáze na primárním serveru. Primární a sekundární servery pro databáze ve skupině převzetí služeb při selhání musí být ve stejném předplatném. Automatické převzetí služeb při selhání skupiny podporu replikace všech databází ve skupině jenom jednu sekundární server v jiné oblasti.

> [!NOTE]
> Při práci s databázemi jeden, nebo součástí fondu na logický server a nyní má více sekundární databáze v jedné nebo několika oblastech, použijte [aktivní geografickou replikaci](sql-database-active-geo-replication.md).

Při použití automaticky – převzetí služeb při selhání skupiny zásadám automatické převzetí služeb při selhání, jakémkoli výpadku, který má vliv na jeden nebo několik databází v seskupení výsledků v automatické převzetí služeb při selhání. Kromě toho-automatické převzetí služeb při selhání skupiny poskytují čtení i zápis a koncové body naslouchacího zápisu jen pro čtení, které zůstávají beze změny během převzetí služeb při selhání. Ať už používáte aktivace ruční nebo automatické převzetí služeb při selhání, převzetí služeb při selhání přepne všechny sekundární databáze ve skupině na primární. Po dokončení převzetí služeb při selhání databáze se automaticky aktualizuje záznam DNS přesměrovat koncové body do nové oblasti. Konkrétní data RPO a RTO, naleznete v tématu [přehled kontinuity](sql-database-business-continuity.md).

Když používáte skupiny automatické převzetí služeb při selhání zásadám automatické převzetí služeb při selhání, jakémkoli výpadku, který má vliv na databází na logickém serveru nebo spravované instance za následek automatické převzetí služeb při selhání. Můžete spravovat pomocí skupiny-automatické převzetí služeb při selhání:

- [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [Prostředí PowerShell: Skupiny převzetí služeb při selhání](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [ROZHRANÍ REST API: Skupiny převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups).

Po převzetí služeb při selhání Ujistěte se, že požadavky na ověřování pro server a databáze jsou nakonfigurovány na nový primární. Podrobnosti najdete v tématu [zabezpečení služby SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md).

Pro dosažení skutečné obchodní kontinuity podnikových procesů, je přidání redundance databáze mezi datacentry pouze část řešení. Obnovení aplikaci (služba) end až do konce po závažnému selhání vyžaduje obnovení všech komponent, které tvoří službu a všechny závislé služby. Příklady těchto komponent: klientský software (například prohlížeč s vlastní JavaScriptu), webových front-endů, úložiště a DNS. Je velmi důležité, že všechny komponenty jsou odolné vůči selhání stejné a budou k dispozici v rámci plánovaná doba obnovení (RTO) vaší aplikace. Proto budete muset Identifikujte všechny závislé služby a pochopit, záruky a možnosti, které poskytují. Poté je nutné provést odpovídající kroky zajistit, aby vaše funkce služeb během převzetí služeb při selhání služby, na kterých závisí. Další informace o návrhu řešení pro zotavení po havárii najdete v tématu [návrhu cloudových řešení pro použití pro zotavení po havárii aktivní geografickou replikaci](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Terminologie skupiny-automatické převzetí služeb při selhání a možnosti

- **Skupiny převzetí služeb při selhání**

  Skupina převzetí služeb při selhání je skupina databází spravovaných pomocí jednoho logického serveru nebo v rámci jednoho managed instance, můžete převzetí služeb při selhání jako jednotku do jiné oblasti pro případ, že některá nebo všechna primární databáze nedostupné kvůli výpadku v primární oblasti.

  - **Logické servery**

     Logické servery některé nebo všechny uživatele databáze na jednom serveru je možné použít ve skupině převzetí služeb při selhání. Logický server navíc podporuje více skupin převzetí služeb při selhání na jednom serveru.

  - **Spravované instance**
  
     Managed instance skupiny převzetí služeb při selhání obsahuje všem uživatelským databázím ve spravované instanci a proto Managed Instance podporuje jenom převzetí služeb při selhání jedné skupiny.

- **Primární**

  Logický server nebo Managed Instance, který je hostitelem primární databází ve skupině převzetí služeb při selhání.

- **Sekundární**

  Logický server nebo Managed Instance, který je hostitelem sekundární databází ve skupině převzetí služeb při selhání. Sekundární nemůže být ve stejné oblasti jako primární.

- **Přidávání databází do skupiny převzetí služeb při selhání na logickém serveru**

  Můžete do několika izolovaných databází nebo databází v elastickém fondu přitom na stejném logickém serveru do stejné skupiny převzetí služeb při selhání. Pokud chcete přidat jednu databázi do skupiny převzetí služeb při selhání, automaticky vytvoří sekundární databáze pomocí stejné velikosti edition a výpočetní výkon. Pokud je primární databáze v elastickém fondu, se automaticky vytvoří sekundární v elastickém fondu se stejným názvem. Pokud chcete přidat databáze, která už má sekundární databáze v sekundární server, geografická replikace je zděděno ve skupině. Pokud chcete přidat databáze, která už má sekundární databáze na serveru, který není součástí skupiny převzetí služeb při selhání, se vytvoří nový sekundární v sekundárním serveru.
  
> [!IMPORTANT]
  > Ve spravované instanci jsou replikovány všem uživatelským databázím. Nelze vybrat podmnožinu uživatelských databází pro replikaci ve skupině převzetí služeb při selhání.

- **Naslouchací proces pro čtení i zápis skupiny převzetí služeb při selhání**

  Záznam DNS CNAME, který tvar, který odkazuje na aktuální primární adresy URL. Umožňuje aplikacím SQL pro čtení a zápis se transparentně znovu připojit k primární databáze, pokud se primární změní po převzetí služeb při selhání.

  - **Logický server záznam DNS CNAME pro naslouchací proces pro čtení i zápis**

     Na logickém serveru, je vytvořen záznam DNS CNAME pro skupinu převzetí služeb při selhání, který odkazuje na aktuální primární adresy URL jako `failover-group-name.database.windows.net`.

  - **Spravované záznam Instance DNS CNAME pro naslouchací proces pro čtení i zápis**

     Na Managed Instance, je vytvořen záznam DNS CNAME pro skupinu převzetí služeb při selhání, který odkazuje na aktuální primární adresy URL jako `failover-group-name.zone_id.database.windows.net`.

- **Převzetí služeb při selhání jen pro čtení naslouchacího procesu skupiny**

  Záznam DNS CNAME, který tvar, který odkazuje na jen pro čtení naslouchací proces, odkazující na adresu URL sekundární. Umožňuje aplikacím SQL jen pro čtení transparentně připojit do sekundární lokality pomocí zadaného pravidla Vyrovnávání zatížení.

  - **Logický server záznam DNS CNAME pro naslouchacího zápisu jen pro čtení**

     Na logickém serveru, je vytvořen záznam DNS CNAME pro posluchače jen pro čtení, který odkazuje na adresu URL sekundární jako `failover-group-name.secondary.database.windows.net`.

  - **Spravované záznam Instance DNS CNAME naslouchacího procesu jen pro čtení**

     Na Managed Instance, je vytvořen záznam DNS CNAME pro posluchače jen pro čtení, který odkazuje na adresu URL sekundární jako `failover-group-name.zone_id.database.windows.net`.

- **Automatické převzetí služeb při selhání zásad**

  Ve výchozím nastavení skupinu převzetí služeb při selhání se nakonfigurují zásady automatické převzetí služeb při selhání. Službu SQL Database aktivuje převzetí služeb při selhání, když se zjistí selhání a období odkladu vypršelo. Systému musíte ověřit, že výpadek nelze možné zmírnit zajištěním předdefinované [vysokou dostupnost infrastruktury služby SQL Database](sql-database-high-availability.md) kvůli škálování dopad. Pokud chcete řídit pracovní postup převzetí služeb při selhání z aplikace, můžete vypnout automatické převzetí služeb při selhání.

- **Zásada převzetí služeb při selhání jen pro čtení**

  Ve výchozím nastavení je zakázána převzetí služeb při selhání naslouchacího zápisu jen pro čtení. Zajišťuje, že není při offline sekundární dopad na výkon primární. Ale to také znamená, že relace jen pro čtení nebudete moct připojit, dokud je obnovit sekundární. Pokud jste nejde tolerovat výpadku pro relace jen pro čtení a lze dočasně použít primární pro jen pro čtení pro čtení i zápis přenosy i za cenu potenciální snížení výkonu z primární, můžete povolit převzetí služeb při selhání pro naslouchací proces jen pro čtení. V takovém případě provozu jen pro čtení automaticky přesměrováni na primární Pokud sekundární není k dispozici.

- **Plánované převzetí služeb při selhání**

   Plánované převzetí služeb při selhání provádí úplnou synchronizaci mezi primární a sekundární databáze před přepne sekundární do primární role. Zaručí se tak ztrátě. Plánované převzetí služeb při selhání se používá v následujících scénářích:

  - Při ztrátě dat je nepřijatelné provést zotavení po havárii v produkčním prostředí
  - Přemístění databáze do jiné oblasti
  - Vrácení databáze do primární oblasti po výpadek zmírnit (navrácení služeb po obnovení).

- **Neplánované převzetí služeb při selhání**

   Vynucené nebo neplánované převzetí služeb při selhání se okamžitě přepne sekundární do primární role bez jakékoli synchronizace s primární. Tato operace způsobí ztrátu dat. Neplánované převzetí služeb při selhání se používá jako způsob obnovení během výpadků, když primární není dostupný. Když je původní primární zpátky do online režimu, bude automaticky znovu připojit bez synchronizace a stát nový sekundární.

- **Ruční převzetí služeb při selhání**

  Převzetí služeb při selhání můžete spustit ručně kdykoli bez ohledu na to, konfiguraci automatického převzetí služeb při selhání. Pokud není nakonfigurované zásady pro automatické převzetí služeb při selhání, ruční převzetí služeb při selhání je potřeba obnovit databáze ve skupině převzetí služeb při selhání do sekundární. Můžete zahájit vynucené nebo popisný převzetí služeb při selhání (se úplná synchronizace dat). Ten může použít k přesunutí primární do sekundární oblasti. Po dokončení převzetí služeb při selhání pro zajištění možnosti připojení k nové primární se automaticky aktualizují záznamy DNS

- **Období odkladu se ztrátou dat**

  Vzhledem k tomu, že primární a sekundární databáze nejsou synchronizovány, použití asynchronní replikace, převzetí služeb může způsobit ztrátu dat. Zásady automatické převzetí služeb při selhání tak, aby odrážely tolerance vaší aplikace ke ztrátě dat. můžete přizpůsobit. Tím, že nakonfigurujete **GracePeriodWithDataLossHours**, můžete řídit, jak dlouho systém čeká před inicializací převzetí služeb při selhání, která by mohla ztrátě dat výsledků.

- **Více skupin převzetí služeb při selhání**

  Můžete nakonfigurovat více skupin převzetí služeb při selhání pro stejného páru servery pod kontrolou škálování převzetí služeb při selhání. Každá skupina převezme nezávisle na sobě. Pokud vaše aplikace s více tenanty pomocí elastických fondů, můžete tuto funkci kombinovat primární a sekundární databáze v každém fondu. Tímto způsobem můžete snížit dopad výpadku pouze polovinu klientům.

  > [!IMPORTANT]
  > Managed Instance nepodporuje více skupin převzetí služeb při selhání.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Osvědčené postupy používání skupin převzetí služeb při selhání izolované databáze a elastické fondy

Automatické převzetí služeb při selhání skupiny musí být nakonfigurované na logickém serveru primární a se připojí k sekundární logický server v jiné oblasti Azure.  Skupiny může zahrnovat všechny nebo některé databáze v těchto serverů. Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace pomocí více databází a skupiny – automatické převzetí služeb při selhání.

![automatické převzetí služeb při selhání](./media/sql-database-auto-failover-group/auto-failover-group.png)

Při návrhu služby s kontinuita podnikových procesů v paměti, dodržujte následující obecné pokyny:

- **Použití jednoho nebo několika skupin převzetí služeb při selhání pro správu převzetí služeb při selhání několika databází**

  Jeden nebo více skupin převzetí služeb při selhání můžete vytvořit mezi dvěma servery v různých oblastech (primární a sekundární server). Každá skupina může zahrnovat jednu nebo několik databází, které se dají obnovit jako jednotka v případě, že některá nebo všechna primární databáze nedostupné kvůli výpadku v primární oblasti. Skupiny převzetí služeb při selhání vytvoří geograficky sekundární databázi se stejný cíl služby jako primární. Pokud chcete přidat existující relaci geografické replikace do skupiny převzetí služeb při selhání, nezapomeňte že GEO-secondary má nakonfigurovanou na stejné úrovně služeb a velikost výpočetního jako primární.

- **Pro úlohy OLTP použijte naslouchací proces pro čtení i zápis**

  Při provádění operací s online zpracováním transakcí, použití `failover-group-name.database.windows.net` jako server pro adresu URL a připojení se automaticky přesměrovaní na primární. Tuto adresu URL nezměnil převzetí služeb při selhání. Všimněte si, že že převzetí služeb při selhání zahrnuje aktualizaci, kterou záznam DNS, takže připojení klientů se přesměrují na nový primární až po klientské mezipaměti DNS je aktualizováno.

- **Pro úlohy jen pro čtení použijte naslouchacího zápisu jen pro čtení**

  Pokud nemáte logicky izolované úlohy jen pro čtení, který je odolný vůči určitým odolnost dat, můžete v aplikaci použít sekundární databáze. Pro relace jen pro čtení, použijte `failover-group-name.secondary.database.windows.net` jako server pro adresu URL a připojení se automaticky přesměrovaní na sekundární. Doporučuje se také, abyste určili v připojovacím řetězci číst záměr pomocí **ApplicationIntent = ReadOnly**.

- **Připravit pro snížení výkonu**

  Rozhodnutí převzetí služeb při selhání SQL je nezávislá na zbývající části aplikace nebo jiných služeb, které využívají. Aplikace může být "směšovat" s některými komponentami, v jedné oblasti a některé v jiném. Abyste se vyhnuli zjištěním snížení výkonnosti, zkontrolujte nasazení redundantního aplikace v oblasti zotavení po Havárii a postupujte podle těchto [pokyny pro zabezpečení sítě](#failover-groups-and-network-security).

  > [!NOTE]
  > Aplikace v oblasti zotavení po Havárii nebude muset použít jiné připojovací řetězec.  

- **Příprava ke ztrátě dat.**

  Pokud se zjistí výpadku SQL čeká dobu určenou podle **GracePeriodWithDataLossHours**. Výchozí hodnota je 1 hodina. Pokud si nemůžete dovolit ztrátu dat, nezapomeňte nastavit **GracePeriodWithDataLossHours** na dostatečně velký počet, jako je 24 hodin. Pomocí ručně vytvořená skupina převzetí služeb při selhání nezdaří zpět ze sekundární do primární.

> [!IMPORTANT]
> Elastické fondy s 800 nebo menší počet Dtu a víc než 250 databází s využitím geografické replikace se setkat s problémy, včetně už plánované převzetí služeb při selhání a snížení výkonu.  Tyto problémy budou pravděpodobně probíhat pro úlohy náročné na zápis, když geografickou replikaci koncové body jsou daleko od podle zeměpisné oblasti, nebo když více koncových bodů sekundární se používají pro každou databázi.  Mezi příznaky tyto problémy jsou označeny při prodleva geografické replikace se zvyšuje v čase.  Toto opoždění je možné monitorovat pomocí [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Pokud dojde k těmto problémům, zmírnění zahrnují zvýšení počtu jednotek Dtu fondů nebo snížit počet geograficky replikovaných databází ve stejném fondu.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Osvědčené postupy používání skupin převzetí služeb při selhání pomocí spravovaných instancí

Automatické převzetí služeb při selhání skupiny na primární instance musí být nakonfigurovaný a se připojí k sekundární instance v jiné oblasti Azure.  Všechny databáze v instanci se replikují do sekundární instanci. Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace pomocí spravované instance a skupiny – automatické převzetí služeb při selhání.

![automatické převzetí služeb při selhání](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!IMPORTANT]
> Automatické převzetí služeb při selhání skupiny pro Managed Instance je ve verzi public preview.

Pokud vaše aplikace používá jako datovou vrstvu Managed Instance, postupujte při návrhu pro kontinuitu podnikových tyto obecné pokyny:

- **Vytvořit sekundární instance ve stejné zóně DNS jako primární instance**

  Když je vytvořena nová instance, jedinečné id je automaticky generována jako zóna DNS a název DNS instance součástí. Vícedoménové (SAN) certifikátu pro tuto instanci zřizován s polem SAN v podobě `zone_id.database.windows.net`. Tento certifikát slouží k ověření připojení klienta do instance ve stejné zóně DNS. K zajištění bez přerušení připojení k primární instance po převzetí služeb při selhání primární i sekundární instancí musí být ve stejné zóně DNS. Když je aplikace připravená pro produkční nasazení, vytvoření sekundární instance v různých oblastech a ujistěte se, že zóna DNS, která sdílí s primární instance. To se provádí tak, že zadáte `DNS Zone Partner` volitelný parametr pomocí webu Azure portal, Powershellu nebo rozhraní REST API.

  Další informace o vytvoření sekundární instance ve stejné zóně DNS jako primární instance najdete v tématu [správy skupiny převzetí služeb při selhání s využitím spravované instance (preview)](#managing-failover-groups-with-managed-instances-preview).

- **Povolit replikaci mezi dvěma instancemi**

  Vzhledem k tomu, že každá instance je izolovaný v své vlastní virtuální sítě, musí být povoleno dvě obousměrné přenosy mezi těmito virtuálními sítěmi. Zobrazit [brána Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Konfigurace převzetí služeb při selhání skupiny pro správu převzetí služeb při selhání celé instance**

  Skupiny převzetí služeb při selhání bude spravovat převzetí služeb při selhání všechny databáze v instanci. Když se vytvoří skupina, bude každou databázi v instanci automaticky geograficky replikované sekundární instanci. Skupiny převzetí služeb při selhání nelze použít k zahájení částečné převzetí služeb při selhání dílčí sady databází.

  > [!IMPORTANT]
  > Pokud databáze je odebrán z primární instance, to se také vloží automaticky na geograficky sekundární instanci.

- **Pro úlohy OLTP použijte naslouchací proces pro čtení i zápis**

  Při provádění operací s online zpracováním transakcí, použití `failover-group-name.zone_id.database.windows.net` jako server pro adresu URL a připojení se automaticky přesměrovaní na primární. Tuto adresu URL nezměnil převzetí služeb při selhání. Převzetí služeb zahrnuje aktualizace záznamu DNS, takže připojení klientů se přesměrují na nový primární až po klientské mezipaměti DNS je aktualizováno. Protože sekundární instance sdílí s primární zóny DNS, klientská aplikace bude moct znovu připojit k němu pomocí stejného certifikátu SAN.

- **Připojte se přímo do geograficky replikované sekundární databáze pro dotazy jen pro čtení**

  Pokud nemáte logicky izolované úlohy jen pro čtení, který je odolný vůči určitým odolnost dat, můžete v aplikaci použít sekundární databáze. Chcete-li připojit přímo do geograficky replikované sekundární databáze, použijte `server.secondary.zone_id.database.windows.net` jako server pro adresu URL a připojení se provádí přímo do geograficky replikované sekundární databáze.

  > [!NOTE]
  > V některých úrovně služby Azure SQL Database podporuje použití [repliky jen pro čtení](sql-database-read-scale-out.md) načíst vyrovnávat zatížení dotazu jen pro čtení použitím kapacita jednu repliku pouze pro čtení a `ApplicationIntent=ReadOnly` parametr v připojení řetězec. Když nakonfigurujete geograficky replikované sekundární můžete tuto funkci pro připojení k buď jen pro čtení repliky v primárním umístění nebo v geograficky replikovaného umístění.
  > - Pro připojení k repliky jen pro čtení v primárním umístění, použijte `failover-group-name.zone_id.database.windows.net`.
  > - Pro připojení k repliky jen pro čtení v primárním umístění, použijte `failover-group-name.secondary.zone_id.database.windows.net`.

- **Připravit pro snížení výkonu**

  Rozhodnutí převzetí služeb při selhání SQL je nezávislá na zbývající části aplikace nebo jiných služeb, které využívají. Aplikace může být "směšovat" s některými komponentami, v jedné oblasti a některé v jiném. Abyste se vyhnuli zjištěním snížení výkonnosti, zkontrolujte nasazení redundantního aplikace v oblasti zotavení po Havárii a postupujte podle těchto [pokyny pro zabezpečení sítě](#failover-groups-and-network-security).

- **Příprava ke ztrátě dat.**

  Pokud se zjistí výpadku SQL automaticky spustí převzetí služeb při selhání pro čtení i zápis, pokud není nulová ztráta dat na nejlepší naši znalostní báze. V opačném případě čeká dobu určenou podle `GracePeriodWithDataLossHours`. Pokud jste zadali `GracePeriodWithDataLossHours`, připravené ke ztrátě dat. Obecně platí Azure během výpadků, upřednostňuje dostupnost. Pokud si nemůžete dovolit ztrátu dat, nezapomeňte nastavit GracePeriodWithDataLossHours do dostatečně velké množství, jako je 24 hodin.

  Aktualizace DNS naslouchacího procesu pro čtení a zápis se stane hned převzetí služeb při selhání je zahájené. Tato operace nesmí dojít ke ztrátě. Proces přepínání databázové role, ale může trvat až 5 minut, než se za normálních podmínek. Dokud se nedokončí, zůstanou některé databáze v nové primární instance jen pro čtení. Pokud převzetí služeb při selhání se inicializuje pomocí prostředí PowerShell celá operace je synchronní. Pokud se inicializuje, pomocí webu Azure portal rozhraní označí stav dokončení. Pokud se inicializuje, pomocí rozhraní REST API, pomocí standardní Azure Resource Manageru pro dotazovací mechanismus monitorování pro dokončení.

  > [!IMPORTANT]
  > Pomocí ručně vytvořená skupina převzetí služeb při selhání přesuňte primárek zpět do původního umístění. Při výpadku, který způsobil převzetí služeb při selhání je zmírnit, můžete přesunout vaše primární databáze do původního umístění. K tomu by mělo zahájit ruční převzetí služeb při selhání skupiny.

## <a name="failover-groups-and-network-security"></a>Skupiny převzetí služeb při selhání a zabezpečení sítě

U některých aplikací, které vyžadují pravidla zabezpečení, že síťový přístup k datové vrstvě je omezen na konkrétní součást nebo komponenty, například virtuální počítač webová služba atd. Tento požadavek uvádí některé běžné problémy pro návrh obchodní kontinuity podnikových procesů a používání skupin převzetí služeb při selhání. Při implementaci těchto s omezeným přístupem, byste měli zvážit následující možnosti.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Použití pravidel virtuální sítě a převzetí služeb při selhání skupiny

Pokud používáte [koncové body služeb virtuální sítě a pravidel](sql-database-vnet-service-endpoint-rule-overview.md) k omezení přístupu ke službě SQL database, mějte na paměti tohoto koncového bodu služby každá virtuální síť se vztahuje na jenom jedné oblasti Azure. Koncový bod neumožňuje jiných oblastech tak, aby přijímal komunikaci z podsítě. Proto pouze klientské aplikace nasazené ve stejné oblasti můžete připojit k primární databáze. Vzhledem k tomu, převzetí služeb při selhání výsledkem klientských relací SQL přesměrován na server v jiné oblasti (sekundární), tyto relace se nezdaří, pokud pochází z klienta mimo tuto oblast. Z tohoto důvodu zásady automatické převzetí služeb při selhání není možné, pokud jsou zúčastněné servery součástí pravidla virtuální sítě. Pro podporu ruční převzetí služeb při selhání, postupujte podle těchto kroků:

1. Zřízení redundantních kopií front-endových komponent vaší aplikace (webové služby, virtuální počítače atd.) v sekundární oblasti
2. Konfigurace [pravidel virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) jednotlivě pro primární a sekundární server
3. Povolit [front-endu převzetí služeb při selhání pomocí konfigurace Traffic Manageru](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Spustit ruční převzetí služeb při selhání, pokud je zjištěn výpadek. Tato možnost je optimalizovaná pro aplikace, které vyžadují konzistentní latence mezi front-endu a datovou vrstvou a podporuje obnovení v případě front-endu, datové vrstvě nebo obojí jsou ovlivněny výpadek.

> [!NOTE]
> Pokud používáte **naslouchacího zápisu jen pro čtení** Vyrovnávání zatížení úlohy jen pro čtení, ujistěte se, že tuto úlohu je proveden v virtuálního počítače nebo jiného prostředku v sekundární oblasti, aby se mohl připojit k sekundární databázi.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Používání skupin převzetí služeb při selhání a pravidla brány firewall služby SQL database

Vyžaduje-li plánu kontinuity podnikových procesů pomocí automatického převzetí služeb při selhání skupiny převzetí služeb při selhání, můžete omezit přístup k databázi SQL pomocí pravidel brány firewall tradiční.  Pro podporu automatického převzetí služeb při selhání, postupujte podle těchto kroků:

1. [Vytvoření veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Vytvoření veřejného load balanceru úrovně](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) a přiřazovat veřejné IP adresy.
3. [Vytvoření virtuální sítě a virtuální počítače](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) front-endových komponent
4. [Vytvořit skupinu zabezpečení sítě](../virtual-network/security-overview.md) a konfigurovat příchozí připojení.
5. Ujistěte se, že odchozí připojení jsou otevřená ke službě Azure SQL database s použitím "Sql" [značka služby](../virtual-network/security-overview.md#service-tags).
6. Vytvoření [pravidlo brány firewall služby SQL database](sql-database-firewall-configure.md) které povolí příchozí provoz z veřejné IP adresy, které jste vytvořili v kroku 1.

Další informace o tom, jak nakonfigurovat odchozí přístup a jaké IP adresy k použití v pravidlech brány firewall najdete v tématu [odchozí připojení nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-outbound-connections.md).

Konfiguraci uvedené výš se zajistí, že automatické převzetí služeb při selhání nebude blokovat připojení z front-endových komponent a předpokládá, že aplikace může tolerovat možnost, delší latence mezi front-endu a datovou vrstvou.

> [!IMPORTANT]
> Pro zajištění kontinuity obchodních procesů pro regionální výpadky musíte zajistit geografickou redundanci pro front-endových komponent a databáze.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Povolením geografická replikace mezi Managed instance a jejich virtuálních sítí

Když nastavíte skupiny převzetí služeb při selhání mezi primárním a sekundárním spravované instance ve dvou různých oblastech, každá instance je izolovaná pomocí nezávislé virtuální sítě. Pro povolení provozu replikace mezi těmito virtuálními sítěmi podle zajistit splněné tyto požadavky:

1. Spravovaných instancí musí být v různých oblastech Azure.
2. Vaše sekundární musí být prázdný (žádné uživatelské databáze).
3. Primární a sekundární spravovaných instancí musí být ve stejné skupině prostředků.
4. Virtuální sítě, které jsou součástí potřebu připojení vyřešíme pomocí spravovaných instancí [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Globální VNet Peering se nepodporuje.
5. Dvě Managed Instance virtuální sítě nemůže mít překrývající se IP adresy.
6. Budete muset nastavit vaše skupiny zabezpečení sítě (NSG) takové, které porty 5022 a rozsahu 11000 ~ 12000 se otevřít příchozí a odchozí pro připojení z jiné podsítě Managed instance. To je, aby byl provoz replikace mezi instancemi

    > [!IMPORTANT]
    > Nesprávně nakonfigurované NSG zabezpečení pravidla vede k zablokované databázových operací kopírování.

7. Musíte nakonfigurovat partnerské zóny DNS na sekundární instanci. Zóny DNS je vlastnost Managed Instance. Představuje součást název hostitele, který následuje název spravované Instance a předchází `.database.windows.net` předponu. Je generována jako náhodný řetězec během vytváření prvního Managed Instance v každé virtuální síti. Zóna DNS se nemůže modifikovat po vytvoření spravované instance a spravovaných instancí ve stejné podsíti sdílet stejnou hodnotu zóny DNS. Pro nastavení skupiny převzetí služeb při selhání pro Managed Instance spravované Instance primární a sekundární Managed Instance musejí sdílet stejnou hodnotu zóny DNS. Můžete to provést tak, že zadáte parametr DnsZonePartner při vytváření sekundární Managed Instance. Definuje vlastnost partnera zóny DNS Managed Instance pro sdílení ve skupině převzetí služeb při selhání instancí s. Předáním do id prostředku jiné mi jako vstup DnsZonePartner, v tuto chvíli vytváří Managed Instance dědí stejnou hodnotu zóny DNS partnera, spravované Instance.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Upgrade nebo při downgradu primární databáze

Můžete upgradovat nebo downgradovat primární databáze do různých výpočetních velikost (v rámci stejné úrovně služeb, ne mezi pro obecné účely a pro důležité obchodní informace) bez odpojení všechny sekundární databáze. Při upgradu, doporučujeme nejprve upgradovat sekundární databáze a pak upgradovat primární. Při downgradu, pořadí: nejprve downgradovat primární a poté downgradovat sekundární. Když upgradujete nebo starší verzi databáze, kterou chcete vrstvu různé služby, se vynucuje toto doporučení.

> [!NOTE]
> Pokud jste vytvořili jako součást konfigurace skupiny převzetí služeb při selhání do sekundární databáze není doporučeno downgradovat sekundární databáze. Tím je zajištěno, že datová vrstva má dostatečnou kapacitu pro zpracování pravidelné zatížení po aktivaci převzetí služeb při selhání.

## <a name="preventing-the-loss-of-critical-data"></a>Zabránění ztrátě důležitých dat

Z důvodu vysoké latenci sítě WAN průběžného kopírování používá mechanismu asynchronní replikace. Asynchronní replikace umožňuje ztrátu některých dat. nevyhnutelné Pokud dojde k chybě. Některé aplikace ale můžou vyžadovat bez ztráty. K ochraně těchto důležitých aktualizací, můžete volat vývojář aplikace [uložená procedura sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systému postup ihned po potvrzení transakce. Volání **uložená procedura sp_wait_for_database_copy_sync** blokuje volající vlákno, dokud se poslední potvrzené transakce bylo přeneseno do sekundární databáze. Ale to nečeká přenášená transakcí, které chcete znovu přehrát a potvrzené na sekundární. **uložená procedura sp_wait_for_database_copy_sync** je vymezen na odkaz na konkrétní průběžného kopírování. Tento postup můžete volat každý uživatel s právy k připojení k primární databáze.

> [!NOTE]
> **uložená procedura sp_wait_for_database_copy_sync** zabrání ztrátě dat po převzetí služeb při selhání, ale nezaručuje úplnou synchronizaci pro oprávnění ke čtení. Zpoždění způsobené **uložená procedura sp_wait_for_database_copy_sync** TDS může být významný a závisí na velikosti protokolů transakcí v době volání.

## <a name="failover-groups-and-point-in-time-restore"></a>Skupiny převzetí služeb při selhání a obnovení k určitému bodu v čase

Informace o použití obnovení k určitému bodu v čase pomocí skupin převzetí služeb při selhání najdete v tématu [bodu v čase obnovení (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Programová správa skupiny převzetí služeb při selhání

Jak je popsáno výše, skupiny automatické převzetí služeb při selhání a aktivní geografickou replikaci můžete také spravovat prostřednictvím kódu programu pomocí prostředí Azure PowerShell a rozhraní REST API. Následující tabulky popisují sadu příkazů, které jsou k dispozici. Aktivní geografická replikace zahrnuje sadu rozhraní API Azure Resource Manageru pro správu, včetně [REST API služby Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) a [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Tato rozhraní API vyžaduje použití skupin prostředků a podporu zabezpečení na základě role (RBAC). Další informace o tom, jak implementovat přístup rolí, najdete v části [řízení přístupu](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: Správa SQL database převzetí služeb při selhání izolované databáze a elastických fondů

| Rutina | Popis |
| --- | --- |
| [New-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ho na primární a sekundární server|
| [Remove-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | Odebere skupinu převzetí služeb při selhání ze serveru a odstranění všech sekundárních databází zahrnuté skupiny |
| [Get-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Načte konfiguraci skupiny převzetí služeb při selhání |
| [Set-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |Upraví konfiguraci skupiny převzetí služeb při selhání |
| [Switch-AzureRMSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Aktivační události převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server |
| [Přidat AzureRmSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/add-azurermsqldatabasetofailovergroup)|Přidá jednu nebo více databází do skupiny převzetí služeb při selhání služby Azure SQL Database|
|  | |

> [!IMPORTANT]
> Vzorový skript najdete v tématu [konfigurace a převzetí služeb při selhání skupiny převzetí služeb při selhání pro izolovanou databázi](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>PowerShell: Správa skupin převzetí služeb při selhání s spravované instance (preview)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>Nainstalujte nejnovější verzi předběžné verze prostředí PowerShell

1. Aktualizace modulu PowerShellGet 1.6.5 (nebo nejnovější verze preview). Zobrazit [Powershellu ve verzi preview webu](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

   ```Powershell
      install-module PowerShellGet -MinimumVersion 1.6.5 -force
   ```

2. V novém okně prostředí PowerShell spusťte následující příkazy:

   ```Powershell
      import-module PowerShellGet
      get-module PowerShellGet #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>Rutiny prostředí PowerShell k vytvoření instance skupiny převzetí služeb při selhání

| Rozhraní API | Popis |
| --- | --- |
| Nové AzureRmSqlDatabaseInstanceFailoverGroup |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ho na primární a sekundární server|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |Upraví konfiguraci skupiny převzetí služeb při selhání|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |Načte konfiguraci skupiny převzetí služeb při selhání|
| Přepínač AzureRmSqlDatabaseInstanceFailoverGroup |Aktivační události převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server|
| Odebrat AzureRmSqlDatabaseInstanceFailoverGroup | Odebere skupinu převzetí služeb při selhání|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>ROZHRANÍ REST API: Spravovat skupiny převzetí služeb při selhání databáze SQL s databázemi ve fondu a jeden

| Rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Vytvoří nebo aktualizuje skupinu převzetí služeb při selhání |
| [Odstranění skupiny převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Odebere skupinu převzetí služeb při selhání ze serveru |
| [Převzetí služeb při selhání (plánovaná)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Převezme služby při selhání z aktuálního primárního serveru na tomto serveru. |
| [Povolit Vynucené převzetí služeb při selhání ztráty dat](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |přes ails z aktuálního primárního serveru na tomto serveru. Tato operace může dojít ke ztrátě. |
| [Získat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Načte skupinu převzetí služeb při selhání. |
| [Seznam skupiny převzetí služeb při selhání serverem](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Seznam skupin převzetí služeb při selhání na serveru. |
| [Aktualizace skupiny převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aktualizace skupiny převzetí služeb při selhání. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>ROZHRANÍ REST API: Správa skupin převzetí služeb při selhání pomocí spravované instance (preview)

| Rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Vytvoří nebo aktualizuje skupinu převzetí služeb při selhání |
| [Odstranění skupiny převzetí služeb při selhání](https://docs.microsoft.com/rest/api/instancefailovergroups/delete) | Odebere skupinu převzetí služeb při selhání ze serveru |
| [Převzetí služeb při selhání (plánovaná)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Převezme služby při selhání z aktuálního primárního serveru na tomto serveru. |
| [Povolit Vynucené převzetí služeb při selhání ztráty dat](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |přes ails z aktuálního primárního serveru na tomto serveru. Tato operace může dojít ke ztrátě. |
| [Získat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Načte skupinu převzetí služeb při selhání. |
| [Seznam skupiny převzetí služeb při selhání – seznam podle umístění](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Seznam skupin převzetí služeb při selhání na místě. |

## <a name="next-steps"></a>Další postup

- Ukázky skriptů najdete tady:
  - [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Konfigurace a převzetí služeb při selhání skupiny převzetí služeb při selhání pro izolovanou databázi](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md)
- Další informace o Azure SQL Database, automatické zálohování, naleznete v tématu [automatické zálohování SQL Database](sql-database-automated-backups.md).
- Další informace o obnovení pomocí automatizovaného zálohování, naleznete v tématu [obnovení databáze ze záloh spouštěných službou](sql-database-recovery-using-backups.md).
- Další informace o požadavcích na ověřování pro novým primárním serverem a databází, naleznete v tématu [zabezpečení služby SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md).
