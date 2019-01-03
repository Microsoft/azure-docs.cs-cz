---
title: Správa po migraci – Azure SQL Database | Dokumentace Microsoftu
description: Další informace o správě databáze po migraci do Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 7b40496d22ffed8096ac40efcb96ec55a8ba63ca
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652791"
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>Nové DBA v cloudu – Správa vaší databáze ve službě Azure SQL Database

Přechod z tradiční svým spravované svým řízeném prostředí PaaS prostředí se může zdát poněkud náročný na první. Jako vývojář aplikací nebo DBA byste znát základní funkce platformy, které by vám pomůže ochránit vaše aplikace, které jsou k dispozici, výkonné, zabezpečené a odolná proti selháním - vždy. V tomto článku, zaměřuje na to přesně. Tento článek stručně slouží k uspořádání prostředků a poskytuje pokyny, jak nejlépe používat klíčové funkce služby SQL Database spravovat a udržovat vaše aplikace běžící efektivně a dosažení optimálních výsledků v cloudu. Typické cílovou skupinu pro účely tohoto článku bude těch, kdo:

- Hodnocení migraci svých aplikací do služby Azure SQL DB – modernizaci vašich aplikací.
- Jsou průběžně migrujeme svoje aplikace – probíhající scénář migrace.
- Nedávno dokončené migrace do Azure SQL DB – nové DBA v cloudu.

Tento článek popisuje některé ze základních vlastností služby Azure SQL DB jako platformu, která můžete snadno využít. Jsou následující:

- Obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR)
- Zabezpečení a dodržování předpisů
- Inteligentní databázi sledování a údržbu
- Přesuny dat

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR)

