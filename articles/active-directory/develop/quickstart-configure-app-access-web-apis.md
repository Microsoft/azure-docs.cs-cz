---
title: 'Rychlý Start: Konfigurace aplikace pro přístup k webovému rozhraní API | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu nakonfigurujete registraci aplikace představující webové rozhraní API na platformě Microsoft identity, aby bylo možné klientským aplikacím povolit vymezený přístup k prostředkům (oprávnění).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 0b064e8491b5d4fa988e9f476143cc0711e3934a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651370"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Rychlý Start: Konfigurace klientské aplikace pro přístup k webovému rozhraní API

V tomto rychlém startu poskytnete klientskou aplikaci registrovanou s platformou Microsoft identity s vymezeným oborem, přístup založený na oprávněních k vlastnímu webovému rozhraní API. Také zadáte klientským aplikacím přístup k Microsoft Graph.

Když v registraci klientské aplikace zadáte obory webového rozhraní API, klientská aplikace může získat přístupový token obsahující tyto obory z platformy Microsoft identity. Ve svém kódu může webové rozhraní API poskytovat přístup k prostředkům na základě oprávnění, které je založené na oborech nalezených v přístupovém tokenu.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným – [vytvořit účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Dokončení [rychlého startu: registrace aplikace](quickstart-register-app.md)
* Dokončení [rychlého startu: Konfigurace aplikace k vystavení webového rozhraní API](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>Přidání oprávnění pro přístup k webovému rozhraní API

V prvním scénáři udělíte klientské aplikaci přístup k vlastnímu webovému rozhraní API, které byste měli zaregistrovat jako součást požadavků. Pokud ještě nemáte registrovanou aplikaci klienta i webové rozhraní API, proveďte kroky uvedené v článcích o těchto dvou [požadavcích](#prerequisites) .

Tento diagram znázorňuje, jak se obě registrace dvou aplikací vztahují k sobě. V této části přidáte oprávnění k registraci klientské aplikace.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="Spojnicový diagram znázorňující webové rozhraní API s vystavenými obory na pravé straně a klientskou aplikací na levé straně s těmito obory vybranými jako oprávnění" border="false":::

Po registraci klientské aplikace i webového rozhraní API a zpřístupnění rozhraní API vytvořením oborů můžete nakonfigurovat oprávnění klienta k rozhraní API pomocí následujících kroků:

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Pokud máte přístup k více klientům, vyberte v horní nabídce možnost **adresář a filtr předplatného** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: a vyberte klienta, který obsahuje registraci klientské aplikace.
1. Vyberte **Azure Active Directory**  >  **Registrace aplikací** a pak vyberte klientskou aplikaci (*nikoli* vaše webové rozhraní API).
1. Vyberte **oprávnění API**  >  **Přidat oprávnění**  >  **Moje rozhraní API**.
1. Vyberte webové rozhraní API, které jste zaregistrovali v rámci požadavků.

    Ve výchozím nastavení je vybraná možnost **delegovaná oprávnění** . Delegovaná oprávnění jsou vhodná pro klientské aplikace, které přistupují k webovému rozhraní API jako přihlášeného uživatele a jejichž přístup se má omezit na oprávnění, která jste vybrali v dalším kroku. Pro tento příklad nechejte vybraná **delegovaná oprávnění** .

    **Oprávnění aplikací** jsou pro aplikace typu Service-nebo démona, které potřebují přístup k webovému rozhraní API sami, bez zásahu uživatele pro přihlášení nebo souhlas. Pokud jste nedefinovali aplikační role pro webové rozhraní API, tato možnost je zakázaná.
1. V části **vybrat oprávnění** rozbalte prostředek, jehož obory jste definovali pro vaše webové rozhraní API, a vyberte oprávnění, která má klientská aplikace mít jménem přihlášeného uživatele.

    Pokud jste použili příklady názvů oborů uvedených v předchozím rychlém startu, měli byste vidět **Employees. Read. All** a **Employees. Write. All**.
    Vyberte **Employees. Read. All** nebo jiné oprávnění, které jste mohli vytvořit při dokončování požadavků.
1. Chcete-li dokončit proces, vyberte možnost **Přidat oprávnění** .

Po přidání oprávnění do rozhraní API by se měla zobrazit vybraná oprávnění v části **konfigurovaná oprávnění**. Následující obrázek ukazuje příklad *zaměstnanci. Read. všechna* delegovaná oprávnění přidaná do registrace klientské aplikace.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Podokno konfigurovaná oprávnění v Azure Portal zobrazující nově přidané oprávnění":::

Můžete si také všimnout, že *uživatel. oprávnění ke čtení* pro rozhraní Microsoft Graph API. Toto oprávnění se přidá automaticky při registraci aplikace v Azure Portal.

## <a name="add-permissions-to-access-microsoft-graph"></a>Přidání oprávnění pro přístup k Microsoft Graph

Kromě přístupu k vlastnímu webovému rozhraní API jménem přihlášeného uživatele může vaše aplikace také potřebovat přístup k datům uživatele (nebo jiným) uloženým v Microsoft Graph. Nebo je možné, že máte aplikaci služby nebo démona, která potřebuje přístup k Microsoft Graph sám sebe, a provádí operace bez zásahu uživatele.

### <a name="delegated-permission-to-microsoft-graph"></a>Delegované oprávnění pro Microsoft Graph

Nakonfigurujte delegované oprávnění na Microsoft Graph, aby klientská aplikace mohla provádět operace jménem přihlášeného uživatele, například číst e-maily nebo upravovat svůj profil. Ve výchozím nastavení se uživatelům klientské aplikace zobrazí dotaz, že se přihlásí k souhlasu delegovaných oprávnění, která jste pro něj nakonfigurovali.

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Pokud máte přístup k více klientům, vyberte v horní nabídce možnost **adresář a filtr předplatného** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: a vyberte klienta, který obsahuje registraci klientské aplikace.
1. Vyberte možnost **Azure Active Directory**  >  **Registrace aplikací** a pak vyberte klientskou aplikaci.
1. Vyberte **oprávnění API**  >  **Přidat**  >  **Microsoft Graph** oprávnění.
1. Vyberte **delegovaná oprávnění**. Microsoft Graph zveřejňuje mnoho oprávnění s nejčastěji používanými v horní části seznamu.
1. V části **vybrat oprávnění** vyberte následující oprávnění:

    | Oprávnění       | Description                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | Zobrazit e-mailovou adresu uživatelů                           |
    | `offline_access` | Udržujte přístup k datům, ke kterým jste udělili přístup. |
    | `openid`         | Přihlášení uživatelů                                       |
    | `profile`        | Zobrazit základní profil uživatele                           |
1. Chcete-li dokončit proces, vyberte možnost **Přidat oprávnění** .

Kdykoli nakonfigurujete oprávnění, uživatelům vaší aplikace se po přihlášení zobrazí výzva k jejich souhlasu, aby vaše aplikace měla přístup k rozhraní API prostředků vaším jménem.

Jako správce můžete také udělit souhlas jménem *všech* uživatelů, aby k tomu nezobrazovali žádné výzvy. Souhlas správce je popsán dále v části [Další informace o oprávněních k rozhraní API a souhlasu správce](#more-on-api-permissions-and-admin-consent) tohoto článku.

### <a name="application-permission-to-microsoft-graph"></a>Oprávnění aplikace pro Microsoft Graph

Nakonfigurujte oprávnění aplikace pro aplikaci, která musí být ověřována sám bez zásahu uživatele nebo jejich souhlasu. Oprávnění aplikací jsou obvykle používána službami na pozadí nebo démony, které přistupují k rozhraní API v "nedostatku" a webovými rozhraními API, které přistupují k jinému (podřízenému) rozhraní API.

V následujících krocích udělíte oprávnění k *souborům Microsoft Graph. číst. všechna* oprávnění jako příklad.

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Pokud máte přístup k více klientům, vyberte v horní nabídce možnost **adresář a filtr předplatného** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: a vyberte klienta, který obsahuje registraci klientské aplikace.
1. Vyberte možnost **Azure Active Directory**  >  **Registrace aplikací** a pak vyberte klientskou aplikaci.
1. Vyberte **oprávnění API**  >  **Přidat** oprávnění  >  **Microsoft Graph**  >  **oprávnění aplikace**.
1. Všechna oprávnění vystavená Microsoft Graph jsou uvedena v části **vybrat oprávnění**.
1. Vyberte oprávnění nebo oprávnění, která chcete aplikaci udělit. Jako příklad můžete mít aplikaci démona, která prohledává soubory ve vaší organizaci a upozorňuje na konkrétní typ souboru nebo název.

    V části **vybrat oprávnění** rozbalte položku **soubory** a potom vyberte oprávnění *soubory. číst. vše* .
1. Vyberte **Přidat oprávnění**.

Některá oprávnění, například soubory Microsoft Graph *. Read. All* , vyžadují souhlas správce. Souhlas správce udělíte kliknutím na tlačítko **udělení souhlasu správce** , které je popsáno dále v části [tlačítko pro souhlas správce](#admin-consent-button) .

### <a name="configure-client-credentials"></a>Konfigurace přihlašovacích údajů klienta

Aplikace, které používají oprávnění aplikace, se ověřují samostatně pomocí svých vlastních přihlašovacích údajů bez nutnosti zásahu uživatele. Předtím, než vaše aplikace (nebo rozhraní API) může získat přístup k Microsoft Graph, vlastnímu webovému rozhraní API nebo jinému rozhraní API pomocí oprávnění aplikace, musíte nakonfigurovat přihlašovací údaje klientské aplikace.

Další informace o konfiguraci přihlašovacích údajů aplikace najdete v části [Přidání přihlašovacích údajů](quickstart-register-app.md#add-credentials) v [rychlém startu: registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md).

## <a name="more-on-api-permissions-and-admin-consent"></a>Další informace o oprávněních rozhraní API a souhlasu správce

Podokno **oprávnění rozhraní API** registrace aplikace obsahuje [nakonfigurovanou tabulku oprávnění](#configured-permissions) a může také obsahovat [Další oprávnění udělená](#other-permissions-granted) v této tabulce. Obě tabulky a [tlačítko souhlasu správce](#admin-consent-button) jsou popsány v následujících částech.

### <a name="configured-permissions"></a>Nakonfigurovaná oprávnění

Tabulka **konfigurovaná oprávnění** v podokně **oprávnění rozhraní API** zobrazuje seznam oprávnění, která vaše aplikace vyžaduje pro základní operaci – seznam *požadovaných prostředků přístupu* (RA). Uživatelé nebo jejich správci budou muset před použitím vaší aplikace souhlasit s těmito oprávněními. Další volitelná oprávnění je možné požádat později za běhu (pomocí dynamického souhlasu).

Toto je minimální seznam oprávnění, která uživatelé budou muset pro vaši aplikaci souhlasit. Může to být víc, ale vždycky se to bude vyžadovat. Z důvodu zabezpečení a pomohli uživatelům a správcům pohodlnější používání vaší aplikace, nikdy Nepožadujte cokoli, co nepotřebujete.

Oprávnění, která se zobrazují v této tabulce, můžete přidat nebo odebrat pomocí výše uvedeného postupu nebo z [jiných udělených oprávnění](#other-permissions-granted) (popsaných v další části). Jako správce můžete udělit souhlas správce pro úplnou sadu oprávnění rozhraní API, které se zobrazí v tabulce, a odvolat souhlas pro jednotlivá oprávnění.

### <a name="other-permissions-granted"></a>Další udělená oprávnění

V podokně **oprávnění rozhraní API** se může zobrazit i tabulka s oprávněním **jiná oprávnění udělená pro {tenant}** . **Ostatní oprávnění udělená pro tabulku {The tenant}** zobrazují oprávnění udělená pro tenanta, který není explicitně nakonfigurovaný u objektu aplikace. Tato oprávnění byla dynamicky vyžádána a poslána uživateli. Tato část se zobrazí jenom v případě, že existuje aspoň jedno oprávnění, které platí.

Můžete přidat úplnou sadu oprávnění rozhraní API nebo jednotlivá oprávnění, která se zobrazí v tabulce **konfigurovaná oprávnění** . Jako správce můžete odvolat souhlas správce pro rozhraní API nebo jednotlivá oprávnění v této části.

### <a name="admin-consent-button"></a>Tlačítko pro souhlas správce

Tlačítko **udělení souhlasu správce pro {vašeho tenanta}** umožňuje správci udělit souhlas správce k oprávněním nakonfigurovaným pro aplikaci. Když vyberete tlačítko, zobrazí se dialogové okno s požadavkem, abyste potvrdili akci souhlasu.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="V podokně konfigurovaná oprávnění v Azure Portal se zvýrazní tlačítko pro udělení souhlasu správce.":::

Po udělení souhlasu se budou zobrazovat oprávnění, která vyžadovala souhlas správce, jako by jim byla udělena souhlas:

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Konfigurace tabulky oprávnění v Azure Portal zobrazení souhlasu správce uděleného pro soubory. číst. všechna oprávnění":::

Tlačítko **udělení souhlasu správce** není k *dispozici* , pokud nejste správcem nebo pokud pro aplikaci nejsou nakonfigurovaná žádná oprávnění. Pokud máte oprávnění udělená, ale ještě nakonfigurovaná, klikněte na tlačítko souhlasu správce, abyste tato oprávnění zpracovali. Můžete je přidat do konfigurovaných oprávnění nebo je odebrat.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu rychlému startu v řadě, kde se dozvíte, jak nakonfigurovat typy účtů, které mají přístup k vaší aplikaci. Můžete například chtít omezit přístup jenom na uživatele ve vaší organizaci (jeden tenant) nebo dovolit uživatelům v jiných klientech Azure AD (víceklientské) a s osobními účty Microsoft (MSA).

> [!div class="nextstepaction"]
> [Úprava účtů podporovaných aplikací](quickstart-modify-supported-accounts.md)
