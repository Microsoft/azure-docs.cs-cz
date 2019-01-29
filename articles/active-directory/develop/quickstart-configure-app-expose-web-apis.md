---
title: Konfigurace aplikace pro zveřejnění webových rozhraní API (Preview) | Azure
description: Zjistěte, jak nakonfigurovat aplikaci pro zveřejnění nového oprávnění nebo oboru a role a tím ji zpřístupnit klientským aplikacím.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 3f38162fd4d95127ddf7797638bc9af21e6aaf20
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095690"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis-preview"></a>Rychlý start: Konfigurace aplikace k vystavení webové rozhraní API (Preview)

Můžete vyvinout webové rozhraní API a zpřístupnit ho klientským aplikacím zveřejněním [oprávnění nebo oborů](developer-glossary.md#scopes) a [rolí](developer-glossary.md#roles). Správně nakonfigurované webové rozhraní API bude k dispozici stejně jako ostatní webová rozhraní API Microsoftu, včetně rozhraní Graph API a rozhraní API pro Office 365.

V tomto rychlém startu se dozvíte, jak nakonfigurovat aplikaci pro zveřejnění nového oboru a tím ji zpřístupnit klientským aplikacím.

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte splnit následující požadavky:

* Přečtěte si o podporovaných [oprávněních a souhlasu](v2-permissions-and-consent.md), kterým je důležité rozumět při vytváření aplikací, které budou používat jiní uživatelé či jiné aplikace.
* Máte tenanta, ke kterému jsou zaregistrované aplikace.
  * Pokud nemáte žádné zaregistrované aplikace, [přečtěte si o registraci aplikací na platformě Microsoft Identity Platform](quickstart-register-app.md).
* Na webu Azure Portal jste se přihlásili k prostředí pro registrace aplikací ve verzi Preview. Kroky v tomto rychlém startu odpovídají novému uživatelskému rozhraní a fungují pouze v případě, že jste se přihlásili k prostředí ve verzi Preview.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Přihlášení k webu Azure Portal a výběr aplikace

Než budete moct nakonfigurovat aplikaci, postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak **Registrace aplikací (Preview)**.
1. Vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat. Jakmile vyberete aplikaci, zobrazí se stránka **Přehled** neboli hlavní stránka registrace dané aplikace.
1. Zvolte metodu, kterou chcete ke zveřejnění nového oboru použít – uživatelské rozhraní nebo manifest aplikace:
    * [Zveřejnění nového oboru prostřednictvím uživatelského rozhraní](#expose-a-new-scope-through-the-ui)
    * [Zveřejnění nového oboru nebo role prostřednictvím manifestu aplikace](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Zveřejnění nového oboru prostřednictvím uživatelského rozhraní

[![Zveřejnění rozhraní API pomocí uživatelského rozhraní](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Zveřejnění nového oboru prostřednictvím uživatelského rozhraní:

1. Na stránce **Přehled** aplikace vyberte část **Zveřejnit rozhraní API**.

1. Vyberte **Přidat obor**.

1. Pokud jste nenastavili **Identifikátor URI ID aplikace**, zobrazí se výzva k jeho zadání. Zadejte identifikátor URI ID vaší aplikace nebo použijte uvedený identifikátor a pak vyberte **Uložit a pokračovat**.

1. Jakmile se zobrazí stránka **Přidat obor**, zadejte informace o vašem oboru:

    | Pole | Popis |
    |-------|-------------|
    | **Název oboru** | Zadejte smysluplný název oboru.<br><br>Například, `Employees.Read.All`. |
    | **Kdo může vyjádřit souhlas** | Vyberte, jestli tento obor můžou odsouhlasit uživatelé, nebo jestli se vyžaduje souhlas správce. Pro oprávnění s vyššími privilegii vyberte možnost **Jen správci**. |
    | **Zobrazovaný název souhlasu správce** | Zadejte smysluplný popis oboru, který se zobrazí správcům.<br><br>Například `Read-only access to Employee records`. |
    | **Popis souhlasu správce** | Zadejte smysluplný popis oboru, který se zobrazí správcům.<br><br>Například `Allow the application to have read-only access to all Employee data.`. |

    Pokud váš obor můžou odsouhlasit uživatelé, přidejte také hodnoty pro následující pole:

    | Pole | Popis |
    |-------|-------------|
    | **Zobrazovaný název souhlasu uživatele** | Zadejte smysluplný název oboru, který se zobrazí uživatelům.<br><br>Například `Read-only access to your Employee records`. |
    | **Popis souhlasu uživatele** | Zadejte smysluplný popis oboru, který se zobrazí uživatelům.<br><br>Například `Allow the application to have read-only access to your Employee data.`. |

1. Nastavte **Stav** a jakmile budete hotovi, vyberte **Přidat obor**.

1. Postupujte podle pokynů a [ověřte, že je webové rozhraní API zveřejněné ostatním aplikacím](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Zveřejnění nového oboru nebo role prostřednictvím manifestu aplikace

[![Zveřejnění nového oboru pomocí kolekce oauth2Permissions v manifestu](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Zveřejnění nového oboru prostřednictvím manifestu aplikace:

1. Na stránce **Přehled** aplikace vyberte část **Manifest**. Otevře se editor manifestu na webovém základu, který vám umožní na webu Azure Portal manifest **Upravit**. Volitelně můžete vybrat **Stáhnout**, upravit manifest místně a potom ho **Nahrát** zpět do aplikace.
    
    Následující příklad ukazuje, jak v prostředku nebo rozhraní API zveřejnit nový obor `Employees.Read.All` přidáním následujícího elementu JSON do kolekce `oauth2Permissions`.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

  > [!NOTE]
  > Hodnota `id` se musí vygenerovat programově nebo pomocí nástroje pro generování globálně jedinečných identifikátorů (GUID), jako je [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). Hodnota `id` představuje jedinečný identifikátor pro obor zveřejněný webovým rozhraním API. Jakmile se klient správně nakonfiguruje pomocí oprávnění pro přístup k webovému rozhraní API, Azure AD vydá přístupový token OAuth 2.0. Když klient volá webové rozhraní API, představí přístupový token, jehož deklarace identity oboru je nastavená na oprávnění vyžádaná v registraci aplikace.
  >
  > Další obory můžete podle potřeby zveřejnit později. Vezměte v úvahu, že webové rozhraní API může zveřejnit více oborů přidružených k celé řadě různých funkcí. Váš prostředek může za běhu řídit přístup k webovému rozhraní API tak, že bude vyhodnocovat deklaraci/deklarace identity oboru (`scp`) v přijatém přístupovém tokenu OAuth 2.0.

1. Jakmile budete hotoví, klikněte na **Uložit**. Webové rozhraní API je teď nakonfigurované k použití jinými aplikacemi v adresáři.
1. Postupujte podle pokynů a [ověřte, že je webové rozhraní API zveřejněné ostatním aplikacím](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Ověření, že je webové rozhraní API zveřejněné ostatním aplikacím

1. Vraťte se do svého tenanta Azure AD, vyberte **Registrace aplikací** a vyhledejte a vyberte klientskou aplikaci, kterou chcete nakonfigurovat.
1. Zopakujte postup popsaný v části [Konfigurace klientské aplikace pro přístup k webovým rozhraním API](#configure-a-client-application-to-access-web-apis).
1. Když se dostanete ke kroku **Výběr rozhraní API**, vyberte váš prostředek. Měl by se zobrazit nový obor, který je k dispozici pro žádosti o oprávnění klientů.

## <a name="more-on-the-application-manifest"></a>Další informace o manifestu aplikace

Manifest aplikace slouží jako mechanismus pro aktualizaci aplikační entity, která definuje všechny atributy konfigurace identity aplikace Azure AD. Další informace o aplikační entitě a jejím schéma najdete v [dokumentaci aplikační entity rozhraní Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Tento článek obsahuje úplné referenční informace o členech aplikační entity používaných k určení oprávnění u rozhraní API, včetně:  

* Člena appRoles, který je kolekcí entit [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) používaných k definování [oprávnění aplikace](developer-glossary.md#permissions) u webového rozhraní API.
* Člena oauth2Permissions, který je kolekcí entit [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) používaných k definování [delegovaných oprávnění](developer-glossary.md#permissions) u webového rozhraní API.

Další informace o obecných konceptech manifestu aplikace najdete v tématu [Vysvětlení manifestu aplikace Azure Active Directory](reference-app-manifest.md).

## <a name="next-steps"></a>Další postup

Další informace najdete v těchto rychlých startech souvisejících se správou aplikací:

* [Registrace aplikace na platformě Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md)
* [Úprava účtů podporovaných aplikací](quickstart-modify-supported-accounts.md)
* [Odebrání aplikace zaregistrované na platformě Microsoft Identity Platform](quickstart-remove-app.md)

Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).

Další informace o pokynech pro branding, kterými byste se měli řídit při vývoji aplikací s využitím Azure Active Directory, najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).
