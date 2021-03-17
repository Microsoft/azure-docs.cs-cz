---
title: Ověřování pro vyhrazený fond SQL (dříve SQL DW)
description: Přečtěte si, jak pomocí Azure Active Directory (Azure AD) nebo ověřování SQL Server ověřit ve službě Azure synapse Analytics přístup k vyhrazenému fondu SQL (dříve SQL DW).
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 80bc9f6fc6af94ba2a5ade77cc1d53b3fc29f1ea
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685340"
---
# <a name="authenticate-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Ověření ve vyhrazeném fondu SQL (dřív SQL DW) ve službě Azure synapse Analytics

Přečtěte si, jak se pomocí Azure Active Directory (Azure AD) nebo ověřování SQL Server ověřit ve vyhrazeném fondu SQL (dřív SQL DW) ve službě Azure synapse.

Pokud se chcete připojit k vyhrazenému fondu SQL (dřív SQL DW), musíte přihlašovací údaje zabezpečení předat pro účely ověřování. Po navázání připojení jsou určitá nastavení připojení nakonfigurovaná v rámci vytváření relace dotazů.  

Další informace o zabezpečení a o tom, jak povolit připojení k vyhrazenému fondu SQL (dřív SQL DW), najdete v tématu [zabezpečení dokumentace k databázi](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Ověřování SQL

Pokud se chcete připojit k vyhrazenému fondu SQL (dřív SQL DW), musíte zadat následující informace:

* Plně kvalifikovaný Server servername
* Zadat ověřování SQL
* Uživatelské jméno
* Heslo
* Výchozí databáze (volitelné)

Ve výchozím nastavení se připojení připojuje k *Hlavní* databázi, a ne k uživatelské databázi. Pokud se chcete připojit k uživatelské databázi, můžete se rozhodnout, že provedete jednu z následujících akcí:

* Určete výchozí databázi při registraci serveru s Průzkumník objektů systému SQL Server v SSDT, SSMS nebo v připojovacím řetězci aplikace. Například zahrňte parametr vlastnost InitialCatalog pro připojení rozhraní ODBC.
* Než vytvoříte relaci v SSDT, zvýrazněte uživatelskou databázi.

> [!NOTE]
> Příkaz Transact-SQL **Use MyDatabase;** není podporován pro změnu databáze pro připojení. Pokyny k připojení ke fondu SQL pomocí SSDT najdete v článku [dotazování pomocí sady Visual Studio](sql-data-warehouse-query-visual-studio.md) .

## <a name="azure-active-directory-authentication"></a>Ověřování služby Azure Active Directory

Ověřování [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) je mechanismus připojení ke fondu SQL pomocí identit v Azure Active Directory (Azure AD). S ověřováním Azure Active Directory můžete centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění. Správa centrálních ID poskytuje jedno místo pro správu vyhrazených uživatelů fondu SQL (dříve SQL DW) a zjednodušuje správu oprávnění.

### <a name="benefits"></a>Výhody

Mezi výhody Azure Active Directory patří:

* Představuje alternativu k ověřování SQL Server.
* Pomáhá zastavit šíření identit uživatelů napříč servery.
* Umožňuje rotaci hesel na jednom místě.
* Spravujte databázová oprávnění pomocí skupin externích (Azure AD).
* Eliminuje ukládání hesel povolením integrovaného ověřování systému Windows a dalších forem ověřování, které podporuje Azure Active Directory.
* Používá uživatele databáze s omezením k ověřování identit na úrovni databáze.
* Podporuje ověřování založené na tokenech pro aplikace, které se připojují ke fondu SQL.
* Podporuje službu Multi-Factor Authentication prostřednictvím univerzálního ověřování služby Active Directory pro různé nástroje, včetně [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [SQL Server datových nástrojů](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

> [!NOTE]
> Azure Active Directory je stále poměrně nové a má určitá omezení. Aby bylo zajištěno, že pro vaše prostředí je Azure Active Directory vhodné, přečtěte si téma [funkce a omezení služby Azure AD](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), konkrétně Další informace.

### <a name="configuration-steps"></a>Postup konfigurace

Pomocí těchto kroků můžete nakonfigurovat ověřování Azure Active Directory.

1. Vytvoření a naplnění Azure Active Directory
2. Volitelné: přidružte nebo změňte službu Active Directory, která je aktuálně přidružená k vašemu předplatnému Azure.
3. Vytvoření správce Azure Active Directory pro Azure synapse
4. Konfigurace klientských počítačů
5. Umožňuje vytvořit uživatele databáze s omezením v databázi namapované na identity Azure AD.
6. Připojení ke svému fondu SQL pomocí identit Azure AD

Aktuálně Azure Active Directory uživatelé nejsou zobrazeni v Průzkumník objektů SSDT. Alternativním řešením je zobrazit uživatele v [Sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="find-the-details"></a>Najít podrobnosti

* Postup pro konfiguraci a použití ověřování Azure Active Directory je skoro stejný pro Azure SQL Database a synapse SQL v Azure synapse. Postupujte podle podrobných pokynů v tématu [připojení k SQL Database nebo fondu SQL pomocí ověřování Azure Active Directory](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Vytvořte vlastní databázové role a přidejte uživatele k rolím. Pak udělte rolím přesnější oprávnění. Další informace najdete v tématu [Začínáme s oprávněním databázového stroje](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Další kroky

Chcete-li začít dotazovat se sadou Visual Studio a dalšími aplikacemi, přečtěte si téma [dotazování pomocí sady Visual Studio](sql-data-warehouse-query-visual-studio.md).
