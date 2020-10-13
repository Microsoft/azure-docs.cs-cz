---
title: Multi-Factor Authentication v Azure Active Directory B2C | Microsoft Docs
description: Postup povolení Multi-Factor Authentication v aplikacích určených pro uživatele zabezpečených Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 69096e5f650a131c5af7ec4da60b7cbca225a56f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87116597"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Povolit službu Multi-Factor Authentication v Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) se přímo integruje s [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) , takže můžete přidat druhou vrstvu zabezpečení pro registraci a přihlašování v aplikacích. Službu Multi-Factor Authentication povolíte bez psaní jediného řádku kódu. Pokud jste už vytvořili zápisy uživatelů pro registraci a přihlašování, můžete přesto povolit službu Multi-Factor Authentication.

Tato funkce pomáhá aplikacím zvládnout následující scénáře:

- Pro přístup k jedné aplikaci nevyžadujete vícefaktorové ověřování, ale vyžadujete, aby k ní měl přístup jiný. Zákazník se například může přihlásit k aplikaci pro automatické pojištění pomocí sociálního nebo místního účtu, ale před přístupem k aplikaci pro domácí pojištění zaregistrovanou ve stejném adresáři musí ověřit telefonní číslo.
- Službu Multi-Factor Authentication nevyžadujete pro přístup k aplikaci obecně, ale vyžadujete přístup k citlivým částem v rámci této aplikace. Zákazník se například může přihlásit k bankovní aplikaci pomocí sociálního nebo místního účtu a zkontrolovat zůstatek účtu, ale před pokusem o přenos do přenosu musí ověřit telefonní číslo.

## <a name="set-multi-factor-authentication"></a>Nastavit službu Multi-Factor Authentication

Při vytváření toku uživatele máte možnost povolit službu Multi-Factor Authentication.

![Nastavit službu Multi-Factor Authentication](./media/custom-policy-multi-factor-authentication/add-policy.png)

Nastavte vícefaktorové **ověřování** na **povoleno**.

K ověření prostředí můžete použít **tok spuštění uživatele** . Potvrďte následující scénář:

Ve vašem tenantovi se vytvoří účet zákazníka před tím, než nastane krok služby Multi-Factor Authentication. V průběhu tohoto kroku se zákazník vyzve k zadání telefonního čísla a jeho ověření. Pokud je ověření úspěšné, je telefonní číslo připojeno k účtu pro pozdější použití. I v případě, že zákazník zruší nebo odmítá, může být zákazník požádán o ověření telefonního čísla znovu při příštím přihlášení s povoleným službou Multi-Factor Authentication.

## <a name="add-multi-factor-authentication"></a>Přidat Multi-Factor Authentication

Službu Multi-Factor Authentication je možné povolit na toku uživatele, který jste vytvořili dříve.

Chcete-li povolit službu Multi-Factor Authentication:

1. Otevřete tok uživatele a pak vyberte **vlastnosti**.
2. Vedle **vícefaktorového ověřování**vyberte **povoleno**.
3. Klikněte na **Uložit** v horní části stránky.


