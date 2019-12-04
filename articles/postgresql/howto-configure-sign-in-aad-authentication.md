---
title: Použití Azure Active Directory-Azure Database for PostgreSQL-Single server
description: Přečtěte si, jak nastavit Azure Active Directory (AAD) pro ověřování pomocí Azure Database for PostgreSQL-Single server.
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c929ac1c171547a4ff485fc43f0f329440f9c3b5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763636"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Použití Azure Active Directory k ověřování pomocí PostgreSQL

Tento článek vás provede jednotlivými kroky konfigurace Azure Active Directory přístupu pomocí Azure Database for PostgreSQL a o tom, jak se připojit pomocí tokenu Azure AD.

> [!IMPORTANT]
> Ověřování Azure AD pro Azure Database for PostgreSQL je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Nastavení uživatele správce Azure AD

Jenom uživatel s právy pro správu Azure AD může vytvořit nebo povolit uživatele pro ověřování pomocí Azure AD. Pokud chcete vytvořit uživatele správce Azure AD, postupujte prosím podle následujících kroků.

1. V Azure Portal vyberte instanci Azure Database for PostgreSQL, kterou chcete povolit pro Azure AD.
2. V části nastavení vyberte Správce služby Active Directory:

![nastavit správce Azure AD][2]

3. Vyberte platného uživatele Azure AD v tenantovi zákazníka, kterému bude správce Azure AD.

> [!IMPORTANT]
> Při nastavování správce se do serveru Azure Database for PostgreSQL, který má oprávnění správce s úplnými oprávněními, přidá nový uživatel. Uživatel s rolí správce Azure AD v Azure Database for PostgreSQL bude mít `azure_ad_admin`role.

Pro každý PostgreSQL Server a výběr jiného se dá vytvořit jenom jeden správce Azure AD, který přepíše stávajícího správce Azure AD nakonfigurovaného pro server. Můžete určit skupinu Azure AD, ne jednotliví uživatelé, kteří mají více správců. Všimněte si, že se pak budete přihlašovat pomocí názvu skupiny pro účely správy.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Vytváření uživatelů Azure AD v Azure Database for PostgreSQL

Pokud chcete do databáze Azure Database for PostgreSQL přidat uživatele Azure AD, proveďte následující kroky po připojení (viz později v části Jak se připojit):

1. Nejdřív zajistěte, aby byl uživatel služby Azure AD `<user>@yourtenant.onmicrosoft.com` platným uživatelem v tenantovi Azure AD.
2. Přihlaste se ke své instanci Azure Database for PostgreSQL jako uživatel s oprávněními správce Azure AD.
3. Vytvoření `<user>@yourtenant.onmicrosoft.com` role v Azure Database for PostgreSQL.
4. Udělejte `<user>@yourtenant.onmicrosoft.com` člena azure_ad_user role. Tento postup je třeba udělit jenom uživatelům Azure AD.

**Příklad:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Ověřování uživatele prostřednictvím služby Azure AD neuděluje uživateli žádná oprávnění k přístupu k objektům v rámci databáze Azure Database for PostgreSQL. Uživateli musíte udělit požadovaná oprávnění ručně.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Vytváření skupin Azure AD v Azure Database for PostgreSQL

Pokud chcete skupině Azure AD povolit přístup k vaší databázi, použijte stejný mechanismus jako u uživatelů, ale místo toho zadejte název skupiny:

**Příklad:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Pokud se přihlásíte, budou členové skupiny používat své osobní přístupové tokeny, ale budou se podepisovat pomocí názvu skupiny zadaného jako uživatelské jméno.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Připojení k Azure Database for PostgreSQL pomocí Azure AD

Následující diagram vysoké úrovně shrnuje pracovní postup použití ověřování Azure AD s Azure Database for PostgreSQL:

![Tok ověřování][1]

Navrhli jsme integraci Azure AD pro práci s běžnými nástroji PostgreSQL, jako je psql, které nepodporují Azure AD a podporují zadání uživatelského jména a hesla jenom při připojování k PostgreSQL. Token Azure AD předáte jako heslo, jak je znázorněno na obrázku výše.

V současné době jsme otestovali následující klienty:

