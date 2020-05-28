---
title: Použití Azure Active Directory-Azure Database for MySQL
description: Přečtěte si, jak nastavit Azure Active Directory (Azure AD) pro ověřování pomocí Azure Database for MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 8ef16f581a4b945d3a5e6ef58166eeed900f3bb3
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140884"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Použití Azure Active Directory k ověřování pomocí MySQL

Tento článek vás provede jednotlivými kroky konfigurace Azure Active Directory přístupu pomocí Azure Database for MySQL a o tom, jak se připojit pomocí tokenu Azure AD.

> [!IMPORTANT]
> Ověřování Azure Active Directory je k dispozici pouze pro MySQL 5,7 a novější.

## <a name="setting-the-azure-ad-admin-user"></a>Nastavení uživatele správce Azure AD

Jenom uživatel s právy pro správu Azure AD může vytvořit nebo povolit uživatele pro ověřování pomocí Azure AD. Pokud chcete vytvořit uživatele správce Azure AD, postupujte prosím podle následujících kroků.

1. V Azure Portal vyberte instanci Azure Database for MySQL, kterou chcete povolit pro Azure AD.
2. V části nastavení vyberte Správce služby Active Directory:

![nastavit správce Azure AD][2]

3. Vyberte platného uživatele Azure AD v tenantovi zákazníka, kterému bude správce Azure AD.

> [!IMPORTANT]
> Při nastavování správce se do serveru Azure Database for MySQL, který má oprávnění správce s úplnými oprávněními, přidá nový uživatel.

Pro každý server MySQL se dá vytvořit jenom jeden správce Azure AD a výběr jiného serveru přepíše stávajícího správce Azure AD nakonfigurovaného pro server.

V budoucí verzi budeme podporovat zadání skupiny Azure AD, ne jednotlivého uživatele, aby měli více správců, ale tato akce se ale v tuto chvíli nepodporuje.

Po nakonfigurování správce se teď můžete přihlásit:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Připojení k Azure Database for MySQL pomocí Azure AD

Následující diagram vysoké úrovně shrnuje pracovní postup použití ověřování Azure AD s Azure Database for MySQL:

![tok ověřování][1]

Navrhli jsme integraci Azure AD pro práci s běžnými nástroji MySQL, jako je MySQL CLI, které nepodporují Azure AD, a podporují zadání uživatelského jména a hesla jenom při připojování k MySQL. Token Azure AD předáte jako heslo, jak je znázorněno na obrázku výše.

V současné době jsme otestovali následující klienty:

- MySQLWorkbench 
- Rozhraní příkazového řádku MySQL

Také jsme otestovali nejběžnější ovladače aplikací, na konci této stránky vidíte podrobnosti.

Jedná se o kroky, které bude uživatel nebo aplikace potřebovat k ověření pomocí služby Azure AD popsané níže:

### <a name="step-1-authenticate-with-azure-ad"></a>Krok 1: ověření pomocí Azure AD

Ujistěte se, že máte nainstalované rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

Vyvolejte nástroj Azure CLI pro ověřování pomocí Azure AD. Vyžaduje vám zadání ID uživatele a hesla služby Azure AD.

```
az login
```

Tento příkaz otevře okno prohlížeče na stránce ověřování Azure AD.

> [!NOTE]
> K provedení těchto kroků můžete také použít Azure Cloud Shell.
> Uvědomte si prosím, že při načítání přístupového tokenu Azure AD v Azure Cloud Shell budete muset explicitně zavolat `az login` a znovu se přihlásit (v samostatném okně s kódem). Po přihlášení `get-access-token` bude příkaz fungovat podle očekávání.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Krok 2: načtení přístupového tokenu Azure AD

Vyvolejte nástroj Azure CLI, který získá přístupový token pro ověřeného uživatele Azure AD z kroku 1 pro přístup k Azure Database for MySQL.

Příklad (pro veřejný cloud):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Výše uvedená hodnota prostředku musí být zadaná přesně tak, jak je znázorněno. U ostatních cloudů se hodnota prostředku dá vyhledat pomocí:

```shell
az cloud show
```

Pro Azure CLI verze 2.0.71 a novější je možné příkaz zadat v následující pohodlnější verzi pro všechny cloudy:

```shell
az account get-access-token --resource-type oss-rdbms
```

