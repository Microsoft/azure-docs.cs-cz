---
title: Vytváření uživatelů – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s Azure Database for PostgreSQL – Citus (škálování).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77484923"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Vytváření uživatelů v Azure Database for PostgreSQL – Citus (škálování)

> [!NOTE]
> Pojem "uživatelé" odkazují na uživatele v rámci skupiny serverů Citus (). Pokud se chcete dozvědět víc o uživatelích předplatného Azure a jejich oprávnění, přečtěte si [článek o řízení přístupu na základě role (RBAC) Azure](../role-based-access-control/built-in-roles.md) nebo si přečtěte, [jak přizpůsobovat role](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>Účet správce serveru

Modul PostgreSQL používá [role](https://www.postgresql.org/docs/current/sql-createrole.html) k řízení přístupu k databázovým objektům a nově vytvořená skupina serverů Citus () obsahuje několik rolí předem definovaných:

* [Výchozí role PostgreSQL](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Vzhledem k tomu, že škálovatelná služba PaaS je spravovaná, jenom společnost Microsoft se `postgres` může přihlásit pomocí role superuživatele. V `citus` případě omezeného přístupu pro správu poskytuje role měřítko.

Oprávnění pro `citus` roli:

* Přečtěte si všechny proměnné konfigurace, dokonce i proměnné, které jsou obvykle viditelné pouze pro uživatele.
* Přečtěte si\_všechna\_ \* zobrazení statistiky PG a používejte různá rozšíření související s statistikami – dokonce i zobrazení nebo rozšíření, která se běžně zobrazují jenom pro uživatele.
* Spouštějte funkce monitorování, které mohou mít přístup ke sdílení zámků na tabulkách, což může trvat dlouhou dobu.
* [Vytvoření rozšíření PostgreSQL](concepts-hyperscale-extensions.md) (protože role je členem `azure_pg_admin`).

`citus` Role má zejména určitá omezení:

* Nejde vytvořit role.
* Nejde vytvořit databáze.

## <a name="how-to-create-additional-user-roles"></a>Jak vytvořit další role uživatelů

Jak už bylo zmíněno, účet `citus` správce nemá oprávnění k vytváření dalších uživatelů. Chcete-li přidat uživatele, použijte rozhraní Azure Portal.

1. Přejděte na stránku **role** pro skupinu serverů s vlastním škálováním a klikněte na **+ Přidat**:

   ![Stránka role](media/howto-hyperscale-create-users/1-role-page.png)

2. Zadejte název role a heslo. Klikněte na **Uložit**.

   ![Přidat roli](media/howto-hyperscale-create-users/2-add-user-fields.png)

Uživatel bude vytvořen v uzlu koordinátor skupiny serverů a bude šířen do všech pracovních uzlů. Role vytvořené prostřednictvím Azure Portal mají `LOGIN` atribut, což znamená, že se jedná o skutečné uživatele, kteří se mohou přihlásit k databázi.

## <a name="how-to-modify-privileges-for-user-role"></a>Postup úpravy oprávnění pro roli uživatele

Nové role uživatele se běžně používají k poskytnutí přístupu k databázi s omezenými oprávněními. Chcete-li upravit uživatelská oprávnění, použijte standardní příkazy PostgreSQL pomocí nástroje, jako je například PgAdmin nebo psql. (Další informace najdete v tématu [připojení pomocí psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) v rychlém startu (Citus).)

Chcete-li například povolit `db_user` čtení `mytable`, udělte oprávnění:

```sql
GRANT SELECT ON mytable TO db_user;
```

Citus () šíří příkazy pro udělení jedné tabulky prostřednictvím celého clusteru a používá je na všech pracovních uzlech. Granty pro systém (například pro všechny tabulky ve schématu) ale musí být spuštěny na každém uzlu data.  Použijte `run_command_on_workers()` pomocnou funkci:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Jak odstranit uživatelskou roli nebo změnit heslo

Chcete-li aktualizovat uživatele, přejděte na stránku **role** pro skupinu serverů s vlastním škálováním a klikněte na tlačítko se třemi tečkami. **..** vedle uživatele. Tři tečky otevřou nabídku pro odstranění uživatele nebo resetování hesla.

   ![Upravit roli](media/howto-hyperscale-create-users/edit-role.png)

`citus` Role má oprávnění a nelze ji odstranit.

## <a name="next-steps"></a>Další kroky

Otevřete bránu firewall pro IP adresy nových uživatelských počítačů, abyste jim umožnili připojení: [Vytvoření a Správa pravidel brány firewall Citus () pomocí Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Další informace o správě uživatelských účtů databáze najdete v dokumentaci k produktu PostgreSQL:

* [Role a oprávnění databáze](https://www.postgresql.org/docs/current/static/user-manag.html)
* [UDĚLIT syntaxi](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Oprávnění](https://www.postgresql.org/docs/current/static/ddl-priv.html)
