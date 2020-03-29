---
title: Registrační pole portálu Azure pro vlastní vyvinuté aplikace
description: Pokyny pro registraci vlastní vyvinuté aplikace s Azure AD
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
ms.openlocfilehash: 36d74b9926639bb4ec49821a3d73b5d615016394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702670"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Registrační pole portálu Azure pro vlastní vyvinuté aplikace

Tento článek poskytuje stručný popis všech dostupných polí v registračním formuláři aplikace na [webu Azure Portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrace nové aplikace

-   Pokud chcete zaregistrovat novou aplikaci, přejděte na [portál Azure](https://portal.azure.com).

-   V levém navigačním podokně klikněte na **Azure Active Directory.**

-   Zvolte **Registrace aplikací** a klepněte na **tlačítko Přidat**.

-   Tím se otevře registrační formulář přihlášky.

## <a name="fields-in-the-application-registration-form"></a>Pole v registračním formuláři přihlášky

| Pole            | Popis                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name (Název)             | Název aplikace Měl by mít minimálně čtyři znaky.                |
| Podporované typy účtu| Vyberte účty, které má vaše aplikace podporovat: účty pouze v tomto organizačním adresáři, účty v libovolném organizačním adresáři nebo účty v libovolném organizačním adresáři a osobní účty Microsoft.  |
| Identifikátor URI přesměrování (volitelné) | Vyberte typ aplikace, kterou vytváříte, **webového** nebo **veřejného klienta (mobilní & plochy)** a zadejte identifikátor URI přesměrování (nebo adresu URL odpovědi) pro vaši aplikaci. V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například http://localhost:31544 může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci. V případě veřejných klientských aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte hodnotu specifickou pro vaši aplikaci, například myapp://auth. Chcete-li zobrazit konkrétní příklady webových aplikací nebo nativních aplikací, podívejte se na naše [rychlé starty](https://docs.microsoft.com/azure/active-directory/develop).|

Po vyplnění výše uvedených polí se aplikace zaregistruje na webu Azure Portal a budete přesměrováni na stránku přehledu aplikace. Stránky nastavení v levém podokně v části **Spravovat** mají další pole pro přizpůsobení aplikace. Níže uvedené tabulky popisují všechna pole. V závislosti na tom, zda jste vytvořili webovou aplikaci nebo veřejnou klientskou aplikaci, se zobrazí pouze podmnožina těchto polí.

### <a name="overview"></a>Přehled

| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID aplikace  | Když zaregistrujete aplikaci, Azure AD přiřadí vaší aplikaci ID aplikace. ID aplikace lze použít k jednoznačné identifikaci vaší aplikace v požadavcích na ověřování do Služby Azure AD, stejně jako pro přístup k prostředkům, jako je rozhraní API graph.                                                          |
| Identifikátor URI ID aplikace      | Mělo by se jedná o jedinečný identifikátor URI, obvykle formuláře **&lt;https:// název\_&gt;/&lt;\_&gt;aplikace název klienta .** Používá se během toku udělení autorizace jako jedinečný identifikátor k určení prostředku, pro který by měl být token vydán. Také se stane deklarací "aud" v vydaném přístupovém tokenu. |

### <a name="branding"></a>Branding

| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nahrání nového loga | Můžete použít k nahrání loga pro vaši aplikaci. Logo musí být ve formátu .bmp, .jpg nebo PNG a velikost souboru by měla být menší než 100 kB. Rozměry obrazu by měly být 215 x 215 pixelů, s centrálními rozměry obrazu 94 x 94 pixelů.|
| Adresa URL domovské stránky   | Toto je přihlašovací adresa URL zadaná během registrace aplikace.|

### <a name="authentication"></a>Ověřování

| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adresa URL odhlášení      | Toto je adresa URL odhlášení jednotného odhlášení. Azure AD odešle požadavek na odhlášení na tuto adresu URL, když uživatel vymaže svou relaci s Azure AD pomocí jakékoli jiné registrované aplikace.|
| Podporované typy účtu  | Tento přepínač určuje, zda lze aplikaci používat více klientů. Obvykle to znamená, že externí organizace můžete použít vaši aplikaci registrací v jejich tenanta a udělení přístupu k datům jejich organizace.|
| Adresy URL pro přesměrování      | Přesměrování nebo odpověď, adresy URL jsou koncové body, kde Azure AD vrátí všechny tokeny, které vaše aplikace požaduje. U nativních aplikací je uživatel odeslán po úspěšné autorizaci. Azure AD zkontroluje, zda přesměrování URI aplikace dodává v požadavku OAuth 2.0 odpovídá jedné z registrovaných hodnot na portálu.|

### <a name="certificates-and-secrets"></a>Certifikáty a tajné klíče

| Pole           | Popis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tajné kódy klienta            | Můžete vytvořit tajné klíče klienta nebo klíče pro programový přístup k webovým rozhraním API zabezpečeným službou Azure AD bez jakékoli interakce s uživatelem. Na stránce **Nový tajný klíč klienta** zadejte popis klíče a datum vypršení platnosti a uložte pro generování klíče. Nezapomeňte jej uložit na bezpečném místě, protože k němu nebudete mít později přístup.             |

## <a name="next-steps"></a>Další kroky

[Správa aplikací pomocí služby Azure Active Directory](../manage-apps/what-is-application-management.md)
