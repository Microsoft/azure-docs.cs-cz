---
title: Vytváření uživatelů – Hyperscale (Citus) – databáze Azure pro PostgreSQL
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s databází Azure pro PostgreSQL – Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484923"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Vytváření uživatelů v databázi Azure pro PostgreSQL – hyperškálování (Citus)

> [!NOTE]
> Termín "uživatelé" odkazuje na uživatele v rámci skupiny serverů Hyperscale (Citus). Chcete-li se místo toho dozvědět o uživatelích předplatného Azure a jejich oprávněních, navštivte článek o [řízení přístupu na základě rolí Azure (RBAC)](../role-based-access-control/built-in-roles.md) nebo si přečtěte, [jak přizpůsobit role](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Účet správce serveru

Modul PostgreSQL používá [role](https://www.postgresql.org/docs/current/sql-createrole.html) k řízení přístupu k databázovým objektům a nově vytvořená skupina serverů Hyperscale (Citus) je dodávána s několika předem definovanými rolemi:

* [Výchozí role PostgreSQL](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Vzhledem k tomu, že Hyperscale je spravovanou `postgres` službou PaaS, může se přihlásit pouze Microsoft s rolí super uživatele. Pro omezený přístup pro správu Hyperscale poskytuje `citus` roli.

Oprávnění pro `citus` roli:

* Přečtěte si všechny konfigurační proměnné, dokonce i proměnné, které jsou obvykle viditelné pouze pro superuživatele.
* Přečtěte\_si\_ \* všechny pg stat zobrazení a používat různé statistiky-související rozšíření - dokonce i pohledy nebo rozšíření obvykle viditelné pouze pro superusers.
* Spouštět funkce monitorování, které mohou mít access share zámky na tabulkách, potenciálně po dlouhou dobu.
* [Vytvořte rozšíření PostgreSQL](concepts-hyperscale-extensions.md) (protože role `azure_pg_admin`je členem).

`citus` Zejména, role má určitá omezení:

* Nelze vytvořit role
* Nelze vytvořit databáze

## <a name="how-to-create-additional-user-roles"></a>Jak vytvořit další role uživatelů

Jak již bylo `citus` zmíněno, účet správce nemá oprávnění k vytvoření dalších uživatelů. Chcete-li přidat uživatele, použijte rozhraní portálu Azure.

1. Přejděte na stránku **Role** pro skupinu serverů Hyperscale a klikněte na **+ Přidat**:

   ![Stránka role](media/howto-hyperscale-create-users/1-role-page.png)

2. Zadejte název a heslo role. Klikněte na **Uložit**.

   ![Přidat roli](media/howto-hyperscale-create-users/2-add-user-fields.png)

Uživatel bude vytvořen v uzlu koordinátora skupiny serverů a rozšířen do všech pracovních uzlů. Role vytvořené prostřednictvím portálu Azure mají `LOGIN` atribut, což znamená, že jsou skuteční uživatelé, kteří se můžou přihlásit k databázi.

## <a name="how-to-modify-privileges-for-user-role"></a>Jak upravit oprávnění pro roli uživatele

Nové uživatelské role se běžně používají k poskytování přístupu k databázi s omezenými oprávněními. Chcete-li upravit uživatelská oprávnění, použijte standardní příkazy PostgreSQL pomocí nástroje, jako je PgAdmin nebo psql. (Viz [připojení s psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) v Hyperscale (Citus) rychlý start.)

Chcete-li například `db_user` `mytable`povolit čtení , udělte oprávnění:

```sql
GRANT SELECT ON mytable TO db_user;
```

Hyperscale (Citus) šíří jednotací příkazy GRANT prostřednictvím celého clusteru a aplikuje je na všechny pracovní uzly. Však grants, které jsou v celém systému (například pro všechny tabulky ve schématu) je třeba spustit na každém uzlu data.  Použijte `run_command_on_workers()` pomocnou funkci:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Jak odstranit roli uživatele nebo změnit jeho heslo

Chcete-li uživatele aktualizovat, navštivte stránku **Role** pro skupinu serverů Hyperscale a klikněte na tři tečky **...** vedle uživatele. Elipsy otevřou nabídku pro odstranění uživatele nebo obnovení hesla.

   ![Úprava role](media/howto-hyperscale-create-users/edit-role.png)

Role `citus` je privilegovaná a nelze ji odstranit.

## <a name="next-steps"></a>Další kroky

Otevřete bránu firewall pro IP adresy počítačů nových uživatelů, abyste jim umožnili připojení: [Vytvořte a spravujte pravidla brány firewall Hyperscale (Citus) pomocí portálu Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Další informace o správě uživatelských účtů databáze naleznete v dokumentaci k produktu PostgreSQL:

* [Databázové role a oprávnění](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Syntaxe GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Oprávnění](https://www.postgresql.org/docs/current/static/ddl-priv.html)
