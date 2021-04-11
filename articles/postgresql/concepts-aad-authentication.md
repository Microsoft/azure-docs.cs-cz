---
title: Ověřování služby Active Directory – Azure Database for PostgreSQL – jeden server
description: Přečtěte si o konceptech Azure Active Directory pro ověřování pomocí Azure Database for PostgreSQL-Single server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: ff588a2fdc079c4b5a1cd50ddc4441a079d3feed
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960916"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Ověřování v PostgreSQL s využitím Azure Active Directory

Ověřování pomocí Microsoft Azure Active Directory (Azure AD) je mechanismus připojení k Azure Database for PostgreSQL pomocí identit definovaných ve službě Azure AD.
Pomocí ověřování Azure AD můžete spravovat identity uživatelů databáze a další služby Microsoftu v centrálním umístění, které zjednodušuje správu oprávnění.

Mezi výhody použití Azure AD patří:

- Jednotný způsob ověřování uživatelů napříč službami Azure
- Správa zásad hesel a rotace hesla na jednom místě
- Azure Active Directory podporuje několik forem ověřování, což může eliminovat nutnost ukládat hesla.
- Zákazníci můžou spravovat databázová oprávnění pomocí skupin externích (Azure AD).
- Ověřování Azure AD používá role PostgreSQL Database k ověřování identit na úrovni databáze.
- Podpora ověřování založeného na tokenech pro aplikace, které se připojují k Azure Database for PostgreSQL

Pro konfiguraci a použití ověřování Azure Active Directory použijte následující postup:

1. Podle potřeby vytvořte a naplňte Azure Active Directory pomocí identit uživatelů.
2. Volitelně můžete přidružit nebo změnit službu Active Directory, která je aktuálně přidružená k vašemu předplatnému Azure.
3. Vytvořte správce Azure AD pro server Azure Database for PostgreSQL.
4. V databázi vytvořte uživatele databáze mapované na identity Azure AD.
5. Připojte se k databázi načtením tokenu pro identitu Azure AD a přihlášením.

