---
title: Vytváření uživatelů v Azure Database for PostgreSQL – Citus (škálování)
description: Tento článek popisuje, jak můžete vytvořit nové uživatelské účty pro interakci s Azure Database for PostgreSQL – Citus (škálování).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7187135b29f0a9a790c032330c73bcb1ae27229b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515938"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Vytváření uživatelů v Azure Database for PostgreSQL – Citus (škálování)

Tento článek popisuje, jak můžete vytvářet uživatele v rámci skupiny serverů Citus (). Pokud se chcete dozvědět víc o uživatelích předplatného Azure a jejich oprávnění, přečtěte si [článek o řízení přístupu na základě role (RBAC) Azure](../role-based-access-control/built-in-roles.md) nebo si přečtěte, [jak přizpůsobovat role](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>Účet správce serveru

Nově vytvořená skupina serverů Citus () obsahuje několik předem definovaných rolí:

* [Výchozí role PostgreSQL](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

Uživatel s oprávněními správce serveru *citus*je členem role *azure_pg_admin* .
Nejedná se však o součást role *Postgres* (Super User).  Vzhledem k tomu, že škálovatelná služba PaaS je spravovaná, pouze společnost Microsoft je součástí role Super uživatel.

Modul PostgreSQL využívá oprávnění k řízení přístupu k databázovým objektům, jak je popsáno v [dokumentaci k produktu PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html).
V Azure Database for PostgreSQL má uživatel pro správu serveru udělená tato oprávnění: LOGIN, NOSUPERUSER, DĚDĚNí, CREATEDB, CREATEROLE, inreplikace.

## <a name="how-to-create-additional-users"></a>Vytváření dalších uživatelů

Účet správce *citus* nemá oprávnění k vytváření dalších uživatelů. Chcete-li přidat uživatele, použijte místo toho Azure Portal.

1. Přejděte na stránku **role** pro skupinu serverů s vlastním škálováním a klikněte na **+ Přidat**:

   ![Stránka role](media/howto-hyperscale-create-users/1-role-page.png)

2. Zadejte název role a heslo. Klikněte na **Uložit**.

   ![Přidat roli](media/howto-hyperscale-create-users/2-add-user-fields.png)

Uživatel bude vytvořen v uzlu koordinátor skupiny serverů a bude šířen do všech pracovních uzlů.

## <a name="how-to-delete-a-user-or-change-their-password"></a>Jak odstranit uživatele nebo změnit heslo

Přejděte na stránku **role** pro skupinu serverů s vlastním škálováním a klikněte na tlačítko se třemi tečkami **...** vedle uživatele. Tři tečky otevřou nabídku pro odstranění uživatele nebo resetování hesla.

   ![Upravit roli](media/howto-hyperscale-create-users/edit-role.png)

Role *citus* má oprávnění Privileged a nelze ji odstranit.

## <a name="how-to-modify-privileges-for-role"></a>Jak změnit oprávnění pro roli

Nové role se běžně používají k poskytnutí přístupu k databázi s omezenými oprávněními. Chcete-li upravit uživatelská oprávnění, použijte standardní příkazy PostgreSQL pomocí nástroje, jako je například PgAdmin nebo psql. (Další informace najdete v tématu [připojení pomocí psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) v rychlém startu (Citus).)

Chcete-li například povolit *db_user* čtení *myTable*, udělte oprávnění:

```sql
GRANT SELECT ON mytable TO db_user;
```

Citus () šíří příkazy pro udělení jedné tabulky prostřednictvím celého clusteru a používá je na všech pracovních uzlech. Granty, které jsou celé systému (např. pro všechny tabulky ve schématu), je třeba spustit v každém uzlu data.  Použijte pomocnou funkci *run_command_on_workers ()* :

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Další kroky

Otevřete bránu firewall pro IP adresy nových uživatelských počítačů, abyste jim umožnili připojení: [Vytvoření a Správa pravidel brány firewall Citus () pomocí Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Další informace o správě uživatelských účtů databáze najdete v dokumentaci k produktu PostgreSQL:

* [Role a oprávnění databáze](https://www.postgresql.org/docs/current/static/user-manag.html)
* [UDĚLIT syntaxi](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Požadovaná](https://www.postgresql.org/docs/current/static/ddl-priv.html)
