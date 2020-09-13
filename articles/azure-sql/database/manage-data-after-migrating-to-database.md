---
title: Správa po migraci
titleSuffix: Azure SQL Database
description: Naučte se spravovat jednotlivé a sdružené databáze po migraci na Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: 016bb1e4a0844be2a137108d673159bd041cd351
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89439771"
---
# <a name="new-dba-in-the-cloud--managing-azure-sql-database-after-migration"></a>Nový DBA v cloudu – Správa Azure SQL Database po migraci
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Přesun z tradičního samostatného prostředí s asistencí do prostředí PaaS se může zdát, že se napřed nachází v bitovém zahlcení. Jako vývojář aplikací nebo v DBA byste chtěli znát základní možnosti platformy, které vám pomohou zajistit dostupnost aplikace, výkonná, bezpečná a odolná – vždy. Tento článek se zaměřuje přesně na to. Článek stručně organizuje prostředky a poskytuje pokyny k tomu, jak nejlépe využít klíčové funkce Azure SQL Database s databázemi s jedinou a sdruženou správou a zajištěním efektivního fungování vaší aplikace a dosažení optimálních výsledků v cloudu. Typickým cílovou skupinou pro tento článek budou tyto osoby:

- Vyhodnocuje migraci svých aplikací na Azure SQL Database – modernizaci vaše aplikace.
- Jsou v procesu migrace jejich aplikací – probíhající scénář migrace.
- Nedávno dokončili migraci na Azure SQL Database – nový DBA v cloudu.

Tento článek popisuje některé základní charakteristiky Azure SQL Database jako platformu, kterou můžete snadno využít při práci s izolovanými databázemi a databázemi ve fondu v elastických fondech. Jsou to tyto:

- Monitorování databází na portálu Azure
- Provozní kontinuita a zotavení po havárii (BCDR)
- Zabezpečení a dodržování předpisů
- Inteligentní monitorování a údržba databáze
- Přesuny dat

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorování databází na portálu Azure

