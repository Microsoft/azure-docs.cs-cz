---
title: 'Služby Azure Active Directory B2C: Vícefaktorové ověřování | Microsoft Docs'
description: Postup povolení služby Multi-Factor Authentication v aplikacích určených zabezpečené službou Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: f439c65d9b4fbeb78623312c426767f89dfd9fab
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C: Povolení služby Multi-Factor Authentication v aplikacích určených uživatelům
Azure Active Directory (Azure AD) B2C se integruje přímo s [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) tak, že přidáte druhou vrstvu zabezpečení do prostředí registrace a přihlašování v aplikacích určených uživatelům. A můžete to provést bez nutnosti napsat jediný řádek kódu. Momentálně podporujeme telefonního hovoru a textové zprávy ověření. Pokud jste již vytvořili zásady registrace a přihlášení, stále můžete povolit službu Multi-Factor Authentication.

> [!NOTE]
> Služba Multi-Factor Authentication lze povolit taky při vytváření zásad registrace a přihlášení, nikoli pouze úpravou existující zásady.
> 
> 

Tato funkce vám pomůže aplikace zpracovávat například následující scénáře:

* Nemusíte požadovat použití vícefaktorového ověřování pro přístup k jedné aplikace, ale potřebujete přístup jiný. Například příjemce může přihlásit k aplikaci automaticky pojištění přes sociální nebo místní účet, ale před přístup k domovskému pojištění aplikaci zaregistrovat ve stejném adresáři musí ověřit telefonní číslo.
* Nemusíte požadovat použití vícefaktorového ověřování pro přístup k aplikaci, obecně, ale potřebujete přístup citlivých částí v něm. Například příjemce můžete přihlásit k aplikaci bankovní s sociálních nebo místní účet, zkontrolujte zůstatek účtu, ale musí ověřit tak telefonní číslo před pokusem o přenos přenosu.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Upravit svojí registrační zásadě k povolení služby Multi-Factor Authentication
1. [Postupujte podle těchto kroků přejděte do okna s funkcemi B2C na portálu Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klikněte na **Zásady registrace**.
3. Klikněte na tlačítko svojí registrační zásadě (například "B2C_1_SiUp") a ten se otevře.
4. Klikněte na tlačítko **služby Multi-Factor authentication** a vypnout **stavu** k **ON**. Klikněte na **OK**.
5. Klikněte na tlačítko **Uložit** v horní části okna.

Funkci "Spustit nyní" v zásadách slouží k ověření prostředí pro uživatele. Zkontrolujte:

Uživatelský účet se vytvoří ve vašem adresáři předtím, než dojde k kroku Multi-Factor Authentication. Během kroku se příjemce zobrazí výzva, zadejte své telefonní číslo a ověřte ji. Pokud je ověření úspěšné, telefonní číslo je připojený k příjemce účet pro pozdější použití. I když příjemce zruší nebo potlačení, potvrdí můžete požádáni o ověření telefonního čísla znovu při dalším přihlášení (s povolenou službou Multi-Factor Authentication).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Upravit zásady přihlášení k povolení služby Multi-Factor Authentication
1. [Postupujte podle těchto kroků přejděte do okna s funkcemi B2C na portálu Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klikněte na tlačítko **přihlášení zásady**.
3. Klikněte na tlačítko Přihlásit zásady vaší (například "B2C_1_SiIn") a ten se otevře. Klikněte na tlačítko **upravit** v horní části okna.
4. Klikněte na tlačítko **služby Multi-Factor authentication** a vypnout **stavu** k **ON**. Klikněte na **OK**.
5. Klikněte na tlačítko **Uložit** v horní části okna.

Funkci "Spustit nyní" v zásadách slouží k ověření prostředí pro uživatele. Zkontrolujte:

Když se příjemci přihlásí (pomocí sociálních nebo místní účet), pokud ověřený telefonní číslo je připojena k příjemce účtu, uživatel vyzván k ověření. Pokud je připojen bez telefonního čísla, příjemci se zobrazí výzva, zadejte jeden a ověřte ji. Na úspěšné ověřování telefonní číslo je připojený k příjemce účtu pro pozdější použití.

## <a name="multi-factor-authentication-on-other-policies"></a>Služba Multi-Factor Authentication na další zásady
Jak je popsáno pro registrace a přihlášení zásady, výše, je také možné povolit službu Multi-Factor authentication na registrace nebo přihlášení zásady a heslo resetovat zásady. Bude k dispozici co nejdříve na zásady pro úpravy profilu.

