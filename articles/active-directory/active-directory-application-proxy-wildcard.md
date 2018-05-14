---
title: Zástupný znak aplikace v proxy aplikace služby Azure Active Directory | Microsoft Docs
description: Další informace o použití zástupných znaků aplikace v proxy aplikace služby Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 61426d992e279cf88ae9750f0047b0cd81a797e3
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Zástupný znak aplikace v proxy aplikace služby Azure Active Directory 

V Azure Active Directory (Azure AD), konfigurace velký počet místní aplikace může být nezvladatelné a zavádí nepotřebné rizika pro chyby konfigurace, pokud řada z nich vyžaduje stejné nastavení. S [Azure AD Application Proxy](manage-apps/application-proxy.md), tento problém můžete vyřešit pomocí zástupných znaků publikování aplikací k publikování a správě mnoho aplikací najednou. Toto je řešení, které vám umožní:

-   Zjednodušení vaše režijní náklady na správu
-   Snižte počet potenciální chyby konfigurace
-   Zajistíte uživatelům zabezpečený přístup k více prostředků

Tento článek vám poskytne informace, které budete muset nakonfigurovat zástupný znak publikování aplikací ve vašem prostředí.


## <a name="create-a-wildcard-application"></a>Vytvoření aplikace zástupný znak 

Pokud máte skupinu aplikací, se stejnou konfigurací, můžete vytvořit aplikaci zástupný znak (*). Potenciální kandidáty pro aplikací se zástupnými znaky jsou aplikace pro sdílení následující nastavení:

- Skupiny uživatelů, kteří mají přístup k nim
- Metoda jednotného přihlašování
- Přístup k protokolu (http, https)

Aplikace se zástupnými znaky můžete publikovat, pokud jak interní a externí adresy URL v následujícím formátu:

> http(s)://*.\<domain\>

Například: `http(s)://*.adventure-works.com`. I interní a externí adresy URL můžete pomocí různých doménách, jako osvědčený postup, měly by být stejné. Při publikování aplikace, zobrazí chybu, pokud jeden z adresy URL nemá zástupný znak.

Pokud máte další aplikace s jinou konfiguraci nastavení, musíte publikovat tyto výjimky jako samostatné aplikace přepsat výchozí nastavení pro zástupný znak. Aplikace bez zástupný znak vždycky přednost aplikace zástupný znak. Z hlediska konfigurace jedná se o "právě" regulární aplikace.

Vytváření aplikací se zástupnými znaky je založena na stejné [publikování toku aplikace](manage-apps/application-proxy-publish-azure-portal.md) , je k dispozici pro všechny ostatní aplikace. Jediným rozdílem je, jestli jste zahrnuli zástupný znak v adresy URL a potenciálně Konfigurace jednotného přihlašování.


## <a name="prerequisites"></a>Požadavky

### <a name="custom-domains"></a>Vlastní domény

Při [vlastní domény](manage-apps/application-proxy-configure-custom-domain.md) jsou volitelné pro všechny ostatní aplikace, jsou předpokladem pro aplikace zástupný znak. Vytvoření vlastní domény vyžaduje, abyste:

1. Vytvořit ověřené domény v rámci Azure 
2. Nahrajte certifikát SSL ve formátu PFX do vašeho proxy aplikace.

Měli byste zvážit použití certifikát se zástupným znakem tak, aby odpovídala aplikaci, kterou chcete vytvořit. Alternativně můžete také použít certifikát, který je uveden pouze konkrétní aplikace. Jenom aplikace, které jsou uvedené v certifikátu v tomto případě nebude dostupný přes tuto aplikací se zástupnými znaky.

Z bezpečnostních důvodů pevný požadavek a nebude podporujeme zástupných znaků pro aplikace, které nemůže používat vlastní doménu pro externí adresu URL.

### <a name="dns-updates"></a>Aktualizace služby DNS

