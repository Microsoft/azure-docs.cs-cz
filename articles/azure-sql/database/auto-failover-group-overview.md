---
title: Skupiny automatického převzetí služeb při selhání
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Skupiny s automatickým převzetím služeb při selhání umožňují spravovat replikaci a automatické nebo koordinované převzetí služeb při selhání skupiny databází na serveru nebo ve všech databázích ve spravované instanci.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 08/28/2020
ms.openlocfilehash: 2035fa811ed6bb5760f2527f66e0f2ca48ccb2c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627221"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Použití skupin automatického převzetí služeb při selhání k zajištění transparentního a koordinovaného převzetí služeb při selhání více databází
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Funkce skupin s automatickým převzetím služeb při selhání umožňuje spravovat replikaci a převzetí služeb při selhání skupiny databází na serveru nebo všech databázích ve spravované instanci do jiné oblasti. Jedná se o deklarativní abstrakci nad stávající [aktivní geografickou replikací](active-geo-replication-overview.md) , která je navržená tak, aby zjednodušila nasazení a správu geograficky replikovaných databází se škálováním. Převzetí služeb při selhání můžete iniciovat ručně nebo je můžete delegovat na službu Azure na základě uživatelsky definované zásady. Druhá možnost umožňuje automaticky obnovit více souvisejících databází v sekundární oblasti po závažných chybách nebo jiné neplánované události, které mají za následek úplnou nebo částečnou ztrátu SQL Database nebo dostupnosti spravované instance SQL v primární oblasti. Skupina převzetí služeb při selhání může zahrnovat jednu nebo více databází, které obvykle používá stejná aplikace. Kromě toho můžete použít čitelné sekundární databáze pro přesměrování zatížení dotazů jen pro čtení. Vzhledem k tomu, že skupiny s automatickým převzetím služeb při selhání zahrnují více databází, je nutné tyto databáze nakonfigurovat na primárním serveru Skupiny s automatickým převzetím služeb při selhání podporují replikaci všech databází ve skupině jenom na jeden sekundární server nebo instanci v jiné oblasti.

> [!NOTE]
> Pokud chcete více Azure SQL Databasech sekundárních umístění ve stejné nebo jiné oblasti, použijte [aktivní geografickou replikaci](active-geo-replication-overview.md).

Pokud používáte skupiny s automatickým převzetím služeb při selhání se zásadami automatického převzetí služeb při selhání, jakékoli výpadky, které mají vliv na jednu nebo několik databází ve skupině, mají za následek automatické převzetí Typicky se jedná o incidenty, které se nedají samy zmírnit pomocí integrovaných automatických operací s vysokou dostupností. Mezi příklady aktivačních událostí převzetí služeb při selhání patří incident způsobený SQL Databasem v důsledku nevracení paměti jádra operačního systému na několika výpočetních uzlech, nebo incident způsobený jedním nebo více klienty v provozu, protože během rutiny vyřazení hardwaru došlo k přerušení nesprávného síťového kabelu.  Další informace najdete v tématu [SQL Database vysoké dostupnosti](high-availability-sla.md).

Skupiny s automatickým převzetím služeb při selhání poskytují koncové body naslouchacího procesu pro čtení i zápis a jen pro čtení, které během převzetí služeb při selhání zůstanou beze změny. Bez ohledu na to, jestli používáte ruční nebo automatickou aktivaci při selhání, převzetí služeb při selhání přepne všechny sekundární databáze ve skupině na primární. Po převzetí služeb při selhání databáze je záznam DNS automaticky aktualizován pro přesměrování koncových bodů do nové oblasti. Konkrétní data RPO a RTO najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).

Pokud používáte skupiny s automatickým převzetím služeb při selhání se zásadami automatického převzetí služeb při selhání, dojde při jakémkoli výpadku, který ovlivňuje databáze na serveru nebo spravované instanci, na automatické převzetí Skupinu automatického převzetí služeb při selhání můžete spravovat pomocí:

- [Azure Portal](geo-distributed-application-configure-tutorial.md)
- [Azure CLI: Skupina převzetí služeb při selhání](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: Skupina převzetí služeb při selhání](scripts/add-database-to-failover-group-powershell.md)
- [REST API: Skupina převzetí služeb při selhání](/rest/api/sql/failovergroups).

Po převzetí služeb při selhání zajistěte, aby byly požadavky na ověření pro vaši databázi a Server nebo instance nakonfigurovány na novém primárním serveru. Podrobnosti najdete v tématu [SQL Database Security po zotavení po havárii](active-geo-replication-security-configure.md).

