---
title: Ověřování pomocí hlaviček s PingAccess pro Azure Proxy aplikací služby AD | Microsoft Docs
description: Publikování aplikací pomocí PingAccess a proxy aplikací pro podporu ověřování na základě hlaviček.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 512aed93906968b14f7c6a13e08f74bbeb2f5f31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87431081"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Ověřování založené na hlavičkách pro jednotné přihlašování s Proxy aplikací a PingAccess

Proxy aplikace Azure Active Directory (Azure AD) spolupracuje se službou PingAccess, aby vaši zákazníci Azure AD mohli získat přístup k více aplikacím. PingAccess rozbalí [existující nabídky proxy aplikací](application-proxy.md) , aby zahrnovaly přístup s jednotným přihlašováním k aplikacím, které používají hlavičky pro ověřování.

## <a name="whats-pingaccess-for-azure-ad"></a>Co je PingAccess pro Azure AD?

S PingAccess pro Azure AD můžete uživatelům udělit přístup k aplikacím, které používají hlavičky pro ověřování, a jednotné přihlašování (SSO). Proxy aplikace tyto aplikace považují za jiné, a to pomocí Azure AD k ověřování přístupu a následnému předávání provozu prostřednictvím služby konektoru. PingAccess se nachází před aplikacemi a překládá přístupový token z Azure AD do hlavičky. Aplikace potom obdrží ověření ve formátu, který může číst.

Pokud se přihlásíte k používání podnikových aplikací, nebudou si uživatelé moci všimnout žádné jiné. Můžou pořád pracovat odkudkoli na jakémkoli zařízení. Konektor proxy aplikací směruje vzdálený provoz do všech aplikací bez ohledu na jejich typ ověřování, takže se i nadále budou automaticky vyrovnávat zatížení.

## <a name="how-do-i-get-access"></a>Návody získat přístup?

Vzhledem k tomu, že tento scénář pochází z partnerství mezi Azure Active Directory a PingAccess, potřebujete licence pro obě služby. Azure Active Directory Premium předplatných ale zahrnuje základní licenci PingAccess, která zahrnuje až 20 aplikací. Pokud potřebujete publikovat více než 20 aplikací založených na hlavičkách, můžete si koupit další licenci z PingAccess.

