---
title: Multi-Factor Authentication v Azure Active Directory B2C | Microsoft Docs
description: Postup povolení Multi-Factor Authentication v aplikacích určených pro uživatele zabezpečených Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e328caa80a0e63f68f2563bc91a6405341ad064e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102064"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Povolit službu Multi-Factor Authentication v Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) se přímo integruje s [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) , takže můžete přidat druhou vrstvu zabezpečení pro registraci a přihlašování v aplikacích. Službu Multi-Factor Authentication povolíte bez psaní jediného řádku kódu. Pokud jste už vytvořili zápisy uživatelů pro registraci a přihlašování, můžete přesto povolit službu Multi-Factor Authentication.

Tato funkce pomáhá aplikacím zpracovávat scénáře, jako například:

- Pro přístup k jedné aplikaci nevyžadujete vícefaktorové ověřování, ale vyžadujete, aby k ní měl přístup jiný. Zákazník se například může přihlásit k aplikaci pro automatické pojištění pomocí sociálního nebo místního účtu, ale před přístupem k aplikaci pro domácí pojištění zaregistrovanou ve stejném adresáři musí ověřit telefonní číslo.
- Službu Multi-Factor Authentication nevyžadujete pro přístup k aplikaci obecně, ale vyžadujete přístup k citlivým částem v rámci této aplikace. Zákazník se například může přihlásit k bankovní aplikaci pomocí sociálního nebo místního účtu a zkontrolovat zůstatek účtu, ale před pokusem o přenos do přenosu musí ověřit telefonní číslo.

## <a name="set-multi-factor-authentication"></a>Nastavit službu Multi-Factor Authentication

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Pomocí filtru **adresář a odběr** v horní nabídce vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Vyberte tok uživatele, pro který chcete povolit MFA. Například *B2C_1_signinsignup*.
1. Vyberte **Vlastnosti**.
1. V části vícefaktorového **ověřování** vyberte požadovanou **metodu MFA**a potom v části **vynucení MFA** vyberte možnost **vždy zapnuto**, nebo ** [podmíněný](conditional-access-user-flow.md) (doporučeno)**. Pro podmíněný postup vytvořte zásadu [zásad podmíněného přístupu](conditional-access-identity-protection-setup.md) a určete aplikace, pro které chcete zásady použít. 
1. Vyberte Uložit. Pro tento tok uživatelů je nyní povoleno vícefaktorové ověřování.

K ověření prostředí můžete použít **tok spuštění uživatele** . Potvrďte následující scénář:

Ve vašem tenantovi se vytvoří účet zákazníka před tím, než nastane krok služby Multi-Factor Authentication. V průběhu tohoto kroku se zákazník vyzve k zadání telefonního čísla a jeho ověření. Pokud je ověření úspěšné, je telefonní číslo připojeno k účtu pro pozdější použití. I v případě, že zákazník zruší nebo odmítá, může být zákazník požádán o ověření telefonního čísla znovu při příštím přihlášení s povoleným službou Multi-Factor Authentication.



