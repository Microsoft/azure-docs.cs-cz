---
title: Správa jedné a sdružené databáze po migraci
description: Zjistěte, jak spravovat databázi po migraci do Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: ebb512fee0186bed3cc7f49f0525dac43e57da3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256182"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>Nový dba v cloudu – správa jednotlivých a sdružených databází v Azure SQL Database

Přechod od tradičního samořízeného, samořízeného prostředí k prostředí PaaS se může zpočátku zdát trochu ohromující. Jako vývojář aplikací nebo DBA byste chtěli znát základní funkce platformy, které by vám pomohly udržet vaši aplikaci k dispozici, výkonnou, bezpečnou a odolnou - vždy. Tento článek si klade za cíl udělat přesně to. Článek stručně organizuje prostředky a poskytuje některé pokyny, jak nejlépe využít klíčové funkce databáze SQL s jedním a sdružené databáze pro správu a udržení aplikace běží efektivně a dosáhnout optimálních výsledků v cloudu. Typické publikum pro tento článek by se ti, kteří:

- Vyhodnocují migraci jejich aplikací do Azure SQL Database – modernizace aplikací.
- Jsou v procesu migrace jejich aplikace – On-going migrace scénář.
- Nedávno dokončili migraci do Azure SQL DB – Nový DBA v cloudu.

Tento článek popisuje některé základní charakteristiky Azure SQL Database jako platformu, kterou můžete snadno využít při práci s jednotlivými databázemi a sdruženými databázemi v elastických fondech. Jedná se o následující:

- Monitorování databáze pomocí portálu Azure
- Provozní kontinuita a zotavení po havárii (BCDR)
- Zabezpečení a dodržování předpisů
- Inteligentní monitorování a údržba databází
- Přesuny dat

> [!NOTE]
> Tento článek se vztahuje na následující možnosti nasazení v Azure SQL Database: jednotlivé databáze a elastické fondy. Nevztahuje se na možnost nasazení spravované instance v databázi SQL.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorování databází na portálu Azure

