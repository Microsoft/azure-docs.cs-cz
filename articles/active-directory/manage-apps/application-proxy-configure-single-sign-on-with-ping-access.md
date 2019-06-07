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
ms.openlocfilehash: ab08c93662988655154cf300ac4ee3758fbc7872
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472802"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Ověřování založené na hlavičkách pro jednotné přihlašování s Proxy aplikací a Pingaccessem

Proxy aplikací služby Azure Active Directory (Azure AD) ve spolupráci s PingAccess tak, aby svým zákazníkům služby Azure AD můžete přistupovat k více aplikací. PingAccess rozšíří [stávající nabídky Proxy aplikací](application-proxy.md) zahrnout přístup jednotné přihlašování k aplikacím, které k ověřování používají hlavičky.

## <a name="whats-pingaccess-for-azure-ad"></a>Co je PingAccess pro Azure AD?

PingAccess pro Azure AD můžete udělit uživatelům přístup a jednotné přihlašování (SSO) k aplikacím, které k ověřování používají hlavičky. Proxy aplikací bude tyto aplikace stejně jako jakýkoli jiný, k ověření přístupu pomocí Azure AD a následným předáním provoz přes službu konektoru. PingAccess je umístěný před aplikace a převádí přístupového tokenu z Azure AD na záhlaví. Aplikace pak obdrží ověřování ve formátu, který může číst.

Uživatelé nebudou Všimněte si, že něco jinak při přihlášení k vašim podnikovým aplikacím používat. Mohou i nadále pracovat z kdekoli na jakémkoli zařízení. Konektory Proxy aplikací směrovat vzdálený provoz pro všechny aplikace bez ohledu na jejich typ ověřování, tak, že budete stále vyvažovat zatížení automaticky.

## <a name="how-do-i-get-access"></a>Jak získám přístup?

Vzhledem k tomu, že tento scénář pochází z partnerství mezi Azure Active Directory a PingAccess, potřebují licence pro obě služby. Předplatná Azure Active Directory Premium však obsahují PingAccess licence basic, která zahrnuje až 20 aplikací. Pokud je potřeba publikovat více než 20 aplikací založené na hlavičkách, si můžete zakoupit další licence od PingAccess.

