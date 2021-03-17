---
title: Registrovat jednostránkové aplikace (SPA) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet jednostránkové aplikace (registrace aplikací).
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 9366bb5b2bb5820245ec1b699bbf2ddda0dd9f9d
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103171"
---
# <a name="single-page-application-app-registration"></a>Jednostránkové aplikace: registrace aplikace

K registraci jednostránkové aplikace (SPA) na platformě Microsoft Identity proveďte následující kroky. Registrační postup se liší od MSAL.js 1,0, který podporuje tok implicitního udělení a MSAL.js 2,0, který podporuje tok autorizačního kódu s PKCE.

## <a name="create-the-app-registration"></a>Vytvoření registrace aplikace

U aplikací založených na MSAL.js 1,0 a 2,0 začněte provedením následujících kroků a vytvořte prvotní registraci aplikace.

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
1. Zadejte **název** vaší aplikace. Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
1. Vyberte **podporované typy účtů** pro aplikaci. Nezadávejte **identifikátor URI přesměrování**.  Popis různých typů účtů najdete v tématu [Registrace aplikace](quickstart-register-app.md).
1. Kliknutím na **Registrovat** vytvořte registraci aplikace.

V dalším kroku nakonfigurujte registraci aplikace pomocí **identifikátoru URI přesměrování** , abyste určili, kde má platforma Microsoft identity by měla přesměrovat klienta spolu s případnými tokeny zabezpečení. Použijte postup, který je vhodný pro verzi MSAL.js, kterou používáte ve vaší aplikaci:

- [MSAL.js 2,0 s tokem kódu ověřování](#redirect-uri-msaljs-20-with-auth-code-flow) (doporučeno)
- [MSAL.js 1,0 s implicitním tokem](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Identifikátor URI pro přesměrování: [MSAL.js 2,0 s tokem kódu ověřování](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

Pomocí těchto kroků přidejte identifikátor URI přesměrování pro aplikaci, která používá MSAL.js 2,0 nebo novější. MSAL.js 2.0 + podporuje tok autorizačního kódu s PKCE a CORS v reakci na [omezení souborů cookie třetích stran v prohlížeči](reference-third-party-cookies-spas.md). V MSAL.js 2.0 + není podporován tok implicitního udělení.

1. V Azure Portal vyberte registraci aplikace, kterou jste vytvořili dříve v části [Vytvoření registrace aplikace](#create-the-app-registration).
1. V části **Spravovat** vyberte **ověřování**  >  **Přidat platformu**.
1. V části **webové aplikace** vyberte dlaždici **aplikace s jednou stránkou** .
1. V části **identifikátory URI pro přesměrování** zadejte [identifikátor URI pro přesměrování](reply-url.md). Nevybírejte **buď zaškrtávací políčko v** části **implicitní udělení a hybridní toky**.
1. Vyberte **Konfigurovat** a dokončete přidávání identifikátoru URI přesměrování.

Právě jste dokončili registraci jednostránkové aplikace (SPA) a nakonfigurovali identifikátor URI pro přesměrování, ke kterému bude klient přesměrován, a budou odeslány všechny tokeny zabezpečení. Když nakonfigurujete identifikátor URI pro přesměrování pomocí dlaždice **jednostránkové aplikace** v podokně **Přidat platformu** , registrace vaší aplikace je nakonfigurovaná tak, aby podporovala tok autorizačního kódu s PKCE a CORS.

Další pokyny najdete v tomto [kurzu](tutorial-v2-javascript-auth-code.md) .

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Identifikátor URI pro přesměrování: [MSAL.js 1,0 s implicitním tokem](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

Pomocí těchto kroků přidejte identifikátor URI pro přesměrování pro jednostránkovou aplikaci, která používá MSAL.js 1,3 nebo starší a implicitně udělený tok. Aplikace, které používají MSAL.js 1,3 nebo starší, nepodporují tok kódu ověřování.

1. V Azure Portal vyberte registraci aplikace, kterou jste vytvořili dříve v části [Vytvoření registrace aplikace](#create-the-app-registration).
1. V části **Spravovat** vyberte **ověřování**  >  **Přidat platformu**.
1. V části **webové aplikace** vyberte dlaždici **aplikace s jednou stránkou** .
1. V části **identifikátory URI pro přesměrování** zadejte [identifikátor URI pro přesměrování](reply-url.md).
1. Povolení **implicitních a hybridních toků**:
    - Pokud se vaše aplikace přihlásí uživatelům, vyberte **tokeny ID**.
    - Pokud vaše aplikace také potřebuje volat chráněné webové rozhraní API, vyberte **přístupové tokeny**. Další informace o těchto typech tokenů najdete v tématu [tokeny ID](id-tokens.md) a [přístupové tokeny](access-tokens.md).
1. Vyberte **Konfigurovat** a dokončete přidávání identifikátoru URI přesměrování.

Právě jste dokončili registraci jednostránkové aplikace (SPA) a nakonfigurovali identifikátor URI pro přesměrování, ke kterému bude klient přesměrován, a budou odeslány všechny tokeny zabezpečení. Výběrem jednoho nebo obou **tokenů ID** a **přístupových tokenů** jste povolili postup implicitního udělení.

Další pokyny najdete v tomto [kurzu](tutorial-v2-javascript-spa.md) .

## <a name="note-about-authorization-flows"></a>Poznámka o autorizačních tocích

Ve výchozím nastavení je registrace aplikace vytvořená pomocí stránky konfigurace platformy s jednou stránkou umožňuje tok autorizačního kódu. Aby bylo možné využít tento tok, musí vaše aplikace používat MSAL.js 2,0 nebo novější.

Jak už bylo uvedeno dříve, jednostránkové aplikace používající MSAL.js 1,3 jsou omezeny na implicitní tok udělení. Aktuální [osvědčené postupy OAuth 2,0](v2-oauth2-auth-code-flow.md) doporučují použití toku autorizačního kódu místo implicitního toku pro jednostránkové. Použití obnovovacích tokenů s omezeným počtem dob také pomůže přizpůsobit aplikaci na [moderní omezení ochrany osobních údajů souborů cookie v prohlížeči](reference-third-party-cookies-spas.md), jako je Safari ITP.

Když všechny vaše produkční aplikace s jednou stránkou, které jsou reprezentované registrací aplikace, používají MSAL.js 2,0 a tok autorizačního kódu, zrušte v Azure Portal podokno **ověřování** registrace aplikace. Aplikace používající MSAL.js 1. x a implicitní tok mohou i nadále fungovat, ale pokud ponecháte povolený implicitní tok (zaškrtnuto).

## <a name="next-steps"></a>Další kroky

Nakonfigurujte kód vaší aplikace tak, aby používal registraci aplikace, kterou jste vytvořili v předchozích krocích: [Konfigurace kódu aplikace](scenario-spa-app-configuration.md).