Aby bylo možné dosáhnout reálné provozní kontinuity, Přidání redundance databáze mezi datacentry je pouze součástí řešení. Kompletní obnovení aplikace (služby) po závažných chybách vyžaduje obnovení všech součástí, které tvoří službu a všechny závislé služby. Mezi tyto komponenty patří klientský software (například prohlížeč s vlastním JavaScriptem), webové front-endy, úložiště a DNS. Je velmi důležité, aby všechny součásti byly odolné vůči stejnou selhání a byly k dispozici v rámci plánované doby obnovení (RTO) vaší aplikace. Proto je potřeba identifikovat všechny závislé služby a porozumět zárukám a funkcím, které poskytují. Pak musíte provést vhodné kroky, abyste zajistili, že vaše služba funguje při převzetí služeb při selhání služeb, na kterých závisí. Další informace o návrhu řešení pro zotavení po havárii najdete v tématu [navrhování cloudových řešení pro zotavení po havárii pomocí aktivní geografické replikace](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="terminology-and-capabilities"></a>Terminologie a možnosti

- **Skupina převzetí služeb při selhání (MLHOVé)**

  Skupina převzetí služeb při selhání je pojmenovaná skupina databází spravovaná jedním serverem nebo ve spravované instanci, která může převzít služby při selhání jako jednotka v jiné oblasti v případě, že všechny nebo některé primární databáze nebudou k dispozici z důvodu výpadku v primární oblasti. Když se vytvoří pro spravovanou instanci SQL, skupina převzetí služeb při selhání obsahuje všechny uživatelské databáze v instanci, takže v instanci se dá nakonfigurovat jenom jedna skupina převzetí služeb při selhání.
  
  > [!IMPORTANT]
  > Název skupiny převzetí služeb při selhání musí být globálně jedinečný v rámci `.database.windows.net` domény.

- **Servery**

     Se servery můžou být některé nebo všechny uživatelské databáze na serveru umístěné ve skupině převzetí služeb při selhání. Server také podporuje více skupin převzetí služeb při selhání na jednom serveru.

- **Primární**

  Server nebo spravovaná instance, která je hostitelem primárních databází ve skupině převzetí služeb při selhání.

- **Sekundární**

  Server nebo spravovaná instance hostující sekundární databáze ve skupině převzetí služeb při selhání. Sekundární nemůže být ve stejné oblasti jako primární.

- **Přidávání samostatných databází do skupiny převzetí služeb při selhání**

  Do stejné skupiny převzetí služeb při selhání můžete umístit několik samostatných databází na stejný server. Pokud přidáte do skupiny převzetí služeb při selhání jednu databázi, automaticky vytvoří sekundární databázi pomocí stejné edice a výpočetní velikosti na sekundárním serveru.  Tento server jste zadali při vytvoření skupiny převzetí služeb při selhání. Pokud přidáváte databázi, která už má sekundární databázi na sekundárním serveru, toto propojení geografické replikace je zděděné skupinou. Když přidáváte databázi, která už má sekundární databázi na serveru, který není součástí skupiny převzetí služeb při selhání, vytvoří se na sekundárním serveru nový sekundární objekt.

  > [!IMPORTANT]
  > Ujistěte se, že sekundární server nemá databázi se stejným názvem, pokud se nejedná o existující sekundární databázi. V případě skupin převzetí služeb při selhání pro spravovanou instanci SQL se replikují všechny uživatelské databáze. V této skupině převzetí služeb při selhání nelze vybrat podmnožinu uživatelských databází pro replikaci.

- **Přidání databází do elastického fondu do skupiny převzetí služeb při selhání**

  Do jedné skupiny převzetí služeb při selhání můžete umístit všechny nebo několik databází v rámci elastického fondu. Pokud je primární databáze v elastickém fondu, sekundární se automaticky vytvoří v elastickém fondu se stejným názvem (sekundární fond). Je nutné zajistit, aby sekundární server obsahoval elastický fond se stejným přesným názvem a dostatek volné kapacity pro hostování sekundárních databází, které budou vytvořeny skupinou převzetí služeb při selhání. Pokud přidáte databázi do fondu, který již má sekundární databázi v sekundárním fondu, bude tento odkaz geografická replikace zděděn skupinou. Když přidáváte databázi, která už má sekundární databázi na serveru, který není součástí skupiny převzetí služeb při selhání, vytvoří se v sekundárním fondu nový sekundární objekt.
  
- **Počáteční osazení**

  Když přidáváte databáze, elastické fondy nebo spravované instance do skupiny převzetí služeb při selhání, před spuštěním replikace dat se vytvoří prvotní fáze osazení. Počáteční fáze osazení je nejdelší a nejnáročná operace. Po dokončení počátečního osazení se data synchronizují a pak se replikují jenom následné změny dat. Čas potřebný k dokončení počátečního dosazení závisí na velikosti dat, počtu replikovaných databází a rychlosti propojení mezi entitami ve skupině převzetí služeb při selhání. Za běžných okolností je možná rychlost osazení až 500 GB hodiny pro SQL Database a až 360 GB za hodinu pro spravovanou instanci SQL. Osazení se provádí pro všechny databáze paralelně.

  V případě spravované instance SQL zvažte rychlost propojení Express Route mezi dvěma instancemi při odhadu doby prvotní fáze osazení. V případě, že rychlost propojení mezi dvěma instancemi je pomalejší, než je potřeba, je pravděpodobné, že čas na počáteční hodnotu bude mít vliv. Můžete použít uvedenou rychlost osazení, počet databází, celkovou velikost dat a rychlost připojení k odhadu, jak dlouho bude prvotní fáze osazení trvat před spuštěním replikace dat. Například pro jednu 100 GB databáze by počáteční fáze osazení trvat přibližně 1,2 hodin, pokud je odkaz 84 schopný přenášet GB za hodinu, a pokud nejsou osazené žádné jiné databáze. Pokud odkaz může přenášet jenom 10 GB za hodinu, pak bude dosazení 100 GB databáze trvat přibližně 10 hodin. Pokud existuje více databází k replikaci, je osazení provedeno paralelně a při kombinaci s pomalým připojením může prvotní fáze osazení trvat mnohem déle, zejména v případě, že paralelní osazení dat ze všech databází přesahuje dostupnou šířku pásma propojení. Pokud je šířka pásma sítě mezi dvěma instancemi omezená a do skupiny převzetí služeb při selhání přidáváte víc spravovaných instancí, zvažte postupně přidání více spravovaných instancí do skupiny převzetí služeb při selhání, jednu po druhé. Vzhledem k odpovídající velikosti skladové položky brány mezi dvěma spravovanými instancemi a v případě, že šířka pásma podnikové sítě umožňuje, je možné dosáhnout rychlostí až 360 GB hodiny.  

- **Zóna DNS**

  Jedinečné ID, které se automaticky vygeneruje při vytvoření nové spravované instance SQL. Pro tuto instanci se zřídí certifikát s více doménami (SAN) pro ověřování připojení klientů k jakékoli instanci ve stejné zóně DNS. Tyto dvě spravované instance ve stejné skupině převzetí služeb při selhání musí sdílet zónu DNS.
  
  > [!NOTE]
  > Pro skupiny převzetí služeb při selhání vytvořené pro SQL Database se nevyžaduje ID zóny DNS.

- **Naslouchací proces pro čtení a zápis skupiny převzetí služeb při selhání**

  Záznam DNS CNAME, který odkazuje na aktuální primární adresu URL. Automaticky se vytvoří při vytvoření skupiny převzetí služeb při selhání a umožňuje úlohám čtení i zápisu transparentně se znovu připojit k primární databázi, když se primární změna po převzetí služeb při selhání. Když je na serveru vytvořená skupina převzetí služeb při selhání, bude záznam CNAME DNS pro adresu URL naslouchacího procesu vytvořený jako `<fog-name>.database.windows.net` . Pokud je skupina převzetí služeb při selhání vytvořena na spravované instanci SQL, bude záznam CNAME DNS pro adresu URL naslouchacího procesu vytvořený jako `<fog-name>.<zone_id>.database.windows.net` .

- **Skupina převzetí služeb při selhání – naslouchací proces jen pro čtení**

  Záznam CNAME DNS vytvořený, který odkazuje na naslouchací proces jen pro čtení, který odkazuje na adresu URL sekundárního objektu. Automaticky se vytvoří při vytvoření skupiny převzetí služeb při selhání a umožňuje úlohy SQL, která je jen pro čtení, k sekundárnímu připojení k sekundárnímu pomocí zadaných pravidel vyrovnávání zatížení. Když je na serveru vytvořená skupina převzetí služeb při selhání, bude záznam CNAME DNS pro adresu URL naslouchacího procesu vytvořený jako `<fog-name>.secondary.database.windows.net` . Pokud je skupina převzetí služeb při selhání vytvořena na spravované instanci SQL, bude záznam CNAME DNS pro adresu URL naslouchacího procesu vytvořený jako `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Zásady automatického převzetí služeb při selhání**

  Ve výchozím nastavení je skupina převzetí služeb při selhání nakonfigurovaná se zásadami automatického převzetí služeb při selhání. Azure aktivuje převzetí služeb při selhání, po zjištění selhání a vypršení platnosti období odkladu. Systém musí ověřit, jestli se výpadek nedá zmírnit integrovanou [infrastrukturou vysoké dostupnosti](high-availability-sla.md) z důvodu rozsahu dopadu. Pokud chcete řídit pracovní postup převzetí služeb při selhání z aplikace, můžete automatické převzetí služeb při selhání vypnout.
  
  > [!NOTE]
  > Vzhledem k tomu, že ověřování škály výpadku a jak rychle se dá zmírnit, zahrnuje lidské akce tým provozu, období odkladu nelze nastavit pod jednu hodinu. Toto omezení se vztahuje na všechny databáze ve skupině převzetí služeb při selhání bez ohledu na stav synchronizace dat.

- **Zásada převzetí služeb při selhání jen pro čtení**

  Ve výchozím nastavení je převzetí služeb při selhání naslouchacího procesu jen pro čtení zakázané. Zajišťuje, že výkon primárního z těchto primárních není ovlivněný, když je sekundární objekt v režimu offline. Ale také to znamená, že relace jen pro čtení se nebudou moci připojit až po obnovení sekundárního zařízení. Pokud nemůžete tolerovat výpadky v relacích jen pro čtení a jsou v pořádku, aby byly primární pro provoz jen pro čtení i pro čtení i zápis na úkor možného snížení výkonu primární služby, můžete povolit převzetí služeb při selhání pro naslouchací proces jen pro čtení konfigurací `AllowReadOnlyFailoverToPrimary` Vlastnosti. V takovém případě bude přenos, který je jen pro čtení, automaticky přesměrován na primární, pokud není k dispozici sekundární.

- **Plánované převzetí služeb při selhání**

   Plánované převzetí služeb při selhání provede úplnou synchronizaci mezi primárními a sekundárními databázemi před sekundárním přepnutím do primární role. To zaručuje, že nedojde ke ztrátě dat. Plánované převzetí služeb při selhání se používá v následujících scénářích:

  - Podrobná cvičení zotavení po havárii (DR) v produkčním prostředí, když je ztráta dat nepřijatelná
  - Přemístit databáze do jiné oblasti
  - Po omezení výpadku (navrácení služeb po obnovení) Vraťte databáze do primární oblasti.

- **Neplánované převzetí služeb při selhání**

   Neplánované nebo vynucené převzetí služeb při selhání okamžitě přepne sekundární roli do primární role bez jakékoli synchronizace s primární. Výsledkem této operace bude ztráta dat. Neplánované převzetí služeb při selhání se používá jako metoda obnovení během výpadků, pokud není primární přístup k primárnímu přístupu. Když je původní primární databáze zpátky online, automaticky se znovu připojí bez synchronizace a stane se novou sekundární.

- **Ruční převzetí služeb při selhání**

  Převzetí služeb při selhání můžete kdykoli iniciovat ručně bez ohledu na konfiguraci automatického převzetí služeb při selhání. Pokud nejsou nakonfigurovány Zásady automatického převzetí služeb při selhání, je nutné ruční převzetí služeb při selhání pro obnovení databází ve skupině převzetí služeb při selhání na sekundární. Můžete iniciovat vynucené nebo popisné převzetí služeb při selhání (s úplnou synchronizací dat). Druhá by mohla být použita k přemístění primární do sekundární oblasti. Po dokončení převzetí služeb při selhání se záznamy DNS automaticky aktualizují, aby se zajistilo připojení k nové primární

- **Období odkladu s ztrátou dat**

  Vzhledem k tomu, že primární a sekundární databáze jsou synchronizovány pomocí asynchronní replikace, může převzetí služeb při selhání dojít ke ztrátě dat. Zásady automatického převzetí služeb při selhání můžete přizpůsobit tak, aby odrážely odolnost vaší aplikace proti ztrátě dat. Konfigurací nástroje `GracePeriodWithDataLossHours` můžete určit, jak dlouho systém počká, než se iniciuje převzetí služeb při selhání, které pravděpodobně bude mít za následek ztrátu dat.

- **Několik skupin převzetí služeb při selhání**

  Můžete nakonfigurovat více skupin převzetí služeb při selhání pro stejnou dvojici serverů pro řízení škálování převzetí služeb při selhání. U každé skupiny dojde k selhání nezávisle. Pokud vaše aplikace pro více tenantů používá elastické fondy, můžete tuto možnost využít ke smíchání primárních a sekundárních databází v jednotlivých fondech. Tímto způsobem můžete snížit dopad výpadku jenom na polovinu klientů.

  > [!NOTE]
  > Spravovaná instance SQL nepodporuje více skupin převzetí služeb při selhání.
  
## <a name="permissions"></a>Oprávnění

Oprávnění pro skupinu převzetí služeb při selhání se spravují prostřednictvím [řízení přístupu na základě role Azure (RBAC)](../../role-based-access-control/overview.md). Role [přispěvatel SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) má všechna potřebná oprávnění ke správě skupin převzetí služeb při selhání.

### <a name="create-failover-group"></a>Vytvořit skupinu převzetí služeb při selhání

Chcete-li vytvořit skupinu převzetí služeb při selhání, budete potřebovat přístup k zápisu RBAC na primární i sekundární server a do všech databází ve skupině převzetí služeb při selhání. U spravované instance SQL potřebujete přístup k zápisu RBAC jak pro primární, tak pro sekundární spravovanou instanci SQL, ale oprávnění pro jednotlivé databáze nejsou relevantní, protože jednotlivé databáze spravované instance SQL nejde do skupiny převzetí služeb při selhání přidat ani z ní odebrat.

### <a name="update-a-failover-group"></a>Aktualizace skupiny převzetí služeb při selhání

Chcete-li aktualizovat skupinu převzetí služeb při selhání, budete potřebovat přístup pro zápis RBAC do skupiny převzetí služeb při selhání a všechny databáze na aktuálním primárním serveru nebo spravované instanci.  

### <a name="fail-over-a-failover-group"></a>Převzít služby při selhání skupiny převzetí služeb při selhání

Pokud chcete převzít služby při selhání skupiny převzetí služeb při selhání, budete potřebovat přístup k zápisu RBAC do skupiny převzetí služeb při selhání na novém primárním serveru nebo spravované instanci.

## <a name="best-practices-for-sql-database"></a>Osvědčené postupy pro SQL Database

Skupina automatického převzetí služeb při selhání musí být nakonfigurovaná na primárním serveru a bude připojena k sekundárnímu serveru v jiné oblasti Azure. Skupiny mohou obsahovat všechny nebo některé databáze na těchto serverech. Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace s využitím více databází a skupiny s automatickým převzetím služeb při selhání.

![Diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace s využitím více databází a skupiny s automatickým převzetím služeb při selhání.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Podrobný kurz přidání databáze v SQL Database do skupiny převzetí služeb při selhání najdete v tématu [přidání SQL Database do skupiny převzetí služeb při selhání](failover-group-add-single-database-tutorial.md) .

Při navrhování služby s ohledem na provozní kontinuitu se řiďte těmito obecnými pokyny:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Použití jedné nebo několika skupin převzetí služeb při selhání pro správu převzetí služeb při selhání více databází

Jednu nebo více skupin s podporou převzetí služeb při selhání je možné vytvořit mezi dvěma servery v různých oblastech (primární a sekundární servery). Každá skupina může zahrnovat jednu nebo několik databází, které jsou obnoveny jako jednotka v případě výpadku v primární oblasti, nebo některé primární databáze nebudou k dispozici. Skupina převzetí služeb při selhání vytvoří geograficky sekundární databázi se stejným cílem služby jako primární. Pokud přidáte existující relaci geografické replikace do skupiny převzetí služeb při selhání, ujistěte se, že je geograficky sekundární nakonfigurovaná se stejnou úrovní služeb a výpočetní velikostí jako primární.
  
> [!IMPORTANT]
> Vytváření skupin převzetí služeb při selhání mezi dvěma servery v různých předplatných se v současnosti nepodporuje pro Azure SQL Database. Pokud primární nebo sekundární server přesunete do jiného předplatného po vytvoření skupiny převzetí služeb při selhání, může dojít k selhání požadavků na převzetí služeb při selhání a dalších operací.

### <a name="using-read-write-listener-for-oltp-workload"></a>Použití naslouchacího procesu pro čtení i zápis pro úlohu OLTP

Při provádění operací OLTP použijte `<fog-name>.database.windows.net` jako adresu URL serveru a připojení se automaticky přesměrují na primární. Tato adresa URL se po převzetí služeb při selhání nemění. Všimněte si, že při převzetí služeb při selhání je potřeba aktualizovat záznam DNS, aby se připojení klientů přesměrovala na nové primární až po aktualizaci mezipaměti DNS klienta.

### <a name="using-read-only-listener-for-read-only-workload"></a>Použití naslouchacího procesu jen pro čtení pro úlohu jen pro čtení

Pokud máte logicky izolovanou úlohu jen pro čtení, která je odolná vůči určité zastaralosti dat, můžete v aplikaci použít sekundární databázi. V případě relací jen pro čtení použijte `<fog-name>.secondary.database.windows.net` jako adresu URL serveru a připojení se automaticky přesměruje na sekundární. Je také vhodné určit v úmyslu přečíst si v připojovacím řetězci pomocí `ApplicationIntent=ReadOnly` . Pokud chcete zajistit, že se po převzetí služeb při selhání může znovu připojit úloha jen pro čtení, nebo pokud sekundární server přejde do režimu offline, ujistěte se, že jste nakonfigurovali `AllowReadOnlyFailoverToPrimary` vlastnost zásady převzetí služeb při selhání.

### <a name="preparing-for-performance-degradation"></a>Příprava na snížení výkonu

Typická aplikace Azure používá několik služeb Azure a skládá se z několika součástí. Automatické převzetí služeb při selhání ve skupině převzetí služeb při selhání se aktivuje v závislosti na stavu samotných komponent Azure SQL. Jiné služby Azure v primární oblasti nemusí být ovlivněny výpadkem a jejich komponenty mohou být v této oblasti stále dostupné. Jakmile se primární databáze přepne do oblasti zotavení po havárii, může se zvýšit latence mezi závislými komponentami. Aby se zabránilo dopadu vyšší latence na výkon aplikace, zajistěte redundanci všech komponent aplikace v oblasti zotavení po havárii a postupujte podle [pokynů pro zabezpečení sítě](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Příprava na ztrátu dat

Pokud se zjistí výpadek, Azure počká na období, které jste určili `GracePeriodWithDataLossHours` . Výchozí hodnota je 1 hodina. Pokud nemůžete zaručit ztrátu dat, nezapomeňte nastavit `GracePeriodWithDataLossHours` na dostatečně velký počet, například 24 hodin. K navrácení služeb po obnovení ze sekundárního na primární se používá ruční převzetí služeb při selhání pomocí skupiny.

> [!IMPORTANT]
> Elastické fondy s 800 nebo méně DTU a více než 250 databází pomocí geografické replikace můžou narazit na problémy, včetně delšího plánovaného převzetí služeb při selhání a sníženého výkonu.  Tyto problémy se budou pravděpodobněji vyskytnout pro úlohy náročné na zápis, když jsou koncové body geografické replikace široce oddělené geograficky nebo pokud se pro každou databázi používá více sekundárních koncových bodů.  Příznaky těchto problémů jsou uvedené v případě, že se prodleva geografické replikace v průběhu času zvyšuje.  Tato prodleva se dá monitorovat pomocí [Sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Pokud dojde k těmto potížím, bude zmírnění rizik zahrnovat zvýšení počtu DTU fondů nebo snížení počtu geograficky replikovaných databází ve stejném fondu.

### <a name="changing-secondary-region-of-the-failover-group"></a>Změna sekundární oblasti skupiny převzetí služeb při selhání

K ilustraci sekvence změn se předpokládá, že server A je primárním serverem, server B je stávající sekundární server a Server C je nový sekundární ve třetí oblasti.  Pro přechod proveďte následující kroky:

1. Pomocí [aktivní geografické replikace](active-geo-replication-overview.md)vytvořte další sekundární databáze pro každou databázi na serveru A na server C. Každá databáze na serveru A bude mít dvě sekundární, jednu na serveru B a jednu na server C. Tím se zajistí, že primární databáze zůstanou chráněné během přechodu.
2. Odstraňte skupinu převzetí služeb při selhání. V tomto okamžiku se přihlášení nezdaří. Důvodem je, že se odstranily aliasy SQL pro naslouchací procesy skupiny převzetí služeb při selhání a brána nerozpozná název skupiny převzetí služeb při selhání.
3. Znovu vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi servery A a C. V tomto okamžiku se přihlášení zastaví, pokud se nezdaří.
4. Přidejte všechny primární databáze na serveru A do nové skupiny převzetí služeb při selhání.
5. Vyřaďte Server B. Všechny databáze na B budou automaticky odstraněny.

### <a name="changing-primary-region-of-the-failover-group"></a>Změna primární oblasti skupiny převzetí služeb při selhání

K ilustraci sekvence změn se předpokládá, že server A je primárním serverem, server B je stávající sekundární server a Server C je nová primární databáze ve třetí oblasti.  Pro přechod proveďte následující kroky:

1. Proveďte plánované převzetí služeb při selhání pro přepnutí primárního serveru na B. Server A se stane novým sekundárním serverem. Převzetí služeb při selhání může vést k několika minutám výpadku. Skutečný čas bude záviset na velikosti skupiny převzetí služeb při selhání.
2. Pomocí [aktivní geografické replikace](active-geo-replication-overview.md)vytvořte další sekundární databáze pro každou databázi na serveru B a serverem C. Každá databáze na serveru B bude mít dvě sekundární verze, jednu na serveru A a jednu na serveru C. Tím se zajistí, že primární databáze zůstanou chráněné během přechodu.
3. Odstraňte skupinu převzetí služeb při selhání. V tomto okamžiku se přihlášení nezdaří. Důvodem je, že se odstranily aliasy SQL pro naslouchací procesy skupiny převzetí služeb při selhání a brána nerozpozná název skupiny převzetí služeb při selhání.
4. Znovu vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi servery B a C. V tomto okamžiku se přihlášení zastaví, pokud se nezdaří.
5. Přidejte všechny primární databáze na B do nové skupiny převzetí služeb při selhání.
6. Proveďte plánované převzetí služeb při selhání ve skupině převzetí služeb při selhání a přepněte B a C. Nyní se Server C stane primární a B-sekundární. Všechny sekundární databáze na serveru A budou automaticky propojeny se základními počítači v jazyce C. Stejně jako v kroku 1 může převzetí služeb při selhání způsobit několik minut výpadků.
7. Vyřaďte Server A. Všechny databáze na straně budou automaticky odstraněny.

> [!IMPORTANT]
> Při odstranění skupiny převzetí služeb při selhání se odstraní také záznamy DNS pro koncové body naslouchacího procesu. V tomto okamžiku existuje nenulová pravděpodobnost někoho jiného, když se vytvoří skupina převzetí služeb při selhání nebo alias serveru se stejným názvem, což vám zabrání v jeho opětovném použití. K minimalizaci rizika nepoužívejte obecné názvy skupin pro převzetí služeb při selhání.

## <a name="best-practices-for-sql-managed-instance"></a>Osvědčené postupy pro spravovanou instanci SQL

Skupina automatického převzetí služeb při selhání musí být nakonfigurovaná na primární instanci, kterou připojí k sekundární instanci v jiné oblasti Azure.  Všechny databáze v instanci se budou replikovat do sekundární instance.

Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace pomocí spravované instance a skupiny automatického převzetí služeb při selhání.

![Diagram automatického převzetí služeb při selhání](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Podrobný kurz přidání spravované [instance do skupiny převzetí služeb při selhání](../managed-instance/failover-group-add-instance-tutorial.md) najdete v článku o podrobném kurzu přidání spravované instance SQL pro použití skupiny převzetí služeb při selhání.

Pokud vaše aplikace jako datovou vrstvu používá spravovanou instanci SQL, postupujte při navrhování provozní kontinuity podle těchto obecných pokynů:

### <a name="creating-the-secondary-instance"></a>Vytvoření sekundární instance

Pro zajištění nepřerušeného připojení k primární spravované instanci SQL po převzetí služeb při selhání musí být primární i sekundární instance ve stejné zóně DNS. Zaručujeme, že stejný certifikát s více doménami (SAN) se dá použít k ověření připojení klientů ke kterékoli z těchto dvou instancí ve skupině převzetí služeb při selhání. Když je vaše aplikace připravená na produkční nasazení, vytvořte sekundární SQL spravovanou instanci v jiné oblasti a ujistěte se, že se zóna DNS sdílí s primární SQL spravovanou instancí. Můžete to provést zadáním volitelného `DNS Zone Partner` parametru pomocí Azure Portal, PowerShellu nebo REST API.

> [!IMPORTANT]
> První spravovaná instance vytvořená v podsíti Určuje zónu DNS pro všechny následné instance ve stejné podsíti. To znamená, že dvě instance ze stejné podsítě nemohou patřit do různých zón DNS.

Další informace o vytváření sekundární instance SQL spravované v rámci stejné zóny DNS jako primární instance najdete v tématu [vytvoření sekundární spravované instance](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance).

### <a name="using-geo-paired-regions"></a>Použití geograficky spárovaných oblastí

Nasaďte spravované instance do [spárovaných oblastí](../../best-practices-availability-paired-regions.md) z důvodů výkonu. Spravované instance nacházející se v geograficky spárované oblasti mají mnohem lepší výkon v porovnání s nespárovanými oblastmi. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Povolení provozu replikace mezi dvěma instancemi

Vzhledem k tomu, že každá instance je izolovaná ve své vlastní virtuální síti, musí být povolen obousměrný provoz mezi těmito virtuální sítě. Viz [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Vytvoření skupiny převzetí služeb při selhání mezi spravovanými instancemi v různých předplatných

Skupinu převzetí služeb při selhání můžete vytvořit mezi spravovanými instancemi SQL ve dvou různých předplatných, pokud jsou předplatná přidružená ke stejnému [Azure Active Directory tenantovi](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology). Při použití rozhraní API PowerShellu to můžete provést zadáním `PartnerSubscriptionId` parametru pro sekundární SQL spravovanou instanci. Při použití REST API může mít každé ID instance obsažené v `properties.managedInstancePairs` parametru vlastní subscriptionID.
  
> [!IMPORTANT]
> Azure Portal nepodporuje vytváření skupin převzetí služeb při selhání v různých předplatných. Pro existující skupiny převzetí služeb při selhání v různých předplatných nebo skupinách prostředků se převzetí služeb při selhání nedá iniciovat ručně prostřednictvím portálu z primární spravované instance SQL. Místo toho ji můžete zahájit z instance v sekundární geografické oblasti.

### <a name="managing-failover-to-secondary-instance"></a>Správa převzetí služeb při selhání do sekundární instance

Skupina převzetí služeb při selhání bude spravovat převzetí služeb při selhání všech databází ve službě SQL Managed Instance. Po vytvoření skupiny se všechny databáze v instanci budou automaticky geograficky replikovat do sekundární instance služby SQL Managed Instance. Pomocí skupin převzetí služeb při selhání nemůžete zahájit částečné převzetí služeb při selhání podmnožiny databází.

> [!IMPORTANT]
> Pokud je databáze z primární spravované instance SQL odebrána, bude také automaticky zrušena na geograficky sekundární instanci SQL Managed.

### <a name="using-read-write-listener-for-oltp-workload"></a>Použití naslouchacího procesu pro čtení i zápis pro úlohu OLTP

Při provádění operací OLTP použijte `<fog-name>.zone_id.database.windows.net` jako adresu URL serveru a připojení se automaticky přesměrují na primární. Tato adresa URL se po převzetí služeb při selhání nemění. Převzetí služeb při selhání zahrnuje aktualizaci záznamu DNS, takže připojení klientů se přesměrují na nový primární až po aktualizaci mezipaměti DNS klienta. Vzhledem k tomu, že sekundární instance sdílí zónu DNS s primárním objektem, klientská aplikace se k ní bude moci znovu připojit pomocí stejného certifikátu sítě SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Připojení k sekundární instanci pomocí naslouchacího procesu jen pro čtení

Pokud máte logicky izolovanou úlohu jen pro čtení, která je odolná vůči určité zastaralosti dat, můžete v aplikaci použít sekundární databázi. Chcete-li se připojit přímo k geograficky replikovanému sekundárnímu serveru, použijte `<fog-name>.secondary.<zone_id>.database.windows.net` jako adresu URL serveru a připojení se provede přímo na geograficky replikovanou sekundární hodnotu.

> [!NOTE]
> V některých úrovních služby SQL Database podporuje použití [replik jen pro čtení](read-scale-out.md) k vyrovnávání zatížení úloh dotazů jen pro čtení pomocí kapacity jedné repliky jen pro čtení a použitím `ApplicationIntent=ReadOnly` parametru v připojovacím řetězci. Pokud jste nakonfigurovali geograficky replikované sekundární umístění, můžete se s využitím této možnosti připojit k replice jen pro čtení v primárním umístění nebo v geograficky replikovaném umístění.
>
> - Pokud se chcete připojit k replice jen pro čtení v primárním umístění, použijte `<fog-name>.<zone_id>.database.windows.net` .
> - Pokud se chcete připojit k replice jen pro čtení v sekundárním umístění, použijte `<fog-name>.secondary.<zone_id>.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Příprava na snížení výkonu

Typická aplikace Azure používá několik služeb Azure a skládá se z několika součástí. Automatické převzetí služeb při selhání ve skupině převzetí služeb při selhání se aktivuje v závislosti na stavu samotných komponent Azure SQL. Jiné služby Azure v primární oblasti nemusí být ovlivněny výpadkem a jejich komponenty mohou být v této oblasti stále dostupné. Jakmile se primární databáze přepne do oblasti zotavení po havárii, může se zvýšit latence mezi závislými komponentami. Aby se zabránilo dopadu vyšší latence na výkon aplikace, zajistěte redundanci všech komponent aplikace v oblasti zotavení po havárii a postupujte podle [pokynů pro zabezpečení sítě](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Příprava na ztrátu dat

Pokud dojde k výpadku, aktivuje se převzetí služeb při selhání pro čtení a zápis, pokud dojde ke ztrátě dat, a to na nejlepší z našich znalostí. V opačném případě počkejte na období, které jste určili. V opačném případě počká na období, které jste určili `GracePeriodWithDataLossHours` . Pokud jste určili `GracePeriodWithDataLossHours` , připravte se na ztrátu dat. Obecně platí, že při výpadkech Azure upřednostňuje dostupnost. Pokud nemůžete zaručit ztrátu dat, nezapomeňte nastavit GracePeriodWithDataLossHours na dostatečně velké číslo, například 24 hodin.

Aktualizace DNS naslouchacího procesu pro čtení a zápis proběhne hned po zahájení převzetí služeb při selhání. Tato operace nebude mít za následek ztrátu dat. Proces přepínání databázových rolí však může za normálních podmínek trvat až 5 minut. Až do dokončení, budou některé databáze v nové primární instanci pořád jen pro čtení. Pokud se převzetí služeb při selhání iniciuje pomocí PowerShellu, bude celá operace synchronní. Pokud je inicializována pomocí Azure Portal, uživatelské rozhraní bude označovat stav dokončení. Pokud je iniciována pomocí REST API, použijte mechanismus dotazování standardní Azure Resource Manager ke sledování dokončení.

> [!IMPORTANT]
> Ruční převzetí služeb při selhání můžete použít k přesunu primárních souborů zpátky do původního umístění. Pokud dojde ke zmírnění výpadku, který způsobil převzetí služeb při selhání, můžete primární databáze přesunout do původního umístění. K tomu je potřeba zahájit ruční převzetí služeb při selhání skupiny.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Změna sekundární oblasti skupiny převzetí služeb při selhání

Předpokládejme, že instance A je primární instancí, instance B je stávající sekundární instance a instance C je nová sekundární instance ve třetí oblasti.  Pro přechod proveďte následující kroky:

1. Vytvořte instanci C se stejnou velikostí jako a ve stejné zóně DNS.
2. Odstraní skupinu převzetí služeb při selhání mezi instancemi a a B. V tomto okamžiku se přihlašovací údaje nezdařily, protože aliasy SQL pro naslouchací procesy skupiny převzetí služeb při selhání byly odstraněny a brána nerozpozná název skupiny převzetí služeb při selhání. Sekundární databáze budou odpojeny od primárních databází a stanou se pro čtení a zápis databáze.
3. Vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi instancí A a C. postupujte podle pokynů v [kurzu skupina převzetí služeb při selhání s SQL Managed instance](../managed-instance/failover-group-add-instance-tutorial.md). Jedná se o datovou operaci, která bude dokončena, pokud jsou všechny databáze z instance A synchronizovány.
4. Odstraňte instanci B, pokud není potřeba, aby nedocházelo k zbytečným poplatkům.

> [!NOTE]
> Po kroku 2 a až do dokončení kroku 3 databáze v instanci A zůstanou nechráněné před závažným selháním instance A.

### <a name="changing-primary-region-of-the-failover-group"></a>Změna primární oblasti skupiny převzetí služeb při selhání

Řekněme, že instance A je primární instancí, instance B je stávající sekundární instance a instance C je nová primární instance ve třetí oblasti.  Pro přechod proveďte následující kroky:

1. Vytvořte instanci C se stejnou velikostí jako B a ve stejné zóně DNS.
2. Připojení k instanci B a ruční převzetí služeb při selhání pro přepnutí primární instance na B. instance A se stane novou sekundární instancí automaticky.
3. Odstraní skupinu převzetí služeb při selhání mezi instancemi a a B. V tomto okamžiku se přihlašovací údaje nezdařily, protože aliasy SQL pro naslouchací procesy skupiny převzetí služeb při selhání byly odstraněny a brána nerozpozná název skupiny převzetí služeb při selhání. Sekundární databáze budou odpojeny od primárních databází a stanou se pro čtení a zápis databáze.
4. Vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi instancí A a C. postupujte podle pokynů v [kurzu skupina převzetí služeb při selhání pomocí spravované instance](../managed-instance/failover-group-add-instance-tutorial.md). Jedná se o datovou operaci, která bude dokončena, pokud jsou všechny databáze z instance A synchronizovány.
5. Odstraňte instanci A, pokud není potřeba, aby nedocházelo k zbytečným poplatkům.

> [!CAUTION]
> Po kroku 3 a až do dokončení kroku 4 databáze v instanci A zůstanou nechráněné z závažného selhání instance A.

> [!IMPORTANT]
> Při odstranění skupiny převzetí služeb při selhání se odstraní také záznamy DNS pro koncové body naslouchacího procesu. V tomto okamžiku existuje nenulová pravděpodobnost někoho jiného, když se vytvoří skupina převzetí služeb při selhání nebo alias serveru se stejným názvem, což vám zabrání v jeho opětovném použití. K minimalizaci rizika nepoužívejte obecné názvy skupin pro převzetí služeb při selhání.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Povolit scénáře závislé na objektech ze systémových databází
Systémové databáze nejsou replikovány do sekundární instance ve skupině převzetí služeb při selhání. Chcete-li povolit scénáře, které závisí na objektech ze systémových databází, v sekundární instanci nezapomeňte vytvořit stejné objekty v sekundárním objektu. Pokud například plánujete použít v sekundární instanci stejné přihlašovací údaje, nezapomeňte je vytvořit se stejným identifikátorem SID. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Skupiny převzetí služeb při selhání a zabezpečení sítě

U některých aplikací pravidla zabezpečení vyžadují, aby byl síťový přístup k datové vrstvě omezený na určitou součást nebo součásti, jako je například virtuální počítač, Webová služba atd. Tento požadavek představuje některé problémy s návrhem kontinuity podnikových aplikací a používání skupin převzetí služeb při selhání. Při implementaci takového omezeného přístupu Vezměte v úvahu následující možnosti.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Používání skupin převzetí služeb při selhání a pravidel virtuální sítě

Pokud používáte [Virtual Network koncových bodů a pravidel služby](vnet-service-endpoint-rule-overview.md) pro omezení přístupu k databázi v SQL Database nebo spravované instanci SQL, uvědomte si, že každý koncový bod služby virtuální sítě se vztahuje jenom na jednu oblast Azure. Koncový bod nepovoluje, aby komunikace z podsítě přijímala jiné oblasti. Proto se k primární databázi mohou připojit pouze klientské aplikace nasazené ve stejné oblasti. Vzhledem k tomu, že při převzetí služeb při selhání dojde k přesměrování klientských relací SQL Database na server v jiné (sekundární) oblasti, tyto relace selžou, pokud pocházejí z klienta mimo tuto oblast. Z tohoto důvodu není možné povolit zásady automatického převzetí služeb při selhání, pokud jsou zúčastněné servery nebo instance zahrnuté v pravidlech Virtual Network. Pokud chcete podporovat ruční převzetí služeb při selhání, postupujte podle těchto kroků:

1. Zřízení redundantních kopií front-endové komponenty aplikace (webová služba, virtuální počítače atd.) v sekundární oblasti
2. Konfigurovat [pravidla virtuální sítě](vnet-service-endpoint-rule-overview.md) jednotlivě pro primární a sekundární server
3. Povolení [převzetí služeb při selhání front-endu pomocí konfigurace Traffic Manageru](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Iniciujte ruční převzetí služeb při selhání při zjištění výpadku. Tato možnost je optimalizovaná pro aplikace, které vyžadují konzistentní latenci mezi front-end a datovou vrstvou, a podporuje obnovení, pokud je vliv na front-end, datovou vrstvu nebo obojí.

> [!NOTE]
> Pokud k vyrovnávání zatížení úlohy jen pro čtení používáte **naslouchací proces jen pro čtení** , ujistěte se, že je tato úloha spuštěná na virtuálním počítači nebo jiném prostředku v sekundární oblasti, aby se mohla připojit k sekundární databázi.

### <a name="use-failover-groups-and-firewall-rules"></a>Použití skupin převzetí služeb při selhání a pravidel brány firewall

Pokud váš plán provozní kontinuity vyžaduje převzetí služeb při selhání pomocí skupin s automatickým převzetím služeb při selhání, můžete omezit přístup k databázi v SQL Database pomocí tradičních pravidel brány firewall. K podpoře automatického převzetí služeb při selhání použijte následující postup:

1. [Vytvoření veřejné IP adresy](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Vytvořte veřejný Nástroj pro vyrovnávání zatížení](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) a přiřaďte k němu veřejnou IP adresu.
3. [Vytvoření virtuální sítě a virtuálních počítačů](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) pro součásti front-endu
4. [Vytvořte skupinu zabezpečení sítě](../../virtual-network/security-overview.md) a nakonfigurujte příchozí připojení.
5. Ujistěte se, že jsou odchozí připojení otevřená pro Azure SQL Database pomocí [značky služby](../../virtual-network/security-overview.md#service-tags)SQL.
6. Vytvořte [SQL Database pravidlo brány firewall](firewall-configure.md) , které povolí příchozí provoz z veřejné IP adresy, kterou jste vytvořili v kroku 1.

Další informace o konfiguraci odchozího přístupu a o tom, jaká IP adresa se má použít v pravidlech brány firewall, najdete v tématu [odchozí připojení nástroje pro vyrovnávání zatížení](../../load-balancer/load-balancer-outbound-connections.md).

Výše uvedená konfigurace zajistí, že automatické převzetí služeb při selhání nebude blokovat připojení z front-endové komponenty a předpokládá, že aplikace dokáže tolerovat delší latenci mezi front-end a datovou vrstvou.

> [!IMPORTANT]
> Pro zajištění kontinuity podnikových důvodů pro regionální výpadky musíte zajistit geografickou redundanci pro front-end komponenty i databáze.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Povolení geografické replikace mezi spravovanými instancemi a jejich virtuálními sítěmi

Při nastavování skupiny převzetí služeb při selhání mezi primárními a sekundárními spravovanými instancemi SQL ve dvou různých oblastech se každá instance izoluje pomocí nezávislé virtuální sítě. Pokud chcete zajistit, aby provoz replikace mezi těmito virtuální sítěmi splňoval tyto požadavky:

- Tyto dvě instance spravované instance SQL musí být v různých oblastech Azure.
- Tyto dvě instance spravované instance SQL musí být stejné úrovně služby a musí mít stejnou velikost úložiště.
- Vaše sekundární instance spravované instance SQL musí být prázdná (žádné uživatelské databáze).
- Virtuální sítě používané instancemi spravované instance SQL musí být připojené prostřednictvím [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [Express Route](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Pokud jsou tyto dvě virtuální sítě propojené prostřednictvím místní sítě, ujistěte se, že žádné pravidlo firewallu neblokuje porty 5022 a 11000–11999. Globální partnerský vztah virtuálních sítí se podporuje s omezením popsaným v níže uvedené poznámce.

   > [!IMPORTANT]
   > [V 9/22/2020 jsme oznámili globální partnerské vztahy virtuálních sítí pro nově vytvořené virtuální clustery](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). To znamená, že globální partnerský vztah virtuálních sítí je podporován pro spravované instance SQL vytvořené v prázdných podsítích po datu oznámení a také pro všechny následné spravované instance vytvořené v těchto podsítích. Pro všechny ostatní podpory partnerského vztahu spravované instance SQL je omezená na sítě ve stejné oblasti v důsledku [omezení globálního partnerského vztahu virtuálních sítí](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Další podrobnosti najdete v článku o nejčastějších dotazech v příslušné části [Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . 

- Dvě spravované instance SQL virtuální sítě nemůžou mít překrývající se IP adresy.
- Je potřeba nastavit skupiny zabezpečení sítě (NSG) tak, aby porty 5022 a 11000–12000 byly otevřené pro příchozí i odchozí připojení z podsítě druhé spravované instance. Důvodem je umožnit provoz replikace mezi instancemi.

   > [!IMPORTANT]
   > Nesprávně nakonfigurovaná pravidla zabezpečení NSG vede k zablokování operací kopírování databáze.

- Sekundární instance spravované SQL je nakonfigurovaná se správným ID zóny DNS. Zóna DNS je vlastnost spravované instance SQL a základního virtuálního clusteru a její ID je zahrnuté do adresy názvu hostitele. ID zóny se generuje jako náhodný řetězec, když se v každé virtuální síti vytvoří první spravovaná instance SQL a stejné ID se přiřadí ke všem ostatním instancím ve stejné podsíti. Po přiřazení se zóna DNS nedá změnit. Spravované instance SQL zahrnuté ve stejné skupině převzetí služeb při selhání musí sdílet zónu DNS. Toho dosáhnete tak, že při vytváření sekundární instance předáte ID zóny primární instance jako hodnotu parametru DnsZonePartner.

   > [!NOTE]
   > Podrobný kurz týkající se konfigurace skupin převzetí služeb při selhání pomocí spravované instance SQL najdete v tématu [Přidání spravované instance SQL do skupiny převzetí služeb při selhání](../managed-instance/failover-group-add-instance-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Upgrade nebo downgrade primární databáze

Primární databázi můžete upgradovat nebo downgradovat na jinou výpočetní velikost (v rámci stejné úrovně služby, ne mezi Pro obecné účely a Pro důležité obchodní informace), aniž byste museli odpojit sekundární databáze. Při upgradu doporučujeme nejdřív upgradovat všechny sekundární databáze a potom upgradovat primární. Když se downgrade, obrátí se pořadí: nejprve nastavte downgrade primární databáze a pak vytvořte downgrade všech sekundárních databází. Toto doporučení se vynucuje při upgradu nebo downgradu databáze na jinou úroveň služby.

Toto pořadí se doporučuje zejména proto, aby se během procesu upgradu nebo downgradu zabránilo problému s přetížením sekundární instance s nižší skladovou položkou, kterou by tak bylo potřeba znovu naplnit. Tomuto problému můžete zabránit také tak, že primární instanci nastavíte jen pro čtení – bude to však mít vliv na všechny úlohy čtení z primární instance i zápisu na ni.

> [!NOTE]
> Pokud jste jako součást konfigurace skupiny převzetí služeb při selhání vytvořili sekundární databázi, nedoporučuje se ji převést na downgrade sekundární databáze. Tím zajistíte, že vaše datová úroveň má dostatečnou kapacitu pro zpracování pravidelného zatížení po aktivaci převzetí služeb při selhání.

## <a name="preventing-the-loss-of-critical-data"></a>Zabránění ztrátě důležitých dat

V důsledku vysoké latence sítí WAN používá průběžné kopírování mechanismus asynchronní replikace. Asynchronní replikace způsobuje nenevyhnutelnou ztrátu dat, pokud dojde k selhání. Některé aplikace ale nemusí vyžadovat žádnou ztrátu dat. Pro ochranu těchto důležitých aktualizací může vývojář aplikace volat [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systémové procedury ihned po potvrzení transakce. Volání `sp_wait_for_database_copy_sync` blokuje volající vlákno, dokud se poslední potvrzená transakce nepřenesla do sekundární databáze. Nečeká ale na přenesení transakcí a jejich potvrzení na sekundárním počítači. `sp_wait_for_database_copy_sync` je vymezen na konkrétní odkaz průběžné kopírování. Tento postup může volat každý uživatel s právy pro připojení k primární databázi.

> [!NOTE]
> `sp_wait_for_database_copy_sync` zabraňuje ztrátě dat po převzetí služeb při selhání, ale nezaručuje úplnou synchronizaci pro přístup pro čtení. Zpoždění způsobené `sp_wait_for_database_copy_sync` voláním procedury může být významné a závisí na velikosti transakčního protokolu v době volání.

## <a name="failover-groups-and-point-in-time-restore"></a>Skupiny převzetí služeb při selhání a obnovení k bodu v čase

Informace o použití obnovení k časovému okamžiku se skupinami převzetí služeb při selhání najdete v tématu [Obnovení bodu v čase (PITR)](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Omezení skupin převzetí služeb při selhání

Mějte na paměti následující omezení:

- Skupiny převzetí služeb při selhání nelze vytvořit mezi dvěma servery nebo instancemi ve stejných oblastech Azure.
- Skupiny převzetí služeb při selhání nelze přejmenovat. Budete muset skupinu odstranit a znovu ji vytvořit s jiným názvem.
- Přejmenování databáze není u instancí ve skupině převzetí služeb při selhání podporováno. Aby bylo možné databázi přejmenovat, budete muset dočasně odstranit skupinu převzetí služeb při selhání.
- Systémové databáze nejsou replikovány do sekundární instance ve skupině převzetí služeb při selhání. Proto by scénáře závislé na objektech ze systémových databází nemohly být na sekundární instanci možné, pokud nejsou objekty ručně vytvořeny na sekundárním objektu.

## <a name="programmatically-managing-failover-groups"></a>Programová správa skupin převzetí služeb při selhání

Jak už bylo popsáno dříve, skupiny automatického převzetí služeb při selhání a aktivní geografická replikace je také možné spravovat programově pomocí Azure PowerShell a REST API. V následujících tabulkách jsou popsány sady příkazů, které jsou k dispozici. Aktivní geografická replikace obsahuje sadu Azure Resource Manager rozhraní API pro správu, včetně rutin [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) a [Azure PowerShell](https://docs.microsoft.com/powershell/azure/). Tato rozhraní API vyžadují použití skupin prostředků a podporují zabezpečení na základě rolí (RBAC). Další informace o tom, jak implementovat role přístupu, najdete v tématu [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover"></a>Správa převzetí služeb při selhání SQL Database

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Rutina | Popis |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji na primární i sekundární servery.|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Odebere skupinu převzetí služeb při selhání ze serveru. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Načte konfiguraci skupiny převzetí služeb při selhání. |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Upraví konfiguraci skupiny převzetí služeb při selhání. |
| [Switch – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Přidá jednu nebo více databází do skupiny převzetí služeb při selhání.|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Příkaz | Popis |
| --- | --- |
| [AZ SQL Failover-Group Create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji na primární i sekundární servery.|
| [AZ SQL Failover-Group DELETE](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Odebere skupinu převzetí služeb při selhání ze serveru. |
| [AZ SQL Failover-Group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Načte konfiguraci skupiny převzetí služeb při selhání. |
| [AZ SQL Failover-Group Update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Upraví konfiguraci skupiny převzetí služeb při selhání nebo přidá jednu nebo více databází do skupiny převzetí služeb při selhání.|
| [AZ SQL Failover-Group set-Primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server. |

# <a name="rest-api"></a>[Rozhraní REST API](#tab/rest-api)

| Rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Vytvoří nebo aktualizuje skupinu převzetí služeb při selhání. |
| [Odstranit skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Odebere skupinu převzetí služeb při selhání ze serveru. |
| [Převzetí služeb při selhání (plánováno)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Aktivuje převzetí služeb při selhání z aktuálního primárního serveru na sekundární server s plnou synchronizací dat.|
| [Vynucené převzetí služeb při selhání umožňuje ztrátu dat](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Aktivuje převzetí služeb při selhání z aktuálního primárního serveru na sekundární server bez synchronizace dat. Tato operace může způsobit ztrátu dat. |
| [Získat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Načte konfiguraci skupiny převzetí služeb při selhání. |
| [Vypsat skupiny převzetí služeb při selhání podle serveru](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Vypíše skupiny převzetí služeb při selhání na serveru. |
| [Aktualizace skupiny převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aktualizuje konfiguraci skupiny převzetí služeb při selhání. |

---

### <a name="manage-sql-managed-instance-failover"></a>Správa převzetí služeb při selhání spravované instance SQL


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Rutina | Popis |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji na primární i sekundární instanci.|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Upraví konfiguraci skupiny převzetí služeb při selhání.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Načte konfiguraci skupiny převzetí služeb při selhání.|
| [Switch – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání do sekundární instance.|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Odebere skupinu převzetí služeb při selhání.|


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Příkaz | Popis |
| --- | --- |
| [AZ SQL Failover-Group Create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji na primární i sekundární servery.|
| [AZ SQL Failover-Group DELETE](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Odebere skupinu převzetí služeb při selhání ze serveru. |
| [AZ SQL Failover-Group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Načte konfiguraci skupiny převzetí služeb při selhání. |
| [AZ SQL Failover-Group Update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Upraví konfiguraci skupiny převzetí služeb při selhání nebo přidá jednu nebo více databází do skupiny převzetí služeb při selhání.|
| [AZ SQL Failover-Group set-Primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server. |

# <a name="rest-api"></a>[Rozhraní REST API](#tab/rest-api)

| Rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Vytvoří nebo aktualizuje konfiguraci skupiny převzetí služeb při selhání. |
| [Odstranit skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Odebere skupinu převzetí služeb při selhání z instance. |
| [Převzetí služeb při selhání (plánováno)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Aktivuje převzetí služeb při selhání z aktuální primární instance do této instance s úplnou synchronizací dat. |
| [Vynucené převzetí služeb při selhání umožňuje ztrátu dat](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Aktivuje převzetí služeb při selhání z aktuální primární instance do sekundární instance bez synchronizace dat. Tato operace může způsobit ztrátu dat. |
| [Získat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Načte konfiguraci skupiny převzetí služeb při selhání. |
| [Seznam skupin převzetí služeb při selhání – seznam podle umístění](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Vypíše skupiny převzetí služeb při selhání v umístění. |

---

## <a name="next-steps"></a>Další kroky

- Podrobné pokyny najdete v tématu.
  - [Přidání SQL Database do skupiny převzetí služeb při selhání](failover-group-add-single-database-tutorial.md)
  - [Přidání elastického fondu do skupiny převzetí služeb při selhání](failover-group-add-elastic-pool-tutorial.md)
  - [Přidání spravované instance SQL do skupiny převzetí služeb při selhání](../managed-instance/failover-group-add-instance-tutorial.md)
- Ukázkové skripty najdete v těchto tématech:
  - [Konfigurace aktivní geografické replikace pro Azure SQL Database pomocí PowerShellu](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Použití PowerShellu ke konfiguraci aktivní geografické replikace pro databázi ve fondu v Azure SQL Database](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Přidání Azure SQL Database do skupiny převzetí služeb při selhání pomocí PowerShellu](scripts/add-database-to-failover-group-powershell.md)
- Přehled provozní kontinuity a scénářů najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md) .
- Další informace o Azure SQL Database automatizovaných zálohách najdete v tématu [SQL Database automatizované zálohy](automated-backups-overview.md).
- Další informace o použití automatizovaných záloh pro obnovení najdete v tématu [obnovení databáze ze zálohy iniciované službou](recovery-using-backups.md).
- Další informace o požadavcích na ověřování nového primárního serveru a databáze najdete v článku [SQL Database zabezpečení po zotavení po havárii](active-geo-replication-security-configure.md).
