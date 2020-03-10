---
title: Osvědčené postupy zabezpečení databáze – Microsoft Azure
description: Tento článek poskytuje sadu osvědčených postupů pro zabezpečení Azure Database.
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
ms.openlocfilehash: 316c3ef3c5bd16b52291029924d04fc159375bc8
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943667"
---
# <a name="azure-database-security-best-practices"></a>Osvědčené postupy zabezpečení databáze v Azure
Tento článek popisuje osvědčené postupy pro zabezpečení databáze.

Osvědčené postupy jsou založené na shodě na základě konsensu a pracují s funkcemi a sadami funkcí platformy Azure. Názory a technologie se v průběhu času mění a tento článek se pravidelně aktualizuje, aby odrážel tyto změny.

## <a name="secure-databases"></a>Zabezpečení databází
Zabezpečení je nejdůležitějším problémem při správě databází a je vždycky prioritou pro [Azure SQL Database](../../sql-database/index.yml). Vaše databáze mohou být pevně zabezpečeny, aby bylo možné plnit většinu regulativních a bezpečnostních požadavků, včetně HIPAA, ISO 27001/27002 a PCI DSS úrovně 1. Aktuální seznam certifikace dodržování předpisů zabezpečení je k dispozici na [webu Centrum zabezpečení společnosti Microsoft](https://azure.microsoft.com/support/trust-center/services/). Své databáze můžete také umístit do konkrétních datových center Azure na základě regulativních požadavků.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Omezení přístupu k databázi pomocí pravidel brány firewall
Microsoft Azure SQL Database poskytuje službu relačních databází pro Azure a další internetové aplikace. Pro zajištění zabezpečení přístupu SQL Database řídí přístup pomocí:

- Pravidla brány firewall, která omezují připojení podle IP adresy.
- Mechanismy ověřování, které vyžadují, aby si uživatelé mohli prokázat svoji identitu.
- Autorizační mechanismy, které omezují uživatele na konkrétní akce a data.

Brány firewall zabraňují všem přístupům k databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.

Následující obrázek ukazuje, kde můžete nastavit bránu firewall serveru v SQL Database:

![Pravidla brány firewall](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

Služba Azure SQL Database je dostupná jenom přes port TCP 1433. Chcete-li získat přístup k databázi SQL z počítače, zajistěte, aby brána firewall klientského počítače umožňovala odchozí komunikaci TCP na portu TCP 1433. Zablokuje příchozí připojení na portu TCP 1433 pomocí pravidel brány firewall, pokud tato připojení nepotřebujete pro jiné aplikace.

V rámci procesu připojení se připojení z virtuálních počítačů Azure přesměrují na IP adresu a port, které jsou pro každou roli pracovního procesu jedinečné. Číslo portu je v rozsahu 11000 až 11999. Další informace o portech TCP najdete v tématu [porty vyšší než 1433 pro ADO.NET 4,5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Další informace o pravidlech brány firewall pro SQL Database najdete v tématu [Pravidla brány firewall služby SQL Database](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Kromě pravidel protokolu IP spravuje brána firewall pravidla virtuální sítě. Pravidla virtuální sítě jsou založená na koncových bodech služby virtuální sítě. Pravidla virtuální sítě mohou být v některých případech vhodnější pro pravidla protokolu IP. Další informace najdete v tématu [koncové body služby virtuální sítě a pravidla pro Azure SQL Database](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Povolit ověřování databáze
SQL Database podporuje dva typy ověřování, ověřování SQL Server a ověřování Azure AD.

### <a name="sql-server-authentication"></a>*Ověřování SQL Server*

Mezi jeho výhody patří následující:

- Umožňuje SQL Database podporovat prostředí se smíšenými operačními systémy, ve kterých se všichni uživatelé neověřují v doméně systému Windows.
- Umožňuje SQL Database podporovat starší aplikace a aplikace poskytované partnerským aplikacím, které vyžadují SQL Server ověřování.
- Umožňuje uživatelům připojit se z neznámých nebo nedůvěryhodných domén. Příkladem může být aplikace, ke které se zákazníci připojí pomocí přiřazených SQL Server přihlášení, aby získali stav svých objednávek.
- Umožňuje SQL Database podporovat webové aplikace, kde uživatelé vytvářejí vlastní identity.
- Umožňuje vývojářům softwaru distribuovat své aplikace pomocí komplexní hierarchie oprávnění na základě známých, přednastavených SQL Serverch přihlášení.

> [!NOTE]
> Ověřování SQL Server nemůže používat protokol zabezpečení Kerberos.

Pokud používáte ověřování SQL Server, musíte:

- Spravujte silné přihlašovací údaje sami.
- Zabezpečte přihlašovací údaje v připojovacím řetězci.
- (Potenciálně) chránit přihlašovací údaje předané sítí z webového serveru do databáze. Další informace najdete v tématu [Postup: připojení k SQL Server pomocí ověřování SQL v ASP.NET 2,0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Ověřování Azure Active Directory (AD)*
Ověřování Azure AD je mechanismus připojení k Azure SQL Database a [SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit ve službě Azure AD. Pomocí ověřování Azure AD můžete spravovat identity uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění. Centrální správa ID poskytuje jediné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění.

> [!NOTE]
> Pro použití ověřování SQL Server doporučujeme používat ověřování Azure AD.

Mezi jeho výhody patří následující:

- Nabízí alternativu k ověřování SQL Server.
- Pomáhá zastavit šíření identit uživatelů napříč databázovými servery.
- Umožňuje otočení hesla na jednom místě.
- Zákazníci můžou spravovat databázová oprávnění pomocí skupin externích (Azure AD).
- Může eliminovat ukládání hesel povolením integrovaného ověřování systému Windows a dalších forem ověřování, které Azure Active Directory podporuje.
- Používá uživatele databáze s omezením k ověřování identit na úrovni databáze.
- Podporuje ověřování na základě tokenů pro aplikace, které se připojují k SQL Database.
- Podporuje AD FS (doménová federace) nebo nativní ověřování uživatele a hesla pro místní instanci Azure Active Directory bez synchronizace domén.
- Azure AD podporuje připojení z SQL Server Management Studio, která používají univerzální ověřování služby Active Directory, což zahrnuje Multi-Factor Authentication. Multi-Factor Authentication poskytuje silné ověřování s řadou možností ověřování – telefonní hovor, textová zpráva, čipové karty s kódem PIN nebo oznámením o mobilní aplikaci. Další informace najdete v tématu [Podpora SSMS pro Azure AD Multi-Factor Authentication s SQL Database a SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Postup konfigurace zahrnuje následující postupy konfigurace a používání ověřování Azure AD:

- Vytvoření a naplnění služby Azure AD.
- Volitelné: přidružte nebo změňte instanci služby Active Directory, která je aktuálně přidružená k vašemu předplatnému Azure.
- Vytvořte správce Azure Active Directory pro Azure SQL Database nebo [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Nakonfigurujte klientské počítače.
- Vytvořte uživatele databáze s omezením ve vaší databázi namapované na identity Azure AD.
- Připojte se k databázi pomocí identit Azure AD.

Podrobné informace najdete v [Azure Active Directory ověřování pro ověřování pomocí SQL Database, spravované instance nebo SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Ochrana dat pomocí šifrování a zabezpečení na úrovni řádků
[Azure SQL Database transparentní šifrování dat](../../sql-database/transparent-data-encryption-azure-sql.md) pomáhá chránit data na disku a chrání před neoprávněným přístupem k hardwaru. Provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase bez nutnosti změny aplikace. Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče, který se nazývá šifrovací klíč databáze.

I v případě, že je celé úložiště šifrované, je důležité také zašifrovat samotnou databázi. Toto je implementace přístupu k ochraně dat v rámci důkladné obrany. Pokud používáte Azure SQL Database a chcete chránit citlivá data (například platební karty nebo čísla sociálního pojištění), můžete šifrovat databáze se ověřeným 256 šifrování AES FIPS 140-2. Toto šifrování splňuje požadavky mnoha oborových standardů (například HIPAA a PCI).

Soubory související s [rozšířením fondu vyrovnávací paměti (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) se při šifrování databáze pomocí transparentního šifrování dat nešifrují. Pro soubory související s BPE je nutné použít nástroje pro šifrování na úrovni systému souborů, jako je [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) nebo [systém souborů EFS (Encrypting File System) (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) .

Vzhledem k tomu, že ověřený uživatel, který má oprávnění správce zabezpečení nebo správce databáze, může k datům přistupovat i v případě, že je databáze šifrovaná pomocí transparentního šifrování dat, měli byste také postupovat podle těchto doporučení:

- Povolte SQL Server ověřování na úrovni databáze.
- Použijte ověřování Azure AD s využitím [rolí RBAC](/azure/role-based-access-control/overview).
- Ujistěte se, že uživatelé a aplikace používají k ověření samostatné účty. Tímto způsobem můžete omezit oprávnění udělená uživatelům a aplikacím a snížit riziko škodlivých aktivit.
- Implementujte zabezpečení na úrovni databáze pomocí pevných databázových rolí (například db_datareader nebo db_datawriter). Případně můžete vytvořit vlastní role pro aplikaci, aby bylo možné udělit explicitní oprávnění vybraným databázovým objektům.

Další způsoby, jak zabezpečit data, je třeba vzít v úvahu:

- [Šifrování na úrovni buňky](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), která umožňuje klientům šifrovat citlivá data v klientských aplikacích a nikdy Neodhalovat šifrovací klíče databázovému stroji (SQL Database nebo SQL Server). V důsledku toho Always Encrypted poskytuje oddělení mezi osobami, které data vlastní (a můžou zobrazit), a osobami, které data spravují (ale nemají přístup).
- [Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security), které zákazníkům umožňuje řídit přístup k řádkům v databázové tabulce na základě charakteristik uživatele, který spouští dotaz. (Příklad charakteristik je členství ve skupině a kontext spuštění.)

Organizace, které nepoužívají šifrování na úrovni databáze, mohou být náchylnější k útokům, které ohrožují data umístěná v databázích SQL.

Další informace o SQL Database transparentní šifrování dat najdete v článku [transparentní šifrování dat Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Povolit auditování databáze
Auditování instance SQL Server databázového stroje nebo jednotlivé databáze zahrnuje události sledování a protokolování. V případě SQL Server můžete vytvořit audity, které obsahují specifikace pro události na úrovni serveru a specifikace pro události na úrovni databáze. Auditované události lze zapsat do protokolů událostí nebo auditovat soubory.

K dispozici je několik úrovní auditování pro SQL Server v závislosti na požadavcích státní správy nebo standardů pro vaši instalaci. Auditování SQL Server poskytuje nástroje a procesy pro povolení, ukládání a zobrazování auditů na různých objektech a databázových serverech.

[Azure SQL Database auditování](/azure/sql-database/sql-database-auditing) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu služby Azure Storage.

Auditování vám pomůže zachovat dodržování legislativních předpisů, pochopit databázovou činnost a najít nesrovnalosti a anomálie, které by mohly ukazovat na obchodní aspekty nebo porušení zabezpečení. Auditování usnadňuje dodržování standardů dodržování předpisů, ale nezaručuje dodržování předpisů.

Další informace o auditování databáze a o tom, jak ji povolit, najdete v tématu Začínáme [s auditováním SQL Database](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Povolit detekci hrozeb databáze
Ochrana před hrozbami překračuje detekci. Ochrana před hrozbami databáze zahrnuje:

- Zjišťování a klasifikace citlivých dat, abyste mohli chránit svá data.
- Implementace zabezpečených konfigurací v databázi, aby bylo možné chránit databázi.
- Zjišťování a reakce na potenciální hrozby, když k nim dojde, abyste mohli rychle reagovat a opravovat.

**Osvědčené postupy**: zjištění, klasifikace a označení citlivých dat ve vašich databázích.   
**Podrobnosti**: klasifikace dat ve službě SQL Database povolením [zjišťování a klasifikace dat](/azure/sql-database/sql-database-data-discovery-and-classification) v Azure SQL Database. Přístup k citlivým datům můžete sledovat na řídicím panelu Azure nebo v sestavách ke stažení.

**Osvědčený postup**: Sledujte slabá místa v databázi, abyste mohli proaktivně vylepšit zabezpečení databáze.   
**Podrobnosti**: použijte službu [posouzení ohrožení zabezpečení](/azure/sql-database/sql-vulnerability-assessment) Azure SQL Database, která vyhledá potenciální ohrožení zabezpečení databáze. Služba využívá znalostní bázi pravidel, která označují slabá místa zabezpečení a zobrazují odchylky od osvědčených postupů, jako jsou například chyby konfigurace, nadměrné oprávnění a nechráněná citlivá data.

Tato pravidla jsou založená na osvědčených postupech Microsoftu a zaměřuje se na problémy zabezpečení, které představují největší rizika pro vaši databázi a jejich cenná data. Zahrnují jak problémy na úrovni databáze, tak i problémy zabezpečení na úrovni serveru, jako je nastavení brány firewall serveru a oprávnění na úrovni serveru. Tato pravidla také reprezentují mnoho požadavků od regulativních orgánů, aby splnily jejich standardy dodržování předpisů.

**Osvědčený postup**: povolení detekce hrozeb.  
**Podrobnosti**: povolení [detekce hrozeb](/azure/sql-database/sql-database-threat-detection) Azure SQL Database k získání výstrah zabezpečení a doporučení, jak zkoumat a zmírnit hrozby. Dostanete výstrahy o podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení a útocích prostřednictvím injektáže SQL a také o přístupu k databázi neobvyklé a vzorcích dotazů.

[Rozšířená ochrana před internetovými útoky](/azure/sql-database/sql-advanced-threat-protection) je jednotný balíček pro pokročilé funkce zabezpečení SQL. Obsahuje výše zmíněné služby: zjišťování a klasifikace dat, posouzení ohrožení zabezpečení a detekce hrozeb. Poskytuje jedno umístění pro povolení a správu těchto schopností.

Povolení těchto možností vám pomůže:

- Splnění standardů ochrany osobních údajů a požadavků na dodržování legislativních předpisů.
- Řízení přístupu k databázím a posílení jejich zabezpečení.
- Monitorujte dynamické databázové prostředí, kde je obtížné sledovat změny.
- Detekuje potenciální hrozby a reagovat na ně.

Kromě toho detekce hrozeb integruje výstrahy se Azure Security Center pro centrální zobrazení stavu zabezpečení všech vašich prostředků Azure.

## <a name="next-steps"></a>Další kroky
V článku [osvědčené postupy a vzory zabezpečení Azure](best-practices-and-patterns.md) najdete v tématu Doporučené postupy zabezpečení pro použití při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu pro zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější verzi v zabezpečení Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde můžou být chyby zabezpečení Microsoftu, včetně problémů s Azure, nahlášené nebo prostřednictvím e-mailu secure@microsoft.com