Další informace najdete v tématu [Azure Active Directory edice](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikování aplikace v Azure

Tento článek je určen pro uživatele, kteří mají při prvním publikování aplikace v tomto scénáři. Kromě podrobností kroků publikování vás seznámí s tím, jak začít s proxy aplikací i s PingAccess. Pokud jste obě služby už nakonfigurovali, ale chcete aktualizovat v krocích publikování, přejděte do části [Přidání aplikace do Azure AD s využitím proxy aplikací](#add-your-application-to-azure-ad-with-application-proxy) .

> [!NOTE]
> Vzhledem k tomu, že tento scénář je partnerství mezi Azure AD a PingAccess, existují některé pokyny na webu identity pro ověřování.

### <a name="install-an-application-proxy-connector"></a>Instalace konektoru proxy aplikací

Pokud jste povolili proxy aplikací a už jste konektor nainstalovali, můžete tuto část přeskočit a přejít na [Přidat aplikaci do Azure AD pomocí proxy aplikací](#add-your-application-to-azure-ad-with-application-proxy).

Konektor proxy aplikací je služba systému Windows Server, která směruje provoz od vzdálených zaměstnanců do publikovaných aplikací. Podrobnější pokyny k instalaci najdete v tématu [kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikace. Zobrazí se stránka **Centrum pro správu Azure Active Directory** .
1. Vyberte **Azure Active Directory**  >  **Application proxy**  >  **Služba konektoru pro stahování**proxy aplikací. Zobrazí se stránka pro **stažení konektoru proxy aplikace** .

   ![Stažení konektoru proxy aplikací](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Postupujte podle pokynů k instalaci.

Stažení konektoru by mělo automaticky povolit proxy aplikací pro váš adresář, ale v případě potřeby můžete vybrat možnost **Povolit proxy aplikace**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Přidání aplikace do Azure AD s využitím proxy aplikací

Existují dvě akce, které je třeba provést v Azure Portal. Nejdřív musíte aplikaci publikovat pomocí proxy aplikace. Pak potřebujete shromáždit některé informace o aplikaci, kterou můžete použít během PingAccess kroků.

#### <a name="publish-your-application"></a>Publikování aplikace

Nejdřív budete muset publikovat aplikaci. Tato akce zahrnuje:

- Přidání místní aplikace do Azure AD
- Přiřazení uživatele k testování aplikace a volba jednotného přihlašování na základě hlaviček
- Nastavení adresy URL pro přesměrování aplikace
- Udělení oprávnění uživatelům a jiným aplikacím pro použití vaší místní aplikace

Publikování vlastní místní aplikace:

1. Pokud jste to neudělali v poslední části, přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikace.
1. Vybrat **podnikové aplikace**  >  **Nová aplikace**  >  **Přidat místní aplikaci**. Zobrazí se stránka **Přidat vlastní místní aplikace** .

   ![Přidání vlastní místní aplikace](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Vyplňte požadovaná pole informacemi o vaší nové aplikaci. Pro nastavení použijte následující pokyny.

   > [!NOTE]
   > Podrobnější návod k tomuto kroku najdete v tématu [Přidání místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Interní adresa URL**: obvykle zadáváte adresu URL, která vás přesměruje na přihlašovací stránku aplikace, když se nacházíte v podnikové síti. V tomto scénáři musí konektor zacházet s proxy PingAccess jako s frontou stránky aplikace. Použijte tento formát: `https://<host name of your PingAccess server>:<port>` . Ve výchozím nastavení je port 3000, ale můžete ho nakonfigurovat v PingAccess.

      > [!WARNING]
      > Pro tento typ jednotného přihlašování musí interní adresa URL používat `https` a nemůže použít `http` .

   1. **Metoda**předběžného ověření: vyberte **Azure Active Directory**.
   1. **Přeložit adresu URL v hlavičkách**: vyberte **ne**.

   > [!NOTE]
   > Pokud se jedná o vaši první aplikaci, spusťte pomocí portu 3000 a vraťte se k aktualizaci tohoto nastavení, pokud změníte konfiguraci PingAccess. Pro následné aplikace bude port muset odpovídat Naslouchacímu procesu, který jste nakonfigurovali v PingAccess. Přečtěte si další informace o [posluchačích v PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Vyberte **Přidat**. Zobrazí se stránka s přehledem pro novou aplikaci.

Teď přiřaďte uživatele k testování aplikací a vyberte jednotné přihlašování založené na hlavičkách:

1. Na bočním panelu aplikace vyberte **Uživatelé a skupiny**  >  **Přidat uživatele**  >  **a skupiny uživatelů ( \<Number> vybrané)**. Zobrazí se seznam uživatelů a skupin, ze kterých si můžete vybrat.

   ![Zobrazí seznam uživatelů a skupin.](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Vyberte uživatele pro testování aplikace a vyberte **Vybrat**. Ujistěte se, že tento testovací účet má přístup k místní aplikaci.
1. Vyberte **Přiřadit**.
1. Z bočního panelu aplikace vyberte **Single sign-on**  >  **záhlaví na základě**jednotného přihlašování.

   > [!TIP]
   > Pokud používáte jednotné přihlašování založené na hlavičkách, je potřeba nainstalovat PingAccess. Pokud chcete zajistit, aby se vaše předplatné Azure automaticky přidružil k instalaci PingAccess, použijte odkaz na této stránce jednotného přihlašování ke stažení PingAccess. Nyní můžete otevřít web pro stahování nebo se na tuto stránku vrátit později.

   ![Zobrazí přihlašovací obrazovku založenou na hlavičkách a PingAccess.](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Vyberte **Uložit**.

Pak se ujistěte, že adresa URL pro přesměrování je nastavená na externí adresu URL:

1. Na bočním panelu **centra pro správu Azure Active Directory** vyberte **Azure Active Directory**  >  **Registrace aplikací**. Zobrazí se seznam aplikací.
1. Vyberte svou aplikaci.
1. Vyberte odkaz vedle **identifikátorů URI přesměrování**, který zobrazuje počet identifikátorů URI pro přesměrování pro webové a veřejné klienty. Zobrazí se stránka ** \<application name> -ověřování** .
1. Ověřte, zda externí adresa URL, kterou jste dříve přiřadili k vaší aplikaci, je v seznamu **identifikátorů URI pro přesměrování** . Pokud ne, přidejte externí adresu URL teď pomocí typu URI přesměrování **webu**a vyberte **Uložit**.

Kromě externí adresy URL by se měl do seznamu identifikátorů URI pro přesměrování přidat autorizační koncový bod Azure Active Directory na externí adrese URL.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Nakonec nastavte svou místní aplikaci tak, aby uživatelé měli přístup pro čtení a další aplikace mají přístup pro čtení a zápis:

1. Z bočního panelu **Registrace aplikací** pro vaši aplikaci vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**  >  **Microsoft API**  >  **Microsoft Graph**. Zobrazí se stránka **požádat o oprávnění API** pro **Microsoft Graph** , která obsahuje rozhraní API pro Windows Azure Active Directory.

   ![Zobrazuje stránku oprávnění API pro žádosti.](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Vyberte **delegovaná oprávnění**  >  **uživatel**uživatel  >  **. číst**.
1. Vyberte **oprávnění aplikace**  >  **Application**  >  **Application. pročtení. vše**.
1. Vyberte **Přidat oprávnění**.
1. Na stránce **oprávnění rozhraní API** vyberte **udělit souhlas správce pro \<your directory name> **.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Shromažďovat informace o PingAccess krocích

Chcete-li nastavit aplikaci pomocí PingAccess, je třeba shromáždit tyto tři informace (všechny identifikátory GUID):

| Název pole Azure AD | Název pole PingAccess | Formát dat |
| --- | --- | --- |
| **ID aplikace (klienta)** | **ID klienta** | Identifikátor GUID |
| **ID adresáře (tenanta)** | **Stavil** | Identifikátor GUID |
| `PingAccess key` | **Tajný kód klienta** | Náhodný řetězec |

Shromažďování těchto informací:

1. Na bočním panelu **centra pro správu Azure Active Directory** vyberte **Azure Active Directory**  >  **Registrace aplikací**. Zobrazí se seznam aplikací.
1. Vyberte svou aplikaci. Zobrazí se stránka **Registrace aplikací** pro vaši aplikaci.

   ![Přehled registrace aplikace](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Vedle hodnoty **ID aplikace (klienta)** vyberte ikonu **Kopírovat do schránky** a potom ji zkopírujte a uložte. Tuto hodnotu zadáte později jako ID klienta PingAccess.
1. Po hodnotě **ID adresáře (tenant)** vyberte možnost **Kopírovat do schránky**a potom ji zkopírujte a uložte. Tuto hodnotu zadáte později jako Vystavitel PingAccess.
1. Z postranního panelu **Registrace aplikací** pro vaši aplikaci vyberte **certifikáty a tajné klíče**  >  **nový tajný klíč klienta**. Zobrazí se stránka **Přidat tajný klíč klienta** .

   ![Zobrazí stránku přidat tajný klíč klienta.](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Do **popisu**zadejte `PingAccess key` .
1. V části **vypršení platnosti**vyberte, jak se má PingAccess klíč nastavit: **1 rok**, **2 roky**nebo **nikdy**.
1. Vyberte **Přidat**. Klíč PingAccess se zobrazí v tabulce tajných klíčů klienta s náhodným řetězcem, který do pole **hodnota** plní text.
1. Vedle pole **hodnota** klíče PingAccess vyberte ikonu **Kopírovat do schránky** a potom ji zkopírujte a uložte. Tuto hodnotu zadáte později jako tajný klíč klienta PingAccess.

**Aktualizujte `acceptMappedClaims` pole:**

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikace.
1. Vyberte **Azure Active Directory**  >  **Registrace aplikací**. Zobrazí se seznam aplikací.
1. Vyberte svou aplikaci.
1. Z postranního panelu **Registrace aplikací** stránky pro vaši aplikaci vyberte možnost **manifest**. Zobrazí se kód JSON manifestu pro registraci vaší aplikace.
1. Vyhledejte `acceptMappedClaims` pole a změňte hodnotu na `True` .
1. Vyberte **Uložit**.

### <a name="use-of-optional-claims-optional"></a>Použití volitelných deklarací identity (volitelné)

Volitelné deklarace identity umožňují přidat standardní, ale nezahrnuté deklarace identity, které má každý uživatel a tenant. Můžete nakonfigurovat volitelné deklarace identity pro aplikaci úpravou manifestu aplikace. Další informace najdete v [článku Principy manifestu aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/) .

Příklad, jak zahrnout e-mailovou adresu do access_token, kterou bude PingAccess využívat:
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
> Pokud chcete použít vlastní deklaraci identity, musíte mít také definovanou vlastní zásadu a přiřazenou aplikaci. Tato zásada by měla obsahovat všechny požadované vlastní atributy.
>
> Pomocí PowerShellu nebo Microsoft Graph můžete provádět definice zásad a jejich přiřazení. Pokud je provádíte v prostředí PowerShell, možná budete muset nejdřív použít `New-AzureADPolicy` a přiřadit aplikaci k aplikaci `Add-AzureADServicePrincipalPolicy` . Další informace najdete v tématu [přiřazení zásad mapování deklarací identity](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Příklad:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Povolit PingAccess používat vlastní deklarace identity

Povolení použití vlastních deklarací identity PingAccess je volitelné, ale vyžaduje se v případě, že aplikace očekává využívání dalších deklarací identity.

Když v následujícím kroku nakonfigurujete PingAccess, bude webová relace, kterou vytvoříte, vytvořit (nastavení->>webové relace), musí mít oddaný **profil požadavků** a **aktualizovat atributy uživatele** nastavené na **ne** .

## <a name="download-pingaccess-and-configure-your-application"></a>Stažení PingAccess a konfigurace vaší aplikace

Teď, když jste dokončili všechny kroky Azure Active Directory nastavení, můžete přejít ke konfiguraci PingAccess.

Podrobný postup pro PingAccess část tohoto scénáře pokračuje v dokumentaci k identitě příkazů. Postupujte podle pokynů v části [Konfigurace PingAccess pro Azure AD pro ochranu aplikací publikovaných pomocí proxy aplikace Microsoft Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) na webu test identity a Stáhněte si [nejnovější verzi PingAccess](https://www.pingidentity.com/en/lp/azure-download.html?).

Tyto kroky vám pomůžou nainstalovat PingAccess a nastavit účet PingAccess (pokud ho ještě nemáte). Pokud pak chcete vytvořit připojení k Azure AD OpenID Connect (OIDC), nastavíte poskytovatele tokenu s hodnotou **ID adresáře (tenant)** , kterou jste zkopírovali z portálu Azure AD. V dalším kroku vytvoříte relaci webu v PingAccess a použijete **ID aplikace (klienta)** a `PingAccess key` hodnoty. Potom můžete nastavit mapování identit a vytvořit virtuálního hostitele, lokalitu a aplikaci.

### <a name="test-your-application"></a>Testování aplikace

Po dokončení všech těchto kroků by měla být vaše aplikace spuštěná a spuštěná. Pokud ho chcete otestovat, otevřete prohlížeč a přejděte na externí adresu URL, kterou jste vytvořili při publikování aplikace v Azure. Přihlaste se pomocí testovacího účtu, který jste přiřadili k aplikaci.

## <a name="next-steps"></a>Další kroky

- [Konfigurace PingAccess pro Azure AD pro ochranu aplikací publikovaných pomocí proxy aplikací Microsoft Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md)
- [Řešení potíží s Proxy aplikací a souvisejícími chybovými zprávami](application-proxy-troubleshoot.md)
