---
title: Použití služby Azure Active Directory – databáze Azure pro PostgreSQL – jeden server
description: Informace o tom, jak nastavit Azure Active Directory (AAD) pro ověřování pomocí Azure Database for PostgreSQL – jeden server
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f5588503825281f407ddbbc2c1c57cd94a9c7ee6
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804703"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Použití služby Azure Active Directory k ověřování pomocí PostgreSQL

Tento článek vás provede kroky, jak nakonfigurovat přístup služby Azure Active Directory pomocí Azure Database for PostgreSQL a jak se připojit pomocí tokenu Azure AD.

> [!IMPORTANT]
> Azure AD ověřování pro Azure Database pro PostgreSQL je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Nastavení uživatele správce Azure AD

Pouze uživatelé správce Azure AD můžete vytvořit nebo povolit uživatele pro ověřování na základě Azure AD. Doporučujeme nepoužívat správce Azure AD pro běžné databázové operace, protože má zvýšená uživatelská oprávnění (např.

Chcete-li nastavit správce Azure AD (můžete použít uživatele nebo skupinu), postupujte podle následujících kroků

1. Na webu Azure Portal vyberte instanci Azure Database for PostgreSQL, kterou chcete povolit pro Azure AD.
2. V části Nastavení vyberte Správce služby Active Directory:

![nastavení správce azure reklamy][2]

3. Vyberte platného uživatele Azure AD v tenantovi zákazníka jako správce Azure AD.

> [!IMPORTANT]
> Při nastavování správce se do databáze Azure pro postgresql server přidá nový uživatel s úplnými oprávněními správce. Roli `azure_ad_admin`bude mít uživatel Azure AD Admin v Azure Database for PostgreSQL .

Na server PostgreSQL lze vytvořit jenom jednoho správce Azure AD a výběr jiného přepíše stávajícího správce Azure AD nakonfigurovaného pro server. Můžete zadat skupinu Azure AD namísto jednotlivého uživatele, který má více správců. Všimněte si, že se pak přihlásíte pomocí názvu skupiny pro účely správy.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Připojení k Azure Database for PostgreSQL pomocí Azure AD

Následující diagram vysoké úrovně shrnuje pracovní postup používání ověřování Azure AD s Azure Database pro PostgreSQL:

![tok ověřování][1]

Navrhli jsme integraci Azure AD pro práci s běžnými nástroji PostgreSQL, jako je psql, které nezhodnou Azure AD a podporují jenom určení uživatelského jména a hesla při připojování k PostgreSQL. Předáme token Azure AD jako heslo, jak je znázorněno na obrázku výše.

V současné době jsme testovali následující klienty:

- příkazový řádek psql (využijte proměnnou PGPASSWORD k předání tokenu, viz níže)
- Azure Data Studio (pomocí rozšíření PostgreSQL)
- Ostatní klienti založené na libpq (např. společné aplikační rámce a ORM)

> [!NOTE]
> Uvědomte si, že použití tokenu Azure AD s pgAdmin není aktuálně podporováno, protože má pevně zakódované omezení 256 znaků pro hesla (které token překračuje).

Toto jsou kroky, které uživatel nebo aplikace bude muset provést ověření pomocí Azure AD popsané níže:

### <a name="step-1-authenticate-with-azure-ad"></a>Krok 1: Ověření pomocí Azure AD

Ujistěte se, že máte [nainstalovaný Azure CLI](/cli/azure/install-azure-cli).

Vyvolání nástroje Azure CLI k ověření pomocí Azure AD. Vyžaduje, abyste své ID uživatele Azure AD a heslo.

```azurecli-interactive
az login
```

Tento příkaz spustí okno prohlížeče na stránku ověřování Azure AD.

> [!NOTE]
> K provedení těchto kroků můžete taky použít Azure Cloud Shell.
> Upozorňujeme, že při načítání přístupového tokenu Azure AD v `az login` Prostředí Azure Cloud Shell budete muset explicitně volat a znovu se přihlásit (v samostatném okně s kódem). Po tomto znaménku v příkazu `get-access-token` bude fungovat podle očekávání.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Krok 2: Načtení přístupového tokenu Azure AD

Vyvolání nástroje Azure CLI získat přístupový token pro azure ad ověřeného uživatele z kroku 1 pro přístup k azure database pro PostgreSQL.

Příklad (pro veřejný cloud):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Výše uvedená hodnota prostředku musí být zadána přesně tak, jak je znázorněno. Pro ostatní cloudy lze hodnotu prostředku vyhledat pomocí:

```azurecli-interactive
az cloud show
```

Pro Azure CLI verze 2.0.71 a novější příkaz lze zadat v následující pohodlnější verzi pro všechny mraky:

```azurecli-interactive
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

Token je řetězec Base 64, který kóduje všechny informace o ověřeném uživateli a který je cílený na službu Azure Database for PostgreSQL.

> [!NOTE]
> Platnost přístupového tokenu je mezi 5 minutami až 60 minutami. Doporučujeme získat přístupový token těsně před zahájením přihlášení do Databáze Azure pro PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Krok 3: Použití tokenu jako hesla pro přihlášení pomocí PostgreSQL

Při připojování je třeba použít přístupový token jako uživatelské heslo PostgreSQL.

Při použití `psql` klienta příkazového řádku musí být `PGPASSWORD` přístupový token předán proměnnou prostředí, `psql` protože přístupový token překračuje délku hesla, která může přímo přijímat:

Příklad systému Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Příklad Linuxu/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Teď můžete nanavázat spojení s Azure Database pro PostgreSQL, jako byste normálně:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Nyní jste ověřeni na serveru PostgreSQL pomocí ověřování Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Vytváření uživatelů Azure AD v Azure Database for PostgreSQL

Pokud chcete přidat uživatele Azure AD do databáze Azure Database for PostgreSQL, proveďte po připojení následující kroky (další část o připojení):

1. Nejprve se ujistěte, `<user>@yourtenant.onmicrosoft.com` že uživatel Azure AD je platný uživatel v tenantovi Azure AD.
2. Přihlaste se k instanci Azure Database for PostgreSQL jako uživatel Azure AD Admin.
3. Vytvořte `<user>@yourtenant.onmicrosoft.com` roli v Azure Database pro PostgreSQL.
4. Uvažte `<user>@yourtenant.onmicrosoft.com` azure_ad_user rolí. To musí být poskytnuta pouze uživatelům Azure AD.

**Příklad:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Ověřování uživatele prostřednictvím služby Azure AD neposkytuje uživateli žádná oprávnění k přístupu k objektům v databázi Azure pro PostgreSQL. Musíte uživateli udělit požadovaná oprávnění ručně.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Vytváření skupin Azure AD v Azure Database for PostgreSQL

Chcete-li povolit skupinu Azure AD pro přístup k databázi, použijte stejný mechanismus jako pro uživatele, ale místo toho zadejte název skupiny:

**Příklad:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Při přihlašování budou členové skupiny používat své osobní přístupové tokeny, ale podepíší se názvem skupiny zadaným jako uživatelské jméno.

## <a name="token-validation"></a>Ověření tokenu

Ověřování Azure AD v Azure Database pro PostgreSQL zajišťuje, že uživatel existuje na serveru PostgreSQL a zkontroluje platnost tokenu ověřením obsahu tokenu. Jsou prováděny následující kroky ověření tokenu:

- Token je podepsaný službou Azure AD a nebyl zfalšován
- Token byl vydán Službou Azure AD pro klienta přidruženého k serveru.
- Platnost tokenu nevypršela.
- Token je pro azure databáze pro prostředek PostgreSQL (a ne jiný prostředek Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrace stávajících uživatelů PostgreSQL na ověřování založené na Azure AD

Ověřování Azure AD můžete povolit pro stávající uživatele. Existují dva případy, které je třeba zvážit:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Případ 1: Uživatelské jméno PostgreSQL odpovídá hlavnímu názvu uživatele služby Azure AD

V nepravděpodobném případě, že vaši stávající uživatelé již odpovídají `azure_ad_user` uživatelským jménům Azure AD, můžete jim udělit roli, abyste je povolili pro ověřování Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Teď se budou moct přihlásit pomocí přihlašovacích údajů Azure AD namísto použití dříve nakonfigurovaného uživatelského hesla PostgreSQL.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Případ 2: Uživatelské jméno PostgreSQL se liší od hlavního názvu uživatele Služby Azure AD

Pokud uživatel PostgreSQL buď neexistuje ve službě Azure AD nebo má jiné uživatelské jméno, můžete použít skupiny Azure AD k ověření jako tento uživatel PostgreSQL. Existující uživatele Azure Database for PostgreSQL můžete migrovat do Azure AD vytvořením skupiny Azure AD s názvem, který odpovídá uživateli PostgreSQL, a pak udělit azure_ad_user role stávajícímu uživateli PostgreSQL:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

To předpokládá, že jste vytvořili skupinu "DBReadUser" ve vašem Azure AD. Uživatelé patřící do této skupiny se nyní budou moci přihlásit k databázi jako tento uživatel.

## <a name="next-steps"></a>Další kroky

* Projděte si celkové koncepty [ověřování Azure Active Directory pomocí databáze Azure pro PostgreSQL – jeden server](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