V [Azure Portal](https://portal.azure.com/)můžete monitorovat využití jednotlivých databází tak, že vyberete databázi a kliknete na graf **monitorování** . Zobrazí se okno **Metrika**, které můžete upravit kliknutím na **Upravit graf**. Přidejte následující metriky:

- Procento CPU
- Procento DTU
- Procento datových V/V
- Procento velikosti databáze

Až tyto metriky přidáte, můžete je dál zobrazit v grafu **monitorování** a získat další informace o okně **metriky** . Tyto čtyři metriky uvádějí průměrné využití v procentech vzhledem k hodnotě **DTU** vaší databáze. Další informace o úrovních služeb najdete v článcích o nákupním [modelu založeném na DTU](service-tiers-dtu.md) a na [Vcore modelu](service-tiers-vcore.md) .  

![Monitorování výkonu databáze v rámci úrovně služeb](./media/manage-data-after-migrating-to-database/sqldb_service_tier_monitoring.png)

Můžete také nastavit upozornění na výkonové metriky. Klikněte na tlačítko **Přidat upozornění** v okně **Metrika**. Nastavte upozornění podle pokynů průvodce. Můžete určit, zda chcete být upozorněni na překročení zadané prahové hodnoty, nebo naopak když metrika poklesne pod zadanou mez.

Například pokud očekáváte nárůst zatížení databáze, můžete nastavit e-mailové upozornění pro případ, že databáze překročí 80 % kterékoli výkonové metriky. Můžete ji použít jako počáteční upozornění, pokud budete muset přejít na další nejvyšší výpočetní velikost.

Metrika výkonu vám také pomůže určit, jestli můžete snížit množství výpočetní velikosti. Předpokládejme, že používáte databáze S2 v úrovni Standard a všechny metriky ukazují, že databáze v průměru nevyužívá více než 10 % dostupného výkonu. Je pravděpodobné, že databáze bude dobře fungovat i v úrovni Standard S1. Mějte ale na paměti, že se špička nebo kolísání před tím, než se rozhodnete přejít na nižší výpočetní velikost.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Provozní kontinuita a zotavení po havárii (BCDR)

Provozní kontinuita a možnosti zotavení po havárii umožňují v případě havárie v podniku pokračovat v práci, jako obvykle. Havárie by mohla být událost na úrovni databáze (například někdo omylem vyřazuje zásadní tabulku) nebo událost na úrovni datového centra (oblastní pohromě, třeba tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Návody vytváření a Správa záloh v SQL Database

Nevytváříte zálohy na Azure SQL Database a je to proto, že je nemusíte mít. SQL Database automaticky zálohuje databáze za vás, takže už se nemusíte starat o plánování, vytváření a správu záloh. Tato platforma má každý týden úplnou zálohu, rozdílové zálohování každých pět minut a zálohování protokolu každých 5 minut, aby se zajistilo, že zotavení po havárii bude efektivní a že se data budou minimální. První úplná záloha se stane hned po vytvoření databáze. Tyto zálohy jsou k dispozici po určitou dobu s názvem "doba uchování" a liší se podle zvolené úrovně služeb. SQL Database poskytuje možnost obnovení do libovolného bodu v čase v rámci této doby uchování pomocí funkce [Obnovení bodu v čase (PITR)](recovery-using-backups.md#point-in-time-restore).

|Úroveň služeb|Doba uchování ve dnech|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Kromě toho vám funkce [dlouhodobá doba uchovávání (LTR)](long-term-retention-overview.md) umožňuje ukládat soubory do záložních souborů po delší dobu, a to po dobu až 10 let a obnovovat data z těchto záloh v libovolném bodě v daném období. Kromě toho jsou zálohy databáze uchovávány v geograficky replikovaném úložišti, aby se zajistila odolnost z oblasti regionálního pohromě. Tyto zálohy můžete v libovolné oblasti Azure kdykoli obnovit v době uchování. Podívejte se na [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Návody zajistit kontinuitu podnikových prostředí v případě havárie na úrovni datacentra nebo regionálního pohromě

Vzhledem k tomu, že zálohy databáze jsou uloženy v geograficky replikovaném úložišti, aby bylo zajištěno, že v případě regionální havárie můžete zálohu obnovit do jiné oblasti Azure. Označuje se jako geografické obnovení. RPO (cíl bodu obnovení) pro toto je obecně < 1 hodina a ERT (odhadovaná doba obnovení – několik minut do hodin).

Pro klíčové databáze Azure SQL Database nabídky, aktivní geografická replikace. To v podstatě znamená, že vytvoří geograficky replikovanou sekundární kopii původní databáze v jiné oblasti. Například pokud je vaše databáze zpočátku hostovaná v Azure Západní USA oblasti a chcete mít regionální odolnost proti havárii. Vytvořili jste aktivní geografickou repliku databáze v Západní USA k tomu, abyste se vysloví Východní USA. Když se Calamity zavede na Západní USA, můžete převzít služby Východní USA oblasti. Konfigurace v rámci skupiny automatického převzetí služeb při selhání je ještě lepší, protože tím zajistíte, že se databáze automaticky převezme k sekundárnímu objektu v Východní USA v případě havárie. RPO pro toto je < 5 sekund a ERT < 30 sekund.

Pokud není nakonfigurovaná skupina automatického převzetí služeb při selhání, musí vaše aplikace aktivně monitorovat po havárii a iniciovat převzetí služeb při selhání sekundárním systémem. V různých oblastech Azure můžete vytvořit až 4 takové aktivní geografické repliky. To je ještě lepší. K těmto sekundárním místním geografickým replikám můžete přistupovat taky pro přístup jen pro čtení. To je velmi užitečné, pokud chcete snížit latenci pro scénář geograficky distribuované aplikace.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Jak se změní plán zotavení po havárii z místního prostředí na SQL Database

V souhrnu SQL Server instalační program vyžaduje aktivně spravovat dostupnost pomocí funkcí, jako je Clustering s podporou převzetí služeb při selhání, zrcadlení databáze, replikace transakcí nebo přesouvání protokolů a údržba a Správa záloh, aby se zajistila Kontinuita podnikových prostředí. S SQL Database platforma je spravuje za vás, takže se můžete soustředit na vývoj a optimalizaci databázové aplikace a nedělejte si starosti se správou havárií. Můžete mít nakonfigurované plány zálohování a zotavení po havárii a pracovat s několika kliknutími na Azure Portal (nebo několika příkazy pomocí rozhraní API PowerShellu).

Další informace o zotavení po havárii najdete v tématu: [Azure SQL Database zotavení po havárii 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Zabezpečení a dodržování předpisů

SQL Database zabezpečení a ochrany osobních údajů velmi vážně. Zabezpečení v rámci SQL Database je k dispozici na úrovni databáze a na úrovni platformy a je nejlépe porozumět při kategorizaci do několika vrstev. V každé vrstvě se dostanete k řízení a získáte optimální zabezpečení pro vaši aplikaci. Vrstvy jsou:

- Ověřování identity & ([ověřování SQL a Azure Active Directory [Azure AD] ověřování](logins-create-manage.md)).
- Sledování aktivity ([auditování](../../azure-sql/database/auditing-overview.md) a [detekce hrozeb](threat-detection-configure.md)).
- Ochrana skutečných dat ([transparentní šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) a [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Řízení přístupu k citlivým a privilegovaným datům ([zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) a [dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Security Center](https://azure.microsoft.com/services/security-center/) nabízí centralizovanou správu zabezpečení napříč úlohami spuštěnými v Azure, v místním prostředí i v jiných cloudech. Můžete si prohlédnout, jestli je základní SQL Database ochrana, jako je [auditování](../../azure-sql/database/auditing-overview.md) a [transparentní šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) , nakonfigurovaná na všech prostředcích, a vytvořit zásady na základě vašich požadavků.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Jaké metody ověřování uživatelů jsou nabízeny v SQL Database

V SQL Database jsou k dispozici dvě metody ověřování:

- [Ověřování Azure Active Directory](authentication-aad-overview.md)
- [Ověřování SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

Tradiční ověřování systému Windows není podporováno. Azure Active Directory (Azure AD) je centralizovaná služba pro správu identit a přístupu. Díky tomu můžete snadno poskytnout přístup s jednotným přihlašováním (SSO) všem pracovníkům ve vaší organizaci. To znamená, že přihlašovací údaje se sdílejí napříč všemi službami Azure pro jednodušší ověřování. 

Azure AD podporuje [azure Multi-Factor Authentication](authentication-mfa-ssms-overview.md) a [pár kliknutí](../../active-directory/hybrid/how-to-connect-install-express.md) na Azure AD se dá integrovat do služby Windows Server Active Directory. Ověřování SQL funguje stejně, jako byste ji používali v minulosti. Zadejte uživatelské jméno a heslo a můžete ověřovat uživatele na všech databázích na daném serveru. Tato možnost také umožňuje SQL Database a Azure synapse Analytics (dříve SQL Data Warehouse) k poskytování Multi-Factor Authentication a uživatelských účtů hostů v doméně služby Azure AD. Pokud již máte místní službu Active Directory, můžete federovat adresář s Azure Active Directory pro rozšiřování adresáře do Azure.

|**Pokud...**|**SQL Database/Azure synapse Analytics**|
|---|---|
|Raději nepoužívejte Azure Active Directory (Azure AD) v Azure.|Použít [ověřování SQL](security-overview.md)|
|Služba AD se používá v místní SQL Server.|[FEDEROVAT AD s Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md)a používejte ověřování Azure AD. Díky tomu můžete použít jednotné přihlašování.|
|Je potřeba vyhovět Multi-Factor Authentication|Vyžadovat Multi-Factor Authentication jako zásadu prostřednictvím [podmíněného přístupu Microsoft](conditional-access-configure.md)a použít [univerzální ověřování Azure AD s podporou Multi-Factor Authentication](authentication-mfa-ssms-overview.md).|
|Mít účty hostů z účtů Microsoft (live.com, outlook.com) nebo jiné domény (gmail.com).|Využijte [Azure AD Universal Authentication](authentication-mfa-ssms-overview.md) v SQL Database/datovém skladu, který využívá [spolupráci Azure AD B2B](../../active-directory/b2b/what-is-b2b.md).|
|Přihlášení k systému Windows pomocí přihlašovacích údajů Azure AD ze federované domény|Použijte [integrované ověřování Azure AD](authentication-aad-configure.md).|
|Přihlášení k systému Windows pomocí přihlašovacích údajů z domény, která není federované s Azure|Použijte [integrované ověřování Azure AD](authentication-aad-configure.md).|
|Musí mít služby střední vrstvy, které se musí připojit k SQL Database nebo Azure synapse Analytics.|Použijte [integrované ověřování Azure AD](authentication-aad-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Omezení Návody nebo řízení přístupu k databázi v síti

K dispozici je více postupů, které můžete použít k dosažení optimální organizace pro připojení pro vaši aplikaci.

- Pravidla brány firewall
- Koncové body služeb virtuální sítě
- Vyhrazené IP adresy

#### <a name="firewall"></a>Brána firewall

Brána firewall zabraňuje přístup k vašemu serveru z externí entity tím, že umožňuje přístup k vašemu serveru jenom konkrétním entitám. Ve výchozím nastavení se všechna připojení k databázím v rámci serveru nepovolují, s výjimkou (optionally7), která přicházejí z jiných služeb Azure. Pomocí pravidla brány firewall můžete otevřít přístup k serveru pouze k entitám (například k počítači vývojáře), který schválíte, povolením IP adresy tohoto počítače přes bránu firewall. Umožňuje taky zadat rozsah IP adres, pro které chcete povolit přístup k serveru. Například IP adresy počítače pro vývojáře ve vaší organizaci můžete přidat najednou zadáním rozsahu na stránce nastavení brány firewall.

Pravidla brány firewall můžete vytvořit na úrovni serveru nebo na úrovni databáze. Pravidla brány firewall protokolu IP na úrovni serveru se dají vytvořit pomocí Azure Portal nebo pomocí SSMS. Další informace o tom, jak nastavit pravidlo brány firewall na úrovni serveru a databáze, najdete v tématu: [Vytvoření pravidel brány firewall protokolu IP v SQL Database](secure-database-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Koncové body služby

Ve výchozím nastavení je vaše databáze nakonfigurovaná na "umožňuje službám Azure přístup k serveru" – to znamená, že se každý virtuální počítač v Azure může pokusit připojit k vaší databázi. Tyto pokusy se stále musí ověřit. Pokud ale nechcete, aby vaše databáze byla dostupná pro jakékoli IP adresy Azure, můžete zakázat možnost Povolit službám Azure přístup k serveru. Kromě toho můžete nakonfigurovat [koncové body služby virtuální](vnet-service-endpoint-rule-overview.md)sítě.

Koncové body služby (SE) umožňují zveřejnit důležité prostředky Azure jenom pro vaši vlastní privátní virtuální síť v Azure. Tím byste v podstatě vyloučili veřejný přístup k vašim prostředkům. Provoz mezi vaší virtuální sítí do Azure zůstane v páteřní síti Azure. Bez SE vám nedostalo směrování paketů vynucené tunelování. Vaše virtuální síť vynutí internetový provoz do vaší organizace a provoz služeb Azure tak, aby přešel přes stejnou trasu. S koncovými body služby je můžete optimalizovat, protože tok paketů je přímo z vaší virtuální sítě do služby v páteřní síti Azure.

![Koncové body služby virtuální sítě](./media/manage-data-after-migrating-to-database/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Vyhrazené IP adresy

Další možností je zřídit [rezervované IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) adresy pro vaše virtuální počítače a přidat do nastavení brány firewall serveru konkrétní IP adresy virtuálních počítačů. Díky přiřazování rezervovaných IP adres ušetříte potíže s tím, že budete muset aktualizovat pravidla brány firewall se měnícími se IP adresami.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>K jakému portu se připojím SQL Database

Port 1433. SQL Database komunikuje prostřednictvím tohoto portu. Pokud se chcete připojit z podnikové sítě, musíte do nastavení brány firewall ve vaší organizaci přidat odchozí pravidlo. Jako vodítko se vyhněte vystavení portu 1433 mimo hranici Azure.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Jak můžu monitorovat a regulovat činnost na mém serveru a databázi v SQL Database

#### <a name="sql-database-auditing"></a>SQL Database Auditing

Pomocí SQL Database můžete zapnout auditování a sledovat události databáze. [SQL Database auditování](../../azure-sql/database/auditing-overview.md) zaznamenává události databáze a zapisuje je do souboru protokolu auditu ve vašem účtu Azure Storage. Auditování je užitečné hlavně v případě, že máte v úmyslu získat přehled o potenciálních porušení zabezpečení a zásad, zachovat dodržování předpisů atd. Umožňuje definovat a konfigurovat určité kategorie událostí, které považujete za audit a na základě toho, že můžete získat předem nakonfigurované sestavy a řídicí panel, abyste získali přehled o událostech, ke kterým dochází v databázi. Tyto zásady auditování můžete použít buď na úrovni databáze, nebo na úrovni serveru. Návod, jak zapnout auditování pro server nebo databázi, najdete v tématu: [Povolení auditování SQL Database](secure-database-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Detekce hrozeb

Díky [detekci hrozeb](threat-detection-configure.md)získáte možnost reagovat na zabezpečení nebo porušení zásad zjištěné auditováním velmi snadno. Nemusíte být odborníkem na zabezpečení, abyste mohli řešit potenciální hrozby nebo porušení v systému. Detekce hrozeb má také některé integrované možnosti, jako je detekce injektáže SQL. Injektáže SQL je pokus o změnu nebo zabezpečení dat a poměrně běžný způsob útoku na databázi aplikace obecně. Detekce hrozeb spouští několik sad algoritmů, které zjišťují potenciální chyby zabezpečení a útoky prostřednictvím injektáže SQL, a také vzory přístupu k databázi neobvyklé (například přístup z neobvyklého umístění nebo neznámého objektu zabezpečení). Bezpečnostní důstojníci nebo jiní určení správci obdrží e-mailové oznámení, pokud se v databázi zjistí hrozba. Každé oznámení poskytuje podrobné informace o podezřelé aktivitě a doporučeních o tom, jak tuto hrozbu dále prozkoumat a zmírnit. Informace o tom, jak zapnout detekci hrozeb, najdete v tématu: [Povolení detekce hrozeb](secure-database-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Návody chránit moje data obecně na SQL Database

Šifrování poskytuje silný mechanizmus pro ochranu citlivých dat před narušiteli a jejich zabezpečení. Šifrovaná data nejsou pro narušitele bez dešifrovacího klíče k dispozici. Proto přidá další vrstvu ochrany nad stávající vrstvy zabezpečení integrované v SQL Database. Existují dvě aspekty ochrany vašich dat v SQL Database:

- Data, která jsou v klidovém umístění v souborech dat a protokolů
- Data, která jsou v letadlu

Ve výchozím nastavení jsou vaše neaktivní data a soubory protokolů v subsystému úložiště v SQL Database zcela a vždy šifrované prostřednictvím [transparentní šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Vaše zálohy jsou také šifrované. V TDE se na straně aplikace nevyžadují žádné změny, které mají přístup k těmto datům. Šifrování a dešifrování je transparentní; Proto název.
V případě ochrany citlivých dat v letadlech a v klidovém prostředí SQL Database poskytuje funkci nazvanou [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE je forma šifrování na straně klienta, která šifruje citlivé sloupce v databázi (takže jsou v šifrovaném textu u správců databáze a neautorizovaných uživatelů). Server obdrží zašifrovaná data, která mají začít. Klíč pro Always Encrypted je také uložen na straně klienta, takže pouze oprávnění klienti mohou dešifrovat citlivé sloupce. Správci serveru a dat nemohou zobrazit citlivá data, protože šifrovací klíče jsou uloženy v klientovi. AE šifruje citlivé sloupce v tabulce na konci, od neautorizovaných klientů po fyzický disk. AE v současné době podporuje porovnání rovnosti, takže specializující může v rámci svých příkazů SQL nadále dotazovat šifrované sloupce. Always Encrypted lze použít s nejrůznějšími možnostmi úložiště klíčů, jako jsou [Azure Key Vault](always-encrypted-azure-key-vault-configure.md), úložiště certifikátů Windows a místní moduly hardwarového zabezpečení.

|**Vlastnosti**|**Funkce Always Encrypted**|**Transparentní šifrování dat**|
|---|---|---|
|**Rozsah šifrování**|Od začátku do konce|Data na REST|
|**Server má přístup k citlivým datům**|No|Ano, protože šifrování je pro neaktivní neaktivní data|
|**Povolené operace T-SQL**|Porovnání rovnosti|Dostupná je celá oblast T-SQL Surface.|
|**Změny aplikací, které jsou nutné k použití této funkce**|Minimální|Velmi minimální|
|**Členitost šifrování**|Úroveň sloupce|úrovni databáze|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Jak můžu omezit přístup k citlivým datům v databázi

Každá aplikace má určitou bitovou část citlivých dat v databázi, kterou je třeba chránit před tím, než bude viditelná pro všechny. Někteří zaměstnanci v organizaci si musí tato data zobrazit, ale ostatní by si tato data nemohli zobrazit. Jedním z příkladů je mzdy zaměstnanců. Vedoucí bude potřebovat přístup k údajům o mzdě pro své přímé sestavy, ale jednotliví členové týmu by neměli mít přístup k informacím o mzdě jejich partnerských vztahů. Dalším scénářem jsou vývojáři dat, kteří můžou v průběhu vývoje nebo testování spolupracovat s citlivými údaji, například čísla sociálního zabezpečení zákazníky. Tyto informace nemusí být k dispozici vývojářům. V takových případech musí být citlivá data buď maskována, nebo nemusí být vystavena vůbec. SQL Database nabízí dva takové přístupy, které brání neoprávněným uživatelům zobrazovat citlivá data:

[Maskování dynamických dat](dynamic-data-masking-overview.md) je funkce maskování dat, která umožňuje omezit vystavení citlivých dat jejich maskováním uživatelům bez privilegovaných oprávnění na aplikační vrstvě. Definujete pravidlo maskování, které může vytvořit masku maskování (například pokud chcete zobrazit jenom poslední čtyři číslice RODNÉho IDENTIFIKÁTORu: XXX-XX-0000 a označit většinu z nich jako xs) a určit, kteří uživatelé mají být z pravidla maskování vyloučeni. Maskování probíhá průběžně a k dispozici jsou různé funkce maskování pro různé kategorie dat. Dynamické maskování dat umožňuje automaticky rozpoznávat citlivá data ve vaší databázi a použít na ni maskování.

[Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) vám umožňuje řídit přístup na úrovni řádků. To znamená, že určité řádky v tabulce databáze založené na uživateli, který spouští dotaz (členství ve skupině nebo kontext spuštění), jsou skryté. Omezení přístupu se provádí na úrovni databáze místo v aplikační vrstvě, aby se zjednodušila logika vaší aplikace. Začnete vytvořením predikátu filtru, odfiltrování řádků, které nejsou vystavené, a zásad zabezpečení další definování, kdo má přístup k těmto řádkům. Nakonec koncový uživatel spustí dotaz a v závislosti na oprávnění uživatele buď zobrazí tyto omezené řádky, nebo je nedokáže vůbec zobrazit.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Návody Správa šifrovacích klíčů v cloudu

K dispozici jsou možnosti správy klíčů pro Always Encrypted (šifrování na straně klienta) i transparentní šifrování dat (šifrování v klidovém umístění). Doporučuje se pravidelně střídat šifrovací klíče. Frekvence rotace by měla být v souladu s vašimi interními předpisy organizace a požadavky na dodržování předpisů.

#### <a name="transparent-data-encryption-tde"></a>Transparentní šifrování dat (TDE)

V TDE existuje dvě klíčová hierarchie – data v jednotlivých uživatelských databázích jsou šifrovaná pomocí symetrického šifrovacího klíče databáze AES-256 (klíč DEK), který je zase zašifrovaný serverem a jedinečným asymetrickým hlavním klíčem RSA 2048. Hlavní klíč se dá spravovat buď takto:

- Automaticky SQL Database platformou.
- Nebo pomocí [Azure Key Vault](always-encrypted-azure-key-vault-configure.md) jako úložiště klíčů.

Ve výchozím nastavení je hlavní klíč pro transparentní šifrování dat spravovaný službou SQL Database pro usnadnění práce. Pokud by vaše organizace měla kontrolu nad hlavním klíčem, je k dispozici možnost použití Azure Key Vault] (Always-Encrypted-Azure-Key-trezor-configure.md) jako úložiště klíčů. Pomocí Azure Key Vault vaše organizace předpokládá kontrolu nad správou klíčů, otočením a řízením oprávnění. [Rotace nebo přepínání typu hlavního klíče TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) je rychlé, protože klíč DEK ho znovu zašifruje. V organizacích, které mají oddělení rolí mezi zabezpečením a správou dat, může správce zabezpečení zřídit klíčový materiál pro hlavní klíč TDE ve službě Azure Key Vault a poskytnout Azure Key Vault identifikátor klíče pro správce databáze, který se použije pro šifrování v klidovém provozu serveru. Key Vault je navržený tak, že Microsoft nevidí ani neextrahuje žádné šifrovací klíče. Získáte také centralizovanou správu klíčů pro vaši organizaci.

#### <a name="always-encrypted"></a>Funkce Always Encrypted

V Always Encrypted existuje také [dvě klíčová hierarchie](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) – sloupec citlivých dat je zašifrovaný šifrovacím klíčem AES 256-Column (cek), který je zase zašifrovaný pomocí hlavního klíče sloupce (CMK). Ovladače klienta, které jsou k dispozici pro Always Encrypted, nemají žádná omezení délky CMKs. Šifrovaná hodnota CEK je uložena v databázi a CMK je uložena v úložišti důvěryhodných klíčů, jako je například úložiště certifikátů systému Windows, Azure Key Vault nebo modul hardwarového zabezpečení.

- [Cek i CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) lze otáčet.
- CEK rotace je velikost datové operace a může být časově náročná v závislosti na velikosti tabulek obsahujících šifrované sloupce. Proto je vhodné naplánovat rotace CEK.
- CMK rotace ale nekoliduje s výkonem databáze a je možné ji provést s oddělenými rolemi.

Následující diagram znázorňuje možnosti úložiště klíčů pro hlavní klíče sloupce v Always Encrypted

![CMK Zprostředkovatelé úložiště Always Encrypted](./media/manage-data-after-migrating-to-database/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Jak můžu optimalizovat a zabezpečit provoz mezi mojí organizací a SQL Database

Síťový provoz mezi vaší organizací a SQL Database by byl obecně směrován přes veřejnou síť. Pokud se ale rozhodnete tuto cestu optimalizovat a zvýšit zabezpečení, můžete se podívat do Azure ExpressRoute. ExpressRoute v podstatě umožňuje, aby se vaše firemní síť rozšířila na platformu Azure prostřednictvím privátního připojení. Provedete to tak, že nebudete přecházet přes veřejný Internet. Získáte také vyšší úroveň zabezpečení, spolehlivosti a směrování, která překládá na nižší latenci sítě a mnohem rychlejší, než byste normálně procházíte přes veřejný Internet. Pokud plánujete přenos významného bloku dat mezi vaší organizací a Azure, můžete využít výhod služby ExpressRoute. Pro připojení z vaší organizace k Azure si můžete vybrat ze tří různých modelů připojení:

- [Společné umístění cloudového systému Exchange](../../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Mezi libovolnými prostředky](../../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-Point](../../expressroute/expressroute-connectivity-models.md#Ethernet)

ExpressRoute také umožňuje zvýšit až dvojnásobek limitu šířky pásma, který zakoupíte bez dalších poplatků. Je také možné nakonfigurovat připojení mezi oblastmi pomocí ExpressRoute. Pokud chcete zobrazit seznam zprostředkovatelů připojení ExpressRoute, přečtěte si téma: [partneři ExpressRoute a umístění partnerských vztahů](../../expressroute/expressroute-locations.md). Následující články popisují Express Route podrobněji:

- [Úvod do expresní trasy](../../expressroute/expressroute-introduction.md)
- [Požadavky](../../expressroute/expressroute-prerequisites.md)
- [Pracovní postupy](../../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Je SQL Database kompatibilní se všemi zákonnými požadavky a jak to může pomáhat s dodržováním předpisů moje vlastní organizace.

SQL Database je kompatibilní s řadou regulativního compliancies. Pokud chcete zobrazit nejnovější sadu compliancies, která byla splněna SQL Database, navštivte [Centrum zabezpečení Microsoftu](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) a přejděte k podrobnostem o compliancies, které jsou pro vaši organizaci důležité, aby se zjistilo, jestli je SQL Database zahrnutá v odpovídajících službách Azure. Je důležité si uvědomit, že i když SQL Database může být certifikovaný jako vyhovující služba, pomáhá s dodržováním služeb vaší organizace, ale nezaručuje to automaticky.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Inteligentní monitorování a údržba databáze po migraci

Po migraci databáze na SQL Database budete chtít monitorovat vaši databázi (například zkontrolovat, jak je využití prostředků jako nebo kontroly DBCC), a provádět pravidelnou údržbu (například znovu sestavit nebo reorganizovat indexy, statistiky atd.). Naštěstí je SQL Database inteligentní v tom smyslu, že používá historické trendy a zaznamenané metriky a statistiky, které vám proaktivně pomůžou monitorovat a spravovat vaši databázi, aby se vaše aplikace spouštěla optimálně vždy. V některých případech může Azure SQL Database automaticky provádět úlohy údržby v závislosti na nastavení konfigurace. Vaše databáze je možné monitorovat v SQL Database třemi omezujícími vlastnostmi:

- Sledování a optimalizace výkonu.
- Optimalizace zabezpečení.
- Optimalizace nákladů.

### <a name="performance-monitoring-and-optimization"></a>Sledování a optimalizace výkonu

Díky dotazům na výkon dotazů můžete získat navržená doporučení pro vaši databázovou úlohu, aby vaše aplikace mohly běžet na optimální úrovni – vždy. Můžete ji také nastavit tak, aby se tato doporučení automaticky uplatnila a nemuseli bother provádět úlohy údržby. Pomocí SQL Database Advisor můžete automaticky implementovat doporučení indexu na základě vaší úlohy – to se nazývá automatické ladění. Doporučení se budou vyvíjet jako úlohy vaší aplikace, aby vám poskytovaly nejrelevantnější návrhy. Získáte také možnost ručně zkontrolovat tato doporučení a použít je podle svého uvážení.  

### <a name="security-optimization"></a>Optimalizace zabezpečení

SQL Database poskytuje užitečná doporučení zabezpečení, která vám pomohou zabezpečit vaše data a detekci hrozeb pro identifikaci a vyšetřování podezřelých databázových aktivit, které mohou představovat potenciální vlákno databáze. [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je služba prohledávání databáze a vytváření sestav, která umožňuje monitorovat stav zabezpečení vašich databází ve velkém měřítku a identifikovat bezpečnostní rizika a oddělit je od standardních hodnot zabezpečení definovaných vámi. Po každé kontrole je k dispozici přizpůsobený seznam kroků, které lze provést s akcemi, a také sestavu posouzení, která může být použita k tomu, aby bylo možné splnit požadavky na dodržování předpisů.

Pomocí Azure Security Center můžete určit doporučení zabezpečení na celé desce a použít je jediným kliknutím.

### <a name="cost-optimization"></a>Optimalizace nákladů

Platforma Azure SQL analyzuje historii využití napříč databázemi na serveru a vyhodnocuje a doporučuje možnosti optimalizace nákladů. Tato analýza obvykle využívá fortnight k analýze a sestavení doporučení, která lze zpracovat. Elastický fond je jedna z těchto možností. Doporučení se zobrazí na portálu jako banner:

![doporučení elastického fondu](./media/manage-data-after-migrating-to-database/elastic-pool-recommendations.png)

Tuto analýzu můžete zobrazit také v části poradce.

![doporučení elastického fondu – poradce](./media/manage-data-after-migrating-to-database/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Návody monitorovat využití výkonu a prostředků v SQL Database

V SQL Database můžete využít inteligentní přehledy platformy, abyste mohli monitorovat výkon a odpovídajícím způsobem ho optimalizovat. Využití výkonu a prostředků v SQL Database můžete monitorovat pomocí následujících metod:

#### <a name="azure-portal"></a>portál Azure

Azure Portal zobrazuje využití databáze tak, že se vybere databáze a klikne na graf v podokně Přehled. Graf můžete upravit tak, aby zobrazoval více metrik, včetně procenta využití procesoru, procenta DTU, procentuální hodnoty v/v, procentu relací a procenta velikosti databáze.

![Graf monitorování](./media/manage-data-after-migrating-to-database/monitoring-chart.png)

![Monitorování CHART2](./media/manage-data-after-migrating-to-database/chart.png)

Z tohoto grafu můžete také nakonfigurovat výstrahy podle prostředku. Tyto výstrahy umožňují reagovat na podmínky prostředků s použitím e-mailu, zapsat do koncového bodu HTTPS/HTTP nebo provést akci. Další informace najdete v tématu o [vytváření výstrah](alerts-insights-configure-portal.md).

#### <a name="dynamic-management-views"></a>Zobrazení dynamické správy

Můžete zadat dotaz na zobrazení dynamické správy [Sys. dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) , které vrátí historii statistik spotřeby prostředků za poslední hodinu a zobrazení systémového katalogu [Sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , které vrátí historii za posledních 14 dní.

#### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](query-performance-insight-use.md) vám umožní zobrazit historii nejdůležitějších dotazů využívajících prostředky a dlouhotrvajících dotazů pro konkrétní databázi. Můžete rychle identifikovat hlavní dotazy podle využití prostředků, doby trvání a četnosti provádění. Můžete sledovat dotazy a detekovat regresi. Tato funkce vyžaduje, aby [úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) bylo povolené a aktivní pro databázi.

![Query Performance Insight](./media/manage-data-after-migrating-to-database/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL Analytics (Preview) v protokolech Azure Monitor

[Protokoly Azure monitor](../../azure-monitor/insights/azure-sql.md) umožňují shromažďovat a vizualizovat klíčovou Azure SQL Databaseou metriku výkonu, podporovat až 150 000 databází a 5 000 elastických fondů SQL na pracovní prostor. Můžete ji použít k monitorování a přijímání oznámení. Metriky SQL Database a elastického fondu můžete monitorovat v několika předplatných Azure a elastických fondech a lze je použít k identifikaci problémů v každé vrstvě aplikačního zásobníku.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Všímáte problémy s výkonem: jak se SQL Database metodologie řešení potíží liší od SQL Server

Hlavní část technik řešení potíží, kterou byste použili pro diagnostiku problémů s výkonem dotazů a databází, zůstávají stejné. Poté, co všechny stejné databázové stroje vyvykonávají výkon cloudu. Azure SQL Database platforem ale vytvořila v "Intelligence". Může vám pomoct vyřešit a diagnostikovat problémy s výkonem ještě snadněji. Může také provádět některé z těchto opravných akcí vaším jménem a v některých případech aktivně opravovat – automaticky.

Váš přístup k problémům s výkonem se může významně využít při použití inteligentních funkcí, jako jsou [Query Performance Insight (QPI)](query-performance-insight-use.md) a [Database Advisor](database-advisor-implement-performance-recommendations.md) ve spojení, takže rozdíl v metodologii se v tomto ohledu liší – už nemusíte provádět ruční práci na základě základních podrobností, které vám můžou pomoct vyřešit problém. Platforma za vás funguje. Jeden příklad, který je QPI. Pomocí QPI můžete procházet vše až na úroveň dotazu a podívat se na historické trendy a zjistit, kdy se dotaz přesně vrátí. Database Advisor poskytuje doporučení pro věci, které vám mohou pomoci zlepšit celkový výkon v obecných případech – chybějící indexy, vyřazování indexů, parametrizace dotazů atd.

Při řešení potíží s výkonem je důležité určit, zda je to pouze aplikace nebo databáze, která má vliv na výkon aplikace. Problém s výkonem se často nachází v aplikační vrstvě. Může se jednat o architekturu nebo vzor přístupu k datům. Zvažte například, že máte aplikaci Chat, která je citlivá na latenci sítě. V takovém případě vaše aplikace utrpí, protože by došlo k velkému počtu krátkých požadavků ("konverzace") mezi aplikací a serverem a v zahlcené síti, takže se tyto přenosy rychle přidávají. Pro zlepšení výkonu v tomto případě můžete použít [dávkové dotazy](performance-guidance.md#batch-queries). Použití dávek vám pomůže se obrovským vzhledem k tomu, že teď se vaše požadavky zpracovávají v dávce. Proto vám pomůže vyjímat latenci zpětného odezvy a zvýšit výkon vaší aplikace.

Pokud si navíc všimnete snížení celkového výkonu vaší databáze, můžete monitorovat zobrazení dynamické správy [Sys. dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) a [Sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , abyste porozuměli využití procesoru, vstupně-výstupních operací a paměti. Váš výkon může mít vliv na to, že vaše databáze nedostatek prostředky. Může se stát, že budete muset změnit velikost výpočetní kapacity nebo úroveň služby na základě požadavků na rostoucí a zmenšení zatížení.

Komplexní sadu doporučení pro ladění problémů s výkonem najdete v tématu: [vyladění databáze](performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Návody se ujistěte, že používáte příslušnou úroveň služby a výpočetní velikost

SQL Database nabízí různé úrovně služeb Basic, Standard a Premium. Na každé úrovni služby získáte zaručený předvídatelný výkon, který je svázán s danou úrovní služeb. V závislosti na vašich úlohách můžete mít shluky aktivity, kde využití prostředků může dosáhnout stropu aktuální velikosti výpočtů, ke které jste v. V takových případech je vhodné nejdřív začít tím, že vyhodnotí, jestli může nějaké ladění pomoct (například přidání nebo změna indexu atd.). Pokud stále dochází k problémům s omezením, zvažte přechod na vyšší úroveň služby nebo výpočetní velikost.

|**Úroveň služeb**|**Běžné scénáře použití**|
|---|---|
|**Basic**|Aplikace s uživateli několik a databází, které nemají vysoké požadavky na souběžnost, škálování a výkon. |
|**Standard**|Aplikace se značnými požadavky na souběžnost, škálování a výkon, které jsou v případě požadavků s nízkým až středním vstupem/výstupem. |
|**Premium**|Aplikace s velkým počtem souběžných uživatelů, vysokým PROCESORem/pamětí a vysokými nároky na vstupně-výstupní operace. Vysoká úroveň souběžnosti, vysoké propustnosti a aplikace citlivé na latenci můžou využívat úrovně Premium. |
|||

Aby se zajistila správná velikost výpočetní kapacity, můžete monitorovat spotřebu prostředků dotazu a databáze jedním z výše uvedených způsobů v tématu "Návody monitorovat výkon a využití prostředků v SQL Database". Pokud zjistíte, že dotazy nebo databáze jsou konzistentně spuštěné na procesoru nebo paměti atd. můžete zvážit horizontální navýšení kapacity až na vyšší výpočetní velikost. Podobně platí, že pokud si všimněte, že i během špičky, nebudete pravděpodobně prostředky používat, a to podobně. Zvažte snížení kapacity z aktuální výpočetní velikosti.

Pokud máte vzor aplikace SaaS nebo scénář konsolidace databáze, zvažte použití elastického fondu pro optimalizaci nákladů. Elastický fond je skvělým způsobem, jak dosáhnout konsolidace databáze a optimalizaci nákladů. Další informace o správě více databází pomocí elastického fondu naleznete v tématu: [Správa fondů a databází](elastic-pool-manage.md#azure-portal).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Jak často potřebuji spouštět kontroly integrity databáze pro moji databázi

SQL Database používá některé inteligentní techniky, které jim umožňují zvládnout určité třídy poškození dat automaticky a bez ztráty dat. Tyto techniky jsou integrované do služby a v případě potřeby je služba využívá. V pravidelných intervalech jsou zálohy databáze v rámci služby testovány jejich obnovením a spuštěním příkazu DBCC CHECKDB. Pokud dojde k problémům, SQL Database je proaktivně adresovaná. [Automatická oprava stránky](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) se používá pro opravené poškozené stránky nebo problémy s integritou dat. Stránky databáze se vždycky ověřují s výchozím nastavením KONTROLNÍho SOUČTu, který ověřuje integritu stránky. SQL Database proaktivně monitoruje a kontroluje integritu dat vaší databáze, a pokud dojde k potížím, adresuje je s nejvyšší prioritou. Kromě toho se můžete rozhodnout, že budete chtít volitelně spustit své vlastní kontroly integrity.  Další informace najdete v tématu [Integrita dat v SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) .

## <a name="data-movement-after-migration"></a>Přesun dat po migraci

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database-using-the-azure-portal"></a>Návody exportovat a importovat data jako soubory BACPAC z SQL Database pomocí Azure Portal

- **Export**: databázi můžete exportovat v Azure SQL Database jako soubor BACPAC z Azure Portal

   ![Export databáze](./media/manage-data-after-migrating-to-database/database-export1.png)

- **Import**: můžete také importovat data jako soubor BacPac do databáze v Azure SQL Database pomocí Azure Portal.

   ![Import databáze](./media/manage-data-after-migrating-to-database/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Návody synchronizovat data mezi SQL Database a SQL Server

Toho můžete dosáhnout několika způsoby:

- **[Synchronizace dat](sql-data-sync-data-sql-server-sql-database.md)** – Tato funkce pomáhá synchronizovat data obousměrně mezi několika SQL Server databázemi a SQL Database. Chcete-li provést synchronizaci s SQL Server databázemi, je nutné nainstalovat a nakonfigurovat agenta synchronizace v místním počítači nebo virtuálním počítači a otevřít odchozí port TCP 1433.
- **[Replikace transakcí](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – pomocí replikace transakcí můžete synchronizovat data z databáze SQL Server, abyste Azure SQL Database s instancí SQL Server, kterou má Vydavatel, a Azure SQL Database je předplatitelem. V současné době je podporována pouze tato instalace. Další informace o tom, jak migrovat data z databáze SQL Server do Azure SQL s minimálními výpadky, najdete v tématu: [použití replikace transakcí](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication) .

## <a name="next-steps"></a>Další kroky

Přečtěte si o [SQL Database](sql-database-paas-overview.md).
