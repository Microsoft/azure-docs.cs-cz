---
title: Ověřování založené na záhlaví pomocí příkazu PingAccess pro proxy aplikace Azure AD | Dokumenty společnosti Microsoft
description: Publikujte aplikace pomocí proxy serveru PingAccess a proxy aplikací pro podporu ověřování na základě záhlaví.
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
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367980"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Ověřování založené na záhlaví pro jednotné přihlašování pomocí proxy aplikace a pingaccessu

Proxy aplikace Azure Active Directory (Azure AD) spolupracuje s PingAccess, takže vaši zákazníci Azure AD mají přístup k dalším vašim aplikacím. PingAccess rozšiřuje [existující nabídky proxy aplikací tak,](application-proxy.md) aby zahrnovaly přístup k aplikacím s jedním přihlášením, které používají záhlaví pro ověřování.

## <a name="whats-pingaccess-for-azure-ad"></a>Co je PingAccess pro Azure AD?

S PingAccess pro Azure AD, můžete poskytnout uživatelům přístup a jednotné přihlašování (Jednotné přihlašování) k aplikacím, které používají záhlaví pro ověřování. Proxy aplikace zachází s těmito aplikacemi jako všechny ostatní, pomocí Azure AD k ověření přístupu a pak předávání provozu prostřednictvím služby konektoru. PingAccess sedí před aplikacemi a převádí přístupový token z Azure AD do záhlaví. Aplikace pak obdrží ověřování ve formátu, který může číst.

Uživatelé si při přihlášení k používání podnikových aplikací nevšimnou ničeho jiného. Stále mohou pracovat odkudkoli na jakémkoli zařízení. Konektory proxy aplikace směrují vzdálený provoz do všech aplikací bez ohledu na jejich typ ověřování, takže budou automaticky vyvažovat zatížení.

## <a name="how-do-i-get-access"></a>Jak získám přístup?

Vzhledem k tomu, že tento scénář pochází z partnerství mezi službou Azure Active Directory a PingAccess, potřebujete licence pro obě služby. Předplatná Služby Azure Active Directory Premium však zahrnují základní licenci PingAccess, která pokrývá až 20 aplikací. Pokud potřebujete publikovat více než 20 aplikací založených na záhlaví, můžete si zakoupit další licenci od aplikace PingAccess.

