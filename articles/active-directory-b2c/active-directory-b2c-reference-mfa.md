---
title: Ověřování službou Multi-Factor Authentication v Azure Active Directory B2C | Dokumentace Microsoftu
description: Postup povolení služby Multi-Factor Authentication v zákaznické aplikace, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3d18e1b2e45aba4e83989e29c533cfc7bf5033fc
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442704"
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C: Povolení služby Multi-Factor Authentication v aplikacích určených
Azure Active Directory (Azure AD) B2C se integruje přímo do [ověřování Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) tak, aby druhou vrstvu zabezpečení můžete přidat do prostředí registrace a přihlášení ve svých aplikacích zaměřené na spotřebitele. A můžete to provést aniž byste museli napsat jediný řádek kódu. Momentálně podporujeme ověření telefonního hovoru a textové zprávy. Pokud jste už vytvořili zásady registrace a přihlášení, můžete stále povolit ověřování službou Multi-Factor Authentication.

> [!NOTE]
> Ověřování službou Multi-Factor Authentication je možné povolit také při vytváření zásad registrace a přihlašování, ne jenom úpravou existující zásady.
> 
> 

Tato funkce pomáhá aplikacím zpracovávat scénáře, jako je následující:

* Nemusíte vyžadovat Vícefaktorové ověřování pro přístup k jedné aplikaci, ale chcete, aby pro přístup k jiným. Například příjemci budou moct přihlašovat do aplikace automaticky pojištění s sociálních sítí nebo místní účet, ale před přístup k domovské pojištění aplikaci registrované ve stejném adresáři, musíte ověřit telefonní číslo.
* Nemusíte vyžadovat Vícefaktorové ověřování pro přístup k aplikaci obecně platí, ale chcete, aby pro přístup k citlivým části v rámci něj. Například příjemce může přihlásit k aplikaci bankovnictví s sociálních sítí nebo místní účet a Kontrola zůstatku, ale před pokusem o převod při přenosu musí ověřit telefonní číslo.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Upravit svojí registrační zásadě povolit Vícefaktorové ověřování
1. [Přejděte do okna s funkcemi B2C na webu Azure portal pomocí těchto kroků](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klikněte na **Zásady registrace**.
3. Klikněte na svojí registrační zásadě (například "B2C_1_SiUp") a otevřete ho.
4. Klikněte na tlačítko **ověřování službou Multi-Factor Authentication** a zapnout **stavu** k **ON**. Klikněte na **OK**.
5. Klikněte na tlačítko **Uložit** v horní části okna.

Funkce "Spustit nyní" na zásadu, kterou můžete použít k ověření uživatelské prostředí. Zkontrolujte:

Uživatelský účet se vytvoří ve vašem adresáři, než dojde k krok ověřování službou Multi-Factor Authentication. Během kroku se příjemce zobrazí výzva, zadejte své telefonní číslo a ověřte ji. Pokud je ověření úspěšné, telefonní číslo je připojen k uživatelský účet pro pozdější použití. I v případě, že uživatel zruší nebo potlačení, uživatel může být zobrazí výzva k potvrzení telefonního čísla znovu při dalším přihlášení (s zapnuté Vícefaktorové ověřování).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Upravit zásady přihlášení k povolení služby Multi-Factor Authentication
1. [Přejděte do okna s funkcemi B2C na webu Azure portal pomocí těchto kroků](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klikněte na tlačítko **zásady přihlašování**.
3. Klikněte na tlačítko Přihlásit zásady (například "B2C_1_SiIn") a otevřete ho. Klikněte na tlačítko **upravit** v horní části okna.
4. Klikněte na tlačítko **ověřování službou Multi-Factor Authentication** a zapnout **stavu** k **ON**. Klikněte na **OK**.
5. Klikněte na tlačítko **Uložit** v horní části okna.

Funkce "Spustit nyní" na zásadu, kterou můžete použít k ověření uživatelské prostředí. Zkontrolujte:

Když se přihlásí uživatel (pomocí sociálních sítí nebo místní účet), pokud ověřené telefonní číslo je připojena k uživatelský účet, uživatel je vyzván k ověří, že. Pokud je připojen bez telefonního čísla, příjemce se zobrazí výzva, zadejte jeden a ověří, že. Telefonní číslo připojen na úspěšné ověřování, uživatelský účet pro pozdější použití.

## <a name="multi-factor-authentication-on-other-policies"></a>Ověřování službou Multi-Factor Authentication na další zásady
Jak je popsáno pro registraci a přihlašování zásady, výše, je také možné povolit vícefaktorové ověřování při registraci nebo zásady přihlášení a hesla resetovat zásady. Bude možné brzy na zásady upravování profilu.

