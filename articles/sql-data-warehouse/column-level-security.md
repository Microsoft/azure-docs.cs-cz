---
title: Zabezpečení na úrovni sloupce – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Zabezpečení na úrovni sloupce (CLS) umožňuje řízení přístupu na sloupce tabulky databáze na základě kontextu spuštění uživatele nebo jejich členství ve skupině. Kompatibilní se Specifikací zjednodušuje návrh a psaní kódu zabezpečení v aplikaci. Specifikace CLS umožňuje implementovat omezení na sloupci přístup.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 06/15/2018
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: 1765c92ad10fa35af98e7c7314eb44c3a119f422
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301051"
---
# <a name="column-level-security"></a>Zabezpečení na úrovni sloupce 
Zabezpečení na úrovni sloupce (CLS) umožňuje řízení přístupu na sloupce tabulky databáze na základě kontextu spuštění uživatele nebo jejich členství ve skupině.  

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

Kompatibilní se Specifikací zjednodušuje návrh a psaní kódu zabezpečení v aplikaci. Specifikace CLS umožňuje implementovat omezení na sloupci přístup chránit citlivá data. Například pro zajištění, aby konkrétní uživatelé mají přístup k jenom určité sloupce tabulky, které jsou relevantní pro jejich oddělení. Logiky přístupu k omezení je umístěn v databázové vrstvě spíše než pryč z dat v jiné aplikační vrstvy. Databáze platí omezení přístupu pokaždé, když dojde k pokusu o tento přístup k datům z libovolné úrovně. Toto omezení je systém zabezpečení spolehlivější a robustní snížením útoku na systém celkové zabezpečení. Kromě toho se specifikací CLS také eliminuje potřebu Úvod do zobrazení k filtrování sloupce pro nastavení omezení přístupu na uživatele. 

Je možné implementovat specifikaci CLS s [udělení](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) příkazu T-SQL. Pomocí tohoto mechanismu ověřování SQL a Azure Active Directory (AAD), jsou podporovány.

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
Následující příklad ukazuje, jak omezit přístup na sloupec "SSN" "Členství" tabulky "TestUser": 

Vytvořte tabulku "Členství" SSN sloupec používá k ukládání čísla sociálního pojištění:

```sql
CREATE TABLE Membership   
  (MemberID int IDENTITY,   
   FirstName varchar(100) NULL,   
   SSN char(9) NOT NULL, 
   LastName varchar(100) NOT NULL,   
   Phone varchar(12) NULL,   
   Email varchar(100) NULL);  
```

"TestUser" Povolit přístup k všechny sloupce kromě sloupce SSN, obsahující citlivá data: 

```sql  
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;   
``` 

Dotazy spouštěné jako "TestUser" selže, pokud obsahují SSN sloupce:

```sql  
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'. 
``` 

## <a name="use-cases"></a>Případy použití
Některé příklady jak kompatibilní se Specifikací používá ještě dnes: 
- Firma z oboru finančních služeb manažerům jediný účet umožňuje přístup pro čísla sociálního pojištění zákazníka (SSN), telefonní čísla a jiné identifikovatelné osobní údaje (PII).
- Zdravotní péče zprostředkovatel umožňuje pouze doktorů tak sestry mít přístup k citlivým zdravotnickými záznamy při neumožňuje členové fakturační oddělení zobrazit tato data.
