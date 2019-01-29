---
title: Aplikace se zástupnými znaky v proxy aplikací Azure Active Directory | Dokumentace Microsoftu
description: Další informace o použití aplikace se zástupnými znaky v proxy aplikací Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 0130e0ea32253de8be5eb9f36e15a08e15625a46
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192804"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplikace se zástupnými znaky v proxy aplikací Azure Active Directory 

V Azure Active Directory (Azure AD), konfigurace velký počet místních aplikací může být nezvladatelné a zavádí zbytečné riziko pro chyby konfigurace, pokud mnoho z nich vyžadují stejné nastavení. S [Azure AD Application Proxy](application-proxy.md), tento problém můžete vyřešit pomocí zástupných znaků aplikace publikování publikovat a spravovat mnoho aplikací najednou. Toto je řešení, které vám umožní:

-   Zjednodušit vaše režijní náklady na správu
-   Snižte počet potenciální chyby konfigurace
-   Zajistíte uživatelům zabezpečený přístup k více prostředkům

Tento článek obsahuje informace, na kterých je nutné nakonfigurovat publikování aplikací zástupných znaků ve vašem prostředí.


## <a name="create-a-wildcard-application"></a>Vytvořte aplikaci se zástupnými znaky 

Pokud máte skupinu aplikací se stejnou konfigurací, můžete vytvořit aplikaci se zástupnými znaky (*). Potenciální kandidáty pro aplikaci se zástupnými znaky jsou aplikace pro sdílení obsahu následující nastavení:

- Skupiny uživatelů mají přístup k nim
- Metoda jednotného přihlašování
- Přístup k protokolu (http, https)

Aplikace se zástupnými znaky můžete publikovat, pokud obě, interní a externí adresy URL jsou v následujícím formátu:

> http(s)://*.\<domain\>

Například: `http(s)://*.adventure-works.com`. Během interní a externí adresy URL můžete použít jako osvědčený postup různých doménách, musí být stejné. Při publikování aplikace, se zobrazí chyba, pokud jedna z adres URL neobsahuje zástupný znak.

Pokud máte další aplikace s různá nastavení konfigurace, je nutné tyto výjimky publikovat jako samostatné aplikace přepsat výchozí nastavení pro zástupný znak. Aplikace bez zástupný znak vždy přednost aplikace se zástupnými znaky. Z hlediska konfigurace jsou "pouze" běžné aplikace.

Vytváření aplikací se zástupnými znaky je založena na stejném [publikování toku aplikace](application-proxy-add-on-premises-application.md) , který je k dispozici pro všechny ostatní aplikace. Jediným rozdílem je, že složku zahrnujete zástupné znaky adresy URL a potenciálně Konfigurace jednotného přihlašování.


## <a name="prerequisites"></a>Požadavky

### <a name="custom-domains"></a>Vlastní domény

Zatímco [vlastních domén](application-proxy-configure-custom-domain.md) jsou volitelné pro všechny ostatní aplikace, jsou předpokladem pro aplikace se zástupnými znaky. Vytváří se vlastní domény vyžaduje, abyste:

1. Vytvořit ověřené domény v Azure 
2. Nahrajte certifikát SSL ve formátu PFX na vaše proxy aplikací.

Měli byste zvážit použití certifikát se zástupným znakem tak, aby odpovídaly aplikaci, kterou chcete vytvořit. Alternativně můžete také použít certifikát, který je uveden pouze konkrétní aplikace. Jenom aplikace uvedené v certifikátu v tomto případě bude přístupné prostřednictvím této aplikaci se zástupnými znaky.

Z bezpečnostních důvodů se jedná o striktní požadavek a nebude podporuje zástupné znaky pro aplikace, které nelze použít vlastní doménu pro externí adresu URL.

### <a name="dns-updates"></a>Aktualizace služby DNS

Pokud používáte vlastní domény, budete muset vytvořit záznam DNS pomocí záznamu CNAME pro externí adresu URL (například `*.adventure-works.com`) odkazující na externí adresu URL koncového bodu proxy aplikace. Záznam CNAME pro aplikace se zástupnými znaky musí odkazovat na externí adresy URL:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Potvrďte, že jste správně nakonfigurovali váš záznam CNAME, můžete použít [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) na jednom z cílové koncové body, například `expenses.adventure-works.com`.  Vaše odpověď by měla obsahovat jsme už uvedli alias (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).


