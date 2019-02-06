---
title: Kurz – registr aplikaci – Azure Active Directory B2C | Dokumentace Microsoftu
description: Informace o postupu registrace webové aplikace v Azure Active Directory B2C s využitím webu Azure portal.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/05/2019
ms.author: davidmu
ms.openlocfilehash: 1f9a4f2f0ac44c8815e33957b49b4215c998eae3
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754162"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Kurz: Registrace aplikace v Azure Active Directory B2C

Před vaše [aplikací](active-directory-b2c-apps.md) mohou komunikovat s Azure Active Directory (Azure AD) B2C, musí být zaregistrovaná v tenantovi, která spravujete. V tomto kurzu se dozvíte, jak zaregistrovat webové aplikace s využitím webu Azure portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Vytvoření tajného klíče klienta

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastní [Tenanta Azure AD B2C](tutorial-create-tenant.md), založte si ho teď. Můžete používat existujícího tenanta Azure AD B2C.

## <a name="register-a-web-application"></a>Registrace webové aplikace

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **aplikací**a pak vyberte **přidat**.
4. Zadejte název aplikace. Například *webapp1*.
5. Pro **zahrnout webovou aplikaci / webové rozhraní API** a **povolit implicitní tok**vyberte **Ano**.
6. Pro **adresy URL odpovědi**, zadejte koncový bod kam by měl Azure AD B2C vrátí všechny tokeny, které vaše aplikace požaduje. Například můžete nastavit ji pro naslouchání lokálně na `https://localhost:44316` Pokud ještě neznáte číslo portu, můžete zadat hodnotu zástupného symbolu a později změnit. Pro účely testování můžete ji nastavit `https://jwt.ms`, který se zobrazí obsah tokenu pro kontrolu. Pro účely tohoto kurzu, nastavte ho na `https://jwt.ms`. 

    Odpověď URL musí začínat schématem `https`, a všechny odpovědi, adresa URL musí sdílet jednu doménu DNS. Například, pokud aplikace obsahuje adresu URL odpovědi z `https://login.contoso.com`, jako je tato adresa URL můžete přidat k němu `https://login.contoso.com/new`. Nebo mohou odkazovat na subdoménu DNS `login.contoso.com`, jako například `https://new.login.contoso.com`. Pokud chcete, aby byla aplikace s `login-east.contoso.com` a `login-west.contoso.com` podle adresy URL odpovědí, je nutné přidat tyto adresy URL odpovědi v tomto pořadí: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Druhé dvě můžete přidat, protože jsou subdoménami první adresy URL odpovědi, `contoso.com`.

7. Klikněte na možnost **Vytvořit**.

## <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Pokud jste aplikaci vymění kód pro token, je potřeba vytvořit tajný klíč aplikace.

1. Vyberte **klíče** a potom klikněte na tlačítko **vygenerovat klíč**.
2. Vyberte **Uložit** zobrazíte klíče. Poznamenejte si hodnotu **Klíč aplikace**. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Vytvoření tajného klíče klienta

> [!div class="nextstepaction"]
> [Vytvořit toky uživatelů v Azure Active Directory B2C](tutorial-create-user-flows.md)