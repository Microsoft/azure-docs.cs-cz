---
title: Ověřování ve službě Active Directory – databáze Azure pro MySQL
description: Informace o konceptech Služby Azure Active Directory pro ověřování pomocí Azure Database for MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299018"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Použití služby Azure Active Directory k ověřování pomocí mySQL

Ověřování služby Microsoft Azure Active Directory (Azure AD) je mechanismus připojení k Azure Database for MySQL pomocí identit definovaných ve službě Azure AD.
Pomocí ověřování Azure AD můžete spravovat identity uživatelů databáze a další služby Microsoftu v centrálním umístění, což zjednodušuje správu oprávnění.

> [!IMPORTANT]
> Azure AD ověřování pro Azure Database for MySQL je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mezi výhody používání azure ad patří:

- Ověřování uživatelů napříč službami Azure jednotným způsobem
- Správa zásad hesel a střídání hesel na jednom místě
- Více forem ověřování podporovaných službou Azure Active Directory, které můžou eliminovat potřebu ukládat hesla
- Zákazníci mohou spravovat oprávnění databáze pomocí externích skupin (Azure AD).
- Ověřování Azure AD používá uživatele databáze MySQL k ověřování identit na úrovni databáze
- Podpora ověřování na základě tokenů pro aplikace připojující se k Azure Database pro MySQL

Chcete-li konfigurovat a používat ověřování služby Azure Active Directory, použijte následující postup:

1. Podle potřeby vytvořte a naplňte službu Azure Active Directory identitami uživatelů.
2. Volitelně přidružit nebo změnit službu Active Directory aktuálně přidruženou k vašemu předplatnému Azure.
3. Vytvořte správce Azure AD pro Azure Database for MySQL server.
4. Vytvořte uživatele databáze v databázi mapované na identity Azure AD.
5. Připojte se k databázi načtením tokenu pro identitu Azure AD a přihlášením.

> [!NOTE]
> Informace o tom, jak vytvořit a naplnit Azure AD a pak nakonfigurovat Azure AD s Azure Database for MySQL, najdete [v tématu Konfigurace a přihlášení pomocí Azure AD pro Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="architecture"></a>Architektura

Následující diagram vysoké úrovně shrnuje, jak funguje ověřování pomocí ověřování Azure AD s Azure Database for MySQL. Šipky ukazují komunikační cesty.

![tok ověřování][1]

## <a name="administrator-structure"></a>Struktura správce

Při použití ověřování Azure AD existují dva účty správce pro server MySQL; původního správce MySQL a správce služby Azure AD. Pouze správce na základě účtu Azure AD můžete vytvořit první Azure AD obsahoval databázi uživatele v databázi uživatele. Přihlášení správce Azure AD může být uživatel Azure AD nebo skupina Azure AD. Pokud je správce účet skupiny, může jej použít libovolný člen skupiny, což umožňuje více správců azure ad pro server MySQL. Použití účtu skupiny jako správce zvyšuje možnosti správy tím, že umožňuje centrálně přidávat a odebírat členy skupiny ve službě Azure AD bez evidenčnízměny uživatelů nebo oprávnění na serveru MySQL. Kdykoli lze nakonfigurovat pouze jednoho správce Azure AD (uživatele nebo skupinu).

![struktura správce][2]

## <a name="permissions"></a>Oprávnění

Chcete-li vytvořit nové uživatele, které lze ověřit pomocí služby Azure AD, musíte být navržený správce Azure AD. Tento uživatel se přiřazuje konfigurací účtu správce Azure AD pro konkrétní databázi Azure pro server MySQL.

Chcete-li vytvořit nového uživatele databáze Azure AD, musíte se připojit jako správce Azure AD. To je znázorněno v [konfigurace a přihlášení s Azure AD pro Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

Jakékoli ověřování Azure AD je možné jenom v případě, že správce Azure AD byl vytvořen pro Azure Database for MySQL. Pokud byl správce služby Azure Active Directory odebrán ze serveru, stávající uživatelé služby Azure Active Directory, kteří byli dříve vytvořeni, se již nemohou připojit k databázi pomocí svých přihlašovacích údajů služby Azure Active Directory.

## <a name="connecting-using-azure-ad-identities"></a>Připojení pomocí identit Azure AD

Ověřování Azure Active Directory podporuje následující metody připojení k databázi pomocí identit Azure AD:

- Heslo služby Azure Active Directory
- Integrovaná služba Azure Active Directory
- Univerzální služba Azure Active Directory s vícefaktorovou pomocí
- Použití certifikátů aplikací služby Active Directory nebo tajných kódů klientů

Po ověření ve službě Active Directory načtete token. Tento token je vaše heslo pro přihlášení.

> [!NOTE]
> Další podrobnosti o tom, jak se připojit k tokenu služby Active Directory, najdete [v tématu Konfigurace a přihlášení pomocí Azure AD pro Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Další aspekty

- Pro azure databázi pro server MySQL lze kdykoli nakonfigurovat jenom jednoho správce Azure AD.
- Pouze správce Azure AD pro MySQL můžete zpočátku připojit k databázi Azure pro MySQL pomocí účtu Azure Active Directory. Správce služby Active Directory může nakonfigurovat následné uživatele databáze Azure AD.
- Pokud je uživatel odstraněn z Azure AD, tento uživatel již nebude moci ověřit pomocí služby Azure AD, a proto již nebude možné získat přístupový token pro tohoto uživatele. V tomto případě, i když odpovídající uživatel bude stále v databázi, nebude možné se připojit k serveru s tímto uživatelem.
> [!NOTE]
> Přihlášení s odstraněným uživatelem Azure AD stále provést, dokud vyprší platnost tokenu (až 60 minut od vydání tokenu).  Pokud také odeberete uživatele z databáze Azure pro MySQL, bude tento přístup okamžitě odvolán.
- Pokud správce Azure AD je odebrána ze serveru, server už nebude přidružen k tenantovi Azure AD, a proto všechna přihlášení Azure AD se deaktivují pro server. Přidání nového správce Azure AD ze stejného klienta znovu povolí přihlášení Azure AD.
- Azure Database for MySQL odpovídá přístupové tokeny do databáze Azure pro uživatele MySQL pomocí jedinečné ho uživatele Azure AD uživatelské ho DIČ, na rozdíl od použití uživatelského jména. To znamená, že pokud se uživatel Azure AD odstraní ve službě Azure AD a nový uživatel vytvořený se stejným názvem, Azure Database for MySQL se domnívá, že jiný uživatel. Proto pokud je uživatel odstraněn z Azure AD a potom nový uživatel se stejným názvem přidán, nový uživatel nebude moci připojit s existujícím uživatelem.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak vytvořit a naplnit Azure AD a pak nakonfigurovat Azure AD s Azure Database for MySQL, najdete [v tématu Konfigurace a přihlášení pomocí Azure AD pro Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).
- Přehled přihlášení a uživatelů databáze pro Azure Database for MySQL najdete v tématu [Vytváření uživatelů v Azure Database for MySQL](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