Po úspěšném ověření bude služba Azure AD vracet přístupový token:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Token je základní řetězec 64, který zakóduje všechny informace o ověřeném uživateli a cílí na službu Azure Database for MySQL.

> [!NOTE]
> Platnost přístupového tokenu je kdekoli mezi 5 minutami a 60 minutami. Než začnete s přihlášením k Azure Database for MySQL, doporučujeme získat přístupový token těsně před inicializací.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Krok 3: použití tokenu jako hesla pro přihlášení pomocí MySQL

Při připojování musíte použít přístupový token jako heslo uživatele MySQL. Při použití klientů GUI, jako je MySQLWorkbench, můžete k načtení tokenu použít výše uvedenou metodu. 

Při použití rozhraní příkazového řádku můžete použít tuto krátkou ruku k připojení: 

**Příklad (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Poznamenejte si nastavení Povolit-nešifrovaný modul plug-in – je třeba použít podobnou konfiguraci s ostatními klienty, aby se zajistilo, že se token pošle na server bez hashování.

Nyní jste ověřeni na server MySQL pomocí ověřování Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Vytváření uživatelů Azure AD v Azure Database for MySQL

Pokud chcete do databáze Azure Database for MySQL přidat uživatele Azure AD, proveďte následující kroky po připojení (viz později v části Jak se připojit):

1. Nejdřív zajistěte, aby byl uživatel Azure AD `<user>@yourtenant.onmicrosoft.com` platným uživatelem v Tenantovi Azure AD.
2. Přihlaste se ke své instanci Azure Database for MySQL jako uživatel s oprávněními správce Azure AD.
3. Vytvoří uživatele `<user>@yourtenant.onmicrosoft.com` v Azure Database for MySQL.

**Případě**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Pro uživatelská jména, která jsou delší než 32 znaků, doporučujeme místo toho použít alias, který se použije při připojování: 

Příklad:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Ověřování uživatele prostřednictvím služby Azure AD neuděluje uživateli žádná oprávnění k přístupu k objektům v rámci databáze Azure Database for MySQL. Uživateli musíte udělit požadovaná oprávnění ručně.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Vytváření skupin Azure AD v Azure Database for MySQL

Pokud chcete skupině Azure AD povolit přístup k vaší databázi, použijte stejný mechanismus jako u uživatelů, ale místo toho zadejte název skupiny:

**Případě**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Pokud se přihlásíte, budou členové skupiny používat své osobní přístupové tokeny, ale budou se podepisovat pomocí názvu skupiny zadaného jako uživatelské jméno.

## <a name="token-validation"></a>Ověření tokenu

Ověřování Azure AD v Azure Database for MySQL zajišťuje, aby uživatel existoval na serveru MySQL, a kontroluje platnost tokenu ověřením obsahu tokenu. Provedou se následující kroky ověření tokenu:

-   Token je podepsán službou Azure AD a nebyl zfalšován.
-   Služba Azure AD vydala token pro tenanta přidruženého k serveru.
-   Platnost tokenu nevypršela.
-   Token pro prostředek Azure Database for MySQL (a ne jiný prostředek Azure)

## <a name="compatibility-with-application-drivers"></a>Kompatibilita s ovladači aplikace

Většina ovladačů je podporovaná, ale nezapomeňte použít nastavení pro odeslání hesla v nešifrovaných textech, takže se token pošle bez úprav.

* C/C++
  * libmysqlclient: podporované
  * MySQL-Connector-c + +: podporováno
* Java
  * Connector/J (MySQL-Connector-Java): podporováno, musí používat `useSSL` nastavení
* Python
  * Konektor/Python: podporováno
* Ruby
  * mysql2: podporované
* .NET
  * MySQL-Connector-NET: podporováno, je nutné přidat modul plug-in pro mysql_clear_password
  * MySQL-NET/MySqlConnector: podporováno
* Node.js
  * mysqljs: Nepodporováno (neposílá token v nešifrovaném textu bez opravy)
  * Node-mysql2: podporováno
* Perl
  * DBD:: MySQL: podporováno
  * NET:: MySQL: nepodporováno
* Přejít
  * ovladač-SQL-Driver: podporováno, přidat `?tls=true&allowCleartextPasswords=true` do připojovacího řetězce

## <a name="next-steps"></a>Další kroky

* Projděte si obecné koncepty [ověřování Azure Active Directory s](concepts-azure-ad-authentication.md) využitím Azure Database for MySQL

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
