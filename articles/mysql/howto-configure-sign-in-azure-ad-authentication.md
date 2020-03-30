---
title: Použití služby Azure Active Directory – databáze Azure pro MySQL
description: Přečtěte si, jak nastavit Azure Active Directory (Azure AD) pro ověřování pomocí Azure Database for MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299001"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Použití služby Azure Active Directory k ověřování pomocí mySQL

Tento článek vás provede kroky, jak nakonfigurovat přístup služby Azure Active Directory pomocí Azure Database for MySQL a jak se připojit pomocí tokenu Azure AD.

> [!IMPORTANT]
> Azure AD ověřování pro Azure Database for MySQL je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Nastavení uživatele správce Azure AD

Jenom uživatel Azure AD Admin můžete vytvořit nebo povolit uživatele pro ověřování na základě Azure AD. Chcete-li vytvořit uživatele azure a ad admin, postupujte podle následujících kroků

1. Na webu Azure Portal vyberte instanci Azure Database for MySQL, kterou chcete povolit pro Azure AD.
2. V části Nastavení vyberte Správce služby Active Directory:

![nastavení správce azure reklamy][2]

3. Vyberte platného uživatele Azure AD v tenantovi zákazníka jako správce Azure AD.

> [!IMPORTANT]
> Při nastavování správce se do databáze Azure pro server MySQL přidá nový uživatel s úplnými oprávněními správce.

Na serveru MySQL lze vytvořit jenom jednoho správce Azure AD a výběr jiného přepíše stávajícího správce Azure AD nakonfigurovaného pro server.

V budoucí verzi budeme podporovat určení skupiny Azure AD namísto jednotlivého uživatele mít více správců, ale to není v současné době ještě podporována.

Po konfiguraci správce se nyní můžete přihlásit:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Připojení k Azure Database for MySQL pomocí Azure AD

Následující diagram vysoké úrovně shrnuje pracovní postup použití ověřování Azure AD s Azure Database for MySQL:

![tok ověřování][1]

Jsme navrhli integrace Azure AD pro práci s běžnými nástroji MySQL, jako je mysql CLI, které nejsou vědomi Azure AD a podporuje pouze určení uživatelské jméno a heslo při připojování k MySQL. Předáme token Azure AD jako heslo, jak je znázorněno na obrázku výše.

V současné době jsme testovali následující klienty:

- MySQLPracovní lavice 
- Mysql CLI

Také jsme testovali nejběžnější ovladače aplikací, můžete vidět podrobnosti na konci této stránky.

Toto jsou kroky, které uživatel nebo aplikace bude muset provést ověření pomocí Azure AD popsané níže:

### <a name="step-1-authenticate-with-azure-ad"></a>Krok 1: Ověření pomocí Azure AD

Ujistěte se, že máte [nainstalovaný Azure CLI](/cli/azure/install-azure-cli).

Vyvolání nástroje Azure CLI k ověření pomocí Azure AD. Vyžaduje, abyste své ID uživatele Azure AD a heslo.

```
az login
```

Tento příkaz spustí okno prohlížeče na stránku ověřování Azure AD.

> [!NOTE]
> K provedení těchto kroků můžete taky použít Azure Cloud Shell.
> Upozorňujeme, že při načítání přístupového tokenu Azure AD v `az login` Prostředí Azure Cloud Shell budete muset explicitně volat a znovu se přihlásit (v samostatném okně s kódem). Po tomto znaménku v příkazu `get-access-token` bude fungovat podle očekávání.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Krok 2: Načtení přístupového tokenu Azure AD

Vyvolání nástroje Azure CLI získat přístupový token pro azure ad ověřeného uživatele z kroku 1 pro přístup k databázi Azure pro MySQL.

Příklad (pro veřejný cloud):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Výše uvedená hodnota prostředku musí být zadána přesně tak, jak je znázorněno. Pro ostatní cloudy lze hodnotu prostředku vyhledat pomocí:

```shell
az cloud show
```

Pro Azure CLI verze 2.0.71 a novější příkaz lze zadat v následující pohodlnější verzi pro všechny mraky:

```shell
az account get-access-token --resource-type oss-rdbms
```

