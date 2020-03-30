---
title: Doporučené postupy zabezpečení databáze – Microsoft Azure
description: Tento článek obsahuje sadu osvědčených postupů pro zabezpečení databáze Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 0f2e0257c5bf855b0d9be61c43b68b4e30b3d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125110"
---
# <a name="azure-database-security-best-practices"></a>Osvědčené postupy zabezpečení databáze v Azure
Tento článek popisuje osvědčené postupy pro zabezpečení databáze.

Osvědčené postupy jsou založeny na konsensu názoru a pracují s aktuálními funkcemi platformy Azure a sadami funkcí. Názory a technologie se v průběhu času mění a tento článek je pravidelně aktualizován, aby tyto změny odrážel.

## <a name="secure-databases"></a>Zabezpečené databáze
Zabezpečení je hlavním problémem pro správu databází a vždy bylo prioritou pro [Azure SQL Database](../../sql-database/index.yml). Vaše databáze mohou být pevně zabezpečeny, aby pomohly splnit většinu regulačních nebo bezpečnostních požadavků, včetně HIPAA, ISO 27001/27002 a PCI DSS Level 1. Aktuální seznam certifikací dodržování předpisů zabezpečení je k dispozici na [webu Microsoft Trust Center](https://azure.microsoft.com/support/trust-center/services/). Můžete také umístit databáze do konkrétních datových center Azure na základě regulačních požadavků.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Omezení přístupu k databázi pomocí pravidel brány firewall
Microsoft Azure SQL Database poskytuje relační databázovou službu pro Azure a další internetové aplikace. Chcete-li zajistit zabezpečení přístupu, řídí databáze SQL přístup pomocí:

- Pravidla brány firewall, která omezují připojení podle adresy IP.
- Mechanismy ověřování, které vyžadují, aby uživatelé prokázali svou identitu.
- Autorizační mechanismy, které omezují uživatele na konkrétní akce a data.

Brány firewall znemožňují veškerý přístup k databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.

Následující obrázek znázorňuje, kde nastavíte serverovou bránu firewall v databázi SQL:

![Pravidla brány firewall](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

Služba Azure SQL Database je dostupná jenom přes port TCP 1433. Chcete-li získat přístup k databázi SQL z počítače, ujistěte se, že brána firewall klientského počítače umožňuje odchozí komunikaci TCP na portu TCP 1433. Blokování příchozích připojení na portu TCP 1433 pomocí pravidel brány firewall, pokud tato připojení nepotřebujete pro jiné aplikace.

Jako součást procesu připojení jsou připojení z virtuálních počítačů Azure přesměrována na IP adresu a port, které jsou jedinečné pro každou roli pracovního procesu. Číslo portu je v rozsahu 11000 až 11999. Další informace o portech TCP naleznete [v tématu Porty nad 1433 pro ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Další informace o pravidlech brány firewall pro SQL Database najdete v tématu [Pravidla brány firewall služby SQL Database](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Kromě pravidel IP spravuje pravidla virtuální sítě také brána firewall. Pravidla virtuální sítě jsou založená na koncových bodech služby virtuální sítě. Pravidla virtuální sítě může být vhodnější než pravidla IP v některých případech. Další informace najdete v [tématu koncové body virtuální síťové služby a pravidla pro Azure SQL Database](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Povolení ověřování v databázi
SQL Database podporuje dva typy ověřování, ověřování serveru SQL Server a ověřování Azure AD.

### <a name="sql-server-authentication"></a>*Ověřování serveru SQL Server*

Mezi jeho výhody patří následující:

- Umožňuje sql database pro podporu prostředí se smíšenými operačními systémy, kde všichni uživatelé nejsou ověřeni doménou systému Windows.
- Umožňuje databázi SQL pro podporu starších aplikací a aplikací dodaných partnery, které vyžadují ověřování serveru SQL Server.
- Umožňuje uživatelům připojit se z neznámých nebo nedůvěryhodných domén. Příkladem je aplikace, kde se se sídlem zákazníci připojit s přiřazené SQL Server přihlášení získat stav jejich objednávek.
- Umožňuje databázi SQL podporovat webové aplikace, kde uživatelé vytvářejí své vlastní identity.
- Umožňuje vývojářům softwaru distribuovat své aplikace pomocí komplexní hierarchie oprávnění založené na známých přednastavených přihlášeních serveru SQL Server.

> [!NOTE]
> Ověřování serveru SQL Server nemůže použít protokol zabezpečení protokolu Kerberos.

Pokud používáte ověřování serveru SQL Server, musíte:

- Spravujte silná pověření sami.
- Chraňte pověření v připojovacím řetězci.
- (Potenciálně) chránit pověření předaná přes síť z webového serveru do databáze. Další informace naleznete [v tématu Postup: Připojení k serveru SQL Server pomocí ověřování SQL v ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Ověřování služby Azure Active Directory (AD)*
Ověřování Azure AD je mechanismus připojení k Azure SQL Database a [SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit ve službě Azure AD. Pomocí ověřování Azure AD můžete spravovat identity uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění. Centrální správa ID poskytuje jediné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění.

> [!NOTE]
> Doporučujeme použití ověřování Azure AD přes použití ověřování SQL Server.

Mezi jeho výhody patří následující:

- Poskytuje alternativu k ověřování serveru SQL Server.
- Pomáhá zastavit šíření identit uživatelů napříč databázovými servery.
- Umožňuje střídání hesel na jednom místě.
- Zákazníci mohou spravovat oprávnění databáze pomocí externích skupin (Azure AD).
- Umožňuje integrované ověřování systému Windows a další formy ověřování podporované službou Azure Active Directory.
- Používá obsažené uživatele databáze k ověření identit na úrovni databáze.
- Podporuje ověřování založené na tokenech pro aplikace, které se připojují k databázi SQL.
- Podporuje ad FS (federace domény) nebo nativní ověřování uživatelů/hesel pro místní instanci služby Azure Active Directory bez synchronizace domény.
- Azure AD podporuje připojení z SQL Server Management Studio, které používají univerzální ověřování služby Active Directory, které zahrnuje vícefaktorové ověřování. Vícefaktorové ověřování poskytuje silné ověřování s řadou možností ověření – telefonní hovor, textová zpráva, čipové karty s kódem PIN nebo oznámení mobilní aplikace. Další informace naleznete v [tématu Podpora SSMS pro azure ad vícefaktorové ověřování s databází SQL a SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Kroky konfigurace zahrnují následující postupy konfigurace a použití ověřování Azure AD:

- Vytvořte a naplňte Azure AD.
- Volitelné: Přidružte nebo změňte instanci služby Active Directory, která je aktuálně přidružená k vašemu předplatnému Azure.
- Vytvořte správce Služby Azure Active Directory pro Azure SQL Database nebo [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Nakonfigurujte klientské počítače.
- Vytvořte obsažené uživatele databáze v databázi mapované na identity Azure AD.
- Připojte se k databázi pomocí identit Azure AD.

Podrobné informace najdete v [aplikaci Use Azure Active Directory authentication for authentication with SQL Database, Managed Instance nebo SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Chraňte svá data pomocí šifrování a zabezpečení na úrovni řádků
[Transparentní šifrování dat azure SQL Database](../../sql-database/transparent-data-encryption-azure-sql.md) pomáhá chránit data na disku a chrání před neoprávněným přístupem k hardwaru. Šifruje a dešifruje databáze, související zálohy a soubory transakčních protokolů v reálném čase, a přitom nevyžaduje změny v aplikaci. Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče nazývaného šifrovací klíč databáze.

I když je celé úložiště zašifrováno, je důležité také šifrovat samotnou databázi. Jedná se o implementaci přístupu ochrany údajů do hloubky. Pokud používáte Azure SQL Database a chcete chránit citlivá data (například čísla kreditních karet nebo sociálního zabezpečení), můžete šifrovat databáze pomocí 256bitového šifrování AES ověřeného FIPS 140-2. Toto šifrování splňuje požadavky mnoha oborových standardů (například HIPAA a PCI).

Soubory související s [rozšířením fondu vyrovnávací paměti (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nejsou šifrovány při šifrování databáze pomocí transparentního šifrování dat. Pro soubory související s BPE je nutné použít nástroje pro šifrování na úrovni systému souborů, jako je [nástroj BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) nebo [systém souborů EFS (Efs).](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10))

Vzhledem k tomu, že oprávněný uživatel, jako je správce zabezpečení nebo správce databáze, má přístup k datům i v případě, že je databáze šifrována transparentním šifrováním dat, měli byste také dodržovat tato doporučení:

- Povolte ověřování serveru SQL Server na úrovni databáze.
- Použití ověřování Azure AD pomocí [rolí RBAC](/azure/role-based-access-control/overview).
- Ujistěte se, že uživatelé a aplikace používají k ověření samostatné účty. Tímto způsobem můžete omezit oprávnění udělená uživatelům a aplikacím a snížit riziko škodlivé aktivity.
- Implementujte zabezpečení na úrovni databáze pomocí pevných databázových rolí (například db_datareader nebo db_datawriter). Nebo můžete vytvořit vlastní role pro vaši aplikaci udělit explicitní oprávnění pro vybrané databázové objekty.

Další způsoby zabezpečení dat navštivte na příkladu:

- [Šifrování na úrovni buňky](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
- [Vždy šifrované](/sql/relational-databases/security/encryption/always-encrypted-database-engine), který umožňuje klientům šifrovat citlivá data uvnitř klientských aplikací a nikdy odhalit šifrovací klíče databázového stroje (SQL Database nebo SQL Server). V důsledku toho vždy šifrované poskytuje oddělení mezi těmi, kteří vlastní data (a můžete zobrazit) a ti, kteří spravují data (ale neměl i přístup).
- [Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security), které umožňuje zákazníkům řídit přístup k řádkům v databázové tabulce na základě charakteristik uživatele, který provádí dotaz. (Příkladcharakteristiky jsou členství ve skupině a spuštění kontextu.)

Organizace, které nepoužívají šifrování na úrovni databáze, mohou být náchylnější k útokům, které ohrožují data umístěná v databázích SQL.

Další informace o transparentním šifrování dat databáze SQL najdete v článku [Transparentní šifrování dat pomocí Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Povolit auditování databáze
Auditování instance databázového stroje SQL Server nebo jednotlivé databáze zahrnuje sledování a protokolování událostí. Pro SQL Server můžete vytvořit audity, které obsahují specifikace pro události na úrovni serveru a specifikace pro události na úrovni databáze. Auditované události lze zapsat do protokolů událostí nebo do auditovaných souborů.

Existuje několik úrovní auditování pro SQL Server, v závislosti na vládní nebo standardy požadavky pro vaši instalaci. Sql Server auditování poskytuje nástroje a procesy pro povolení, ukládání a zobrazení auditů na různých serverových a databázových objektů.

[Azure SQL Database auditování](/azure/sql-database/sql-database-auditing) sleduje události databáze a zapíše je do protokolu auditu ve vašem účtu úložiště Azure.

Auditování vám může pomoci udržovat dodržování předpisů, porozumět databázové aktivitě a najít nesrovnalosti a anomálie, které by mohly ukazovat na obchodní problémy nebo porušení zabezpečení. Auditování usnadňuje dodržování standardů dodržování předpisů, ale nezaručuje dodržování předpisů.

Další informace o auditování databáze a jeho povolení najdete [v tématu Začínáme s auditováním databáze SQL](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Povolit zjišťování ohrožení databáze
Ochrana před hrozbami přesahuje detekci. Ochrana před hrozbami databáze zahrnuje:

- Zjišťování a klasifikace nejcitlivějších dat, abyste mohli svá data chránit.
- Implementace zabezpečených konfigurací v databázi, abyste mohli databázi chránit.
- Detekce potenciálních hrozeb a reakce na ně, abyste mohli rychle reagovat a najakovat.

**Osvědčený postup:** Zjišťování, klasifikace a označování citlivých dat v databázích.   
**Detail**: Klasifikujte data v databázi SQL povolením [zjišťování dat a klasifikace](/azure/sql-database/sql-database-data-discovery-and-classification) v Azure SQL Database. Přístup k citlivým datům můžete sledovat na řídicím panelu Azure nebo stahovat sestavy.

**Osvědčený postup**: Sledování slabých míst databáze, abyste mohli proaktivně zlepšit zabezpečení databáze.   
**Podrobnosti**: Použijte službu Azure SQL Database [Vulnerability Assessment,](/azure/sql-database/sql-vulnerability-assessment) která hledá potenciální chyby zabezpečení databáze. Služba využívá znalostní bázi pravidel, která označují chyby zabezpečení a zobrazují odchylky od osvědčených postupů, jako jsou chybné konfigurace, nadměrná oprávnění a nechráněná citlivá data.

Pravidla jsou založena na osvědčených postupech společnosti Microsoft a zaměřují se na problémy se zabezpečením, které představují největší rizika pro vaši databázi a její cenná data. Zahrnují problémy na úrovni databáze i problémy se zabezpečením na úrovni serveru, jako je nastavení brány firewall serveru a oprávnění na úrovni serveru. Tato pravidla také představují mnoho požadavků regulačních orgánů na splnění jejich norem pro dodržování předpisů.

**Osvědčený postup**: Povolit detekci hrozeb.  
**Podrobnosti**: Povolte Azure SQL Database [Threat Detection](/azure/sql-database/sql-database-threat-detection) získat výstrahy zabezpečení a doporučení, jak prozkoumat a zmírnit hrozby. Získáte výstrahy o podezřelých databázových aktivitách, potenciálních chybách zabezpečení a útocích injektáže SQL, stejně jako neobvyklé vzory přístupu k databázi a dotazů.

[Advanced Threat Protection](/azure/sql-database/sql-advanced-threat-protection) je jednotný balíček pro pokročilé možnosti zabezpečení SQL. Zahrnuje výše uvedené služby: zjišťování a klasifikace dat, posouzení ohrožení zabezpečení a detekce hrozeb. Poskytuje jediné umístění pro povolení a správu těchto funkcí.

Povolení těchto funkcí vám pomůže:

- Splňte standardy ochrany osobních údajů a požadavky na dodržování předpisů.
- Ovládejte přístup k databázím a zpevněte jejich zabezpečení.
- Sledujte prostředí dynamické databáze, kde je obtížné sledovat změny.
- Detekujte potenciální hrozby a reagujte na ně.

Kromě toho detekce hrozeb integruje výstrahy s Azure Security Center pro centrální zobrazení stavu zabezpečení všech vašich prostředků Azure.

## <a name="next-steps"></a>Další kroky
Podívejte se na [osvědčené postupy a vzory zabezpečení Azure, kde](best-practices-and-patterns.md) najdete další doporučené postupy zabezpečení, které se používají při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovějších informacích o azure securityu
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde lze nahlásit chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo e-mailemsecure@microsoft.com
