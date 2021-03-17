---
title: Zabezpečení na úrovni sloupce pro vyhrazený fond SQL
description: Column-Level Security umožňuje zákazníkům řídit přístup k sloupcům tabulky databáze na základě kontextu spuštění uživatele nebo členství ve skupině, zjednodušit návrh a kódování zabezpečení ve vaší aplikaci a umožňuje implementovat omezení přístupu k sloupci.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: fb34051f7d4b24190806dde939c8cc6d9c2a4896
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679943"
---
# <a name="column-level-security"></a>Zabezpečení na úrovni sloupců

Column-Level Security umožňuje zákazníkům řídit přístup k sloupcům tabulky na základě kontextu spuštění nebo členství ve skupině uživatele.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Vzhledem k tomu, že toto video bylo zveřejněné, [zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) bylo v Azure Synapse k dispozici pro vyhrazený fond SQL.

Zabezpečení na úrovni sloupců zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. umožňuje omezit přístup k sloupci na ochranu citlivých dat. Například zajistěte, aby konkrétní uživatelé měli přístup pouze k určitým sloupcům tabulky, které se vztahují k jejich oddělení. Logika omezení přístupu se nachází v databázové vrstvě, nikoli z dat v jiné aplikační vrstvě. Databáze použije omezení přístupu při každém pokusu o přístup k datům z jakékoli úrovně. Toto omezení zajišťuje spolehlivější a robustní zabezpečení tím, že redukuje plochu celkového systému zabezpečení. Kromě toho zabezpečení na úrovni sloupců také eliminuje nutnost zavedení zobrazení pro odfiltrování sloupců pro ukládání omezení přístupu uživatelům.

Můžete implementovat zabezpečení na úrovni sloupce pomocí příkazu [grant](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL. Tento mechanismus podporuje ověřování SQL i Azure Active Directory (Azure AD).

![Diagram znázorňuje sloupcovou tabulku s prvním sloupcem s uzavřenou visacího zámku nezobrazuje a jejími buňkami oranžová barva, zatímco ostatní sloupce jsou bílé buňky.](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntax

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Příklad

Následující příklad ukazuje, jak omezit `TestUser` přístup k `SSN` sloupci `Membership` tabulky:

Vytvořit `Membership` tabulku se sloupcem SSN, který se používá k uložení čísel sociálního pojištění:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Umožňuje `TestUser` přístup ke všem sloupcům s výjimkou sloupce SSN, který má citlivá data:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Dotazy provedené jako `TestUser` selžou, pokud budou zahrnovat sloupec SSN:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Případy použití

Některé příklady použití zabezpečení na úrovni sloupců v současnosti:

- Společnost DataServices zajišťuje přístup jenom správcům účtů, kteří mají přístup k číslům sociálního pojištění zákazníka (SSN), telefonním číslům a dalším osobním údajům.
- Poskytovatel péče o zdravotní péči umožňuje přístup k citlivým lékařským záznamům jenom lékaři a zdravotní sestry a zároveň brání členům fakturačního oddělení zobrazit tato data.