## <a name="considerations"></a>Požadavky


### <a name="accepted-formats"></a>Přijímané formáty

Pro aplikace se zástupnými znaky **interní adresa URL** musí být ve formátu `http(s)://*.<domain>`. 

![ID aplikace](./media/application-proxy-wildcard/22.png)


Při konfiguraci **externí adresa URL**, je nutné použít následující formát: `https://*.<custom domain>` 

![ID aplikace](./media/application-proxy-wildcard/21.png)

Další pozice zástupný znak, více zástupných znaků nebo jiných řetězců regulárního výrazu nejsou podporovány a jsou příčinou chyby.


### <a name="excluding-applications-from-the-wildcard"></a>Vyloučení aplikací z zástupný znak

Aplikace můžete vyloučit z aplikací se zástupnými znaky podle

- Publikování aplikace výjimek jako regulární aplikace 
- Povolení zástupný znak pouze pro určité aplikace prostřednictvím nastavení serveru DNS  


Publikování aplikace jako regulární aplikace je upřednostňovanou metodou k vyloučení z zástupný znak. Měli byste publikovat vyloučené aplikace před aplikace se zástupnými znaky zajistit, že vaše výjimky se vynucují od začátku. Nejspecifičtější aplikaci bude vždy přednost – publikovat jako aplikaci `budgets.finance.adventure-works.com` má přednost před aplikace `*.finance.adventure-works.com`, která naopak má přednost před aplikace `*.adventure-works.com`. 