- psql CommandLine (použití proměnné PGPASSWORD k předání tokenu, viz níže)
- Azure Data Studio (pomocí rozšíření PostgreSQL)
- Ostatní libpq klienti (např. běžné aplikační architektury a ORMs)

> [!NOTE]
> Uvědomte si prosím, že použití tokenu Azure AD s pgAdmin se v tuto chvíli nepodporuje, protože má pevně zakódované omezení 256 znaků pro hesla (které token překročí).

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
> Uvědomte si prosím, že při načítání přístupového tokenu Azure AD v Azure Cloud Shell budete muset explicitně volat `az login` a znovu se přihlásit (v samostatném okně s kódem). Po přihlášení bude příkaz `get-access-token` fungovat podle očekávání.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Krok 2: načtení přístupového tokenu Azure AD

Vyvolejte nástroj Azure CLI, který získá přístupový token pro ověřeného uživatele Azure AD z kroku 1 pro přístup k Azure Database for PostgreSQL.

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

Token je základní řetězec 64, který zakóduje všechny informace o ověřeném uživateli a cílí na službu Azure Database for PostgreSQL.

> [!NOTE]
> Platnost přístupového tokenu je kdekoli mezi 5 minutami a 60 minutami. Než začnete s přihlášením k Azure Database for PostgreSQL, doporučujeme získat přístupový token těsně před inicializací.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Krok 3: použití tokenu jako hesla pro přihlášení pomocí PostgreSQL

Při připojování musíte použít přístupový token jako heslo uživatele PostgreSQL.

Při použití klienta příkazového řádku `psql` musí být přístupový token předaný prostřednictvím proměnné prostředí `PGPASSWORD`, protože přístupový token překračuje délku hesla, kterou `psql` může přijmout přímo:

Příklad Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Příklad pro Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Nyní můžete iniciovat připojení pomocí Azure Database for PostgreSQL jako obvykle:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres"
```

Nyní jste ověřeni na server PostgreSQL pomocí ověřování Azure AD.

## <a name="token-validation"></a>Ověření tokenu

Ověřování Azure AD v systému Azure Database for PostgreSQL zajišťuje, že uživatel existuje na serveru PostgreSQL a kontroluje platnost tokenu ověřením obsahu tokenu. Provedou se následující kroky ověření tokenu:

-   Token je podepsán službou Azure AD a nebyl zfalšován.
-   Služba Azure AD vydala token pro tenanta přidruženého k serveru.
-   Platnost tokenu nevypršela.
-   Token pro prostředek Azure Database for PostgreSQL (a ne jiný prostředek Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrace stávajících uživatelů PostgreSQL do ověřování založeného na službě Azure AD

Můžete povolit ověřování Azure AD pro stávající uživatele. Je třeba vzít v úvahu dva případy:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Případ 1: PostgreSQL uživatelské jméno odpovídá hlavnímu názvu uživatele Azure AD

V nepravděpodobném případě, že stávající uživatelé už se shodují s uživatelskými jmény služby Azure AD, můžete jim udělit `azure_ad_user` roli, aby se mohly povolit pro ověřování Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Nyní se budou moci přihlásit pomocí přihlašovacích údajů služby Azure AD místo používání dříve nakonfigurovaných hesel uživatele PostgreSQL.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Případ 2: uživatelské jméno PostgreSQL se liší od hlavního názvu uživatele služby Azure AD.

Pokud uživatel PostgreSQL neexistuje v Azure AD nebo má jiné uživatelské jméno, můžete k ověření jako tento uživatel PostgreSQL použít skupiny Azure AD. Stávající uživatele Azure Database for PostgreSQL můžete migrovat do služby Azure AD tak, že vytvoříte skupinu Azure AD s názvem, který odpovídá PostgreSQL uživateli, a potom udělíte roli azure_ad_user existujícímu uživateli PostgreSQL:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

To předpokládá, že jste ve službě Azure AD vytvořili skupinu "DBReadUser". Uživatelé patřící do této skupiny se nyní budou moci přihlásit k databázi jako tento uživatel.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o celkových konceptech [Azure Active Directory ověřování pomocí Azure Database for PostgreSQL-Single server](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
