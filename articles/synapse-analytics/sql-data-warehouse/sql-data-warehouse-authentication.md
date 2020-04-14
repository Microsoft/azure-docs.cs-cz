---
title: Authentication
description: Zjistěte, jak se ověřovat na Azure Synapse Analytics pomocí Azure Active Directory (Azure AD) nebo SQL Server ověřování.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: ed8dd902ac490b4e6a0f172029bf3ffa1d44acee
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251839"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Ověření azure synapse analytics

Zjistěte, jak ověřit sql analytics v Azure Synapse pomocí Azure Active Directory (AAD) nebo SQL Server ověřování.

Chcete-li se připojit k fondu SQL, musíte předat pověření zabezpečení pro účely ověřování. Po navázání připojení jsou určitá nastavení připojení nakonfigurována jako součást vytvoření relace dotazu.  

Další informace o zabezpečení a povolení připojení k datovému skladu naleznete [v tématu zabezpečení dokumentace k databázi](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Ověřování pomocí SQL

Chcete-li se připojit k fondu SQL, musíte zadat následující informace:

* Plně kvalifikovaný název serveru
* Určení ověřování SQL
* Uživatelské jméno
* Heslo
* Výchozí databáze (volitelně)

Ve výchozím nastavení se připojení připojuje k *hlavní* databázi a nikoli k databázi uživatelů. Chcete-li se připojit k databázi uživatelů, můžete provést jednu ze dvou věcí:

* Při registraci serveru pomocí Průzkumníka objektů serveru SQL Server v aplikaci SSDT, SSMS nebo v připojovacím řetězci aplikace zadejte výchozí databázi. Zahrňte například parametr InitialCatalog pro připojení ODBC.
* Před vytvořením relace v ssdt zvýrazněte databázi uživatelů.

> [!NOTE]
> Příkaz Transact-SQL **USE MyDatabase;** není podporován pro změnu databáze pro připojení. Pokyny pro připojení k fondu SQL s SSDT naleznete v článku [Dotaz s Visual Studio.](sql-data-warehouse-query-visual-studio.md)

## <a name="azure-active-directory-aad-authentication"></a>Ověřování služby Azure Active Directory (AAD)

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ověřování je mechanismus připojení k fondu SQL pomocí identit ve službě Azure Active Directory (Azure AD). Pomocí ověřování Azure Active Directory můžete centrálně spravovat identity uživatelů databází a dalších služeb Microsoftu v jednom centrálním umístění. Centrální správa ID poskytuje jedno místo pro správu uživatelů Azure Synapse a zjednodušuje správu oprávnění.

### <a name="benefits"></a>Výhody

Mezi výhody služby Azure Active Directory patří:

* Poskytuje alternativu k ověřování serveru SQL Server.
* Pomáhá zastavit šíření identit uživatelů napříč databázovými servery.
* Umožňuje rotaci hesel na jednom místě.
* Spravujte oprávnění databáze pomocí externích skupin (Azure AD).
* Eliminuje ukládání hesel povolením integrovaného ověřování systému Windows a dalších forem ověřování podporovaných službou Azure Active Directory.
* Používá obsažené uživatele databáze k ověřování identit na úrovni databáze.
* Podporuje ověřování na základě tokenů pro aplikace, které se připojují k fondu SQL.
* Podporuje vícefaktorové ověřování prostřednictvím univerzálního ověřování služby Active Directory pro různé nástroje, včetně [sql server management studio](../../sql-database/sql-database-ssms-mfa-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a SQL Server Data [Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> Azure Active Directory je stále relativně nová a má určitá omezení. Chcete-li zajistit, aby služba Azure Active Directory vyhovovala vašemu prostředí, přečtěte si informace [o funkcích a omezeních služby Azure AD](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), konkrétně v dalších aspektech.

### <a name="configuration-steps"></a>Postup konfigurace

Chcete-li nakonfigurovat ověřování služby Azure Active Directory, postupujte takto.

1. Vytvoření a naplnění služby Azure Active Directory
2. Volitelné: Přidružení nebo změna aktivního adresáře, který je aktuálně přidružený k vašemu předplatnému Azure.
3. Vytvoření správce služby Azure Active Directory pro Azure Synapse
4. Konfigurace klientských počítačů
5. Vytvoření obsahujících uživatelů databáze v databázi mapovaných na identity Azure AD
6. Připojení k fondu SQL pomocí identit Azure AD

V současné době se uživatelé služby Azure Active Directory nezobrazují v Průzkumníku objektů SSDT. Jako zástupné řešení zobrazíte uživatele v [souboru sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="find-the-details"></a>Najít podrobnosti

* Kroky konfigurace a použití ověřování Azure Active Directory jsou téměř identické pro Azure SQL Database a SQL Analytics v Azure Synapse. Postupujte podle podrobných kroků v tématu [Připojení k databázi SQL nebo fondu SQL pomocí ověřování Azure Active Directory](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Vytvořte vlastní databázové role a přidejte uživatele do rolí. Potom udělte role podrobné oprávnění. Další informace naleznete v [tématu Začínáme s oprávněními databázového stroje](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s dotazováním pomocí Sady Visual Studio a dalších aplikací, přečtěte [si informace o dotazu pomocí sady Visual Studio](sql-data-warehouse-query-visual-studio.md).
