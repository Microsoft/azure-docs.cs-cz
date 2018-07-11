---
title: Jak vyplnit konkrétní pole pro vlastní vyvinuté aplikaci | Dokumentace Microsoftu
description: Pokyny o tom, jak vyplnit konkrétní pole při registraci vlastní vyvinuté aplikaci s využitím Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: c6f6b0685c83d9305c62f121aec562d2afedae21
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "36335713"
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
| Název             | Název aplikace. Musí mít minimálně 4 znaky.                |
| Typ aplikace | **Webovou aplikaci/webové rozhraní API**: aplikace, která představuje webovou aplikaci, webové rozhraní API nebo obojí 
| |**Nativní**: aplikace, která se dá nainstalovat na počítač nebo zařízení uživatele           |
| Přihlašovací adresa URL      | Adresa URL, kde se uživatel může přihlásit k používání aplikace                                  |

Po vyplnění polí výše, aplikace je zaregistrovaný na webu Azure Portal a budete přesměrováni na stránku aplikace. **Nastavení** stisknutí tlačítka na panelu aplikace otevře se stránka nastavení, která má více polí můžete přizpůsobit aplikaci. Následující tabulka popisuje všechna pole na stránce nastavení. Všimněte si, že zobrazí pouze podmnožinu těchto polí, v závislosti na tom, jestli jste vytvořili webovou aplikaci nebo nativní aplikaci.

| Pole           | Popis                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID aplikace  | Při registraci aplikace Azure AD přiřadí vaší aplikace ID aplikace. Aplikace, ID se dá použít k jednoznačné identifikaci aplikace v žádosti o ověření do služby Azure AD, jakož i přístup k prostředkům, jako jsou rozhraní Graph API.                                                          |
| Identifikátor URI ID aplikace      | To by měl být jedinečný identifikátor URI, obvykle ve formátu **https://&lt;tenanta\_název&gt;/&lt;aplikace\_název&gt;.** Během toku udělení autorizace, slouží jako jedinečný identifikátor pro zadejte prostředek, který by měl být token vydán pro. Bude také "aud" deklarace identity v vydaný přístupový token. |
| Nahrát nové logo | Může být využit k nahrání loga pro vaši aplikaci. Logo musí být ve formátu .bmp, .jpg nebo .png a velikost souboru by měla být menší než 100KB. Rozměry obrázku by měl být 215 × 215 pixelů a středové rozměry obrázku 94 x 94 pixelů.                                                       |
| Adresa URL domovské stránky   | Toto je přihlašovací adresa URL zadaná při registraci aplikace.                                                                                                                                                                                                                                              |
| Adresa URL – odhlášení      | Tuto adresu URL jednotného odhlašování odhlašování. Azure AD odešle žádost o odhlášení na tuto adresu URL Pokud uživatel vymaže jejich relace s Azure AD pomocí jiných registrované aplikaci.                                                                                                                                       |
| Více tenantů  | Tento přepínač určuje, jestli aplikace můžou využívat více tenantů. Obvykle to znamená, že externími organizacemi moci používat vaši aplikaci prostřednictvím registrace v rámci jejich tenanta a udělení přístupu k datům organizace.                                                                   |
| Adresy URL odpovědí      | Odpověď adresy URL jsou koncové body, kam Azure AD vrací všechny tokeny, které vaše aplikace požaduje.                                                                                                                                                                                                          |
| Identifikátory URI pro přesměrování   | Pro nativní aplikace to je, kde je uživatel přesměrován po úspěšném ověření. Kontrola Azure AD, poskytující identifikátor URI aplikace pro přesměrování v požadavku OAuth 2.0 odpovídá jednomu z registrovaných hodnoty na portálu.                                                            |
| Klíče            | Vytvořit klíče k programově přístup k webovému rozhraní API zabezpečené pomocí Azure AD bez nutnosti zásahu uživatele. Z \* \*klíče\* \* stránky, zadejte popis klíče a datum vypršení platnosti a uložit ke generování klíče. Ujistěte se, uložte ho někam zabezpečené, nebudou mít přístup k později.             |

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](manage-apps/what-is-application-management.md)
