---
title: Přehled zabezpečení služby Azure SQL Database | Dokumentace Microsoftu
description: Další informace o zabezpečení Azure SQL Database a SQL Server, jaký je rozdíl mezi cloudem a místním SQL serverem.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 8d3a14228d31c8f4507dbcac7b9bd47ae1c4de0a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902148"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Přehled možností zabezpečení Azure SQL Database

V tomto článku najdete základní informace o zabezpečení datové vrstvy aplikace pomocí Azure SQL Database. Konkrétně tento článek vám pomůže začít s prostředky pro ochranu dat, řízení přístupu a proaktivním monitorováním.

Úplný přehled všech funkcí zabezpečení dostupných pro různé typy SQL najdete v tématu [Security Center pro databázový stroj SQL Serveru a Azure SQL Database](https://msdn.microsoft.com/library/bb510589). Další informace také najdete v [technickém dokumentu white paper o zabezpečení služby Azure SQL Database](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="protect-data"></a>Ochrana dat

### <a name="encryption"></a>Šifrování

SQL Database chrání data tím, že zajišťuje šifrování přenášených dat pomocí [protokolu TLS (Transport Layer Security)](https://support.microsoft.com/kb/3135244), neaktivních uložených dat pomocí [transparentního šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) a používaných dat pomocí funkce [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

> [!IMPORTANT]
> Všechna připojení ke službě Azure SQL Database vyžadují nepřetržité šifrování (SSL/TLS) příchozích a odchozích databázových dat. V připojovacím řetězci aplikace musíte zadat parametry, které šifrují připojení a *není* důvěřovat certifikátu serveru (Pokud se to pro vás zkopírujte připojovací řetězec z portálu Azure), jinak připojení nelze ověřit identitu serveru a je náchylný na útoky "man-in-the-middle". Například u ovladače ADO.NET mají parametry připojovacího řetězce hodnoty **Encrypt=True** a **TrustServerCertificate=False**. Informace o připojení a TLS najdete v tématu [požadavky TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

Existují i jiné možnosti šifrování dat:

- [Šifrování na úrovni buňky](https://msdn.microsoft.com/library/ms179331.aspx) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
- Pokud potřebujete modul hardwarového zabezpečení nebo chcete centrálně spravovat hierarchii šifrovacích klíčů, měli byste uvažovat o [řešení Azure Key Vault s SQL Serverem na virtuálním počítači v Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="data-discovery--classification"></a>Zjišťování a klasifikace dat

Data zjišťování a klasifikace (aktuálně ve verzi preview) nabízí rozšířené možnosti, které jsou integrované do Azure SQL Database pro zjišťování, klasifikace, označování popisky a ochranu citlivých dat ve vašich databázích. Zjišťování a klasifikace nanejvýš citlivých dat (obchodní/finanční, zdravotní péče, identifikovatelné osobní údaje, atd.) můžete přehrát velmi důležitou roli v vaší organizace zásadní roli. informace o ochranu. Může sloužit jako infrastruktura pro:

- Různé scénáře zabezpečení, jako je například monitorování (auditování) a upozorňuje na neobvyklé přístup k citlivým datům.
- Řízení přístupu k a posílení zabezpečení, databáze obsahující vysoce citlivá data.
- Pomáhá splnit požadavky na dodržování legislativních předpisů a data standardy ochrany osobních údajů.

Další informace najdete v tématu [Začínáme s SQL DB zjišťování a klasifikace dat](sql-database-data-discovery-and-classification.md).

## <a name="control-access"></a>Řízení přístupu

Služba SQL Database chrání vaše data omezením přístupu k databázi pomocí pravidel brány firewall, ověřovacími mechanismy vyžadujícími po uživatelích prokázání identity a autorizací přístupu k datům prostřednictvím členství a oprávnění na základě rolí, stejně jako prostřednictvím zabezpečení na úrovni řádku a dynamického maskování dat. Diskuzi o používání funkcí řízení přístupu ve službě SQL Database najdete v tématu [Řízení přístupu](sql-database-control-access.md).

> [!IMPORTANT]
> Správa databází a logických serverů v Azure se řídí tím, jaké role má uživatelský účet na portálu přiřazené. Další informace v tomto článku najdete v tématu [řízení přístupu na základě rolí na webu Azure portal](../role-based-access-control/overview.md).

### <a name="firewall-and-firewall-rules"></a>Brána firewall a pravidla brány firewall

Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud pomocí [pravidel brány firewall](sql-database-firewall-configure.md) neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.

### <a name="authentication"></a>Authentication

Ověřování ve službě SQL Database určuje, jakým způsobem prokážete svou identitu při připojování k databázi. SQL Database podporuje dva typy ověřování:

- **Ověřování SQL**

  Tuto metodu ověřování pomocí uživatelského jména a hesla. Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Tyto přihlašovací údaje můžete použít k ověření a přihlášení k libovolné databázi na daném serveru jako vlastník databáze neboli „dbo“.

- **Ověřování pomocí Azure Active Directory**

  Tato metoda ověřování používá identity spravované v Azure Active Directory a je podporované u spravovaných a integrovaných domén. [Kdykoliv to půjde](https://msdn.microsoft.com/library/ms144284.aspx), použijte ověřování pomocí Active Directory (integrované zabezpečení). Pokud chcete k ověřování použít Azure Active Directory, musíte vytvořit jiného správce serveru, který se jmenuje „Azure AD admin“ a který smí spravovat uživatele a skupiny služby Azure AD. Tento správce také smí provádět všechny operace jako běžný správce serveru. Postup vytvoření účtu správce služby Azure AD, který umožňuje ověřování Azure Active Directory, najdete v tématu [Připojení ke službě SQL Database s ověřením přes Azure Active Directory](sql-database-aad-authentication.md).

### <a name="authorization"></a>Autorizace

Autorizace určuje, co může uživatel provádět ve službě Azure SQL Database. Tyto možnosti jsou dané členstvím v databázových rolích a oprávněními k databázi na úrovni objektů vašeho účtu. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění. Účet správce serveru, který používáte k připojení, je členem skupiny db_owner. Tato skupina může s databází provádět všechny operace. Tento účet uložte kvůli nasazení upgradovaných schémat a dalším možnostem správy. Použijte účet „ApplicationUser“, který má omezenější oprávnění a umožňuje připojit se z aplikace k databázi s nejnižšími oprávněními, jaké aplikace potřebuje.

### <a name="row-level-security"></a>Zabezpečení na úrovni řádku

Zabezpečení na úrovni řádku umožňuje řízení přístupu k řádkům v databázové tabulce na základě charakteristiky uživatele spouštějícího dotaz (například členství ve skupině nebo kontext spuštění). Další informace najdete v tématu [Zabezpečení na úrovni řádku](https://docs.microsoft.com/sql/relational-databases/security/row-level-security).

### <a name="dynamic-data-masking"></a>Dynamické maskování dat

Maskování dynamických dat SQL Database omezuje riziko ohrožení citlivých dat pomocí jejich maskování pro neoprávněné uživatele. Automaticky maskování dynamických dat zjišťuje potenciálně citlivá data ve službě Azure SQL Database a poskytuje užitečná doporučení pro maskování těchto polí s minimálním dopadem na aplikační vrstvu. Funguje tak, že maskuje citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění. Další informace najdete v tématu [Začínáme s SQL Database dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md).

## <a name="proactive-monitoring"></a>Proaktivní monitorování

Služba SQL Database chrání vaše data poskytováním možností auditování a detekce hrozeb.

### <a name="auditing"></a>Auditování

Auditování služby SQL Database sleduje databázové aktivity a pomáhá dodržovat legislativní předpisy díky zaznamenávání databázových událostí do protokolu auditu ve vašem účtu služby Azure Storage. Auditování vám umožňuje pochopit probíhající databázové aktivity a pomocí analýzy a zkoumání historické aktivity identifikovat potenciální hrozby nebo možné zneužití a narušení zabezpečení. Další informace najdete v tématu [Začínáme s auditováním služby SQL Database](sql-database-auditing.md).  

### <a name="threat-detection"></a>Detekce hrozeb

Detekce hrozeb doplňuje auditování a poskytuje další úroveň inteligentního zabezpečení, které jsou integrované do služby Azure SQL Database, která zjistí a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Upozorní vás na podezřelé aktivity, potenciální ohrožení zabezpečení a útoky prostřednictvím injektáže SQL, jakož i neobvyklé databázové vzorce přístupu. Výstrahy detekce hrozeb můžete zobrazit v [Azure Security Center](https://azure.microsoft.com/services/security-center/) a zadejte podrobnosti o podezřelé aktivitě a doporučení akce k prošetření a zmírnění hrozby. Detekce hrozeb stojí 15 USD a server za měsíc. Prvních 60 dní je zdarma. Další informace najdete v článku [Začínáme zjišťovat hrozby ve službě SQL Database](sql-database-threat-detection.md).

## <a name="compliance"></a>Dodržování předpisů

Kromě výše uvedených funkcí a funkci, která může pomoci vaší aplikace vyhovět různým nárokům na zabezpečení, Azure SQL Database také účastní pravidelných auditů a byla certifikována pro řadu standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://azure.microsoft.com/support/trust-center/), kde také najdete nejnovější seznam [certifikátů služby SQL Database v oblasti dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="security-management"></a>Správa zabezpečení

SQL Database vám pomůže spravovat zabezpečení vašich dat tím, že poskytuje kontrol databáze a centralizované zabezpečení řídicím panelu pomocí [posouzení ohrožení zabezpečení SQL](sql-vulnerability-assessment.md).

**Posouzení ohrožení zabezpečení**: [posouzení ohrožení zabezpečení SQL](sql-vulnerability-assessment.md) (aktuálně ve verzi preview) je snadno konfigurovat integrované do Azure SQL Database, která pomáhá zjišťovat, sledování a náprava potenciálních databáze nástroje ohrožení zabezpečení. Posouzení prověřování ohrožení zabezpečení se spustí na vaši databázi a generuje sestavu, která budete mít přehled o stavu vašeho zabezpečení, včetně praktické kroky k vyřešení problémů se zabezpečením a Vylepšete svoje zabezpečení databáze. Sestavu posouzení je může přizpůsobit pro vaše prostředí přijatelná směrného plánu konfigurace oprávnění, funkce Konfigurace a nastavení databáze. To vám mohou pomoci při:

- Splnění požadavků na dodržování předpisů, které vyžadují sestavy výstupu kontroly databáze.
- Splňovat standardy ochrany osobních údajů data.
- Monitorování databáze dynamické prostředí, kde je obtížné sledovat změny.

Další informace najdete v tématu [posouzení ohrožení zabezpečení SQL](sql-vulnerability-assessment.md).

## <a name="next-steps"></a>Další postup

- Diskuzi o používání funkcí řízení přístupu ve službě SQL Database najdete v tématu [Řízení přístupu](sql-database-control-access.md).
- Informace o auditování databáze, najdete v článku [auditování služby SQL Database](sql-database-auditing.md).
- Diskuzi o detekci hrozeb, naleznete v tématu [detekce hrozeb služby SQL Database](sql-database-threat-detection.md).
