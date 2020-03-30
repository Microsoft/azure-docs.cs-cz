---
title: Přehled zabezpečení databáze Azure| Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled funkcí zabezpečení databáze Azure.
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
ms.openlocfilehash: e5ed60ea59dc8cf19b8f9ca7e96777dbc6980171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906064"
---
# <a name="azure-database-security-overview"></a>Přehled zabezpečení databáze Azure

Zabezpečení je hlavním problémem pro správu databází a vždy bylo prioritou pro Azure SQL Database. Azure SQL Database podporuje zabezpečení připojení s pravidly brány firewall a šifrováním připojení. Podporuje ověřování pomocí uživatelského jména a hesla a ověřování Azure Active Directory (Azure AD), které používá identity spravované službou Azure Active Directory. Autorizace používá řízení přístupu na základě rolí.

Azure SQL Database podporuje šifrování prováděním šifrování a dešifrování databází v reálném čase, přidružené zálohy a soubory protokolu transakcí v klidovém stavu bez nutnosti změny v aplikaci.

Společnost Microsoft poskytuje další způsoby šifrování podnikových dat:

-   Šifrování na úrovni buněk je k dispozici k šifrování určitých sloupců nebo dokonce buněk dat pomocí různých šifrovacích klíčů.
-   Pokud potřebujete modul hardwarového zabezpečení nebo centrální správu hierarchie šifrovacích klíčů, zvažte použití Azure Key Vault s SQL Serverem ve virtuálním počítači Azure (VM).
-   Vždy šifrované (aktuálně ve verzi preview) zprůhlední šifrování pro aplikace. Umožňuje také klientům šifrovat citlivá data uvnitř klientských aplikací bez sdílení šifrovacích klíčů s databází SQL.

Azure SQL Database Auditing umožňuje podnikům zaznamenávat události do protokolu auditování ve službě Azure Storage. Auditování služby SQL Database je také integrované do služby Microsoft Power BI kvůli snadnějšímu procházení sestav a analýz.

Databáze Azure SQL lze pevně zabezpečit tak, aby splňovaly většinu regulačních nebo bezpečnostních požadavků, včetně HIPAA, ISO 27001/27002 a PCI DSS level 1. Aktuální seznam certifikací dodržování předpisů zabezpečení je k dispozici na [webu Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/services/).

Tento článek vás provede základy zabezpečení databází Microsoft Azure SQL pro strukturovaná, tabulková a relační data. Tento článek vám především pomůže začít s prostředky pro ochranu dat, řízením přístupu a proaktivním monitorováním.

## <a name="protection-of-data"></a>Ochrana údajů

SQL Database pomáhá zabezpečit vaše data tím, že poskytuje šifrování:

- Pro data v pohybu prostřednictvím [zabezpečení transportní vrstvy (TLS).](https://support.microsoft.com/kb/3135244)
- Pro data v klidovém stavu prostřednictvím [transparentního šifrování dat](https://go.microsoft.com/fwlink/?LinkId=526242).
- Pro data, která se používají prostřednictvím [vždy šifrované](https://msdn.microsoft.com/library/mt163865.aspx).

Existují i jiné možnosti šifrování dat:

-   [Šifrování na úrovni buňky](https://msdn.microsoft.com/library/ms179331.aspx) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
-   [Azure Key Vault s SQL Serverem ve virtuálním počítači Azure](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), pokud potřebujete modul hardwarového zabezpečení nebo centrální správu hierarchie šifrovacího klíče.

### <a name="encryption-in-motion"></a>Šifrování v pohybu

Běžným problémem pro všechny aplikace klient/server je potřeba ochrany osobních údajů při přesunu dat přes veřejné a soukromé sítě. Pokud data pohybující se po síti nejsou šifrována, je pravděpodobné, že je mohou zachytit a ukrást neoprávnění uživatelé. Pokud máte co do činění s databázovými službami, ujistěte se, že data jsou šifrována mezi databázovým klientem a serverem. Také se ujistěte, že data jsou šifrována mezi databázovými servery, které komunikují mezi sebou a s aplikacemi střední vrstvy.

Jedním z problémů při správě sítě je zabezpečení dat odesílaných mezi aplikacemi v nedůvěryhodné síti. [Pomocí tls/ssl](/windows-server/security/tls/transport-layer-security-protocol) můžete ověřovat servery a klienty a potom k šifrování zpráv mezi ověřenými stranami.

V procesu ověřování klient TLS/SSL odešle zprávu serveru TLS/SSL. Server odpoví informacemi, které server potřebuje k ověření. Klient a server provedou další výměnu klíčů relací a ověřovací dialog se ukončí. Po dokončení ověřování může komunikace zabezpečená protokolem SSL začít mezi serverem a klientem prostřednictvím symetrických šifrovacích klíčů vytvořených během procesu ověřování.

Všechna připojení k Azure SQL Database vyžadují šifrování (TLS/SSL) po celou dobu, kdy jsou data "na cestě" do a z databáze. Sql Database používá TLS/SSL k ověřování serverů a klientů a potom k šifrování zpráv mezi ověřenými stranami. 

V připojovacím řetězci aplikace je nutné zadat parametry pro šifrování připojení a nedůvěřovat certifikátu serveru. (To se provádí za vás, pokud zkopírujete připojovací řetězec z portálu Azure.) V opačném případě připojení neověří identitu serveru a bude náchylné k útokům "man-in-the-middle". Pro ovladač ADO.NET jsou například tyto parametry připojovacího řetězce `Encrypt=True` a `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Můžete provést několik opatření k zabezpečení databáze. Navrhněte například zabezpečený systém, šifrujte důvěrné datové zdroje a vytvořte bránu firewall kolem databázových serverů. Ale ve scénáři, kde jsou ukradena fyzická média (například jednotky nebo záložní pásky), může škodlivá strana pouze obnovit nebo připojit databázi a procházet data.

Jedním z řešení je šifrování citlivých dat v databázi a ochrana klíčů, které se používají k šifrování dat pomocí certifikátu. Toto řešení zabrání komukoli bez klíčů používat data, ale tento druh ochrany musí být plánovány.

Chcete-li tento problém vyřešit, SQL Server a SQL Database podporují [transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Transparentní šifrování dat šifruje sql server a sql database datové soubory, známé jako šifrovací data v klidovém stavu.

Transparentní šifrování dat pomáhá chránit před hrozbou škodlivé aktivity. Šifruje a dešifruje databáze, související zálohy a soubory transakčních protokolů v reálném čase, a přitom nevyžaduje změny v aplikaci.  

Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče nazývaného šifrovací klíč databáze. V databázi SQL je šifrovací klíč databáze chráněn integrovaným certifikátem serveru. Vestavěný certifikát serveru je jedinečný pro každý server databáze SQL.

Pokud je databáze ve vztahu geograficky dr. je na každém serveru chráněna jiným klíčem. Pokud jsou dvě databáze připojeny ke stejnému serveru, sdílejí stejný předdefinovaný certifikát. Společnost Microsoft automaticky otočí tyto certifikáty alespoň každých 90 dní. 

Další informace naleznete v tématu [Transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Šifrování v provozu (klient)

Většina narušení bezpečnosti dat zahrnuje krádež důležitých údajů, jako jsou čísla kreditních karet nebo osobně identifikovatelné informace. Databáze mohou být pokladnice citlivých informací. Mohou obsahovat osobní údaje zákazníků (jako jsou vnitrostátní identifikační čísla), důvěrné informace o hospodářské soutěži a duševní vlastnictví. Ztracená nebo odcizená data, zejména zákaznická data, mohou mít za následek poškození značky, konkurenční nevýhodu a vážné pokuty - dokonce i soudní spory.

![Funkce Vždy šifrovaná ilustrovaná jako zámek a klíč](./media/database-security-overview/azure-database-fig1.png)

[Vždy šifrované](https://msdn.microsoft.com/library/mt163865.aspx) je funkce určená k ochraně citlivých dat uložených v databázích Azure SQL Database nebo SQL Server. Vždy šifrované umožňuje klientům šifrovat citlivá data uvnitř klientských aplikací a nikdy odhalit šifrovací klíče databázového stroje (SQL Database nebo SQL Server).

Vždy šifrované poskytuje oddělení mezi lidmi, kteří vlastní data (a mohou je zobrazit) a lidmi, kteří data spravují (ale neměli by mít přístup). Pomáhá zajistit, aby místní správci databází, cloudové databázové operátory nebo jiní vysoce privilegovaní, ale neoprávnění uživatelé nemohli přistupovat k šifrovaným datům.

Kromě toho vždy šifrované zprůhlední šifrování pro aplikace. V klientském počítači je nainstalován vždy šifrovaný ovladač, který umožňuje automatické šifrování a dešifrování citlivých dat v klientské aplikaci. Ovladač šifruje data v citlivých sloupcích před předáním dat do databázového stroje. Ovladač automaticky přepíše dotazy tak, aby sémantiku do aplikace byly zachovány. Podobně ovladač transparentně dešifruje data uložená ve šifrovaných databázových sloupcích obsažených ve výsledcích dotazu.

## <a name="access-control"></a>Řízení přístupu

Chcete-li zajistit zabezpečení, sql database řídí přístup pomocí:

- Pravidla brány firewall, která omezují připojení podle adresy IP.
- Mechanismy ověřování, které vyžadují, aby uživatelé prokázali svou identitu.
- Autorizační mechanismy, které omezují uživatele na konkrétní akce a data.

### <a name="database-access"></a>Přístup k databázi

Ochrana dat začíná řízením přístupu k vašim datům. Datové centrum, které hostuje vaše data, spravuje fyzický přístup. Bránu firewall můžete nakonfigurovat pro správu zabezpečení v síťové vrstvě. Přístup můžete také řídit konfigurací přihlášení pro ověřování a definováním oprávnění pro role serveru a databáze.

#### <a name="firewall-and-firewall-rules"></a>Brána firewall a pravidla brány firewall

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) poskytuje relační databázovou službu pro Azure a další internetové aplikace. Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku. Další informace najdete v tématu [Přehled pravidel brány firewall služby Azure SQL Database](/azure/sql-database/sql-database-firewall-configure).

Služba Azure SQL Database je dostupná jenom přes port TCP 1433. Chcete-li získat přístup k databázi SQL z počítače, ujistěte se, že brána firewall klientského počítače umožňuje odchozí komunikaci TCP na portu TCP 1433. Pokud příchozí připojení nejsou potřebné pro jiné aplikace, blokovat je na portu TCP 1433.

#### <a name="authentication"></a>Ověřování

Ověřování se týká způsobu, jakým prokážete svou identitu při připojování k databázi. SQL Database podporuje dva typy ověřování:

-   **Ověřování serveru SQL Server**: Při vytvoření logické instance SQL, nazývané účet odběratele databáze SQL, je vytvořen jeden přihlašovací účet. Tento účet se připojuje pomocí [ověřování serveru SQL Server](/azure/sql-database/sql-database-security-overview) (uživatelské jméno a heslo). Tento účet je správcem v logické instanci na serveru a ve všech uživatelských databázích připojených k této instanci. Oprávnění účtu odběratele nelze omezit. Existovat může jenom jeden z těchto účtů.
-   **Ověřování Azure Active Directory:** [Ověřování Azure AD](/azure/sql-database/sql-database-aad-authentication) je mechanismus připojení k Azure SQL Database a Azure SQL Data Warehouse pomocí identit ve službě Azure AD. Můžete ji použít k centrální správě identit uživatelů databáze.

![Ověřování Azure AD pomocí databáze SQL](./media/database-security-overview/azure-database-fig2.png)

 Mezi výhody ověřování Azure AD patří:
  - Poskytuje alternativu k ověřování serveru SQL Server.
  - Pomáhá zastavit šíření identit uživatelů napříč databázovými servery a umožňuje střídání hesel na jednom místě.
  - Oprávnění databáze můžete spravovat pomocí externích skupin (Azure AD).
  - Může eliminovat ukládání hesel povolením integrovaného ověřování systému Windows a dalších forem ověřování, které podporuje Azure AD.

#### <a name="authorization"></a>Autorizace

[Autorizace](/azure/sql-database/sql-database-manage-logins) označuje, co může uživatel dělat v rámci databáze Azure SQL. Je řízena [členstvím](https://msdn.microsoft.com/library/ms189121) v databázové roli vašeho uživatelského účtu a [oprávněními na úrovni objektů](https://msdn.microsoft.com/library/ms191291.aspx). Autorizace je proces určení, ke kterým seknutelným prostředkům má objekt zabezpečení přístup a které operace jsou pro tyto prostředky povoleny.

### <a name="application-access"></a>Přístup k aplikaci

#### <a name="dynamic-data-masking"></a>Dynamické maskování dat

Servisní zástupce v call centru může identifikovat volající několika číslicemi jejich čísla sociálního pojištění nebo čísla kreditní karty. Tyto datové položky by však neměly být plně vystaveny zástupci služby.

Můžete definovat pravidlo maskování, které maskuje všechny kromě posledních čtyř číslic čísla sociálního pojištění nebo čísla platební karty v sadě výsledků libovolného dotazu.

![Maskování čísla odeslané mezi databází SQL a obchodními aplikacemi](./media/database-security-overview/azure-database-fig3.png)

Jako další příklad lze definovat vhodnou datovou masku, která chrání osobní údaje. Vývojář pak může dotazovat produkční prostředí pro účely řešení potíží bez porušení předpisů.

[Maskování dynamických dat sql database](/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje vystavení citlivých dat maskováním neprivilegovaným uživatelům. Dynamické maskování dat je podporované pro verzi V12 azure sql database.

[Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking) pomáhá zabránit neoprávněnému přístupu k citlivým datům tím, že umožňuje určit, kolik citlivých dat se má odhalit s minimálním dopadem na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.

> [!Note]
> Dynamické maskování dat může nakonfigurovat správce databáze Azure, správce serveru nebo pracovník zabezpečení role.

#### <a name="row-level-security"></a>Zabezpečení na úrovni řádků

Dalším běžným požadavkem na zabezpečení víceklientských databází je [zabezpečení na úrovni řádků](https://msdn.microsoft.com/library/dn765131.aspx). Tuto funkci můžete použít k řízení přístupu k řádkům v databázové tabulce na základě charakteristik uživatele, který provádí dotaz. (Příkladcharakteristiky jsou členství ve skupině a spuštění kontextu.)

![Zabezpečení na úrovni řádků umožňující uživateli přístup k řádkům v tabulce prostřednictvím klientské aplikace](./media/database-security-overview/azure-database-fig4.png)

Logika omezení přístupu se nachází v databázové vrstvě, nikoli mimo data v jiné aplikační vrstvě. Databázový systém použije omezení přístupu při každém pokusu o přístup k datům z libovolné vrstvy. Díky tomu je váš bezpečnostní systém spolehlivější a robustnější díky zmenšení plochy vašeho bezpečnostního systému.

Zabezpečení na úrovni řádků zavádí řízení přístupu založené na predikátu. Obsahuje flexibilní, centralizované hodnocení, které může vzít v úvahu metadata nebo jiná kritéria, která správce určí podle potřeby. Predikát se používá jako kritérium k určení, zda má uživatel odpovídající přístup k datům na základě atributů uživatele. Řízení přístupu založené na popiscích můžete implementovat pomocí řízení přístupu založeného na predikátu.

## <a name="proactive-monitoring"></a>Proaktivní monitorování

SQL Database pomáhá zabezpečit vaše data tím, že poskytuje možnosti *auditování* a *detekce hrozeb.*

### <a name="auditing"></a>Auditování

[Azure SQL Database auditování](/azure/sql-database/sql-database-auditing-get-started) zvyšuje vaši schopnost získat přehled o událostech a změnách, ke kterým dochází v rámci databáze. Příklady jsou aktualizace a dotazy proti datům.

Sql Database auditování sleduje události databáze a zapíše je do protokolu auditu v účtu úložiště Azure. Auditování vám může pomoci udržovat dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které mohou naznačovat obchodní problémy nebo podezření na porušení zabezpečení. Auditování umožňuje a usnadňuje dodržování standardů dodržování předpisů, ale nezaručuje dodržování předpisů.

Auditování databáze SQL můžete použít k:

- **Zachovat** záznam auditu vybraných událostí. Můžete definovat akce databáze, které se mají auditovat.
- **Zpráva** o databázové aktivitě. Můžete využít nakonfigurované sestavy a řídicí panel, abyste mohli rychle začít s vytvářením sestav aktivit a událostí.
- **Analyzujte** sestavy. Můžete vyhledávat podezřelé události, neobvyklou aktivitu a trendy.

Existují dvě metody auditování:

-   **Auditování objektů blob**: Protokoly se zapisují do úložiště objektů blob Azure. Toto je novější metoda auditování. Poskytuje vyšší výkon, podporuje auditování na úrovni objektů s vyšší rozlišovací schopností a je nákladově efektivnější.
-   **Auditování tabulek**: Protokoly se zapisují do úložiště azure table.

### <a name="threat-detection"></a>Detekce hrozeb

[Pokročilá ochrana před internetovými technologiemi pro Azure SQL Database](/azure/sql-database/sql-advanced-threat-protection) detekuje podezřelé aktivity, které označují potenciální ohrožení zabezpečení. Zjišťování hrozeb můžete použít k reakci na podezřelé události v databázi, jako je například injektáže SQL, jak k nim dochází. Poskytuje výstrahy a umožňuje použití azure sql database auditování prozkoumat podezřelé události.

![Detekce hrozeb pro databázi SQL a webovou aplikaci s externím útočníkem a škodlivým insiderem](./media/database-security-overview/azure-database-fig5.jpg)

SQL Advanced Threat Protection (ATP) poskytuje sadu pokročilých funkcí zabezpečení SQL, včetně zjišťování dat & klasifikace, posouzení ohrožení zabezpečení a detekce hrozeb. 

- [Klasifikace & zjišťování dat](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Posouzení zranitelnosti](/azure/sql-database/sql-vulnerability-assessment)  
- [Detekce hrozeb](/azure/sql-database/sql-database-threat-detection)

[Azure Database for PostgreSQL Advanced Threat Protection](/azure/postgresql/concepts-data-access-and-security-threat-protection) poskytuje novou vrstvu zabezpečení, která umožňuje detekovat potenciální hrozby a reagovat na ně tak, že poskytuje výstrahy zabezpečení na neobvyklé aktivity. Uživatelé obdrží upozornění na podezřelé databázové aktivity a potenciální chyby zabezpečení, stejně jako neobvyklé přístup k databázi a dotazy vzory. Pokročilá ochrana před internetovými hrozbami pro Azure Database for PostgreSQL integruje výstrahy s Azure Security Center. Mezi typy výstrah patří:

- Přístup z neobvyklého místa
- Přístup z neobvyklého datového centra Azure 
- Přístup z neznámého jistiny 
- Přístup z potenciálně škodlivé aplikace 
- Databáze Azure hrubou silou pro přihlašovací údaje PostgreSQL 

[Azure Database for MySQL Advanced Threat Protection](/azure/mysql/concepts-data-access-and-security-threat-protection) poskytuje ochranu podobnou postgreSQL rozšířené ochraně.  

## <a name="centralized-security-management"></a>Centralizovaná správa zabezpečení

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje integrované monitorování zabezpečení a správu zásad napříč vašimi předplatnými Azure. Pomáhá odhalovat hrozby, které by jinak mohly zůstat bez povšimnutí, a pracuje s širokým ekosystémem bezpečnostních řešení.

[Security Center](../../security-center/security-center-alerts-data-services.md) pomáhá chránit data v databázi SQL tím, že poskytuje přehled o zabezpečení všech serverů a databází. Pomocí centra zabezpečení můžete:

- Definujte zásady pro šifrování a auditování databáze SQL.
- Sledujte zabezpečení prostředků databáze SQL ve všech vašich předplatných.
- Rychle identifikujte a najakte problémy se zabezpečením.
- Integrujte výstrahy z [azure sql database detekce hrozeb](/azure/sql-database/sql-database-threat-detection).

Security Center podporuje přístup založený na rolích.

## <a name="sql-information-protection"></a>Ochrana informací SQL

[SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) automaticky zjišťuje a klasifikuje potenciálně citlivá data, poskytuje mechanismus označování pro trvalé označování citlivých dat s atributy klasifikace a poskytuje podrobný řídicí panel zobrazující stav klasifikace databáze.  

Kromě toho vypočítá citlivost sady výsledků dotazů SQL, takže dotazy, které extrahují citlivá data, mohou být explicitně auditovány a data mohou být chráněna. Další podrobnosti o ochraně informací SQL najdete v tématu Zjišťování a klasifikace dat databáze Azure SQL.

[Zásady ochrany informací SQL](/azure/security-center/security-center-info-protection-policy) můžete nakonfigurovat v Centru zabezpečení Azure.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace je online trh s aplikacemi a službami, který umožňuje začínajícím podnikům a nezávislým dodavatelům softwaru nabízet svá řešení zákazníkům Azure po celém světě.
Azure Marketplace kombinuje partnerské ekosystémy Microsoft Azure do jednotné platformy, která lépe slouží zákazníkům a partnerům. Můžete [spustit vyhledávání](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) pro zobrazení produktů zabezpečení databáze dostupných na Azure Marketplace.

## <a name="next-steps"></a>Další kroky

- [Zabezpečení databáze Azure SQL](/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center a služba Azure SQL Database](/azure/security-center/security-center-sql-database)
- [Detekce hrozeb databáze SQL](/azure/sql-database/sql-database-threat-detection)
- [Zlepšení výkonu databáze SQL](/azure/sql-database/sql-database-performance-tutorial)
