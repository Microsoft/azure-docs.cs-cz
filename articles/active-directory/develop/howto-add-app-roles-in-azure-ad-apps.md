---
title: Přidat role aplikace a získat je od tokenu | Azure
titleSuffix: Microsoft identity platform
description: Naučte se přidávat role aplikací do aplikace registrované v Azure Active Directory, přiřazovat k těmto rolím uživatele a skupiny a přijímat je v deklaraci identity role v tokenu.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706009"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Postupy: Přidání rolí aplikace do aplikace a jejich přijetí v tokenu

Řízení přístupu na základě role (RBAC) je oblíbeným mechanismem pro vynucování autorizace v aplikacích. Při použití RBAC správce udělí oprávnění k rolím, nikoli jednotlivým uživatelům nebo skupinám. Správce pak může přiřadit role různým uživatelům a skupinám a určovat, kdo má přístup k jakému obsahu a funkcím.

Díky použití RBAC s aplikačními rolemi a deklaracemi rolí můžou vývojáři bezpečně vymáhat autorizaci ve svých aplikacích s menším úsilím na jejich straně.

Dalším přístupem je použití skupin Azure AD a deklarací skupin, jak je znázorněno v ukázce kódu [WebApp-GroupClaims-dotnet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) na GitHubu. Role skupin a aplikací Azure AD se vzájemně nevylučují. je možné je použít společně pro zajištění ještě jemnějšího řízení přístupu.

## <a name="declare-roles-for-an-application"></a>Deklarace rolí pro aplikaci