> [!NOTE]
> Informace o tom, jak vytvořit a naplnit Azure AD a jak nakonfigurovat Azure AD pomocí Azure Database for PostgreSQL, najdete v tématu [Konfigurace a přihlášení pomocí Azure AD pro Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="architecture"></a>Architektura

Následující diagram vysoké úrovně shrnuje, jak ověřování funguje pomocí ověřování Azure AD s Azure Database for PostgreSQL. Šipky označují cesty komunikace.

![tok ověřování][1]

## <a name="administrator-structure"></a>Struktura správce

Při použití ověřování Azure AD jsou k dispozici dva účty správců pro server PostgreSQL. původní správce PostgreSQL a správce Azure AD. Pouze správce, který je založen na účtu Azure AD, může vytvořit prvního uživatele databáze s omezením Azure AD v uživatelské databázi. Přihlášení správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Když je správcem účet skupiny, dá se použít kterýkoli člen skupiny a tím umožňuje více správců služby Azure AD pro server PostgreSQL. Použití účtu skupiny jako správce vylepšuje spravovatelnost tím, že umožňuje centrálně přidávat a odebírat členy skupin ve službě Azure AD beze změny uživatelů nebo oprávnění na serveru PostgreSQL. Kdykoli se dá nakonfigurovat jenom jeden správce Azure AD (uživatel nebo skupina).

![struktura správy][2]

## <a name="permissions"></a>Oprávnění

Pokud chcete vytvořit nové uživatele, kteří se můžou ověřit ve službě Azure AD, musíte mít `azure_ad_admin` roli v databázi. Tato role je přiřazená konfigurací účtu správce Azure AD pro konkrétní server Azure Database for PostgreSQL.

Pokud chcete vytvořit nového uživatele databáze Azure AD, musíte se připojit jako správce Azure AD. To je znázorněno v [konfiguraci a přihlášení pomocí Azure AD pro Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

Jakékoli ověřování Azure AD je možné pouze v případě, že byl vytvořen správce Azure AD pro Azure Database for PostgreSQL. Pokud byl správce Azure Active Directory odebraný ze serveru, stávající uživatelé Azure Active Directory, kteří se dřív vytvořili, se už nebudou moct k databázi připojit pomocí svých Azure Active Directory přihlašovacích údajů.

## <a name="connecting-using-azure-ad-identities"></a>Připojení pomocí identit Azure AD

Ověřování Azure Active Directory podporuje následující metody připojení k databázi pomocí identit Azure AD:

- Azure Active Directory heslo
- Azure Active Directory integrovaný
- Azure Active Directory univerzální s MFA
- Používání certifikátů aplikací služby Active Directory nebo klientských tajných klíčů
- [Spravovaná identita](howto-connect-with-managed-identity.md)

Po ověření vůči službě Active Directory načtěte token. Tento token je vaším heslem pro přihlášení.

Mějte na paměti, že operace správy, jako je přidání nových uživatelů, jsou v tuto chvíli podporované jenom pro role uživatelů Azure AD.

> [!NOTE]
> Další podrobnosti o tom, jak se připojit k tokenu služby Active Directory, najdete v tématu [Konfigurace a přihlášení pomocí Azure AD pro Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Další aspekty

- Pro zlepšení spravovatelnosti doporučujeme zřídit vyhrazenou skupinu Azure AD jako správce.
- Pro Azure Database for PostgreSQL Server můžete kdykoli nakonfigurovat pouze jednoho správce Azure AD (uživatele nebo skupinu).
- Pouze správce Azure AD pro PostgreSQL se může zpočátku připojit k Azure Database for PostgreSQL pomocí účtu Azure Active Directory. Správce služby Active Directory může nakonfigurovat další uživatele databáze služby Azure AD.
- Pokud se uživatel z Azure AD odstraní, už ho nebude moct ověřit ve službě Azure AD, a proto už nebude možné získat přístupový token pro tohoto uživatele. V takovém případě, i když bude odpovídající role stále v databázi, se nebude možné připojit k serveru s touto rolí.
> [!NOTE]
> Přihlášení k odstraněnému uživateli Azure AD se pořád může vykonat do vypršení platnosti tokenu (až 60 minut od vydání tokenu).  Pokud také odeberete uživatele z Azure Database for PostgreSQL tento přístup se okamžitě odvolá.
- Pokud se správce Azure AD odebere ze serveru, server už nebude přidružený k tenantovi Azure AD, takže všechna přihlášení k Azure AD budou pro tento server zakázaná. Když se přidá nový správce Azure AD ze stejného tenanta, znovu se povolí přihlášení služby Azure AD.
- Azure Database for PostgreSQL odpovídá přístupovým tokenům k roli Azure Database for PostgreSQL pomocí jedinečného ID uživatele Azure AD, a to na rozdíl od použití uživatelského jména. To znamená, že pokud se uživatel Azure AD odstraní v Azure AD a vytvoří se nový uživatel se stejným názvem, Azure Database for PostgreSQL považuje za jiného uživatele. Proto pokud se uživatel z Azure AD odstraní a pak se nový uživatel s tímto názvem přidal, nový uživatel se nebude moct připojit k existující roli. Aby to bylo možné, Azure Database for PostgreSQL správce Azure AD musí odvolat a potom udělit roli "azure_ad_user" uživateli aktualizovat ID uživatele Azure AD.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak vytvořit a naplnit Azure AD a jak nakonfigurovat Azure AD pomocí Azure Database for PostgreSQL, najdete v tématu [Konfigurace a přihlášení pomocí Azure AD pro Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).
- Přehled přihlašovacích údajů, uživatelů a databázových rolí Azure Database for PostgreSQL najdete v tématu věnovaném [Vytvoření uživatelů v Azure Database for PostgreSQLm jednom serveru](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