Další informace naleznete v [tématu Edice Služby Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikování aplikace v Azure

Tento článek je pro lidi publikovat aplikaci s tímto scénářem poprvé. Kromě podrobností o postupech publikování vás provede tím, jak začít s aplikačním proxy serverem i pingaccessem. Pokud jste už nakonfigurovali obě služby, ale chcete aktualizovat kroky publikování, přejděte na část [Přidat aplikaci do Azure AD s proxy aplikací.](#add-your-application-to-azure-ad-with-application-proxy)

> [!NOTE]
> Vzhledem k tomu, že tento scénář je partnerství mezi Azure AD a PingAccess, některé pokyny existují na webu identity ping.

### <a name="install-an-application-proxy-connector"></a>Instalace konektoru proxy aplikace

Pokud jste povolili proxy aplikace povoleno a nainstalovaný konektor již, můžete přeskočit tuto část a přejděte na [přidat aplikaci do Azure AD s proxy aplikací](#add-your-application-to-azure-ad-with-application-proxy).

Konektor proxy aplikací je služba systému Windows Server, která směruje provoz ze vzdálených zaměstnanců do publikovaných aplikací. Podrobnější pokyny k instalaci najdete [v tématu: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikací. Zobrazí se stránka **Centra pro správu Služby Azure Active Directory.**
1. Vyberte **službu** > **stažení** > **konektoru aplikace**Azure Active Directory . Zobrazí se stránka **Stažení konektoru proxy aplikace.**

   ![Konektor proxy aplikace ke stažení](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Postupujte podle pokynů k instalaci.

Stažení konektoru by mělo automaticky povolit proxy aplikace pro váš adresář, ale pokud ne, můžete vybrat **Povolit proxy aplikace**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Přidání aplikace do Azure AD pomocí proxy aplikace

Existují dvě akce, které je potřeba provést na webu Azure Portal. Nejprve je třeba publikovat aplikaci s proxy aplikací. Potom je třeba shromáždit některé informace o aplikaci, které můžete použít během pingaccess kroky.

#### <a name="publish-your-application"></a>Publikování aplikace

Nejprve budete muset publikovat svou žádost. Tato akce zahrnuje:

- Přidání místní aplikace do Azure AD
- Přiřazení uživatele k testování aplikace a výběru přihlašování založeného na záhlaví
- Nastavení adresy URL přesměrování aplikace
- Udělení oprávnění uživatelům a dalším aplikacím k použití místní aplikace

Publikování vlastní místní aplikace:

1. Pokud jste to v poslední části neudělali, přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikací.
1. Vyberte **podnikové aplikace** > **Nová aplikace** > **Přidání místní aplikace**. Zobrazí se stránka **Přidat vlastní místní aplikaci.**

   ![Přidání vlastní místní aplikace](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Vyplňte požadovaná pole s informacemi o nové aplikaci. Pro nastavení použijte níže uvedené pokyny.

   > [!NOTE]
   > Podrobnější návod k tomuto kroku najdete v [tématu Přidání místní aplikace do Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Interní adresa URL**: Obvykle zadáte adresu URL, která vás přenese na přihlašovací stránku aplikace, když jste v podnikové síti. V tomto scénáři konektor musí považovat proxy server PingAccess jako titulní stránku aplikace. Použijte tento `https://<host name of your PingAccess server>:<port>`formát: . Port je ve výchozím nastavení 3000, ale můžete jej nakonfigurovat v aplikaci PingAccess.

      > [!WARNING]
      > Pro tento typ jednotného přihlašování `https` musí interní `http`adresa URL používat a nemůže používat .

   1. **Metoda předběžného ověřování**: Zvolte **službu Azure Active Directory**.
   1. **Přeložit adresu URL v záhlaví:** Zvolte **ne**.

   > [!NOTE]
   > Pokud se jedná o první aplikaci, použijte port 3000 ke spuštění a vraťte se k aktualizaci tohoto nastavení, pokud změníte konfiguraci PingAccess. Pro následné aplikace bude port muset odpovídat naslouchací proces, který jste nakonfigurovali v PingAccess. Další informace o [naslouchacích procesech v aplikaci PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Vyberte **Přidat**. Zobrazí se stránka s přehledem nové aplikace.

Nyní přiřaďte uživatele k testování aplikací a zvolte jednotné přihlašování založené na záhlaví:

1. Na postranním panelu aplikace vyberte **Uživatelé a skupiny** > **Přidat uživatele** > **Uživatelé a skupiny (Číslo\<> vybráno).** Zobrazí se seznam uživatelů a skupin, ze kterých si můžete vybrat.

   ![Zobrazuje seznam uživatelů a skupin.](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Vyberte uživatele pro testování aplikací a vyberte **vybrat**. Ujistěte se, že tento testovací účet má přístup k místní aplikaci.
1. Vyberte **Přiřadit**.
1. Na postranním panelu aplikace vyberte **Možnost Jednopřihlápne na** > **základě záhlaví**.

   > [!TIP]
   > Pokud je to poprvé, co používáte jednotné přihlašování založené na záhlaví, musíte nainstalovat PingAccess. Chcete-li se ujistit, že vaše předplatné Azure je automaticky přidruženo k instalaci PingAccess, použijte odkaz na této stránce jednotného přihlášení ke stažení PingAccess. Nyní můžete otevřít web pro stahování nebo se vrátit na tuto stránku později.

   ![Zobrazuje přihlašovací obrazovku založenou na záhlaví a pingaccess.](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Vyberte **Uložit**.

Pak zkontrolujte, zda je adresa URL přesměrování nastavena na externí adresu URL:

1. Na postranním panelu **Centra pro správu Služby Správy Azure AD vyberte** **registrace aplikací**Azure **Active Directory** > . Zobrazí se seznam aplikací.
1. Vyberte aplikaci.
1. Vyberte odkaz vedle **přesměrování identifikátorů URI**s počtem identifikátorů URI přesměrování nastavených pro webové a veřejné klienty. Zobrazí se ** \<stránka Název aplikace> – Ověřování.**
1. Zkontrolujte, zda je externí adresa URL, kterou jste aplikaci přiřadili dříve, v seznamu **Přesměrovat identifikátory URI.** Pokud tomu tak není, přidejte externí adresu URL nyní pomocí přesměrovat uri typ **webu**a vyberte **Uložit**.

Nakonec nastavte místní aplikaci tak, aby uživatelé měli přístup pro čtení a další aplikace měly přístup pro čtení a zápis:

1. Na postranním panelu **Registrace aplikací** pro vaši aplikaci vyberte **oprávnění** > rozhraní API**Přidat oprávnění** > **Microsoft API** > **Microsoft Graph**. Zobrazí se stránka **Oprávnění rozhraní API požadavku** pro Microsoft **Graph,** která obsahuje rozhraní API pro službu Windows Azure Active Directory.

   ![Zobrazuje stránku Oprávnění rozhraní API požadavku.](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Vyberte **delegovaná oprávnění** > **User.Read****User** > .
1. Vyberte **oprávnění** > aplikace > **Application.ReadWrite.All**.**Application**
1. Vyberte **Přidat oprávnění**.
1. Na stránce **oprávnění rozhraní API** vyberte **Udělit souhlas správce s \<názvem adresáře>**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Shromažďování informací pro kroky PingAccess

Chcete-li nastavit aplikaci pomocí aplikace PingAccess, je třeba shromáždit tyto tři informace (všechny identifikátory GUID):

| Název pole Azure AD | Název pole PingAccess | Formát dat |
| --- | --- | --- |
| **ID aplikace (klienta)** | **ID klienta** | GUID |
| **ID adresáře (tenanta)** | **Emitenta** | GUID |
| `PingAccess key` | **Tajný klíč klienta** | Náhodný řetězec |

Chcete-li shromáždit tyto informace:

1. Na postranním panelu **Centra pro správu Služby Správy Azure AD vyberte** **registrace aplikací**Azure **Active Directory** > . Zobrazí se seznam aplikací.
1. Vyberte aplikaci. Zobrazí se stránka **Registrace aplikací** pro vaši aplikaci.

   ![Přehled registrace pro přihlášku](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Vedle hodnoty **ID Aplikace (klienta)** vyberte ikonu **Kopírovat do schránky** a pak ji zkopírujte a uložte. Tuto hodnotu zadáte později jako ID klienta pingaccess.
1. Vedle **ID adresáře (tenanta)** vyberte také **možnost Kopírovat do schránky**a pak ji zkopírujte a uložte. Tuto hodnotu zadáte později jako vystavitele PingAccess.
1. Na postranním panelu **registrace aplikací** pro vaši aplikaci vyberte **certifikáty a tajné klíče** > **Nový tajný klíč klienta**. Zobrazí se stránka **Přidat tajný klíč klienta.**

   ![Zobrazuje stránku <a00 000 :: Přidat tajný klíč klienta.](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Do **pole** `PingAccess key`Popis zadejte .
1. V **části Expires**zvolte, jak nastavit klávesu PingAccess: **Za 1 rok**, Za 2 **roky**nebo **Nikdy**.
1. Vyberte **Přidat**. Klíč PingAccess se zobrazí v tabulce tajných kódů klienta s náhodným řetězcem, který se automaticky vyplní v poli **HODNOTA.**
1. Vedle pole **HODNOTA** klíče PingAccess vyberte ikonu **Kopírovat do schránky** a pak ji zkopírujte a uložte. Tuto hodnotu zadáte později jako tajný klíč klienta služby PingAccess.

**Aktualizovat `acceptMappedClaims` pole:**

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) jako správce aplikací.
1. Vyberte**registrace aplikací**Azure **Active Directory** > . Zobrazí se seznam aplikací.
1. Vyberte aplikaci.
1. Na postranním panelu stránky **Registrace aplikací** pro vaši aplikaci vyberte **Manifest**. Zobrazí se kód Manifestu JSON pro registraci vaší přihlášky.
1. Vyhledejte `acceptMappedClaims` pole a změňte `True`hodnotu na .
1. Vyberte **Uložit**.

### <a name="use-of-optional-claims-optional"></a>Použití nepovinných deklarací (nepovinné)

Volitelné deklarace identity umožňují přidat standardní, ale nezahrnuté deklarace, které má každý uživatel a klient. Volitelné deklarace identity pro vaši aplikaci můžete nakonfigurovat úpravou manifestu aplikace. Další informace najdete [v článku Principy manifestu aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Příklad zahrnutí e-mailové adresy do access_token, kterou bude aplikace PingAccess využívat:
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

[Zásady mapování deklarací identity (preview)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) pro atributy, které neexistují v AzureAD. Mapování deklarací identity umožňuje migrovat staré aplikace on-prem do cloudu přidáním dalších vlastních deklarací, které jsou zálohovány vaším adfs nebo uživatelskými objekty.

Chcete-li, aby aplikace používala vlastní deklaraci identity a obsahovala další pole, ujistěte se, že jste také [vytvořili vlastní zásady mapování deklarací identity a přiřadili ji k aplikaci](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Chcete-li použít vlastní deklaraci, musíte mít také vlastní zásady definované a přiřazené k aplikaci. Tato zásada by měla zahrnovat všechny požadované vlastní atributy.
>
> Definici a přiřazení zásad můžete provést prostřednictvím prostředí PowerShell nebo Microsoft Graph. Pokud je provádíte v Prostředí PowerShell, budete `New-AzureADPolicy` pravděpodobně muset nejprve `Add-AzureADServicePrincipalPolicy`použít a pak je přiřadit k aplikaci s . Další informace naleznete v tématu [Přiřazení zásad mapování deklarací](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Příklad:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Povolit funkci PingAccess použití vlastních deklarací identity

Povolení pingaccess používat vlastní deklarace identity je volitelné, ale vyžaduje, pokud očekáváte, že aplikace využívat další deklarace identity.

Při konfiguraci aplikace PingAccess v následujícím kroku musí být v webové relaci , kterou vytvoříte (nastavení >webových relacích s přístupem >), není vybraná volba **Profil požadavku** a **je nastavena možnost Aktualizovat atributy uživatele** na **ne.**

## <a name="download-pingaccess-and-configure-your-application"></a>Stažení aplikace PingAccess a konfigurace aplikace

Teď, když jste dokončili všechny kroky nastavení služby Azure Active Directory, můžete přejít ke konfiguraci PingAccess.

Podrobné kroky pro část PingAccess tohoto scénáře pokračovat v dokumentaci identity ping. Postupujte podle pokynů v [části Konfigurace pingaccessu pro Azure AD k ochraně aplikací publikovaných pomocí proxy aplikací Microsoft Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) na webu Identity ping.

Tyto kroky vám pomohou nainstalovat PingAccess a nastavit účet PingAccess (pokud ještě nemáte jeden). Pak k vytvoření připojení Azure AD OpenID Connect (OIDC) nastavit zprostředkovatele tokenu s **Directory (tenant) ID** hodnotu, kterou jste zkopírovali z portálu Azure AD. Dále k vytvoření webové relace na PingAccess, použijte **ID aplikace (klienta)** a `PingAccess key` hodnoty. Poté můžete nastavit mapování identit a vytvořit virtuálního hostitele, webu a aplikace.

### <a name="test-your-application"></a>Testování aplikace

Po dokončení všech těchto kroků by měla být aplikace spuštěna. Chcete-li jej otestovat, otevřete prohlížeč a přejděte na externí adresu URL, kterou jste vytvořili při publikování aplikace v Azure. Přihlaste se pomocí testovacího účtu, který jste přiřadili k aplikaci.

## <a name="next-steps"></a>Další kroky

- [Konfigurace technologie PingAccess for Azure AD k ochraně aplikací publikovaných pomocí proxy aplikací Microsoft Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](what-is-single-sign-on.md)
- [Poradce při potížích s proxy aplikacemi a chybové zprávy](application-proxy-troubleshoot.md)
