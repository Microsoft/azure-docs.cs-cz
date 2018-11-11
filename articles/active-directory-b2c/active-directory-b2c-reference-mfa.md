---
title: Ověřování službou Multi-Factor Authentication v Azure Active Directory B2C | Dokumentace Microsoftu
description: Postup povolení služby Multi-Factor Authentication v zákaznické aplikace, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: eabae0f3575719c6cb93affefe0a393dd13d1439
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014002"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Povolení služby Multi-Factor authentication v Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C se integruje přímo do [ověřování Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) tak, že přidáte druhou vrstvu zabezpečení do prostředí registrace a přihlášení ve svých aplikacích. Povolení služby Multi-Factor authentication aniž byste museli napsat jediný řádek kódu. Pokud jste už vytvořili sign up a zásady přihlašování, můžete stále povolit ověřování službou Multi-Factor Authentication.

Tato funkce pomáhá aplikacím zpracovávat scénáře, jako je následující:

- Nemusíte vyžadovat vícefaktorové ověřování pro přístup k jedné aplikaci, ale chcete, aby pro přístup k jiné. Například zákazník se můžete přihlásit automaticky pojištění aplikace sociálních sítí nebo místní účet, ale před přístup k domovské pojištění aplikaci registrované ve stejném adresáři, musíte ověřit telefonní číslo.
- Nemusíte vyžadovat vícefaktorové ověřování pro přístup k aplikaci obecně platí, ale chcete, aby pro přístup k citlivým části v rámci něj. Například zákazník se můžete přihlásit k aplikaci bankovnictví s sociální sítě nebo místní účet a kontrola účet zůstatek, ale před pokusem o převod při přenosu musí ověřit tak telefonní číslo.

## <a name="set-multi-factor-authentication"></a>Nastavení služby Multi-Factor authentication

Když vytvoříte zásadu, máte možnost povolit ověřování službou Multi-Factor Authentication.

![Nastavení služby Multi-Factor authentication](./media/active-directory-b2c-reference-mfa/add-policy.png)

Nastavte **stavu** k **na**.

Můžete použít **spustit nyní** na zásadu, kterou chcete ověřit prostředí. Zkontrolujte následující scénář:

Účet zákazníka se vytvoří ve vašem tenantovi, než dojde k krok ověřování službou Multi-Factor Authentication. Během kroku zákazník se zobrazí výzva zadat telefonní číslo a ověřte ji. Pokud je ověření úspěšné, telefonní číslo je připojený k účtu pro pozdější použití. I v případě, že zákazník zruší nebo potlačení, Zákazník může být zobrazí výzva k potvrzení telefonní číslo znovu při dalším přihlášení s povoleným ověřováním službou Multi-Factor Authentication.

## <a name="add-multi-factor-authentication"></a>Přidat ověřování službou Multi-Factor Authentication

Je možné povolit vícefaktorové ověřování u zásady, které jste předtím vytvořili. 

Pokud chcete povolit ověřování službou Multi-Factor Authentication:

1. Otevřete zásadu a pak vyberte **upravit**. 
2. Vyberte **ověřování službou Multi-Factor Authentication**
3. Nastavte **stavu** k **na**.
4. Klikněte na **Uložit** v horní části stránky.


