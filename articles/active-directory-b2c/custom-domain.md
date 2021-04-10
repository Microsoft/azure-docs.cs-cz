---
title: Povolit Azure AD B2C vlastní domény
titleSuffix: Azure AD B2C
description: Naučte se, jak povolit vlastní domény v adresách URL pro přesměrování pro Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2de419885938b27ebce4a934db5ef966965b3dbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580160"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Povolení vlastních domén pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Tento článek popisuje, jak povolit vlastní domény v adresách URL pro přesměrování pro Azure Active Directory B2C (Azure AD B2C). Používání vlastní domény s vaší aplikací nabízí ucelenější uživatelské prostředí. Z perspektivy uživatele zůstanou v doméně během procesu přihlašování místo přesměrování na Azure AD B2C výchozí doménu *<název tenanta>. b2clogin.com*.

![Uživatelské prostředí pro vlastní doménu](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Přehled vlastní domény

Pomocí front-bran [Azure](https://azure.microsoft.com/services/frontdoor/)můžete povolit vlastní domény pro Azure AD B2C. Přední dvířka Azure jsou globální, škálovatelný vstupní bod, který využívá síť Microsoft Global Edge k vytváření rychlých, zabezpečených a široce škálovatelných webových aplikací. Můžete vykreslovat Azure AD B2C obsah za předními dvířky Azure a potom nakonfigurovat možnost v frontách Azure pro doručování obsahu prostřednictvím vlastní domény v adrese URL vaší aplikace.

Následující diagram znázorňuje integraci služby Azure front-dveří:

1. V aplikaci uživatel klikne na tlačítko pro přihlášení, které převezme přihlašovací stránku Azure AD B2C. Tato stránka určuje vlastní název domény.
1. Webový prohlížeč přeloží název vlastní domény na IP adresu front-dveří Azure. Během překladu názvů DNS se záznam kanonického názvu (CNAME) s vlastním názvem domény odkazuje na front-end výchozího hostitele (například `contoso.azurefd.net` ). 
1. Provoz, který je adresován do vlastní domény (například `login.contoso.com` ), je směrován do zadaného výchozího front-end hostitele ( `contoso.azurefd.net` ).
1. Přední dvířka Azure vyvolají Azure AD B2C obsah pomocí Azure AD B2C `<tenant-name>.b2clogin.com` výchozí domény. Požadavek na koncový bod Azure AD B2C zahrnuje vlastní hlavičku HTTP, která obsahuje původní název vlastní domény.
1. Azure AD B2C odpoví na požadavek zobrazením relevantního obsahu a původní vlastní domény.

![Diagram vlastních doménových sítí](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> Připojení z prohlížeče do front Azure by mělo vždycky místo protokolu IPv6 používat protokol IPv4.

Při používání vlastních domén Vezměte v úvahu následující skutečnosti:

- Můžete nastavit několik vlastních domén. Maximální počet podporovaných vlastních domén najdete v tématu [limity a omezení služby Azure AD](../active-directory/enterprise-users/directory-service-limits-restrictions.md) pro Azure AD B2C a [limity, kvóty a omezení předplatného a služby](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) Azure pro přední dveře Azure.
- Přední dvířka Azure jsou samostatná služba Azure, takže se účtují další poplatky. Další informace najdete v tématu [ceny za přední dveře](https://azure.microsoft.com/pricing/details/frontdoor).
- Pokud chcete používat [Firewall webových aplikací](../web-application-firewall/afds/afds-overview.md)přes Azure, musíte potvrdit, že konfigurace a pravidla brány firewall fungují správně s vašimi uživatelskými Azure AD B2C toky.
- Po nakonfigurování vlastních domén budou uživatelé i nadále mít přístup k Azure AD B2C výchozímu názvu domény *<název tenanta>. b2clogin.com* (Pokud nepoužíváte vlastní zásadu a [zablokujete přístup](#block-access-to-the-default-domain-name).
- Pokud máte více aplikací, proveďte jejich migraci do vlastní domény, protože prohlížeč ukládá relaci Azure AD B2C pod aktuálně používaný název domény.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>Přidání vlastního názvu domény do tenanta

Postupujte podle pokynů pro [Přidání a ověření vlastní domény ve službě Azure AD](../active-directory/fundamentals/add-custom-domain.md). Po ověření domény odstraňte záznam TXT DNS, který jste vytvořili.

> [!IMPORTANT]
> V případě těchto kroků se ujistěte, že se přihlásíte ke svému klientovi **Azure AD B2C** a vyberete službu **Azure Active Directory** .

Ověřte každou subdoménu, kterou plánujete použít. Ověření pouze domény nejvyšší úrovně nestačí. Pokud se například chcete přihlašovat pomocí *Login.contoso.com* a *account.contoso.com*, je nutné ověřit obě subdomény, nikoli pouze *contoso.com* domény.  

## <a name="create-a-new-azure-front-door-instance"></a>Vytvoření nové instance front-dveří Azure

Postupujte podle kroků pro [Vytvoření přední obrazovky pro aplikaci](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) s použitím výchozích nastavení pro hostitele front-endu a pravidla směrování. 

> [!IMPORTANT]
> V případě těchto kroků se po přihlášení k Azure Portal v kroku 1 vyberte **adresář + předplatné** a zvolte adresář, který obsahuje předplatné Azure, které chcete použít pro přední dveře Azure. *Nemělo by se* jednat o adresář, který obsahuje vašeho tenanta Azure AD B2C. 

V kroku **přidejte back-end**, použijte následující nastavení:

* Jako **typ hostitele back-endu** vyberte **vlastní hostitel**.  
* Pro **název hostitele back-endu** vyberte název hostitele pro svůj koncový bod Azure AD B2C <název tenanta>. b2clogin.com. Například contoso.b2clogin.com. 
* V části **Hlavička hostitele back-endu** vyberte stejnou hodnotu, jakou jste vybrali pro **název hostitele back-endu**.

![Přidání back-endu](./media/custom-domain/add-a-backend.png)

Po přidání **back-endu** do **back-endového fondu** zakažte **sondy stavu**.

![Přidání back-endového fondu](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Nastavení vlastní domény na předních dveřích Azure

Použijte postup [Přidání vlastní domény do předních dveří](../frontdoor/front-door-custom-domain.md). Při vytváření `CNAME` záznamu pro vlastní doménu použijte vlastní název domény, který jste předtím ověřili v kroku [Přidání vlastního názvu domény do vašeho kroku Azure AD](#add-a-custom-domain-name-to-your-tenant) . 

Po ověření vlastního názvu domény vyberte **vlastní název domény HTTPS**. Pak v části **typ správy certifikátů** vyberte [Správa front-dveří](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)nebo [použijte vlastní certifikát](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). 

Následující snímek obrazovky ukazuje, jak přidat vlastní doménu a povolit HTTPS pomocí certifikátu Azure front-dveří.

![Nastavení vlastní domény Azure pro front-dvířka](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>Konfigurace CORS

Pokud [přizpůsobíte Azure AD B2C uživatelské rozhraní](customize-ui-with-html.md) šablonou HTML, budete muset [nakonfigurovat CORS](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) s vlastní doménou.

Nakonfigurujte službu Azure Blob Storage pro sdílení prostředků mezi zdroji pomocí následujících kroků:

1. Na webu [Azure Portal](https://portal.azure.com) přejděte ke svému účtu úložiště.
1. V nabídce vyberte **CORS**.
1. V případě **povolených zdrojů** zadejte `https://your-domain-name` . Nahraďte `your-domain-name` názvem domény. Například, `https://login.contoso.com`. Při zadávání názvu tenanta použijte všechna malá písmena.
1. U **povolených metod** vyberte obojí `GET` a `OPTIONS` .
1. U **povolených hlaviček** zadejte hvězdičku (*).
1. U **zveřejněných hlaviček** zadejte hvězdičku (*).
1. Do **maximálního stáří** zadejte 200.
1. Vyberte **Uložit**.

## <a name="configure-your-identity-provider"></a>Konfigurace poskytovatele identity

Když se uživatel rozhodne přihlásit pomocí zprostředkovatele sociální identity, Azure AD B2C zahájí žádost o autorizaci a převezme uživatele vybraného zprostředkovatele identity, aby dokončil proces přihlášení. Žádost o autorizaci Určuje `redirect_uri` s názvem Azure AD B2C výchozí doména: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Pokud jste zásady nakonfigurovali tak, aby povolovaly přihlášení pomocí externího zprostředkovatele identity, aktualizujte identifikátory URI přesměrování OAuth s vlastní doménou. Většina zprostředkovatelů identity umožňuje registrovat více identifikátorů URI pro přesměrování. Doporučujeme přidat identifikátory URI přesměrování místo jejich nahrazení, abyste mohli testovat vlastní zásady, aniž by to ovlivnilo aplikace, které používají Azure AD B2C výchozí název domény. 

V následujícím identifikátoru URI přesměrování:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Pomocí vlastního názvu domény nahraďte **<Custom-Domain-name>** .
- Nahraďte **<název tenanta>** názvem vašeho TENANTA nebo ID tenanta.

Následující příklad ukazuje platný identifikátor URI přesměrování OAuth:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Pokud se rozhodnete použít [ID tenanta](#optional-use-tenant-id), platný identifikátor URI přesměrování OAuth by vypadal jako následující:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

Metadata [zprostředkovatele identity SAML](saml-identity-provider-technical-profile.md) by vypadala takto:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>Testování vlastní domény

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **toky uživatelů (zásady)**.
1. Vyberte tok uživatele a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na možnost **Kopírovat do schránky**.

    ![Zkopírování identifikátoru URI žádosti o autorizaci](./media/custom-domain/user-flow-run-now.png)

1. V adrese URL **koncového bodu toku uživatele** nahraďte Azure AD B2C doménu (<tenant-Name>. b2clogin.com) vlastní doménou.  
    Například namísto:

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    použije

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. Vyberte **Spustit tok uživatele**. Vaše zásada Azure AD B2C by se měla načíst.
1. Přihlaste se pomocí místních i sociálních účtů.
1. Opakujte test se zbytkem vašich zásad.

## <a name="configure-your-application"></a>Konfigurace aplikace 

Jakmile nakonfigurujete a otestujete vlastní doménu, můžete aktualizovat aplikace tak, aby se jako název hostitele místo domény Azure AD B2C načetla adresa URL, která určuje vaši vlastní doménu. 

Integrace vlastních domén se vztahuje na koncové body ověřování, které používají zásady Azure AD B2C (uživatelské toky nebo vlastní zásady) k ověřování uživatelů. Tyto koncové body můžou vypadat takto:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Nahraďte:
- **vlastní** doména s vlastní doménou
- **název tenanta** s vaším jménem TENANTA nebo ID tenanta
- **název zásady** s názvem zásady. [Přečtěte si další informace o Azure AD B2Cch zásadách](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


Metadata [poskytovatele služby SAML](./saml-service-provider.md) můžou vypadat takto: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>Volitelné Použít ID tenanta

Název tenanta B2C můžete v adrese URL nahradit identifikátorem GUID ID vašeho tenanta tak, aby se v adrese URL odebraly všechny odkazy na "B2C". Identifikátor GUID ID tenanta najdete na stránce Přehled B2C v Azure Portal.
Můžete třeba změnit `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` na `https://account.contosobank.co.uk/<tenant ID GUID>/`.

Pokud se rozhodnete použít ID tenanta místo názvu tenanta, nezapomeňte odpovídajícím způsobem aktualizovat **identifikátor URI přesměrování** zprostředkovatele identity. Další informace najdete v tématu [Konfigurace poskytovatele identity](#configure-your-identity-provider).

### <a name="token-issuance"></a>Vystavení tokenu

V závislosti na použité vlastní doméně se mění deklarace identity vydavatele tokenu (ISS). Například:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Zablokovat přístup k výchozímu názvu domény

Po přidání vlastní domény a konfiguraci aplikace budou uživatelé stále mít přístup k <název tenanta>. b2clogin.com. Abyste zabránili přístupu, můžete nakonfigurovat zásady tak, aby kontrolovaly žádost o autorizaci "název hostitele" na seznamu povolených domén. Název hostitele je název domény, který se zobrazí v adrese URL. Název hostitele je k dispozici prostřednictvím `{Context:HostName}` [překladačů deklarací identity](claim-resolver-overview.md). Pak můžete zobrazit vlastní chybovou zprávu. 

1. Získejte příklad zásady podmíněného přístupu, která kontroluje název hostitele z [GitHubu](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. V každém souboru nahraďte řetězec `yourtenant` názvem vašeho tenanta Azure AD B2C. Například pokud je název vašeho tenanta B2C *contosob2c*, všechny instance `yourtenant.onmicrosoft.com` se stanou `contosob2c.onmicrosoft.com` .
1. Soubory zásad nahrajte v následujícím pořadí: `B2C_1A_TrustFrameworkExtensions_HostName.xml` a pak `B2C_1A_signup_signin_HostName.xml` .

::: zone-end

## <a name="troubleshooting"></a>Řešení potíží

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C vrátí chybu nenalezené stránky.

- **Příznak** – po nakonfigurování vlastní domény se při pokusu o přihlášení pomocí vlastní domény zobrazí chybová zpráva HTTP 404.
- **Možné příčiny** : Tento problém může souviset s konfigurací DNS nebo konfigurací back-endu služby Azure front. 
- **Řešení**:  
    1. Ujistěte se, že je vlastní doména [zaregistrovaná a úspěšně ověřená](#add-a-custom-domain-name-to-your-tenant) ve vašem tenantovi Azure AD B2C.
    1. Ujistěte se, že je správně nakonfigurovaná [vlastní doména](../frontdoor/front-door-custom-domain.md) . `CNAME`Záznam pro vaši vlastní doménu musí ukazovat na výchozí hostitele front-endu Azure (například contoso.azurefd.NET).
    1. Zajistěte, aby [konfigurace back-endu služby Azure front-endu](#set-up-your-custom-domain-on-azure-front-door) odkazovala na tenanta, ve kterém jste nastavili vlastní název domény a kde se ukládají uživatelské toky nebo vlastní zásady.

### <a name="identify-provider-returns-an-error"></a>Identifikace poskytovatele vrátí chybu.

- **Příznak** – Jakmile nakonfigurujete vlastní doménu, budete se moct přihlásit pomocí místních účtů. Když se ale přihlašujete s přihlašovacími údaji z externích [poskytovatelů sociálních nebo podnikových identit](add-identity-provider.md), zprostředkovatelé identity zobrazí chybovou zprávu.
- **Možné příčiny** – když Azure AD B2C převezme uživatele, aby se přihlásil pomocí federovaného poskytovatele identity, určí identifikátor URI pro přesměrování. Identifikátor URI přesměrování je koncový bod, do kterého poskytovatel identity vrací token. Identifikátor URI přesměrování je stejná doména, jakou vaše aplikace používá, s požadavkem na autorizaci. Pokud identifikátor URI pro přesměrování ještě není zaregistrovaný ve zprostředkovateli identity, může nedůvěřovat novému identifikátoru URI přesměrování, což má za následek chybovou zprávu. 
- **Řešení** – podle kroků v části [Konfigurace zprostředkovatele identity](#configure-your-identity-provider) přidejte nový identifikátor URI pro přesměrování. 


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Můžu použít pokročilou konfiguraci služby Azure pro front-dveří, třeba *pravidla firewallu webových aplikací*? 
  
I když nejsou v oficiálních nastaveních rozšířené konfigurace služby Azure, můžete je použít na vlastní riziko. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Proč se při pokusu o spuštění moje zásady používá spustit nyní, proč nemůžu zobrazit vlastní doménu?

Zkopírujte adresu URL, změňte název domény ručně a pak ji vložte zpět do prohlížeče.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Která IP adresa se zobrazí Azure AD B2C? IP adresa uživatele nebo IP adresa front-dveří Azure?

Přední dvířka Azure přecházejí do původní IP adresy uživatele. Tato IP adresa se zobrazí v části vytváření sestav auditu nebo ve vašich vlastních zásadách.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>Můžu použít Firewall webových aplikací třetích stran (WAF) s B2C?

Pokud chcete používat vlastní bránu firewall webových aplikací před předními dvířky Azure, musíte nakonfigurovat a ověřit, že vše funguje správně s Azure AD B2Cmi toky uživatelů.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [autorizačních žádostech OAuth](protocols-overview.md).