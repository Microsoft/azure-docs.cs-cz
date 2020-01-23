---
title: Co je zabezpečení na úrovni sloupců pro SQL Data Warehouse?
description: Zabezpečení na úrovni sloupců umožňuje zákazníkům řídit přístup k sloupcům tabulky databáze na základě kontextu spuštění uživatele nebo členství ve skupině, zjednodušit návrh a kódování zabezpečení ve vaší aplikaci a umožňuje implementovat omezení na sloupec. stoupit.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 344701989a753e17d8a026f6bb771a6030bdb71f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513044"
---
# <a name="column-level-security"></a>Zabezpečení na úrovni sloupců

Zabezpečení na úrovni sloupců umožňuje zákazníkům řídit přístup k sloupcům tabulky na základě kontextu spuštění nebo členství ve skupině uživatele.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Vzhledem k tomu, že toto video bylo zaslané [zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) , SQL Data Warehouse. 

Zabezpečení na úrovni sloupců zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. umožňuje omezit přístup k sloupci na ochranu citlivých dat. Například zajistěte, aby konkrétní uživatelé měli přístup pouze k určitým sloupcům tabulky, které se vztahují k jejich oddělení. Logika omezení přístupu se nachází v databázové vrstvě, nikoli z dat v jiné aplikační vrstvě. Databáze použije omezení přístupu při každém pokusu o přístup k datům z jakékoli úrovně. Toto omezení zajišťuje spolehlivější a robustní zabezpečení tím, že redukuje plochu celkového systému zabezpečení. Kromě toho zabezpečení na úrovni sloupců také eliminuje nutnost zavedení zobrazení pro odfiltrování sloupců pro ukládání omezení přístupu uživatelům.

Můžete implementovat zabezpečení na úrovni sloupce pomocí příkazu [grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL. Tento mechanismus podporuje ověřování SQL i Azure Active Directory (AAD).

![specifikace CLS](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntaxe

```sql
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

## <a name="example"></a>Příklad:
Následující příklad ukazuje, jak omezit `TestUser` přístup k sloupci `SSN` tabulky `Membership`:

Vytvoří `Membership`ovou tabulku se sloupcem SSN, který se používá k uložení čísel sociálního pojištění:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Povolí `TestUser` přístup ke všem sloupcům s výjimkou sloupce SSN, který má citlivá data:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Dotazy spouštěné jako `TestUser` selžou, pokud budou zahrnovat sloupec SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Případy použití

Některé příklady použití zabezpečení na úrovni sloupců v současnosti:

- Společnost DataServices zajišťuje přístup jenom správcům účtů, kteří mají přístup k číslům sociálního pojištění zákazníka (rodné číslo), telefonním číslům a dalším identifikovatelným osobním údajům (PII).
- Poskytovatel péče o zdravotní péči umožňuje přístup k citlivým lékařským záznamům jenom lékaři a zdravotní sestry a zároveň brání členům fakturačního oddělení zobrazit tato data.
