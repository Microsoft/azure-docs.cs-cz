---
title: Přehled zabezpečení Azure Database | Microsoft Docs
description: Tento článek obsahuje přehled funkcí zabezpečení Azure Database.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: 9053866b76ec1cc409c3f00d0af59bf3761d3936
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927986"
---
# <a name="azure-database-security-overview"></a>Přehled zabezpečení Azure Database

Zabezpečení je nejdůležitějším problémem při správě databází a je vždycky prioritou pro Azure SQL Database. Azure SQL Database podporuje zabezpečení připojení pomocí pravidel brány firewall a šifrování připojení. Podporuje ověřování pomocí uživatelského jména a hesla a Azure Active Directory (Azure AD) ověřování, které používá identity spravované pomocí Azure Active Directory. Autorizace používá řízení přístupu na základě role.

Azure SQL Database podporuje šifrování tím, že provádí šifrování a dešifrování databází, přidružených záloh a souborů protokolů transakcí v reálném čase bez nutnosti provádět změny aplikace.

Microsoft poskytuje další způsoby, jak šifrovat podniková data:

-   Šifrování na úrovni buňky je k dispozici pro šifrování konkrétních sloupců nebo dokonce buněk dat s různými šifrovacími klíči.
-   Pokud potřebujete modul hardwarového zabezpečení nebo centrální správu vaší hierarchie šifrovacích klíčů, zvažte použití Azure Key Vault s SQL Server na virtuálním počítači Azure (VM).
-   Always Encrypted (aktuálně ve verzi Preview) zajistí transparentní šifrování aplikací. Umožňuje klientům taky šifrovat citlivá data v klientských aplikacích bez sdílení šifrovacích klíčů s SQL Database.

Azure SQL Database auditování umožňuje podnikům zaznamenávat události do protokolu auditu v Azure Storage. Auditování služby SQL Database je také integrované do služby Microsoft Power BI kvůli snadnějšímu procházení sestav a analýz.

