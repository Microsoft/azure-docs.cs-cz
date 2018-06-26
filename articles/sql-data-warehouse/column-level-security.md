---
title: Zabezpečení na úrovni sloupce Azure SQL Data Warehouse | Microsoft Docs
description: Zabezpečení na úrovni sloupce (specifikace CLS) umožňuje zákazníkům k řízení přístupu k databázi sloupců tabulky na základě kontextu spuštění uživatele nebo jejich členství ve skupině. Specifikace CLS zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. Specifikace CLS umožňuje implementovat omezení na sloupci přístup.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 06/15/2018
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: 5a916132f705f3c517ee6789b61a3972b2445b62
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938387"
---
# <a name="column-level-security"></a>Zabezpečení na úrovni sloupce 
Zabezpečení na úrovni sloupce (specifikace CLS) umožňuje zákazníkům k řízení přístupu k databázi sloupců tabulky na základě kontextu spuštění uživatele nebo jejich členství ve skupině.  

Specifikace CLS zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. Specifikace CLS umožňuje implementovat omezení na sloupci přístup k chrání citlivá data. Například konkrétním uživatelům přístup zajistí jenom některé sloupce tabulky vztahujících se k jejich oddělení. Logika omezení přístupu je umístěn v databázové vrstvy spíše než tokeny z dat v jiném aplikační vrstvě. Databáze platí omezení přístupu pokaždé, když dojde k pokusu o tento přístup k datům z libovolné úrovně. Díky systému zabezpečení spolehlivější a robustní snížením plochy celkového zabezpečení systému. Kromě toho také nemusí pro představení zobrazení filtrovat sloupce pro nastavení omezení přístupu na uživatele. 

Může implementovat specifikací CLS s [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) příkaz T-SQL. Tento mechanismus ověřování SQL a Azure Active Directory (AAD) jsou podporovány.

![specifikací CLS](./media/column-level-security/cls.png)

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
Následující příklad ukazuje, jak pro omezení přístupu, číslo sociálního pojištění' sloupec 'Členství' tabulky 'TestUser': 

Vytváření tabulek, členství, s SSN sloupec použitý k uložení čísel sociálního pojištění:

```sql
CREATE TABLE Membership   
  (MemberID int IDENTITY,   
   FirstName varchar(100) NULL,   
   SSN char(9) NOT NULL, 
   LastName varchar(100) NOT NULL,   
   Phone varchar(12) NULL,   
   Email varchar(100) NULL);  
```

Povolit 'TestUser' pro přístup k všechny sloupce kromě SSN sloupec, který má citlivá data: 

```sql  
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;   
``` 

Dotazy provést, protože 'TestUser, se nezdaří, pokud obsahují sloupec číslo sociálního pojištění:

```sql  
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'. 
``` 

## <a name="use-cases"></a>Případy použití
Některé příklady jak specifikací CLS je používán dnes: 
- Firma finančních služeb umožňuje pouze účet správce, kteří mají mít přístup k čísel sociálního pojištění zákazníků (SSN), telefonních čísel a dalších identifikovatelné osobní údaje (PII).
- Zdravotní péče zprostředkovatele umožňuje pouze lékaři a sestry tak, aby měl přístup k citlivým lékařské záznamy při neumožňuje členové fakturační oddělení Chcete-li zobrazit tato data.
