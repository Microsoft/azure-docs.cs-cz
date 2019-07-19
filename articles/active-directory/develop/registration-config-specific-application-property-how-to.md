---
title: Postup vyplnění konkrétních polí pro aplikaci s vlastním vývojem | Microsoft Docs
description: Pokyny, jak vyplnit konkrétní pole při registraci vlastní vyvíjené aplikace pomocí Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46bc6d2ebbb22ffc37ca73a3fff3b7b5a3a534b8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325337"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Postup vyplnění konkrétních polí pro aplikaci vytvořenou pro vlastní nastavení

Tento článek poskytuje stručný popis všech dostupných polí ve formuláři pro registraci aplikace v [Azure Portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrace nové aplikace

-   Pokud chcete zaregistrovat novou aplikaci, přejděte na [Azure Portal](https://portal.azure.com).

-   V levém navigačním podokně klikněte na **Azure Active Directory.**

-   Vyberte **Registrace aplikací** a klikněte na **Přidat**.

-   Tím otevřete formulář pro registraci aplikace.

## <a name="fields-in-the-application-registration-form"></a>Pole ve formuláři pro registraci aplikace


| Pole            | Popis                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name             | Název aplikace Musí mít minimálně čtyři znaky.                |
| Podporované typy účtu| Vyberte účty, které chcete, aby vaše aplikace podporovala: účty v tomto adresáři organizace, účty v libovolném organizačním adresáři nebo účty v libovolném organizačním adresáři a v osobních účtech Microsoft.  |
| Identifikátor URI přesměrování (volitelné) | Vyberte typ aplikace, kterou vytváříte, **webový** nebo **veřejný klient (mobilní & Desktop)** a pak zadejte identifikátor URI přesměrování (nebo adresu URL odpovědi) pro vaši aplikaci. V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například http://localhost:31544 může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci. V případě veřejných klientských aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte hodnotu specifickou pro vaši aplikaci, například myapp://auth. Konkrétní příklady webových nebo nativních aplikací najdete v našich [rychlých startech](https://docs.microsoft.com/azure/active-directory/develop).|

Jakmile vyplníte výše uvedená pole, aplikace je zaregistrována v Azure Portal a budete přesměrováni na stránku s přehledem aplikace. Stránky nastavení v levém podokně v části **Spravovat** mají více polí pro přizpůsobení aplikace. V následujících tabulkách jsou popsána všechna pole. V závislosti na tom, zda jste vytvořili webovou aplikaci nebo veřejnou klientskou aplikaci, se zobrazí pouze podmnožina těchto polí.

### <a name="overview"></a>Přehled
| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID aplikace  | Když zaregistrujete aplikaci, Azure AD přiřadí vaší aplikaci ID aplikace. ID aplikace se dá použít k jednoznačné identifikaci vaší aplikace v požadavcích na ověření ve službě Azure AD a také k přístupu k prostředkům, jako je Graph API.                                                          |
| Identifikátor URI ID aplikace      | Mělo by se jednat o jedinečný identifikátor URI, většinou ve **tvaru&lt;název aplikace&gt;\_\_/&lt;&gt;název klienta https://.** Tento postup se používá během autorizačního toku autorizace jako jedinečný identifikátor k určení prostředku, pro který má být token vydán. Také se z vydaného přístupového tokenu stala deklarací ' AUD '. |

### <a name="branding"></a>Branding

| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nahrát nové logo | Tuto možnost můžete použít k nahrání loga aplikace. Logo musí být ve formátu. bmp,. jpg nebo. png a velikost souboru by měla být menší než 100 KB. Rozměry obrázku by měly být 215x215 pixely s rozměry centrálního obrazu 94x94 pixelů.|
| Adresa URL domovské stránky   | Toto je přihlašovací adresa URL zadaná při registraci aplikace.|

### <a name="authentication"></a>Ověřování

| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adresa URL – odhlášení      | Toto je adresa URL pro odhlášení jednotného odhlášení. Pokud uživatel vymaže svou relaci s Azure AD pomocí jakékoli jiné registrované aplikace, odešle Azure AD na tuto adresu URL žádost o odhlášení.|
| Podporované typy účtu  | Tento přepínač určuje, zda lze aplikaci použít ve více klientech. Obvykle to znamená, že externí organizace můžou aplikaci používat tak, že ji zaregistruje ve svém tenantovi a udělí přístup k datům organizace.|
| Adresy URL pro přesměrování      | Adresa URL přesměrování nebo odpověď jsou koncové body, kde Azure AD vrací jakékoli tokeny, které vaše aplikace požaduje. Pro nativní aplikace se uživateli pošle po úspěšné autorizaci. Azure AD kontroluje, že identifikátor URI pro přesměrování, který vaše aplikace dodá v žádosti OAuth 2,0, odpovídá jedné z registrovaných hodnot na portálu.|

### <a name="certificates-and-secrets"></a>Certifikáty a tajné klíče

| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tajné klíče klienta            | Můžete vytvářet klientské tajné klíče nebo klíče pro programové přístup k webovým rozhraním API zabezpečeným službou Azure AD bez zásahu uživatele. Na stránce **nový tajný klíč klienta** zadejte popis klíče a datum vypršení platnosti a klikněte na Uložit, aby se klíč vygeneroval. Ujistěte se, že je uložíte někam v bezpečí, protože k němu nebudete mít přístup později.             |

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](../manage-apps/what-is-application-management.md)
