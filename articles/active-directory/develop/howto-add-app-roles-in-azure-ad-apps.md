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
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: fce963bd9ffdc6f768d7b3de4a9e4870add06136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104242"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>Postupy: Přidání rolí aplikace do aplikace a jejich přijetí v tokenu

Řízení přístupu na základě role (RBAC) je oblíbeným mechanismem pro vynucování autorizace v aplikacích. Při použití RBAC správce udělí oprávnění k rolím, nikoli jednotlivým uživatelům nebo skupinám. Správce pak může přiřadit role různým uživatelům a skupinám a určovat, kdo má přístup k jakému obsahu a funkcím.

Díky použití RBAC s aplikačními rolemi a deklaracemi rolí můžou vývojáři bezpečně vymáhat autorizaci ve svých aplikacích s menší námahou.

Dalším přístupem je použití skupin Azure AD a deklarací skupin, jak je znázorněno v ukázce kódu [Active-Directory-aspnetcore-WebApp-openidconnect-v2](https://aka.ms/groupssample) na GitHubu. Role skupin a aplikací Azure AD se vzájemně nevylučují. je možné je použít společně pro zajištění ještě jemnějšího řízení přístupu.

## <a name="declare-roles-for-an-application"></a>Deklarace rolí pro aplikaci

Role aplikace definujete pomocí [Azure Portal](https://portal.azure.com). Role aplikací se obvykle definují v registraci aplikace, která představuje službu, aplikaci nebo rozhraní API. Když se uživatel přihlásí k aplikaci, Azure AD vygeneruje `roles` deklaraci identity pro každou roli, které uživatel nebo instanční objekt získal individuálně pro uživatele, a z jejich členství ve skupinách. Dá se použít k implementaci autorizace založené na deklaracích. Role aplikací se dají přiřadit [uživateli nebo skupině uživatelů](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app). Role aplikací lze také přiřadit instančnímu objektu pro jinou aplikaci nebo [instančnímu objektu spravované identity](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md).

> [!IMPORTANT]
> Když v tuto chvíli přidáte instanční objekt do skupiny a potom k této skupině přiřadíte roli aplikace, Azure AD tuto deklaraci nepřidá k tokenům, které vystaví `roles` IT problémy.

Existují dva způsoby, jak deklarovat aplikační role pomocí Azure Portal:

* [Uživatelské rozhraní rolí aplikace](#app-roles-ui--preview) | Tisk
* [Editor manifestu aplikace](#app-manifest-editor)

Počet rolí, které přidáváte, se počítá s omezeními pro manifest aplikace vydanými Azure Active Directory. Informace o těchto omezeních najdete v části  [omezení manifestu](./reference-app-manifest.md#manifest-limits) [Azure Active Directory odkaz na manifest aplikace](reference-app-manifest.md).

### <a name="app-roles-ui--preview"></a>Uživatelské rozhraní rolí aplikace | Tisk

> [!IMPORTANT]
> Funkce uživatelského rozhraní portálu rolí aplikace [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Vytvoření aplikační role pomocí uživatelského rozhraní Azure Portal:

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. V horní nabídce vyberte možnost **adresář a filtr předplatného** a potom zvolte klienta Azure Active Directory, který obsahuje registraci aplikace, do které chcete přidat roli aplikace.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací** a pak vyberte aplikaci, ve které chcete role aplikace definovat.
1. Vybrat **role aplikace | Zobrazte náhled** a pak vyberte **vytvořit roli aplikace**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Podokno rolí aplikace registrace aplikace v Azure Portal":::
1. V podokně **vytvořit roli aplikace** zadejte nastavení pro roli. V tabulce následující po obrázku jsou popsána jednotlivá nastavení a jejich parametry.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Kontextové podokno pro registraci aplikací pro vytváření rolí v Azure Portal":::

    | Pole | Popis | Příklad |
    |-------|-------------|---------|
    | **Zobrazovaný název** | Zobrazovaný název role aplikace, který se zobrazí v prostředí pro vyjádření souhlasu správce a přiřazování aplikací Tato hodnota může obsahovat mezery. | `Survey Writer` |
    | **Povolené typy členů** | Určuje, jestli tato role aplikace může být přiřazená uživatelům, aplikacím nebo oběma.<br/><br/>Pokud je k dispozici `applications` , role aplikací se zobrazí jako oprávnění aplikace v oddílu **Spravovat** registraci aplikace > **oprávnění rozhraní api > přidat oprávnění > moje rozhraní API > zvolit oprávnění rozhraní API > aplikace**. | `Users/Groups` |
    | **Hodnota** | Určuje hodnotu deklarace identity rolí, kterou by měla aplikace očekávat v tokenu. Hodnota by měla přesně odpovídat řetězci odkazovanému v kódu aplikace. Hodnota nesmí obsahovat mezery. | `Survey.Create` |
    | **Popis** | Podrobnější popis role aplikace zobrazený během přiřazování aplikací pro správu a jejich souhlasu. | `Writers can create surveys.` |
    | **Chcete tuto roli aplikace povolit?** | Určuje, jestli je povolená role aplikace. Pokud chcete odstranit roli aplikace, zrušte zaškrtnutí políčka a před provedením operace odstranění tuto změnu použijte. | *Kontrolovaný* |

1. Výběrem možnosti **Použít** změny uložte.

### <a name="app-manifest-editor"></a>Editor manifestu aplikace

Chcete-li přidat role úpravou manifestu přímo:

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. V horní nabídce vyberte možnost **adresář a filtr předplatného** a potom zvolte klienta Azure Active Directory, který obsahuje registraci aplikace, do které chcete přidat roli aplikace.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací** a pak vyberte aplikaci, ve které chcete role aplikace definovat.
1. Znovu v části **Spravovat** vyberte **manifest**.
1. Upravte manifest aplikace tak, že vyhledáte `appRoles` nastavení a přidáte aplikační role. Můžete definovat aplikační role, které cílí `users` na, `applications` nebo obojí. Následující fragmenty kódu JSON ukazují příklady obou.
1. Uložte manifest.

Každá definice role aplikace v manifestu musí mít jedinečný identifikátor GUID pro svou `id` hodnotu.

`value`Vlastnost každé definice role aplikace by měla přesně odpovídat řetězcům, které jsou používány v kódu v aplikaci. `value`Vlastnost nemůže obsahovat mezery. Pokud k tomu dojde, při ukládání manifestu dojde k chybě.

#### <a name="example-user-app-role"></a>Příklad: role uživatelské aplikace

Tento příklad definuje aplikační roli s názvem `Writer` , kterou můžete přiřadit `User` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>Příklad: role Aplikační aplikace

Pokud je k dispozici `applications` , role aplikací se zobrazí jako oprávnění aplikace v oddílu **Spravovat** registraci aplikace > **oprávnění rozhraní api > přidat oprávnění > moje rozhraní API > zvolit oprávnění rozhraní API > aplikace**.

Tento příklad ukazuje aplikační roli, která je cílena na `Application` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>Přiřazení uživatelů a skupin k rolím

Po přidání rolí aplikace do aplikace můžete k rolím přiřadit uživatele a skupiny. Přiřazení uživatelů a skupin k rolím je možné provést prostřednictvím uživatelského rozhraní portálu nebo programově pomocí [Microsoft Graph](/graph/api/user-post-approleassignments). Když se uživatelé, kteří jsou přiřazeni k různým rolím aplikace, přihlásí k aplikaci, jejich tokeny budou mít přiřazené role v `roles` deklaraci identity.

Přiřazení uživatelů a skupin k rolím pomocí Azure Portal:

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. V **Azure Active Directory** v navigační nabídce vlevo vyberte **podnikové aplikace** .
1. Výběrem **všech aplikací** zobrazíte seznam všech aplikací. Pokud se vaše aplikace v seznamu nezobrazí, použijte filtry v horní části seznamu **všechny aplikace** , abyste seznam omezili, nebo přejděte dolů na seznam a vyhledejte svoji aplikaci.
1. Vyberte aplikaci, ve které chcete rolím přiřadit uživatele nebo skupinu zabezpečení.
1. Pod možností **Spravovat** vyberte **Uživatelé a skupiny**.
1. Výběrem **Přidat uživatele** otevřete podokno **Přidat přiřazení** .
1. V podokně **Přidat přiřazení** vyberte selektor **Uživatelé a skupiny** . Zobrazí se seznam uživatelů a skupin zabezpečení. Můžete vyhledat určitého uživatele nebo skupinu a vybrat více uživatelů a skupin, které se zobrazí v seznamu.
1. Po výběru uživatelů a skupin vyberte tlačítko **Vybrat** , abyste mohli pokračovat.
1. V podokně **Přidat přiřazení** vyberte **Vybrat roli** . Zobrazí se všechny role, které jste definovali pro danou aplikaci.
1. Zvolte roli a vyberte tlačítko **Vybrat** .
1. Kliknutím na tlačítko **přiřadit** dokončíte přiřazení uživatelů a skupin do aplikace.

Ověřte, že se uživatelé a skupiny, které jste přidali, zobrazují v seznamu **Uživatelé a skupiny** .

## <a name="assign-app-roles-to-applications"></a>Přiřazení aplikačních rolí k aplikacím

Po přidání rolí aplikace do aplikace můžete klientské aplikaci přiřadit roli aplikace pomocí Azure Portal nebo programově pomocí [Microsoft Graph](/graph/api/user-post-approleassignments).

Při přiřazování rolí aplikací k aplikaci vytvoříte *oprávnění aplikace*. Oprávnění aplikace jsou obvykle používána aplikacemi démon nebo back-end službami, které potřebují ověřovat a autorizovat volání rozhraní API jako sebe bez interakce uživatele.

Přiřazení rolí aplikace k aplikaci pomocí Azure Portal:

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. V **Azure Active Directory** v navigační nabídce vlevo vyberte **Registrace aplikací** .
1. Výběrem **všech aplikací** zobrazíte seznam všech aplikací. Pokud se vaše aplikace v seznamu nezobrazí, použijte filtry v horní části seznamu **všechny aplikace** , abyste seznam omezili, nebo přejděte dolů na seznam a vyhledejte svoji aplikaci.
1. Vyberte aplikaci, ke které chcete přiřadit roli aplikace.
1. Vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**.
1. Vyberte kartu **Moje rozhraní API** a potom vyberte aplikaci, pro kterou jste definovali role aplikace.
1. Vyberte **oprávnění aplikace**.
1. Vyberte role, které chcete přiřadit.
1. Kliknutím na tlačítko **Přidat oprávnění** dokončete přidání rolí.

Nově přidané role by se měly zobrazit v podokně **oprávnění API** pro registraci vaší aplikace.

#### <a name="grant-admin-consent"></a>Udělit souhlas správce

Vzhledem k tomu, že se jedná o *oprávnění aplikace*, nedelegovaná oprávnění, správce musí udělit souhlas s použitím rolí aplikace přiřazených k aplikaci.

1. V podokně **oprávnění rozhraní API** pro registraci aplikace vyberte **\<tenant name\> udělit souhlas správce pro**.
1. Po zobrazení výzvy pro udělení souhlasu pro požadovaná oprávnění vyberte **Ano** .

Sloupec **status** by měl odrážet souhlas **udělený pro \<tenant name\>**.

## <a name="use-app-roles-in-your-web-api"></a>Použití aplikačních rolí ve vašem webovém rozhraní API

Po definování rolí aplikace a jejich přiřazení k uživateli, skupině nebo aplikaci je dalším krokem přidání kódu do webového rozhraní API, které při volání rozhraní API kontroluje tyto role. To znamená, že když klientská aplikace požaduje operaci rozhraní API, kterou jste určili, vyžaduje autorizaci, kód rozhraní API musí ověřit, zda jsou obory v přístupovém tokenu prezentovaném ve volání klientské aplikace.

Informace o tom, jak přidat autorizaci do webového rozhraní API, najdete v tématu [chráněné webové rozhraní API: ověření oborů a aplikačních rolí](scenario-protected-web-api-verification-scope-app-roles.md).

## <a name="app-roles-vs-groups"></a>Aplikační role vs. skupiny

I když můžete k autorizaci použít aplikační role nebo skupiny, může to mít vliv na hlavní rozdíly, které se rozhodnete použít pro svůj scénář.

| Aplikační role                                                                          | Skupiny                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Jsou specifické pro aplikaci a jsou definovány v registraci aplikace. Přesouvá s aplikací. | Nejsou specifické pro aplikaci, ale pro tenanta Azure AD. |
| Role aplikací se při odebrání registrace do aplikace odeberou.                      | Skupiny zůstávají beze změny i v případě, že je aplikace odebrána.            |
| Zadáno v `roles` deklaraci identity.                                                     | Poskytnuto v `groups` deklaraci identity.                                 |

Vývojáři můžou použít aplikační role k řízení toho, jestli se uživatel může přihlásit k aplikaci nebo aplikace může získat přístupový token pro webové rozhraní API. Pro rozšiřování tohoto řízení zabezpečení na skupiny můžou vývojáři a správci přiřadit skupiny zabezpečení i k rolím aplikací.

Role aplikací jsou upřednostňovány vývojáři, když chtějí popsat a řídit parametry autorizace ve své aplikaci sami. Například aplikace, která používá skupiny pro autorizaci, se přeruší v dalším tenantovi, protože ID skupiny i název můžou být odlišné. Aplikace, která používá role aplikace, zůstane v bezpečí. Ve skutečnosti se přiřazování skupin k aplikačním rolím v oblíbených aplikacích SaaS ze stejných důvodů.

## <a name="next-steps"></a>Další kroky

Další informace o aplikačních rolích najdete v následujících zdrojích.

* Vzorový kód na GitHubu
  * [Přidání autorizace pomocí skupin a deklarací skupin do ASP.NET Core webové aplikace](https://aka.ms/groupssample)
  * [Úhlová aplikace s jednoduchou stránkou (SPA) – volání webového rozhraní API .NET Core a používání aplikačních rolí a skupin zabezpečení](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* Referenční dokumentace
  * [Manifest aplikace Azure AD](./reference-app-manifest.md)
  * [Přístupové tokeny Azure AD](access-tokens.md)
  * [Tokeny Azure AD ID](id-tokens.md)
  * [Poskytněte do aplikace volitelné deklarace identity.](active-directory-optional-claims.md)
* Video: [implementace autorizace ve svých aplikacích s využitím platformy Microsoft Identity Platform](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
