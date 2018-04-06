---
title: K vyplnění konkrétních polí pro aplikaci zákaznických | Microsoft Docs
description: Pokyny k vyplnění konkrétních polí při registraci vlastní aplikace vyvinuté pomocí Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cb85971d6d3d4dade69b9dc04e070c67341533a5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Postup vyplňte konkrétní pole vyvinul vlastní aplikace

V tomto článku poskytují stručný popis všechna dostupná pole ve formuláři pro registraci aplikace v [portál Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Zaregistrujte novou aplikaci

-   Chcete-li zaregistrovat novou aplikaci, přejděte na [portál Azure](https://portal.azure.com).

-   V levém navigačním podokně klikněte na tlačítko **Azure Active Directory.**

-   Zvolte **registrace aplikace** a klikněte na tlačítko **přidat**.

-   Tuto, otevřete si aplikaci registračním formuláři.

## <a name="fields-in-the-application-registration-form"></a>Pole ve formuláři pro registraci aplikace


| Pole            | Popis                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Název             | Název aplikace. Musí mít minimálně 4 znaky.                |
| Typ aplikace | **Webovou aplikaci nebo webové rozhraní API**: aplikace, která představuje webovou aplikaci, webového rozhraní API nebo obojí 
| |**Nativní**: aplikace, která může být nainstalována na počítači nebo zařízení uživatele           |
| Přihlašovací adresa URL      | Adresa URL, kde můžete uživatelům přihlásit a používat vaši aplikaci                                  |

Jakmile jste vyplnili pole výše, zaregistrovat aplikaci na portálu Azure a budete přesměrováni na stránku aplikace. **Nastavení** stisknutí tlačítka na panelu aplikace otevře se stránka nastavení, který má více polí můžete přizpůsobit vaší aplikace. Následující tabulka popisuje všechna pole na stránce nastavení. Všimněte si, že by vidět jenom podmnožinu těchto polí, v závislosti na tom, jestli jste vytvořili webovou aplikaci nebo nativní aplikaci.

| Pole           | Popis                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID aplikace  | Při registraci aplikace Azure AD přiřadí vaší aplikace ID aplikace. Aplikace, ID, které lze použít k jednoznačné identifikaci vaší aplikace v žádosti o ověření do služby Azure AD, a také přístup k prostředkům, například rozhraní Graph API.                                                          |
| Identifikátor URI ID aplikace      | Měl by být jedinečný identifikátor URI, obvykle ve formátu **https://&lt;klienta\_název&gt;/&lt;aplikace\_název&gt;.** Při udělení tok autorizace, slouží jako jedinečný identifikátor pro zadejte prostředek, který by měl být token vydán pro. Také bude deklarace identity, oblast, v vydaný přístupový token. |
| Nahrát nové logo | To můžete použít k nahrání logo, které se pro vaši aplikaci. Logo musí být ve formátu BMP, JPG nebo PNG a velikost souboru musí být menší než 100KB. Dimenze pro bitovou kopii by měl být 215 x 215 pixelů, s dimenzemi v centrální image 94 x 94 pixelů.                                                       |
| Adresa URL domovské stránky   | Toto je přihlašovací adresa URL zadaná při registraci aplikace.                                                                                                                                                                                                                                              |
| Adresa URL – odhlášení      | Tuto adresu URL odhlásit jediné odhlášení. Azure AD odešle žádost o odhlášení tato adresa URL Pokud uživatel zruší zaškrtnutí jejich relace s Azure AD pomocí jiných zaregistrovanou aplikaci.                                                                                                                                       |
| Více tenantů  | Tento přepínač určuje, zda aplikace mohou být využívána víc klientů. Obvykle to znamená, že externími organizacemi moci používat vaši aplikaci zaregistrujete na jejich klienta a udělení přístupu k datům jejich organizace.                                                                   |
| Adresy URL odpovědí      | Odpověď se adresy URL koncových bodů kde Azure AD vrátí všechny tokeny, které vaše aplikace požaduje.                                                                                                                                                                                                          |
| Identifikátory URI pro přesměrování   | Nativních aplikací, to je, kde uživatel být odeslat následující úspěšném ověření. Azure kontrolu služby Active Directory, který poskytuje identifikátor URI aplikace přesměrování v požadavku OAuth 2.0 odpovídá jednomu z registrovaných hodnoty na portálu.                                                            |
| Klíče            | Můžete vytvořit klíče prostřednictvím kódu programu přístup k webovému rozhraní API pro zabezpečené službou Azure AD bez nutnosti zásahu uživatele. Z \* \*klíče\* \* stránky, zadejte klíče popis a datum vypršení platnosti a uložte ke generování klíče. Zajistěte, aby ji uložit někde bezpečný, protože nebudete moci později přístup.             |

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](active-directory-enable-sso-scenario.md)
