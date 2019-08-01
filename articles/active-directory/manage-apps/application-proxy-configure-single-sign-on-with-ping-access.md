---
title: Ověřování založené na hlavičkách pingaccessu pro Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Publikování aplikací pomocí Proxy aplikace a PingAccess pro podporu ověřování založené na hlavičkách.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0544ed0ff217b6e37cca22a1fc1e0048b30da462
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694216"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Ověřování založené na hlavičkách pro jednotné přihlašování s Proxy aplikací a Pingaccessem

Proxy aplikace Azure Active Directory (Azure AD) spolupracuje se službou PingAccess, aby vaši zákazníci Azure AD mohli získat přístup k více aplikacím. PingAccess rozšíří [stávající nabídky Proxy aplikací](application-proxy.md) zahrnout přístup jednotné přihlašování k aplikacím, které k ověřování používají hlavičky.

## <a name="whats-pingaccess-for-azure-ad"></a>Co je PingAccess pro Azure AD?

S PingAccess pro Azure AD můžete uživatelům udělit přístup k aplikacím, které používají hlavičky pro ověřování, a jednotné přihlašování (SSO). Proxy aplikací bude tyto aplikace stejně jako jakýkoli jiný, k ověření přístupu pomocí Azure AD a následným předáním provoz přes službu konektoru. PingAccess se nachází před aplikacemi a překládá přístupový token z Azure AD do hlavičky. Aplikace potom obdrží ověření ve formátu, který může číst.

Uživatelé nebudou Všimněte si, že něco jinak při přihlášení k vašim podnikovým aplikacím používat. Mohou i nadále pracovat z kdekoli na jakémkoli zařízení. Konektor proxy aplikací směruje vzdálený provoz do všech aplikací bez ohledu na jejich typ ověřování, takže se i nadále budou automaticky vyrovnávat zatížení.

## <a name="how-do-i-get-access"></a>Jak získám přístup?

Vzhledem k tomu, že tento scénář pochází z partnerství mezi Azure Active Directory a PingAccess, potřebujete licence pro obě služby. Předplatná Azure Active Directory Premium však obsahují PingAccess licence basic, která zahrnuje až 20 aplikací. Pokud je potřeba publikovat více než 20 aplikací založené na hlavičkách, si můžete zakoupit další licence od PingAccess.