Databáze SQL Azure je možné důkladně zabezpečit, aby splňovaly většinu regulativních a bezpečnostních požadavků, včetně HIPAA, ISO 27001/27002 a PCI DSS úrovně 1. Aktuální seznam certifikací dodržování předpisů zabezpečení je k dispozici na [webu Centrum zabezpečení Microsoft Azure](https://azure.microsoft.com/support/trust-center/services/).

Tento článek vás seznámí se základy zabezpečení Microsoft Azure databází SQL pro strukturované, tabelární a relační data. Tento článek vám především pomůže začít s prostředky pro ochranu dat, řízením přístupu a proaktivním monitorováním.

## <a name="protection-of-data"></a>Ochrana dat

SQL Database pomáhá zabezpečit vaše data tím, že poskytuje šifrování:

- Pro data v pohybu přes [protokol TLS (Transport Layer Security)](https://support.microsoft.com/kb/3135244).
- Pro neaktivní data prostřednictvím [transparentního šifrování dat](https://go.microsoft.com/fwlink/?LinkId=526242).
- Pro data, která se používají prostřednictvím [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Existují i jiné možnosti šifrování dat:

-   [Šifrování na úrovni buňky](https://msdn.microsoft.com/library/ms179331.aspx) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
-   Pokud potřebujete modul hardwarového zabezpečení nebo centrální správu vaší hierarchie šifrovacího klíče, [Azure Key Vault s SQL Server na virtuálním počítači Azure](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="encryption-in-motion"></a>Šifrování v pohybu

Běžným problémem všech klientských a serverových aplikací je nutnost ochrany osobních údajů při přesunu dat přes veřejné a privátní sítě. Pokud data přesunovaná přes síť nejsou šifrovaná, existuje možnost, že může být zachycena a odcizena neoprávněnými uživateli. Pokud pracujete s databázovými službami, zajistěte, aby byla data zašifrovaná mezi klientem databáze a serverem. Také se ujistěte, že jsou data zašifrovaná mezi databázovými servery, které komunikují vzájemně a s aplikacemi střední vrstvy.

Jedním z problémů, když spravujete síť, je zabezpečení dat odesílaných mezi aplikacemi v rámci nedůvěryhodné sítě. [Protokol TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) můžete použít k ověření serverů a klientů a pak ho použít k šifrování zpráv mezi ověřenými stranami.

V procesu ověřování pošle klient TLS/SSL zprávu serveru TLS/SSL. Server odpoví informacemi, které server potřebuje k ověření. Klient a server provedou další výměnu klíčů relací a dialogové okno ověřování skončí. Po dokončení ověřování může komunikace zabezpečená protokolem SSL začít mezi serverem a klientem prostřednictvím symetrických šifrovacích klíčů, které jsou vytvořené během procesu ověřování.

Všechna připojení k Azure SQL Database vyžadují šifrování (TLS/SSL) nepřetržitě, zatímco data jsou přenášena do a z databáze. SQL Database používá protokol TLS/SSL k ověřování serverů a klientů a pak je používá k šifrování zpráv mezi ověřenými stranami. 

V připojovacím řetězci aplikace musíte zadat parametry pro šifrování připojení a nemusíte důvěřovat certifikátu serveru. (To se provádí po zkopírování připojovacího řetězce z Azure Portal.) V opačném případě připojení neověřuje identitu serveru a bude náchylné k útokům typu "muž-in-the-middle". Pro ovladač ADO.NET jsou `Encrypt=True` tyto parametry připojovacího řetězce například a. `TrustServerCertificate=False`

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Můžete provést několik preventivních opatření, které vám pomohou zabezpečit databázi. Můžete například navrhnout zabezpečený systém, šifrovat důvěrné prostředky a vytvořit bránu firewall kolem databázových serverů. Ale v situaci, kdy je fyzické médium (například jednotky nebo zálohovací pásky) odcizeno, může osoba se zlými úmysly databázi obnovit nebo připojit a procházet data.

Jedním z řešení je šifrovat citlivá data v databázi a chránit klíče používané k šifrování dat pomocí certifikátu. Toto řešení zabrání komukoli bez použití těchto klíčů v používání dat, ale tento druh ochrany musí být plánovaný.

Chcete-li tento problém vyřešit, SQL Server a SQL Database podporu [transparentního šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Transparentní šifrování dat šifruje SQL Server a SQL Database datových souborů, označovaných jako neaktivní data šifrování.

Transparentní šifrování dat pomáhá chránit před hrozbou škodlivých aktivit. Provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase bez nutnosti změny aplikace.  

Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče, který se nazývá šifrovací klíč databáze. V SQL Database je šifrovací klíč databáze chráněn integrovaným certifikátem serveru. Integrovaný certifikát serveru je jedinečný pro každý SQL Database Server.

Pokud je databáze v relaci Geo-DR, je chráněná jiným klíčem na každém serveru. Pokud jsou dvě databáze připojené ke stejnému serveru, sdílejí stejný integrovaný certifikát. Microsoft tyto certifikáty automaticky přetočí nejméně každých 90 dnů. 

Další informace najdete v tématu [transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Používané šifrování (klient)

Většina porušení dat zahrnuje krádež kritických dat, jako jsou třeba čísla kreditních karet nebo identifikovatelné osobní údaje. Databáze mohou povýšit troves citlivých informací. Můžou obsahovat osobní údaje zákazníků (například národní identifikační čísla), důvěrné informace o konkurenci a duševní vlastnictví. Ztracená nebo odcizená data, zejména zákaznická data, můžou mít za následek poškození značky, konkurenční nevýhody a vážné pokuty, dokonce i soudní spory.

![Funkce Always Encrypted ilustruje jako zámek a klíč.](./media/database-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) je funkce navržená tak, aby chránila citlivá data uložená v databázích Azure SQL Database nebo SQL Server. Always Encrypted umožňuje klientům šifrovat citlivá data v klientských aplikacích a nikdy Neodhalovat šifrovací klíče databázovému stroji (SQL Database nebo SQL Server).

Always Encrypted poskytuje oddělení mezi lidmi, kteří data vlastní (a můžou zobrazit), a lidem, kteří data spravují (ale nemají přístup). Pomáhá zajistit, aby správci místních databází, cloudových databázových operátorů nebo jiných vysoce privilegovaných a neautorizovaných uživatelů měli přístup k šifrovaným datům.

Always Encrypted navíc šifrování transparentním pro aplikace. V klientském počítači je nainstalován ovladač s podporou Always Encrypted, aby mohl automaticky šifrovat a dešifrovat citlivá data v klientské aplikaci. Ovladač šifruje data v citlivých sloupcích před předáním dat do databázového stroje. Ovladač automaticky přepíše dotazy, aby byla zachována sémantika aplikace. Podobně ovladač transparentně dešifruje data uložený ve sloupcích zašifrovaných databází, která jsou obsažená ve výsledcích dotazu.

## <a name="access-control"></a>Řízení přístupu

Pro zajištění zabezpečení SQL Database řídí přístup pomocí:

- Pravidla brány firewall, která omezují připojení podle IP adresy.
- Mechanismy ověřování, které vyžadují, aby si uživatelé mohli prokázat svoji identitu.
- Autorizační mechanismy, které omezují uživatele na konkrétní akce a data.

### <a name="database-access"></a>Přístup k databázi

Ochrana dat začíná řízením přístupu k vašim datům. Datacentrum, které hostuje vaše data, spravuje fyzický přístup. Bránu firewall můžete nakonfigurovat tak, aby spravovala zabezpečení v síťové vrstvě. Přístup také řídíte konfigurací přihlášení pro ověřování a definováním oprávnění pro role serveru a databáze.

#### <a name="firewall-and-firewall-rules"></a>Brána firewall a pravidla brány firewall

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) poskytuje službu relačních databází pro Azure a další internetové aplikace. Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku. Další informace najdete v tématu [Přehled pravidel brány firewall služby Azure SQL Database](/azure/sql-database/sql-database-firewall-configure).

Služba Azure SQL Database je dostupná jenom přes port TCP 1433. Chcete-li získat přístup k databázi SQL z počítače, zajistěte, aby brána firewall klientského počítače umožňovala odchozí komunikaci TCP na portu TCP 1433. Pokud nejsou příchozí připojení potřebná pro jiné aplikace, zablokujte je na portu TCP 1433.

#### <a name="authentication"></a>Ověřování

Ověřování se týká způsobu, jakým prokážete svou identitu při připojování k databázi. SQL Database podporuje dva typy ověřování:

-   **Ověřování SQL Server**: Při vytvoření logické instance služby SQL se vytvoří účet s podporou jediného přihlášení, takzvaný účet předplatitele služby SQL Database. Tento účet se připojuje pomocí [SQL Server ověřování](/azure/sql-database/sql-database-security-overview) (uživatelské jméno a heslo). Tento účet je správcem v logické instanci na serveru a ve všech uživatelských databázích připojených k této instanci. Oprávnění účtu předplatitele nejde omezit. Existovat může jenom jeden z těchto účtů.
-   **Ověřování Azure Active Directory**: [Ověřování Azure AD](/azure/sql-database/sql-database-aad-authentication) je mechanismus připojení k Azure SQL Database a Azure SQL Data Warehouse pomocí identit ve službě Azure AD. Můžete ji použít k centrální správě identit uživatelů databáze.

![Ověřování Azure AD s SQL Database](./media/database-security-overview/azure-database-fig2.png)

 Mezi výhody ověřování Azure AD patří:
  - Nabízí alternativu k ověřování SQL Server.
  - Pomáhá zastavit šíření identit uživatelů napříč databázovými servery a na jednom místě umožňuje otočení hesla.
  - Databázová oprávnění můžete spravovat pomocí externích skupin (Azure AD).
  - Můžete eliminovat ukládání hesel tím, že povolíte integrované ověřování systému Windows a další formy ověřování, které Azure AD podporuje.

#### <a name="authorization"></a>Authorization

[Autorizace](/azure/sql-database/sql-database-manage-logins) odkazuje na to, co může uživatel provádět ve službě Azure SQL Database. Řídí se členstvím v databázových rolích [](https://msdn.microsoft.com/library/ms189121) vašeho uživatelského účtu a [oprávněními na úrovni objektů](https://msdn.microsoft.com/library/ms191291.aspx). Autorizace je proces určení, ke kterému zabezpečeným prostředkům má primární přístup a které operace jsou pro tyto prostředky povoleny.

### <a name="application-access"></a>Přístup k aplikaci

#### <a name="dynamic-data-masking"></a>Dynamické maskování dat

Zástupce služby v centru volání může identifikovat volajících několika číslicemi čísla sociálního pojištění nebo čísla platební karty. Tyto datové položky by ale neměly být zcela vystaveny zástupci služby.

Můžete definovat pravidlo maskování, které bude maskovat všechny, ale jenom poslední čtyři číslice čísla sociálního pojištění nebo číslo platební karty v sadě výsledků dotazu.

![Maskování na čísle odeslaném mezi databází SQL a obchodními aplikacemi](./media/database-security-overview/azure-database-fig3.png)

Dalším příkladem může být definování vhodné datové masky pro ochranu osobně identifikovatelných informací. Vývojář se pak může dotazovat na produkční prostředí pro účely řešení potíží bez porušení předpisů v řádu dodržování předpisů.

[Dynamické maskování dat služby SQL Database](/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých dat pomocí jejich maskování pro neoprávněné uživatele. Dynamické maskování dat je podporováno pro V12 verzi Azure SQL Database.

[Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking) pomáhá zabránit neoprávněnému přístupu k citlivým datům tím, že umožňuje určit, kolik citlivých dat se má zobrazit s minimálním dopadem na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.

> [!Note]
> Dynamické maskování dat může nakonfigurovat role správce Azure Database, správce serveru nebo bezpečnostního důstojníka.

#### <a name="row-level-security"></a>Zabezpečení na úrovni řádku

Dalším běžným požadavkem zabezpečení pro víceklientské databáze je [zabezpečení na úrovni řádků](https://msdn.microsoft.com/library/dn765131.aspx). Tuto funkci můžete použít k řízení přístupu k řádkům v databázové tabulce na základě charakteristik uživatele, který spouští dotaz. (Příklad charakteristik je členství ve skupině a kontext spuštění.)

![Zabezpečení na úrovni řádků, které uživateli umožňuje přístup k řádkům v tabulce prostřednictvím klientské aplikace](./media/database-security-overview/azure-database-fig4.png)

Logika omezení přístupu se nachází v databázové vrstvě, nikoli z dat v jiné aplikační vrstvě. Databázový systém použije omezení přístupu při každém pokusu o přístup k datům z libovolné úrovně. Tím se zajistí spolehlivější a robustní zabezpečení systému tím, že se zmenší plocha v systému zabezpečení.

Zabezpečení na úrovni řádku zavádí řízení přístupu založené na predikátech. Nabízí flexibilní a centralizované hodnocení, které může vzít v úvahu metadata nebo jiná kritéria, která správce podle potřeby určí. Predikát slouží jako kritérium k určení, zda má uživatel odpovídající přístup k datům na základě atributů uživatele. Řízení přístupu na základě popisku můžete implementovat pomocí řízení přístupu založeného na predikátech.

## <a name="proactive-monitoring"></a>Proaktivní monitorování

SQL Database pomáhá zabezpečit vaše data tím, že poskytuje možnosti *auditování* a *detekce hrozeb* .

### <a name="auditing"></a>Auditování

[Azure SQL Database auditování](/azure/sql-database/sql-database-auditing-get-started) zvyšuje vaši schopnost získat přehled o událostech a změnách, ke kterým došlo v rámci databáze. Příklady jsou aktualizace a dotazy na data.

SQL Database auditování sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu služby Azure Storage. Auditování vám může pomoci zajistit dodržování legislativních předpisů, pochopit databázovou činnost a získat přehled o nesrovnalostech a anomáliích, které by mohly poukazovat na obavy z podnikání nebo na podezřelé porušení zabezpečení. Auditování umožňuje a usnadňuje dodržování standardů dodržování předpisů, ale nezaručuje dodržování předpisů.

Auditování SQL Database můžete použít k těmto akcím:

- **Zachovejte** záznam auditu vybraných událostí. Můžete definovat kategorie databázových akcí, které mají být auditovány.
- **Sestava** aktivity databáze. Pomocí předem nakonfigurovaných sestav a řídicího panelu můžete rychle začít pracovat s vytvářením sestav aktivit a událostí.
- **Analýza** sestav. Můžete najít podezřelé události, neobvyklé aktivity a trendy.

Existují dvě metody auditování:

-   **Auditování objektů BLOB**: Protokoly se zapisují do úložiště objektů blob v Azure. Toto je novější metoda auditování. Poskytuje vyšší výkon, podporuje vyšší úroveň auditování na úrovni objektů a je cenově výhodnější.
-   **Auditování tabulek**: Protokoly se zapisují do služby Azure Table Storage.

### <a name="threat-detection"></a>Detekce hrozeb

[Rozšířená ochrana před internetovými útoky pro Azure SQL Database](/azure/sql-database/sql-advanced-threat-protection) detekuje podezřelé aktivity, které indikují potenciální bezpečnostní hrozby. Detekci hrozeb můžete použít k reakci na podezřelé události v databázi, jako jsou například injektáže SQL, při jejich výskytu. Poskytuje výstrahy a umožňuje pomocí Azure SQL Database auditování prozkoumat podezřelé události.

![Detekce hrozeb pro SQL Database a webovou aplikaci s externím útočníkem a škodlivým insiderem](./media/database-security-overview/azure-database-fig5.jpg)

SQL Advanced Threat Protection (ATP) poskytuje sadu pokročilých funkcí zabezpečení SQL, včetně klasifikace & zjišťování dat, posouzení ohrožení zabezpečení a detekce hrozeb. 

- [Klasifikace & Discovery Data](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Posouzení ohrožení zabezpečení](/azure/sql-database/sql-vulnerability-assessment)  
- [Detekce hrozeb](/azure/sql-database/sql-database-threat-detection)

[Azure Database for PostgreSQL Rozšířená ochrana před internetovými útoky](/azure/postgresql/concepts-data-access-and-security-threat-protection) poskytuje novou vrstvu zabezpečení, která umožňuje detekovat a reagovat na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro aktivity neobvyklé. Uživatelé dostanou upozornění na podezřelé databázové aktivity a potenciální ohrožení zabezpečení a také neobvyklé přístup k databázi a vzor dotazů. Rozšířená ochrana před internetovými útoky pro Azure Database for PostgreSQL integruje výstrahy s Azure Security Center. Mezi typy výstrah patří:

- Přístup z neobvyklého umístění
- Přístup z neobvyklého datového centra Azure 
- Přístup z neznámého objektu zabezpečení 
- Přístup z potenciálně škodlivé aplikace 
- Pověření hrubou silou služby Azure Database for PostgreSQL 

[Azure Database for MySQL Rozšířená ochrana před internetovými útoky](/azure/mysql/concepts-data-access-and-security-threat-protection) poskytuje ochranu podobnou PostgreSQL pokročilé ochraně.  

## <a name="centralized-security-management"></a>Centralizovaná správa zabezpečení

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje integrované monitorování zabezpečení a správu zásad napříč předplatnými Azure. Pomáhá detekovat hrozby, které by jinak neinformovaly, a funguje s širokou ekosystémem řešení zabezpečení.

[Security Center](../../security-center/security-center-sql-database.md) pomáhá chránit data v SQL Database tím, že poskytuje přehled o zabezpečení všech serverů a databází. Pomocí Security Center můžete:

- Definujte zásady pro SQL Database šifrování a auditování.
- Monitorujte zabezpečení SQL Database prostředků napříč všemi vašimi předplatnými.
- Rychle identifikujte a napravte problémy se zabezpečením.
- Integrujte výstrahy z [Azure SQL Database detekce hrozeb](/azure/sql-database/sql-database-threat-detection).

Security Center podporuje přístup na základě rolí.

## <a name="sql-information-protection"></a>SQL Information Protection

[SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) automaticky zjišťuje a klasifikuje potenciálně citlivá data, poskytuje mechanismus označování pro trvalé označení citlivých dat pomocí atributů klasifikace a poskytuje podrobný řídicí panel ukazující stav klasifikace databáze.  

Kromě toho počítá sadu výsledků dotazu SQL, takže dotazy, které extrahují citlivá data, lze explicitně auditovat a data lze chránit. Další podrobnosti o Information Protection SQL najdete v tématu Azure SQL Database zjišťování a klasifikace dat.

[Zásady Information Protection SQL](/azure/security-center/security-center-info-protection-policy) můžete nakonfigurovat v Azure Security Center.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace je internetové tržiště aplikací a služeb, kde start-upy a nezávislí dodavatelé softwaru můžou nabízet svoje řešením zákazníkům Azure z celého světa.
Azure Marketplace kombinuje Microsoft Azure partnerských ekosystémů do sjednocené platformy, aby lépe poskytovala zákazníkům a partnerům. Chcete-li zobrazit produkty zabezpečení databáze dostupné v Azure Marketplace, můžete [Spustit hledání](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) .

## <a name="next-steps"></a>Další kroky

- [Zabezpečení služby Azure SQL Database](/azure/sql-database/sql-database-security-tutorial)
- [Služba Azure Security Center a Azure SQL Database](/azure/security-center/security-center-sql-database)
- [SQL Database detekce hrozeb](/azure/sql-database/sql-database-threat-detection)
- [Zlepšení výkonu SQL Database](/azure/sql-database/sql-database-performance-tutorial)
