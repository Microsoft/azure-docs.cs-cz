---
title: 'Rychlý Start: Konfigurace aplikace k vystavení webového rozhraní API | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak nakonfigurovat aplikaci, aby vystavila nové oprávnění, obor a roli k zpřístupnění aplikace klientským aplikacím.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830287"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Rychlý Start: Konfigurace aplikace k vystavení webového rozhraní API

Můžete vyvinout webové rozhraní API a zpřístupnit ho klientským aplikacím zveřejněním [oprávnění nebo oborů](developer-glossary.md#scopes) a [rolí](developer-glossary.md#roles). Správně nakonfigurované webové rozhraní API bude k dispozici stejně jako ostatní webová rozhraní API Microsoftu, včetně rozhraní Graph API a rozhraní API pro Office 365.

V tomto rychlém startu se dozvíte, jak nakonfigurovat aplikaci, aby vystavila nový obor, který zpřístupní klientským aplikacím.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dokončení [rychlého startu: registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md)

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Přihlášení k webu Azure Portal a výběr aplikace

Než budete moct nakonfigurovat aplikaci, postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak vyberte **Registrace aplikací**.
1. Vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat. Jakmile vyberete aplikaci, zobrazí se stránka **Přehled** neboli hlavní stránka registrace dané aplikace.
1. Zvolte metodu, kterou chcete ke zveřejnění nového oboru použít – uživatelské rozhraní nebo manifest aplikace:
    * [Zveřejnění nového oboru prostřednictvím uživatelského rozhraní](#expose-a-new-scope-through-the-ui)
    * [Zveřejnění nového oboru nebo role prostřednictvím manifestu aplikace](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Zveřejnění nového oboru prostřednictvím uživatelského rozhraní

[![Ukazuje, jak vystavit rozhraní API pomocí uživatelského rozhraní.](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

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

1. Volitelné Chcete-li potlačit zobrazení výzvy ke souhlasu uživatelů vaší aplikace s definovanými obory, můžete "předběžně autorizovat" klientská aplikace pro přístup k webovému rozhraní API. Měli byste předběžně autorizovat *jenom* ty klientské aplikace, kterým důvěřujete, protože uživatelé nebudou mít možnost odmítnout souhlas.
    1. V části **autorizované klientské aplikace**vyberte **Přidat klientskou aplikaci** .
    1. Zadejte **aplikaci (ID klienta)** klientské aplikace, kterou chcete předběžně autorizovat. Například u webové aplikace, kterou jste předtím zaregistrovali.
    1. V části **autorizované obory**vyberte obory, pro které chcete potlačit zobrazování výzev k vyjádření souhlasu, a pak vyberte **Přidat aplikaci**.

    Klientská aplikace je teď předem autorizovaná klientská aplikace (DPS) a uživatelé se při přihlašování k nim nebudou vyzváni k souhlasu.

1. Postupujte podle pokynů a [ověřte, že je webové rozhraní API zveřejněné ostatním aplikacím](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Zveřejnění nového oboru nebo role prostřednictvím manifestu aplikace

Manifest aplikace slouží jako mechanismus pro aktualizaci entity aplikace definující atributy registrace aplikace služby Azure AD.

[![Vystavení nového oboru pomocí kolekce oauth2Permissions v manifestu](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

K vystavení nového oboru úpravou manifestu aplikace:

1. Na stránce **Přehled** aplikace vyberte část **Manifest**. Otevře se editor manifestu na webovém základu, který vám umožní na webu Azure Portal manifest **Upravit**. Volitelně můžete vybrat **Stáhnout**, upravit manifest místně a potom ho **Nahrát** zpět do aplikace.

    Následující příklad ukazuje, jak v prostředku nebo rozhraní API zveřejnit nový obor `Employees.Read.All` přidáním následujícího elementu JSON do kolekce `oauth2Permissions`.

    Vygenerujte `id` hodnotu programově nebo pomocí nástroje pro generování identifikátoru GUID, jako je například [Guidgen](https://www.microsoft.com/download/details.aspx?id=55984).

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

1. Jakmile budete hotoví, klikněte na **Uložit**. Webové rozhraní API je teď nakonfigurované k použití jinými aplikacemi v adresáři.
1. Postupujte podle pokynů a [ověřte, že je webové rozhraní API zveřejněné ostatním aplikacím](#verify-the-web-api-is-exposed-to-other-applications).

Další informace o entitě aplikace a jejím schématu najdete v tématu Referenční dokumentace k typu prostředku [aplikace][ms-graph-application] Microsoft Graph.

Další informace o manifestu aplikace, včetně referenčního schématu, najdete v tématu [Principy manifestu aplikace Azure AD](reference-app-manifest.md).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Ověření, že je webové rozhraní API zveřejněné ostatním aplikacím

1. Vraťte se do tenanta služby Azure AD, vyberte **Registrace aplikací**a pak vyhledejte a vyberte klientskou aplikaci, kterou chcete nakonfigurovat.
1. Zopakujte postup popsaný v části [Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md).
1. Když se dostanete k kroku [výběru rozhraní API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis), vyberte prostředek (registrace aplikace webového rozhraní API).
    * Pokud jste vytvořili registraci aplikace webového rozhraní API pomocí Azure Portal, váš prostředek rozhraní API je uvedený na kartě **Moje rozhraní** API.
    * Pokud jste aplikaci Visual Studio povolili vytvoření registrace aplikace webového rozhraní API během vytváření projektu, je váš prostředek rozhraní API uveden v části **rozhraní API moje organizace používá** kartu.

Po výběru prostředku webového rozhraní API byste měli vidět nový rozsah dostupný pro žádosti o oprávnění klienta.

## <a name="using-the-exposed-scopes"></a>Použití vystavených oborů

Jakmile je klient vhodně nakonfigurovaný s oprávněními pro přístup k webovému rozhraní API, může ho Azure AD vystavit přístupový token OAuth 2,0. Když klient volá webové rozhraní API, prezentuje přístupový token, který má `scp` deklaraci identity () nastavenou na oprávnění požadovaná při registraci aplikace.

Další obory můžete podle potřeby zveřejnit později. Vezměte v úvahu, že webové rozhraní API může zveřejnit více oborů přidružených k celé řadě různých funkcí. Váš prostředek může za běhu řídit přístup k webovému rozhraní API tak, že bude vyhodnocovat deklaraci/deklarace identity oboru (`scp`) v přijatém přístupovém tokenu OAuth 2.0.

V aplikacích je úplná hodnota oboru zřetězením **identifikátoru URI ID aplikace** webového rozhraní API (prostředku) a **názvu oboru**.

Pokud je například identifikátor URI ID aplikace webového rozhraní API `https://contoso.com/api` a název vašeho oboru je `Employees.Read.All` , úplný rozsah je:

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili své webové rozhraní API tak, že nakonfigurujete jeho obory, nakonfigurujte registraci klientské aplikace s oprávněním pro přístup k těmto oborům.

> [!div class="nextstepaction"]
> [Konfigurace registrace aplikace pro přístup k webovému rozhraní API](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
