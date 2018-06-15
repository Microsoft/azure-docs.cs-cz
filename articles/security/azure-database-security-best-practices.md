---
title: Osvědčené postupy zabezpečení databáze Azure | Microsoft Docs
description: Tento článek obsahuje sadu osvědčené postupy pro zabezpečení databáze Azure.
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
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 81acf90f0d600c0c3dafa2a4ccd2f8564fd18c9a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893887"
---
# <a name="azure-database-security-best-practices"></a>Osvědčené postupy zabezpečení databáze Azure

Zabezpečení je velmi důležité při správě databáze a byla vždy prioritu pro Azure SQL Database. Vaše databáze mohly být zabezpečeny úzce pomohou uspokojení nejvíce zákonných nebo požadavky na zabezpečení, mimo jiné včetně HIPAA, ISO 27001/27002 a PCI DSS úrovně 1. Aktuální seznam certifikace dodržování předpisů zabezpečení je k dispozici na [Microsoft Trust Center lokality](http://azure.microsoft.com/support/trust-center/services/). Můžete taky umístit vaše databáze v konkrétní datových centrech Azure na základě zákonných požadavků.

V tomto článku se budeme zabývat kolekce osvědčené postupy zabezpečení databáze Azure. Tyto doporučené postupy jsou odvozeny od našich zkušeností s zabezpečení databáze Azure a prostředí zákazníků, jako sami.

Pro každý osvědčený postup objasníme:

-   Co je osvědčeným postupem
-   Proč chcete povolit tento osvědčený postup
-   Pokud se nepodaří povolit osvědčený postup, co může být výsledkem
-   Jak můžete informace o povolení osvědčený postup

Tento článek osvědčené postupy zabezpečení databáze Azure je založený na konsenzu stanovisko a možnostech platformy Azure a nastaví funkce, které jsou v době, kdy byla zapsána v tomto článku. Názory a technologie v průběhu času mění a v tomto článku budeme je aktualizovat v pravidelných intervalech, aby odrážela tyto změny.

Osvědčené postupy pro databáze Azure zabezpečení popsané v tomto článku patří:

-   Pomocí pravidel brány firewall můžete omezit přístup k databázi
-   Povolit ověřování databáze
-   Chraňte svá data pomocí šifrování
-   Ochranu přenášených dat
-   Povolit auditování databáze
-   Povolení detekce hrozeb databáze


## <a name="use-firewall-rules-to-restrict-database-access"></a>Pomocí pravidel brány firewall můžete omezit přístup k databázi

Microsoft Azure SQL Database poskytuje relační databázovou službu pro aplikace Azure a další internetové aplikace. Databáze SQL zajistit zabezpečení přístupu řídí přístup s pravidla brány firewall omezení podle IP adresy, ověřovací mechanismy, které by uživatelé museli prokázání své identity a autorizace mechanismy omezení uživatelů na konkrétní akce a data připojení. Brány firewall zabránit veškerý přístup k databázovému serveru, dokud nezadáte, které počítače mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.

![Pravidla brány firewall](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Služba Azure SQL Database je dostupná jenom prostřednictvím portu TCP 1433. Pokud chcete z počítače získat přístup ke službě SQL Database, ověřte, že brána firewall vašeho klientského počítače umožňuje odchozí komunikaci TCP přes port TCP 1433. Pokud není skutečně potřeba pro jiné aplikace, blokovala příchozí připojení na portu TCP 1433 pomocí pravidel brány firewall.

V rámci připojovacího procesu budou připojení virtuálních počítačů Azure přesměrována na jinou IP adresu a port, které jsou pro každou roli pracovního procesu jedinečné. Číslo portu je v rozsahu 11000 až 11999. Další informace o portech TCP naleznete v tématu [porty nad rámec 1433 pro technologii ADO.NET 4.5 a SQL Database2](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12).

> [!Note]
> Další informace o pravidlech brány firewall pro SQL Database najdete v tématu [Pravidla brány firewall služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="enable-database-authentication"></a>Povolit ověřování databáze
Databáze SQL podporuje dva typy ověřování, ověřování SQL a Azure Active Directory Authentication (ověřování Azure AD).

**Ověřování SQL** se doporučuje v následujících případech:

-   To umožňuje SQL Azure pro podporu prostředí smíšený operačních systémech, kde všichni uživatelé nejsou ověřené doménou systému Windows.
-   Umožňuje SQL Azure pro podporu starší aplikace a aplikace třetích stran, které vyžadují ověřování systému SQL Server.
-   Umožňuje uživatelům připojit se z domén neznámý nebo nedůvěryhodný. Například aplikace, kde Zákazníci zavedené připojení s přiřadit přihlášení serveru SQL pro příjem stav jejich objednávek.
-   Umožňuje SQL Azure pro podporu webových aplikací, kde uživatelé vytvářet své vlastní identity.
-   Umožňuje vývojářům softwaru k distribuci svých aplikacích pomocí hierarchie komplexní oprávnění podle známé a přednastavené přihlášení serveru SQL.

> [!Note]
> Ověřování systému SQL Server však nelze použít bezpečnostní protokol Kerberos.

Pokud používáte **ověřování SQL** musíte:

-   Spravujte silné přihlašovací údaje.
-   Ochranu přihlašovacích údajů v připojovacím řetězci.
-   (Potenciálně) ochranu přihlašovacích údajů předaných přes síť z webového serveru do databáze. Další informace najdete v části [postupy: připojení k SQL serveru pomocí ověřování SQL v technologii ASP.NET 2.0](https://msdn.microsoft.com/library/ms998300.aspx).

**Ověřování Azure Active Directory** mechanismus připojit k Microsoft Azure SQL Database a [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) pomocí identit v Azure Active Directory (Azure AD). Při ověřování Azure AD můžete centrálně spravovat identity uživatelů, databáze a další služby Microsoftu v jednom centrálním místě. Centrální správa ID poskytuje jednotné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění. Výhody patří:

-   Nabízí alternativu k ověřování serveru SQL Server.
-   Pomáhá zastavit šíření identit uživatelů mezi servery databáze.
-   Umožňuje otočení heslo na jednom místě.
-   Zákazníci můžete spravovat oprávnění databáze pomocí externí skupiny (AAD).
-   Ukládání hesel se může eliminovat tím, že umožňuje integrované ověřování systému Windows a jiných forem ověřování podporovaných službou Azure Active Directory.
-   K ověření identity na úrovni databáze používá Azure AD authentication uživatele databáze s omezením.
-   Azure AD podporuje ověřování na základě tokenu pro aplikace, připojení k databázi SQL.
-   Ověřování služby Azure AD podporuje služby AD FS (federation domény) nebo ověřování nativní uživatele a heslo pro místní Azure Active Directory bez synchronizace domény.
-   Azure AD podporuje připojení z SQL Server Management Studio, které používají Universal ověřování služby Active Directory, která zahrnuje Multi-Factor Authentication (MFA). Vícefaktorové ověřování zahrnuje silné ověřování s celou řadu možností snadno ověření – telefonní hovor, textová zpráva, čipové karty s PIN kód nebo oznámení mobilní aplikace. Další informace najdete v tématu [SSMS podpora pro Azure AD MFA s SQL Database a SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication).

Postup konfigurace obsahuje následující kroky konfigurace a použití ověřování Azure Active Directory.

-   Vytvořit a naplnit Azure AD.
-   Volitelné: Přidružení nebo změňte služby active directory, který je aktuálně přidružena předplatného Azure.
-   Vytvoření správce Azure Active Directory pro server Azure SQL nebo [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Nakonfigurujte klientské počítače.
-   Vytvořte uživatele databáze s omezením ve vaší databázi namapované na Azure AD identity.
-   Připojení k vaší databázi pomocí identit Azure AD.

Podrobnosti o informace můžete najít [zde](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

## <a name="protect-your-data-using-encryption"></a>Chraňte svá data pomocí šifrování

[Azure SQL Database transparentní šifrování dat (šifrování TDE)](https://msdn.microsoft.com/library/dn948096.aspx) pomáhá chránit před ohrožením škodlivých aktivit provedením v reálném čase šifrování a dešifrování databáze, přidružených záloh a soubory protokolu transakcí v klidovém stavu bez nutnosti změny aplikace. Šifrování TDE zašifruje úložiště celé databáze pomocí symetrický klíč s názvem šifrovací klíč databáze.

I v případě, že šifrované celý úložiště, je velmi důležité šifrovat taky sama vaší databáze. Toto je implementace obrany v hloubka přístupu, ochrany dat. Pokud používáte Azure SQL Database a chcete, chrání citlivá data, jako je například platebních karet nebo čísla sociálního pojištění, můžete šifrovat databáze s FIPS 140-2 ověřit 256bitová šifrování AES splňuje požadavky na mnoho oborových standardů (například HIPAA, PCI) .

Je důležité si uvědomit, že soubory související s [vyrovnávací paměti rozšíření fondu (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nejsou šifrují, pokud databáze je zašifrovaná pomocí šifrování TDE. Je nutné použít nástroje šifrování na úrovni systému souborů, jako je [BitLocker](https://technet.microsoft.com/library/cc732774) nebo [systému souborů EFS (ENCRYPTING File System)](https://technet.microsoft.com/library/cc700811.aspx) pro BPE související soubory.

Od oprávněný uživatel, jako je zabezpečení správce nebo správce databáze měli přístup k datům, i v případě, že databáze je šifrován TDE, by navíc dodržíte následující doporučení:

-   Povolte ověřování SQL na úrovni databáze.
-   Ověřování pomocí služby Azure AD pomocí [role RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview).
-   Uživatelé a aplikace by měla používat samostatné účty k ověření. Tímto způsobem můžete omezit oprávnění udělená uživatelům a aplikacím a snížení rizika škodlivých aktivit.
-   Implementace zabezpečení na úrovni databáze pomocí pevné databázové role (například db_datareader nebo db_datawriter), nebo můžete vytvořit vlastní role pro vaši aplikaci udělit výslovná oprávnění k objektům vybrané databáze.

Existují i jiné možnosti šifrování dat:

-   [Šifrování na úrovni buňky](https://msdn.microsoft.com/library/ms179331.aspx) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
-   Šifrování používá pomocí funkce Always Encrypted: [vždy šifrována](https://msdn.microsoft.com/library/mt163865.aspx) umožňuje klientům šifrovat citlivá data uvnitř klientské aplikace a nikdy odhalit šifrovací klíče pro databázový stroj (SQL Database nebo SQL Server). V důsledku toho vždy šifrována zajišťuje oddělení mezi uživatelů, kteří vlastní data (a můžete ji zobrazit) a ty, kteří spravovat data (ale musí mít žádný přístup).
-   Pomocí zabezpečení na úrovni řádků: zabezpečení na úrovni řádků umožňuje zákazníkům k řízení přístupu na řádky v tabulce databáze na základě charakteristik uživatele provádění dotazu (například skupinu členství nebo provádění kontextu). Další informace najdete v tématu [Zabezpečení na úrovni řádku](https://msdn.microsoft.com/library/dn765131).

## <a name="protect-data-in-transit"></a>Ochranu přenášených dat
Základní součástí strategie ochrany dat by měly být ochrany dat během přenosu. Vzhledem k tomu, že data bude přesunutí a zpět z mnoho míst, obecné doporučení je vždy používat protokoly SSL/TLS pro výměnu dat v různých umístěních. V některých případech můžete chtít izolovat celý komunikační kanál mezi místní a cloudové infrastruktury pomocí virtuální privátní sítě (VPN).

Pro přesun mezi vaší místní infrastruktury a Azure data měli byste zvážit příslušná bezpečnostní opatření, například HTTPS nebo VPN.

Organizace, které je potřeba zabezpečit přístup z více pracovní stanice nachází v místě k Azure, použijte [Azure site-to-site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Organizace, které je potřeba zabezpečit přístup z jednotlivých pracovních stanicích nachází místní nebo mimo pracoviště do Azure, zvažte použití [Point-to-Site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Větších datových sad se dají přesunout přes vyhrazené vysokorychlostní propojení WAN, jako [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Pokud chcete použít ExpressRoute, můžete také šifrování dat na úrovni aplikace pomocí [SSL/TLS](https://support.microsoft.com/kb/257591) nebo jiné protokoly pro zvýšení ochrany.

Pokud jsou interakci s Azure Storage prostřednictvím portálu Azure, všechny transakce dojít přes HTTPS. [Rozhraní API REST úložiště](https://msdn.microsoft.com/library/azure/dd179355.aspx) přes HTTPS můžete použít také pro interakci s [Azure Storage](https://azure.microsoft.com/services/storage/) a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organizace, které se nepodařilo ochranu přenášených dat budou náchylnější pro [útokům man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [odposlouchávání](https://technet.microsoft.com/library/gg195641.aspx) a zneužití relace. Prvním krokem při přístupu k důvěrných dat může být tyto útoky.

Další informace o Azure VPN možnost přečíst v článku [plánování a návrhu pro bránu VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

## <a name="enable-database-auditing"></a>Povolit auditování databáze
Auditování instanci databázového stroje SQL Server nebo jednotlivé databáze zahrnuje sledování a protokolování události na databázového stroje. SQL Server audit umožňuje vytvářet audity serveru, které může obsahovat specifikace auditu serveru pro události na úrovni serveru a specifikace auditu databáze pro databázi úroveň události. Auditované události lze zapsat do protokolů událostí nebo auditovat soubory.

Existuje několik úrovní auditování pro SQL Server, v závislosti na státní nebo standardy požadavky pro instalaci. SQL Server Audit poskytuje nástroje a procesy, které je potřeba povolit, uložit a zobrazit audity na různé objekty serveru a databáze.

[Auditování databáze SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) sleduje události databáze a zapisuje je do auditu protokolu ve vašem účtu úložiště Azure.

Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.

Auditování umožňuje a zjednodušuje dodržování standardů dodržování předpisů, ale není zajistit dodržování předpisů.

Další informace o auditování databáze a jak ji povolit, najdete v článku [povolit auditování a zjišťování hrozeb na serverech SQL v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="enable-database-threat-detection"></a>Povolení detekce hrozeb databáze
Detekce hrozeb SQL umožňuje zjistit a reagovat na potenciální hrozby, kdy k nim dojde tím, že poskytuje výstrahy zabezpečení na neobvyklé aktivity. Obdržíte výstrahu při databáze podezřelé aktivity, potenciální ohrožení zabezpečení a prostřednictvím injektáže SQL, jakož i nezvyklé databázové přístupové vzorce. Detekce hrozeb SQL výstrahy zadejte podrobnosti podezřelých aktivit a doporučujeme akce o tom, jak prozkoumat a zmírnit riziko.

Například Injektáž SQL je jedním z běžné problémy zabezpečení webových aplikací na Internetu, slouží k útokům na základě dat aplikace. Útočníci využít výhod ohrožení zabezpečení aplikace se zlými úmysly příkazy SQL, do pole pro zadání aplikací, vložit před nedodržením nebo upravovat data v databázi.

Další informace o tom, jak nastavit detekce hrozeb pro vaše databáze v Azure portálu najdete, [detekce hrozeb databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Kromě toho detekce hrozeb SQL integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/). Zveme vás k bezplatnému vyzkoušení po dobu 60 dnů.

Další informace o databázi detekce hrozeb a jak ji povolit, najdete v článku [povolit auditování a zjišťování hrozeb na serverech SQL v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="conclusion"></a>Závěr
Databáze Azure je platforma robustní databáze, s celou řadu funkcí zabezpečení, které splňují mnoho organizace i regulačních požadavků. Vám může pomoct chránit data pomocí řízení fyzický přístup k datům a použití různých možností pro zabezpečení dat na soubor-, sloupec-, řádek úrovni nebo se transparentní šifrování dat, šifrování na úrovni buněk nebo zabezpečení na úrovni řádků. Vždy šifrovaný také umožní operace proti šifrovaná data, zjednodušuje proces aktualizace aplikace. Přístup k auditování protokoly aktivity databáze SQL se pak poskytuje informace, které potřebujete, abyste mohli vědět, jak a kdy je přístup k datům.

## <a name="next-steps"></a>Další postup
- Další informace o pravidlech brány firewall najdete v tématu [pravidla brány Firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).
- Informace o uživateli a přihlašování najdete v tématu [Správa přihlašování](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).
- Podívejte se kurz [zabezpečení vaší databázi SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