Možnosti obnovení obchodní kontinuity podnikových procesů a po havárii umožňují pokračovat vaši firmu obvyklým způsobem, v případě havárie. Po havárii může být událostí na úrovni databáze (například někdo omylem zahodí zásadní tabulka) nebo datového centra událostí na úrovni (místní pohromě, třeba tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Jak vytvořit a spravovat zálohy vytvořené pro službu SQL Database

Nevytvářejte záloh ve službě Azure SQL DB a důvodem je, že není nutné. SQL Database automaticky zálohuje databáze za vás, tak už se musí starat o plánování, provádění a správa záloh. Platformu trvá úplné zálohování každý týden, rozdílové že zálohování každých pár hodin a do protokolu zálohování každých 5 minut, ujistěte se, že je efektivní zotavení po havárii a ztrátě dat, minimální. Co nejdříve po vytvoření databáze se stane první úplná záloha. Tyto zálohy jsou k dispozici po určitou dobu nazývá "Doba uchování" a se liší podle úrovně služby, kterou zvolíte. SQL Database poskytuje možnost obnovit do libovolného bodu v čase během období uchovávání dat pomocí [bodu v čase obnovení (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Úroveň služeb|Doba uchování ve dnech|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Kromě toho [dlouhodobé uchovávání dat (LTR)](sql-database-long-term-retention.md) funkce umožňuje opřete se o záložní soubory po mnohem delší dobu, až 10 let a data obnovit ze zálohy kdykoli během tohoto období. Kromě toho zálohy databáze jsou uloženy v geograficky replikovaném úložišti, zajistit odolnost vůči místní pohromě. Můžete také obnovit tyto zálohy v libovolné oblasti Azure v libovolném bodě čas v rámci doby uchování. Zobrazit [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Jak zajistím, aby obchodní kontinuity podnikových procesů v případě havárie na úrovni datacentra nebo místní pohromě

Protože vaše zálohy databáze uložené v geograficky replikovaném úložišti, a zkontrolujte, že v případě regionálního, je obnovit zálohu do jiné oblasti Azure. Tomu se říká geografické obnovení. Cíl bodu obnovení (bodu obnovení rpo) pro tuto je obecně < 1 hodina a ERT (odhadovaný čas obnovení – několik minut až hodin).

Pro nepostradatelné databáze Azure SQL DB nabízí, aktivní geografickou replikaci. Co to v podstatě umožňuje je, že vytvoří geograficky replikované sekundární kopii původní databáze v jiné oblasti. Například pokud vaše databáze je původně hostované v oblasti Azure USA – západ a místní haváriím. V oblasti západní USA, USA – východ promluvil si by vytvoření repliky aktivní georeplikace do databáze. V případě náhlé calamity v oblasti západní USA, můžete převzít služby při selhání oblasti USA – východ. Konfigurace ve skupině – automatické převzetí služeb při selhání je ještě lepší, protože tím zajistíte, že databáze automaticky převezme služby při selhání do sekundární lokality v oblasti východní USA v případě havárie. Plánovaný bod obnovení pro tento je < 5 sekund a ERT < 30 sekund.

Pokud skupinu automatické převzetí služeb při selhání není nakonfigurovaná, vaše aplikace potřebuje aktivně monitorovat havárii a zahájit převzetí služeb při selhání do sekundární. Můžete vytvořit až 4 takové active geo repliky v různých oblastech Azure. Získá ještě lepší. Můžete také přistupovat tato sekundární active geo replik pro přístup jen pro čtení. To se hodí velmi snížení latence pro scénář geograficky distribuované aplikace.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Jak mám plán zotavení po havárii změní z místního ke službě SQL Database

Stručně řečeno tradiční místní SQL Server instalační program vyžaduje, abyste aktivně spravovat vaše dostupnosti pomocí funkce, jako je Clustering převzetí služeb při selhání, zrcadlení databáze, replikaci transakcí nebo přesouvání protokolu a udržovat a spravovat zálohy vytvořené k zajištění Kontinuita podnikových procesů. SQL Database spravuje platformu tyto pro vás, abyste se mohli soustředit na vývoj a optimalizace databáze aplikace a bez starostí o správu po havárii největší. Můžete mít zálohování a plány zotavení po havárii nakonfigurovaný a práce s několika málo kliknutí na webu Azure portal (nebo několik příkazů pomocí rozhraní API prostředí PowerShell).

Další informace o zotavení po havárii, naleznete v tématu: [Zotavení po havárii databáze Azure SQL 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Zabezpečení a dodržování předpisů

Databáze SQL trvá zřeteli zabezpečení a ochrana osobních údajů. Zabezpečení v rámci SQL Database je dostupná na úrovni databáze a na úrovni platformy a nejlépe odhalíte při rozdělit do několika vrstev. V každé vrstvě získáte k řízení a poskytují optimální zabezpečení pro vaši aplikaci. Vrstvy jsou:

- Identita a ověřování ([ověřování systému Windows/SQL a ověřování Azure Active Directory [AAD]](sql-database-control-access.md)).
- Monitorování aktivit ([auditování](sql-database-auditing.md) a [detekce hrozeb](sql-database-threat-detection.md)).
- Ochrana skutečná data ([transparentního šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) a [[AE] s funkcí Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Řízení přístupu k datům citlivé a privilegovaných ([zabezpečení na úrovní řádků](/sql/relational-databases/security/row-level-security) a [maskování dynamických dat](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Security Center](https://azure.microsoft.com/services/security-center/) nabízí centralizované bezpečnostní správu napříč úlohy běžící v Azure, místně nebo v jiných cloudech. Můžete zobrazit, jestli je základní ochranu databáze SQL jako [auditování](sql-database-auditing.md) a [transparentního šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) jsou nakonfigurované na všechny prostředky a vytvořit zásady založené na vaše požadavky.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Jaké metody ověřování uživatele se nabízejí ve službě SQL Database

Existují [dvě metody ověřování](sql-database-control-access.md#authentication) ve službě SQL Database nabízí:

- [Ověřování pomocí Azure Active Directory](sql-database-aad-authentication.md)
- Ověřování pomocí SQL

Tradiční windows ověřování není podporováno. Azure Active Directory (AD) je centralizovaná služba správy identit a přístupu. To velmi jednoduše zadáte jednotné přihlašování přístup (SSO) na všechny zaměstnance ve vaší organizaci. To znamená, že přihlašovací údaje jsou sdíleny napříč všemi službami Azure pro jednodušší ověřování. Podporuje AAD [MFA (Vícefaktorové ověřování)](sql-database-ssms-mfa-authentication.md) a s [několika kliknutími](../active-directory/hybrid/how-to-connect-install-express.md) AAD je možné integrovat se službou Windows Server Active Directory. Ověřování SQL funguje úplně stejně, jako jste používali ho v minulosti. Zadejte uživatelské jméno a heslo a uživatele k jakékoli databázi na daném logický server, můžete ověřovat. Umožňuje také SQL Database a SQL Data Warehouse, která nabízí služby Multi-Factor authentication a uživatelské účty hostů v doméně služby Azure AD. Pokud už máte Active Directory v místním, může provést federaci adresář s Azure Active Directory pro rozšíření adresáře do Azure.

|**Pokud jste...**|**SQL Database nebo SQL Data Warehouse**|
|---|---|
|Nechcete použít Azure Active Directory (AD) ve službě Azure|Použití [ověřování SQL](sql-database-security-overview.md)|
|Využité AD v systému SQL Server on-premises|[Vytvoření federace AD se službou Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)a využít ověřování Azure AD. Díky tomu můžete použít jednotné přihlašování.|
|Potřebujete vynutit ověřování Multi-Factor Authentication (MFA)|Vyžadovat vícefaktorové ověřování jako zásady prostřednictvím [podmíněný přístup Microsoft](sql-database-conditional-access.md)a použijte [Azure AD univerzální ověřování s podporou MFA](sql-database-ssms-mfa-authentication.md).|
|Účty hostů z účtů Microsoft (live.com, outlook.com) nebo v jiných doménách (gmail.com)|Použití [Azure AD univerzální ověřování](sql-database-ssms-mfa-authentication.md) ve službě SQL Database/Data Warehouse, která využívá [spolupráce B2B ve službě Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Přihlášení k Windows pomocí přihlašovacích údajů Azure AD z federované domény|Použití [integrované ověřování Azure AD](sql-database-aad-authentication-configure.md).|
|Přihlášení k Windows pomocí přihlašovacích údajů z domény není Federovaná pomocí služby Azure|Použití [integrované ověřování Azure AD](sql-database-aad-authentication-configure.md).|
|Střední vrstvy služby, které je potřeba připojit k SQL Database nebo SQL Data Warehouse|Použití [integrované ověřování Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Jak omezit nebo řídit přístup připojení k databázi

Existuje několik postupů vašim službám, které můžete použít k dosažení organizace průběhem připojení pro vaši aplikaci.

- Pravidla brány firewall
- Koncové body služby virtuální sítě
- Vyhrazené IP adresy

#### <a name="firewall"></a>Brána firewall

Brána firewall brání přístupu ke svému serveru z externí entitu tím, že povolíte přístup jenom konkrétní entity ke svému logickému serveru. Ve výchozím nastavení všechna připojení a databázím uvnitř logický server jsou zakázány, s výjimkou připojení nárůst od ostatních služeb Azure. S pravidlem brány firewall můžete otevřít přístup k vašemu serveru jenom pro entity (například počítač Vývojář), které schválíte, tím, že IP adresa tohoto počítače přes bránu firewall. Také vám umožňuje zadat rozsahu IP adres, které chcete povolit přístup k logickému serveru. Například vývojář počítače IP adresy ve vaší organizaci je možné přidat najednou pomocí celé řady na stránce nastavení brány Firewall.

Můžete vytvořit pravidla brány firewall na úrovni serveru nebo na úrovni databáze. Pravidla brány firewall na úrovni serveru můžete vytvořit buď pomocí Azure portal nebo pomocí aplikace SSMS. Získání informací o tom, jak nastavit pravidlo brány firewall na úrovni databáze a serveru, naleznete v tématu: [Vytvoření pravidla brány firewall ve službě SQL Database](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal).

#### <a name="service-endpoints"></a>Koncové body služby

Ve výchozím nastavení, databáze SQL je nakonfigurovaná na "Povolit službám Azure přístup k serveru" – to znamená, že všechny virtuální počítače v Azure může pokus o připojení k vaší databázi. Tyto pokusy ještě získá ověření. Nicméně pokud by například vaše databáze bude přístupný pro všechny IP adresy Azure, můžete zakázat "Povolit službám Azure přístup k serveru". Kromě toho můžete nakonfigurovat [koncové body služby virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md).

Koncové body služby (SE) umožňují vám umožní vystavit důležité prostředky Azure pouze pro vaše vlastní privátní virtuální síť v Azure. Tímto způsobem, v podstatě eliminovat veřejný přístup k vašim prostředkům. Provoz mezi vaší virtuální sítě do Azure zůstává v páteřní síti Azure. Bez SE dostanete vynuceného tunelování používá směrování paketů. Službě virtual network vynutí přenosy z Internetu do vaší organizace a provoz služeb Azure si projít stejným trasy. S koncovými body služby můžete optimalizovat to od toku paketů přímo z vaší virtuální sítě do služby v páteřní síti Azure.

![Koncové body služby virtuální sítě](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Vyhrazené IP adresy

Další možností je zřízení [vyhrazené IP adresy](../virtual-network/virtual-networks-reserved-public-ip.md) pro virtuální počítače a seznamu povolených IP adres ty konkrétního virtuálního počítače IP adresy na serveru, nastavení brány firewall. Po přiřazení rezervované IP adresy, uložíte potíže s museli měnit IP adresy aktualizovat pravidla brány firewall.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Jaké portu se mohu připojit k SQL Database na

Port 1433. SQL Database komunikuje přes tento port. Chcete-li připojit z podnikové sítě, budete muset přidat odchozí pravidlo v nastavení brány firewall vaší organizace. Jako vodítko Vyhněte se zveřejňování portu 1433 mimo hranice Azure. Můžete spustit v Azure pomocí SSMS [Vzdálená aplikace RemoteApp Azure](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). To není nutné otevřít odchozí připojení k portu 1433, IP adresa je statická, tak, aby databáze může být otevřený, aby pouze vzdálené aplikace RemoteApp a podporuje Multi Factor Authentication (MFA).

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Jak můžu monitorovat a regulovat aktivitu na tento server a databáze ve službě SQL Database

#### <a name="sql-database-auditing"></a>Auditování služby SQL Database

SQL Database můžete zapnout auditování sledovat události databáze. [Auditování služby SQL Database](sql-database-auditing.md) zaznamenává události databáze a zapisuje je do souboru protokolu auditování v účtu úložiště Azure. Auditování je obzvláště užitečné, pokud chcete získat přehled o případné porušení zabezpečení a zásad, zajistit dodržování předpisů atd. Umožňuje definovat a nakonfigurovat určité kategorie událostí, které se domníváte, že potřebujete auditování a na základě, získejte předem nakonfigurované sestavy a řídicí panel pro spoluprodej základní informace o události, ke kterým dochází ve vaší databázi. Můžete použít tyto zásady auditování na úrovni databáze, nebo na úrovni serveru. Příručka o tom, jak zapnout auditování serveru/databáze, naleznete v tématu: [Povolení SQL Database auditování](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary).

#### <a name="threat-detection"></a>Detekce hrozeb

S [detekce hrozeb](sql-database-threat-detection.md), získáte možnost tak, aby fungoval na porušení zabezpečení nebo zásady auditování velmi snadno zjistit. Nemusíte být zabezpečení odborné vyřešit potenciální hrozby nebo narušení ve vašem systému. Detekce hrozeb má také některé integrované funkce, jako je detekce útoku prostřednictvím injektáže SQL. Útok prostřednictvím injektáže SQL při pokusu o alter nebo ohrozit zabezpečení dat a poměrně běžný způsob obecně napadení databázové aplikace. Detekce hrozeb služby SQL Database se spustí více sad algoritmů, které detekovat potenciální ohrožení zabezpečení a útoky prostřednictvím injektáže SQL, jakož i databáze neobvyklé vzory přístupu k (jako je například přístup z neobvyklého umístění nebo neznámého objektu zabezpečení). Vedoucí pracovníci pověření ochranou zabezpečení nebo jiné určené správci přijímání oznámení, pokud se zjistí ohrožení databáze. Každé upozornění obsahuje podrobnosti o podezřelé aktivitě a doporučení k dále zkoumat a zmírnit hrozby. Zjistěte, jak zapnout detekce hrozeb, najdete v tématech: [Povolení detekce hrozeb služby SQL Database](sql-database-security-tutorial.md#enable-sql-database-threat-detection).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Jak chránit svá data obecně pro službu SQL Database

Šifrování poskytuje silné mechanismus k ochraně a zabezpečení citlivých dat z případné útočníky. Šifrovaná data se bez použití dostupné útočníkům bez dešifrovací klíč. Díky tomu se přidává další vrstvu ochrany nad rámec existujících vrstev zabezpečení integrované ve službě SQL Database. Existují dva aspekty ochrany dat ve službě SQL Database:

- Vaše data, která jsou v klidovém stavu v souborech protokolu a data
- Vaše data, která je vydávaných za pochodu

Ve službě SQL Database ve výchozím nastavení, vaše data v klidovém stavu v souborech protokolu a data v subsystému úložiště a vždy zcela zašifrují prostřednictvím [transparentního šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Zálohování je také šifrovaný. S transparentní šifrování dat nejsou žádné změny na straně vaší aplikace, která je přístup k těmto datům vyžaduje. Šifrování a dešifrování stát transparentně; Proto název.
Pro ochranu vašich citlivých dat. vydávaných za pochodu a v klidovém stavu, SQL Database poskytuje funkci s názvem [vždy šifrované (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE je forma šifrování na straně klienta, který šifruje citlivé sloupce v databázi (tak, aby byly v šifrovaného textu pro správce databáze a neoprávněným uživatelům). Server přijímá začneme šifrovaná data. Klíč pro Always Encrypted je také uložená na straně klienta, takže pouze autorizovaní klienti můžou dešifrovat citlivé sloupce. Server a správci dat neuvidí citlivá data, protože šifrovací klíče jsou uložené na straně klienta. AE šifruje citlivé sloupců v tabulce komplexní klientů neoprávněný fyzický disk. AE podporuje porovnání rovnosti v současné době specializující mohli pokračovat v dotazování šifrované sloupce jako součást svých příkazy jazyka SQL. Vždy šifrovaný jde použít s širokou škálu možností úložiště klíčů, jako například [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), úložiště certifikátů Windows a modulů zabezpečení místního hardwaru.

|**Vlastnosti**|**Funkce Always Encrypted**|**Transparentní šifrování dat**|
|---|---|---|
|**Značka span šifrování**|Začátku do konce|Data v klidovém stavu|
|**Databázový server můžete přístup k citlivým datům**|Ne|Ano, protože je šifrování pro neaktivní uložená data|
|**Povolené operace T-SQL**|Porovnání rovnosti|Je k dispozici všechny plochy T-SQL|
|**Změny aplikace vyžaduje použití funkce**|Minimální|Minimální|
|**Členitost šifrování**|Na úrovni sloupce|Úrovni databáze|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Jak můžete omezit přístup k citlivým datům v databázi

Každá aplikace má bit citlivá data v databázi, kterou je potřeba chránit tomu nebudou viditelné všem uživatelům. Některé pracovníky v rámci organizace potřebují k zobrazení těchto dat, ale ostatní by neměl moct zobrazit tato data. Jedním z příkladů je mezd zaměstnanců. Správce by ale potřebovat přístup k mzdové informace o jeho přímé podřízené, jednotlivé členy týmu by neměl mít přístup k informacím mzdy svých spolupracovníků. Další možností je dat vývojáři, kteří mohou interakci s důvěrnými osobními údaji během fází vývoje nebo testování, například čísla sociálního zabezpečení zákazníků. Tyto informace znovu nemusí být vystavená pro vývojáře. V takových případech citlivých dat buď musí maskována nebo nesmí být zveřejněné vůbec. SQL Database nabízí tyto dva přístupy umožňují zabránit neoprávněným uživatelům tomu nebudou moct prohlížet citlivá data:

[Maskování dynamických dat](sql-database-dynamic-data-masking-get-started.md) je funkci maskování dat, která umožňuje omezit riziko ohrožení citlivých dat jejich maskováním pro neprivilegované uživatele na aplikační vrstvu. Můžete definovat pravidla maskování, které můžete společně tvoří masku maskování (například k zobrazení jenom poslední čtyři číslice národní SSN ID: XXX-XX-0000 a označit většina jako Xs) a určit, kteří uživatelé mají k vyloučení z maskování pravidla. Na běhu se stane, maskování a nejsou k dispozici pro různé kategorie dat různé funkce maskování. Dynamické maskování dat umožňuje automaticky rozpoznat citlivá data v databázi a pro ni nastavit maskování.

[Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) vám umožňuje řídit přístup na úrovni řádků. To znamená, jsou skryté některé řádky v tabulce databáze podle uživatele spouštějícího dotaz (skupiny členství nebo kontext spuštění). Omezení přístupu se provádí na úrovni databáze, místo v aplikační vrstvě, zjednodušit vaše aplikace logiky. Začněte vytvořením predikátu filtru, vyfiltrování řádků, které nejsou vystaveny a zabezpečení zásady další definování kdo má přístup pro tyto řádky. Nakonec koncový uživatel spustí dotaz a, v závislosti na oprávnění uživatele, zobrazit tyto řádky s omezeným přístupem nebo se nezobrazuje je vůbec.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Jak můžu spravovat šifrovacích klíčů v cloudu

Existují možnosti správy klíčů pro Always Encrypted (šifrování na straně klienta) a transparentního šifrování dat (šifrování v klidovém stavu). Doporučujeme pravidelně rotovat šifrovací klíče. Frekvence střídání by mělo odpovídat předpisy interní organizace a požadavky na dodržování předpisů.

#### <a name="transparent-data-encryption-tde"></a>Transparentní šifrování dat (TDE)

Dvě klíč hierarchie je transparentní šifrování dat – data v každé uživatelské databázi se šifrují symetrický AES-256 databáze jedinečný šifrovací klíč databáze (DEK), který je zase šifruje pomocí serveru jedinečná asymetrického hlavní klíč RSA 2048. Hlavní klíč lze spravovat buď:

- Automaticky podle platformy – SQL Database.
- Nebo můžete pomocí [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) jako úložiště klíčů.

Ve výchozím nastavení spravuje hlavní klíč pro transparentní šifrování dat služby SQL Database pro usnadnění práce. Pokud vaše organizace řídit hlavního klíče, je k dispozici možnost používat Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) jako úložiště klíčů. Pomocí služby Azure Key Vault, vaše organizace předpokládá kontrolu nad klíčové prvky zřizování, otočení a oprávnění. [Otočení nebo přepínání typu hlavního klíče TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) je rychlý, jako je jenom znovu zašifruje klíč DEK. Pro organizace s oddělení rolí mezi zabezpečení a správu dat může zřízení materiál klíče pro hlavní klíč transparentní šifrování dat ve službě Azure Key Vault a zadejte identifikátor klíče Azure Key Vault pro správce databáze pro správce zabezpečení šifrování v klidovém stavu na serveru. Key Vault je navržený tak, aby Microsoft neznala ani neextrahovala nějaké šifrovací klíče. Získáte také centralizovanou správu klíčů pro vaši organizaci.

#### <a name="always-encrypted"></a>Funkce Always Encrypted

K dispozici je také [dvě klíč hierarchie](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) v Always Encrypted – sloupec citlivá data se šifrují AES 256 sloupec šifrovacího klíče (CEK), který je zase šifruje pomocí hlavního klíče sloupce (CMK). Ovladače klienta k dispozici pro Always Encrypted mají žádné omezení délky CMKs. Zašifrovaná hodnota CEK je uložen v databázi a CMK je uložen v důvěryhodné úložiště klíčů, jako jsou Windows Store certifikát, Azure Key Vault nebo modul hardwarového zabezpečení.

- Oba [CEK a CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) lze otočit.
- Musí se obměnit CEK velikost datové operace a může být náročné na čas v závislosti na velikosti tabulky obsahující šifrované sloupce. Proto je vhodné podle toho naplánujte CEK otáčení.
- CMK otočení, ale nebude v konfliktu s výkonem databáze a lze provést s oddělenými role.

Následující diagram ukazuje možnosti úložiště klíčů pro hlavních klíčů sloupce v Always Encrypted

![Funkce Always encrypted CMK ukládání zprostředkovatelů](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Jak lze optimalizovat a zabezpečili tak komunikaci mezi Moje organizace a SQL Database

Síťový provoz mezi vaší organizace a SQL Database bude obecně směrované přes veřejnou síť. Ale pokud se rozhodnete pro optimalizaci tuto cestu a zvýšit zabezpečení, můžete se podívat do Express Route. Expressroute v podstatě umožňuje rozšířit vaše podnikové sítě do platformy Azure pomocí soukromého připojení. Díky tomu není přejít přes veřejný Internet. Můžete také získat lepší zabezpečení, spolehlivost a směrování optimalizace, které se přeloží na nižší latenci sítě a mnohem rychlejší zpracování než na kolik máte obvykle neprovádějí prostřednictvím veřejného Internetu. Pokud plánujete přenosu významné blok dat mezi vaší organizace a Azure, pomocí Expressroute můžete k výraznému nákladů. Můžete vybírat ze tří modelů různých připojení pro připojení k vaší organizace do Azure:

- [Společné umístění Exchange cloudu](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Any-to-any](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Typu point-to-Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Expressroute vám také umožní burst až 2 x limit šířky pásma, kterou si koupíte na žádné další poplatky. Je také možné nakonfigurovat pro různé oblasti připojení pomocí expressroute. Pokud chcete zobrazit seznam poskytovatelů připojení ER, naleznete v tématu: [Express Route partnery a umístění partnerského vztahu](../expressroute/expressroute-locations.md). Následující články popisují Express Route podrobněji:

- [Úvod k tomu Express Route](../expressroute/expressroute-introduction.md)
- [Požadavky](../expressroute/expressroute-prerequisites.md)
- [Pracovní postupy](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>SQL Database je splňovat zákonné požadavky, a jak, která pomáhá díky dodržování předpisů pro vlastní organizaci

SQL Database je kompatibilní s celou řadou dodržování legislativních předpisů. Pokud chcete zobrazit nejnovější sadu dodržování předpisů, které byly splněny, přejděte [Microsoft Trust Center](https://microsoft.com/trustcenter/compliance/complianceofferings) a přechod na dodržování předpisů, které jsou důležité pro vaši organizaci, pokud chcete zobrazit, pokud je zahrnují využijí služby Azure SQL Database. Je důležité si uvědomit, že i když SQL Database může musí být certifikovaná jako kompatibilní služby, pomáhá v dodržování předpisů služby vaší organizace ale nezaručuje automaticky ji.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Inteligentní databázové monitorování a údržba po migraci

Po migraci databáze do služby SQL Database se bude monitorovat vaši databázi (pro příklad, jak se využití prostředků, jako je kontrola nebo DBCC kontroluje) a provádění pravidelné údržby (třeba znovu sestavit nebo reorganizovat indexy, statistiku atd.). Naštěstí SQL Database je inteligentní v tom smyslu, že použije k proaktivně vám pomůžou monitorovat a spravovat databáze, tak, aby vaše aplikace běží optimálně vždy historické trendy a nahrané metriky a statistiky. V některých případech se databáze Azure SQL automaticky provádět úlohy údržby v závislosti na nastavení konfigurace. Existují tři omezující vlastnosti pro monitorování vaší databáze ve službě SQL Database:

- Sledování výkonu a optimalizace.
- Optimalizace zabezpečení.
- Optimalizace nákladů.

### <a name="performance-monitoring-and-optimization"></a>Sledování výkonu a optimalizace

S informace o výkonu dotazů můžete získat přizpůsobená doporučení pro vaše databázové úlohy tak, aby vaše aplikace můžete spouštět opakovaně na optimální úrovni - vždy. Je můžete také nastavit tak, aby automaticky použije tato doporučení a není potřeba Nepokoušejte se provádí úlohy údržby. Index Advisor můžete automaticky implementovat doporučení indexu na základě vašich úloh – tento postup se nazývá automatického ladění. Doporučení se vyvíjí jako úlohy změny aplikace, kde přinášejí nejrelevantnější návrhy. Získáte také možnost ručně zkontrolovat tato doporučení a použít je na vašem uvážení.  

### <a name="security-optimization"></a>Optimalizace zabezpečení

SQL Database poskytuje užitečná doporučení zabezpečení a pomáhá vám zabezpečit vaše data a detekce hrozeb pro identifikaci a vyšetřování podezřelých databázových aktivitách, které mohou představovat potenciální vlákna do databáze. [Posouzení ohrožení zabezpečení SQL](sql-vulnerability-assessment.md) je databáze vyhledávání a vytváření sestav služby, která vám umožní monitorovat stav zabezpečení vašich databází ve velkém měřítku a identifikujte bezpečnostní rizika a odchylují směrný plán zabezpečení, které jste definovali. Po každé skenování je součástí přizpůsobený seznam praktické kroky a skripty pro nápravu a sestavu posouzení, které je možné, aby splňovaly požadavky na dodržování předpisů.

Pomocí Azure Security Center identifikuje bezpečnostní doporučení uvedená na panelu a použít je jediným kliknutím.

### <a name="cost-optimization"></a>Optimalizace nákladů

Platforma Azure SQL analyzuje historie využití napříč databázemi na serveru k vyhodnocení a vhodné možnosti optimalizace nákladů. Tato analýza obvykle trvá čtrnáct dní k analýze a Vybudujte užitečná doporučení. Elastický fond je jednou z těchto možností. Doporučení se zobrazí na portálu jako nápis:

![doporučení elastického fondu](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Tato analýza můžete zobrazit také v části "Advisor (Poradce):

![doporučení – Poradce pro elastický fond](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Jak monitorovat výkon a využití prostředků ve službě SQL Database

Ve službě SQL Database můžete využít intelligent insights platformy monitorovat výkon a optimalizace odpovídajícím způsobem. Můžete monitorovat výkon a využití prostředků ve službě SQL Database pomocí následujících metod:

#### <a name="azure-portal"></a>portál Azure

Na webu Azure portal zobrazuje využití izolované databáze podle vyberete databázi a kliknete na grafu v podokně s přehledem. Můžete upravit v grafu zobrazí několik metrik, včetně procento využití procesoru, procento DTU, procento datových v/v, procento relací a procento velikosti databáze.

![Graf sledování](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Monitorování graf 2](./media/sql-database-manage-after-migration/chart.png)

Z tohoto grafu můžete také nastavit upozornění podle prostředků. Tyto výstrahy umožní reagovat na podmínky prostředku s e-mailu, zapsat do koncového bodu HTTPS nebo HTTP nebo provedení akce. Další informace najdete v tématu [vytvářet upozornění](sql-database-insights-alerts-portal.md).

#### <a name="dynamic-management-views"></a>Zobrazení dynamické správy

Můžete zadat dotaz [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) zobrazení dynamické správy k vrácení historie statistiky využití prostředků od poslední hodiny a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) zobrazení katalogu systému vrátit Historie za posledních 14 dní.

#### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) umožňuje zobrazit historii hlavní dotazy využívání prostředků a dlouho běžící dotazy na konkrétní databáze. Můžete rychle identifikovat hlavní dotazy podle využití prostředků a doby trvání, četnosti provádění. Můžete sledovat dotazy a zjišťovat regrese. Tato funkce vyžaduje [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) bude povoleným a aktivním pro databázi.

![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-log-analytics"></a>Azure SQL Analytics (Preview) ve službě Log Analytics

[Azure Log Analytics](../azure-monitor/insights/azure-sql.md) umožňuje shromažďovat a vizualizace klíčových metrik výkonu Azure SQL Azure podporují až 150 000 databází SQL a 5 000 SQL elastické fondy na pracovní prostory. Slouží ke sledování a přijímat oznámení. Můžete monitorovat metriky elastického fondu SQL Database a napříč několika předplatných Azure a elastické fondy a je možné identifikovat problémy v každé vrstvě zásobníku aplikací.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Cena vašeho povšimnutí problémy s výkonem: Jak Moje databáze SQL metodologie řešení potíží se liší od SQL serveru

Hlavní část postupy řešení potíží, můžete využít pro diagnostiku dotazů a problémů s výkonem databáze zůstávají stejné. Po všech stejný Server SQL využívá modul cloudu. Však má integrované platformy – Azure SQL DB "intelligence". Může pomoct při řešení potíží a ještě snadněji diagnostikovat problémy s výkonem. Můžete ho také některé z těchto nápravná opatření proveďte vaším jménem a v některých případech, proaktivně opravit - automaticky.

Váš přístup k řešení potíží s problémy s výkonem pomocí inteligentní funkce, jako výrazně využívat [Insight(QPI) výkon dotazu](sql-database-query-performance.md) a [Database Advisor](sql-database-advisor.md) ve spojení a tak rozdíl v metodologie se liší v tom ohledu – už nebude potřeba ruční práce mletí důležité podrobnosti, které vám můžou pomoct problém vyřešit. k dispozici. Platforma těžkou práci udělá za vás. Příkladem, který je QPI. S QPI můžete přejít úplně na úrovni dotaz a podívejte se na historické trendy a zjistit, po který poklesl přesně dotazu. Database Advisor poskytuje doporučení na kroky, které může pomoci zvýšit celkový výkon obecně takto: chybějící indexy, vyřazení indexů, parametrizování dotazů atd.

Pomocí řešení potíží s výkonem, je důležité určit, jestli je to jenom aplikace nebo databáze jejich zálohování, který je vliv na výkon vašich aplikací. Často problému s výkonem, spočívá v aplikační vrstvě. Může to být architekturu nebo vzor přístupu k datům. Představte si třeba, že máte aplikaci přetížených, který je citlivý na latenci sítě. V tomto případě aplikace odkážete vzhledem k tomu, že by existovat mnoho krátký požadavků přejdete vpřed a zpět (příliš "upovídaným") mezi aplikací a serveru a v přetížené síti, přidejte těchto výměn dat rychle. Chcete-li v tomto případě zlepšit výkon, můžete použít [dávkové dotazy](sql-database-performance-guidance.md#batch-queries). Použití dávek vám pomůže výrazně vzhledem k tomu, že teď vaše žádosti zpracovat v dávce; To znamená což pomáhá omezit latenci umožňujícím zpětnou transformaci a zvýšit výkon vašich aplikací.

Kromě toho pokud zjistíte pokles celkový výkon vaší databáze, můžete monitorovat [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) za účelem zobrazení dynamické správy pochopení spotřeby procesoru, vstupně-výstupní operace a paměti. Výkon možná vliv, protože databáze je nedostatek prostředků. Je možné, že budete muset změnit velikost výpočetních a/nebo podle zvětšování a zmenšování vytížení úroveň služby.

Komplexní sadu doporučení pro ladění problémů s výkonem naleznete v tématu: [Ladit vaši databázi](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Jak zajistím, aby mi teď při použití úrovně příslušnou službu a vypočítat velikost

SQL Database nabízí různé úrovně služeb Basic, Standard a Premium. Každá úroveň služby získáte zaručené předvídatelný výkon vázané na úrovně služby. V závislosti na velikosti pracovní zátěže může mít nárůstům aktivity kde využití prostředků pravděpodobně dojde k aktuální velikost výpočetní prostředky, které jsou v horní mez. V takových případech je užitečné začít vyhodnocovat, jestli všechny ladění může pomoci (například přidáním nebo změnou indexu atd.). Pokud budete mít stále omezit problémy, zvažte přechod na vyšší úroveň služby nebo vypočítat velikost.

|**Úroveň služeb**|**Běžné scénáře použití**|
|---|---|
|**Basic**|Aplikace s několika uživateli a databázi, která nemá vysoké požadavky na souběžnost, škálování a výkonu. |
|**Standard**|Aplikace s značné požadavky na souběžnost, škálování a výkonu s velkou provázaností s nízkým až středním požadavky na vstupně-výstupních operací. |
|**Premium**|Aplikací s velkým množstvím souběžných uživatelů, vysoké využití procesoru/paměti a vysoké požadavky na vstupně-výstupních operací. Úroveň Premium můžete využít vysokou souběžnosti, vysoké propustnosti a latence aplikace s citlivými informacemi. |
|||

Za to, že jste na správné výpočetního prostředí, můžete monitorovat využití prostředků dotazu a databáze prostřednictvím jednoho z výše uvedených způsobů, jak v "Jak je možné sledovat využití výkonu a prostředků ve službě SQL Database". By pro vás, vaše dotazy databáze běží konzistentně hot na procesoru nebo paměti atd. zvažte možnost škálování na vyšší výpočetní velikost. Podobně pokud Pamatujte, že i během hodiny ve špičce, můžete nevypadají používat příslušné prostředky největší; Vezměte v úvahu škálování z aktuální výpočty velikosti.

Pokud máte vzor pro aplikace SaaS nebo scénáře konsolidace databáze, zvažte použití elastického fondu pro optimalizaci nákladů. Elastický fond je skvělý způsob, jak dosáhnout databázi konsolidace a optimalizaci nákladů. Další informace o správě více databází pomocí Elastických fondů, naleznete v tématu: [Správa fondů a databází](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Jak často je potřeba spustit kontroly integrity databáze pro moje databáze

SQL Database používá některé inteligentní techniky, které umožňují zpracovávat určité třídy poškození dat, automaticky a bez ztráty dat. Tyto postupy jsou součástí služby a se službou service využívají když třeba nastane. V pravidelných intervalech jsou testovány záloh vašich databází ve službě obnovení databází a v něm spuštěný příkaz DBCC CHECKDB. Pokud dojde k problémům, SQL Database je aktivně řeší. [Automatické opravy](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) se používá k opravě stránky, které jsou poškozené nebo máte problémy s integritou dat. Na stránkách databáze jsou vždy ověřuje s výchozím nastavením kontrolního SOUČTU, která ověřuje integritu stránky. SQL Database aktivně monitoruje a kontroly integrity dat vaší databáze, pokud k nim dojde, adresuje s nejvyšší prioritou. Kromě toho můžete volitelně spustit podle vaší vlastní kontroly integrity.  Další informace najdete v tématu [integritu dat ve službě SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Přesun dat po migraci

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Jak exportovat a importovat data jako souborů BACPAC z databáze SQL

- **Exportovat**: Azure SQL database můžete exportovat do souboru BACPAC z portálu Azure portal

   ![export databáze](./media/sql-database-export/database-export.png)

- **Import**: Můžete také importovat data do souboru BACPAC do databáze pomocí webu Azure portal.

   ![import databáze](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Jak synchronizovat data mezi SQL Database a SQL Server

Máte několik způsobů, jak toho dosáhnout:

- **[Synchronizace dat](sql-database-sync-data.md)**  – tato funkce umožňuje synchronizovat data obousměrně mezi několika místních databází SQL serveru a SQL Database. K synchronizaci s místní databází SQL serveru, musíte nainstalovat a nakonfigurovat agenta synchronizace na místním počítači a otevřít odchozí port TCP 1433.
- **[Transakce replikace](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)**  – pomocí transakční replikace můžete synchronizovat data z místního ke službě Azure SQL DB místní vydavatele a odběratele se služby Azure SQL DB. Nyní je podporována pouze tento instalační program. Další informace o tom, jak migrovat data z místních Azure SQL s minimálními výpadky najdete v tématu: [Použití transakční replikace](sql-database-cloud-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Další postup

Další informace o [SQL Database](sql-database-technical-overview.md).
