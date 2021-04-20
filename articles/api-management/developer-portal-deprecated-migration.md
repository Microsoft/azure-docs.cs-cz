---
title: Migrace na nový portál pro vývojáře z webu starší verze portálu pro vývojáře
titleSuffix: Azure API Management
description: Naučte se migrovat z starší verze portálu pro vývojáře na nový portál pro vývojáře v API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: e4f9f3822b58886f7d453d52402b078d8401133f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738877"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migrace na nový portál pro vývojáře

Tento článek popisuje kroky, které je třeba provést při migraci z zastaralého staršího portálu na nový portál pro vývojáře v API Management.

> [!IMPORTANT]
> Starší verze portálu pro vývojáře je nyní zastaralá a bude dostávat pouze aktualizace zabezpečení. Můžete ho i nadále používat, jak je obvyklé, do svého vyřazení v říjnu 2023, když se odebere ze všech API Management Services.

![Portál pro vývojáře API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Vylepšení na novém portálu pro vývojáře

Nový portál pro vývojáře řeší celá řada omezení nepoužívaného portálu. Nabízí [vizuální editor pro přetahování](api-management-howto-developer-portal-customize.md) , který umožňuje upravovat obsah a vyhrazený panel pro návrháře na webu. Stránky, přizpůsobení a konfigurace se ukládají jako Azure Resource Manager prostředky ve službě API Management, která umožňuje [automatizovat nasazení portálu](automate-portal-deployments.md). A nakonec je základ kódu na portálu open source, takže [ho můžete roztáhnout vlastními funkcemi](api-management-howto-developer-portal.md#managed-vs-self-hosted).

## <a name="how-to-migrate-to-new-developer-portal"></a>Postup migrace na nový portál pro vývojáře

Nový portál pro vývojáře není kompatibilní s nepoužívaným portálem a automatizovaná migrace není možná. Je potřeba ručně znovu vytvořit obsah (stránky, text, multimediální soubory) a přizpůsobit vzhled nového portálu. Přesné kroky se budou lišit v závislosti na přizpůsobení a složitosti portálu. Pokyny najdete [v kurzu na portálu pro vývojáře](api-management-howto-developer-portal-customize.md) . Zbývající konfigurace, jako je například seznam rozhraní API, produktů, uživatelů a zprostředkovatelů identity, se automaticky sdílí mezi oběma portály.

> [!IMPORTANT]
> Pokud jste nový portál pro vývojáře spustili dřív, ale neudělali jste žádné změny, obnovte výchozí obsah tak, aby se aktualizoval na nejnovější verzi.

Při migraci z zastaralého portálu mějte na paměti následující změny:

- Pokud zpřístupníte portál pro vývojáře pomocí vlastní domény, [přiřaďte](configure-custom-domain.md) k novému portálu pro vývojáře doménu. Použijte možnost **portálu pro vývojáře** z rozevíracího seznamu v Azure Portal.
- Pomocí [zásad CORS](developer-portal-faq.md#cors) v rozhraních API povolte interaktivní konzolu testů.
- Pokud vložíte vlastní šablonu stylů CSS do stylu portálu, je nutné [replikovat styl pomocí integrovaného návrhového panelu](api-management-howto-developer-portal-customize.md). Na novém portálu není povoleno vkládání šablon stylů CSS.
- Vlastní JavaScript můžete vložit jenom v [samoobslužné verzi nového portálu](api-management-howto-developer-portal.md#managed-vs-self-hosted).
- Pokud je váš API Management ve virtuální síti a je přístupný pro Internet přes Application Gateway, [Přečtěte si článek v této dokumentaci](api-management-howto-integrate-internal-vnet-appgateway.md) , kde najdete přesné kroky konfigurace. Musíte:

    - Povolte připojení ke koncovému bodu správy API Management.
    - Povolte připojení k novému koncovému bodu portálu.
    - Zakáže vybraná pravidla firewallu webových aplikací.

- Pokud jste změnili výchozí šablonu e-mailových oznámení tak, aby zahrnovala explicitně definovanou zastaralou adresu URL portálu, změňte ji na buď pomocí parametru URL portálu, nebo přejděte na adresu URL nového portálu. Pokud šablony místo toho používají vestavěný parametr URL portálu, nejsou vyžadovány žádné změny.
- *Problémy* a *aplikace* nejsou podporované na novém portálu pro vývojáře.
- Na novém portálu pro vývojáře se nepodporuje přímá integrace s Facebookem, Microsoftem, Twitterem a Google jako zprostředkovatelé identity. S těmito poskytovateli se můžete integrovat prostřednictvím Azure AD B2C.
- Pokud používáte delegování, změňte návratovou adresu URL ve vašich aplikacích a místo koncového bodu *URL pro vygenerování jednotného přihlašování* použijte [koncový bod rozhraní API pro *získání sdíleného přístupového tokenu*](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) .
- Pokud používáte Azure AD jako zprostředkovatele identity:

    - Změňte návratovou adresu URL v aplikaci tak, aby odkazovala na novou doménu portálu pro vývojáře.
    - Upravte příponu návratové adresy URL v aplikaci z `/signin-aad` na `/signin` .

- Pokud používáte Azure AD B2C jako zprostředkovatele identity:

    - Změňte návratovou adresu URL v aplikaci tak, aby odkazovala na novou doménu portálu pro vývojáře.
    - Upravte příponu návratové adresy URL v aplikaci z `/signin-aad` na `/signin` .
    - V deklaracích aplikace uveďte *křestní jméno*, *příjmení* a *ID objektu uživatele* .

- Pokud používáte OAuth 2,0 v interaktivní konzole test, změňte návratovou adresu URL v aplikaci tak, aby odkazovala na novou doménu portálu pro vývojáře a upravíte příponu:

    - Z `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` na `/signin-oauth/code/callback/[serverName]` pro pro tok udělení autorizačního kódu.
    - Z `/docs/services/[serverName]/console/oauth2/implicit/callback` na `/signin-oauth/implicit/callback` pro pro implicitní tok udělení.
- Pokud používáte OpenID Connect v interaktivní konzole test, změňte návratovou adresu URL v aplikaci tak, aby odkazovala na novou doménu portálu pro vývojáře a upravíte příponu:

    - Z `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` na `/signin-oauth/code/callback/[serverName]` pro pro tok udělení autorizačního kódu.
    - Z `/docs/services/[serverName]/console/openidconnect/implicit/callback` na `/signin-oauth/implicit/callback` pro pro implicitní tok udělení.

## <a name="next-steps"></a>Další kroky

Další informace o portálu pro vývojáře:

- [Přehled vývojářského portálu pro službu Azure API Management](api-management-howto-developer-portal.md)
- [Přístup a přizpůsobení portálu pro vývojáře](api-management-howto-developer-portal-customize.md)