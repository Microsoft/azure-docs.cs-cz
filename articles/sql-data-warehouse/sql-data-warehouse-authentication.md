---
title: Ověřování
description: Naučte se ověřovat Azure SQL Data Warehouse pomocí Azure Active Directory (AAD) nebo SQL Server ověřování.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fda29e432fbd952261893f3c32a4df7b9990ae66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692930"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Ověřování pro Azure SQL Data Warehouse
Naučte se ověřovat Azure SQL Data Warehouse pomocí Azure Active Directory (AAD) nebo SQL Server ověřování.

Pokud se chcete připojit k SQL Data Warehouse, musíte předat přihlašovací údaje zabezpečení pro účely ověřování. Po navázání připojení jsou určitá nastavení připojení nakonfigurovaná v rámci vytváření relace dotazů.  

Další informace o zabezpečení a o tom, jak povolit připojení k datovému skladu, najdete v tématu [zabezpečení databáze v SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Ověřování pomocí SQL
Chcete-li se připojit k SQL Data Warehouse, je nutné zadat následující informace:

* Plně kvalifikovaný Server servername
* Zadat ověřování SQL
* Uživatelské jméno
* Heslo
* Výchozí databáze (volitelné)

Ve výchozím nastavení se připojení připojuje k *Hlavní* databázi, a ne k uživatelské databázi. Pokud se chcete připojit k uživatelské databázi, můžete se rozhodnout, že provedete jednu z následujících akcí:

* Určete výchozí databázi při registraci serveru s Průzkumník objektů systému SQL Server v SSDT, SSMS nebo v připojovacím řetězci aplikace. Například zahrňte parametr vlastnost InitialCatalog pro připojení rozhraní ODBC.
* Než vytvoříte relaci v SSDT, zvýrazněte uživatelskou databázi.

> [!NOTE]
> Příkaz Transact-SQL **Use MyDatabase;** není podporován pro změnu databáze pro připojení. Pokyny k připojení k SQL Data Warehouse pomocí SSDT najdete v článku [dotazování pomocí sady Visual Studio][Query with Visual Studio] .
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Ověřování Azure Active Directory (AAD)
Ověřování [Azure Active Directory][What is Azure Active Directory] je mechanismus připojení k Microsoft Azure SQL Data Warehouse pomocí identit v Azure Active Directory (Azure AD). S ověřováním Azure Active Directory můžete centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění. Správa centrálních ID poskytuje jediné místo pro správu SQL Data Warehouse uživatelů a zjednodušuje správu oprávnění. 

### <a name="benefits"></a>Výhody
Mezi výhody Azure Active Directory patří:

* Představuje alternativu k ověřování SQL Server.
* Pomáhá zastavit šíření identit uživatelů napříč databázovými servery.
* Umožňuje rotaci hesel na jednom místě.
* Spravujte databázová oprávnění pomocí skupin externích (AAD).
* Eliminuje ukládání hesel povolením integrovaného ověřování systému Windows a dalších forem ověřování, které podporuje Azure Active Directory.
* Používá uživatele databáze s omezením k ověřování identit na úrovni databáze.
* Podporuje ověřování založené na tokenech pro aplikace, které se připojují k SQL Data Warehouse.
* Podporuje službu Multi-Factor Authentication prostřednictvím univerzálního ověřování služby Active Directory pro různé nástroje, včetně [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) a [SQL Server datových nástrojů](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> Azure Active Directory je stále poměrně nové a má určitá omezení. Aby bylo zajištěno, že pro vaše prostředí je Azure Active Directory vhodné, přečtěte si téma [funkce a omezení služby Azure AD][Azure AD features and limitations], konkrétně Další informace.
> 
> 

### <a name="configuration-steps"></a>Postup konfigurace
Pomocí těchto kroků můžete nakonfigurovat ověřování Azure Active Directory.

1. Vytvoření a naplnění Azure Active Directory
2. Volitelné: přidružte nebo změňte službu Active Directory, která je aktuálně přidružená k vašemu předplatnému Azure.
3. Vytvořte správce Azure Active Directory pro Azure SQL Data Warehouse.
4. Konfigurace klientských počítačů
5. Umožňuje vytvořit uživatele databáze s omezením v databázi namapované na identity Azure AD.
6. Připojení k datovému skladu pomocí identit Azure AD

Aktuálně Azure Active Directory uživatelé nejsou zobrazeni v Průzkumník objektů SSDT. Alternativním řešením je zobrazit uživatele v zobrazení [Sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Najít podrobnosti
* Kroky pro konfiguraci a použití ověřování Azure Active Directory jsou skoro totožné pro Azure SQL Database a Azure SQL Data Warehouse. Postupujte podle podrobných pokynů v tématu [připojení k SQL Database nebo SQL Data Warehouse pomocí Azure Active Directory ověřování](../sql-database/sql-database-aad-authentication.md).
* Vytvořte vlastní databázové role a přidejte uživatele k rolím. Pak udělte rolím přesnější oprávnění. Další informace najdete v tématu [Začínáme s oprávněním databázového stroje](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Další kroky
Pokud se chcete začít dotazovat na svůj datový sklad pomocí sady Visual Studio a jiných aplikací, najdete informace v článku [Dotazování pomocí sady Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
