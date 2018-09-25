---
title: Azure database osvědčené postupy zabezpečení | Dokumentace Microsoftu
description: Tento článek poskytuje sadu osvědčených postupů pro zabezpečení služby Azure database.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: tomsh
ms.openlocfilehash: 0f738348dd0a000df8b1da299bb7b58ebc5a1165
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040089"
---
# <a name="azure-database-security-best-practices"></a>Osvědčené postupy zabezpečení Azure database
Zabezpečení je velmi důležité pro správu databází a byl vždy prioritu [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/). Vaše databáze je možné úzce svázat s pomáhají splňovat nejvíce zákonné nebo požadavky na zabezpečení, včetně HIPAA, ISO 27001/27002 a PCI DSS úrovně 1. Aktuální seznam certifikací dodržování předpisů zabezpečení je k dispozici na [webu Microsoft Trust Center](http://azure.microsoft.com/support/trust-center/services/). Můžete také umístit své databáze v určité síti datových center Azure na základě zákonných požadavků.

V tomto článku se podíváme na kolekci osvědčené postupy zabezpečení Azure database. Tyto osvědčené postupy jsou odvozeny z našich zkušeností s zabezpečení služby Azure database a prostředí zákazníků, jako je sami.

Pro každý osvědčeným postupem je vám vysvětlíme:

-   Jaký je doporučený postup
-   Proč chcete povolit tento osvědčený postup
-   Pokud chcete povolit osvědčený postup, co mohou být způsobeny
-   Jak lze zjistíte, jak povolit osvědčený postup

Tento článek osvědčené postupy zabezpečení Azure databáze je založen na stanovisko shody a možnostech platformy Azure a sady funkcí, protože existují v okamžiku, kdy byla zapsána v tomto článku. Názory a technologie v průběhu času měnit a tento článek bude aktualizován v pravidelných intervalech, aby tyto změny projevily.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Omezení přístupu k databázi pomocí pravidel brány firewall
Microsoft Azure SQL Database poskytuje relační databázovou službu pro Azure a další internetové aplikace. K zajištění zabezpečení přístupu, SQL Database – ovládací prvky přístupu pomocí:

- Pravidla brány firewall, které omezují připojení podle IP adresy.
- Ověřovací mechanismy, které vyžadují, aby uživatelé k prokázání své identity.
- Autorizačních mechanismů, které omezují uživatelů ke konkrétním akcím a data.

Že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.

Následující obrázek ukazuje, kde nastavit bránu firewall serveru ve službě SQL Database:

![Pravidla brány firewall](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Služba Azure SQL Database je dostupná jenom prostřednictvím portu TCP 1433. Chcete-li z počítače získat přístup k databázi SQL, ujistěte se, že brána firewall klientského počítače umožňuje odchozí komunikaci TCP na portu TCP 1433. Blokovat příchozí připojení na portu TCP 1433 pomocí pravidel brány firewall, pokud už nebudete potřebovat tato připojení pro jiné aplikace.

Jako součást procesu připojení se přesměrují připojení z virtuálních počítačů Azure na IP adresu a port, které jsou jedinečné pro každou roli pracovního procesu. Číslo portu je v rozsahu 11000 až 11999. Další informace o portech TCP najdete v tématu [porty nad 1433 pro technologii ADO.NET 4.5](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Další informace o pravidlech brány firewall pro SQL Database najdete v tématu [Pravidla brány firewall služby SQL Database](../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Kromě IP pravidel brány firewall slouží ke správě pravidel virtuální sítě. Pravidla virtuální sítě jsou založené na koncových bodech služby virtuální sítě. Pravidla virtuální sítě může být vhodnější než pravidla protokolu IP v některých případech. Další informace najdete v tématu [koncové body služeb virtuální sítě a pravidel pro službu Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Povolit ověřování databáze
SQL Database podporuje dva typy ověřování, ověřování SQL serveru a ověřování Azure AD.

### <a name="sql-server-authentication"></a>*Ověřování systému SQL Server*

Mezi výhody patří následující:

- Umožňuje SQL Database pro podporu prostředí s smíšené operační systémy, ve kterém všichni uživatelé nejsou ověřené domény Windows.
- Umožňuje SQL Database pro podporu starších aplikací a zadané partnerské aplikace, které vyžadují ověřování serveru SQL Server.
- Umožňuje uživatelům připojit se z neznámého nebo nedůvěryhodného domén. Příkladem je aplikace, kde zavedené zákazníci připojit s přiřazenou přihlášeních SQL serveru pro příjem stav jejich objednávky.
- Umožňuje SQL Database pro podporu webových aplikací, kde uživatelé vytvářet svoje vlastní identity.
- Vývojáři softwaru k distribuci aplikací pomocí hierarchie komplexní oprávnění na základě známých a předvolby přihlášení serveru SQL Server.

> [!NOTE]
> Ověřování serveru SQL Server nelze použít bezpečnostní protokol Kerberos.
>
>

Pokud používáte ověřování SQL serveru, musíte mít:

- Správa silné přihlašovací údaje.
- Ochrana přihlašovacích údajů v připojovacím řetězci.
- (Potenciálně) ochranu přihlašovacích údajů předaných přes síť z webového serveru do databáze. Další informace najdete v tématu [postupy: připojení k SQL serveru pomocí ověřování SQL v technologii ASP.NET 2.0](https://msdn.microsoft.com/library/ms998300.aspx).

### <a name="azure-active-directory-ad-authentication"></a>*Ověřování Azure Active Directory (AD)*
Ověřování Azure AD je mechanismus pro připojení ke službě Azure SQL Database a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit ve službě Azure AD. Pomocí ověřování Azure AD můžete spravovat identity uživatelů databáze a další služby Microsoftu v jednom centrálním místě. Centrální správa ID zajišťuje centrální místo pro správu uživatelů databáze a zjednodušuje správu oprávnění.

> [!NOTE]
> Doporučujeme vám používat ověřování Azure AD prostřednictvím použití ověřování systému SQL Server.
>
>

Mezi výhody patří následující:

- To poskytuje alternativu k ověřování serveru SQL Server.
- Pomáhá zastavit šíření identit uživatelů více databázových serverů.
- To umožňuje rotace hesla na jednom místě.
- Zákazníci můžou spravovat oprávnění k databázi pomocí skupin externí (Azure AD).
- Ukládání hesel se může eliminovat tím, že integrované ověřování Windows a další formy ověřování podporovaných službou Azure Active Directory.
- Uživatelé databáze s omezením používá k ověření identity na úrovni databáze.
- Pro aplikace, které se připojují ke službě SQL Database podporuje ověřování založené na tokenech.
- Podporuje služby AD FS (federation domény) nebo ověřování nativní uživatele a hesla pro místní instance služby Azure Active Directory bez synchronizace domény.
- Azure AD podporuje připojení z SQL Server Management Studio, které pomocí univerzálního ověřování Active Directory, která zahrnuje ověření službou Multi-Factor Authentication. Nabízí silné ověřování s celou řadou možností ověření služby Multi-Factor Authentication – telefonní hovor, textová zpráva, čipové karty s PIN kódu nebo oznámení přes mobilní aplikaci. Další informace najdete v tématu [podpora nástroje SSMS pro ověřování Azure Multi-Factor Authentication AD s SQL Database a SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Postup konfigurace obsahuje následující kroky konfigurace a používání ověřování Azure AD:

- Vytvoření a naplnění služby Azure AD.
- Volitelné: Přiřadit nebo změnit instanci Active Directory, který nemá aktuálně přiřazen k vašemu předplatnému Azure.
- Vytvoření správce Azure Active Directory pro službu Azure SQL Database nebo [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Konfigurace klientských počítačů.
- Vytvořte uživatele databáze s omezením v databázi namapované na identit Azure AD.
- Připojení k databázi pomocí identit Azure AD.

Můžete najít podrobné informace v [ověřování pomocí Azure Active Directory pro ověřování pomocí SQL Database, mi nebo SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption"></a>Ochrana dat pomocí šifrování
[Azure SQL Database transparentní šifrování dat](https://msdn.microsoft.com/library/dn948096.aspx) pomáhá chránit data na disku a chrání před neoprávněným přístupem k hardwaru. Provede v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory protokolů transakcí v klidovém stavu bez nutnosti změny aplikace. Transparentní šifrování dat šifruje úložiště celou databázi pomocí symetrický klíč s názvem šifrovací klíč databáze.

I v případě, že celé úložiště je šifrované, je potřeba taky k šifrování samotná databáze. Toto je implementace obrany v přístupu k ochraně dat. Pokud používáte Azure SQL Database a chcete chránit citlivá data (například kreditních karet nebo čísla sociálního pojištění), můžete šifrování databází s FIPS 140-2 ověřené oborového 256bitového šifrování AES. Toto šifrování splňuje požadavky na mnoho oborových standardů (například HIPAA a PCI).

Soubory související s nástrojem [vyrovnávací paměti rozšíření fondu (funkce BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) při šifrování databáze s použitím transparentního šifrování dat nejsou šifrována. Je nutné použít šifrování na úrovni systému souborů nástrojů, jako je [BitLocker](https://technet.microsoft.com/library/cc732774) nebo [systém souborů EFS (ENCRYPTING File System)]() funkce BPE souborů.

Protože autorizovaným uživatelům s jako správce zabezpečení nebo správce databáze můžete přístup k datům, i když je databáze šifrovaná pomocí transparentního šifrování dat, postupujte také tato doporučení:

- Povolte ověřování systému SQL Server na úrovni databáze.
- Ověřování pomocí služby Azure AD s použitím [role RBAC](../role-based-access-control/overview.md).
- Ujistěte se, že uživatelé a aplikace k ověření používat samostatné účty. Tímto způsobem můžete omezit oprávnění udělená uživatelům a aplikacím a snížit rizika škodlivých aktivit.
- Implementace zabezpečení na úrovni databáze s použitím pevné databázové role (jako je například db_datareader a db_datawriter). Nebo můžete vytvořit vlastní role pro vaši aplikaci udělit explicitní oprávnění pro vybrané databázové objekty.

Existují i jiné možnosti šifrování dat:

- [Šifrování na úrovni buňky](https://msdn.microsoft.com/library/ms179331.aspx) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
- [Funkce Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx), která umožňuje klientům šifrovat citlivá data v klientských aplikacích a nikdy odhalit šifrovací klíče pro databázový stroj (SQL Database nebo SQL Server). V důsledku toho funkce Always Encrypted umožňuje oddělit mezi těmi, kdo data vlastní (a mohou je zobrazovat) a těmi, kdo spravovat data (ale nemají žádný přístup).
- [Zabezpečení na úrovní řádků](https://msdn.microsoft.com/library/dn765131), což umožňuje řízení přístupu k řádkům v databázové tabulce na základě charakteristiky uživatele, který spouští dotaz. (Příklad vlastnosti jsou skupiny členství a spuštění kontextu.)

Organizace, které nepoužívají šifrování na úrovni databáze může být více náchylné k útokům, které se ohrožení dat umístěných v databázích SQL.

Další informace o SQL Database transparentní šifrování dat najdete v článku [transparentního šifrování dat s využitím Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Povolení auditování databáze
Auditování instance databázového stroje SQL Server nebo jednotlivé databáze zahrnuje sledování a protokolování událostí. Pro SQL Server můžete vytvořit audity, které obsahují specifikace událostí na úrovni serveru a specifikaci události na úrovni databáze. Auditované události do protokolů událostí nebo soubory auditu je možné napsat.

Existuje několik úrovní auditování pro SQL Server, v závislosti na státní správy nebo požadavky normy pro vaši instalaci. Auditování SQL serveru poskytuje nástroje a procesy pro povolení, ukládání a zobrazení audity na různých serveru a databázových objektů.

[Auditování služby Azure SQL Database](../sql-database/sql-database-auditing.md) sleduje události databáze a zapisuje je do auditu protokolu ve vašem účtu úložiště Azure.

Auditování pomáhá zajistit dodržování legislativních předpisů, porozumět databázové aktivitě a vyhledání nesrovnalostí a anomálií, které může ukazovat na možné problémy obchodního charakteru nebo na narušení zabezpečení. Auditování usnadňuje dodržování standardů dodržování předpisů, ale nezaručuje dodržování předpisů.

Další informace o auditování databáze a jak se dá povolit, najdete v článku [Začínáme s auditem SQL database](../sql-database/sql-database-auditing.md).

## <a name="enable-database-threat-detection"></a>Povolení detekce hrozeb databáze
Ochrana před internetovými útoky jde nad rámec zjišťování. Ochrana před internetovými útoky pro databázi zahrnuje:

- Zjišťování a klasifikace vaše nejcitlivější data, takže data můžete chránit.
- Implementace zabezpečené konfigurace ve vaší databázi, budete moci chránit vaši databázi.
- Detekce a reakce na potenciální hrozby, když k nim dojde, což vám umožní rychle reagovat a napravit.

**Osvědčený postup**: zjistit, klasifikovat a označovat citlivá data ve vašich databázích.   
**Podrobnosti o**: klasifikace dat ve službě SQL database tím, že [Data zjišťování a klasifikace](../sql-database/sql-database-data-discovery-and-classification.md) ve službě Azure SQL Database. Můžete sledovat přístup k citlivým datům na řídicím panelu Azure nebo stáhnout sestavy.

**Osvědčený postup**: sledování ohrožení zabezpečení databáze, tak můžete aktivně zlepšit zabezpečení databáze.   
**Podrobnosti o**: použití Azure SQL Database [sken posouzení ohrožení zabezpečení](../sql-database/sql-vulnerability-assessment.md) službu, která hledá potenciální ohrožení zabezpečení databáze. Služba využívá znalostní bázi pravidel, která příznak ohrožení zabezpečení a odchylky od osvědčených postupů, jako jsou chybné konfigurace, nadměrná oprávnění a nechráněné citlivá data zobrazit.

Pravidla jsou založené na osvědčených postupů Microsoftu a zaměřuje se na problémy se zabezpečením, které představovat největší rizika pro vaši databázi a její cennými daty. Pokrývají problémy na úrovni databáze a problémy zabezpečení na úrovni serveru, jako je nastavení brány firewall serveru a oprávnění na úrovni serveru. Tato pravidla také představovat mnoho požadavků z správními orgány podle jejich standardy pro dodržování předpisů.

**Osvědčený postup**: povolení detekce hrozeb.  
**Podrobnosti o**: povolení Azure SQL Database [detekce hrozeb](../sql-database/sql-database-threat-detection.md) k získání výstrah zabezpečení a doporučení o tom, jak zkoumat a zmírnit hrozby. Získání výstrah o podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útocích prostřednictvím injektáže SQL, i neobvyklé databázové přístup a dotazy.

[Rozšířená ochrana před internetovými útoky](../sql-database/sql-advanced-threat-protection.md) je jednotný balíček pro pokročilé funkce zabezpečení SQL. To zahrnuje služby již bylo zmíněno dříve: Data zjišťování a klasifikace, posouzení ohrožení zabezpečení a detekce hrozeb. Pro povolení a správa těchto možností nabízí na jednom místě.

Tyto funkce vám pomůže:

- Splňovat standardy ochrany osobních údajů data a požadavky na dodržování legislativních předpisů.
- Řízení přístupu k databázím a posílení jejich zabezpečení.
- Monitorování prostředí dynamické databáze, kde jsou těžko sledují změny.
- Detekovat a reagovat na potenciální hrozby.

Detekce hrozeb navíc integruje výstrahy se službou Azure Security Center pro přehledu o stavu zabezpečení všech vašich prostředků Azure.

## <a name="next-steps"></a>Další postup
Zobrazit [osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md) pro další doporučené postupy zabezpečení, mají použít, když jste návrhu, nasazení a správa cloudových řešení pomocí služby Azure.

Jsou následující prostředky vám poskytnou další obecné informace o zabezpečení Azure a související služby Microsoftu:
* [Blog týmu Azure zabezpečení](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější vydání v Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – tam, kde mohou být hlášeny chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo prostřednictvím e-mailu secure@microsoft.com