Po úspěšném ověření Azure AD vrátí přístupový token:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Token je řetězec Base 64, který kóduje všechny informace o ověřeném uživateli a který je zacílený na službu Azure Database for MySQL.

> [!NOTE]
> Platnost přístupového tokenu je mezi 5 minutami až 60 minutami. Doporučujeme získat přístupový token těsně před zahájením přihlášení do Azure Database pro MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Krok 3: Použití tokenu jako hesla pro přihlášení pomocí MySQL

Při připojování je třeba použít přístupový token jako uživatelské heslo MySQL. Při použití gui klientů, jako je MySQLWorkbench, můžete použít výše uvedenou metodu k načtení tokenu. 

Při použití vodicí hodu můžete použít tuto krátkou ručnici k připojení: 

**Příklad (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Všimněte si nastavení "enable-cleartext-plugin" – musíte použít podobnou konfiguraci s ostatními klienty, abyste se ujistili, že token bude odeslán na server bez zachycování.

Nyní jste ověřeni na serveru MySQL pomocí ověřování Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Vytváření uživatelů Azure AD v Azure Database for MySQL

Pokud chcete přidat uživatele Azure AD do databáze Azure Database for MySQL, proveďte po připojení následující kroky (další část o připojení):

1. Nejprve se ujistěte, `<user>@yourtenant.onmicrosoft.com` že uživatel Azure AD je platný uživatel v tenantovi Azure AD.
2. Přihlaste se k instanci Azure Database for MySQL jako uživatel Azure AD Admin.
3. Vytvořte `<user>@yourtenant.onmicrosoft.com` uživatele v Azure Database for MySQL.

**Příklad:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

U uživatelských jmen, která přesahují 32 znaků, se doporučuje použít alias, který se použije při připojování: 

Příklad:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Ověřování uživatele prostřednictvím služby Azure AD neposkytuje uživateli žádná oprávnění k přístupu k objektům v databázi Azure pro mySQL. Musíte uživateli udělit požadovaná oprávnění ručně.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Vytváření skupin Azure AD v Azure Database for MySQL

Chcete-li povolit skupinu Azure AD pro přístup k databázi, použijte stejný mechanismus jako pro uživatele, ale místo toho zadejte název skupiny:

**Příklad:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Při přihlašování budou členové skupiny používat své osobní přístupové tokeny, ale podepíší se názvem skupiny zadaným jako uživatelské jméno.

## <a name="token-validation"></a>Ověření tokenu

Ověřování Azure AD v Azure Database for MySQL zajišťuje, že uživatel existuje na serveru MySQL a zkontroluje platnost tokenu ověřením obsahu tokenu. Jsou prováděny následující kroky ověření tokenu:

-   Token je podepsaný službou Azure AD a nebyl zfalšován
-   Token byl vydán Službou Azure AD pro klienta přidruženého k serveru.
-   Platnost tokenu nevypršela.
-   Token je pro azure databáze pro prostředek MySQL (a ne jiný prostředek Azure)

## <a name="compatibility-with-application-drivers"></a>Kompatibilita s ovladači aplikací

Většina ovladačů je podporována, ale ujistěte se, že používáte nastavení pro odesílání hesla ve prostém textu, takže token bude odeslán bez evidencí.

* C/C++
  * libmysqlclient: Podporováno
  * mysql-connector-c++: Podporováno
* Java
  * Konektor/J (mysql-connector-java): Podporováno, musí využívat `useSSL` nastavení
* Python
  * Konektor/Python: Podporováno
* Ruby
  * mysql2: Podporováno
* .NET
  * mysql-connector-net: Podporováno, je třeba přidat plugin pro mysql_clear_password
  * mysql-net/MySqlConnector: Podporováno
* Node.js
  * mysqljs: Není podporováno (neodesílá token ve maprostém textu bez opravy)
  * node-mysql2: Podporováno
* Perl
  * DBD::mysql: Podporováno
  * Net::MySQL: Není podporováno
* Přejít
  * go-sql-driver: Podporováno, přidat `?tls=true&allowCleartextPasswords=true` do připojovacího řetězce

## <a name="next-steps"></a>Další kroky

* Projděte si celkové koncepty [ověřování Azure Active Directory pomocí Azure Database for MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