Další informace najdete v článku [Edice služby Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikování aplikace v Azure

Tento článek je určený pro osoby k publikování aplikace v tomto scénáři poprvé. Kromě s podrobnostmi o publikování kroky, provede vás v části Začínáme s Proxy aplikací a Pingaccessem. Pokud jste už nakonfigurovali obě služby, ale chcete si můžete znovu projít postup publikování, pokračujte [přidejte svoji aplikaci do služby Azure AD pomocí Proxy aplikace](#add-your-application-to-azure-ad-with-application-proxy) oddílu.

> [!NOTE]
> Vzhledem k tomu, že tento scénář je partnerství mezi službami Azure AD a PingAccess, některé pokyny existují na webu s Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Nainstalujte konektor Proxy aplikací

Pokud jste povolili Proxy aplikace povolená a již nainstalovaný konektor, můžete tuto část přeskočit a přejít na [přidejte svoji aplikaci do služby Azure AD pomocí Proxy aplikace](#add-your-application-to-azure-ad-with-application-proxy).

Konektor Proxy aplikací je služba Windows Server, která směruje provoz z vzdálení zaměstnanci k publikovaným aplikacím. Podrobnější pokyny k instalaci najdete v článku [kurzu: Přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikací v Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikací. **Centra pro správu Azure Active Directory** se zobrazí stránka.
2. Vyberte **Azure Active Directory** > **proxy aplikací** > **stáhnout službu konektoru**. **Stažení konektoru Proxy aplikace** se zobrazí stránka.

   ![Stažení konektoru proxy aplikace](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. Postupujte podle pokynů k instalaci.

Stažení konektoru by měl automaticky povolí Proxy aplikace pro svůj adresář, ale pokud ne, můžete vybrat **povolení Proxy aplikace**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Přidejte svoji aplikaci do služby Azure AD pomocí Proxy aplikace

Existují dvě akce, které musíte provést na webu Azure Portal. Je třeba nejprve, můžete publikovat svoji aplikaci pomocí Proxy aplikace. Potom budete muset shromažďovat některé informace o aplikaci, která můžete použít během kroků PingAccess.

#### <a name="publish-your-application"></a>Publikování aplikace

Nejprve budete muset aplikaci publikovat. Tato akce zahrnuje:

- Přidání místní aplikace do služby Azure AD
- Přiřazení uživatele k testování aplikace a výběr jednotné přihlašování založené na hlavičkách
- Nastavení adresy URL pro přesměrování aplikace
- Udělení oprávnění pro uživatele a jiným aplikacím použít místní aplikace

Chcete-li publikovat místní aplikace:

1. Pokud jste v poslední části, přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikací.
2. Vyberte **podnikové aplikace** > **novou aplikaci** > **On-premises application**. **Přidat vlastní místní aplikaci** se zobrazí stránka.

   ![Přidat místní aplikace](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
3. Vyplňte požadovaná pole s informacemi o novou aplikaci. Použijte pokyny níže pro nastavení.

   > [!NOTE]
   > Podrobnější návod tohoto kroku najdete v tématu [přidat místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Interní adresa URL**: Obvykle je zadat adresu URL, která vás přesměruje na přihlašovací stránce aplikace, až budete v podnikové síti. V tomto scénáři konektoru je potřeba považovat za PingAccess proxy serveru na přední stránce aplikace. Použijte tento formát: `https://<host name of your PingAccess server>:<port>`. Port, který je 3000 ve výchozím nastavení, ale můžete ho nakonfigurovat PingAccess.

      > [!WARNING]
      > Pro tento typ jednotného přihlašování, interní adresa URL musí používat `https` nebo nemůže používat `http`.

   2. **Metoda předběžného ověřování služby**: Zvolte **Azure Active Directory**.
   3. **Přeložit adresy URL v hlavičkách**: Zvolte **ne**.

   > [!NOTE]
   > Pokud je toto první aplikace, použijte ke spuštění a vrátit zpět a aktualizovat toto nastavení, pokud změníte konfiguraci PingAccess port 3000. Aplikacemi třeba port tak, aby odpovídaly naslouchací proces, který jste nakonfigurovali v PingAccess. Další informace o [naslouchacích procesů v PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).
4. Vyberte **Přidat**. Zobrazí se stránka s přehledem pro novou aplikaci.

Nyní přiřadit uživatele pro testování aplikací a zvolte založeným na hlavičkách jednotné přihlašování:

1. Na bočním panelu aplikace vyberte **uživatelů a skupin** > **přidat uživatele** > **uživatelům a skupinám (\<číslo > vybrané)** . Můžete zvolit z se zobrazí seznam uživatelů a skupin.

   ![Uživatelé a skupiny](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)
2. Vybrat uživatele pro testování aplikací a vyberte **vyberte**. Ujistěte se, že testovacího účtu má přístup k místní aplikaci.
3. Vyberte **Přiřadit**.
4. Na bočním panelu aplikace vyberte **jednotného přihlašování** > **založeným na hlavičkách**.

   > [!TIP]
   > Pokud je toto vaše první přihlášení pomocí založeným na hlavičkách jednotného přihlašování, budete muset nainstalovat PingAccess. Ujistěte se, že vaše předplatné Azure je automaticky přiřazen k instalaci PingAccess, použijte odkaz na této stránce jednotné přihlašování ke stažení PingAccess. Můžete teď otevřít server pro stahování nebo vraťte na tuto stránku později.

   ![Přihlašování na základě záhlaví](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)
5. Vyberte **Uložit**.

Pak zajistěte, aby vaše přesměrování, adresa URL je nastavený na externí adresu URL:

1. Z **centra pro správu Azure Active Directory** bočním panelu vyberte **Azure Active Directory** > **registrace aplikací**. Zobrazí se seznam aplikací.

   ![Registrace aplikací](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)
2. Vyberte svou aplikaci.
3. Vyberte odkaz vedle položky **identifikátory URI přesměrování**, zobrazující počet přesměrování nastavení identifikátorů URI pro webové aplikace a veřejné klienty. **\<Název aplikace >-ověřování** zobrazí se stránka.
4. Zkontrolujte, zda je externí adresa URL, který jste přiřadili do vaší aplikace dříve v **identifikátory URI přesměrování** seznamu. Pokud tomu tak není, přidejte externí adresu URL teď pomocí typ identifikátoru URI přesměrování **webové**a vyberte **Uložit**.

Nakonec nastavte místní aplikaci tak, aby uživatelé mají oprávnění ke čtení a jiné aplikace mají přístup pro čtení/zápis:

1. Z **registrace aplikací** postranního panelu pro vaši aplikaci, vyberte **oprávnění k rozhraní API** > **přidat oprávnění**  >   **Rozhraní API od Microsoftu** > **Microsoft Graphu**. **Žádosti rozhraní API oprávnění** stránce **Microsoft Graphu** se zobrazí, který obsahuje rozhraní API pro Windows Azure Active Directory.

   ![Požádat o oprávnění k rozhraní API](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)
2. Vyberte **delegovaná oprávnění** > **uživatele** > **User.Read**.
3. Vyberte **oprávnění aplikace** > **aplikace** > **Application.ReadWrite.All**.
4. Vyberte **přidat oprávnění**.
5. V **oprávnění k rozhraní API** stránce **udělit souhlas správce pro \<váš název adresáře >** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>Shromáždit data za PingAccess kroky

Je potřeba shromáždit tyto tři údaje (všechny identifikátory GUID) k nastavení aplikace a PingAccess:

| Název pole, Azure AD | Název pole PingAccess | Formát dat |
| --- | --- | --- |
| **ID aplikace (klient)** | **ID klienta** | GUID |
| **ID adresáře (tenant)** | **Issuer** | GUID |
| `PingAccess key` | **Tajný klíč klienta** | Náhodný řetězec |

Shromažďování těchto informací:

1. Z **centra pro správu Azure Active Directory** bočním panelu vyberte **Azure Active Directory** > **registrace aplikací**. Zobrazí se seznam aplikací.
2. Vyberte svou aplikaci. **Registrace aplikací** stránky pro vaše aplikace se zobrazí.

   ![Registrace – přehled pro aplikaci](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)
3. Vedle položky **ID aplikace (klient)** hodnotu, vyberte **kopírování do schránky** ikonu, pak zkopírujte a uložte ho. Dále zadejte tuto hodnotu jako ID PingAccess od klienta.
4. Další **ID adresáře (tenant)** hodnota a také vybrat **kopírování do schránky**, pak zkopírujte a uložte ho. Můžete zadat tuto hodnotu později jako PingAccess od vystavitele.
5. Na postranním panelu Nástroje **registrace aplikací** pro vaši aplikaci, vyberte **certifikátů a tajných kódů** > **nový tajný kód klienta**. **Přidat tajný klíč klienta** se zobrazí stránka.

   ![Přidat tajný klíč klienta](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)
6. V **popis**, typ `PingAccess key`.
7. V části **Expires**, zvolte, jak nastavit klíč PingAccess: **Za 1 rok**, **2 roky**, nebo **nikdy**.
8. Vyberte **Přidat**. PingAccess klíče se zobrazí v tabulce tajné klíče klienta, řetězec s náhodnou této autofills v **hodnotu** pole.
9. Vedle PingAccess klíč **hodnotu** pole, vyberte **kopírování do schránky** ikonu, pak zkopírujte a uložte ho. Tuto hodnotu můžete zadat později jako tajný kód klienta PingAccess pro.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Aktualizace GraphAPI k odesílání vlastních polí (volitelné)

Pokud potřebujete vlastní deklarace identity, která odesílá další tokeny v rámci access_token používané PingAccess, nastavte `acceptMappedClaims` pole aplikace pro `True`. Můžete použít Graph Exploreru nebo manifestu aplikace na portálu Azure AD k provedení této změny.

**Tento příklad používá Graph Exploreru:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**V tomto příkladu [portálu Azure Active Directory](https://aad.portal.azure.com/) aktualizovat `acceptMappedClaims` pole:**

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikací.
2. Vyberte **Azure Active Directory** > **registrace aplikací**. Zobrazí se seznam aplikací.
3. Vyberte svou aplikaci.
4. Na postranním panelu z **registrace aplikací** stránky pro vaši aplikaci, vyberte **Manifest**. Zobrazí se kód JSON manifestu pro registraci vaší aplikace.
5. Hledat `acceptMappedClaims` pole a změňte hodnotu na `True`.
6. Vyberte **Uložit**.


### <a name="use-of-optional-claims-optional"></a>Použití nepovinných deklarací identity (volitelné)
Nepovinných deklarací identity umožňuje přidat standard-but-not-included-by-default deklarace identity, které má každý uživatel a tenanta. Úprava manifestu aplikace můžete nakonfigurovat nepovinných deklarací identity pro vaši aplikaci. Další informace najdete v tématu [pochopení článku manifestu aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Příklad zahrnout e-mailovou adresu do access_token, který bude využívat PingAccess:
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

### <a name="use-of-claims-mapping-policy-optional"></a>Použití deklarací identity mapování zásad (volitelné)
[Deklarace identity mapování zásady (preview)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties/) pro atributy, které neexistují v Azure AD. Mapování deklarací umožňuje migrace staré aplikace v místním prostředí do cloudu tak, že přidáte další vlastní deklarace identity, které využívají služby AD FS nebo uživatelské objekty

Chcete-li aplikaci pomocí vlastní deklarace identity a zahrnutí dalších polí, ujistěte se, když jste také [vytvoření vlastní deklarace identity mapování zásad a přiřazené k aplikaci](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Pokud chcete použít vlastní deklarace identity, musí mít také vlastní zásady definované a přiřazené k aplikaci. Tato zásada by měl obsahovat všechny požadované vlastní atributy.
>
> Definice zásady a přiřazení prostřednictvím Powershellu, Azure AD Graph Explorer nebo Microsoft Graphu můžete provést. Pokud provádíte je v prostředí PowerShell, budete muset nejdřív použít `New-AzureADPolicy` a přiřaďte ho k aplikaci pomocí `Add-AzureADServicePrincipalPolicy`. Další informace najdete v tématu [deklarací přiřazení zásady mapování](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Příklad:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id 
```

### <a name="enable-pingaccess-to-use-custom-claims-optional-but-required-if-you-expect-the-application-to-consume-additional-claims"></a>Povolit PingAccess používat vlastní deklarace identity (volitelné, ale vyžaduje, pokud očekáváte, že začala spotřebovávat další deklarace identity aplikace)
Když nakonfigurujete PingAccess v následujícím kroku, relace webu, který vytvoříte (Nastavení -> přístup -> webovými relacemi) musí mít **požádat o profilu** vypnutá a **aktualizovat atributy uživatele** Nastavte na **ne**

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess stáhnout a nakonfigurovat svoji aplikaci

Teď, když jste dokončili všechny kroky instalace služby Azure Active Directory, které můžete přejít ke konfiguraci PingAccess.

Podrobné pokyny k PingAccess součástí tohoto scénáře pokračovat v dokumentaci k příkazu Ping Identity. Postupujte podle pokynů v [nakonfigurovat PingAccess pro Azure AD chrání aplikace publikované pomocí Proxy aplikací služby Microsoft Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) na webové stránce Ping Identity.

Tyto kroky vám pomůžou nainstalovat PingAccess a nastavit účet PingAccess (pokud ho ještě nemáte). Potom vytvořte připojení k Azure AD OpenID Connect (OIDC), nastavíte zprostředkovatele tokenu s **ID adresáře (tenant)** hodnotu, kterou jste zkopírovali z portálu Azure AD. Dále vytvořte relaci web na PingAccess, použijete **ID aplikace (klient)** a `PingAccess key` hodnoty. Potom můžete nastavit mapování identit a vytvořit virtuální hostitel, webu nebo aplikace.

### <a name="test-your-application"></a>Testování aplikace

Po dokončení těchto kroků, vaše aplikace měla být zprovozněný. K otestování, otevřete prohlížeč a přejděte na externí adresu URL, kterou jste vytvořili při publikování aplikace v Azure. Přihlaste se pomocí testovacího účtu, který jste přiřadili k aplikaci.

## <a name="next-steps"></a>Další postup

- [Nakonfigurovat PingAccess pro Azure AD chrání aplikace publikované pomocí Proxy aplikací služby Microsoft Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md)
- [Poradce při potížích s Proxy aplikací problémy a chybové zprávy](application-proxy-troubleshoot.md)
