---
title: Ověřování služby Active Directory – Azure Database for MySQL
description: Přečtěte si o konceptech Azure Active Directory pro ověřování pomocí Azure Database for MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: e3afa7e7129c34aae8487affe979bd7704580a1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612445"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Ověřování v MySQL s využitím Azure Active Directory

Ověřování pomocí Microsoft Azure Active Directory (Azure AD) je mechanismus připojení k Azure Database for MySQL pomocí identit definovaných ve službě Azure AD.
Pomocí ověřování Azure AD můžete spravovat identity uživatelů databáze a další služby Microsoftu v centrálním umístění, které zjednodušuje správu oprávnění.

Mezi výhody použití Azure AD patří:

- Jednotný způsob ověřování uživatelů napříč službami Azure
- Správa zásad hesel a rotace hesla na jednom místě
- Azure Active Directory podporuje několik forem ověřování, což může eliminovat nutnost ukládat hesla.
- Zákazníci můžou spravovat databázová oprávnění pomocí skupin externích (Azure AD).
- Ověřování Azure AD používá k ověřování identit na úrovni databáze uživatele databáze MySQL.
- Podpora ověřování založeného na tokenech pro aplikace, které se připojují k Azure Database for MySQL

Pro konfiguraci a použití ověřování Azure Active Directory použijte následující postup:

1. Podle potřeby vytvořte a naplňte Azure Active Directory pomocí identit uživatelů.
2. Volitelně můžete přidružit nebo změnit službu Active Directory, která je aktuálně přidružená k vašemu předplatnému Azure.
3. Vytvořte správce Azure AD pro server Azure Database for MySQL.
4. V databázi vytvořte uživatele databáze mapované na identity Azure AD.
5. Připojte se k databázi načtením tokenu pro identitu Azure AD a přihlášením.

> [!NOTE]
> Informace o tom, jak vytvořit a naplnit Azure AD a jak nakonfigurovat Azure AD pomocí Azure Database for MySQL, najdete v tématu [Konfigurace a přihlášení pomocí Azure AD pro Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="architecture"></a>Architektura

Následující diagram vysoké úrovně shrnuje, jak ověřování funguje pomocí ověřování Azure AD s Azure Database for MySQL. Šipky označují cesty komunikace.

![tok ověřování][1]

## <a name="administrator-structure"></a>Struktura správce

Při použití ověřování Azure AD jsou k dispozici dva účty správců pro server MySQL. původní správce MySQL a správce Azure AD. Pouze správce, který je založen na účtu Azure AD, může vytvořit prvního uživatele databáze s omezením Azure AD v uživatelské databázi. Přihlášení správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Když je správcem účet skupiny, může ho použít libovolný člen skupiny a povolit pro server MySQL více správců služby Azure AD. Použití účtu skupiny jako správce vylepšuje spravovatelnost tím, že umožňuje centrálně přidávat a odebírat členy skupin ve službě Azure AD beze změny uživatelů nebo oprávnění na serveru MySQL. Kdykoli se dá nakonfigurovat jenom jeden správce Azure AD (uživatel nebo skupina).

![struktura správy][2]

## <a name="permissions"></a>Oprávnění

Pokud chcete vytvořit nové uživatele, kteří se můžou ověřit pomocí Azure AD, musíte být navržený správce Azure AD. Tento uživatel je přiřazený konfigurací účtu správce Azure AD pro konkrétní server Azure Database for MySQL.

Pokud chcete vytvořit nového uživatele databáze Azure AD, musíte se připojit jako správce Azure AD. To je znázorněno v [konfiguraci a přihlášení pomocí Azure AD pro Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

Jakékoli ověřování Azure AD je možné pouze v případě, že byl vytvořen správce Azure AD pro Azure Database for MySQL. Pokud byl správce Azure Active Directory odebraný ze serveru, stávající uživatelé Azure Active Directory, kteří se dřív vytvořili, se už nebudou moct k databázi připojit pomocí svých Azure Active Directory přihlašovacích údajů.

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
> Další podrobnosti o tom, jak se připojit k tokenu služby Active Directory, najdete v tématu [Konfigurace a přihlášení pomocí Azure AD pro Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Další aspekty

- Ověřování Azure Active Directory je k dispozici pouze pro MySQL 5,7 a novější.
- Pro Azure Database for MySQL server můžete kdykoli nakonfigurovat pouze jednoho správce Azure AD.
- Pouze správce Azure AD pro MySQL se může zpočátku připojit k Azure Database for MySQL pomocí účtu Azure Active Directory. Správce služby Active Directory může nakonfigurovat další uživatele databáze služby Azure AD.
- Pokud se uživatel z Azure AD odstraní, už ho nebude moct ověřit ve službě Azure AD, a proto už nebude možné získat přístupový token pro tohoto uživatele. V takovém případě, i když bude odpovídající uživatel v databázi stále, nebude možné se připojit k serveru s tímto uživatelem.
> [!NOTE]
> Přihlášení k odstraněnému uživateli Azure AD se pořád může vykonat do vypršení platnosti tokenu (až 60 minut od vydání tokenu).  Pokud také odeberete uživatele z Azure Database for MySQL tento přístup se okamžitě odvolá.
- Pokud se správce Azure AD odebere ze serveru, server už nebude přidružený k tenantovi Azure AD, takže všechna přihlášení k Azure AD budou pro tento server zakázaná. Když se přidá nový správce Azure AD ze stejného tenanta, znovu se povolí přihlášení služby Azure AD.
- Azure Database for MySQL odpovídá přístupovým tokenům Azure Database for MySQLmu uživateli pomocí jedinečného ID uživatele Azure AD, a to na rozdíl od použití uživatelského jména. To znamená, že pokud se uživatel Azure AD odstraní v Azure AD a vytvoří se nový uživatel se stejným názvem, Azure Database for MySQL považuje za jiného uživatele. Proto pokud se uživatel z Azure AD odstraní a pak se nový uživatel s tímto názvem přidal, nový uživatel se nebude moct připojit k existujícímu uživateli.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak vytvořit a naplnit Azure AD a jak nakonfigurovat Azure AD pomocí Azure Database for MySQL, najdete v tématu [Konfigurace a přihlášení pomocí Azure AD pro Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).
- Přehled přihlášení a uživatele databáze pro Azure Database for MySQL najdete v tématu věnovaném [Vytvoření uživatelů v Azure Database for MySQL](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
