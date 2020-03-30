---
title: Co je zabezpečení na úrovni sloupců pro Azure Synapse?
description: Zabezpečení na úrovni sloupců umožňuje zákazníkům řídit přístup ke sloupcům databázové tabulky na základě kontextu spuštění uživatele nebo členství ve skupině, zjednodušit návrh a kódování zabezpečení ve vaší aplikaci a umožnit implementovat omezení pro sloupec Přístup.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 24ead458232b096a5c69ffe8b45c6298a9da9f75
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349099"
---
# <a name="column-level-security"></a>Zabezpečení na úrovni sloupců

Zabezpečení na úrovni sloupců umožňuje zákazníkům řídit přístup ke sloupcům tabulky na základě kontextu spuštění uživatele nebo členství ve skupině.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Vzhledem k tomu, že toto video bylo zveřejněno [na úrovni řádku zabezpečení](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) bylo k dispozici pro Azure Synapse. 

Zabezpečení na úrovni sloupců zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci a umožňuje omezit přístup ke sloupcům a chránit citlivá data. Například zajištění, že konkrétní uživatelé mají přístup pouze k určitým sloupcům tabulky, které se připodobují k jejich oddělení. Logika omezení přístupu se nachází v databázové vrstvě, nikoli mimo data v jiné aplikační vrstvě. Databáze použije omezení přístupu při každém pokusu o přístup k datům z libovolné vrstvy. Toto omezení činí zabezpečení spolehlivější a robustnější tím, že zužuje plochu celkového bezpečnostního systému. Zabezpečení na úrovni sloupců navíc také eliminuje potřebu zavádět zobrazení pro odfiltrování sloupců pro uložení omezení přístupu pro uživatele.

Zabezpečení na úrovni sloupce můžete implementovat pomocí příkazu [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL. Pomocí tohoto mechanismu jsou podporovány ověřování SQL a Azure Active Directory (AAD).

![Cls](./media/column-level-security/cls.png)

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
Následující příklad ukazuje, `TestUser` jak omezit `SSN` přístup ke `Membership` sloupci tabulky:

Vytvořit `Membership` tabulku se sloupcem SSN, který slouží k ukládání čísel sociálního pojištění:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Povolit `TestUser` přístup ke všem sloupcům s výjimkou sloupce SSN, který obsahuje citlivá data:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Dotazy provedené jako `TestUser` se nezdaří, pokud obsahují sloupec SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Případy použití

Některé příklady, jak se dnes používá zabezpečení na úrovni sloupců:

- Firma poskytující finanční služby umožňuje pouze správcům účtů přístup k číslům sociálního zabezpečení zákazníků (SSN), telefonním číslům a dalším osobním údajům(PII).
- Poskytovatel zdravotní péče umožňuje pouze lékařům a sestrám přístup k citlivým lékařským záznamům a zároveň zabraňuje členům fakturačního oddělení v prohlížení těchto údajů.
