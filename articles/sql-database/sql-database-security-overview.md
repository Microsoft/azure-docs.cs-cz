---
title: "Přehled zabezpečení služby Azure SQL Database | Dokumentace Microsoftu"
description: "Další informace o zabezpečení Azure SQL Database a SQL Server, včetně rozdíly mezi cloudové a místní SQL Server."
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/12/2018
ms.author: giladm
ms.openlocfilehash: 1dc34f021fa6482c65cce0e922951ae329987c43
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="securing-your-sql-database"></a>Zabezpečení SQL Database

V tomto článku najdete základní informace o zabezpečení datové vrstvy aplikace pomocí Azure SQL Database. Konkrétně tento článek vám pomůže začít s prostředky pro ochranu dat, řízení přístupu a Proaktivní monitorování. 

Úplný přehled všech funkcí zabezpečení dostupných pro různé typy SQL najdete v tématu [Security Center pro databázový stroj SQL Serveru a Azure SQL Database](https://msdn.microsoft.com/library/bb510589). Další informace také najdete v [technickém dokumentu white paper o zabezpečení služby Azure SQL Database](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="protect-data"></a>Ochrana dat

### <a name="encryption"></a>Šifrování
Databáze SQL zabezpečuje vašich dat tím, že poskytuje šifrování dat v provozu s [Transport Layer Security](https://support.microsoft.com/kb/3135244), pro data v klidovém stavu s [transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)a pro data v použití s [ Funkce Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). 

> [!IMPORTANT]
>Všechna připojení ke službě Azure SQL Database vyžadují nepřetržité šifrování (SSL/TLS) příchozích a odchozích databázových dat. V připojovacím řetězci vaší aplikace, musíte zadat parametry k šifrování připojení a *není* důvěřovat certifikátu serveru (Pokud udělá se to pro vás zkopírujte připojovací řetězec mimo portál Azure), jinak hodnota připojení není ověřit identitu serveru a je náchylný na útoky "man-in-the-middle". Například u ovladače ADO.NET mají parametry připojovacího řetězce hodnoty **Encrypt=True** a **TrustServerCertificate=False**. 

Existují i jiné možnosti šifrování dat:

* [Šifrování na úrovni buňky](https://msdn.microsoft.com/library/ms179331.aspx) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
* Pokud potřebujete modul hardwarového zabezpečení nebo chcete centrálně spravovat hierarchii šifrovacích klíčů, měli byste uvažovat o [řešení Azure Key Vault s SQL Serverem na virtuálním počítači v Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="data-discovery--classification"></a>Data zjišťování a klasifikace
Data zjišťování a klasifikace (momentálně ve verzi preview) poskytuje pokročilé možnosti integrovaný do Azure SQL Database pro zjišťování, klasifikaci, označování a ochranu citlivá data v databázích máte. Zjišťování a klasifikace co nejvíce citlivými daty (business, finanční, zdravotní péče, osobní údaje, atd.) můžete přehrát hrají roli v ochrany vzrůstem informace vaší organizace. Může sloužit jako infrastruktury pro:

- Různé scénáře zabezpečení, jako jsou monitorování (auditování) a výstrah na neobvyklé přístup k citlivým datům.
- Řízení přístupu k a posílení zabezpečení databáze, které obsahují vysoce citlivá data.
- Vám pomáhá s splnění standardů data o ochraně osobních údajů a dodržování legislativních požadavků.

Další informace najdete v tématu [Začínáme s SQL DB Data zjišťování a klasifikace](sql-database-data-discovery-and-classification.md). 

## <a name="control-access"></a>Řízení přístupu
Služba SQL Database chrání vaše data omezením přístupu k databázi pomocí pravidel brány firewall, ověřovacími mechanismy vyžadujícími po uživatelích prokázání identity a autorizací přístupu k datům prostřednictvím členství a oprávnění na základě rolí, stejně jako prostřednictvím zabezpečení na úrovni řádku a dynamického maskování dat. Diskuzi o používání funkcí řízení přístupu ve službě SQL Database najdete v tématu [Řízení přístupu](sql-database-control-access.md).

> [!IMPORTANT]
> Správa databází a logických serverů v Azure se řídí tím, jaké role má uživatelský účet na portálu přiřazené. Další informace v tomto článku najdete v tématu [řízení přístupu na základě rolí na portálu Azure](../active-directory/role-based-access-control-what-is.md).
>

### <a name="firewall-and-firewall-rules"></a>Brána firewall a pravidla brány firewall
Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud pomocí [pravidel brány firewall](sql-database-firewall-configure.md) neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.

### <a name="authentication"></a>Authentication
Ověřování ve službě SQL Database určuje, jakým způsobem prokážete svou identitu při připojování k databázi. SQL Database podporuje dva typy ověřování:
* **Ověřování SQL,** které používá uživatelské jméno a heslo. Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Tyto přihlašovací údaje můžete použít k ověření a přihlášení k libovolné databázi na daném serveru jako vlastník databáze neboli „dbo“. 
* **Ověřování pomocí Azure Active Directory,** které používá identity spravované v Azure Active Directory a je podporované u spravovaných a integrovaných domén. [Kdykoliv to půjde](https://msdn.microsoft.com/library/ms144284.aspx), použijte ověřování pomocí Active Directory (integrované zabezpečení). Pokud chcete k ověřování použít Azure Active Directory, musíte vytvořit jiného správce serveru, který se jmenuje „Azure AD admin“ a který smí spravovat uživatele a skupiny služby Azure AD. Tento správce také smí provádět všechny operace jako běžný správce serveru. Postup vytvoření účtu správce služby Azure AD, který umožňuje ověřování Azure Active Directory, najdete v tématu [Připojení ke službě SQL Database s ověřením přes Azure Active Directory](sql-database-aad-authentication.md).

### <a name="authorization"></a>Autorizace
Autorizace určuje, co může uživatel provádět ve službě Azure SQL Database. Tyto možnosti jsou dané členstvím v databázových rolích a oprávněními k databázi na úrovni objektů vašeho účtu. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění. Účet správce serveru, který používáte k připojení, je členem skupiny db_owner. Tato skupina může s databází provádět všechny operace. Tento účet uložte kvůli nasazení upgradovaných schémat a dalším možnostem správy. Použijte účet „ApplicationUser“, který má omezenější oprávnění a umožňuje připojit se z aplikace k databázi s nejnižšími oprávněními, jaké aplikace potřebuje.

### <a name="row-level-security"></a>Zabezpečení na úrovni řádku
Zabezpečení na úrovni řádku umožňuje řízení přístupu k řádkům v databázové tabulce na základě charakteristiky uživatele spouštějícího dotaz (například členství ve skupině nebo kontext spuštění). Další informace najdete v tématu [Zabezpečení na úrovni řádku](https://msdn.microsoft.com/library/dn765131).

### <a name="dynamic-data-masking"></a>Dynamické maskování dat 
Databáze SQL dynamická data maskování omezuje ohrožení citlivých dat pomocí maskování uživatelům bez oprávnění. Dynamické maskování automaticky dat zjistí potenciálně citlivá data v databázi SQL Azure a poskytuje řešitelné doporučení k maskování těchto polí s minimálním dopadem na aplikační vrstvu. Funguje tak, že maskuje citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění. Další informace najdete v tématu [Začínáme s SQL Database dynamická data maskování](sql-database-dynamic-data-masking-get-started.md) lze použít k omezení rizika citlivá data.

## <a name="proactive-monitoring"></a>Proaktivní monitorování
Služba SQL Database chrání vaše data poskytováním možností auditování a detekce hrozeb. 

### <a name="auditing"></a>Auditování
Auditování služby SQL Database sleduje databázové aktivity a pomáhá dodržovat legislativní předpisy díky zaznamenávání databázových událostí do protokolu auditu ve vašem účtu služby Azure Storage. Auditování vám umožňuje pochopit probíhající databázové aktivity a pomocí analýzy a zkoumání historické aktivity identifikovat potenciální hrozby nebo možné zneužití a narušení zabezpečení. Další informace najdete v tématu [Začínáme s auditováním služby SQL Database](sql-database-auditing.md).  

### <a name="threat-detection"></a>Detekce hrozeb
Detekce hrozeb doplňuje auditování, tím, že poskytuje další vrstvu zabezpečení intelligence integrovaný do služby Azure SQL Database, která upozorní na neobvyklé a potenciálně škodlivé pokusy přístup nebo využívat databáze. Upozorní o podezřelých aktivit, potenciální ohrožení zabezpečení a prostřednictvím injektáže SQL, jakož i nezvyklé databázové přístupové vzorce. Můžete prohlížet výstrah o zjištěných hrozbách [Azure Security Center](https://azure.microsoft.com/services/security-center/) a zadejte podrobnosti podezřelých aktivit a doporučujeme akce o tom, jak prozkoumat a zmírnit riziko. Detekce hrozeb stojí $15, počet serverů za měsíc. Je bezplatná pro prvních 60 dní. Další informace najdete v článku [Začínáme zjišťovat hrozby ve službě SQL Database](sql-database-threat-detection.md).
 
## <a name="compliance"></a>Dodržování předpisů
Kromě výše uvedeného funkce a funkce, které mohou pomoci aplikace splňovat různé požadavky na zabezpečení, Azure SQL Database také účastní pravidelné audity a certifikovala proti počet standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://azure.microsoft.com/support/trust-center/), kde také najdete nejnovější seznam [certifikátů služby SQL Database v oblasti dodržování předpisů](https://azure.microsoft.com/support/trust-center/services/).


## <a name="security-management"></a>Správa zabezpečení

Databáze SQL slouží ke správě zabezpečení vašich dat. Díky pomocí řídicího panelu centralizované zabezpečení a kontroly databáze [vyhodnocení ohrožení zabezpečení SQL](sql-vulnerability-assessment.md).

**Vyhodnocení ohrožení zabezpečení**: [vyhodnocení ohrožení zabezpečení SQL](sql-vulnerability-assessment.md) (aktuálně ve verzi preview) je snadno konfigurovat nástroj integrovaný do Azure SQL Database, která vám může pomoct zjistit, sledovat a napravovat potenciální databáze chyby zabezpečení. Hodnocení provede kontrolu ohrožení zabezpečení ve vaší databázi a vygeneruje sestavu, která poskytuje přehled vaší stavu zabezpečení, včetně řešitelné kroků vyřešit problémy se zabezpečením a zvýšit zabezpečení vaší databáze. Zprávy o hodnocení můžete přizpůsobit pro vaše prostředí nastavení přijatelné směrný konfigurace oprávnění, konfigurace funkce a nastavení databáze. To vám může pomoci:

- Splňovat požadavky předpisů vyžadujících sestavy kontroly databáze. 

- Splnění standardů data o ochraně osobních údajů. 

- Monitorování dynamické databáze prostředí, kde je obtížné sledovat změny.

Další informace najdete v tématu [vyhodnocení ohrožení zabezpečení SQL](sql-vulnerability-assessment.md).

## <a name="next-steps"></a>Další postup

- Diskuzi o používání funkcí řízení přístupu ve službě SQL Database najdete v tématu [Řízení přístupu](sql-database-control-access.md).
- Informace o auditování databáze, najdete v části [auditování databáze SQL](sql-database-auditing.md).
- Informace o detekce hrozeb, najdete v části [detekce hrozeb SQL Database](sql-database-threat-detection.md).
