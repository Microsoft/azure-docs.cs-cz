---
title: Vícefaktorové ověřování ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Jak povolit vícefaktorové ověřování v aplikacích orientovaných na spotřebitele zabezpečených službou Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25724ba82e57c5e3800fa1a989dd4f504df1c163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189272"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Povolení vícefaktorového ověřování ve službě Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) integruje přímo s [Azure Multi-Factor Authentication,](../active-directory/authentication/multi-factor-authentication.md) takže můžete přidat druhou vrstvu zabezpečení pro registraci a přihlášení prostředí ve vašich aplikacích. Povolíte vícefaktorové ověřování bez psaní jednoho řádku kódu. Pokud jste již vytvořili zaregistrovat a přihlásit uživatelské toky, můžete stále povolit vícefaktorové ověřování.

Tato funkce pomáhá aplikacím zpracovávat scénáře, jako jsou následující:

- Pro přístup k jedné aplikaci nevyžadujete vícefaktorové ověřování, ale vyžadujete přístup k jiné aplikaci. Zákazník se například může přihlásit k aplikaci automatického pojištění pomocí sociálního nebo místního účtu, ale musí ověřit telefonní číslo před přístupem k aplikaci domácího pojištění registrované ve stejném adresáři.
- Pro přístup k aplikaci obecně nevyžadujete vícefaktorové ověřování, ale vyžadujete přístup k citlivým částem v ní. Zákazník se může například přihlásit k bankovní aplikaci pomocí sociálního nebo místního účtu a zkontrolovat zůstatek na účtu, ale před pokusem o bankovní převod musí ověřit telefonní číslo.

## <a name="set-multi-factor-authentication"></a>Nastavení vícefaktorového ověřování

Při vytváření toku uživatele máte možnost povolit vícefaktorové ověřování.

![Nastavení vícefaktorového ověřování](./media/custom-policy-multi-factor-authentication/add-policy.png)

Nastavte **vícefaktorové ověřování** na **povoleno**.

Můžete použít **Spustit tok uživatele** k ověření prostředí. Potvrďte následující scénář:

Účet zákazníka se vytvoří ve vašem tenantovi před tím, než dojde ke kroku vícefaktorového ověřování. Během kroku je zákazník požádán, aby poskytl telefonní číslo a ověřil ho. Pokud je ověření úspěšné, telefonní číslo je připojeno k účtu pro pozdější použití. I v případě, že zákazník zruší nebo vypadne, může být zákazník požádán o ověření telefonního čísla znovu během dalšího přihlášení s povoleným vícefaktorovým ověřováním.

## <a name="add-multi-factor-authentication"></a>Přidání vícefaktorového ověřování

Je možné povolit vícefaktorové ověřování na tok uživatele, který jste dříve vytvořili.

Povolení vícefaktorového ověřování:

1. Otevřete tok uživatele a pak vyberte **Vlastnosti**.
2. Vedle **položky Vícefaktorové ověřování**vyberte **Možnost Povoleno**.
3. Klikněte na **Uložit** v horní části stránky.


