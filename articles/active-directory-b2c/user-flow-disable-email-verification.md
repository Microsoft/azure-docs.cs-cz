---
title: Zakázat ověřování e-mailů při registraci zákazníka
titleSuffix: Azure AD B2C
description: Naučte se zakázat ověřování e-mailů při registraci zákazníka v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10613bd2d6219272248f882e45ae1c33d2cc9d61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85384205"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Zakázat ověřování e-mailů při registraci zákazníka v Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Ověřování e-mailu zakážete pomocí těchto kroků:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Pomocí filtru **adresář a odběr** v horní nabídce vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Vyberte tok uživatele, pro který chcete zakázat ověřování e-mailů. Například *B2C_1_signinsignup*.
1. Vyberte **rozložení stránky**.
1. Vyberte **stránku pro registraci místního účtu**.
1. V části **atributy uživatele**vyberte **e-mailová adresa**.
1. V rozevíracím seznamu **vyžaduje ověření** vyberte možnost **ne**.
1. Vyberte **Uložit**. Ověřování e-mailu je teď pro tento tok uživatelů zakázané.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [přizpůsobit uživatelské rozhraní v Azure Active Directory B2C](customize-ui-overview.md)

