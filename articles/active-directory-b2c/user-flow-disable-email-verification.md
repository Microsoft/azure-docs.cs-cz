---
title: Zakázání ověření e-mailu během registrace zákazníka
titleSuffix: Azure AD B2C
description: Přečtěte si, jak zakázat ověřování e-mailů během registrace zákazníků ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126748"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Zakázání ověření e-mailu během registrace zákazníka ve službě Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Ověřování e-mailů zakážete takto:

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Pomocí filtru **Directory + odběr** v horní nabídce vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatele**.
1. Vyberte tok uživatele, pro který chcete zakázat ověření e-mailu. Například *B2C_1_signinsignup*.
1. Vyberte **Rozložení stránky**.
1. Vyberte **stránku registrace místního účtu**.
1. V části **Atributy uživatele**vyberte **možnost E-mailová adresa**.
1. V rozevíracím uznanéma **Vyžaduje ověření** vyberte **ne**.
1. Vyberte **Uložit**. Ověření e-mailu je nyní pro tento tok uživatelů zakázáno.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [přizpůsobit uživatelské rozhraní ve službě Azure Active Directory B2C](customize-ui-overview.md)

