---
title: Skupiny převzetí služeb při selhání
description: Skupiny s automatickým převzetím služeb při selhání je funkce databáze SQL, která umožňuje spravovat replikaci a automatické / koordinované převzetí služeb při selhání skupiny databází na serveru SQL Database nebo všech databází ve spravované instanci.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269832"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Použití skupin s automatickým převzetím služeb při selhání k povolení transparentního a koordinovaného převzetí služeb při selhání více databází

Skupiny s automatickým převzetím služeb při selhání je funkce databáze SQL, která umožňuje spravovat replikaci a převzetí služeb při selhání skupiny databází na serveru SQL Database nebo všech databází ve spravované instanci do jiné oblasti. Jedná se o deklarativní abstrakci nad existující aktivní funkcí [geografické replikace,](sql-database-active-geo-replication.md) která má zjednodušit nasazení a správu geograficky replikovaných databází ve velkém měřítku. Převzetí služeb při selhání můžete zahájit ručně nebo jej můžete delegovat na službu SQL Database na základě uživatelem definované zásady. Druhá možnost umožňuje automaticky obnovit více souvisejících databází v sekundární oblasti po katastrofické selhání nebo jiné neplánované události, která má za následek úplnou nebo částečnou ztrátu dostupnosti služby SQL Database v primární oblasti. Skupina převzetí služeb při selhání může obsahovat jednu nebo více databází, obvykle používaných stejnou aplikací. Kromě toho můžete použít čitelné sekundární databáze k vyložení úloh dotazů jen pro čtení. Vzhledem k tomu, že skupiny automatického převzetí služeb při selhání zahrnují více databází, musí být tyto databáze nakonfigurovány na primárním serveru. Skupiny s automatickým převzetím služeb při selhání podporují replikaci všech databází ve skupině pouze na jeden sekundární server v jiné oblasti.

> [!NOTE]
> Při práci s jednu nebo sdružené databáze na serveru sql databáze a chcete více sekundárních ve stejné nebo různé oblasti, použijte [aktivní geografickou replikaci](sql-database-active-geo-replication.md). 

Pokud používáte skupiny automatického převzetí služeb při selhání s automatickými zásadami převzetí služeb při selhání, jakýkoli výpadek, který má vliv na jednu nebo několik databází ve skupině, má za následek automatické převzetí služeb při selhání. Obvykle se jedná o incidenty, které nelze zmírnit vlastní mise předdefinované automatické operace s vysokou dostupností. Příklady aktivačních událostí převzetí služeb při selhání zahrnují incident způsobený kroužkem klienta SQL nebo řídicím kroužkem, který je vypnutý z důvodu nevracení paměti jádra operačního systému na několika výpočetních uzlech, nebo incident způsobený jedním nebo více kroužky klienta, protože během něj byl přerušen nesprávný síťový kabel, protože během běžné vyřazování hardwaru z provozu.  Další informace naleznete v [tématu SQL Database High Availability](sql-database-high-availability.md).

Kromě toho skupiny automaticképřevzetí služeb při selhání poskytují koncové body posluchače jen pro čtení a jen pro čtení, které zůstávají nezměněny během převzetí služeb při selhání. Bez ohledu na to, zda používáte ruční nebo automatickou aktivaci převzetí mne, převzetí služeb při selhání přepne všechny sekundární databáze ve skupině na primární. Po dokončení převzetí služeb při selhání databáze se záznam DNS automaticky aktualizuje, aby přesměroval koncové body do nové oblasti. Konkrétní data RPO a RTO naleznete [v tématu Přehled kontinuity provozu](sql-database-business-continuity.md).

Pokud používáte skupiny automatického převzetí služeb při selhání s automatickou zásadou převzetí služeb při selhání, jakýkoli výpadek, který má vliv na databáze na serveru SQL Database nebo spravované instanci, má za následek automatické převzetí služeb při selhání. Skupinu automatického převzetí služeb při selhání můžete spravovat pomocí:

- [Portál Azure](sql-database-implement-geo-distributed-database.md)
- [Cli Azure: Skupina pro převzetí služeb při selhání](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [Prostředí PowerShell: Skupina převzetí služeb při selhání](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [ROZHRANÍ REST API: Skupina převzetí služeb při selhání](/rest/api/sql/failovergroups).

Po převzetí služeb při selhání zajistěte, aby byly požadavky na ověřování serveru a databáze nakonfigurovány na nové primární. Podrobnosti naleznete v [tématu sql database security after disaster recovery](sql-database-geo-replication-security-config.md).

Přidání redundance databáze mezi datová centra je pouze součástí řešení, aby bylo dosaženo skutečné kontinuity podnikání. Obnovení aplikace (služby) end-to-end po selhání katastrofální vyžaduje obnovení všech součástí, které tvoří službu a všechny závislé služby. Příklady těchto součástí zahrnují klientský software (například prohlížeč s vlastním JavaScriptem), webové front-endy, úložiště a DNS. Je důležité, aby všechny součásti jsou odolné vůči stejným selháním a budou k dispozici v rámci cíle doby obnovení (RTO) vaší aplikace. Proto je třeba identifikovat všechny závislé služby a pochopit záruky a možnosti, které poskytují. Potom je nutné provést odpovídající kroky k zajištění, že vaše služba funguje během převzetí služeb převzetí služeb, na kterých závisí. Další informace o navrhování řešení pro zotavení po havárii naleznete v [tématu Návrh cloudových řešení pro zotavení po havárii pomocí aktivní geografické replikace](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Terminologie a možnosti skupiny s automatickým převzetím služeb při selhání

- **Skupina převzetí služeb při selhání (FOG)**

  Skupina převzetí služeb při selhání je pojmenovaná skupina databází spravovaných jedním databázovým serverem SQL nebo v rámci jedné spravované instance, která může přepojit jako jednotku jako jednotku do jiné oblasti v případě, že všechny nebo některé primární databáze nebudou k dispozici z důvodu výpadku v primární oblasti. Při vytvoření pro spravované instance obsahuje skupina převzetí služeb při selhání všechny uživatelské databáze v instanci, a proto lze v instanci nakonfigurovat pouze jednu skupinu převzetí služeb při selhání.
  
  > [!IMPORTANT]
  > Název skupiny převzetí služeb při selhání musí `.database.windows.net` být v doméně globálně jedinečný.

- **Servery služby SQL Database**

     S sql database servery některé nebo všechny uživatelské databáze na jednom serveru DATABÁZE SQL lze umístit do skupiny převzetí služeb při selhání. Databázový server SQL také podporuje více skupin převzetí služeb při selhání na jednom databázovém serveru SQL.

- **Primární**

  Databázový server SQL nebo spravovaná instance, která hostuje primární databáze ve skupině s podporou převzetí služeb při selhání.

- **Sekundární**

  Databázový server SQL nebo spravovaná instance, která hostuje sekundární databáze ve skupině s podporou převzetí služeb při selhání. Sekundární nemůže být ve stejné oblasti jako primární.

- **Přidání jednotlivých databází do skupiny převzetí služeb při selhání**

  Do stejné skupiny převzetí služeb při selhání můžete umístit několik jednotlivých databází na stejný databázový server SQL. Pokud přidáte jednu databázi do skupiny převzetí služeb při selhání, automaticky vytvoří sekundární databázi pomocí stejné edice a výpočetní velikosti na sekundárním serveru.  Tento server jste zadali při vytvoření skupiny převzetí služeb při selhání. Pokud přidáte databázi, která již má sekundární databázi na sekundárním serveru, je toto propojení geografické replikace zděděno skupinou. Přidáte-li databázi, která již má sekundární databázi na serveru, který není součástí skupiny převzetí služeb při selhání, vytvoří se na sekundárním serveru nový sekundární objekt.

  > [!IMPORTANT]
  > Ujistěte se, že sekundární server nemá databázi se stejným názvem, pokud se nejedná o existující sekundární databázi. Ve skupinách s podporou převzetí služeb při selhání pro spravovanou instanci jsou replikovány všechny uživatelské databáze. Ve skupině s podporou převzetí služeb při selhání nelze vybrat podmnožinu uživatelských databází pro replikaci.

- **Přidání databází do elastického fondu do skupiny převzetí služeb při selhání**

  Všechny nebo několik databází můžete umístit do elastického fondu do stejné skupiny převzetí služeb při selhání. Pokud je primární databáze v elastickém fondu, sekundární je automaticky vytvořen v elastickém fondu se stejným názvem (sekundární fond). Je nutné zajistit, aby sekundární server obsahoval elastický fond se stejným přesným názvem a dostatečnou volnou kapacitou pro hostování sekundárních databází, které budou vytvořeny skupinou převzetí služeb při selhání. Pokud přidáte databázi ve fondu, který již má sekundární databázi v sekundárním fondu, toto propojení geografické replikace je zděděno skupinou. Přidáte-li databázi, která již má sekundární databázi na serveru, který není součástí skupiny převzetí služeb při selhání, vytvoří se v sekundárním fondu nový sekundární objekt.
  
- **Počáteční výsev** 

  Při přidávání databází, elastických fondů nebo spravovaných instancí do skupiny převzetí služeb při selhání existuje počáteční fáze nastavení před zahájením replikace dat. Počáteční fáze výsevu je nejdelší a nejdražší operace. Po dokončení počátečního osevačného se data synchronizují a replikují se pouze následné změny dat. Čas potřebný k dokončení počátečního osiva závisí na velikosti dat, počtu replikovaných databází a rychlosti propojení mezi entitami ve skupině s podporou převzetí služeb při selhání. Za normálních okolností je typická rychlost osiva 50–500 GB za hodinu pro jednu databázi nebo elastický fond a 18–35 GB za hodinu pro spravovanou instanci. Výsev se provádí pro všechny databáze paralelně. Uvedenou rychlost osývání můžete použít spolu s počtem databází a celkovou velikostí dat k odhadu, jak dlouho bude počáteční fáze osiva trvat, než začne replikace dat.

  U spravovaných instancí je třeba při odhadování času počáteční fáze výsevu vzít v úvahu také rychlost spojení expresní trasy mezi těmito dvěma instancemi. Je-li rychlost spojení mezi těmito dvěma instancemi pomalejší než to, co je nezbytné, je pravděpodobně výrazně ovlivněna doba osiva. Uvedenou rychlost osývání, počet databází, celkovou velikost dat a rychlost propojení můžete použít k odhadu, jak dlouho bude počáteční fáze osevací fáze trvat, než začne replikace dat. Například pro jednu databázi 100 GB počáteční fáze by trvalo kdekoli od 2.8 do 5.5 hodiny, pokud je propojení schopné tlačit 35 GB za hodinu. Pokud odkaz může přenášet pouze 10 GB za hodinu, pak seeding 100 GB databáze bude trvat asi 10 hodin. Pokud je k replikaci více databází, bude se spouštění prováděno paralelně a v kombinaci s pomalou rychlostí připojení může počáteční fáze výsevu trvat podstatně déle, zejména pokud paralelní výsev dat ze všech databází překročí dostupné šířku pásma propojení. Pokud je šířka pásma sítě mezi dvěma instancemi omezená a přidáváte více spravovaných instancí do skupiny převzetí služeb při selhání, zvažte přidání více spravovaných instancí do skupiny převzetí služeb při selhání postupně, jeden po druhém.

  
- **Zóna DNS**

  Jedinečné ID, které se automaticky vygeneruje při vytvoření nové instance. Pro ověření připojení klientů k libovolné instanci ve stejné zóně DNS je zřízen certifikát s více doménami (SAN). Dvě spravované instance ve stejné skupině převzetí služeb při selhání musí sdílet zónu DNS.
  
  > [!NOTE]
  > ID zóny DNS není vyžadováno pro skupiny převzetí služeb při selhání vytvořené pro servery databáze SQL.

- **Naslouchací proces čtení a zápisu skupiny převzetí služeb při selhání**

  Záznam CNAME DNS, který odkazuje na adresu URL aktuální primární. Je vytvořen automaticky při vytvoření skupiny převzetí služeb při selhání a umožňuje úlohy SQL čtení a zápisu transparentně znovu připojit k primární databázi při primární změny po převzetí služeb při selhání. Při vytvoření skupiny převzetí služeb při selhání na serveru SQL Database se `<fog-name>.database.windows.net`vytvoří záznam Dns CNAME pro adresu URL posluchače jako . Při vytvoření skupiny převzetí služeb při selhání ve spravované instanci se `<fog-name>.zone_id.database.windows.net`vytvoří záznam CNAME DNS pro adresu URL posluchače jako .

- **Naslouchací proces skupiny převzetí služeb při selhání**

  Vytvořen záznam CNAME DNS, který odkazuje na naslouchací proces jen pro čtení, který odkazuje na sekundární adresu URL. Je vytvořen automaticky při vytvoření skupiny převzetí služeb při selhání a umožňuje úlohy SQL jen pro čtení transparentně připojit k sekundární pomocí zadaných pravidel vyrovnávání zatížení. Při vytvoření skupiny převzetí služeb při selhání na serveru SQL Database se `<fog-name>.secondary.database.windows.net`vytvoří záznam Dns CNAME pro adresu URL posluchače jako . Při vytvoření skupiny převzetí služeb při selhání ve spravované instanci se `<fog-name>.zone_id.secondary.database.windows.net`vytvoří záznam CNAME DNS pro adresu URL posluchače jako .

- **Zásady automatického převzetí služeb při selhání**

  Ve výchozím nastavení je skupina převzetí služeb při selhání nakonfigurována pomocí zásad automatického převzetí služeb při selhání. Služba SQL Database aktivuje převzetí služeb při selhání po zjištění selhání a vypršení období odkladu. Systém musí ověřit, že výpadek nelze zmírnit vestavěnou [infrastrukturou vysoké dostupnosti služby SQL Database](sql-database-high-availability.md) z důvodu rozsahu dopadu. Pokud chcete řídit pracovní postup převzetí služeb při selhání z aplikace, můžete vypnout automatické převzetí služeb při selhání.
  
  > [!NOTE]
  > Vzhledem k tomu, že ověření rozsahu výpadku a toho, jak rychle může být zmírněno, zahrnuje lidské akce operačního týmu, nelze dobu odkladu nastavit pod jednu hodinu.  Toto omezení platí pro všechny databáze ve skupině převzetí služeb při selhání bez ohledu na jejich stav synchronizace dat. 

- **Zásady převzetí služeb při selhání jen pro čtení**

  Ve výchozím nastavení je zakázáno převzetí služeb při selhání naslouchací proces jen pro čtení. Zajišťuje, že výkon primární není ovlivněna při sekundární je offline. To však také znamená, že relace jen pro čtení nebude moci připojit, dokud sekundární je obnovena. Pokud nemůžete tolerovat prostoje pro relace jen pro čtení a jsou v pořádku dočasně použít primární pro čtení a čtení a zápis provozu na úkor potenciální snížení `AllowReadOnlyFailoverToPrimary` výkonu primární, můžete povolit převzetí služeb při selhání pro naslouchací proces jen pro čtení konfigurací vlastnosti. V takovém případě bude provoz jen pro čtení automaticky přesměrován na primární, pokud sekundární není k dispozici.

- **Plánované převzetí služeb při selhání**

   Plánované převzetí služeb při selhání provádí úplnou synchronizaci mezi primární a sekundární databází před sekundární přepne na primární roli. To zaručuje žádnou ztrátu dat. Plánované převzetí služeb při selhání se používá v následujících scénářích:

  - Proveďte cvičení zotavení po havárii (DR) ve výrobě, pokud ztráta dat není přijatelná
  - Přemístění databází do jiné oblasti
  - Vrátit databáze do primární oblasti po výpadku byla zmírněna (navrácení služeb po obnovení).

- **Neplánované převzetí služeb při selhání**

   Neplánované nebo vynucené převzetí služeb při selhání okamžitě přepne sekundární primární roli bez synchronizace s primární. Tato operace bude mít za následek ztrátu dat. Neplánované převzetí služeb při selhání se používá jako metoda obnovení během výpadků, když primární není přístupný. Když je původní primární je zpět do režimu online, automaticky se znovu připojit bez synchronizace a stát se novým sekundární.

- **Ruční převzetí služeb při selhání**

  Převzetí služeb při selhání můžete kdykoli zahájit ručně bez ohledu na automatickou konfiguraci převzetí služeb při selhání. Pokud není nakonfigurována automatická zásada převzetí služeb při selhání, je ruční převzetí služeb při selhání nutné k obnovení databází ve skupině převzetí služeb při selhání na sekundární. Můžete zahájit vynucené nebo přátelské převzetí služeb při selhání (s úplnou synchronizací dat). Ten by mohl být použit k přemístění primární do sekundární oblasti. Po dokončení převzetí služeb při selhání jsou záznamy DNS automaticky aktualizovány, aby bylo zajištěno připojení k novému primárnímu

- **Období odkladu se ztrátou dat**

  Vzhledem k tomu, že primární a sekundární databáze jsou synchronizovány pomocí asynchronní replikace, může selhání způsobit ztrátu dat. Můžete přizpůsobit zásady automatického převzetí služeb při selhání tak, aby odrážely toleranci aplikace ke ztrátě dat. Konfigurací `GracePeriodWithDataLossHours`můžete určit, jak dlouho systém čeká před zahájením převzetí služeb při selhání, které pravděpodobně povede ke ztrátě dat.

- **Více skupin převzetí služeb při selhání**

  Můžete nakonfigurovat více skupin převzetí služeb při selhání pro stejnou dvojici serverů pro řízení rozsahu převzetí služeb při selhání. Každá skupina převezme služby při selhání nezávisle. Pokud vaše víceklientská aplikace používá elastické fondy, můžete tuto možnost použít ke kombinaci primárních a sekundárních databází v každém fondu. Tímto způsobem můžete snížit dopad výpadku pouze na polovinu nájemníků.

  > [!NOTE]
  > Spravovaná instance nepodporuje více skupin převzetí služeb při selhání.
  
## <a name="permissions"></a>Oprávnění

Oprávnění pro skupinu převzetí služeb při selhání jsou spravována pomocí [řízení přístupu na základě rolí (RBAC).](../role-based-access-control/overview.md) Role [přispěvatele serveru SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) má všechna potřebná oprávnění ke správě skupin s podporou převzetí služeb při selhání.

### <a name="create-failover-group"></a>Vytvořit skupinu převzetí služeb při selhání

Chcete-li vytvořit skupinu s podporou převzetí služeb při selhání, potřebujete přístup pro zápis RBAC na primární i sekundární servery a ke všem databázím ve skupině s podporou převzetí služeb při selhání. Pro spravovanou instanci potřebujete přístup pro zápis RBAC k primární i sekundární spravované instanci, ale oprávnění pro jednotlivé databáze nejsou relevantní, protože jednotlivé databáze spravovaných instancí nelze přidat nebo odebrat ze skupiny převzetí služeb při selhání. 

### <a name="update-a-failover-group"></a>Aktualizace skupiny převzetí služeb při selhání

Chcete-li aktualizovat skupinu s podporou převzetí služeb při selhání, potřebujete přístup pro zápis RBAC do skupiny převzetí služeb při selhání a všechny databáze na aktuálním primárním serveru nebo spravované instanci.  

### <a name="failover-a-failover-group"></a>Převzetí služeb při selhání skupiny převzetí služeb při selhání

Chcete-li přepojit na převzetí služeb při selhání skupiny, budete potřebovat RBAC přístup pro zápis do skupiny převzetí služeb při selhání na novém primárním serveru nebo spravované instanci.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Doporučené postupy používání skupin s podporou převzetí služeb při selhání s jednotlivými databázemi a elastické fondy

Skupina automatického převzetí služeb při selhání musí být nakonfigurována na primárním databázovém serveru SQL a připojí ji k sekundárnímu serveru SQL Database v jiné oblasti Azure. Skupiny mohou obsahovat všechny nebo některé databáze na těchto serverech. Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace pomocí více databází a skupiny automatického převzetí služeb při selhání.

![automatické převzetí služeb při selhání](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> V tématu [Přidání jedné databáze do skupiny převzetí služeb při selhání](sql-database-single-database-failover-group-tutorial.md) pro podrobný kurz krok za krokem přidání jedné databáze do skupiny převzetí služeb při selhání.

Při navrhování služby s ohledem na kontinuitu provozu postupujte podle těchto obecných pokynů:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Použití jedné nebo několika skupin převzetí služeb při selhání ke správě převzetí služeb při selhání více databází

Jednu nebo více skupin převzetí služeb při selhání lze vytvořit mezi dvěma servery v různých oblastech (primární a sekundární servery). Každá skupina může obsahovat jednu nebo více databází, které jsou obnoveny jako celek v případě, že všechny nebo některé primární databáze nebudou k dispozici z důvodu výpadku v primární oblasti. Skupina převzetí služeb při selhání vytvoří geografickou sekundární databázi se stejným cílem služby jako primární. Pokud přidáte existující vztah geografické replikace do skupiny převzetí služeb při selhání, ujistěte se, že geograficky sekundární je nakonfigurován se stejnou úrovní služby a výpočetní velikost jako primární.
  
> [!IMPORTANT]
> Vytváření skupin převzetí služeb při selhání mezi dvěma servery v různých předplatných není aktuálně podporováno pro jednotlivé databáze a elastické fondy. Pokud po vytvoření skupiny převzetí služeb při selhání přesunete primární nebo sekundární server na jiné předplatné, může to mít za následek selhání požadavků na převzetí služeb při selhání a dalších operací.

### <a name="using-read-write-listener-for-oltp-workload"></a>Použití naslouchací proces čtení a zápisu pro úlohu OLTP

Při provádění operací OLTP `<fog-name>.database.windows.net` použijte jako adresu URL serveru a připojení jsou automaticky přesměrována na primární. Tato adresa URL se po převzetí služeb při selhání nezmění. Všimněte si, že převzetí služeb při selhání zahrnuje aktualizaci záznamu DNS, takže připojení klientů jsou přesměrována na nové primární až po aktualizaci mezipaměti DNS klienta.

### <a name="using-read-only-listener-for-read-only-workload"></a>Použití naslouchací proces jen pro čtení pro úlohy jen pro čtení

Pokud máte logicky izolované úlohy jen pro čtení, která je tolerantní k určité neaktuálnosti dat, můžete použít sekundární databázi v aplikaci. Pro relace jen pro `<fog-name>.secondary.database.windows.net` čtení použijte jako adresu URL serveru a připojení je automaticky směrováno na sekundární. Doporučuje se také uvést v připojovacím řetězci záměr čtení pomocí `ApplicationIntent=ReadOnly`. Pokud chcete zajistit, že úloha jen pro čtení můžete znovu připojit po převzetí služeb při selhání `AllowReadOnlyFailoverToPrimary` nebo v případě, že sekundární server přejde do offline, ujistěte se, že konfigurace vlastnost zásady převzetí služeb při selhání.

### <a name="preparing-for-performance-degradation"></a>Příprava na snížení výkonu

Typická aplikace Azure používá více služeb Azure a skládá se z více součástí. Automatické převzetí služeb při selhání skupiny převzetí služeb při selhání se aktivuje na základě stavu součásti Azure SQL sám. Ostatní služby Azure v primární oblasti nemusí být ovlivněny výpadku a jejich součásti může být stále k dispozici v této oblasti. Jakmile se primární databáze přepnout do oblasti zotavení po Havárii, latence mezi závislé součásti může zvýšit. Chcete-li se vyhnout dopadu vyšší latence na výkon aplikace, zajistěte redundanci všech součástí aplikace v oblasti zotavení po Havárii a postupujte podle [těchto pokynů pro zabezpečení sítě](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Příprava na ztrátu dat

Pokud je zjištěn výpadek, SQL čeká na `GracePeriodWithDataLossHours`období, které jste zadali . Výchozí hodnota je 1 hodina. Pokud si nemůžete dovolit ztrátu `GracePeriodWithDataLossHours` dat, ujistěte se, že jste nastavili dostatečně velké číslo, například 24 hodin. Ruční převzetí služeb při selhání pomocí skupiny použijte k navrácení služeb po selhání ze sekundárního na primární.

> [!IMPORTANT]
> Elastické fondy s 800 nebo méně DTU a více než 250 databází pomocí geografické replikace může dojít k problémům, včetně delší plánované převzetí služeb při selhání a snížený výkon.  Tyto problémy jsou pravděpodobnější dojít pro úlohy náročné na zápis, při geografické replikace koncové body jsou široce odděleny podle zeměpisné polohy nebo při více sekundární koncové body se používají pro každou databázi.  Příznaky těchto problémů jsou indikovány při zpoždění geografické replikace se zvyšuje v průběhu času.  Toto zpoždění lze sledovat pomocí [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Pokud k těmto problémům dojde, pak skutečnosti snižující závažnost rizika zahrnují zvýšení počtu dtu fondu nebo snížení počtu geograficky replikovaných databází ve stejném fondu.

### <a name="changing-secondary-region-of-the-failover-group"></a>Změna sekundární oblasti skupiny převzetí služeb při selhání

Pro ilustraci pořadí změn budeme předpokládat, že server A je primární server, server B je existující sekundární server a server C je nový sekundární ve třetí oblasti.  Chcete-li provést přechod, postupujte takto:

1.  Pomocí [aktivní geografické replikace](sql-database-active-geo-replication.md)vytvořte další sekundární databáze každé databáze na serveru A na server C . Každá databáze na serveru A bude mít dvě sekundární databáze, jednu na serveru B a jednu na serveru C. To zaručí, že primární databáze zůstanou chráněny během přechodu.
2.  Odstraňte skupinu převzetí služeb při selhání. V tomto okamžiku přihlášení se nedaří. Důvodem je, že aliasy SQL pro naslouchací procesy skupiny s podporou převzetí služeb při selhání byly odstraněny a brána nerozpozná název skupiny převzetí služeb při selhání.
3.  Znovu vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi servery A a C. V tomto okamžiku přihlášení přestane selhávat.
4.  Přidejte všechny primární databáze na serveru A do nové skupiny převzetí služeb při selhání.
5.  Přetažení serveru B. Všechny databáze na B budou automaticky smazány. 


### <a name="changing-primary-region-of-the-failover-group"></a>Změna primární oblasti skupiny převzetí služeb při selhání

Pro ilustraci pořadí změn budeme předpokládat, že server A je primární server, server B je existující sekundární server a server C je nový primární ve třetí oblasti.  Chcete-li provést přechod, postupujte takto:

1.  Proveďte plánované převzetí služeb při selhání a přepněte primární server na B. Server A se stane novým sekundárním serverem. Převzetí služeb při selhání může mít za následek několik minut prostojů. Skutečný čas bude záviset na velikosti skupiny převzetí služeb při selhání.
2.  Pomocí [aktivní geografické replikace](sql-database-active-geo-replication.md)vytvořte další sekundární databáze každé databáze na serveru B na serveru C . Každá databáze na serveru B bude mít dvě sekundární databáze, jednu na serveru A a jednu na serveru C. To zaručí, že primární databáze zůstanou chráněny během přechodu.
3.  Odstraňte skupinu převzetí služeb při selhání. V tomto okamžiku přihlášení se nedaří. Důvodem je, že aliasy SQL pro naslouchací procesy skupiny s podporou převzetí služeb při selhání byly odstraněny a brána nerozpozná název skupiny převzetí služeb při selhání.
4.  Znovu vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi servery A a C. V tomto okamžiku přihlášení přestane selhávat.
5.  Přidejte všechny primární databáze na B do nové skupiny převzetí služeb při selhání. 
6.  Proveďte plánované převzetí služeb při selhání skupiny převzetí služeb při selhání přepnout B a C. Nyní server C se stane primární a B - sekundární. Všechny sekundární databáze na serveru A budou automaticky propojeny s primárkami na C. Stejně jako v kroku 1 může mít selhání za následek několik minut prostojů.
6.  Zastavte server A. Všechny databáze na A budou automaticky odstraněny.

> [!IMPORTANT]
> Při odstranění skupiny převzetí služeb při selhání jsou odstraněny také záznamy DNS pro koncové body posluchače. V tomto okamžiku existuje nenulová pravděpodobnost, že někdo jiný vytvoří skupinu s podporou převzetí služeb při selhání nebo alias serveru se stejným názvem, což vám zabrání v jeho opětovném použití. Chcete-li minimalizovat riziko, nepoužívejte obecné názvy skupin převzetí služeb při selhání.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Doporučené postupy používání skupin s podporou převzetí služeb při selhání se spravovanými instancemi

Skupina automatického převzetí služeb při selhání musí být nakonfigurovaná v primární instanci a připojí ji k sekundární instanci v jiné oblasti Azure.  Všechny databáze v instanci budou replikovány do sekundární instance.

Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace pomocí spravované instance a skupiny automatického převzetí služeb při selhání.

![automatické převzetí služeb při selhání](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> V [tématu Přidání spravované instance do skupiny převzetí služeb při selhání](sql-database-managed-instance-failover-group-tutorial.md) pro podrobný kurz krok za krokem přidání spravované instance pro použití skupiny převzetí služeb při selhání.

Pokud vaše aplikace používá spravovanou instanci jako datovou vrstvu, postupujte při navrhování kontinuity provozu podle těchto obecných pokynů:

### <a name="creating-the-secondary-instance"></a>Vytvoření sekundární instance 

Chcete-li zajistit nepřerušené připojení k primární instanci po převzetí služeb při selhání, musí být primární i sekundární instance ve stejné zóně DNS. Zaručí, že stejný certifikát s více doménami (SAN) lze použít k ověření připojení klientů k jedné ze dvou instancí ve skupině s podporou převzetí služeb při selhání. Když je vaše aplikace připravená pro produkční nasazení, vytvořte sekundární instanci v jiné oblasti a ujistěte se, že sdílí zónu DNS s primární instancí. Můžete to udělat zadáním `DNS Zone Partner` volitelný parametr pomocí portálu Azure, PowerShell nebo rozhraní REST API.

> [!IMPORTANT]
> První instance vytvořená v podsíti určuje zónu DNS pro všechny následující instance ve stejné podsíti. To znamená, že dvě instance ze stejné podsítě nemohou patřit do různých zón DNS.

Další informace o vytvoření sekundární instance ve stejné zóně DNS jako primární instance naleznete v [tématu Vytvoření sekundární spravované instance](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

### <a name="enabling-replication-traffic-between-two-instances"></a>Povolení replikačního provozu mezi dvěma instancemi

Vzhledem k tomu, že každá instance je izolovaná ve své vlastní virtuální síti, musí být povolen obousměrný provoz mezi těmito virtuálními sítěmi. Viz [brána Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Vytvoření skupiny převzetí služeb při selhání mezi spravovanými instancemi v různých předplatných

Můžete vytvořit skupinu převzetí služeb při selhání mezi spravovanými instancemi ve dvou různých předplatných. Při použití rozhraní API prostředí PowerShell `PartnerSubscriptionId` to můžete udělat zadáním parametru pro sekundární instanci. Při použití rozhraní REST API může `properties.managedInstancePairs` mít každé ID instance zahrnuté v parametru vlastní id předplatného.
  
> [!IMPORTANT]
> Portál Azure nepodporuje vytváření skupin s podporou převzetí služeb při selhání v různých předplatných. Také pro existující skupiny převzetí služeb při selhání v různých předplatných nebo skupinách prostředků nelze spustit ručně prostřednictvím portálu z primární instance. Spusťte ji z geograficky sekundární instance místo.

### <a name="managing-failover-to-secondary-instance"></a>Správa převzetí služeb při selhání sekundární instanci

Skupina převzetí služeb při selhání bude spravovat převzetí služeb při selhání všech databází v instanci. Při vytvoření skupiny bude každá databáze v instanci automaticky geograficky replikována do sekundární instance. Skupiny převzetí služeb při selhání nelze použít k zahájení částečného převzetí služeb při selhání podmnožiny databází.

> [!IMPORTANT]
> Pokud je databáze odebrána z primární instance, bude také automaticky vynechána na geograficky sekundární instanci.

### <a name="using-read-write-listener-for-oltp-workload"></a>Použití naslouchací proces čtení a zápisu pro úlohu OLTP

Při provádění operací OLTP `<fog-name>.zone_id.database.windows.net` použijte jako adresu URL serveru a připojení jsou automaticky přesměrována na primární. Tato adresa URL se po převzetí služeb při selhání nezmění. Převzetí služeb při selhání zahrnuje aktualizaci záznamu DNS, takže připojení klientů jsou přesměrována na nové primární až po aktualizaci mezipaměti DNS klienta. Vzhledem k tomu, že sekundární instance sdílí zónu DNS s primární, klientská aplikace se k ní bude moci znovu připojit pomocí stejného certifikátu SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Použití naslouchací proces jen pro čtení pro připojení k sekundární instanci

Pokud máte logicky izolované úlohy jen pro čtení, která je tolerantní k určité neaktuálnosti dat, můžete použít sekundární databázi v aplikaci. Chcete-li se připojit přímo k sekundární `server.secondary.zone_id.database.windows.net` geograficky replikované, použijte jako adresu URL serveru a připojení je provedeno přímo na geograficky replikované sekundární.

> [!NOTE]
> V některých úrovních služby Azure SQL Database podporuje použití [replik jen pro čtení](sql-database-read-scale-out.md) k vyrovnávání zatížení úlohy `ApplicationIntent=ReadOnly` dotazjen pro čtení pomocí kapacity jedné repliky jen pro čtení a pomocí parametru v připojovacím řetězci. Pokud jste nakonfigurovali sekundární geografickou replikaci, můžete tuto funkci použít k připojení k replikě jen pro čtení v primárním umístění nebo v geograficky replikovaném umístění.
> - Chcete-li se připojit k replice jen `<fog-name>.zone_id.database.windows.net`pro čtení v primárním umístění, použijte .
> - Chcete-li se připojit k replice jen `<fog-name>.secondary.zone_id.database.windows.net`pro čtení v sekundárním umístění, použijte .

### <a name="preparing-for-performance-degradation"></a>Příprava na snížení výkonu

Typická aplikace Azure používá více služeb Azure a skládá se z více součástí. Automatické převzetí služeb při selhání skupiny převzetí služeb při selhání se aktivuje na základě stavu součásti Azure SQL sám. Ostatní služby Azure v primární oblasti nemusí být ovlivněny výpadku a jejich součásti může být stále k dispozici v této oblasti. Jakmile se primární databáze přepnout do oblasti zotavení po Havárii, latence mezi závislé součásti může zvýšit. Chcete-li se vyhnout dopadu vyšší latence na výkon aplikace, zajistěte redundanci všech součástí aplikace v oblasti zotavení po Havárii a postupujte podle [těchto pokynů pro zabezpečení sítě](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Příprava na ztrátu dat

Pokud je zjištěn výpadek, SQL automaticky aktivuje převzetí služeb při selhání čtení a zápisu, pokud je nulová ztráta dat podle našich nejlepších znalostí. V opačném případě čeká na období `GracePeriodWithDataLossHours`zadané . Pokud jste `GracePeriodWithDataLossHours`zadali , připravte se na ztrátu dat. Obecně platí, že během výpadků Azure upřednostňuje dostupnost. Pokud si nemůžete dovolit ztrátu dat, ujistěte se, že nastavit GracePeriodWithDataLossHours na dostatečně velké číslo, například 24 hodin.

Aktualizace DNS naslouchací proces čtení a zápis ustane ihned po spuštění převzetí služeb při selhání. Tato operace nebude mít za následek ztrátu dat. Proces přepínání databázových rolí však může za normálních podmínek trvat až 5 minut. Dokud nebude dokončena, některé databáze v nové primární instanci budou stále jen pro čtení. Pokud převzetí služeb při selhání je zahájeno pomocí prostředí PowerShell, celá operace je synchronní. Pokud se inicializuje pomocí portálu Azure, ui bude indikovat stav dokončení. Pokud se inicializuje pomocí rozhraní REST API, použijte standardní mechanismus dotazování Azure Resource Manager u monitorování pro dokončení.

> [!IMPORTANT]
> Ruční převzetí služeb při selhání pomocí skupiny přesuňte primárky zpět do původního umístění. Při výpadku, který způsobil převzetí služeb při selhání je zmírněna, můžete přesunout primární databáze do původního umístění. Chcete-li to provést, měli byste zahájit ruční převzetí služeb při selhání skupiny.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Změna sekundární oblasti skupiny převzetí služeb při selhání

Předpokládejme, že instance A je primární instance, instance B je existující sekundární instance a instance C je nová sekundární instance ve třetí oblasti.  Chcete-li provést přechod, postupujte takto:

1.  Vytvořte instanci C se stejnou velikostí jako A a ve stejné zóně DNS. 
2.  Odstraňte skupinu převzetí služeb při selhání mezi instancemi A a B. V tomto okamžiku přihlášení se nezdaří, protože aliasy SQL pro naslouchací procesy skupiny převzetí služeb při selhání byly odstraněny a brána nerozpozná název skupiny převzetí služeb při selhání. Sekundární databáze budou odpojeny od primárek a stanou se databázemi pro čtení a zápis. 
3.  Vytvořte skupinu převzetí služeb při selhání se stejným názvem mezi instancí A a C. Postupujte podle pokynů ve [skupině s podporou převzetí služeb při selhání s kurzem spravované instance](sql-database-managed-instance-failover-group-tutorial.md). Toto je operace velikosti dat a bude dokončena, když jsou všechny databáze z instance A nasazeny a synchronizovány.
4.  Odstraňte instanci B, pokud není potřeba, abyste se vyhnuli zbytečným poplatkům.

> [!NOTE]
> Po kroku 2 a až do dokončení kroku 3 databáze v instanci A zůstane nechráněné před katastrofickým selháním instance A.

### <a name="changing-primary-region-of-the-failover-group"></a>Změna primární oblasti skupiny převzetí služeb při selhání

Předpokládejme, že instance A je primární instance, instance B je existující sekundární instance a instance C je nová primární instance ve třetí oblasti.  Chcete-li provést přechod, postupujte takto:

1.  Vytvořte instanci C se stejnou velikostí jako B a ve stejné zóně DNS. 
2.  Připojte se k instanci B a ručně převzetí služeb při selhání přepnout primární instanci b. Instance A se stane automaticky novou sekundární instance.
3.  Odstraňte skupinu převzetí služeb při selhání mezi instancemi A a B. V tomto okamžiku přihlášení se nezdaří, protože aliasy SQL pro naslouchací procesy skupiny převzetí služeb při selhání byly odstraněny a brána nerozpozná název skupiny převzetí služeb při selhání. Sekundární databáze budou odpojeny od primárek a stanou se databázemi pro čtení a zápis. 
4.  Vytvořte skupinu s podporou převzetí služeb při selhání se stejným názvem mezi instancí A a C. Postupujte podle pokynů ve [skupině s podporou převzetí služeb při selhání pomocí kurzu spravované instance](sql-database-managed-instance-failover-group-tutorial.md). Toto je operace velikosti dat a bude dokončena, když jsou všechny databáze z instance A nasazeny a synchronizovány.
5.  Odstranit instanci A, pokud není potřeba, aby se zabránilo zbytečným poplatkům.

> [!NOTE] 
> Po kroku 3 a až do dokončení kroku 4 databáze v instanci A zůstane nechráněné před katastrofickým selháním instance A.

> [!IMPORTANT]
> Při odstranění skupiny převzetí služeb při selhání jsou odstraněny také záznamy DNS pro koncové body posluchače. V tomto okamžiku existuje nenulová pravděpodobnost, že někdo jiný vytvoří skupinu s podporou převzetí služeb při selhání nebo alias serveru se stejným názvem, což vám zabrání v jeho opětovném použití. Chcete-li minimalizovat riziko, nepoužívejte obecné názvy skupin převzetí služeb při selhání.

## <a name="failover-groups-and-network-security"></a>Skupiny převzetí služeb při selhání a zabezpečení sítě

U některých aplikací pravidla zabezpečení vyžadují, aby byl přístup k datové vrstvě omezen na určitou komponentu nebo součásti, jako je virtuální soud, webová služba atd. Tento požadavek představuje některé problémy pro návrh kontinuity provozu a použití skupin převzetí služeb při selhání. Při implementaci takového omezeného přístupu zvažte následující možnosti.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Použití skupin s podporou převzetí služeb při selhání a pravidel virtuální sítě

Pokud používáte [koncové body služby Virtuální síť a pravidla](sql-database-vnet-service-endpoint-rule-overview.md) k omezení přístupu k databázi SQL, uvědomte si, že každý koncový bod služby virtuální sítě se vztahuje pouze na jednu oblast Azure. Koncový bod neumožňuje jiné oblasti přijímat komunikaci z podsítě. Proto pouze klientské aplikace nasazené ve stejné oblasti se mohou připojit k primární databázi. Vzhledem k tomu, že výsledkem převzetí služeb při selhání jsou relace klientů SQL přesměrovány na server v jiné (sekundární) oblasti, tyto relace se nezdaří, pokud pochází z klienta mimo tuto oblast. Z tohoto důvodu nelze povolit zásady automatického převzetí služeb při selhání, pokud jsou zúčastněné servery zahrnuty do pravidel virtuální sítě. Chcete-li podporovat ruční převzetí služeb při selhání, postupujte takto:

1. Zřízení redundantních kopií front-endových součástí vaší aplikace (webová služba, virtuální počítače atd.) v sekundární oblasti
2. Konfigurace [pravidel virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) jednotlivě pro primární a sekundární server
3. Povolení [převzetí služeb při selhání front-endu pomocí konfigurace správce provozu](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Zahájit ruční převzetí služeb při selhání při zjištění výpadku. Tato možnost je optimalizovaná pro aplikace, které vyžadují konzistentní latenci mezi front-endem a datovou vrstvou a podporuje obnovení, když je výpadek ovlivněn buď front-endem, datovou vrstvou nebo oběma.

> [!NOTE]
> Pokud používáte **naslouchací proces jen pro čtení** k vyrovnávání zatížení úlohy jen pro čtení, ujistěte se, že tato úloha se spouští ve virtuálním virtuálním zařízení nebo jiné prostředky v sekundární oblasti, aby se můžete připojit k sekundární databázi.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Použití skupin převzetí služeb při selhání a pravidel brány firewall databáze SQL

Pokud váš plán kontinuity podnikání vyžaduje převzetí služeb při selhání pomocí skupin s automatickým převzetím služeb při selhání, můžete omezit přístup k databázi SQL pomocí tradičních pravidel brány firewall. Chcete-li podporovat automatické převzetí služeb při selhání, postupujte takto:

1. [Vytvoření veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Vytvořte veřejný elitář](../load-balancer/quickstart-load-balancer-standard-public-portal.md) a přiřaďte mu veřejnou IP adresu.
3. [Vytvoření virtuální sítě a virtuálních počítačů](../load-balancer/quickstart-load-balancer-standard-public-portal.md) pro komponenty front-endu
4. [Vytvořte skupinu zabezpečení sítě](../virtual-network/security-overview.md) a nakonfigurujte příchozí připojení.
5. Ujistěte se, že odchozí připojení jsou otevřená do databáze Azure SQL pomocí [značky služby](../virtual-network/security-overview.md#service-tags)"Sql" .
6. Vytvořte [pravidlo brány firewall databáze SQL,](sql-database-firewall-configure.md) které umožní příchozí provoz z veřejné IP adresy, kterou vytvoříte v kroku 1.

Další informace o konfiguraci odchozího přístupu a o tom, jakou ip adresu použít v pravidlech brány firewall, naleznete v [tématu Odchozí připojení vyvažovače zatížení](../load-balancer/load-balancer-outbound-connections.md).

Výše uvedená konfigurace zajistí, že automatické převzetí služeb při selhání nebude blokovat připojení z front-endových součástí a předpokládá, že aplikace může tolerovat delší latenci mezi front-endem a datovou vrstvou.

> [!IMPORTANT]
> Chcete-li zaručit kontinuitu provozu pro regionální výpadky, musíte zajistit geografickou redundanci pro front-endové komponenty i databáze.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Povolení geografické replikace mezi spravovanými instancemi a jejich virtuálními sítěmi

Když nastavíte skupinu převzetí služeb při selhání mezi primární a sekundární spravované instance ve dvou různých oblastech, každá instance je izolována pomocí nezávislé virtuální sítě. Chcete-li povolit provoz replikace mezi těmito virtuálními sítěmi, ujistěte se, že jsou splněny tyto požadavky:

1. Dvě spravované instance musí být v různých oblastech Azure.
2. Dvě spravované instance musí být stejné úrovně služby a mají stejnou velikost úložiště.
3. Sekundární spravovaná instance musí být prázdná (žádné uživatelské databáze).
4. Virtuální sítě používané spravovanými instancemi musí být připojeny prostřednictvím [brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [expresní trasy](../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Když se dvě virtuální sítě připojují prostřednictvím místní sítě, ujistěte se, že neexistují žádné pravidlo brány firewall blokující porty 5022 a 11000-11999. Globální partnerský vztah virtuální sítě není podporován.
5. Dvě spravované instance virtuální sítě nemůže mít překrývající se IP adresy.
6. Je třeba nastavit skupiny zabezpečení sítě (NSG) tak, aby porty 5022 a rozsah 11000 ~12000 jsou otevřené příchozí a odchozí pro připojení z podsítě jiné spravované instance. Toto je povolit přenosreplikace mezi instancemi.

   > [!IMPORTANT]
   > Chybně nakonfigurovaná pravidla zabezpečení skupiny zabezpečení sítě vedou k operacím kopírování databáze.

7. Sekundární instance je nakonfigurována se správným ID zóny DNS. Zóna DNS je vlastnostspravované instance a virtuálního clusteru a její ID je zahrnuto v adrese názvu hostitele. ID zóny se generuje jako náhodný řetězec při vytvoření první spravované instance v každé virtuální síti a stejné ID je přiřazeno všem ostatním instancím ve stejné podsíti. Po přiřazení nelze zónu DNS změnit. Spravované instance zahrnuté ve stejné skupině převzetí služeb při selhání musí sdílet zónu DNS. Toho dosáhnete předáním ID zóny primární instance jako hodnoty parametru DnsZonePartner při vytváření sekundární instance. 

   > [!NOTE]
   > Podrobný návod na konfiguraci skupin s podporou převzetí služeb při selhání se spravovanou instancí naleznete [v tématu přidání spravované instance do skupiny s podporou převzetí služeb při selhání](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Upgrade nebo snížení klasifikace primární databáze

Primární databázi můžete upgradovat nebo depozitovat na jinou výpočetní velikost (v rámci stejné úrovně služby, nikoli mezi obecnými a důležitými pro podniky), aniž byste odpojili sekundární databáze. Při upgradu doporučujeme nejprve inovovat všechny sekundární databáze a potom upgradovat primární. Při downgradu pořadí, stornovat pořadí: downgrade primární první a potom downgrade všechny sekundární databáze. Při upgradu nebo downgrade databáze na jinou úroveň služby, toto doporučení je vynuceno.

Tato sekvence se doporučuje konkrétně, aby se zabránilo problému, kde sekundární na nižší skladové položky získá přetížené a musí být re-seeded během procesu upgradu nebo downgrade. Můžete se také vyhnout problému tím, že primární jen pro čtení, na úkor dopadu všech úloh pro čtení a zápis proti primární.

> [!NOTE]
> Pokud jste vytvořili sekundární databázi jako součást konfigurace skupiny převzetí služeb při selhání, nedoporučuje se přechod na nižší verzi sekundární databáze. Tím zajistíte, že vaše datová vrstva má dostatečnou kapacitu pro zpracování běžné úlohy po aktivaci převzetí služeb při selhání.

## <a name="preventing-the-loss-of-critical-data"></a>Předcházení ztrátě důležitých dat

Vzhledem k vysoké latenci rozsáhlých sítí používá souvislá kopie mechanismus asynchronní replikace. Asynchronní replikace způsobí, že některé ztráty dat nevyhnutelné, pokud dojde k selhání. Některé aplikace však může vyžadovat žádnou ztrátu dat. Chcete-li chránit tyto důležité aktualizace, může vývojář aplikace zavolat [proceduru sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systému ihned po potvrzení transakce. Volání `sp_wait_for_database_copy_sync` blokuje volající vlákno, dokud nebyla poslední potvrzená transakce přenesena do sekundární databáze. Však nečeká na přehrání předávaných transakcí a potvrzené na sekundární. `sp_wait_for_database_copy_sync`je vymezen a to na konkrétní souvislé kopírovací propojení. Tento postup může volat každý uživatel s právy připojení k primární databázi.

> [!NOTE]
> `sp_wait_for_database_copy_sync`zabraňuje ztrátě dat po převzetí služeb při selhání, ale nezaručuje úplnou synchronizaci pro přístup pro čtení. Zpoždění způsobené volání `sp_wait_for_database_copy_sync` procedury může být významné a závisí na velikosti transakční protokol v době volání.

## <a name="failover-groups-and-point-in-time-restore"></a>Skupiny převzetí služeb při selhání a obnovení bodu v čase

Informace o použití obnovení bodu v čase se skupinami s podporou převzetí služeb při selhání naleznete [v tématu Bod v čase zotavení (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Omezení skupin převzetí služeb při selhání

Mějte na paměti následující omezení:

- Skupinu převzetí služeb při selhání nelze vytvořit mezi dvěma servery nebo instancemi ve stejné oblasti Azure.
- Skupinu převzetí služeb při selhání nelze přejmenovat. Budete muset odstranit skupinu a znovu ji vytvořit s jiným názvem. 
- Přejmenování databáze není podporováno pro instance ve skupině převzetí služeb při selhání. Chcete-li přejmenovat databázi, budete muset dočasně odstranit skupinu převzetí služeb při selhání.

## <a name="programmatically-managing-failover-groups"></a>Programová správa skupin s podporou převzetí služeb při selhání

Jak již bylo popsáno dříve, automatické převzetí služeb při selhání skupiny a aktivní geografické replikace lze také spravovat programově pomocí Azure PowerShell a rozhraní REST API. Následující tabulky popisují sadu příkazů, které jsou k dispozici. Aktivní geografická replikace zahrnuje sadu rozhraní API Azure Pro Správce prostředků pro správu, včetně [rozhraní AZURE SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) a rutin Azure [PowerShell](https://docs.microsoft.com/powershell/azure/overview). Tato řešení API vyžadují použití skupin prostředků a podporují zabezpečení založené na rolích (RBAC). Další informace o tom, jak implementovat přístupové role, najdete v [tématu Řízení přístupu na základě rolí Azure](../role-based-access-control/overview.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Správa převzetí služeb při selhání databáze SQL pomocí jednotlivých databází a elastických fondů

| Rutina | Popis |
| --- | --- |
| [Nová-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji na primárních i sekundárních serverech.|
| [Odebrat-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Odebere skupinu převzetí služeb při selhání ze serveru. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Načte konfiguraci skupiny s podporou převzetí služeb při selhání. |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Upravuje konfiguraci skupiny převzetí služeb při selhání. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Přidá do skupiny převzetí služeb při selhání jednu nebo více databází.|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Správa skupin převzetí služeb při selhání databáze SQL se spravovanými instancemi

| Rutina | Popis |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji v primárníi i sekundárních instancích.|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Upravuje konfiguraci skupiny převzetí služeb při selhání.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Načte konfiguraci skupiny s podporou převzetí služeb při selhání.|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární instanci|
| [Odebrat-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Odebere skupinu převzetí služeb při selhání.|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Správa převzetí služeb při selhání databáze SQL pomocí jednotlivých databází a elastických fondů

| Příkaz | Popis |
| --- | --- |
| [az sql převzetí služeb při selhání vytvořit skupiny](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji na primárních i sekundárních serverech.|
| [odstranění skupiny převzetí služeb při selhání az SQL](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Odebere skupinu převzetí služeb při selhání ze serveru. |
| [az sql převrácení služeb při selhání zobrazit](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Načte konfiguraci skupiny s podporou převzetí služeb při selhání. |
| [aktualizace skupiny převzetí služeb při selhání služby AZ SQL](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Upraví konfiguraci skupiny s podporou převzetí služeb při selhání nebo přidá jednu nebo více databází do skupiny převzetí služeb při selhání.|
| [az sql převzetí služeb při selhání skupina-primární](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Správa skupin převzetí služeb při selhání databáze SQL se spravovanými instancemi

| Příkaz | Popis |
| --- | --- |
| [az sql instance-failover-group create az sql instance-failover-group create az sql instance-failover-group create az](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ji v primárníi i sekundárních instancích. |
| [aktualizace az sql instance-failover-group](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Upravuje konfiguraci skupiny s podporou převzetí služeb při selhání.|
| [az sql instance-failover-group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Načte konfiguraci skupiny s podporou převzetí služeb při selhání.|
| [az sql instance-failover-group-primární](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární instanci|
| [az sql instance-failover-group delete az sql instance-failover-group delete az sql instance-failover-group delete az](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Odebere skupinu převzetí služeb při selhání. |

* * *

> [!IMPORTANT]
> Ukázkový skript naleznete v [tématu Konfigurace skupiny převzetí služeb při selhání a převzetí služeb při selhání pro jednu databázi](scripts/sql-database-add-single-db-to-failover-group-powershell.md).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>ROZHRANÍ REST API: Správa skupin převzetí služeb při selhání databáze SQL pomocí jedné a sdružené databáze

| rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Vytvoří nebo aktualizuje skupinu s podporou převzetí služeb při selhání. |
| [Odstranit skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Odebere skupinu převzetí služeb při selhání ze serveru. |
| [Převzetí služeb při selhání (plánováno)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Aktivuje převzetí služeb při selhání z aktuálního primárního serveru na sekundární server s úplnou synchronizací dat.|
| [Vynutit převzetí služeb při selhání povolit ztrátu dat](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Aktivuje převzetí služeb při selhání z aktuálního primárního serveru na sekundární server bez synchronizace dat. Tato operace může způsobit ztrátu dat. |
| [Získat skupinu s podporou převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Načte konfiguraci skupiny s podporou převzetí služeb při selhání. |
| [Seznam skupin převzetí služeb při selhání podle serveru](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Zobrazí seznam skupin převzetí služeb při selhání na serveru. |
| [Aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aktualizuje konfiguraci skupiny s podporou převzetí služeb při selhání. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>ROZHRANÍ REST API: Správa skupin s podporou převzetí služeb při selhání pomocí spravovaných instancí

| rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Vytvoří nebo aktualizuje konfiguraci skupiny s podporou převzetí služeb při selhání. |
| [Odstranit skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Odebere skupinu převzetí služeb při selhání z instance. |
| [Převzetí služeb při selhání (plánováno)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Aktivuje převzetí služeb při selhání z aktuální primární instance do této instance s úplnou synchronizací dat. |
| [Vynutit převzetí služeb při selhání povolit ztrátu dat](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Aktivuje převzetí služeb při selhání z aktuální primární instance do sekundární instance bez synchronizace dat. Tato operace může způsobit ztrátu dat. |
| [Získat skupinu s podporou převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | načte konfiguraci skupiny s podporou převzetí služeb při selhání. |
| [Seznam skupin s podporou převzetí služeb při selhání – seznam podle umístění](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Zobrazí seznam skupin s podporou převzetí služeb při selhání v umístění. |

## <a name="next-steps"></a>Další kroky

- Podrobné návody najdete na
    - [Přidání jedné databáze do skupiny převzetí služeb při selhání](sql-database-single-database-failover-group-tutorial.md)
    - [Přidání elastického fondu do skupiny převzetí služeb při selhání](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Přidání spravované instance do skupiny převzetí služeb při selhání](sql-database-managed-instance-failover-group-tutorial.md)
- Ukázkové skripty naleznete v tématu:
  - [Konfigurace aktivní geografické replikace pro jednu databázi v Azure SQL Database pomocí PowerShellu](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurace aktivní geografické replikace pro sdruženou databázi v Azure SQL Database pomocí PowerShellu](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Přidání jedné databáze Azure SQL Database do skupiny s podporou převzetí služeb při selhání pomocí Prostředí PowerShell](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Přehled kontinuity provozu a scénáře najdete v tématu [Přehled kontinuity provozu](sql-database-business-continuity.md)
- Informace o automatickém zálohování azure sql database najdete v tématu [automatické zálohování databáze SQL Database](sql-database-automated-backups.md).
- Další informace o použití automatického zálohování pro obnovení naleznete [v tématu Obnovení databáze ze záloh iniciovaných službou](sql-database-recovery-using-backups.md).
- Další informace o požadavcích na ověřování pro nový primární server a databázi naleznete [v tématu zabezpečení databáze SQL po zotavení po havárii](sql-database-geo-replication-security-config.md).
