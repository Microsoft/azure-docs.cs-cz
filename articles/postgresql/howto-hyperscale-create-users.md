---
title: Vytváření uživatelů – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s Azure Database for PostgreSQL – Citus (škálování).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/8/2019
ms.openlocfilehash: 84f5a8f638e4a9525b330277ff1eaa26ba035e1a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907407"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Vytváření uživatelů v Azure Database for PostgreSQL – Citus (škálování)

> [!NOTE]
> Pojem "uživatelé" odkazují na uživatele v rámci skupiny serverů Citus (). Pokud se chcete dozvědět víc o uživatelích předplatného Azure a jejich oprávnění, přejděte na [článek řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/built-in-roles.md) nebo si přečtěte, [jak přizpůsobit role](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Účet správce serveru

Modul PostgreSQL používá [role](https://www.postgresql.org/docs/current/sql-createrole.html) k řízení přístupu k databázovým objektům a nově vytvořená skupina serverů Citus () obsahuje několik rolí předem definovaných:

* [Výchozí role PostgreSQL](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Vzhledem k tomu, že škálovatelná služba PaaS je spravovaná, jenom společnost Microsoft se může přihlásit pomocí `postgres` role superuživatele. V případě omezeného přístupu pro správu poskytuje `citus` role měřítko.

Oprávnění pro `citus` roli:

* Přečtěte si všechny proměnné konfigurace, dokonce i proměnné, které jsou obvykle viditelné pouze pro uživatele.
* Přečtěte si \_ všechna \_ \* zobrazení statistiky PG a používejte různá rozšíření související s statistikami – dokonce i zobrazení nebo rozšíření, která se běžně zobrazují jenom pro uživatele.
* Spouštějte funkce monitorování, které mohou mít přístup ke sdílení zámků na tabulkách, což může trvat dlouhou dobu.
* [Vytvoření rozšíření PostgreSQL](concepts-hyperscale-extensions.md) (protože role je členem `azure_pg_admin` ).

`citus`Role má zejména určitá omezení:

* Nejde vytvořit role.
* Nejde vytvořit databáze.

## <a name="how-to-create-additional-user-roles"></a>Jak vytvořit další role uživatelů

Jak už bylo zmíněno, `citus` účet správce nemá oprávnění k vytváření dalších uživatelů. Chcete-li přidat uživatele, použijte rozhraní Azure Portal.

1. Přejděte na stránku **role** pro skupinu serverů s vlastním škálováním a klikněte na **+ Přidat**:

   :::image type="content" source="media/howto-hyperscale-create-users/1-role-page.png" alt-text="Stránka role":::

2. Zadejte název role a heslo. Klikněte na **Uložit**.

   :::image type="content" source="media/howto-hyperscale-create-users/2-add-user-fields.png" alt-text="Přidat roli":::

Uživatel bude vytvořen v uzlu koordinátor skupiny serverů a bude šířen do všech pracovních uzlů. Role vytvořené prostřednictvím Azure Portal mají `LOGIN` atribut, což znamená, že se jedná o skutečné uživatele, kteří se mohou přihlásit k databázi.

## <a name="how-to-modify-privileges-for-user-role"></a>Postup úpravy oprávnění pro roli uživatele

Nové role uživatele se běžně používají k poskytnutí přístupu k databázi s omezenými oprávněními. Chcete-li upravit uživatelská oprávnění, použijte standardní příkazy PostgreSQL pomocí nástroje, jako je například PgAdmin nebo psql. (Další informace najdete v tématu [připojení pomocí psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) v rychlém startu (Citus).)

Chcete-li například povolit `db_user` čtení `mytable` , udělte oprávnění:

```sql
GRANT SELECT ON mytable TO db_user;
```

Citus () šíří příkazy pro udělení jedné tabulky prostřednictvím celého clusteru a používá je na všech pracovních uzlech. Také šíří granty, které jsou celé systému (např. pro všechny tabulky ve schématu):

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Jak odstranit uživatelskou roli nebo změnit heslo

Chcete-li aktualizovat uživatele, přejděte na stránku **role** pro skupinu serverů s vlastním škálováním a klikněte na tlačítko se třemi tečkami. **..** vedle uživatele. Tři tečky otevřou nabídku pro odstranění uživatele nebo resetování hesla.

   :::image type="content" source="media/howto-hyperscale-create-users/edit-role.png" alt-text="Upravit roli":::

`citus`Role má oprávnění a nelze ji odstranit.

## <a name="next-steps"></a>Další kroky

Otevřete bránu firewall pro IP adresy nových uživatelských počítačů, abyste jim umožnili připojení: [Vytvoření a Správa pravidel brány firewall Citus () pomocí Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Další informace o správě uživatelských účtů databáze najdete v dokumentaci k produktu PostgreSQL:

* [Role a oprávnění databáze](https://www.postgresql.org/docs/current/static/user-manag.html)
* [UDĚLIT syntaxi](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Oprávnění](https://www.postgresql.org/docs/current/static/ddl-priv.html)
