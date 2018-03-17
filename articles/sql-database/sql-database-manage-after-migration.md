---
title: "Správa po migraci – Azure SQL Database | Microsoft Docs"
description: "Naučte se spravovat vaše databáze po dokončení migrace do Azure SQL Database."
services: sql-database
author: joesackmsft
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: article
ms.date: 03/16/2018
ms.author: josack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: 4e50a1be3437ab1b027c1ca0f160402239e13e92
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>Nové DBA v cloudu – správu databáze v databázi SQL Azure

Přechod z tradiční samoobslužné spravované, může to vypadat trochu čtenáře samoobslužné řízeného prostředí do prostředí PaaS na první. Jako vývojář aplikace nebo DBA by budete chtít vědět základní funkce platformy, na které by vám pomůže ochránit vaše aplikace k dispozici, původce, zabezpečení a odolné - vždy. K tomu přesně cílem je v tomto článku. Článek stručně uspořádá prostředky a dává některé pokyny o tom, jak nejlépe pomocí klíčové funkce SQL Database můžete spravovat a udržovat vaši aplikaci běžící efektivně a dosažení optimálních výsledků v cloudu. Typické cílová skupina pro tento článek by ty, kdo:-
- Hodnocení migrace příslušných aplikacích k databázi SQL Azure – Modernizing vaší aplikace.
- Probíhá migrace příslušných aplikacích – průběžné scénář migrace.
- Nedávném dokončení migrace na Azure SQL DB – nové DBA v cloudu.

Tento článek popisuje některé základní charakteristiky databáze SQL Azure jako platformu, která můžete snadno využít. Jsou následující:- 
- Obchodní kontinuitu a zotavení po havárii (BCDR)
- Zabezpečení a dodržování předpisů
- Inteligentní databáze monitorování a údržby
- Přesuny dat


## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Obchodní kontinuitu a zotavení po havárii (BCDR)
Obchodní kontinuitu a po havárii obnovení dalo umožňují pokračovat vaší firmě obvyklým způsobem, v případě havárie. Po havárii může být událostí na úrovni databáze (například někdo omylem zahodí zásadní tabulku) nebo datové centrum událostí na úrovni (místní katastrofická, například tsunami). 

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Jak vytvořit a spravovat zálohy pro službu SQL Database?
Nemusíte vytvářet zálohy na databázi SQL Azure a je to způsobeno nemusíte. Databáze SQL automaticky vytvoří zálohu databáze, takže už se musí starat o plánování, přičemž a správu zálohování. Platforma trvá úplné zálohování každý týden, rozdílové že zálohování každých několik hodin a protokolu zálohování každých 5 minut, aby zajistil zotavení po havárii efektivní a ztrátě dat, která je minimální. Při vytváření databáze se stane první úplné zálohování. Tyto zálohy jsou k dispozici po určitou dobu názvem "Doba uchování" a se liší podle úroveň výkonu, které zvolíte.  SQL Database nabízí možnost obnovit do libovolného bodu v čase v rámci doby uchování pomocí [bodu v možnosti doba obnovení (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Konfigurovat výkon|Doba uchování ve dnech|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Kromě toho [dlouhodobé uchovávání zleva doprava](sql-database-long-term-retention.md) funkce umožňuje podržte na záložní soubory mnohem delší dobu konkrétně až deset let a obnovení dat z tyto zálohy v libovolném bodě v rámci této doby. Kromě toho zálohy databáze se ukládají na geograficky replikované úložiště zajistit odolnost proti z místní katastrofická. Můžete také obnovit tyto zálohy v libovolné oblasti Azure v libovolném bodě čas v rámci dobu uchování. V tématu [obchodní kontinuity přehled](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Jak zjistím kontinuity podnikových procesů v případě havárie úrovni datacenter nebo regionální katastrofická?
Zálohování databáze jsou uloženy v podsystému geograficky replikované úložiště zajistit, aby v případě havárie místní, můžete obnovit zálohu do jiné oblasti Azure. Toto je voláno geografické obnovení. Plánovaný bod obnovení (plánovaného bodu obnovení) pro tento je obecně < 1 hodina a vložit (odhadovaný čas obnovení – několik minut až hodin).

Důležité databáze Azure SQL DB nabízí, aktivní geografickou replikací. To v podstatě udělá se vytvoří geograficky replikované sekundární kopie původní databáze v jiné oblasti. Například pokud vaše databáze je původně hostované v oblasti Azure západní USA a chcete odolnost regionální po havárii. Měli byste vytvořit repliku aktivní georeplikace do databáze v západní USA. Tím vyjádříte východní USA. Pokud calamity zasáhnout na západní USA, můžete převzetí služeb při selhání pro oblast východní USA. Konfigurace je ve skupině automatické převzetí služeb při selhání je ještě lepší, protože to zajistí, že databáze automaticky převezme sekundární ve východní USA v případě havárie. Plánovaný bod obnovení pro tuto je < 5 sekund a do vložit < 30 sekund.

Pokud není nakonfigurováno skupinu automatické převzetí služeb při selhání, aplikace musí aktivně sledování havárii a zahájit převzetí služeb při selhání na sekundární. Můžete vytvořit až 4 takové active geo repliky v různých oblastech Azure. Získá ještě lepší. Můžete taky přejít tyto sekundární active geo repliky pro přístup jen pro čtení. To se dodává velmi užitečný pro snižování latence pro scénář geograficky distribuované aplikace. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Jak Moje plánu zotavení po havárii změní z místního k databázi SQL?
Souhrn instalace systému SQL Server tradičních místních vyžadovat aktivně spravovat vaše dostupnosti pomocí funkce, jako je Clustering převzetí služeb při selhání, zrcadlení databáze, replikaci transakcí, atd. přesouvání protokolu a udržovat a spravovat zálohy zajistit Kontinuita podnikových procesů. Databáze SQL spravuje platforma to pro vás, vám umožní soustředit se na vývoji a optimalizaci aplikace databáze a nestarat se o správu po havárii co nejvíc. Můžete mít zálohování a plány obnovení po havárii nakonfigurované a práci s několika kliknutí na portálu Azure (nebo několik příkazů pomocí rozhraní API prostředí PowerShell). 

Další informace o zotavení po havárii, najdete v části: [Azure SQL Db po havárii obnovení 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Zabezpečení a dodržování předpisů
Databáze SQL trvá velmi vážně zabezpečení a ochrana osobních údajů. Zabezpečení v rámci databáze SQL je k dispozici na úrovni databáze a na úrovni platformy a nejlépe odhalíte při rozdělené do několika vrstev. V každé vrstvě dojde k řízení a zabezpečeny optimální pro vaši aplikaci. Vrstvy, které jsou:
- Identit a ověření ([ověřování systému Windows nebo SQL a ověřování služby Azure Active Directory [AAD]](sql-database-control-access.md)).
- Monitorování aktivit ([auditování](sql-database-auditing.md) a [detekce hrozby](sql-database-threat-detection.md)).
- Ochrana skutečná data ([transparentní šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) a [vždy šifrována [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)). 
- Řízení přístupu k datům citlivé a privilegovaných ([zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) a [dynamického maskování dat](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Security Center](https://azure.microsoft.com/services/security-center/) nabízí možnost správy centralizované zabezpečení přes úlohy běžící v Azure, místně a v ostatních cloudů. Se zobrazí, jestli je to nezbytné ochrany SQL Database jako [auditování](sql-database-auditing.md) a [transparentní šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) jsou nakonfigurované na všechny prostředky a vytvářet zásady podle vlastních požadavků.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Jaké metody ověřování uživatele jsou k dispozici v databázi SQL?
Existují [dvě metody ověřování](sql-database-control-access.md#authentication) nabízí v databázi SQL: 
- [Ověřování služby Azure Active Directory](sql-database-aad-authentication.md)
- Ověřování SQL. 

Ověření tradiční windows není podporováno. Azure Active Directory (AD) je centralizovaná služba správy identit a přístupu. S tímto můžete velmi pohodlně zadat jednotné přihlašování přístup (SSO) na všechny uživatele ve vaší organizaci. To znamená, že přihlašovací údaje jsou sdíleny ve všech služeb Azure pro jednodušší ověřování. Podporuje AAD [vícefaktorového ověřování (Vícefaktorové ověřování)](sql-database-ssms-mfa-authentication.md) a s [několika kliknutími](../active-directory/connect/active-directory-aadconnect-get-started-express.md) AAD se dá integrovat s Windows Server Active Directory. Ověřování SQL funguje stejně jako jste dosud používali ho v minulosti. Zadejte uživatelské jméno a heslo a může ověřit uživatele k jakékoli databázi v daném logického serveru. To také umožňuje SQL Database a SQL Data Warehouse k nabízení služby Multi-Factor authentication a hosta uživatelské účty v doméně služby Azure AD. Pokud již máte Active Directory v místě, můžete vytvořit federaci adresář s Azure Active Directory pro rozšíření adresáře do Azure.

|**Pokud jste...**|**Databáze SQL / SQL datového skladu**|
|---|---|
|Raději použít Azure Active Directory (AD) ve službě Azure|Použití [ověřování SQL.](sql-database-security-overview.md)|
|Použít AD v systému SQL Server na místě|[Vytvořit federaci s Azure AD AD](../active-directory/connect/active-directory-aadconnect.md)a používání ověřování Azure AD. To můžete pomocí jednotného přihlašování.|
|Potřebujete vynutit vícefaktorové ověřování (MFA)|Vyžadovalo jako zásada prostřednictvím [podmíněného přístupu Microsoft](sql-database-conditional-access.md)a použijte [Azure AD Universal ověřování s podpora vícefaktorového ověřování](sql-database-ssms-mfa-authentication.md).|
|Účty hostů z účty Microsoft (live.com, outlook.com) nebo v jiných doménách (gmail.com)|Použití [Azure AD Universal ověřování](sql-database-ssms-mfa-authentication.md) v SQL databáze nebo datového skladu, která využívá [spolupráce Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Jsou protokolovány systému Windows z federované domény pomocí přihlašovacích údajů Azure AD|Použití [integrované ověřování služby Azure AD](sql-database-aad-authentication-configure.md).|
|Jsou protokolovány systému Windows pomocí přihlašovacích údajů z domény není sdružených se službou Azure|Použití [integrované ověřování služby Azure AD](sql-database-aad-authentication-configure.md).|
|Máte služby střední vrstvy, které je třeba se připojit k SQL Database nebo SQL Data Warehouse|Použití [integrované ověřování služby Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Jak omezit nebo řízení přístupu připojení k databázi?
Existuje několik postupů k dispozici, který můžete použít k dosažení optimální připojení organizace pro vaši aplikaci. 
- Pravidla brány firewall
- Koncové body služby virtuální sítě
- Vyhrazené IP adresy

#### <a name="firewall"></a>Brána firewall
Brána firewall brání přístupu k serveru externí entity tím, že se pouze konkrétní entity přístup ke svému logickému serveru. Ve výchozím nastavení všechna připojení a databází uvnitř logický server jsou zakázány na úrovni, s výjimkou připojení brzo z jiné služby Azure. S pravidlo brány firewall můžete otevřít přístup na váš server jenom pro entity (například počítač vývojáře), které schválit, tím, že IP adresa tohoto počítače přes bránu firewall. Také umožňuje zadat rozsah IP adres, kterou chcete povolit přístup k logickému serveru. Například můžete vývojáře IP adresy počítačů ve vaší organizaci přidat najednou tak, že zadáte rozsah na stránce nastavení brány Firewall. 

Můžete vytvořit pravidla brány firewall na úrovni serveru nebo na úrovni databáze. Pravidla brány firewall úrovni serveru můžete buď vytvořit pomocí portálu nebo pomocí aplikace SSMS. Dozvědět další informace o tom, jak nastavit pravidlo brány firewall na úrovni databáze a serveru, najdete v tématu: [v databázi SQL vytvořit pravidla brány firewall](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal).

#### <a name="service-endpoints"></a>Koncové body služby
Ve výchozím vaše databáze SQL je nakonfigurované tak, aby "Povolit všech služeb Azure" – což znamená, že všechny virtuální počítače v Azure mohou zkuste se připojit k databázi. Tyto pokusy ještě získá ověření. Ale pokud nechcete, databáze být přístupný pro všechny IP adresy Azure, můžete zakázat "Povolit všech služeb Azure". Kromě toho můžete nakonfigurovat [koncové body služby virtuální síť](sql-database-vnet-service-endpoint-rule-overview.md).

Koncové body služby (SE) umožňují vystavit důležité prostředky Azure, jenom na vlastní privátní virtuální sítě v Azure. Díky tomu v podstatě eliminovat veřejný přístup k prostředkům. Přenos dat mezi vaší virtuální sítě do Azure probíhal v Azure páteřní síti. Bez SE zobrazí vynucené tunelování směrování paketů. Virtuální síť vynutí internetový provoz pro vaši organizaci a provoz služby Azure si projít stejným trasy. S koncovými body služby můžete optimalizovat to od toku paketů přímo z vaší virtuální sítě ke službě na Azure páteřní síti.

![Koncové body služby virtuální sítě](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

#### <a name="reserved-ips"></a>Vyhrazené IP adresy
Další možností je zřídit [vyhrazené IP adresy](../virtual-network/virtual-networks-reserved-public-ip.md) pro virtuální počítače a seznam povolených adres ty konkrétní virtuálních počítačů pro IP adresy na serveru nastavení brány firewall. Přiřazením vyhrazené IP adresy uložit potíže toho, že se aktualizovat pravidla brány firewall se při změně IP adres.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Jaké portu lze připojit k databázi SQL na?

Port 1433. Databáze SQL komunikuje přes tento port. Chcete-li připojit z podnikové sítě, budete muset přidat odchozí pravidlo v nastavení brány firewall vaší organizace. Jako vodítko Vyhněte se vystavení port 1433 mimo Azure hranic. Můžete spustit v Azure pomocí aplikace SSMS [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). To není nutné otevřít odchozí připojení k portu 1433, IP adresa je statická, tak, aby databáze lze udělit pouze vzdálené aplikace RemoteApp a podporuje více Multi-Factor Authentication (MFA).

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Jak můžete monitorovat a regulovat aktivitu na můj server a databáze v databázi SQL?
#### <a name="sql-database-auditing"></a>Auditování databáze SQL
S databází SQL můžete zapnout ON auditování sledovat události v databázi. [Auditování databáze SQL](sql-database-auditing.md) zaznamenává události databáze a zapisuje je do souboru protokolu auditování v účtu úložiště Azure. Auditování je obzvláště užitečné, pokud chcete získat přehled o případné porušení zabezpečení a zásady, zajistit dodržování předpisů atd. Umožňuje definovat a nakonfigurovat určité kategorie události, které si myslíte, třeba auditování a na základě získat předem nakonfigurované sestavy a řídicí panel vám získat přehled událostí, ke kterým dochází ve vaší databázi. Můžete použít tyto zásady auditu na úrovni databáze nebo na úrovni serveru. Příručka o tom, jak zapnout auditování pro váš server a databáze, najdete v části: [povolit auditování databáze SQL](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary).

#### <a name="threat-detection"></a>Detekce hrozeb
S [detekce hrozby](sql-database-threat-detection.md), získáte možnost provedení akce porušení zabezpečení nebo zásady zjištěny velmi snadno auditování. Nemusíte být odborné adres v systému potenciální hrozby nebo porušení zabezpečení. Detekce hrozeb má také některé integrované funkce, jako je například zjištění Injektáž SQL. Injektáž SQL je pokus o alter nebo ohrozit zabezpečení dat a celkem běžné způsob obecně napadení databázovou aplikaci. Detekce hrozeb databáze SQL se spustí více sad algoritmů, které je zjistit potenciální ohrožení zabezpečení a prostřednictvím injektáže SQL a také nezvyklé databázové přístupové vzorce (například přístup z neobvyklého umístění nebo neznámého objektu zabezpečení). Osoby zabezpečení nebo jiné určené správci zobrazit e-mailové oznámení, pokud je zjištěno ohrožení v databázi. Jednotlivá oznámení obsahuje podrobnosti o podezřelé aktivity a doporučení dále zkoumat a zmírnit riziko. Zjistěte, jak zapnout detekce hrozeb, najdete v tématu: [povolení detekce hrozeb databáze SQL](sql-database-security-tutorial.md#enable-sql-database-threat-detection). 
### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Jak lze zabezpečit data obecně v databázi SQL?
Zajišťuje šifrování silné mechanismus k ochraně a zabezpečený citlivá data z případné útočníky. Šifrovaná data je bez použití útočníkům bez dešifrovací klíč. Proto přidá další vrstvu ochrany nad existující vrstvy zabezpečení vytvořené v databázi SQL. Existují dva aspekty ochrany dat v databázi SQL: 
- Vaše data, která je na rest v souborech protokolu a data 
- Vaše data, která je během letu. 

V databázi SQL, ve výchozím nastavení, jsou data v klidovém stavu uložených v souborech protokolu a data v subsystému úložiště úplně a vždy šifrována prostřednictvím [transparentní šifrování dat [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Zálohy jsou šifrované. U kterých jsou bez nutnosti na straně vaší aplikace, která je přístup k těmto datům změn. Šifrování a dešifrování dojít transparentně; Proto název. Pro ochranu citlivých dat během letu i v klidu SQL Database nabízí funkci [vždy šifrované AE](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE je forma šifrování na straně klienta, který šifruje citlivé sloupce v databázi (tak, aby byly v ciphertext neoprávněným uživatelům a správci databází). Server přijímá na začátku šifrovaná data. Klíč pro funkce Always Encrypted je také uloženo na straně klienta, takže pouze autorizovaným klientům můžete dešifrování citlivých sloupce. Data správci serveru a nejde zobrazit citlivá data, vzhledem k tomu, že šifrovací klíče jsou uložené na straně klienta. AE šifruje citlivé sloupců v tabulce koncová, z neoprávněným klientům fyzického disku. AE podporuje porovnání rovnosti dnes, takže DBAs můžete pokračovat v dotazování šifrované sloupce jako součást jejich příkazy SQL. Vždy šifrovaný lze použít s celou řadu možností úložiště klíčů, jako například [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), úložiště certifikátů systému Windows a modulů místní hardwarového zabezpečení.

|**Vlastnosti**|**Funkce Always Encrypted**|**Transparentní šifrování dat**|
|---|---|---|
|**Šifrování rozpětí**|Klient server|Data na rest|
|**Databázový server, můžete přístup k citlivým datům**|Ne|Ano, protože šifrování je pro data v klidovém stavu|
|**Povolených operací T-SQL**|Porovnání rovnosti|Je k dispozici všechny útoku T-SQL|
|**Změny aplikace vyžaduje použití funkce**|Minimální|Minimálními|
|**Členitost šifrování**|Na úrovni sloupce|Úroveň databáze|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Jak můžete omezit přístup k citlivým datům v databázi?
Každá aplikace má bit citlivá data v databázi, která je potřeba chránit nebudou viditelné pro všechny uživatele. Některé pracovníky v rámci organizace třeba Pokud chcete zobrazit tato data, ale ostatní by neměl být schopní zobrazit tato data. Jedním z příkladů je mzdy zaměstnanců. Správce ale potřebovat přístup k informacím mzdy jejich sestav přímo, jednotlivé členy týmu by neměl mít přístup k informacím mzdy z svým spolupracovníkům. Další možností je dat vývojáře, kteří mohou interakci s citlivá data během fáze vývoj nebo testování, například čísla sociálního zabezpečení zákazníků. Opakujte tyto informace nemusí být zpřístupněny vývojář. V takových případech citlivá data buď musí maskování nebo nebyla vůbec vystavené. SQL Database nabízí tyto dva přístupy k zabránit neoprávněným uživatelům v schopnost zobrazit citlivá data:

[Dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md) je funkce maskování dat, která umožňuje omezit vystavování citlivých dat pomocí maskování uživatelům bez oprávnění na aplikační vrstvu. Můžete definovat pravidla maskování, které můžete vytvořit vzor maskování (například k zobrazení pouze poslední čtyři číslice national ID SSN: XXX-XX-0000 a označit ji jako Xs většinu) a identifikovat uživatele, kteří mají být vyloučeny z pravidla maskování. Maskování probíhá na průběžně a nejsou k dispozici pro různé kategorie dat různé funkce maskování. Dynamická data maskování umožňuje automaticky zjistit citlivá data v databázi a použije maskování na ni.

[Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) vám umožňuje řídit přístup na úrovni řádků. Znamená, některé řádky v tabulce databáze na základě uživatele provádění dotazu (skupiny členství nebo provádění kontextu) jsou skryté. Omezení přístupu se provádí na úrovni databáze místo v aplikační vrstvě, zjednodušit logiky aplikace. Začněte vytvořením predikát filtru, filtrování řádků, které nejsou vystaveny a zabezpečení zásady další definování kdo má přístup na tyto řádky. Nakonec koncový uživatel spustí jejich dotaz a, v závislosti na oprávnění uživatele, zobrazit tyto řádky s omezeným přístupem nebo ji moci zobrazovat vůbec.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Jak lze spravovat šifrovací klíče v cloudu?

Existují možnosti správy klíčů pro vždy šifrovat (šifrování na straně klienta) a transparentní šifrování dat (šifrování v klidovém stavu). Doporučujeme pravidelně obměna šifrovacích klíčů. Frekvence střídání by měl dodržovaly předpisy interní organizaci a požadavky na dodržování předpisů.

**Transparentní šifrování šifrování dat (TDE)**: je hierarchie dva klíče v TDE – data v každé databázi uživatele jsou zašifrována pomocí symetrického AES 256 databáze jedinečný šifrovací klíč databáze (DEK), který je zase šifruje pomocí serveru jedinečný asymetrické šifrování RSA 2048 hlavní klíč. Hlavní klíč může být buď spravovat:
- Automaticky platformou – databáze SQL.
- Nebo můžete pomocí [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) jako úložiště klíčů.

Ve výchozím nastavení hlavní klíč pro transparentní šifrování dat spravuje služba SQL Database pro usnadnění práce. Pokud si organizace přeje kontrolu nad hlavního klíče, je možnost k použití Vault](sql-database-always-encrypted-azure-key-vault.md) klíč Azure jako úložiště klíčů. Pomocí Azure Key Vault, vaše organizace předpokládá kontrolu nad klíče ovládací prvky zřizování, otáčení a oprávnění. [Otočení nebo přepnutí typ šifrování TDE hlavní klíč](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) je rychlé, protože pouze znovu zašifruje klíč DEK. Pro organizace s oddělení rolí mezi zabezpečení a správu dat může správce zabezpečení zřídit materiál klíče pro šifrování TDE hlavního klíče v Azure Key Vault a zajistit identifikátorem klíče Azure Key Vault pro správce databáze pro šifrování v klidovém stavu na serveru. Key Vault je navržený tak, aby Microsoft neznala ani neextrahovala žádné šifrovací klíče. Získáte také centralizovanou správu klíčů pro vaši organizaci. 

**Funkce Always Encrypted**: k dispozici je také [dva klíče hierarchie](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) v vždy šifrována - sloupec citlivých dat šifrování AES 256 sloupec šifrovací klíč (CEK), který je zase šifruje pomocí hlavního klíče sloupce (CMK). Klient ovladače dodané pro funkce Always Encrypted mít žádná omezení délky CMKs. Zašifrovanou hodnotu CEK je uložené v databázi, a CMK je uložené v důvěryhodné úložiště klíčů, jako je například úložiště certifikátů systému Windows, Azure Key Vault nebo modul hardwarového zabezpečení. 
- Obě [CEK a CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) lze otáčet. 
- Otočení CEK je velikost operace dat a může být časově náročný v závislosti na velikosti tabulky obsahující šifrované sloupce. Proto je vhodné naplánovat CEK otočení odpovídajícím způsobem. 
- CMK otočení, ale nebudou v konfliktu s výkonem databáze a lze provést pomocí oddělených role.
Následující diagram znázorňuje možnosti úložiště klíčů pro hlavního klíče sloupce v vždy šifrována.

![Funkce Always encrypted CMK ukládání zprostředkovatelů](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Jak můžete optimalizovat a zabezpečení přenosů mezi Moje organizace a SQL Database?
Síťový provoz mezi vaší organizací a SQL Database by získat obecně směrován přes veřejnou síť. Ale pokud zvolíte možnost Optimalizovat tuto cestu a zvyšuje zabezpečení, můžete zobrazit do Express Route. Expresní trasy v podstatě umožňuje rozšířit vaší podnikové síti do platformy Azure přes privátní připojení. Díky tomu můžete se nepřenášejí prostřednictvím veřejného Internetu. Můžete také získat vyšší zabezpečení, spolehlivost a optimalizace směrování, která znamená, že je na nižší latenci sítě a mnohem vyšší rychlost, než jste by normálně zaznamenat budete prostřednictvím veřejného Internetu. Pokud plánujete přenosu významné bloku dat mezi vaší organizace a Azure, pomocí Express Route přispět nákladů. Do Azure můžete vybírat tři modely jiné připojení pro připojení z vaší organizace: 
- [Společné umístění Exchange cloudu](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Any-to-any](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Express Route také umožňuje burst až 2 x limit šířky pásma, kterou jste si koupili pro bez dalších poplatků. Je také možné nakonfigurovat pro různé oblasti připojení pomocí Express route. Seznam zprostředkovatelů ER připojení najdete v tématu: [Express partnery trasy a umístění partnerského vztahu](../expressroute/expressroute-locations.md). Následující články popisují Express Route podrobněji:
- [Úvod na Express Route](../expressroute/expressroute-introduction.md)
- [Požadavky](../expressroute/expressroute-prerequisites.md)
- [Pracovní postupy](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Je kompatibilní s žádné ze zákonných požadavků v databázi SQL a jak který pomáhá s kontrolou vlastní organizaci?
Databáze SQL je kompatibilní s rozsahem regulačních compliances. Chcete-li zobrazit nejnovější sadu compliances, které byly splněny, navštivte [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings) a přechod na compliances, které jsou důležité pro vaši organizaci chcete zobrazit, pokud databáze SQL je zahrnuta v části kompatibilní služby Azure. Je důležité si uvědomit, že i když SQL Database může musí být certifikovaná jako kompatibilní služby, pomůcky v dodržování předpisů služby vaší organizace ale nezaručí automaticky ji.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Inteligentní databáze monitorování a údržby po migraci

Jakmile provedete migraci databáze k databázi SQL, se chystáte chcete sledovat vaše databáze (pro příklad, jak je využití prostředků, jako je kontrola nebo DBCC kontroluje) a provádět pravidelné údržby (například znovu sestavit nebo reorganizovat indexy, statistiky atd.). Naštěstí databáze SQL je inteligentní v tom smyslu, že používá historických trendů a zaznamenaná metriky a statistiky proaktivně můžete monitorovat a spravovat databáze, tak, aby vaše aplikace běží optimálně vždy. V některých případech můžete databázi SQL Azure automaticky provádět úlohy údržby v závislosti na nastavení konfigurace. Existují tři omezující vlastnosti do monitorování vaší databáze v databázi SQL:
- Sledování výkonu a optimalizace.
- Optimalizace zabezpečení.
- Optimalizace nákladů.

**Sledování výkonu a optimalizace**: S přehled o výkonu dotazu, můžete získat šité na míru doporučení pro úlohy databáze tak, aby vaše aplikace může běžet na optimální úrovni – vždy. Můžete můžete také nastavit tak, aby automaticky použije tato doporučení a nemáte nepokoušejte provádění úloh údržby. S Poradce pro Index automaticky můžete implementovat podle velikosti pracovní zátěže doporučení indexu – tento postup se nazývá Automatické ladění. Doporučení momentální jako zatížení změny aplikace, kde přinášejí nejdůležitější návrhy. Získáte také možnost ručně zkontrolovat tato doporučení a použít je svého uvážení.  

**Optimalizace zabezpečení**: SQL Database poskytuje doporučení, kterého lze provést akci zabezpečení a pomáhá vám zabezpečit vaše data a detekce hrozeb pro identifikaci a databáze podezřelé aktivity, které může představovat potenciální vlákna na odstranění příčin databáze. [Vyhodnocení ohrožení zabezpečení SQL](sql-vulnerability-assessment.md) je databáze vyhledávání a vytváření sestav služby, která umožňuje sledovat stav zabezpečení vašich databází ve velkém měřítku a zjistit rizika zabezpečení a soubor ze základního zabezpečení, které jste definovali. Po každé prohledávání jsou součástí přizpůsobeného seznamu řešitelné kroky a nápravy skripty a zprávu o hodnocení, které můžete použít pro pomoc ke splnění dodržování předpisů.

S Azure Security Center identifikovat doporučení zabezpečení na kartě a použít je s jedním kliknutím. 

**Náklady optimalizace**: platformy Azure SQL analyzuje historie využití mezi databází na serveru k vyhodnocení a doporučujeme možnostmi optimalizaci nákladů. Tato analýza trvá obvykle čtrnáct dní k analýze a sestavení řešitelné doporučení. Elastický fond je jednou z těchto možností. Doporučení se zobrazí na portálu jako nápis:

![doporučení pro elastický fond](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png) 

Můžete také zobrazit tuto analýzu v části "Advisor":

![doporučení advisor elastického fondu](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Jak monitorování výkonu a využití prostředků v databázi SQL?

V databázi SQL můžete využít inteligentního Statistika platformy při sledování výkonu a ladění odpovídajícím způsobem. Můžete sledovat výkon a využití prostředků v databázi SQL pomocí následujících metod:
- **Portál Azure**: portál Azure ukazuje využití izolované databáze výběrem databáze a kliknutím na grafu v podokně s přehledem. Můžete upravit v grafu zobrazí více metriky, včetně procento využití procesoru, procentuální DTU, procento vstupů/výstupů dat, procentuální relací a procentech velikosti databáze.

   ![Graf sledování](./media/sql-database-manage-after-migration/monitoring-chart.png)

   ![Monitorování graf 2](./media/sql-database-manage-after-migration/chart.png)

Z tohoto grafu můžete také nakonfigurovat výstrahy prostředkem. Tyto výstrahy umožní reagovat na prostředek podmínky s e-mailu, zapisovat do koncový bod HTTPS nebo HTTP nebo provedení akce. Najdete v článku [monitorování výkonu databáze v databázi SQL](sql-database-single-database-monitor.md) podrobné pokyny.

- **Zobrazení dynamické správy**: můžete zadat dotaz [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) zobrazení dynamické správy vrácení historie statistiky spotřeby prostředků z za poslední hodinu a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) zobrazení katalogu systému vrátit historie za posledních 14 dní.
- **Dotaz na informace o výkonu**: [Query Performance Insight](sql-database-query-performance.md) vám umožní zobrazit historii nejčastějších dotazů využívání prostředků a dlouho běžící dotazy pro konkrétní databázi. Můžete rychle identifikovat využití prostředků, doba trvání a četnosti provádění NEJČASTĚJŠÍCH dotazů. Můžete sledovat dotazy a zjišťovat regrese. Tato funkce vyžaduje [úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) být povoleno a aktivní databáze.

   ![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

- **SQL analýzy Azure (Preview) na portálu analýzy protokolů**: [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) umožňuje shromažďovat a vizualizovat klíčové metriky výkonu Azure SQL Azure, podporu až 150 000 databází SQL a 5 000 elastické fondy SQL za pracovní prostor. Můžete ho k monitorování a přijímat oznámení. Můžete sledovat SQL Database a metriky elastického fondu v rámci více předplatných Azure a elastické fondy a slouží k identifikaci problémů v každé vrstvě balíčku aplikace.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>I mě vašeho povšimnutí problémy s výkonem: jak Moje metodologie řešení potíží databáze SQL se liší od systému SQL Server?
Hlavní část postupy řešení potíží byste použili pro diagnostiku dotazu a problémů s výkonem databáze zůstávají stejné. Po všech stejný Server SQL zajišťuje modul cloudu. Platforma - databázi SQL Azure je však součástí 'intelligence'. Ho můžete odstraňovat a diagnostikovat problémy s výkonem i snadněji. Můžete ho také některé z těchto opravné akce můžete provádět vaším jménem a v některých případech, proaktivně opravit - automaticky. 

Pomocí funkcí inteligentního výrazně využívat vašeho přístupu k řešení potíží s výkonem [Insight(QPI) výkonu dotazu](sql-database-query-performance.md) a [databáze Advisor](sql-database-advisor.md) ve spojení a takže rozdíl ve metodika se liší v tomto ohledu – už musíte udělat manuální úsilí mletí si důležité informace, které vám můžou pomoct při řešení potíží po ruce. Platforma provede náročné práce. Příkladem této je QPI. S QPI můžete přejít k podrobnostem úplně a úroveň dotazu a podívejte se na historických trendů a zjistěte, po který poklesl přesně dotazu. Databáze Advisor vám doporučení na kroky, které může pomoci zvýšit celkový výkon obecně jako - chybějící indexy, vyřadit indexy, Parametrizace své dotazy atd. 

Při odstraňování potíží výkonu, je potřeba zjistit, jestli je právě aplikace nebo jejich zálohování databáze, která je ovlivňující výkon aplikace. Často problému s výkonem spočívá v aplikační vrstvě. Může to být architekturu nebo vzor přístupu data. Zvažte například, že máte chatty aplikaci, která je citlivá na latenci sítě. V takovém případě aplikace vyskytne, protože by byl mnoho krátké požadavků přejdete oběma směry ("chatty") mezi aplikací a serveru a na přetížení sítě, přidejte tyto zbytečné komunikace až fast. Chcete-li zlepšit výkon v tomto případě, můžete použít [Batch dotazy](sql-database-performance-guidance.md#batch-queries). Použití dávek usnadňuje vytváření vyžadovalo nadměrné protože teď své žádosti zpracovat v dávce; Proto vám pomáhá omezit latenci zvyšuje výkon a zlepšit výkon aplikace. 

Kromě toho, pokud si všimnete snížení celkového výkonu databáze, můžete sledovat [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) zobrazení dynamické správy za účelem pochopení spotřeby procesoru, vstupně-výstupních operací a paměti. Výkon může být dopad, protože databáze je nedostatek prostředků. Může to být, že budete muset změnit úroveň výkonu nebo podle zvětšování a zmenšování vytížení úrovně služby. 

Komplexní sadu doporučení pro ladění problémů s výkonem, najdete v tématu: [ladit databáze](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Jak zjistím, že je použito příslušnou službu úroveň a úroveň výkonu?
SQL Database nabízí různé úrovně služeb Basic, Standard a Premium. Každé úrovni služeb získáte zaručenou předvídatelný výkon vázaný na této úrovni služby. V závislosti na velikosti pracovní zátěže může mít shluky aktivity kde může vaše využití prostředků dosaženo horní meze aktuální úroveň výkonu, které jsou v. V takových případech je užitečné první spuštění vyhodnocením zda žádné ladění může pomoct (např. Přidání nebo změna indexu atd.). Pokud stále narazíte na potíže limit, zvažte přechod na vyšší úroveň výkonu nebo úrovně služby. 

|**Úrovně služeb**|**Běžných scénářích případů použití**|
|---|---|
|**Basic**|Aplikace s několik uživatelů a databázi, která nemá vysokou požadavky na souběžnost, rozsah a výkon. |
|**Standard**|Aplikace s značné požadavky souběžnosti, rozsah a výkon kombinaci s nízkou až střední nároky na vstupně-výstupní operace. |
|**Premium**|Aplikace s mnoha souběžných uživatelů, vysoké využití procesoru nebo paměti a vysokým vstupně-výstupní operace vyžaduje. Vysoká souběžnosti, vysoké propustnosti a latence citlivých aplikací můžete využít na úrovni Premium. |
|||

Pro zajištění, že jste na úroveň výkonu správné, můžete monitorovat vaší spotřeby prostředků dotazu a databáze prostřednictvím jednoho z výše uvedených způsobů, jak v "Jak můžu monitorování využití výkonu a prostředků v databázi SQL". By měl můžete zjistit, že vaše dotazy databáze běží konzistentně aktivní na využití procesoru nebo paměti atd. můžete zvážit vertikálním navýšení kapacity na vyšší úroveň výkonu. Podobně pokud jste Všimněte si, že i vaše dobu ve špičce, můžete nevypadají využívat požadované prostředky co nejvíc; Vezměte v úvahu škálování z aktuální úroveň výkonu. 

Pokud máte vzor pro aplikace SaaS nebo scénáři databáze konsolidace, zvažte použití fondu elastické databáze pro optimalizaci nákladů. Elastický fond je skvělým způsobem, jak databázi konsolidace a optimalizaci nákladů. Další informace o správě více databází pomocí elastického fondu, najdete v části: [spravovat fondy a databází](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal). 

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Jak často je potřeba spustit kontroly integrity databáze pro databázi?
Databáze SQL používá některé inteligentní technik, které mohla zpracovávat určité třídy poškození dat, automaticky a bez ztráty dat. Tyto postupy jsou součástí služby a jsou využity službou když třeba nastane. V pravidelných intervalech jsou testovány záloh databáze napříč službou, je obnovení a spuštěním příkazu DBCC CHECKDB na něm. Pokud dojde k problémům, databáze SQL je proaktivně řeší. [Oprava automatické stránky](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) využívaná opravě stránek, které jsou poškozené nebo mít problémy s integritou dat. Na stránkách databáze jsou vždy ověřuje s výchozím nastavením kontrolního SOUČTU, která ověřuje integritu stránky. Databáze SQL proaktivně monitoruje a zkontroluje integritu dat databáze a, pokud k nim dojde, je adresy s nejvyšší prioritou. Kromě toho můžete případně spustit vlastní kontroly integrity na vaše bude.  Další informace najdete v tématu [integritu dat v databázi SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Přesun dat po migraci

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Jak exportovat a importovat data jako soubory souboru BACPAC z databáze SQL?

- **Export**: Azure SQL database, můžete exportovat jako soubor souboru BACPAC z portálu Azure

   ![Export databáze.](./media/sql-database-export/database-export.png)

- **Import**: data jako soubor souboru BACPAC můžete také importovat do databáze pomocí portálu Azure.

   ![Import databáze.](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Jak synchronizaci dat mezi SQL Database a SQL Server?
Existuje několik způsobů jak toho docílit: 
- **[Synchronizaci dat](sql-database-sync-data.md)**  – tato funkce umožňuje synchronizaci dat obousměrně mezi více místní databáze systému SQL Server a databáze SQL. K synchronizaci s místní databází systému SQL Server, musíte nainstalovat a nakonfigurovat synchronizaci agenta v místním počítači a otevřete odchozí port TCP 1433.
- **[Transakce replikace](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)**  – s transakce replikace můžete synchronizaci dat z místního k databázi SQL Azure s místní se vydavatele a odběratele se databáze SQL Azure. Prozatím se jenom tato instalace je podporována. Další informace o tom, jak migrovat data z místního do Azure SQL s minimálními výpadky najdete v tématu: [použití transakcí replikace](sql-database-cloud-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Další postup
Další informace o [databáze SQL](sql-database-technical-overview.md).

