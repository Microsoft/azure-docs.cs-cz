---
title: Zabezpečení na úrovni sloupců
description: Zabezpečení na úrovni sloupce (CLS) umožňuje zákazníkům řídit přístup k sloupcům tabulky databáze na základě kontextu spuštění uživatele nebo jejich členství ve skupinách. Specifikace CLS zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. Specifikace CLS umožňuje implementovat omezení přístupu k sloupci.
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
ms.openlocfilehash: 605dfadaf4cd1686b124b120151e6a88a43f1a68
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693082"
---
# <a name="column-level-security"></a>Zabezpečení na úrovni sloupců
Zabezpečení na úrovni sloupce (CLS) umožňuje zákazníkům řídit přístup k sloupcům tabulky databáze na základě kontextu spuštění uživatele nebo jejich členství ve skupinách.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

Specifikace CLS zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. Specifikace CLS umožňuje implementovat omezení přístupu k sloupci pro ochranu citlivých dat. Například zajistěte, aby konkrétní uživatelé měli přístup pouze k určitým sloupcům tabulky, které se vztahují k jejich oddělení. Logika omezení přístupu se nachází v databázové vrstvě, nikoli z dat v jiné aplikační vrstvě. Databáze použije omezení přístupu při každém pokusu o přístup k datům z libovolné úrovně. Toto omezení zajišťuje spolehlivější a robustní systém zabezpečení tím, že snižuje plochu celého systému zabezpečení. Kromě toho CLS také eliminuje nutnost zavedení zobrazení pro odfiltrování sloupců pro ukládání omezení přístupu pro uživatele.

Specifikaci CLS můžete implementovat pomocí příkazu [grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL. Tento mechanismus podporuje ověřování SQL i Azure Active Directory (AAD).

![specifikaci](./media/column-level-security/cls.png)

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

## <a name="example"></a>Příklad
Následující příklad ukazuje, jak omezit "TestUser" na přístup k sloupci ' SSN ' v tabulce členství:

Vytvořte tabulku Membership se sloupcem SSN, která se používá k uložení čísel sociálního pojištění:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Povolí možnost TestUser pro přístup ke všem sloupcům s výjimkou sloupce SSN, který obsahuje citlivá data:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Dotazy spouštěné jako TestUser selžou, pokud budou zahrnovat sloupec SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Případy použití
Některé příklady použití specifikace CLS v současnosti:
- Společnost DataServices zajišťuje přístup jenom správcům účtů, kteří mají přístup k číslům sociálního pojištění zákazníka (rodné číslo), telefonním číslům a dalším identifikovatelným osobním údajům (PII).
- Poskytovatel péče o zdravotní péči umožňuje přístup k citlivým lékařským záznamům jenom lékaři a zdravotní sestry, zatímco neumožňuje členům fakturačního oddělení zobrazit tato data.