Na [webu Azure Portal](https://portal.azure.com/)můžete sledovat využití jednotlivých databází výběrem databáze a kliknutím na graf **Monitorování.** Zobrazí se okno **Metrika**, které můžete upravit kliknutím na **Upravit graf**. Přidejte následující metriky:

- Procento CPU
- Procento DTU
- Procento datových V/V
- Procento velikosti databáze

Po přidání těchto metrik je můžete dál zobrazovat v grafu **Monitorování** s dalšími informacemi v okně **Metrika.** Tyto čtyři metriky uvádějí průměrné využití v procentech vzhledem k hodnotě **DTU** vaší databáze. Další informace o úrovních služeb najdete v článku [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a na [nákupních modelech založených na virtuálních jádrech.](sql-database-service-tiers-vcore.md)  

![Monitorování výkonu databáze v rámci úrovně služeb](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Můžete také nastavit upozornění na výkonové metriky. Klikněte na tlačítko **Přidat upozornění** v okně **Metrika**. Nastavte upozornění podle pokynů průvodce. Můžete určit, zda chcete být upozorněni na překročení zadané prahové hodnoty, nebo naopak když metrika poklesne pod zadanou mez.

Například pokud očekáváte nárůst zatížení databáze, můžete nastavit e-mailové upozornění pro případ, že databáze překročí 80 % kterékoli výkonové metriky. Můžete použít jako včasné varování zjistit, kdy budete muset přepnout na další nejvyšší výpočetní velikost.

Metriky výkonu vám také mohou pomoci určit, jestli jste schopni přejít na nižší výpočetní velikost. Předpokládejme, že používáte databáze S2 v úrovni Standard a všechny metriky ukazují, že databáze v průměru nevyužívá více než 10 % dostupného výkonu. Je pravděpodobné, že databáze bude dobře fungovat i v úrovni Standard S1. Uvědomte si však úlohy, které se před rozhodnutím přesunout na nižší výpočetní velikost zoátápějí nebo kolísají.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Provozní kontinuita a zotavení po havárii (BCDR)

Možnosti kontinuity provozu a zotavení po havárii vám umožní pokračovat v práci jako obvykle v případě katastrofy. Katastrofou může být událost na úrovni databáze (například někdo omylem upustí klíčovou tabulku) nebo událost na úrovni datového centra (regionální katastrofa, například tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Jak lze vytvořit a spravovat zálohy v databázi SQL

Nevytváříte zálohy v Azure SQL DB a to proto, že není třeba. SQL Database automaticky zálohuje databáze za vás, takže už se nemusíte starat o plánování, přijímání a správu záloh. Platforma trvá úplnou zálohu každý týden, rozdílové zálohování každých několik hodin a záloha protokolu každých 5 minut, aby bylo zajištěno, že zotavení po havárii je efektivní a ztráta dat minimální. První úplná záloha se stane, jakmile vytvoříte databázi. Tyto zálohy jsou k dispozici po určitou dobu s názvem "Doba uchování" a liší se v závislosti na úrovni služby, kterou zvolíte. SQL Database poskytuje možnost obnovit do libovolného bodu v čase v rámci této doby uchovávání pomocí [bodu v čase zotavení (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Úroveň služeb|Retenční doba ve dnech|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Kromě toho funkce [dlouhodobé uchovávání (LTR)](sql-database-long-term-retention.md) umožňuje uchovávat záložní soubory po mnohem delší dobu konkrétně po dobu až 10 let a obnovit data z těchto záloh v libovolném okamžiku během této doby. Zálohy databáze jsou navíc uloženy v geograficky replikovaném úložišti, aby byla zajištěna odolnost před regionální katastrofou. Tyto zálohy můžete také obnovit v libovolné oblasti Azure v libovolném okamžiku v rámci období uchování. Viz [Přehled kontinuity provozu](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Jak zajistím kontinuitu provozu v případě katastrofy na úrovni datového centra nebo regionální katastrofy

Vzhledem k tomu, že zálohy databáze jsou uloženy v geograficky replikované úložiště zajistit, že v případě regionální havárie, můžete obnovit zálohu do jiné oblasti Azure. To se nazývá geo-obnovení. RPO (Cíl bodu obnovení) je obecně < 1 hodinu a ERT (Odhadovaná doba zotavení – několik minut až hodin).

Pro kritické databáze nabízí Azure SQL DB aktivní geografickou replikaci. V podstatě se vytvoří geograficky replikovaná sekundární kopie původní databáze v jiné oblasti. Například pokud vaše databáze je zpočátku hostované v oblasti Azure – západ USA a chcete odolnost regionální chod. Vytvořili byste aktivní geo repliku databáze v západní CHU, abyste řekli Východní USA. Když kalamita udeří na západní USA, můžete se dostat do východního amerického regionu. Konfigurace je ve skupině automaticképřevzetí služeb při selhání je ještě lepší, protože to zajišťuje, že databáze automaticky převezme služby při selhání sekundární v USA ve východním usa v případě havárie. RPO pro toto je < 5 sekund a ERT < 30 sekund.

Pokud není nakonfigurována skupina automatického převzetí služeb při selhání, musí vaše aplikace aktivně sledovat havárii a zahájit převzetí služeb při selhání sekundární. Můžete vytvořit až 4 takové aktivní geografické repliky v různých oblastech Azure. Bude to ještě lepší. Můžete také přistupovat k těmto sekundárním aktivním geografickým replikám pro přístup jen pro čtení. To je velmi užitečné pro snížení latence pro scénář geograficky distribuovaných aplikací.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Jak se můj plán zotavení po havárii změní z místní na databázi SQL

V souhrnu tradiční místní nastavení serveru SQL Server vyžaduje, abyste aktivně spravovali dostupnost pomocí funkcí, jako je clustering s podporou převzetí služeb při selhání, zrcadlení databáze, replikace transakcí nebo přesouvání protokolů a údržba a správa záloh, aby bylo zajištěno Kontinuita podnikání. S SQL Database, platforma spravuje tyto za vás, takže se můžete soustředit na vývoj a optimalizaci databázové aplikace a nemusíte se starat o správu katastrof tolik. Můžete mít plány zálohování a zotavení po havárii nakonfigurované a pracují s několika kliknutími na webu Azure Portal (nebo několik příkazů pomocí rozhraní API prostředí PowerShell).

Další informace o zotavení po havárii najdete v [tématu: Azure SQL DB Disaster Recovery 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Zabezpečení a dodržování předpisů

SQL Database bere zabezpečení a ochranu osobních údajů velmi vážně. Zabezpečení v rámci databáze SQL je k dispozici na úrovni databáze a na úrovni platformy a je nejlépe pochopit, když jsou rozděleny do několika vrstev. V každé vrstvě se dostanete ke kontrole a zajištění optimálního zabezpečení pro vaši aplikaci. Vrstvy jsou:

- Ověřování & identity[(ověřování SQL a ověřování služby Azure Active Directory [AAD].](sql-database-manage-logins.md)
- Monitorovací činnost ([Auditování](sql-database-auditing.md) a [detekce hrozeb](sql-database-threat-detection.md)).
- Ochrana skutečných dat[(Transparentní šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) a [vždy šifrované [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Řízení přístupu k citlivým a privilegovaným datům[(zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) a [dynamické maskování dat).](/sql/relational-databases/security/dynamic-data-masking)

[Azure Security Center](https://azure.microsoft.com/services/security-center/) nabízí centralizovanou správu zabezpečení napříč úlohami spuštěným v Azure, místně a v jiných cloudech. Můžete zobrazit, zda jsou základní ochrana databáze SQL, jako je [auditování](sql-database-auditing.md) a [transparentní šifrování dat [TDE],](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) nakonfigurovány na všech prostředcích a vytvořit zásady založené na vlastních požadavcích.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Jaké metody ověřování uživatelů jsou nabízeny v databázi SQL

V databázi SQL jsou nabízeny dvě metody ověřování:

- [Ověřování služby Azure AD](sql-database-aad-authentication.md)
- [Ověřování pomocí SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

Tradiční ověřování systému Windows není podporováno. Azure Active Directory (AD) je centralizovaná služba správy identit a přístupu. S tímto můžete velmi pohodlně poskytnout přístup jednotného přihlašování (SSO) pro všechny pracovníky ve vaší organizaci. To znamená, že přihlašovací údaje jsou sdíleny ve všech službách Azure pro jednodušší ověřování. AAD podporuje [vícefaktorové ověřování (MFA)](sql-database-ssms-mfa-authentication.md) a pomocí [několika kliknutí](../active-directory/hybrid/how-to-connect-install-express.md) lze ad integrovat se službou Windows Server Active Directory. Ověřování SQL funguje přesně tak, jak jste ho používali v minulosti. Zadáte uživatelské jméno nebo heslo a můžete ověřit uživatele do libovolné databáze na daném serveru SQL Database. To také umožňuje SQL Database a SQL Data Warehouse nabízet vícefaktorové ověřování a uživatelské účty hosta v rámci domény Azure AD. Pokud už máte službu Active Directory v místním prostředí, můžete adresář federovat pomocí služby Azure Active Directory a rozšířit svůj adresář do Azure.

|**Pokud...**|**Databáze SQL / datový sklad SQL**|
|---|---|
|Raději nepoužívat Azure Active Directory (AD) v Azure|Použití [ověřování SQL](sql-database-security-overview.md)|
|Použito služby AD na místním serveru SQL Server|[Federate AD s Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)a používat ověřování Azure AD. S tímto, můžete použít jednotné přihlašování.|
|Potřeba vynutit vícefaktorové ověřování (MFA)|Vyžadovat vícefaktorové ověřování jako zásadu prostřednictvím [podmíněného přístupu Microsoftu](sql-database-conditional-access.md)a používat [univerzální ověřování Azure AD s podporou vícefaktorové ověřování](sql-database-ssms-mfa-authentication.md).|
|Mít účty hosta z účtů Microsoft (live.com, outlook.com) nebo jiných domén (gmail.com)|Použijte [univerzální ověřování Azure AD](sql-database-ssms-mfa-authentication.md) v databázi SQL/datovém skladu, který využívá azure [ad b2b spolupráce](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Jsou přihlášeni k Windows pomocí přihlašovacích údajů Azure AD z federované domény|Použijte [integrované ověřování Azure AD](sql-database-aad-authentication-configure.md).|
|Jsou přihlášeni k Systému Windows pomocí pověření z domény, která není federována pomocí Azure|Použijte [integrované ověřování Azure AD](sql-database-aad-authentication-configure.md).|
|Mít služby střední vrstvy, které se potřebují připojit k databázi SQL nebo datovému skladu SQL|Použijte [integrované ověřování Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Jak omezit nebo řídit přístup k databázi pomocí připojení

K dispozici je několik technik, které můžete použít k dosažení optimální organizace připojení pro vaši aplikaci.

- Pravidla brány firewall
- Koncové body služby Virtuální sítě
- Vyhrazené IP adresy

#### <a name="firewall"></a>Brána firewall

Brána firewall zabraňuje přístupu k serveru z externí entity tím, že umožňuje přístup pouze určitým entitám k databázovému serveru SQL. Ve výchozím nastavení jsou všechna připojení a databáze uvnitř serveru SQL Database zakázána, s výjimkou připojení přicházejících z jiných služeb Azure. Pomocí pravidla brány firewall můžete otevřít přístup k serveru pouze entitám (například vývojářskému počítači), které schválíte, povolením IP adresy tohoto počítače prostřednictvím brány firewall. Umožňuje také zadat rozsah IP adresy, které byste chtěli povolit přístup k serveru SQL Database. Ip adresy vývojářských počítačů ve vaší organizaci lze například přidat najednou zadáním rozsahu na stránce Nastavení brány firewall.

Pravidla brány firewall můžete vytvořit na úrovni serveru nebo na úrovni databáze. Pravidla brány IP na úrovni serveru lze vytvořit buď pomocí portálu Azure, nebo pomocí Služby SSMS. Další informace o nastavení pravidla brány firewall na úrovni serveru a databáze naleznete [v tématu: Vytvoření pravidel brány firewall IP v databázi SQL Database](sql-database-security-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Koncové body služby

Ve výchozím nastavení je vaše databáze SQL nakonfigurovaná tak, aby "Povolit službám Azure přístup k serveru" – což znamená, že jakýkoli virtuální počítač v Azure se může pokusit připojit k vaší databázi. Tyto pokusy stále musí být ověřeny. Pokud však nechcete, aby vaše databáze byla přístupná na všech IP serverech Azure, můžete zakázat funkci "Povolit službám Azure přístup k serveru". Kromě toho můžete nakonfigurovat [koncové body služby virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md).

Koncové body služby (SE) umožňují vystavit vaše důležité prostředky Azure jenom do vlastní privátní virtuální sítě v Azure. Tímto způsobem v podstatě eliminujete veřejný přístup k vašim prostředkům. Provoz mezi vaší virtuální sítí do Azure zůstane v páteřní síti Azure. Bez SE získáte vynucené tunelové směrování paketů. Vaše virtuální síť vynutí internetový provoz do vaší organizace a provoz služby Azure přejít stejnou trasu. Pomocí koncových bodů služby to můžete optimalizovat, protože pakety proudí přímo z vaší virtuální sítě do služby v páteřní síti Azure.

![Koncové body služeb virtuální sítě](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Vyhrazené IP adresy

Další možností je zřídit [vyhrazené IP adresy](../virtual-network/virtual-networks-reserved-public-ip.md) pro vaše virtuální počítače a přidat tyto konkrétní IP adresy virtuálního počítače v nastavení brány firewall serveru. Přiřazením vyhrazených IP adres ušetříte potíže s nutností aktualizovat pravidla brány firewall se změnou adres IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Na jakém portu se připojuji k databázi SQL

Přístav 1433. SQL Database komunikuje přes tento port. Chcete-li se připojit v rámci podnikové sítě, je třeba přidat odchozí pravidlo v nastavení brány firewall vaší organizace. Jako vodítko se vyhněte vystavení portu 1433 mimo hranice Azure.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Jak lze sledovat a regulovat aktivitu na serveru a databázi v databázi SQL

#### <a name="sql-database-auditing"></a>SQL Database Auditing

Pomocí databáze SQL Database můžete zapnout auditování ke sledování událostí databáze. [Auditování databáze SQL](sql-database-auditing.md) zaznamenává události databáze a zapisuje je do souboru protokolu auditu ve vašem účtu úložiště Azure. Auditování je užitečné zejména v případě, že máte v úmyslu získat přehled o možném porušení zabezpečení a zásad, zachovat dodržování předpisů atd. Umožňuje definovat a konfigurovat určité kategorie událostí, o kterých si myslíte, že potřebují auditování, a na základě toho můžete získat předkonfigurované sestavy a řídicí panel, abyste získali přehled o událostech, ke kterým dochází v databázi. Tyto zásady auditování můžete použít buď na úrovni databáze, nebo na úrovni serveru. Příručka o tom, jak zapnout auditování serveru nebo databáze, najdete v [tématu Povolení auditování databáze SQL](sql-database-security-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Detekce hrozeb

Díky [detekci hrozeb](sql-database-threat-detection.md)získáte možnost velmi snadno jednat o zabezpečení nebo porušení zásad zjištěných auditováním. Nemusíte být odborníkem na zabezpečení, abyste se zabývali potenciálními hrozbami nebo porušeními ve vašem systému. Detekce hrozeb má také některé integrované funkce, jako je detekce injektáže SQL Injection. SQL Injection je pokus o změnu nebo ohrožení dat a poměrně běžný způsob útoku na databázovou aplikaci obecně. Detekce hrozeb spouští několik sad algoritmů, které detekují potenciální chyby zabezpečení a útoky injektáží SQL, stejně jako neobvyklé vzory přístupu k databázi (například přístup z neobvyklého umístění nebo neznámým objektem zabezpečení). Bezpečnostní pracovníci nebo jiní určení správci obdrží e-mailové oznámení, pokud je v databázi zjištěna hrozba. Každé oznámení obsahuje podrobnosti o podezřelé aktivity a doporučení, jak dále prozkoumat a zmírnit hrozbu. Informace o tom, jak zapnout detekci hrozeb, najdete v tématu [Povolení detekce hrozeb](sql-database-security-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Jak lze chránit data obecně v databázi SQL

Šifrování poskytuje silný mechanismus pro ochranu a zabezpečení citlivých dat před narušiteli. Vaše šifrovaná data jsou pro narušitele bez dešifrovacího klíče k ničemu. Proto přidává další vrstvu ochrany nad existující vrstvy zabezpečení integrované v databázi SQL. Ochrana dat v databázi SQL má dva aspekty:

- Vaše data, která jsou v klidovém stavu v souborech dat a protokolů
- Vaše data, která jsou za letu

V databázi SQL database jsou ve výchozím nastavení vaše data v klidu v datech a souborech protokolu v podsystému úložiště zcela a vždy šifrována pomocí [transparentního šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Zálohy jsou také šifrovány. S TDE nejsou vyžadovány žádné změny na straně aplikace, která je přístup k těmto datům. Šifrování a dešifrování proběhne transparentně; odtud název.
Pro ochranu citlivých dat za letu a v klidovém stavu poskytuje databáze SQL funkci nazvanou [Vždy šifrované (AE).](/sql/relational-databases/security/encryption/always-encrypted-database-engine) AE je forma šifrování na straně klienta, která šifruje citlivé sloupce v databázi (takže jsou v šifrovaném textu správcům databáze a neoprávněným uživatelům). Server obdrží šifrovaná data. Klíč pro vždy šifrované je také uložen na straně klienta, takže pouze autorizovaní klienti mohou dešifrovat citlivé sloupce. Správci serveru a dat nemohou zobrazit citlivá data, protože šifrovací klíče jsou uloženy v klientovi. AE šifruje citlivé sloupce v tabulce od začátku do konce, od neoprávněných klientů na fyzický disk. AE podporuje porovnání rovnosti dnes, takže dba můžete pokračovat dotaz šifrované sloupce jako součást jejich příkazy SQL. Vždy šifrované lze použít s různými možnostmi úložiště klíčů, jako je [například Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), úložiště certifikátů systému Windows a místní moduly zabezpečení hardwaru.

|**Vlastnosti**|**Vždy šifrované**|**Transparentní šifrování dat**|
|---|---|---|
|**Rozsah šifrování**|Konec do konce|Data k odpočinku|
|**Databázový server může přistupovat k citlivým datům**|Ne|Ano, protože šifrování je pro data v klidovém stavu|
|**Povolené operace T-SQL**|Porovnání rovnosti|K dispozici je celá plocha T-SQL|
|**Změny aplikací potřebné k použití funkce**|Minimální|Velmi minimální|
|**Rozlišovací rozlišovací schopnost**|Úroveň sloupce|úrovni databáze|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Jak lze omezit přístup k citlivým datům v databázi

Každá aplikace má určitý bit citlivých dat v databázi, která musí být chráněny před viditelné pro všechny. Někteří pracovníci v organizaci potřebují tato data zobrazit, ale ostatní by neměli být schopni zobrazit tato data. Jedním z příkladů jsou mzdy zaměstnanců. Manažer by potřeboval přístup k informacím o mzdách pro své přímé zprávy, nicméně jednotliví členové týmu by neměli mít přístup k informacím o mzdách svých vrstevníků. Dalším scénářem jsou vývojáři dat, kteří mohou komunikovat s citlivými daty během fází vývoje nebo testování, například SSN zákazníků. Tyto informace znovu nemusí být vystaveny vývojáři. V takových případech musí být citlivá data buď maskována, nebo nemusí být vůbec vystavena. SQL Database nabízí dva takové přístupy, aby se zabránilo neoprávněným uživatelům z moci zobrazit citlivá data:

[Dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md) je funkce maskování dat, která umožňuje omezit vystavení citlivých dat maskováním neprivilegovaným uživatelům v aplikační vrstvě. Definujete maskovací pravidlo, které může vytvořit vzor maskování (například zobrazit pouze poslední čtyři číslice národního ID SSN: XXX-XX-0000 a označit většinu jako X) a určit, kteří uživatelé mají být vyloučeni z pravidla maskování. Maskování se děje za chodu a pro různé kategorie dat jsou k dispozici různé maskovací funkce. Dynamické maskování dat umožňuje automaticky detekovat citlivá data v databázi a použít na ně maskování.

[Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) umožňuje řídit přístup na úrovni řádku. To znamená, že některé řádky v databázové tabulce založené na uživateli provádějícím dotaz (členství ve skupině nebo kontext spuštění) jsou skryté. Omezení přístupu se provádí na databázové vrstvě namísto v aplikační vrstvě, aby se zjednodušila logika aplikace. Můžete začít vytvořením predikátu filtru, filtrování řádků, které nejsou vystaveny a zásady zabezpečení další definování, kdo má přístup k těmto řádkům. Nakonec koncový uživatel spustí svůj dotaz a v závislosti na oprávnění uživatele, které buď zobrazit tyto omezené řádky nebo nejsou schopni zobrazit vůbec.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Jak lze spravovat šifrovací klíče v cloudu

K dispozici jsou možnosti správy klíčů pro vždy šifrované (šifrování na straně klienta) a transparentní šifrování dat (šifrování v klidovém stavu). Doporučujeme pravidelně střídat šifrovací klíče. Frekvence otáčení by měla být v souladu s interními organizačními předpisy a požadavky na dodržování předpisů.

#### <a name="transparent-data-encryption-tde"></a>Transparentní šifrování dat (TDE)

V TDE existuje hierarchie se dvěma klíči – data v každé databázi uživatele jsou šifrována symetrickým databázovým šifrovacím klíčem (DEK) jedinečného databázového klíče AES-256, který je zase šifrován serverově jedinečným asymetrickým klíčem RSA 2048. Hlavní klíč lze spravovat buď:

- Automaticky platformou - SQL Database.
- Nebo pomocí [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) jako úložiště klíčů.

Ve výchozím nastavení je hlavní klíč pro transparentní šifrování dat spravován službou SQL Database pro usnadnění. Pokud vaše organizace chce kontrolu nad hlavní klíč, je možnost použít Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) jako úložiště klíčů. Pomocí Azure Key Vault, vaše organizace převezme kontrolu nad key zřizování, střídání a řízení oprávnění. [Otočení nebo přepínání typu hlavního klíče TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) je rychlé, protože pouze znovu šifruje DEK. Pro organizace s oddělením rolí mezi zabezpečením a správou dat může správce zabezpečení zřídit klíčový materiál pro hlavní klíč TDE v úložišti Azure Key Vault a poskytnout správci úložiště klíčů Azure Key Vault, který bude používat pro šifrování v klidovém stavu na serveru. Trezor klíčů je navržen tak, aby společnost Microsoft neviděla ani neextrahoval žádné šifrovací klíče. Získáte také centralizovanou správu klíčů pro vaši organizaci.

#### <a name="always-encrypted"></a>Funkce Always Encrypted

V always encrypted je také [hierarchie se dvěma klíči](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) - sloupec citlivých dat je šifrován šifrovacím klíčem AES 256 sloupců (CEK), který je zase šifrován hlavním klíčem sloupce (CMK). Klient ovladače poskytované pro vždy šifrované nemají žádná omezení na délku CMKs. Šifrovaná hodnota CEK je uložena v databázi a CMK je uložen v úložišti důvěryhodných klíčů, jako je například Úložiště certifikátů Windows, Trezor klíčů Azure nebo modul hardwarového zabezpečení.

- Jak [CEK,](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) tak CMK lze otáčet.
- Cek rotace je velikost datové operace a může být časově náročné v závislosti na velikosti tabulek obsahujících šifrované sloupce. Proto je rozumné plánovat rotace CEK odpovídajícím způsobem.
- CMK rotace, však nenarušuje výkon databáze a lze provést s oddělenými rolemi.

Následující diagram znázorňuje možnosti úložiště klíčů pro hlavní klíče sloupců v části Vždy šifrované

![Vždy šifrované zprostředkovatele úložiště CMK](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Jak lze optimalizovat a zabezpečit provoz mezi organizací a databází SQL

Síťový provoz mezi vaší organizací a databází SQL by se obecně směroval přes veřejnou síť. Pokud se však rozhodnete tuto cestu optimalizovat a zajistit její zabezpečení, můžete se podívat na expresní trasu. Expresní trasa v podstatě umožňuje rozšířit firemní síť do platformy Azure přes privátní připojení. Tímto způsobem nepřejdete přes veřejný internet. Získáte také vyšší zabezpečení, spolehlivost a optimalizaci směrování, což znamená nižší latenci sítě a mnohem vyšší rychlosti, než byste normálně zaznamenali při přecování veřejného internetu. Pokud plánujete přenos významné části dat mezi vaší organizací a Azure, pomocí Express Route může přinést výhody nákladů. Můžete si vybrat ze tří různých modelů připojení pro připojení z vaší organizace do Azure:

- [Společné umístění cloudové burzy](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Any-to-any](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Express Route také umožňuje roztrhnout až 2x limit šířky pásma, který zakoupíte bez dalších poplatků. Je také možné konfigurovat připojení mezi oblastmi pomocí expresní trasy. Seznam poskytovatelů připojení er naleznete v [tématu: Express Route Partners a Peering Locations](../expressroute/expressroute-locations.md). Následující články popisují expresní trasu podrobněji:

- [Úvod na expresní trase](../expressroute/expressroute-introduction.md)
- [Požadavky](../expressroute/expressroute-prerequisites.md)
- [Pracovní postupy](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Je SQL Database v souladu s regulačními požadavky a jak to pomáhá s dodržováním předpisů vlastní organizace

SQL Database je kompatibilní s řadou regulačních požadavků. Chcete-li zobrazit nejnovější sadu kompatibility, které byly splněny sql database, navštivte [Microsoft Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) a přejít k podrobnostem o kompatibility, které jsou důležité pro vaši organizaci a zjistěte, jestli SQL Database je součástí kompatibilní služby Azure. Je důležité si uvědomit, že i když SQL Database může být certifikován jako kompatibilní služby, pomáhá v souladu se službou vaší organizace, ale automaticky zaručit.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Inteligentní monitorování a údržba databází po migraci

Po migraci databáze do databáze SQL budete chtít sledovat databázi (například zkontrolovat, jak se má využití prostředků nebo kontroly DBCC) a provádět pravidelnou údržbu (například znovu sestavit nebo reorganizovat indexy, statistiky atd.). Naštěstí SQL Database je inteligentní v tom smyslu, že používá historické trendy a zaznamenané metriky a statistiky proaktivně vám pomohou sledovat a udržovat databázi, takže vaše aplikace běží optimálně vždy. V některých případech může Azure SQL DB automaticky provádět úlohy údržby v závislosti na nastavení konfigurace. Monitorování databáze v databázi SQL jsou tři omezující:

- Sledování a optimalizace výkonu.
- Optimalizace zabezpečení.
- Optimalizace nákladů.

### <a name="performance-monitoring-and-optimization"></a>Monitorování a optimalizace výkonu

Pomocí přehledů výkonu dotazů můžete získat přizpůsobená doporučení pro databázové úlohy, aby vaše aplikace mohly vždy běžet na optimální úrovni. Můžete jej také nastavit tak, aby se tato doporučení automaticky použila a nemusíte se obtěžovat s prováděním úloh údržby. S Poradcem pro index můžete automaticky implementovat doporučení indexu na základě vašeho pracovního vytížení – to se nazývá automatické ladění. Doporučení se vyvíjejí s tím, jak se mění pracovní vytížení aplikace, abyste měli nejrelevantnější návrhy. Získáte také možnost ručně zkontrolovat tato doporučení a použít je podle vašeho uvážení.  

### <a name="security-optimization"></a>Optimalizace zabezpečení

SQL Database poskytuje naváděná doporučení zabezpečení, která vám pomohou zabezpečit data a detekci hrozeb pro identifikaci a vyšetřování podezřelých databázových aktivit, které mohou představovat potenciální vlákno do databáze. [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je služba pro vyhledávání a vytváření sestav databáze, která umožňuje sledovat stav zabezpečení databází ve velkém měřítku a identifikovat bezpečnostní rizika a odchýlit se od vámi definovaného směrného plánu zabezpečení. Po každém skenování je k dispozici přizpůsobený seznam kroků a nápravných skriptů a také hodnotící zpráva, kterou lze použít ke splnění požadavků na dodržování předpisů.

Pomocí Azure Security Center identifikujete doporučení zabezpečení plošně a aplikujete je jediným kliknutím.

### <a name="cost-optimization"></a>Optimalizace nákladů

Platforma Azure SQL analyzuje historii využití napříč databázemi na serveru a vyhodnotí a doporučí možnosti optimalizace nákladů. Tato analýza obvykle trvá čtrnáct dní analyzovat a vybudovat žalovatelné doporučení. Elastický bazén je jednou z takových možností. Doporučení se zobrazí na portálu jako banner:

![doporučení elastického bazénu](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Tuto analýzu můžete také zobrazit v části Poradce:

![poradce pro doporučení elastického bazénu](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Jak lze sledovat výkon a využití prostředků v databázi SQL

V databázi SQL můžete využít inteligentní přehledy platformy ke sledování výkonu a odpovídajícím způsobem naladit. Výkon a využití prostředků v databázi SQL můžete sledovat pomocí následujících metod:

#### <a name="azure-portal"></a>portál Azure

Portál Azure zobrazuje využití databáze výběrem databáze a kliknutím na graf v podokně Přehled. Graf můžete upravit tak, aby zobrazoval více metrik, včetně procenta procesoru, procenta DTU, procenta vio dat, procenta relací a procenta velikosti databáze.

![Graf monitorování](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Graf monitorování2](./media/sql-database-manage-after-migration/chart.png)

V tomto grafu můžete také konfigurovat výstrahy podle prostředků. Tyto výstrahy umožňují reagovat na podmínky prostředků pomocí e-mailu, zapisovat do koncového bodu HTTPS/HTTP nebo provést akci. Další informace naleznete v [tématu Vytvoření výstrah](sql-database-insights-alerts-portal.md).

#### <a name="dynamic-management-views"></a>Zobrazení dynamické správy

Můžete dotaz ovat v zobrazení dynamické správy [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) a vrátit tak historii statistik spotřeby zdrojů za poslední hodinu a zobrazení katalogu systému [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) a vrátit historii za posledních 14 dní.

#### <a name="query-performance-insight"></a>Query Performance Insight

[Přehled výkonu dotazu](sql-database-query-performance.md) umožňuje zobrazit historii nejvyšších dotazů náročných na prostředky a dlouhotrvajících dotazů pro konkrétní databázi. Dotazy TOP můžete rychle identifikovat podle využití prostředků, doby trvání a četnosti provádění. Můžete sledovat dotazy a zjistit regresi. Tato funkce vyžaduje, aby [úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) bylo povoleno a aktivní pro databázi.

![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL Analytics (preview) v protokolech Azure Monitoru

[Protokoly Azure Monitor](../azure-monitor/insights/azure-sql.md) uvolní a vizualizují klíčové metriky výkonu databáze Azure SQL, které podporují až 150 000 databází SQL a 5 000 elastických fondů SQL na pracovní prostor. Můžete ji použít ke sledování a přijímání oznámení. Můžete sledovat metriky sql database a elastického fondu napříč více předplatnými Azure a elastické fondy a lze použít k identifikaci problémů v každé vrstvě zásobníku aplikací.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Všiml jsem si problémů s výkonem: Jak se metodika řešení potíží s databází SQL liší od serveru SQL Server

Hlavní část technik řešení potíží, které byste použili pro diagnostiku problémů s dotazem a výkonem databáze, zůstává stejná. Po všech stejný chod SQL Server motornapě cloud. Platforma – Azure SQL DB má však integrované 'inteligence'. Může vám pomoci řešit a diagnostikovat problémy s výkonem ještě snadněji. Může také provádět některé z těchto nápravných opatření vaším jménem a v některých případech je proaktivně opravit - automaticky.

Váš přístup k řešení problémů s výkonem může výrazně těžit z použití inteligentních funkcí, jako je [například Přehled výkonu dotazu (QPI)](sql-database-query-performance.md) a [databázový poradce](sql-database-advisor.md) ve spojení, a proto se rozdíl v metodice liší v tomto ohledu – již nemusíte provádět ruční práci broušení základních podrobností, které vám mohou pomoci vyřešit problém po ruce. Platforma dělá těžkou práci za vás. Jedním z příkladů je QPI. S QPI, můžete přejít celou cestu dolů na úroveň dotazu a podívat se na historické trendy a zjistit, kdy přesně dotaz regresované. Poradce pro databázi vám poskytuje doporučení na věci, které vám mohou pomoci zlepšit celkový výkon obecně jako - chybějící indexy, uvolnění indexů, parametrizace dotazů atd.

Při řešení potíží s výkonem je důležité určit, zda se jedná pouze o aplikaci nebo databázi, která ji podporuje, což má vliv na výkon vaší aplikace. Často problém s výkonem spočívá v aplikační vrstvě. Může to být architektura nebo vzor přístupu k datům. Zvažte například, že máte chattovou aplikaci, která je citlivá na latenci sítě. V tomto případě vaše aplikace trpí, protože by bylo mnoho krátkých požadavků jít tam a zpět ("upovídaný") mezi aplikací a serverem a na přetížené síti, tyto zpáteční lety přidat rychle. Chcete-li zlepšit výkon v tomto případě, můžete použít [dávkové dotazy](sql-database-performance-guidance.md#batch-queries). Použití dávek vám ohromně pomůže, protože nyní vaše požadavky získat zpracovány v dávce; proto vám pomůže snížit latenci odezvy a zlepšit výkon aplikace.

Pokud navíc zjistíte snížení celkového výkonu databáze, můžete sledovat zobrazení dynamické správy [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) a [sys.resource_stats,](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) abyste porozuměli spotřebě procesoru, vi a paměti. Váš výkon může mít vliv, protože databáze je nedostatek prostředků. Může se být, že budete muset změnit velikost výpočetní ch a/nebo úroveň služby na základě rostoucía zmenšující se požadavky na pracovní vytížení.

Komplexní sadu doporučení pro optimalizaci problémů s výkonem naleznete v [tématu: Vyladění databáze](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Jak zajistím, že používám příslušnou úroveň služby a velikost výpočetních prostředků

SQL Database nabízí různé úrovně služeb Basic, Standard a Premium. Každá úroveň služby získáte zaručený předvídatelný výkon vázaný na tuto úroveň služby. V závislosti na vaší pracovní vytížení může mít shluky aktivity, kde využití prostředků může zasáhnout strop aktuální výpočetní velikosti, ve které se nacházejí. V takových případech je užitečné nejprve začít vyhodnocením, zda jakékoli ladění může pomoci (například přidání nebo změna indexu atd.). Pokud stále dochází k problémům s omezením, zvažte přechod na vyšší úroveň služby nebo výpočetní velikost.

|**Úroveň služeb**|**Scénáře běžných případů použití**|
|---|---|
|**Basic**|Aplikace s hrstkou uživatelů a databází, která nemá vysoké požadavky na souběžnost, škálování a výkon. |
|**Standard**|Aplikace se značnou souběžností, škálování a požadavky na výkon spolu s nízkými až středními nároky na vstupně-výkon. |
|**Premium**|Aplikace se spoustou souběžných uživatelů, vysokým procesorem/pamětí a vysokými nároky na vstupně-up. Vysoké souběžnosti, vysoká propustnost a latence citlivé aplikace můžete využít úroveň Premium. |
|||

Chcete-li se ujistit, že máte správnou výpočetní velikost, můžete sledovat spotřebu dotazů a databázových prostředků pomocí jednoho z výše uvedených způsobů v části "Jak lze sledovat výkon a využití prostředků v databázi SQL". Pokud zjistíte, že vaše dotazy / databáze jsou konzistentně spuštěny horké na PROCESORU / paměti atd. Podobně, pokud si všimnete, že i během špičky, nezdá se, že používat zdroje tolik; zvažte zmenšení z aktuální výpočetní velikosti.

Pokud máte vzor aplikace SaaS nebo scénář konsolidace databáze, zvažte použití elastického fondu pro optimalizaci nákladů. Elastický fond je skvělý způsob, jak dosáhnout konsolidace databáze a optimalizace nákladů. Další informace o správě více databází pomocí elastického fondu najdete v tématu [Správa fondů a databází](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Jak často je nutné pro databázi spouštět kontroly integrity databáze

SQL Database používá některé inteligentní techniky, které umožňují zpracovat určité třídy poškození dat automaticky a bez ztráty dat. Tyto techniky jsou integrovány do služby a jsou využívány službou v případě potřeby. V pravidelných intervalech zálohy databáze v rámci služby jsou testovány jejich obnovením a spuštěním DBCC CHECKDB na něm. Pokud se jedná o problémy, SQL Database je aktivně řeší. [Automatická oprava stránky](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) je využita k opravě stránek, které jsou poškozené nebo mají problémy s integritou dat. Stránky databáze jsou vždy ověřeny pomocí výchozího nastavení CHECKSUM, které ověřuje integritu stránky. SQL Database proaktivně monitoruje a kontroluje integritu dat databáze a pokud vzniknou problémy, řeší je s nejvyšší prioritou. Kromě těchto možností se můžete volitelně zvolit, zda chcete podle své vůle spustit vlastní kontroly integrity.  Další informace naleznete [v tématu Integrity data in SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Přesun dat po migraci

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Jak exportovat a importovat data jako soubory BACPAC z databáze SQL

- **Export:** Databázi Azure SQL můžete exportovat jako soubor BACPAC z webu Azure Portal.

   ![export databáze](./media/sql-database-export/database-export1.png)

- **Import**: Data můžete importovat také jako soubor BACPAC do databáze pomocí portálu Azure.

   ![import databáze](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Jak lze synchronizovat data mezi sql database a SQL server

Máte několik způsobů, jak toho dosáhnout:

- **[Synchronizace dat](sql-database-sync-data.md)** – tato funkce pomáhá synchronizovat data obousměrně mezi více místních databází SQL Serveru a SQL Database. Chcete-li synchronizovat s místními databázemi serveru SQL Server, je třeba nainstalovat a nakonfigurovat agenta synchronizace v místním počítači a otevřít odchozí port TCP 1433.
- **[Replikace transakcí](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – s replikací transakcí můžete synchronizovat data z místního do Azure SQL DB s místním vydavatelem a Azure SQL DB je předplatitelem. Prozatím je podporováno pouze toto nastavení. Další informace o tom, jak migrovat data z místního do Azure SQL s minimálními prostoji, najdete v [tématu Použití replikace transakcí.](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Další kroky

Další informace o [databázi SQL](sql-database-technical-overview.md).
