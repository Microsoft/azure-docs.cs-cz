---
title: Nastavení registrace a přihlášení pomocí OpenID Connect
titleSuffix: Azure AD B2C
description: Nastavte registraci a přihlášení s libovolným poskytovatelem identity OpenID Connect (IdP) ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c72abf79f0a420309ebe229673be9439fd99b74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188252"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí OpenID Connect pomocí Služby Azure Active Directory B2C

[OpenID Connect](openid-connect.md) je ověřovací protokol postavený na OAuth 2.0, který lze použít pro bezpečné přihlášení uživatele. Většina zprostředkovatelů identit, kteří používají tento protokol jsou podporovány v Azure AD B2C. Tento článek vysvětluje, jak můžete do toků uživatelů přidat vlastní poskytovatele identit OpenID Connect.

## <a name="add-the-identity-provider"></a>Přidání zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C kliknutím na filtr **Directory + předplatné** v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Nový zprostředkovatel EOpenID Connect**.

## <a name="configure-the-identity-provider"></a>Konfigurace zprostředkovatele identity

Každý poskytovatel identity OpenID Connect popisuje dokument metadat, který obsahuje většinu informací potřebných k přihlášení. To zahrnuje informace, jako jsou adresy URL, které chcete použít, a umístění veřejných podpisových klíčů služby. Dokument metadat OpenID Connect je vždy umístěn v `.well-known\openid-configuration`koncovém bodě, který končí v . Pro poskytovatele identity OpenID Connect, kterého chcete přidat, zadejte jeho adresu URL metadat.

## <a name="client-id-and-secret"></a>ID klienta a tajné

Chcete-li uživatelům umožnit přihlášení, zprostředkovatel identity vyžaduje, aby vývojáři zaregistrovat aplikaci ve své službě. Tato aplikace má ID, které se označuje jako **ID klienta** a **tajný klíč klienta**. Zkopírujte tyto hodnoty od poskytovatele identity a zadejte je do odpovídajících polí.

> [!NOTE]
> Tajný klíč klienta je volitelný. Pokud však chcete použít [tok autorizačního kódu](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), který tajný klíč používá k výměně kódu za token, musíte zadat tajný klíč klienta.

## <a name="scope"></a>Rozsah

Obor definuje informace a oprávnění, která chcete získat od vlastního poskytovatele identity. Požadavky OpenID Connect musí `openid` obsahovat hodnotu oboru, aby bylo možné získat token ID od poskytovatele identity. Bez tokenu ID se uživatelé nemohou přihlásit k Azure AD B2C pomocí vlastního zprostředkovatele identity. Ostatní obory mohou být připojeny oddělené mezerou. Informace o tom, jaké další obory mohou být k dispozici, naleznete v dokumentaci k vlastnímu zprostředkovateli identity.

## <a name="response-type"></a>Typ odpovědi

Typ odpovědi popisuje, jaký druh informací je odeslána `authorization_endpoint` zpět v počáteční volání na vlastní zprostředkovatele identity. Lze použít následující typy odpovědí:

* `code`: Podle [toku autorizačního kódu](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)bude kód vrácen zpět do Azure AD B2C. Azure AD B2C pokračuje `token_endpoint` volání k výměně kódu pro token.
* `id_token`: Token ID se vrátí zpět do Azure AD B2C od vlastního zprostředkovatele identity.

## <a name="response-mode"></a>Režim odezvy

Režim odezvy definuje metodu, která by měla být použita k odeslání dat zpět z vlastního zprostředkovatele identity do Azure AD B2C. Lze použít následující režimy odezvy:

* `form_post`: Tento režim odezvy je doporučen pro nejlepší zabezpečení. Odpověď je přenášena `POST` pomocí metody HTTP, přičemž kód nebo token `application/x-www-form-urlencoded` je kódován v těle pomocí formátu.
* `query`: Kód nebo token je vrácena jako parametr dotazu.

## <a name="domain-hint"></a>Nápověda k doméně

Nápovědu k doméně lze přeskočit přímo na přihlašovací stránku zadaného zprostředkovatele identity namísto toho, aby uživatel provést výběr mezi seznam emitovaných zprostředkovatelů identity. Chcete-li povolit tento druh chování, zadejte hodnotu nápovědy domény. Chcete-li přejít na vlastní zprostředkovatele `domain_hint=<domain hint value>` identity, přidejte parametr na konec vašeho požadavku při volání Azure AD B2C pro přihlášení.

## <a name="claims-mapping"></a>Mapování deklarací identity

Poté, co vlastní zprostředkovatel identity odešle token ID zpět do Azure AD B2C, Azure AD B2C musí být schopen mapovat deklarace identity z přijatého tokenu na deklarace identity, které Azure AD B2C rozpozná a používá. Pro každé z následujících mapování naleznete v dokumentaci vlastního zprostředkovatele identity pochopit deklarace identity, které jsou vráceny zpět v tokenech zprostředkovatele identity:

* **ID uživatele**: Zadejte deklaraci identity, která poskytuje *jedinečný identifikátor* přihlášeného uživatele.
* **Zobrazovaný název**: Zadejte deklaraci, která uživateli poskytuje *zobrazované jméno* nebo *celé jméno.*
* **Křestní jméno**: Zadejte deklaraci, která obsahuje *křestní jméno* uživatele.
* **Příjmení**: Zadejte deklaraci, která udává *příjmení* uživatele.
* **E-mail**: Zadejte deklaraci, která poskytuje *e-mailovou adresu* uživatele.