Aplikační role jsou definovány v [Azure Portal](https://portal.azure.com).  Když se uživatel přihlásí do aplikace, Azure AD vygeneruje `roles` deklaraci identity pro každou roli, které uživatel získal individuálně pro uživatele, a z jejich členství ve skupinách.  Přiřazení uživatelů a skupin k rolím je možné provést prostřednictvím uživatelského rozhraní portálu nebo programově pomocí [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

Vytvoření aplikační role:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte možnost **adresář a filtr předplatného** a potom zvolte klienta Azure Active Directory, který obsahuje registraci aplikace, do které chcete přidat roli aplikace.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat**vyberte **Registrace aplikací**a pak vyberte aplikaci, ve které chcete role aplikace definovat.
1. Vybrat **role aplikace | Zobrazte náhled**a pak vyberte **vytvořit roli aplikace**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Podokno rolí aplikace registrace aplikace v Azure Portal":::
1. V podokně **vytvořit roli aplikace** zadejte nastavení pro roli. V tabulce následující po obrázku jsou popsána jednotlivá nastavení a jejich parametry.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Kontextové podokno pro registraci aplikací pro vytváření rolí v Azure Portal":::

    | Pole | Popis | Příklad |
    |-------|-------------|---------|
    | **Zobrazovaný název** | Zobrazovaný název role aplikace, který se zobrazí v prostředí pro vyjádření souhlasu správce a přiřazování aplikací Tato hodnota může obsahovat mezery.  | `Survey Writer` |
    | **Povolené typy členů** | Určuje, jestli tato role aplikace může být přiřazená uživatelům, aplikacím nebo oběma. | `Users/Groups` |
    | **Hodnota** | Určuje hodnotu deklarace identity rolí, kterou by měla aplikace očekávat v tokenu. Hodnota by měla přesně odpovídat řetězci odkazovanému v kódu aplikace. Hodnota nesmí obsahovat mezery. | `Survey.Create` |
    | **Popis** | Podrobnější popis role aplikace zobrazený během přiřazování aplikací pro správu a jejich souhlasu. | `Writers can create surveys.` |
    | **Chcete tuto roli aplikace povolit?** | Určuje, jestli je povolená role aplikace. Pokud chcete odstranit roli aplikace, zrušte zaškrtnutí políčka a před provedením operace odstranění tuto změnu použijte. | *Kontrolovaný* |

1. Výběrem možnosti **Použít** změny uložte.

> [!NOTE]
> Počet rolí, které přidáte, počítá směrem k omezením definovaným pro manifest aplikace. Informace o těchto omezeních najdete v části  [omezení manifestu](./reference-app-manifest.md#manifest-limits) [Azure Active Directory odkaz na manifest aplikace](reference-app-manifest.md).

## <a name="assign-users-and-groups-to-roles"></a>Přiřazení uživatelů a skupin k rolím

Po přidání rolí aplikace do aplikace můžete k rolím přiřadit uživatele a skupiny.

1. V **Azure Active Directory** v Azure Portal vyberte v levé navigační nabídce možnost **podnikové aplikace** .
1. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

     Pokud se vaše aplikace v seznamu nezobrazí, použijte filtry v horní části seznamu **všechny aplikace** , abyste seznam omezili, nebo přejděte dolů na seznam a vyhledejte svoji aplikaci.

1. Vyberte aplikaci, ve které chcete rolím přiřadit uživatele nebo skupinu zabezpečení.
1. Pod možností **Spravovat** vyberte **Uživatelé a skupiny**.
1. Výběrem **Přidat uživatele** otevřete podokno **Přidat přiřazení** .
1. V podokně **Přidat přiřazení** vyberte selektor **Uživatelé a skupiny** .

     Zobrazí se seznam uživatelů a skupin zabezpečení. Můžete vyhledat určitého uživatele nebo skupinu a vybrat více uživatelů a skupin, které se zobrazí v seznamu.

1. Po výběru uživatelů a skupin vyberte tlačítko **Vybrat** , abyste mohli pokračovat.
1. V podokně **Přidat přiřazení** vyberte **Vybrat roli** . Zobrazí se všechny role, které jste definovali pro danou aplikaci.
1. Zvolte roli a vyberte tlačítko **Vybrat** .
1. Kliknutím na tlačítko **přiřadit** dokončíte přiřazení uživatelů a skupin do aplikace.
1. Ověřte, že se uživatelé a skupiny, které jste přidali, zobrazují v seznamu **Uživatelé a skupiny** .

## <a name="receive-roles-in-tokens"></a>Přijímání rolí v tokenech

Když se uživatelé, kteří jsou přiřazeni k různým rolím aplikace, přihlásí k aplikaci, jejich tokeny budou mít přiřazené role v `roles` deklaraci identity.

## <a name="web-api-application-permissions"></a>Oprávnění aplikace webového rozhraní API

Můžete definovat aplikační role, které cílí na **uživatele nebo skupiny**, **aplikace**nebo **obojí**. Když vyberete **aplikace** nebo **obojí**, v **oprávněních rozhraní API**klientské aplikace se zobrazí role aplikace jako oprávnění aplikace.

Chcete-li vybrat oprávnění aplikace v registraci klientské aplikace poté, co jste definovali roli, která cílí na **aplikace** nebo **obojí**:

1. V **Azure Active Directory** v Azure Portal vyberte **Registrace aplikací**a pak vyberte registraci klientské aplikace.
1. V části **Spravovat**vyberte **oprávnění rozhraní API**.
1. Vyberte **Přidat oprávnění**  >  **Moje rozhraní API**.
1. Vyberte aplikaci, do které jste přidali aplikační roli, a pak vyberte **oprávnění aplikace**.

## <a name="next-steps"></a>Další kroky

Další informace o aplikačních rolích najdete v následujících zdrojích informací:

- Ukázka kódu: [Přidání autorizace pomocí rolí aplikace & deklaracích rolí do webové aplikace ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (GitHub)
- Video: [implementace autorizace ve svých aplikacích s využitím platformy Microsoft Identity Platform ](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
- [Manifest aplikace Azure Active Directory](./reference-app-manifest.md)
- [Přístupové tokeny Azure AD](access-tokens.md)
- [Služba Azure AD `id_tokens`](id-tokens.md)