Můžete také omezit zástupný znak a fungují jenom pro určité aplikace přes správu DNS. Jako osvědčený postup byste měli vytvořit záznam CNAME, který obsahuje zástupný znak a odpovídá formátu externí adresu URL, které jste nakonfigurovali. Adresy URL konkrétní aplikace, ale můžete místo toho přejděte na zástupné znaky. Například namísto z `*.adventure-works.com`, bodu `hr.adventure-works.com`, `expenses.adventure-works.com` a `travel.adventure-works.com individually` k `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Pokud použijete tuto možnost, budete také potřebovat jiný záznam CNAME pro hodnotu `AppId.domain`, například `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`také Unified do stejného umístění. Můžete najít **AppId** na stránce vlastnosti aplikace aplikací se zástupnými znaky:

![ID aplikace](./media/application-proxy-wildcard/01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Adresa URL domovské stránky pro MyApps panel nastavení

Aplikaci se zástupnými znaky je reprezentován jedním dlaždice v [MyApps panel](https://myapps.microsoft.com). Ve výchozím nastavení je skrytý tuto dlaždici. Zobrazit dlaždici a pozemního uživatelů na konkrétní stránce:

1. Postupujte podle pokynů pro [nastavení adresa URL domovské stránky](application-proxy-configure-custom-home-page.md).
2. Nastavte **zobrazit aplikace** k **true** na stránce vlastností aplikace.

### <a name="kerberos-constrained-delegation"></a>Omezené delegování protokolu Kerberos

Pro aplikace využívající [kerberos omezeného delegování (KCD) jako metodu jednotného přihlašování](application-proxy-configure-single-sign-on-with-kcd.md), hlavní název služby uvedené pro metodu jednotného přihlašování možná bude nutné také zástupný znak. Například může být hlavní název služby: `HTTP/*.adventure-works.com`. Potřebujete mít jednotlivé nakonfigurované na back-end serverech hlavní názvy služby (například `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).



## <a name="scenario-1-general-wildcard-application"></a>Scénář 1: Aplikaci se zástupnými znaky obecné

V tomto scénáři máte tři různé aplikace, které chcete publikovat:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Všechny tři aplikace:

- Používají všichni uživatelé
- Použití *integrované ověřování Windows* 
- Mají stejné vlastnosti


Můžete publikovat pomocí postupu uvedeného v aplikací se zástupnými znaky [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-add-on-premises-application.md). Tento scénář předpokládá:

- Tenant s následujícím ID: `000aa000-11b1-2ccc-d333-4444eee4444e` 

- Volá ověřenou doménou `adventure-works.com` není nakonfigurovaná.

- A **CNAME** položku, která odkazuje `*.adventure-works.com` k `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` se vytvořil.

Následující [popsané kroky](application-proxy-add-on-premises-application.md), vytvořte novou aplikaci proxy aplikací ve vašem tenantovi. V tomto příkladu je zástupný znak do následujících polí:

- Interní adresa URL:

    ![Interní adresa URL](./media/application-proxy-wildcard/42.png)


- Externí adresa URL:

    ![Externí adresa URL](./media/application-proxy-wildcard/43.png)

 
- Interní aplikace hlavní název služby: 

    ![Konfigurace hlavního názvu služby](./media/application-proxy-wildcard/44.png)


Publikováním aplikací se zástupnými znaky můžete nyní přístup tři aplikace tak, že přejdete na adresy URL, které se používají pro (například `travel.adventure-works.com`).

Konfigurace implementuje následující strukturu:

![ID aplikace](./media/application-proxy-wildcard/05.png)

| Barva | Popis |
| ---   | ---         |
| Modrá  | Aplikace explicitně publikované a je viditelný na webu Azure Portal. |
| Šedá  | Aplikace můžete přístupné prostřednictvím nadřazená aplikace. |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scénář 2: Aplikaci se zástupnými znaky obecné s výjimkou

V tomto scénáři musíte kromě toho tři hlavní aplikace jiná aplikace `finance.adventure-works.com`, které by měly být dostupné jenom finanční oddělení. Aktuální struktura aplikace finanční aplikace bude přístupný prostřednictvím aplikací se zástupnými znaky a všichni zaměstnanci. Pro toto nastavení změnit, vyloučíte vaší aplikace z vašeho zástupné nakonfigurováním Finance jako samostatné aplikace s víc omezující oprávnění.



Budete potřebovat, abyste měli jistotu, že existují záznamy CNAME, na kterou odkazuje `finance.adventure-works.com` do aplikace konkrétní zadaného koncového bodu, na stránce Proxy aplikací pro aplikaci. V tomto scénáři `finance.adventure-works.com` odkazuje na `https://finance-awcycles.msappproxy.net/`. 

Následující [popsané kroky](application-proxy-add-on-premises-application.md), tento scénář vyžaduje následující nastavení:


- V **interní adresa URL**, nastavíte **finance** místo zástupné znaky. 

    ![Interní adresa URL](./media/application-proxy-wildcard/52.png)

- V **externí adresa URL**, nastavíte **finance** místo zástupné znaky. 

    ![Externí adresa URL](./media/application-proxy-wildcard/53.png)

- Vnitřní hlavní název služby aplikace nastavíte **finance** místo zástupné znaky.

    ![Konfigurace hlavního názvu služby](./media/application-proxy-wildcard/54.png)


Tato konfigurace implementuje následující scénář:

![Scénář](./media/application-proxy-wildcard/09.png)

Protože `finance.adventure-works.com` je adresa URL konkrétnější než `*.adventure-works.com`, má přednost. Uživatelé, že přejdete na `finance.adventure-works.com` mají prostředí podle prostředků finanční aplikace. V tomto případě jsou mít přístup jenom zaměstnanci finančního `finance.adventure-works.com`.

Pokud máte více aplikací publikovaných pro finance a máte `finance.adventure-works.com` jako ověřenou doménu, můžete publikovat aplikaci se zástupnými znaky jiné `*.finance.adventure-works.com`. Protože se jedná konkrétnější než Obecné `*.adventure-works.com`, má přednost, pokud uživatel přistupuje k aplikaci v doméně finance.


## <a name="next-steps"></a>Další postup

Další informace o:

- **Vlastní domény**, naleznete v tématu [práce s vlastními doménami v Proxy aplikací Azure AD](application-proxy-configure-custom-domain.md).

- **Publikování aplikací**, naleznete v tématu [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-add-on-premises-application.md)