Pokud používáte vlastní domény, je potřeba vytvořit položku DNS s záznam CNAME pro externí adresu URL (například `*.adventure-works.com`) odkazující na externí adresu URL koncového bodu proxy aplikace. Záznam CNAME pro aplikace zástupný znak, musí odkazovat na externí adresy URL:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Potvrďte, že jste správně nakonfigurovali vaší CNAME, můžete použít [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) na cílové koncové body, například `expenses.adventure-works.com`.  Vaše odpověď by měla obsahovat již bylo uvedeno alias (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).


## <a name="considerations"></a>Požadavky


### <a name="accepted-formats"></a>Přijatý formáty

Pro aplikace zástupný znak **interní adresa URL** je třeba naformátovat jako `http(s)://*.<domain>`. 

![appId](./media/active-directory-application-proxy-wildcard\22.png)


Při konfiguraci **externí adresu URL**, musíte použít následující formát: `https://*.<custom domain>` 

![appId](./media/active-directory-application-proxy-wildcard\21.png)

Další pozice zástupného, víc zástupných znaků nebo jiných řetězců regex nejsou podporovány a způsobují chyby.


### <a name="excluding-applications-from-the-wildcard"></a>Vyloučení aplikací z zástupný znak

Aplikace můžete vyloučit z aplikací se zástupnými znaky podle

- Publikování aplikace výjimka jako regulární aplikace 
- Povolení zástupného pouze pro konkrétní aplikace prostřednictvím nastavení serveru DNS  


Publikování aplikace jako regulární aplikace je upřednostňovaná metoda k vyloučení z zástupný znak. Byste měli publikovat vyloučené aplikace před zástupný znak aplikace k zajištění, že vaše výjimky jsou vyžadována od začátku. Většina konkrétní aplikaci, bude vždy přednost – aplikace publikován jako `budgets.finance.adventure-works.com` má přednost před aplikace `*.finance.adventure-works.com`, která naopak má přednost před aplikace `*.adventure-works.com`. 

Můžete také omezit zástupného postup pouze pro konkrétní aplikace prostřednictvím vaší správy DNS. Jako osvědčený postup měli byste vytvořit záznam CNAME, který obsahuje zástupný znak a odpovídá formát externí adresu URL, které jste nakonfigurovali. Adresy URL pro konkrétní aplikaci, ale můžete místo toho přejděte na zástupné znaky. Například místo z `*.adventure-works.com`, bod `hr.adventure-works.com`, `expenses.adventure-works.com` a `travel.adventure-works.com individually` k `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Pokud použijete tuto možnost, musíte taky jiný záznam CNAME pro hodnotu `AppId.domain`, například `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, také přejdete do stejného umístění. Můžete najít **AppId** na stránce vlastnosti aplikace aplikací se zástupnými znaky:

![appId](./media/active-directory-application-proxy-wildcard\01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Nastavení adresy URL domovské stránky pro MyApps panel

Představuje aplikací se zástupnými znaky s jedním dlaždice v [MyApps panel](https://myapps.microsoft.com). Ve výchozím nastavení je skrytý na tuto dlaždici. Chcete-li zobrazit dlaždice a mají pevnou uživatelů na konkrétní stránky:

1. Postupujte podle pokynů pro [nastavení adresy URL domovské stránky](application-proxy-office365-app-launcher.md).
2. Nastavit **zobrazit aplikace** k **true** na stránce vlastností aplikace.

### <a name="kerberos-constrained-delegation"></a>Omezené delegování protokolu Kerberos

Pro aplikace pomocí [kerberos omezeného delegování (použitím KCD) jako metodu jednotného přihlašování k](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md), SPN uvedené pro metodu jednotného přihlašování také potřebovat zástupný znak. Například může mít SPN: `HTTP/*.adventure-works.com`. Potřebujete mít jednotlivé SPN nakonfigurované na back-end serverů (například `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).



## <a name="scenario-1-general-wildcard-application"></a>Scénář 1: Aplikací se zástupnými znaky obecné

V tomto scénáři máte tři různé aplikace, kterou chcete publikovat:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Všechny tři aplikace:

- Všichni uživatelé používají
- Použití *integrované ověřování systému Windows* 
- Mají stejné vlastnosti


Můžete publikovat pomocí postupu uvedeného v aplikací se zástupnými znaky [publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md). Tento scénář předpokládá:

- Klient s následujícím ID: `000aa000-11b1-2ccc-d333-4444eee4444e` 

- Volá se ověřené domény `adventure-works.com` nebyl nakonfigurován.

- A **CNAME** položku, která ukazuje `*.adventure-works.com` k `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` byla vytvořena.

Následující [popsané kroky](manage-apps/application-proxy-publish-azure-portal.md), vytvořte novou aplikaci proxy aplikace ve vašem klientovi. V tomto příkladu je zástupný znak v těchto oblastech:

- Interní adresa URL:

    ![Interní adresa URL](./media/active-directory-application-proxy-wildcard\42.png)


- Externí adresu URL:

    ![Externí adresa URL](./media/active-directory-application-proxy-wildcard\43.png)

 
- Interní aplikace hlavní název služby: 

    ![Hlavní název služby konfigurace](./media/active-directory-application-proxy-wildcard\44.png)


Tím, že publikujete aplikací se zástupnými znaky, nyní máte přístup tři aplikace tak, že přejdete na adresy URL se používá ke (například `travel.adventure-works.com`).

Konfigurace implementuje následující strukturou:

![appId](./media/active-directory-application-proxy-wildcard\05.png)

| Barva | Popis |
| ---   | ---         |
| Modrá  | Aplikace explicitně publikované a je viditelný na portálu Azure. |
| Šedá  | Aplikace můžete získat přístup prostřednictvím s nadřazenou aplikací. |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scénář 2: Obecné zástupné aplikace s výjimkou

V tomto scénáři musíte kromě toho tři obecné aplikace jiná aplikace, `finance.adventure-works.com`, který by měl mít jenom finanční dělení přístupný. Aktuální struktura aplikace finanční aplikace bude přístupný prostřednictvím aplikací se zástupnými znaky a pro všechny zaměstnance. Chcete-li toto nastavení změnit, vyloučíte aplikace z vaší zástupný znak nakonfigurováním finanční jako samostatné aplikace s víc omezující oprávnění.



Musíte zajistit, že existují záznamy CNAME této body `finance.adventure-works.com` aplikace konkrétní koncový bod, zadaný na stránce Proxy aplikací pro aplikaci. V tomto scénáři `finance.adventure-works.com` odkazuje na `https://finance-awcycles.msappproxy.net/`. 

Následující [popsané kroky](manage-apps/application-proxy-publish-azure-portal.md), tento scénář vyžaduje následující nastavení:


- V **interní adresa URL**, nastavíte **finanční** místo zástupný znak. 

    ![Interní adresa URL](./media/active-directory-application-proxy-wildcard\52.png)

- V **externí adresu URL**, nastavíte **finanční** místo zástupný znak. 

    ![Externí adresa URL](./media/active-directory-application-proxy-wildcard\53.png)

- Interní aplikace SPN nastavíte **finanční** místo zástupný znak.

    ![Hlavní název služby konfigurace](./media/active-directory-application-proxy-wildcard\54.png)


Tato konfigurace implementuje následující scénář:

![Scénář](./media/active-directory-application-proxy-wildcard\09.png)

Protože `finance.adventure-works.com` je více konkrétní adresy URL než `*.adventure-works.com`, bude mít přednost. Uživatelé, přejdete na `finance.adventure-works.com` se zkušenostmi zadaný v aplikaci finanční prostředky. V tomto případě mají mít přístup jenom zaměstnanci finančního `finance.adventure-works.com`.

Pokud máte několik aplikací publikována pro finanční a máte `finance.adventure-works.com` jako ověřené domény může publikovat jinou aplikací se zástupnými znaky `*.finance.adventure-works.com`. Protože to je specifičtější než Obecné `*.adventure-works.com`, má přednost před, pokud se uživatel připojí k aplikaci v doméně finanční.


## <a name="next-steps"></a>Další postup

Další informace o:

- **Vlastní domény**, najdete v části [práce s vlastní domény v Azure AD Application Proxy](manage-apps/application-proxy-configure-custom-domain.md).

- **Publikování aplikací**, najdete v části [publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md)


