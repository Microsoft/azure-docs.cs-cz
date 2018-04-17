---
title: Ověření do Azure SQL Data Warehouse | Microsoft Docs
description: Naučte se provádět ověřování na Azure SQL Data Warehouse pomocí ověřování Azure Active Directory (AAD) nebo SQL Server.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 2340168e736c13b5d911a987d36b0224d3c3b1ed
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Ověření do Azure SQL Data Warehouse
Naučte se provádět ověřování na Azure SQL Data Warehouse pomocí ověřování Azure Active Directory (AAD) nebo SQL Server.

Pro připojení k SQL Data Warehouse, musí projít v zabezpečovací přihlašovací údaje pro účely ověření. Při navazování připojení, jsou určitá nastavení připojení nakonfigurovaný jako součást navazování relace dotazu.  

Další informace o zabezpečení a povolení připojení k vašemu datovému skladu naleznete v tématu [zabezpečení databáze v SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Ověřování pomocí SQL
Pro připojení k SQL Data Warehouse, je nutné zadat následující informace:

* Plně kvalifikovaný servername
* Zadejte ověřování SQL.
* Uživatelské jméno
* Heslo
* Výchozí databáze (volitelné)

Ve výchozím nastavení se připojení připojí k *hlavní* databáze a nejsou své databázi uživatelů. Pokud chcete připojit ke své databázi uživatelů, můžete provést jednu ze dvou akcí:

* Zadejte výchozí databáze při registraci serveru pomocí Průzkumníka objektů SQL Server v rozšíření SSDT, aplikace SSMS, nebo v aplikaci připojovací řetězec. Příklady parametr vlastnosti InitialCatalog pro připojení k rozhraní ODBC.
* Před vytvořením relace v sadě SSDT zvýrazněte uživatele databáze.

> [!NOTE]
> Příkaz Transact-SQL **použití databáze;** není podporována pro změnu databáze pro připojení. Pokyny k připojení k SQL Data Warehouse s rozšířením SSDT, najdete v tématu [dotazu pomocí sady Visual Studio] [ Query with Visual Studio] článku.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Ověřování Azure Active Directory (AAD)
[Azure Active Directory] [ What is Azure Active Directory] ověřování je mechanismus připojit k Microsoft Azure SQL Data Warehouse pomocí identit v Azure Active Directory (Azure AD). Pomocí ověřování Azure Active Directory můžete centrálně spravovat identity uživatelů, databáze a další služby Microsoftu v jednom centrálním místě. Centrální správa ID poskytuje jednotné místo pro správu uživatelů SQL Data Warehouse a zjednodušuje správu oprávnění. 

### <a name="benefits"></a>Výhody
Azure Active Directory výhody patří:

* Poskytuje alternativu k ověřování serveru SQL Server.
* Pomáhá zastavit šíření identit uživatelů mezi servery databáze.
* Umožňuje otočení heslo na jednom místě
* Správa oprávnění databáze pomocí externí skupiny (AAD).
* Eliminuje ukládání hesel povolením integrované ověřování systému Windows a jiných forem ověřování podporovaných službou Azure Active Directory.
* Uživatelé databáze obsažené používá k ověření identity na úrovni databáze.
* Podporuje ověřování na základě tokenu pro připojení k SQL Data Warehouse aplikace.
* Podporuje Vícefaktorové ověřování prostřednictvím Universal ověřování služby Active Directory pro SQL Server Management Studio. Popis služby Multi-Factor Authentication najdete v tématu [SSMS podpora pro Azure AD MFA s SQL Database a SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Azure Active Directory je pořád relativně nové a má určitá omezení. Pro zajištění, že Azure Active Directory je vhodné pro vaše prostředí, naleznete v [funkce Azure AD a omezení][Azure AD features and limitations], konkrétně další aspekty.
> 
> 

### <a name="configuration-steps"></a>Kroky konfigurace
Postupujte podle těchto kroků nakonfigurujete ověřování Azure Active Directory.

1. Vytvořit a naplnit Azure Active Directory
2. Volitelné: Přidružení nebo změňte služby active directory, který je aktuálně přidružena předplatného Azure
3. Vytvořte správce Azure Active Directory pro Azure SQL Data Warehouse.
4. Konfigurace klientských počítačů
5. Vytvoření databáze s omezením uživatelů ve vaší databázi namapované na identit Azure AD
6. Připojení k vašemu datovému skladu pomocí identit Azure AD

Aktuálně nejsou Azure Active Directory uživatelům zobrazí v Průzkumníku objektů rozšíření SSDT. Jako alternativní řešení, zobrazit uživateli v [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Najít podrobnosti
* Postup pro konfiguraci a použití ověřování Azure Active Directory jsou téměř stejné pro databázi SQL Azure a Azure SQL Data Warehouse. Podrobné pokyny v tématu [připojení k SQL Database nebo SQL Data Warehouse pomocí pomocí Azure ověřování služby Active Directory](../sql-database/sql-database-aad-authentication.md).
* Vytvořte vlastní databázové role a přidání uživatelů do rolí. Potom udělte oprávnění na podrobné úrovni do rolí. Další informace najdete v tématu [Začínáme s oprávněními modul databáze](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Další postup
Pokud chcete spustit dotaz na vaše data warehouse pomocí sady Visual Studio a dalších aplikací, najdete v části [dotazu pomocí sady Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
