---
title: Ověřování pro Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Zjistěte, jak provést ověření do služby Azure SQL Data Warehouse pomocí ověřování Azure Active Directory (AAD) nebo SQL Server.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d082ba8bd2819450609a8a6e4ab41b4320158d4b
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307838"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Ověřování pro Azure SQL Data Warehouse
Zjistěte, jak provést ověření do služby Azure SQL Data Warehouse pomocí ověřování Azure Active Directory (AAD) nebo SQL Server.

Pro připojení k SQL Data Warehouse, musíte předat v zabezpečovací přihlašovací údaje pro účely ověřování. Při navazování připojení, určitá nastavení připojení jsou nakonfigurované jako součást vytváření relaci dotazu.  

Další informace o zabezpečení a jak povolit připojení ke svému datovému skladu naleznete v tématu [zabezpečit databázi ve službě SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Ověřování pomocí SQL
Pro připojení k SQL Data Warehouse, je třeba zadat následující informace:

* Plně kvalifikovaný název_serveru
* Zadejte ověřování SQL
* Uživatelské jméno
* Heslo
* (Volitelné) i výchozí databáze

Ve výchozím nastavení se připojení připojí k *hlavní* databáze a nikoli uživatelské databázi. Pro připojení k uživatelské databázi, můžete to udělat jedním ze dvou kroků:

* Zadejte výchozí databáze při registraci serveru Průzkumníku objektů SQL serveru v sadě SSDT, aplikace SSMS, nebo v připojovacím řetězci vaší aplikace. Například přidejte parametr počáteční katalog pro připojení k rozhraní ODBC.
* Zvýrazněte uživatelskou databázi před vytvořením relace v SSDT.

> [!NOTE]
> Příkaz jazyka Transact-SQL **použití databáze;** není podporována pro změnu databáze pro připojení. Pokyny k připojení k SQL Data Warehouse s rozšířením SSDT, najdete [dotazování pomocí sady Visual Studio] [ Query with Visual Studio] článku.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Ověřování Azure Active Directory (AAD)
[Azure Active Directory] [ What is Azure Active Directory] ověřování je mechanismus pro připojení k Microsoft Azure SQL Data Warehouse s využitím identit v Azure Active Directory (Azure AD). Ověřování Azure Active Directory centrálně spravovat identity uživatelů databáze a další služby Microsoftu v jednom centrálním místě. Centrální správa ID zajišťuje centrální místo pro správu uživatelů SQL Data Warehouse a zjednodušuje správu oprávnění. 

### <a name="benefits"></a>Výhody
Azure Active Directory k výhodám patří:

* Poskytuje alternativu k ověřování serveru SQL Server.
* Pomáhá zastavit šíření identit uživatelů více databázových serverů.
* Umožňuje rotace hesla na jednom místě
* Spravujte oprávnění k databázi pomocí externí skupiny (AAD).
* Eliminuje ukládat tím, že integrované ověřování Windows a další formy ověřování podporovaných službou Azure Active Directory.
* Uživatelé databáze k ověření identity na úrovni databáze s omezením použití.
* Podporuje ověřování založené na tokenech pro aplikace, připojení k SQL Data Warehouse.
* Podporuje Vícefaktorové ověřování pomocí univerzálního ověřování Active Directory pro SQL Server Management Studio. Popis ověřování službou Multi-Factor Authentication, naleznete v tématu [podpora nástroje SSMS pro ověřování Azure AD MFA s SQL Database a SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Azure Active Directory je pořád relativně nové a má určitá omezení. Ujistěte se, že Azure Active Directory je vhodné pro vaše prostředí, najdete v článku [funkcí Azure AD a omezení][Azure AD features and limitations], konkrétně další aspekty.
> 
> 

### <a name="configuration-steps"></a>Postup konfigurace
Postupujte podle těchto kroků a nakonfigurujte ověřování Azure Active Directory.

1. Vytvoření a naplnění služby Azure Active Directory
2. Volitelné: Přiřadit nebo změnit služby active directory, která je teď přidružená k předplatnému Azure
3. Vytvoření správce Azure Active Directory pro službu Azure SQL Data Warehouse.
4. Konfigurace klientských počítačů
5. Vytvořte uživatele databáze s omezením v databázi namapované na identit Azure AD
6. Připojení k vašemu datovému skladu pomocí identit Azure AD

Uživatelé Azure Active Directory nejsou aktuálně zobrazený v Průzkumníku objektů rozšíření SSDT. Jako alternativní řešení, zobrazení uživatelů v [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Podrobnosti najdete
* Postup pro konfiguraci a ověřování pomocí Azure Active Directory jsou téměř shodné pro Azure SQL Database a Azure SQL Data Warehouse. Podrobné kroky v tématu [připojení k SQL Database nebo SQL Data Warehouse pomocí Active Directory ověřování služby Azure](../sql-database/sql-database-aad-authentication.md).
* Vytvořit vlastní databázové role a přidání uživatelů do rolí. Potom udělte různě odstupňovaná oprávnění k rolím. Další informace najdete v tématu [Začínáme s oprávněními modul databáze](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Další postup
Pokud chcete začít dotazovat na váš datový sklad pomocí sady Visual Studio a dalších aplikací, přečtěte si téma [dotazování pomocí sady Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
