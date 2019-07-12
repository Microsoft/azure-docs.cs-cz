---
title: Jak vyplnit konkrétní pole pro vlastní vyvinuté aplikaci | Dokumentace Microsoftu
description: Pokyny o tom, jak vyplnit konkrétní pole při registraci vlastní vyvinuté aplikaci s využitím Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b01ff1e2d0c9bc926d54bd54716e0579ef395ec0
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655997"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Jak vyplnit konkrétní pole pro vlastní vyvinuté aplikaci

Tento článek obsahuje stručný popis všechna dostupná pole ve formuláři pro registraci aplikace v [webu Azure portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrace nové aplikace

-   Registrace nové aplikace, přejděte [webu Azure portal](https://portal.azure.com).

-   V levém navigačním podokně klikněte na tlačítko **Azure Active Directory.**

-   Zvolte **registrace aplikací** a klikněte na tlačítko **přidat**.

-   Otevřete tento registrační formulář aplikace.

## <a name="fields-in-the-application-registration-form"></a>Pole ve formuláři pro registraci aplikace


| Pole            | Popis                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name             | Název aplikace. Musí mít minimálně 4 znaky.                |
| Podporované typy účtu| Vyberte účty, které chcete aplikaci pro podporu: účty v tomto adresáři organizace jenom účty v libovolném adresáři organizace nebo účty v libovolném adresáři organizace a osobní účty Microsoft.  |
| (Volitelné) identifikátor URI pro přesměrování | Vyberte typ aplikace, které sestavujete, **webové** nebo **veřejným klientem (mobilních a desktopových)** a pak zadejte identifikátor URI pro přesměrování (nebo adresy URL odpovědi) pro vaši aplikaci. V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například http://localhost:31544 může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci. V případě veřejných klientských aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte hodnotu specifickou pro vaši aplikaci, jako je například myapp://auth. Konkrétní příklady webových nebo nativních aplikací najdete v našich [rychlých startech](https://docs.microsoft.com/azure/active-directory/develop).|

Jakmile jste vyplnili pole výše, aplikace je zaregistrovaný na webu Azure Portal a budete přesměrováni na stránku přehled aplikace. Nastavení stránky v levém podokně v části **spravovat** mají více polí můžete přizpůsobit aplikaci. Následující tabulky popisují všechna pole. Uvidíte pouze podmnožinu těchto polí, v závislosti na tom, jestli jste vytvořili webovou aplikaci nebo veřejné klientské aplikace.

### <a name="overview"></a>Přehled
| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID aplikace  | Při registraci aplikace Azure AD přiřadí vaší aplikace ID aplikace. Aplikace, ID se dá použít k jednoznačné identifikaci aplikace v žádosti o ověření do služby Azure AD, jakož i přístup k prostředkům, jako jsou rozhraní Graph API.                                                          |
| Identifikátor URI ID aplikace      | To by měl být jedinečný identifikátor URI, obvykle ve formátu **https://&lt;tenanta\_název&gt;/&lt;aplikace\_název&gt;.** Během toku udělení autorizace, slouží jako jedinečný identifikátor pro zadejte prostředek, který by měl být token vydán pro. Bude také "aud" deklarace identity v vydaný přístupový token. |

### <a name="branding"></a>Branding

| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nahrát nové logo | Může být využit k nahrání loga pro vaši aplikaci. Logo musí být ve formátu .bmp, .jpg nebo .png a velikost souboru by měla být menší než 100 KB. Rozměry obrázku by měl být 215 × 215 pixelů a středové rozměry obrázku 94 x 94 pixelů.|
| Adresa URL domovské stránky   | Toto je přihlašovací adresa URL zadaná při registraci aplikace.|

### <a name="authentication"></a>Ověřování

| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adresa URL – odhlášení      | Toto je adresa URL jednotného odhlašování odhlašování. Azure AD odešle žádost o odhlášení na tuto adresu URL Pokud uživatel vymaže jejich relace s Azure AD pomocí jiných registrované aplikaci.|
| Podporované typy účtu  | Tento přepínač určuje, jestli aplikace můžou využívat více tenantů. Obvykle to znamená, že externími organizacemi moci používat vaši aplikaci prostřednictvím registrace v rámci jejich tenanta a udělení přístupu k datům organizace.|
| Adresy URL pro přesměrování      | Přesměrování nebo odpověď, jsou adresy URL koncové body, kam Azure AD vrací všechny tokeny, které vaše aplikace požaduje. Pro nativní aplikace to je, kde je uživatel přesměrován po úspěšném ověření. Azure AD ověří, že vaše aplikace poskytuje v požadavku OAuth 2.0 URI přesměrování odpovídá jednomu z registrovaných hodnoty na portálu.|

### <a name="certificates-and-secrets"></a>Certifikáty a tajné kódy

| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tajné klíče klienta            | Klienta můžete vytvořit tajné kódy nebo klávesy se šipkami, programově přístup k webové rozhraní API zabezpečené pomocí Azure AD bez nutnosti zásahu uživatele. Z **nový tajný kód klienta** stránky, zadejte popis klíče a datum vypršení platnosti a uložit ke generování klíče. Ujistěte se, uložte ho někam zabezpečené, nebudou mít přístup k později.             |

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](../manage-apps/what-is-application-management.md)
