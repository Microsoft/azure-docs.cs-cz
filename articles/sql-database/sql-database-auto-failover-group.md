---
title: Skupiny převzetí služeb při selhání
description: Skupiny automatického převzetí služeb při selhání je funkce SQL Database, která umožňuje spravovat replikaci a automatické nebo koordinované převzetí služeb při selhání skupiny databází na serveru SQL Database nebo ve všech databázích ve spravované instanci.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355810"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Použití skupin automatického převzetí služeb při selhání k zajištění transparentního a koordinovaného převzetí služeb při selhání více databází

Skupiny s automatickým převzetím služeb při selhání je funkce SQL Database, která umožňuje spravovat replikaci a převzetí služeb při selhání skupiny databází na serveru SQL Database nebo všech databázích ve spravované instanci do jiné oblasti. Jedná se o deklarativní abstrakci nad stávající [aktivní geografickou replikací](sql-database-active-geo-replication.md) , která je navržená tak, aby zjednodušila nasazení a správu geograficky replikovaných databází se škálováním. Převzetí služeb při selhání můžete iniciovat ručně nebo je můžete delegovat na službu SQL Database na základě uživatelsky definované zásady. Druhá možnost umožňuje automaticky obnovit více souvisejících databází v sekundární oblasti po závažných chybách nebo jiné neplánované události, které mají za následek úplnou nebo částečnou ztrátu dostupnosti služby SQL Database v primární oblasti. Skupina převzetí služeb při selhání může zahrnovat jednu nebo více databází, které obvykle používá stejná aplikace. Kromě toho můžete použít čitelné sekundární databáze pro přesměrování zatížení dotazů jen pro čtení. Vzhledem k tomu, že skupiny s automatickým převzetím služeb při selhání zahrnují více databází, je nutné tyto databáze nakonfigurovat na primárním serveru Skupiny s automatickým převzetím služeb při selhání podporují replikaci všech databází ve skupině jenom na jeden sekundární server v jiné oblasti.

> [!NOTE]
> Když pracujete s jednou nebo ve fondu databází na serveru SQL Database a chcete více sekundárních umístění ve stejné nebo jiné oblasti, použijte [aktivní geografickou replikaci](sql-database-active-geo-replication.md). 

Pokud používáte skupiny s automatickým převzetím služeb při selhání se zásadami automatického převzetí služeb při selhání, jakékoli výpadky, které mají vliv na jednu nebo několik databází ve skupině, mají za následek automatické převzetí Typicky se jedná o incidenty, které se nedají samy zmírnit pomocí integrovaných automatických operací s vysokou dostupností. Mezi příklady triggerů převzetí služeb při selhání patří incident, který je způsobený vyzváněním nebo řídicím kanálem klienta SQL z důvodu nevracení paměti jádra operačního systému na několika výpočetních uzlech, nebo incidentu, který vychází z jednoho nebo více okruhů klientů, je mimo provoz, protože během ro byl vyjmut chybný síťový kabel utine vyřazení hardwaru z provozu.  Další informace najdete v tématu [SQL Database vysoké dostupnosti](sql-database-high-availability.md).

Skupiny s automatickým převzetím služeb při selhání poskytují koncové body naslouchacího procesu pro čtení i zápis a jen pro čtení, které během převzetí služeb při selhání zůstanou beze změny. Bez ohledu na to, jestli používáte ruční nebo automatickou aktivaci při selhání, převzetí služeb při selhání přepne všechny sekundární databáze ve skupině na primární. Po převzetí služeb při selhání databáze je záznam DNS automaticky aktualizován pro přesměrování koncových bodů do nové oblasti. Konkrétní data RPO a RTO najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md).

Pokud používáte skupiny s automatickým převzetím služeb při selhání se zásadami automatického převzetí služeb při selhání, dojde při jakémkoli výpadku, který ovlivňuje databáze na serveru SQL Database nebo spravované instanci, na automatické převzetí Skupinu automatického převzetí služeb při selhání můžete spravovat pomocí:

- [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [Azure CLI: Skupina převzetí služeb při selhání](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: Skupina převzetí služeb při selhání](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: Skupina převzetí služeb při selhání](/rest/api/sql/failovergroups).

Po převzetí služeb při selhání zajistěte, aby byly požadavky na ověřování pro server a databázi nakonfigurovány na novém primárním serveru. Podrobnosti najdete v tématu [SQL Database Security po zotavení po havárii](sql-database-geo-replication-security-config.md).

Aby bylo možné dosáhnout reálné provozní kontinuity, Přidání redundance databáze mezi datacentry je pouze součástí řešení. Kompletní obnovení aplikace (služby) po závažných chybách vyžaduje obnovení všech součástí, které tvoří službu a všechny závislé služby. Mezi tyto komponenty patří klientský software (například prohlížeč s vlastním JavaScriptem), webové front-endy, úložiště a DNS. Je velmi důležité, aby všechny součásti byly odolné vůči stejnou selhání a byly k dispozici v rámci plánované doby obnovení (RTO) vaší aplikace. Proto je potřeba identifikovat všechny závislé služby a porozumět zárukám a funkcím, které poskytují. Pak musíte provést vhodné kroky, abyste zajistili, že vaše služba funguje při převzetí služeb při selhání služeb, na kterých závisí. Další informace o návrhu řešení pro zotavení po havárii najdete v tématu [navrhování cloudových řešení pro zotavení po havárii pomocí aktivní geografické replikace](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Terminologie a možnosti skupin pro automatické převzetí služeb při selhání

- **Skupina převzetí služeb při selhání (MLHOVé)**

  Skupina převzetí služeb při selhání je pojmenovaná skupina databází spravovaná jedním SQL Database serverem nebo v rámci jedné spravované instance, která může převzít služby při selhání jako jednotka v jiné oblasti v případě výpadku v primární oblasti, nebo když některé primární databáze nebudou k dispozici. Při vytvoření pro spravované instance obsahuje skupina převzetí služeb při selhání všechny uživatelské databáze v instanci, a proto lze v instanci nakonfigurovat pouze jednu skupinu převzetí služeb při selhání.
  
  > [!IMPORTANT]
  > Název skupiny převzetí služeb při selhání musí být globálně jedinečný v rámci `.database.windows.net` domény.

- **SQL Database servery**

     U SQL Databasech serverů je možné do skupiny převzetí služeb při selhání umístit některé nebo všechny uživatelské databáze na jednom serveru SQL Database. SQL Database Server navíc podporuje více skupin převzetí služeb při selhání na jednom serveru SQL Database.

- **Primární**

  Server SQL Database nebo spravovaná instance hostující primární databáze ve skupině převzetí služeb při selhání.

- **Sekundární**

  Server SQL Database nebo spravovaná instance hostující sekundární databáze ve skupině převzetí služeb při selhání. Sekundární nemůže být ve stejné oblasti jako primární.

- **Přidávání samostatných databází do skupiny převzetí služeb při selhání**

  Do stejné skupiny převzetí služeb při selhání můžete umístit několik samostatných databází na stejném SQL Databaseovém serveru. Pokud přidáte do skupiny převzetí služeb při selhání jednu databázi, automaticky vytvoří sekundární databázi pomocí stejné edice a výpočetní velikosti na sekundárním serveru.  Tento server jste zadali při vytvoření skupiny převzetí služeb při selhání. Pokud přidáváte databázi, která už má sekundární databázi na sekundárním serveru, toto propojení geografické replikace je zděděné skupinou. Když přidáváte databázi, která už má sekundární databázi na serveru, který není součástí skupiny převzetí služeb při selhání, vytvoří se na sekundárním serveru nový sekundární objekt.

  > [!IMPORTANT]
  > Ujistěte se, že sekundární server nemá databázi se stejným názvem, pokud se nejedná o existující sekundární databázi. Ve skupinách převzetí služeb při selhání pro spravovanou instanci se replikují všechny uživatelské databáze. V této skupině převzetí služeb při selhání nelze vybrat podmnožinu uživatelských databází pro replikaci.

- **Přidání databází do elastického fondu do skupiny převzetí služeb při selhání**

  Do jedné skupiny převzetí služeb při selhání můžete umístit všechny nebo několik databází v rámci elastického fondu. Pokud je primární databáze v elastickém fondu, sekundární se automaticky vytvoří v elastickém fondu se stejným názvem (sekundární fond). Je nutné zajistit, aby sekundární server obsahoval elastický fond se stejným přesným názvem a dostatek volné kapacity pro hostování sekundárních databází, které budou vytvořeny skupinou převzetí služeb při selhání. Pokud přidáte databázi do fondu, který již má sekundární databázi v sekundárním fondu, bude tento odkaz geografická replikace zděděn skupinou. Když přidáváte databázi, která už má sekundární databázi na serveru, který není součástí skupiny převzetí služeb při selhání, vytvoří se v sekundárním fondu nový sekundární objekt.
  
- **Počáteční osazení** 

  Když přidáváte databáze, elastické fondy nebo spravované instance do skupiny převzetí služeb při selhání, před spuštěním replikace dat se vytvoří prvotní fáze osazení. Počáteční fáze osazení je nejdelší a nejnáročná operace. Po dokončení počátečního osazení se data synchronizují a pak se replikují jenom následné změny dat. Čas potřebný k dokončení počátečního dosazení závisí na velikosti dat, počtu replikovaných databází a rychlosti propojení mezi entitami ve skupině převzetí služeb při selhání. Za běžných okolností je typická rychlost osazení 50-500 GB pro izolovanou databázi nebo elastický fond a 18-35 GB za hodinu pro spravovanou instanci. Osazení se provádí pro všechny databáze paralelně. Můžete použít uvedenou rychlost osazení spolu s počtem databází a celkovou velikostí dat pro odhad, jak dlouho bude prvotní fáze osazení trvat před spuštěním replikace dat.

  U spravovaných instancí je potřeba při odhadu doby prvotní fáze osazení také vzít v úvahu rychlost propojení Express Route mezi těmito dvěma instancemi. V případě, že rychlost propojení mezi dvěma instancemi je pomalejší, než je potřeba, je pravděpodobné, že čas na počáteční hodnotu bude mít vliv. Můžete použít uvedenou rychlost osazení, počet databází, celkovou velikost dat a rychlost připojení k odhadu, jak dlouho bude prvotní fáze osazení trvat před spuštěním replikace dat. Například pro jednu 100 GB databáze by počáteční fáze osazení ponechala v rozmezí 2,8-5,5 hodin, pokud je odkaz schopný zabírat 35 GB za hodinu. Pokud odkaz může přenášet jenom 10 GB za hodinu, pak bude dosazení 100 GB databáze trvat přibližně 10 hodin. Pokud existuje více databází k replikaci, je osazení provedeno paralelně a při kombinaci s pomalým připojením může prvotní fáze osazení trvat delší dobu, zejména v případě, že paralelní osazení dat ze všech databází překračuje dostupné množství. Šířka pásma propojení. Pokud je šířka pásma sítě mezi dvěma instancemi omezená a do skupiny převzetí služeb při selhání přidáváte víc spravovaných instancí, zvažte postupně přidání více spravovaných instancí do skupiny převzetí služeb při selhání, jednu po druhé.

  
- **Zóna DNS**

  Jedinečné ID, které se automaticky vygeneruje při vytvoření nové instance. Pro tuto instanci se zřídí certifikát s více doménami (SAN) pro ověřování připojení klientů k jakékoli instanci ve stejné zóně DNS. Tyto dvě spravované instance ve stejné skupině převzetí služeb při selhání musí sdílet zónu DNS.
  
  > [!NOTE]
  > Pro skupiny převzetí služeb při selhání vytvořené pro SQL Database servery se nevyžaduje ID zóny DNS.

- **Naslouchací proces pro čtení a zápis skupiny převzetí služeb při selhání**

  Záznam DNS CNAME, který odkazuje na aktuální primární adresu URL. Automaticky se vytvoří při vytvoření skupiny převzetí služeb při selhání a umožňuje, aby se při převzetí služeb při selhání znovu znovu připojila úloha SQL pro čtení i zápis k primární databázi. Když je na serveru SQL Database vytvořená skupina převzetí služeb při selhání, záznam CNAME DNS pro adresu URL naslouchacího procesu se vytvoří jako `<fog-name>.database.windows.net`. Pokud je skupina převzetí služeb při selhání vytvořena na spravované instanci, záznam CNAME DNS pro adresu URL naslouchacího procesu se vytvoří jako `<fog-name>.zone_id.database.windows.net`.

- **Skupina převzetí služeb při selhání – naslouchací proces jen pro čtení**

  Záznam CNAME DNS vytvořený, který odkazuje na naslouchací proces jen pro čtení, který odkazuje na adresu URL sekundárního objektu. Automaticky se vytvoří při vytvoření skupiny převzetí služeb při selhání a umožňuje úlohy SQL, která je jen pro čtení, k sekundárnímu připojení k sekundárnímu pomocí zadaných pravidel vyrovnávání zatížení. Když je na serveru SQL Database vytvořená skupina převzetí služeb při selhání, záznam CNAME DNS pro adresu URL naslouchacího procesu se vytvoří jako `<fog-name>.secondary.database.windows.net`. Pokud je skupina převzetí služeb při selhání vytvořena na spravované instanci, záznam CNAME DNS pro adresu URL naslouchacího procesu se vytvoří jako `<fog-name>.zone_id.secondary.database.windows.net`.

- **Zásady automatického převzetí služeb při selhání**

  Ve výchozím nastavení je skupina převzetí služeb při selhání nakonfigurovaná se zásadami automatického převzetí služeb při selhání. Služba SQL Database aktivuje převzetí služeb při selhání po zjištění selhání a vypršení lhůty. Systém musí ověřit, jestli se výpadek nedá zmírnit vestavěnou [infrastrukturou vysoké dostupnosti služby SQL Database](sql-database-high-availability.md) z důvodu rozsahu dopadu. Pokud chcete řídit pracovní postup převzetí služeb při selhání z aplikace, můžete automatické převzetí služeb při selhání vypnout.
  
  > [!NOTE]
  > Vzhledem k tomu, že ověřování škály výpadku a jak rychle se dá zmírnit, zahrnuje lidské akce tým provozu, období odkladu nelze nastavit pod jednu hodinu.  Toto omezení se vztahuje na všechny databáze ve skupině převzetí služeb při selhání bez ohledu na stav synchronizace dat. 

- **Zásada převzetí služeb při selhání jen pro čtení**

  Ve výchozím nastavení je převzetí služeb při selhání naslouchacího procesu jen pro čtení zakázané. Zajišťuje, že výkon primárního z těchto primárních není ovlivněný, když je sekundární objekt v režimu offline. Ale také to znamená, že relace jen pro čtení se nebudou moci připojit až po obnovení sekundárního zařízení. Pokud nemůžete tolerovat výpadky relací jen pro čtení a jsou v pořádku, aby byly primárním serverem k dispozici pro provoz jen pro čtení i zápis pro čtení, a to na úkor možného snížení výkonu primární služby, můžete povolit převzetí služeb při selhání pro naslouchací proces jen pro čtení konfigurací vlastnosti `AllowReadOnlyFailoverToPrimary`. V takovém případě bude přenos, který je jen pro čtení, automaticky přesměrován na primární, pokud není k dispozici sekundární.

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

  Vzhledem k tomu, že primární a sekundární databáze jsou synchronizovány pomocí asynchronní replikace, může převzetí služeb při selhání dojít ke ztrátě dat. Zásady automatického převzetí služeb při selhání můžete přizpůsobit tak, aby odrážely odolnost vaší aplikace proti ztrátě dat. Konfigurací `GracePeriodWithDataLossHours`můžete určit, jak dlouho systém počká, než se iniciuje převzetí služeb při selhání, které pravděpodobně bude mít za následek ztrátu dat.

- **Několik skupin převzetí služeb při selhání**

  Můžete nakonfigurovat více skupin převzetí služeb při selhání pro stejnou dvojici serverů pro řízení škálování převzetí služeb při selhání. U každé skupiny dojde k selhání nezávisle. Pokud vaše aplikace pro více tenantů používá elastické fondy, můžete tuto možnost využít ke smíchání primárních a sekundárních databází v jednotlivých fondech. Tímto způsobem můžete snížit dopad výpadku jenom na polovinu klientů.

  > [!NOTE]
  > Spravovaná instance nepodporuje více skupin převzetí služeb při selhání.
  
## <a name="permissions"></a>Oprávnění

Oprávnění pro skupinu převzetí služeb při selhání se spravují prostřednictvím [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md). Role [přispěvatel SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) má všechna potřebná oprávnění ke správě skupin převzetí služeb při selhání.

### <a name="create-failover-group"></a>Vytvořit skupinu převzetí služeb při selhání

Chcete-li vytvořit skupinu převzetí služeb při selhání, budete potřebovat přístup k zápisu RBAC na primární i sekundární server a do všech databází ve skupině převzetí služeb při selhání. Pro spravovanou instanci potřebujete přístup pro zápis RBAC do primární i sekundární spravované instance, ale oprávnění pro jednotlivé databáze nejsou relevantní, protože jednotlivé databáze spravované instance nelze do skupiny převzetí služeb při selhání přidávat ani je z ní odebírat. 

### <a name="update-a-failover-group"></a>Aktualizace skupiny převzetí služeb při selhání

Chcete-li aktualizovat skupinu převzetí služeb při selhání, budete potřebovat přístup pro zápis RBAC do skupiny převzetí služeb při selhání a všechny databáze na aktuálním primárním serveru nebo spravované instanci.  

### <a name="failover-a-failover-group"></a>Převzetí služeb při selhání skupiny

Pokud chcete převzít služby při selhání skupiny převzetí služeb při selhání, budete potřebovat přístup k zápisu RBAC do skupiny převzetí služeb při selhání na novém primárním serveru nebo spravované instanci.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Osvědčené postupy použití skupin převzetí služeb při selhání s izolovanými databázemi a elastickými fondy

Skupina automatického převzetí služeb při selhání musí být nakonfigurovaná na primárním SQL Databasem serveru a bude připojena k sekundárnímu SQL Database serveru v jiné oblasti Azure. Skupiny mohou obsahovat všechny nebo některé databáze na těchto serverech. Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace s využitím více databází a skupiny s automatickým převzetím služeb při selhání.

![automatické převzetí služeb při selhání](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Podrobný kurz přidání izolované databáze do skupiny převzetí služeb při selhání najdete v tématu [Přidání izolované databáze do skupiny převzetí služeb při selhání](sql-database-single-database-failover-group-tutorial.md) .

Při navrhování služby s ohledem na provozní kontinuitu se řiďte těmito obecnými pokyny:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Použití jedné nebo několika skupin převzetí služeb při selhání pro správu převzetí služeb při selhání více databází

Jednu nebo více skupin s podporou převzetí služeb při selhání je možné vytvořit mezi dvěma servery v různých oblastech (primární a sekundární servery). Každá skupina může zahrnovat jednu nebo několik databází, které jsou obnoveny jako jednotka v případě výpadku v primární oblasti, nebo některé primární databáze nebudou k dispozici. Skupina převzetí služeb při selhání vytvoří geograficky sekundární databázi se stejným cílem služby jako primární. Pokud přidáte existující relaci geografické replikace do skupiny převzetí služeb při selhání, ujistěte se, že je geograficky sekundární nakonfigurovaná se stejnou úrovní služeb a výpočetní velikostí jako primární.
  
> [!IMPORTANT]
> Vytváření skupin převzetí služeb při selhání mezi dvěma servery v různých předplatných se v současné době nepodporuje u izolovaných databází a elastických fondů. Pokud primární nebo sekundární server přesunete do jiného předplatného po vytvoření skupiny převzetí služeb při selhání, může dojít k selhání požadavků na převzetí služeb při selhání a dalších operací.

### <a name="using-read-write-listener-for-oltp-workload"></a>Použití naslouchacího procesu pro čtení i zápis pro úlohu OLTP

Při provádění operací OLTP použijte jako adresu URL serveru `<fog-name>.database.windows.net` a připojení se automaticky přesměrují na primární. Tato adresa URL se po převzetí služeb při selhání nemění. Všimněte si, že při převzetí služeb při selhání je potřeba aktualizovat záznam DNS, aby se připojení klientů přesměrovala na nové primární až po aktualizaci mezipaměti DNS klienta.

### <a name="using-read-only-listener-for-read-only-workload"></a>Použití naslouchacího procesu jen pro čtení pro úlohu jen pro čtení

Pokud máte logicky izolovanou úlohu jen pro čtení, která je odolná vůči určité zastaralosti dat, můžete v aplikaci použít sekundární databázi. V případě relací jen pro čtení použijte jako adresu URL serveru `<fog-name>.secondary.database.windows.net` a připojení se automaticky přesměruje na sekundární. Je také vhodné určit v úmyslu přečíst si v připojovacím řetězci pomocí `ApplicationIntent=ReadOnly`. Pokud chcete zajistit, že se po převzetí služeb při selhání může znovu připojit úloha jen pro čtení, nebo pokud sekundární server přejde do režimu offline, ujistěte se, že jste nakonfigurovali vlastnost `AllowReadOnlyFailoverToPrimary` zásady převzetí služeb při selhání.

### <a name="preparing-for-performance-degradation"></a>Příprava na snížení výkonu

Typická aplikace Azure používá více služeb Azure a skládá se z několika součástí. Automatické převzetí služeb při selhání ve skupině převzetí služeb při selhání se aktivuje v závislosti na stavu samotných komponent Azure SQL. Jiné služby Azure v primární oblasti nemusí být ovlivněny výpadkem a jejich komponenty mohou být v této oblasti stále dostupné. Jakmile se primární databáze přepne do oblasti zotavení po havárii, může se zvýšit latence mezi závislými komponentami. Aby se zabránilo dopadu vyšší latence na výkon aplikace, zajistěte redundanci všech komponent aplikace v oblasti zotavení po havárii a postupujte podle [pokynů pro zabezpečení sítě](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Příprava na ztrátu dat

Pokud se zjistí výpadek, SQL počká na období zadané v `GracePeriodWithDataLossHours`. Výchozí hodnota je 1 hodina. Pokud nemůžete zaručit ztrátu dat, nezapomeňte nastavit `GracePeriodWithDataLossHours` na dostatečně velké číslo, například 24 hodin. K navrácení služeb po obnovení ze sekundárního na primární se používá ruční převzetí služeb při selhání pomocí skupiny.

> [!IMPORTANT]
> Elastické fondy s 800 nebo méně DTU a více než 250 databází pomocí geografické replikace můžou narazit na problémy, včetně delšího plánovaného převzetí služeb při selhání a sníženého výkonu.  Tyto problémy se budou pravděpodobněji vyskytnout pro úlohy náročné na zápis, když jsou koncové body geografické replikace široce oddělené geograficky nebo pokud se pro každou databázi používá více sekundárních koncových bodů.  Příznaky těchto problémů jsou uvedené v případě, že se prodleva geografické replikace v průběhu času zvyšuje.  Tato prodleva se dá monitorovat pomocí [Sys. dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Pokud dojde k těmto potížím, bude zmírnění rizik zahrnovat zvýšení počtu DTU fondů nebo snížení počtu geograficky replikovaných databází ve stejném fondu.

### <a name="changing-secondary-region-of-the-failover-group"></a>Změna sekundární oblasti skupiny převzetí služeb při selhání

K ilustraci sekvence změn se předpokládá, že server A je primárním serverem, server B je stávající sekundární server a Server C je nový sekundární ve třetí oblasti.  Pro přechod proveďte následující kroky:

1.  Pomocí [aktivní geografické replikace](sql-database-active-geo-replication.md)vytvořte další sekundární databáze pro každou databázi na serveru A na server C. Každá databáze na serveru A bude mít dvě sekundární, jednu na serveru B a jednu na server C. Tím se zajistí, že primární databáze zůstanou chráněné během přechodu.
2.  Odstraňte skupinu převzetí služeb při selhání. V tomto okamžiku se přihlášení nezdaří. Důvodem je, že se odstranily aliasy SQL pro naslouchací procesy skupiny převzetí služeb při selhání a brána nerozpozná název skupiny převzetí služeb při selhání.
3.  Znovu vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi servery A a C. V tomto okamžiku se přihlášení zastaví, pokud se nezdaří.
4.  Přidejte všechny primární databáze na serveru A do nové skupiny převzetí služeb při selhání.
5.  Vyřaďte Server B. Všechny databáze na B budou automaticky odstraněny. 


### <a name="changing-primary-region-of-the-failover-group"></a>Změna primární oblasti skupiny převzetí služeb při selhání

K ilustraci sekvence změn se předpokládá, že server A je primárním serverem, server B je stávající sekundární server a Server C je nová primární databáze ve třetí oblasti.  Pro přechod proveďte následující kroky:

1.  Proveďte plánované převzetí služeb při selhání pro přepnutí primárního serveru na B. Server A se stane novým sekundárním serverem. Převzetí služeb při selhání může vést k několika minutám výpadku. Skutečný čas bude záviset na velikosti skupiny převzetí služeb při selhání.
2.  Pomocí [aktivní geografické replikace](sql-database-active-geo-replication.md)vytvořte další sekundární databáze pro každou databázi na serveru B a serverem C. Každá databáze na serveru B bude mít dvě sekundární verze, jednu na serveru A a jednu na serveru C. Tím se zajistí, že primární databáze zůstanou chráněné během přechodu.
3.  Odstraňte skupinu převzetí služeb při selhání. V tomto okamžiku se přihlášení nezdaří. Důvodem je, že se odstranily aliasy SQL pro naslouchací procesy skupiny převzetí služeb při selhání a brána nerozpozná název skupiny převzetí služeb při selhání.
4.  Znovu vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi servery A a C. V tomto okamžiku se přihlášení zastaví, pokud se nezdaří.
5.  Přidejte všechny primární databáze na B do nové skupiny převzetí služeb při selhání. 
6.  Proveďte plánované převzetí služeb při selhání ve skupině převzetí služeb při selhání a přepněte B a C. Nyní se Server C stane primární a B-sekundární. Všechny sekundární databáze na serveru A budou automaticky propojeny se základními počítači v jazyce C. Stejně jako v kroku 1 může převzetí služeb při selhání způsobit několik minut výpadků.
6.  Vyřaďte Server A. Všechny databáze na straně budou automaticky odstraněny.

> [!IMPORTANT]
> Při odstranění skupiny převzetí služeb při selhání se odstraní také záznamy DNS pro koncové body naslouchacího procesu. V tomto okamžiku existuje nenulová pravděpodobnost někoho jiného, když se vytvoří skupina převzetí služeb při selhání nebo alias serveru se stejným názvem, což vám zabrání v jeho opětovném použití. K minimalizaci rizika nepoužívejte obecné názvy skupin pro převzetí služeb při selhání.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Osvědčené postupy použití skupin převzetí služeb při selhání se spravovanými instancemi

Skupina automatického převzetí služeb při selhání musí být nakonfigurovaná na primární instanci a bude připojena k sekundární instanci v jiné oblasti Azure.  Všechny databáze v instanci budou replikovány do sekundární instance.

Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace pomocí spravované instance a skupiny automatického převzetí služeb při selhání.

![automatické převzetí služeb při selhání](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Podrobný kurz přidávání spravované instance do [skupiny převzetí služeb při selhání najdete v tématu Přidání spravované instance](sql-database-managed-instance-failover-group-tutorial.md) pro použití skupiny převzetí služeb při selhání.

Pokud vaše aplikace používá spravovanou instanci jako datovou vrstvu, postupujte při navrhování provozní kontinuity podle těchto obecných pokynů:

### <a name="creating-the-secondary-instance"></a>Vytvoření sekundární instance 

Pro zajištění nepřerušeného připojení k primární instanci po převzetí služeb při selhání musí být primární i sekundární instance ve stejné zóně DNS. Zaručujeme, že stejný certifikát s více doménami (SAN) se dá použít k ověření připojení klientů ke kterékoli z těchto dvou instancí ve skupině převzetí služeb při selhání. Když je vaše aplikace připravená na produkční nasazení, vytvořte sekundární instanci v jiné oblasti a ujistěte se, že se zóna DNS sdílí s primární instancí. Můžete to provést zadáním `DNS Zone Partner` volitelného parametru pomocí Azure Portal, PowerShellu nebo REST API.

> [!IMPORTANT]
> První instance vytvořená v podsíti Určuje zónu DNS pro všechny následné instance ve stejné podsíti. To znamená, že dvě instance ze stejné podsítě nemohou patřit do různých zón DNS.

Další informace o vytvoření sekundární instance ve stejné zóně DNS jako primární instance najdete v tématu [vytvoření sekundární spravované instance](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

### <a name="enabling-replication-traffic-between-two-instances"></a>Povolení provozu replikace mezi dvěma instancemi

Vzhledem k tomu, že každá instance je izolovaná ve své vlastní virtuální síti, musí být povolen obousměrný provoz mezi těmito virtuální sítě. Viz [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Vytvoření skupiny převzetí služeb při selhání mezi spravovanými instancemi v různých předplatných

Skupinu převzetí služeb při selhání můžete vytvořit mezi spravovanými instancemi ve dvou různých předplatných. Pokud používáte rozhraní PowerShell API, můžete to udělat zadáním parametru `PartnerSubscriptionId` pro sekundární instanci. Při použití REST API může mít každé ID instance obsažené v parametru `properties.managedInstancePairs` vlastní subscriptionID.
  
> [!IMPORTANT]
> Azure Portal nepodporuje vytváření skupin převzetí služeb při selhání v různých předplatných. Pro existující skupiny převzetí služeb při selhání v různých předplatných nebo skupinách prostředků se převzetí služeb při selhání nedá iniciovat ručně prostřednictvím portálu z primární instance. Místo toho ji inicializujte z instance geografické sekundární instance.

### <a name="managing-failover-to-secondary-instance"></a>Správa převzetí služeb při selhání do sekundární instance

Skupina převzetí služeb při selhání bude spravovat převzetí služeb při selhání všech databází v instanci. Při vytvoření skupiny se všechny databáze v instanci automaticky geograficky replikují do sekundární instance. Skupiny převzetí služeb při selhání nelze použít k zahájení částečného převzetí služeb při selhání podmnožiny databází.

> [!IMPORTANT]
> Pokud je databáze odebrána z primární instance, bude také automaticky odstraněna na geografickou sekundární instanci.

### <a name="using-read-write-listener-for-oltp-workload"></a>Použití naslouchacího procesu pro čtení i zápis pro úlohu OLTP

Při provádění operací OLTP použijte jako adresu URL serveru `<fog-name>.zone_id.database.windows.net` a připojení se automaticky přesměrují na primární. Tato adresa URL se po převzetí služeb při selhání nemění. Převzetí služeb při selhání zahrnuje aktualizaci záznamu DNS, takže připojení klientů se přesměrují na nový primární až po aktualizaci mezipaměti DNS klienta. Vzhledem k tomu, že sekundární instance sdílí zónu DNS s primárním objektem, klientská aplikace se k ní bude moci znovu připojit pomocí stejného certifikátu sítě SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Připojení k sekundární instanci pomocí naslouchacího procesu jen pro čtení

Pokud máte logicky izolovanou úlohu jen pro čtení, která je odolná vůči určité zastaralosti dat, můžete v aplikaci použít sekundární databázi. Pokud se chcete připojit přímo k geograficky replikovanému sekundárnímu, použijte `server.secondary.zone_id.database.windows.net` jako adresu URL serveru a připojení se provede přímo na geograficky replikovanou sekundární hodnotu.

> [!NOTE]
> V některých úrovních služby Azure SQL Database podporuje použití [replik jen pro čtení](sql-database-read-scale-out.md) k vyrovnávání zatížení úloh dotazů jen pro čtení pomocí kapacity jedné repliky jen pro čtení a použitím parametru `ApplicationIntent=ReadOnly` v připojovacím řetězci. Když jste nakonfigurovali geograficky replikovanou sekundární položku, můžete tuto možnost použít k připojení k replice jen pro čtení v primárním umístění nebo v geograficky replikovaném umístění.
> - Pokud se chcete připojit k replice jen pro čtení v primárním umístění, použijte `<fog-name>.zone_id.database.windows.net`.
> - Pokud se chcete připojit k replice jen pro čtení v sekundárním umístění, použijte `<fog-name>.secondary.zone_id.database.windows.net`.

### <a name="preparing-for-performance-degradation"></a>Příprava na snížení výkonu

Typická aplikace Azure používá více služeb Azure a skládá se z několika součástí. Automatické převzetí služeb při selhání ve skupině převzetí služeb při selhání se aktivuje v závislosti na stavu samotných komponent Azure SQL. Jiné služby Azure v primární oblasti nemusí být ovlivněny výpadkem a jejich komponenty mohou být v této oblasti stále dostupné. Jakmile se primární databáze přepne do oblasti zotavení po havárii, může se zvýšit latence mezi závislými komponentami. Aby se zabránilo dopadu vyšší latence na výkon aplikace, zajistěte redundanci všech komponent aplikace v oblasti zotavení po havárii a postupujte podle [pokynů pro zabezpečení sítě](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Příprava na ztrátu dat

Pokud dojde k výpadku, SQL automaticky aktivuje převzetí služeb při selhání pro čtení a zápis, pokud dojde ke ztrátě dat na základě našeho vědomí. V opačném případě počká na období určené `GracePeriodWithDataLossHours`. Pokud jste zadali `GracePeriodWithDataLossHours`, připravte se na ztrátu dat. Obecně platí, že při výpadkech Azure upřednostňuje dostupnost. Pokud nemůžete zaručit ztrátu dat, nezapomeňte nastavit GracePeriodWithDataLossHours na dostatečně velké číslo, například 24 hodin.

Aktualizace DNS naslouchacího procesu pro čtení a zápis proběhne hned po zahájení převzetí služeb při selhání. Tato operace nebude mít za následek ztrátu dat. Proces přepínání databázových rolí však může za normálních podmínek trvat až 5 minut. Až do dokončení, budou některé databáze v nové primární instanci pořád jen pro čtení. Pokud se převzetí služeb při selhání iniciuje pomocí PowerShellu, bude celá operace synchronní. Pokud je inicializována pomocí Azure Portal, uživatelské rozhraní bude označovat stav dokončení. Pokud je iniciována pomocí REST API, použijte mechanismus dotazování standardní Azure Resource Manager ke sledování dokončení.

> [!IMPORTANT]
> Ruční převzetí služeb při selhání můžete použít k přesunu primárních souborů zpátky do původního umístění. Pokud dojde ke zmírnění výpadku, který způsobil převzetí služeb při selhání, můžete primární databáze přesunout do původního umístění. K tomu je potřeba zahájit ruční převzetí služeb při selhání skupiny.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Změna sekundární oblasti skupiny převzetí služeb při selhání

Předpokládejme, že instance A je primární instancí, instance B je stávající sekundární instance a instance C je nová sekundární instance ve třetí oblasti.  Pro přechod proveďte následující kroky:

1.  Vytvořte instanci C se stejnou velikostí jako a ve stejné zóně DNS. 
2.  Odstraní skupinu převzetí služeb při selhání mezi instancemi a a B. V tomto okamžiku se přihlašovací údaje nezdařily, protože aliasy SQL pro naslouchací procesy skupiny převzetí služeb při selhání byly odstraněny a brána nerozpozná název skupiny převzetí služeb při selhání. Sekundární databáze budou odpojeny od primárních databází a stanou se pro čtení a zápis databáze. 
3.  Vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi instancí A a C. postupujte podle pokynů v [kurzu skupina převzetí služeb při selhání pomocí spravované instance](sql-database-managed-instance-failover-group-tutorial.md). Jedná se o datovou operaci, která bude dokončena, pokud jsou všechny databáze z instance A synchronizovány.
4.  Odstraňte instanci B, pokud není potřeba, aby nedocházelo k zbytečným poplatkům.

> [!NOTE]
> Po kroku 2 a až do dokončení kroku 3 databáze v instanci A zůstanou nechráněné před závažným selháním instance A.

### <a name="changing-primary-region-of-the-failover-group"></a>Změna primární oblasti skupiny převzetí služeb při selhání

Řekněme, že instance A je primární instancí, instance B je stávající sekundární instance a instance C je nová primární instance ve třetí oblasti.  Pro přechod proveďte následující kroky:

1.  Vytvořte instanci C se stejnou velikostí jako B a ve stejné zóně DNS. 
2.  Připojení k instanci B a ruční převzetí služeb při selhání pro přepnutí primární instance na B. instance A se stane novou sekundární instancí automaticky.
3.  Odstraní skupinu převzetí služeb při selhání mezi instancemi a a B. V tomto okamžiku se přihlašovací údaje nezdařily, protože aliasy SQL pro naslouchací procesy skupiny převzetí služeb při selhání byly odstraněny a brána nerozpozná název skupiny převzetí služeb při selhání. Sekundární databáze budou odpojeny od primárních databází a stanou se pro čtení a zápis databáze. 
4.  Vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi instancí A a C. postupujte podle pokynů v [kurzu skupina převzetí služeb při selhání pomocí spravované instance](sql-database-managed-instance-failover-group-tutorial.md). Jedná se o datovou operaci, která bude dokončena, pokud jsou všechny databáze z instance A synchronizovány.
5.  Odstraňte instanci A, pokud není potřeba, aby nedocházelo k zbytečným poplatkům.

> [!NOTE] 
> Po kroku 3 a až do dokončení kroku 4 databáze v instanci A zůstanou nechráněné z závažného selhání instance A.

> [!IMPORTANT]
> Při odstranění skupiny převzetí služeb při selhání se odstraní také záznamy DNS pro koncové body naslouchacího procesu. V tomto okamžiku existuje nenulová pravděpodobnost někoho jiného, když se vytvoří skupina převzetí služeb při selhání nebo alias serveru se stejným názvem, což vám zabrání v jeho opětovném použití. K minimalizaci rizika nepoužívejte obecné názvy skupin pro převzetí služeb při selhání.

## <a name="failover-groups-and-network-security"></a>Skupiny převzetí služeb při selhání a zabezpečení sítě

U některých aplikací pravidla zabezpečení vyžadují, aby byl síťový přístup k datové vrstvě omezený na určitou součást nebo součásti, jako je například virtuální počítač, Webová služba atd. Tento požadavek představuje některé problémy s návrhem kontinuity podnikových aplikací a používání skupin převzetí služeb při selhání. Při implementaci takového omezeného přístupu Vezměte v úvahu následující možnosti.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Používání skupin převzetí služeb při selhání a pravidel virtuální sítě

Pokud používáte [Virtual Network koncových bodů a pravidel služby](sql-database-vnet-service-endpoint-rule-overview.md) pro omezení přístupu k vaší databázi SQL, pamatujte, že každý koncový bod služby Virtual Network se vztahuje jenom na jednu oblast Azure. Koncový bod nepovoluje, aby komunikace z podsítě přijímala jiné oblasti. Proto se k primární databázi mohou připojit pouze klientské aplikace nasazené ve stejné oblasti. Vzhledem k tomu, že dojde k převzetí služeb při selhání v relacích klienta SQL, které jsou přesměrovány na server v jiné (sekundární) oblasti, tyto relace selžou, pokud pocházejí z klienta mimo tuto oblast. Z tohoto důvodu není možné povolit zásady automatického převzetí služeb při selhání, pokud jsou zúčastněné servery zahrnuté v pravidlech Virtual Network. Pokud chcete podporovat ruční převzetí služeb při selhání, postupujte podle těchto kroků:

1. Zřízení redundantních kopií front-endové komponenty aplikace (webová služba, virtuální počítače atd.) v sekundární oblasti
2. Konfigurovat [pravidla virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) jednotlivě pro primární a sekundární server
3. Povolení [převzetí služeb při selhání front-endu pomocí konfigurace Traffic Manageru](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Iniciujte ruční převzetí služeb při selhání při zjištění výpadku. Tato možnost je optimalizovaná pro aplikace, které vyžadují konzistentní latenci mezi front-end a datovou vrstvou, a podporuje obnovení, pokud je vliv na front-end, datovou vrstvu nebo obojí.

> [!NOTE]
> Pokud k vyrovnávání zatížení úlohy jen pro čtení používáte **naslouchací proces jen pro čtení** , ujistěte se, že je tato úloha spuštěná na virtuálním počítači nebo jiném prostředku v sekundární oblasti, aby se mohla připojit k sekundární databázi.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Používání skupin převzetí služeb při selhání a pravidel brány firewall pro SQL Database

Pokud váš plán provozní kontinuity vyžaduje převzetí služeb při selhání pomocí skupin s automatickým převzetím služeb při selhání, můžete omezit přístup k databázi SQL pomocí tradičních pravidel brány firewall. K podpoře automatického převzetí služeb při selhání použijte následující postup:

1. [Vytvoření veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Vytvořte veřejný Nástroj pro vyrovnávání zatížení](../load-balancer/quickstart-load-balancer-standard-public-portal.md) a přiřaďte k němu veřejnou IP adresu.
3. [Vytvoření virtuální sítě a virtuálních počítačů](../load-balancer/quickstart-load-balancer-standard-public-portal.md) pro součásti front-endu
4. [Vytvořte skupinu zabezpečení sítě](../virtual-network/security-overview.md) a nakonfigurujte příchozí připojení.
5. Zajistěte, aby se odchozí připojení otevírala ve službě Azure SQL Database pomocí [značky služby](../virtual-network/security-overview.md#service-tags)SQL.
6. Vytvořte [pravidlo brány firewall služby SQL Database](sql-database-firewall-configure.md) , které povolí příchozí provoz z veřejné IP adresy, kterou jste vytvořili v kroku 1.

Další informace o tom, jak nakonfigurovat odchozí přístup a jaká IP adresa se má použít v pravidlech brány firewall, najdete v tématu [odchozí připojení nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-outbound-connections.md).

Výše uvedená konfigurace zajistí, že automatické převzetí služeb při selhání nebude blokovat připojení z front-endové komponenty a předpokládá, že aplikace dokáže tolerovat delší latenci mezi front-end a datovou vrstvou.

> [!IMPORTANT]
> Pro zajištění kontinuity podnikových důvodů pro regionální výpadky musíte zajistit geografickou redundanci pro front-end komponenty i databáze.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Povolení geografické replikace mezi spravovanými instancemi a jejich virtuální sítě

Při nastavování skupiny převzetí služeb při selhání mezi primárními a sekundárními spravovanými instancemi ve dvou různých oblastech se každá instance izoluje pomocí nezávislé virtuální sítě. Pokud chcete zajistit, aby provoz replikace mezi těmito virtuální sítěmi splňoval tyto požadavky:

1. Tyto dvě spravované instance se musí nacházet v různých oblastech Azure.
2. Tyto dvě spravované instance musí být stejné úrovně služby a měly by mít stejnou velikost úložiště.
3. Vaše sekundární spravovaná instance musí být prázdná (žádné uživatelské databáze).
4. Virtuální sítě používané spravovanými instancemi musí být připojené pomocí [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [Express Route](../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Pokud se dvě virtuální sítě připojují prostřednictvím místní sítě, ujistěte se, že neexistuje žádné pravidlo brány firewall blokující porty 5022 a 11000-11999. Globální partnerský vztah virtuálních sítí se nepodporuje.
5. U dvou spravovaných instancí virtuální sítě nejde překrývání IP adres.
6. Musíte nastavit skupiny zabezpečení sítě (NSG) tak, aby porty 5022 a rozsah 11000 ~ 12000 byly otevřené příchozí a odchozí pro připojení z podsítě jiné spravované instance. To umožňuje provoz replikace mezi instancemi.

   > [!IMPORTANT]
   > Nesprávně nakonfigurovaná pravidla zabezpečení NSG vede k zablokování operací kopírování databáze.

7. Sekundární instance je nakonfigurovaná se správným ID zóny DNS. Zóna DNS je vlastnost spravované instance a virtuálního clusteru a její ID je obsaženo v adrese názvu hostitele. ID zóny se generuje jako náhodný řetězec, když se v každé virtuální síti vytvoří první spravovaná instance a stejné ID se přiřadí ke všem ostatním instancím ve stejné podsíti. Po přiřazení se zóna DNS nedá změnit. Spravované instance zahrnuté do stejné skupiny převzetí služeb při selhání musí sdílet zónu DNS. Toho dosáhnete tak, že při vytváření sekundární instance předáte ID zóny primární instance jako hodnotu parametru DnsZonePartner. 

   > [!NOTE]
   > Podrobný kurz týkající se konfigurace skupin převzetí služeb při selhání pomocí spravované instance najdete v tématu [Přidání spravované instance do skupiny převzetí služeb při selhání](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Upgrade nebo downgrade primární databáze

Primární databázi můžete upgradovat nebo downgradovat na jinou výpočetní velikost (v rámci stejné úrovně služby, ne mezi Pro obecné účely a Pro důležité obchodní informace), aniž byste museli odpojit sekundární databáze. Při upgradu doporučujeme nejdřív upgradovat všechny sekundární databáze a potom upgradovat primární. Když se downgrade, obrátí se pořadí: nejprve nastavte downgrade primární databáze a pak vytvořte downgrade všech sekundárních databází. Když provedete upgrade nebo downgrade databáze na jinou úroveň služby, toto doporučení se vynutilo.

Tato sekvence se doporučuje výslovně vyhnout problému, při kterém se sekundární položka u menší skladové položky (SKU) přetěžuje a že se musí znovu naplnit během upgradu nebo procesu downgrade. Můžete se taky vyhnout problému tím, že nastavíte primární jen pro čtení, na úkor všech úloh, které mají vliv na čtení a zápis na primární úrovni.

> [!NOTE]
> Pokud jste sekundární databázi vytvořili jako součást konfigurace skupiny převzetí služeb při selhání, nedoporučuje se ji převést na downgrade sekundární databáze. Tím zajistíte, že vaše datová úroveň má dostatečnou kapacitu pro zpracování pravidelného zatížení po aktivaci převzetí služeb při selhání.

## <a name="preventing-the-loss-of-critical-data"></a>Zabránění ztrátě důležitých dat

V důsledku vysoké latence sítí WAN používá průběžné kopírování mechanismus asynchronní replikace. Asynchronní replikace způsobuje nenevyhnutelnou ztrátu dat, pokud dojde k selhání. Některé aplikace ale nemusí vyžadovat žádnou ztrátu dat. Pro ochranu těchto důležitých aktualizací může vývojář aplikace volat [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systémové procedury ihned po potvrzení transakce. Volání `sp_wait_for_database_copy_sync` blokuje volající vlákno, dokud se poslední potvrzená transakce nepřenesla do sekundární databáze. Nečeká ale na přenesení transakcí a jejich potvrzení na sekundárním počítači. `sp_wait_for_database_copy_sync` má obor na konkrétní odkaz průběžné kopírování. Tento postup může volat každý uživatel s právy pro připojení k primární databázi.

> [!NOTE]
> `sp_wait_for_database_copy_sync` zabrání ztrátě dat po převzetí služeb při selhání, ale nezaručuje úplnou synchronizaci pro přístup pro čtení. Zpoždění způsobené voláním procedury `sp_wait_for_database_copy_sync` může být významné a závisí na velikosti transakčního protokolu v době volání.

## <a name="failover-groups-and-point-in-time-restore"></a>Skupiny převzetí služeb při selhání a obnovení k bodu v čase

Informace o použití obnovení k časovému okamžiku se skupinami převzetí služeb při selhání najdete v tématu [Obnovení bodu v čase (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Omezení skupin převzetí služeb při selhání

Mějte na paměti následující omezení:

- Skupinu převzetí služeb při selhání nelze vytvořit mezi dvěma servery nebo instancemi ve stejných oblastech Azure.
- Skupinu převzetí služeb při selhání nelze přejmenovat. Budete muset skupinu odstranit a znovu ji vytvořit s jiným názvem. 
- Přejmenování databáze není u instancí ve skupině převzetí služeb při selhání podporováno. Aby bylo možné databázi přejmenovat, budete muset dočasně odstranit skupinu převzetí služeb při selhání.

## <a name="programmatically-managing-failover-groups"></a>Programová správa skupin převzetí služeb při selhání

Jak už bylo popsáno dříve, skupiny automatického převzetí služeb při selhání a aktivní geografická replikace je také možné spravovat programově pomocí Azure PowerShell a REST API. V následujících tabulkách jsou popsány sady příkazů, které jsou k dispozici. Aktivní geografická replikace obsahuje sadu Azure Resource Manager rozhraní API pro správu, včetně rutin [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) a [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Tato rozhraní API vyžadují použití skupin prostředků a podporují zabezpečení na základě rolí (RBAC). Další informace o tom, jak implementovat role přístupu, najdete v tématu [Access Control na základě rolí v Azure](../role-based-access-control/overview.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Správa převzetí služeb při selhání SQL Database s izolovanými databázemi a elastickými fondy

| Rutina | Popis |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji na primární i sekundární servery.|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Odebere skupinu převzetí služeb při selhání ze serveru. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Načte konfiguraci skupiny převzetí služeb při selhání. |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Upraví konfiguraci skupiny převzetí služeb při selhání. |
| [Switch – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Přidá jednu nebo více databází do skupiny převzetí služeb při selhání.|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Správa skupin převzetí služeb při selhání SQL Database se spravovanými instancemi

| Rutina | Popis |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji na primární i sekundární instanci.|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Upraví konfiguraci skupiny převzetí služeb při selhání.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Načte konfiguraci skupiny převzetí služeb při selhání.|
| [Switch – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání do sekundární instance.|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Odebere skupinu převzetí služeb při selhání.|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Správa převzetí služeb při selhání SQL Database s izolovanými databázemi a elastickými fondy

| Příkaz | Popis |
| --- | --- |
| [AZ SQL Failover-Group Create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji na primární i sekundární servery.|
| [AZ SQL Failover-Group DELETE](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Odebere skupinu převzetí služeb při selhání ze serveru. |
| [AZ SQL Failover-Group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Načte konfiguraci skupiny převzetí služeb při selhání. |
| [AZ SQL Failover-Group Update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Upraví konfiguraci skupiny převzetí služeb při selhání nebo přidá jednu nebo více databází do skupiny převzetí služeb při selhání.|
| [AZ SQL Failover-Group set-Primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server. |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Správa skupin převzetí služeb při selhání SQL Database se spravovanými instancemi

| Příkaz | Popis |
| --- | --- |
| [AZ SQL instance-Failover-Group Create](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji na primární i sekundární instanci. |
| [AZ SQL instance-převzetí služeb při selhání – aktualizace skupiny](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Upraví konfiguraci skupiny převzetí služeb při selhání.|
| [AZ SQL instance-Failover-Group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Načte konfiguraci skupiny převzetí služeb při selhání.|
| [AZ SQL instance-převzetí služeb při selhání-sada skupin – primární](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání do sekundární instance.|
| [AZ SQL instance-Failover-Group DELETE](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Odebere skupinu převzetí služeb při selhání. |

* * *

> [!IMPORTANT]
> Vzorový skript najdete v tématu [Konfigurace a převzetí služeb při selhání pro skupinu převzetí služeb při selhání pro jednu databázi](scripts/sql-database-add-single-db-to-failover-group-powershell.md).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Správa skupin převzetí služeb při selhání databáze SQL pomocí jedné a sdružené databáze

| Rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Vytvoří nebo aktualizuje skupinu převzetí služeb při selhání. |
| [Odstranit skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Odebere skupinu převzetí služeb při selhání ze serveru. |
| [Převzetí služeb při selhání (plánováno)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Aktivuje převzetí služeb při selhání z aktuálního primárního serveru na sekundární server s plnou synchronizací dat.|
| [Vynucené převzetí služeb při selhání umožňuje ztrátu dat](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Aktivuje převzetí služeb při selhání z aktuálního primárního serveru na sekundární server bez synchronizace dat. Tato operace může způsobit ztrátu dat. |
| [Získat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Načte konfiguraci skupiny převzetí služeb při selhání. |
| [Vypsat skupiny převzetí služeb při selhání podle serveru](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Vypíše skupiny převzetí služeb při selhání na serveru. |
| [Aktualizace skupiny převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aktualizuje konfiguraci skupiny převzetí služeb při selhání. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: Správa skupin převzetí služeb při selhání se spravovanými instancemi

| Rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Vytvoří nebo aktualizuje konfiguraci skupiny převzetí služeb při selhání. |
| [Odstranit skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Odebere skupinu převzetí služeb při selhání z instance. |
| [Převzetí služeb při selhání (plánováno)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Aktivuje převzetí služeb při selhání z aktuální primární instance do této instance s úplnou synchronizací dat. |
| [Vynucené převzetí služeb při selhání umožňuje ztrátu dat](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Aktivuje převzetí služeb při selhání z aktuální primární instance do sekundární instance bez synchronizace dat. Tato operace může způsobit ztrátu dat. |
| [Získat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Načte konfiguraci skupiny převzetí služeb při selhání. |
| [Seznam skupin převzetí služeb při selhání – seznam podle umístění](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Vypíše skupiny převzetí služeb při selhání v umístění. |

## <a name="next-steps"></a>Další kroky

- Podrobné pokyny najdete v tématu.
    - [Přidání jedné databáze do skupiny převzetí služeb při selhání](sql-database-single-database-failover-group-tutorial.md)
    - [Přidat elastický fond do skupiny převzetí služeb při selhání](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Přidání spravované instance do skupiny převzetí služeb při selhání](sql-database-managed-instance-failover-group-tutorial.md)
- Ukázkové skripty najdete v těchto tématech:
  - [Konfigurace aktivní geografické replikace pro izolovanou databázi v Azure SQL Database pomocí PowerShellu](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Použití PowerShellu ke konfiguraci aktivní geografické replikace pro databázi ve fondu v Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Přidání jedné databáze Azure SQL Database do skupiny převzetí služeb při selhání pomocí PowerShellu](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Přehled provozní kontinuity a scénářů najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md) .
- Další informace o Azure SQL Database automatizovaných zálohách najdete v tématu [SQL Database automatizované zálohy](sql-database-automated-backups.md).
- Další informace o použití automatizovaných záloh pro obnovení najdete v tématu [obnovení databáze ze zálohy iniciované službou](sql-database-recovery-using-backups.md).
- Další informace o požadavcích na ověřování nového primárního serveru a databáze najdete v článku [SQL Database zabezpečení po zotavení po havárii](sql-database-geo-replication-security-config.md).