Další informace najdete v článku [Edice služby Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikování aplikace v Azure

Tento článek je určen pro uživatele, kteří mají při prvním publikování aplikace v tomto scénáři. Kromě podrobností kroků publikování vás seznámí s tím, jak začít s proxy aplikací i s PingAccess. Pokud jste obě služby už nakonfigurovali, ale chcete aktualizovat v krocích publikování, přejděte do části [Přidání aplikace do Azure AD s využitím proxy aplikací](#add-your-application-to-azure-ad-with-application-proxy) .

> [!NOTE]
> Vzhledem k tomu, že tento scénář je partnerství mezi službami Azure AD a PingAccess, některé pokyny existují na webu s Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Nainstalujte konektor Proxy aplikací

Pokud jste povolili proxy aplikací a už jste konektor nainstalovali, můžete tuto část přeskočit a přejít na [Přidat aplikaci do Azure AD pomocí proxy aplikací](#add-your-application-to-azure-ad-with-application-proxy).

Konektor proxy aplikací je služba systému Windows Server, která směruje provoz od vzdálených zaměstnanců do publikovaných aplikací. Podrobnější pokyny k instalaci najdete v [tématu Kurz: Přidejte místní aplikaci pro vzdálený přístup prostřednictvím proxy aplikací v Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikace. Zobrazí se stránka **Centrum pro správu Azure Active Directory** .
1. Vyberte **Azure Active Directory** > **Služba konektoru pro stahování** **proxy** > aplikací. Zobrazí se stránka pro **stažení konektoru proxy aplikace** .

   ![Stažení konektoru proxy aplikací](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Postupujte podle pokynů k instalaci.

Stažení konektoru by mělo automaticky povolit proxy aplikací pro váš adresář, ale v případě potřeby můžete vybrat možnost **Povolit proxy aplikace**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Přidání aplikace do Azure AD s využitím proxy aplikací

Existují dvě akce, které musíte provést na webu Azure Portal. Je třeba nejprve, můžete publikovat svoji aplikaci pomocí Proxy aplikace. Pak potřebujete shromáždit některé informace o aplikaci, kterou můžete použít během PingAccess kroků.

#### <a name="publish-your-application"></a>Publikování aplikace

Nejdřív budete muset publikovat aplikaci. Tato akce zahrnuje:

- Přidání místní aplikace do Azure AD
- Přiřazení uživatele k testování aplikace a volba jednotného přihlašování na základě hlaviček
- Nastavení adresy URL pro přesměrování aplikace
- Udělení oprávnění uživatelům a jiným aplikacím pro použití vaší místní aplikace

Publikování vlastní místní aplikace:

1. Pokud jste to neudělali v poslední části, přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikace.
1. Vyberte **podnikové aplikace** > **Nová** > **místní**aplikace. Zobrazí se stránka **Přidat vlastní místní aplikace** .

   ![Přidat vlastní místní aplikaci](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Vyplňte požadovaná pole informacemi o vaší nové aplikaci. Pro nastavení použijte následující pokyny.

   > [!NOTE]
   > Podrobnější návod k tomuto kroku najdete v tématu [Přidání místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Interní adresa URL**: Obvykle zadáte adresu URL, která vás přesměruje na přihlašovací stránku aplikace, když se nacházíte v podnikové síti. V tomto scénáři musí konektor zacházet s proxy PingAccess jako s frontou stránky aplikace. Použijte tento formát: `https://<host name of your PingAccess server>:<port>`. Port, který je 3000 ve výchozím nastavení, ale můžete ho nakonfigurovat PingAccess.

      > [!WARNING]
      > Pro tento typ jednotného přihlašování musí interní adresa URL používat `https` a nemůže použít. `http`

   1. **Metoda**předběžného ověřování: Vyberte **Azure Active Directory**.
   1. **Přeložit adresu URL v hlavičkách**: Vyberte **ne**.

   > [!NOTE]
   > Pokud je toto první aplikace, použijte ke spuštění a vrátit zpět a aktualizovat toto nastavení, pokud změníte konfiguraci PingAccess port 3000. Pro následné aplikace bude port muset odpovídat Naslouchacímu procesu, který jste nakonfigurovali v PingAccess. Další informace o [naslouchacích procesů v PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Vyberte **Přidat**. Zobrazí se stránka s přehledem pro novou aplikaci.

Teď přiřaďte uživatele k testování aplikací a vyberte jednotné přihlašování založené na hlavičkách:

1. Na bočním panelu aplikace vyberte **Uživatelé a skupiny** > **Přidat uživatele** > **a skupiny uživatelů (\<počet vybraných >)** . Zobrazí se seznam uživatelů a skupin, ze kterých si můžete vybrat.

   ![Zobrazí seznam uživatelů a skupin.](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Vyberte uživatele pro testování aplikace a vyberte **Vybrat**. Ujistěte se, že testovacího účtu má přístup k místní aplikaci.
1. Vyberte **Přiřadit**.
1. Z bočního panelu aplikace vyberte**záhlaví na základě** **jednotného přihlašování** > .

   > [!TIP]
   > Pokud je toto vaše první přihlášení pomocí založeným na hlavičkách jednotného přihlašování, budete muset nainstalovat PingAccess. Ujistěte se, že vaše předplatné Azure je automaticky přiřazen k instalaci PingAccess, použijte odkaz na této stránce jednotné přihlašování ke stažení PingAccess. Můžete teď otevřít server pro stahování nebo vraťte na tuto stránku později.

   ![Zobrazí přihlašovací obrazovku založenou na hlavičkách a PingAccess.](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Vyberte **Uložit**.

Pak se ujistěte, že adresa URL pro přesměrování je nastavená na externí adresu URL:

1. Na bočním panelu **centra pro správu Azure Active Directory** vyberte **Azure Active Directory** > **Registrace aplikací**. Zobrazí se seznam aplikací.
1. Vyberte svou aplikaci.
1. Vyberte odkaz vedle identifikátorů **URI přesměrování**, který zobrazuje počet identifikátorů URI pro přesměrování pro webové a veřejné klienty. Zobrazí se stránka **název aplikace>-ověřování.\<**
1. Ověřte, zda externí adresa URL, kterou jste dříve přiřadili k vaší aplikaci, je v seznamu **identifikátorů URI pro přesměrování** . Pokud ne, přidejte externí adresu URL teď pomocí typu URI přesměrování **webu**a vyberte **Uložit**.

Nakonec nastavte svou místní aplikaci tak, aby uživatelé měli přístup pro čtení a další aplikace mají přístup pro čtení a zápis:

1. Z bočního panelu **Registrace aplikací** pro vaši aplikaci > vyberte **oprávnění rozhraní API** **Přidat oprávnění** > **Microsoft API** > **Microsoft Graph**. Zobrazí se stránka **požádat o oprávnění API** pro **Microsoft Graph** , která obsahuje rozhraní API pro Windows Azure Active Directory.

   ![Zobrazuje stránku oprávnění API pro žádosti.](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Vyberte **delegovaná oprávnění** > **uživatel** > uživatel **. číst**.
1. Vyberte **oprávnění** > aplikace > Application Application. pročtení **. vše**.
1. Vyberte **Přidat oprávnění**.
1. Na stránce **oprávnění rozhraní API** vyberte **pro \<název adresáře > udělit souhlas správce**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Shromáždit data za PingAccess kroky

Chcete-li nastavit aplikaci pomocí PingAccess, je třeba shromáždit tyto tři informace (všechny identifikátory GUID):

| Název pole Azure AD | Název pole PingAccess | Formát dat |
| --- | --- | --- |
| **ID aplikace (klienta)** | **ID klienta** | GUID |
| **ID adresáře (tenant)** | **Stavil** | GUID |
| `PingAccess key` | **Tajný kód klienta** | Náhodný řetězec |

Shromažďování těchto informací:

1. Na bočním panelu **centra pro správu Azure Active Directory** vyberte **Azure Active Directory** > **Registrace aplikací**. Zobrazí se seznam aplikací.
1. Vyberte svou aplikaci. Zobrazí se stránka **Registrace aplikací** pro vaši aplikaci.

   ![Přehled registrace aplikace](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Vedle hodnoty **ID aplikace (klienta)** vyberte ikonu **Kopírovat do schránky** a potom ji zkopírujte a uložte. Tuto hodnotu zadáte později jako ID klienta PingAccess.
1. Po hodnotě **ID adresáře (tenant)** vyberte možnost **Kopírovat do schránky**a potom ji zkopírujte a uložte. Tuto hodnotu zadáte později jako Vystavitel PingAccess.
1. Z postranního panelu **Registrace aplikací** pro vaši aplikaci vyberte **certifikáty a tajné klíče** > **nový tajný klíč klienta**. Zobrazí se stránka **Přidat tajný klíč klienta** .

   ![Zobrazí stránku přidat tajný klíč klienta.](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Do **popisu**zadejte `PingAccess key`.
1. V části **vypršení platnosti**vyberte, jak se má klíč PingAccess nastavit: **V 1 roce**, **v 2 letech**nebo **nikdy**.
1. Vyberte **Přidat**. Klíč PingAccess se zobrazí v tabulce tajných klíčů klienta s náhodným řetězcem, který do pole **hodnota** plní text.
1. Vedle pole **hodnota** klíče PingAccess vyberte ikonu **Kopírovat do schránky** a potom ji zkopírujte a uložte. Tuto hodnotu zadáte později jako tajný klíč klienta PingAccess.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Aktualizace GraphAPI pro odesílání vlastních polí (volitelné)

Pokud potřebujete vlastní deklaraci identity, která odesílá jiné tokeny v rámci access_token spotřebovaného PingAccess, `acceptMappedClaims` nastavte pole aplikace `True`na. Tuto změnu můžete provést pomocí Průzkumníka grafů nebo manifestu aplikace portálu Azure AD.

**V tomto příkladu se používá Průzkumník grafů:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**V tomto příkladu se používá [portál Azure Active Directory](https://aad.portal.azure.com/) k aktualizaci `acceptMappedClaims` pole:**

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikace.
1. Vyberte **Azure Active Directory** > **registrace aplikací**. Zobrazí se seznam aplikací.
1. Vyberte svou aplikaci.
1. Z postranního panelu **Registrace aplikací** stránky pro vaši aplikaci vyberte možnost **manifest**. Zobrazí se kód JSON manifestu pro registraci vaší aplikace.
1. Vyhledejte pole a změňte hodnotu na `True`. `acceptMappedClaims`
1. Vyberte **Uložit**.

### <a name="use-of-optional-claims-optional"></a>Použití volitelných deklarací identity (volitelné)

Volitelné deklarace identity umožňují přidat standardní, ale nezahrnuté deklarace identity, které má každý uživatel a tenant. Můžete nakonfigurovat volitelné deklarace identity pro aplikaci úpravou manifestu aplikace. Další informace najdete v [článku Principy manifestu aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/) .

Příklad, který bude obsahovat e-mailovou adresu do access_token, který PingAccess spotřebuje:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Použití zásad mapování deklarací (volitelné)

[Zásady mapování deklarací identity (Preview)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) pro atributy, které neexistují v AzureAD. Mapování deklarací umožňuje migrovat staré aplikace Prem do cloudu přidáním dalších vlastních deklarací, které jsou založené na službě ADFS nebo uživatelských objektech.

Pokud chcete, aby vaše aplikace používala vlastní deklaraci identity a zahrnovala další pole, ujistěte se, že jste [vytvořili také vlastní zásadu mapování deklarací identity a přiřadili ji k aplikaci](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Pokud chcete použít vlastní deklarace identity, musí mít také vlastní zásady definované a přiřazené k aplikaci. Tato zásada by měl obsahovat všechny požadované vlastní atributy.
>
> Pomocí prostředí PowerShell, Průzkumníka služby Azure AD Graph nebo Microsoft Graph můžete provádět definice zásad a jejich přiřazení. Pokud je provádíte v prostředí PowerShell, možná budete muset nejdřív použít `New-AzureADPolicy` a přiřadit `Add-AzureADServicePrincipalPolicy`aplikaci k aplikaci. Další informace najdete v tématu [přiřazení zásad mapování deklarací identity](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Příklad:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Povolit PingAccess používat vlastní deklarace identity

Povolení použití vlastních deklarací identity PingAccess je volitelné, ale vyžaduje se v případě, že aplikace očekává využívání dalších deklarací identity.

Když v následujícím kroku nakonfigurujete PingAccess, bude webová relace, kterou vytvoříte, vytvořit (Nastavení-> > webové relace), musí mít oddaný **profil požadavků** a **aktualizovat atributy uživatele** nastavené na **ne** .

## <a name="download-pingaccess-and-configure-your-application"></a>Stažení PingAccess a konfigurace vaší aplikace

Teď, když jste dokončili všechny kroky instalace služby Azure Active Directory, které můžete přejít ke konfiguraci PingAccess.

Podrobný postup pro PingAccess část tohoto scénáře pokračuje v dokumentaci k identitě příkazů. Postupujte podle pokynů v části [Konfigurace PingAccess pro Azure AD pro ochranu aplikací publikovaných pomocí proxy aplikace Microsoft Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) na webu testovat identitu na webu.

Tyto kroky vám pomůžou nainstalovat PingAccess a nastavit účet PingAccess (pokud ho ještě nemáte). Pokud pak chcete vytvořit připojení k Azure AD OpenID Connect (OIDC), nastavíte poskytovatele tokenu s hodnotou **ID adresáře (tenant)** , kterou jste zkopírovali z portálu Azure AD. V dalším kroku vytvoříte relaci webu v PingAccess a použijete **ID aplikace (klienta)** a `PingAccess key` hodnoty. Potom můžete nastavit mapování identit a vytvořit virtuální hostitel, webu nebo aplikace.

### <a name="test-your-application"></a>Testování aplikace

Po dokončení všech těchto kroků by měla být vaše aplikace spuštěná a spuštěná. Pokud ho chcete otestovat, otevřete prohlížeč a přejděte na externí adresu URL, kterou jste vytvořili při publikování aplikace v Azure. Přihlaste se pomocí testovacího účtu, který jste přiřadili k aplikaci.

## <a name="next-steps"></a>Další postup

- [Konfigurace PingAccess pro Azure AD pro ochranu aplikací publikovaných pomocí proxy aplikací Microsoft Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md)
- [Poradce při potížích s Proxy aplikací problémy a chybové zprávy](application-proxy-troubleshoot.md)
