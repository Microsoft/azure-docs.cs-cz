---
title: Aplikace se zástupnými kódy v proxy aplikace Azure AD
description: Zjistěte, jak používat aplikace se zástupnými kódy v proxy serveru aplikace Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e5861e802f39adecb5661bc17c22b432f137d59
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770302"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplikace se zástupnými kódy v proxy serveru aplikace Azure Active Directory

Ve službě Azure Active Directory (Azure AD) se konfigurace velkého počtu místních aplikací může rychle stát nespravovatelnými a představuje zbytečná rizika pro chyby konfigurace, pokud mnoho z nich vyžaduje stejné nastavení. Pomocí [proxy aplikací Azure AD](application-proxy.md)můžete tento problém vyřešit pomocí publikování aplikací se zástupnými kódy k publikování a správě mnoha aplikací najednou. Toto je řešení, které vám umožní:

- Zjednodušení administrativní režie
- Snížení počtu potenciálních chyb konfigurace
- Umožněte uživatelům bezpečný přístup k více prostředkům

Tento článek obsahuje informace, které potřebujete ke konfiguraci publikování aplikace se zástupnými kódy ve vašem prostředí.

## <a name="create-a-wildcard-application"></a>Vytvoření aplikace se zástupnými kódy

Pokud máte skupinu aplikací se stejnou konfigurací, můžete vytvořit aplikaci se zástupnými kódy (*). Potenciálními kandidáty pro aplikaci se zástupnými symboly jsou aplikace sdílející následující nastavení:

- Skupina uživatelů, kteří k nim mají přístup
- Metoda spřimit sazby
- Přístupový protokol (http, https)

Aplikace se zástupnými znaky můžete publikovat, pokud jsou interní i externí adresy URL v následujícím formátu:

> http(s)://*. \<doména\>

Například: `http(s)://*.adventure-works.com`.

Zatímco interní a externí adresy URL mohou používat různé domény, jako osvědčený postup by měly být stejné. Při publikování aplikace se zobrazí chyba, pokud jedna z adres URL nemá zástupný znak.

Vytvoření aplikace se zástupnými kódy je založeno na stejném [toku publikování aplikace,](application-proxy-add-on-premises-application.md) který je k dispozici pro všechny ostatní aplikace. Jediným rozdílem je, že do adres URL a potenciálně konfigurace přiřazování a přihrádky zahrnete zástupný znak.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, ujistěte se, že jste tyto požadavky splnili.

### <a name="custom-domains"></a>Vlastní domény

Zatímco [vlastní domény](application-proxy-configure-custom-domain.md) jsou volitelné pro všechny ostatní aplikace, jsou předpokladem pro zástupné aplikace. Vytváření vlastních domén vyžaduje:

1. Vytvořte ověřenou doménu v rámci Azure.
1. Nahrajte certifikát TLS/SSL ve formátu PFX do proxy aplikace.

Měli byste zvážit použití certifikátu se zástupnými kódy tak, aby odpovídal aplikaci, kterou chcete vytvořit. Alternativně můžete také použít certifikát, který obsahuje pouze konkrétní aplikace. V takovém případě budou prostřednictvím této zástupné aplikace přístupné pouze aplikace uvedené v certifikátu.

Z bezpečnostních důvodů se jedná o pevný požadavek a nebudeme podporovat zástupné znaky pro aplikace, které nemohou používat vlastní doménu pro externí adresu URL.

### <a name="dns-updates"></a>Aktualizace DNS

Při použití vlastních domén je třeba vytvořit položku DNS se záznamem CNAME pro externí adresu URL `*.adventure-works.com`(například) směřující na externí adresu URL koncového bodu proxy aplikace. U aplikací se zástupnými symboly musí záznam CNAME překážet na příslušné externí adresy URL:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Chcete-li potvrdit, že jste správně nakonfigurovali cname, můžete použít [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) na jednom z cílových koncových bodů, `expenses.adventure-works.com`například .  Vaše odpověď by měla obsahovat`<yourAADTenantId>.tenant.runtime.msappproxy.net`již zmíněný alias ( ).

## <a name="considerations"></a>Požadavky

Zde jsou některé důležité informace, které byste měli vzít v úvahu pro zástupné aplikace.

### <a name="accepted-formats"></a>Přijaté formáty

U aplikací se zástupnými symboly musí `http(s)://*.<domain>`být interní adresa **URL** formátována jako .

![Pro interní adresu URL použijte formát http(s)://*. \<> domény](./media/application-proxy-wildcard/22.png)

Při konfiguraci **externí adresy URL**je nutné použít následující formát:`https://*.<custom domain>`

![Pro externí adresu URL použijte formát https://*. \<vlastní> domény](./media/application-proxy-wildcard/21.png)

Jiné pozice zástupných znaků, více zástupných znaků nebo jiných řetězců regulárních výrazů nejsou podporovány a způsobují chyby.

### <a name="excluding-applications-from-the-wildcard"></a>Vyloučení aplikací ze zástupné hospo-

Aplikaci můžete vyloučit z aplikace se zástupnými symboly

- Publikování aplikace výjimky jako běžné aplikace
- Povolení zástupných symbolů pouze pro určité aplikace prostřednictvím nastavení DNS

Publikování aplikace jako běžné aplikace je upřednostňovanou metodou pro vyloučení aplikace ze zástupného znaku. Měli byste publikovat vyloučené aplikace před zástupnými symboly aplikace zajistit, že vaše výjimky jsou vynuceny od začátku. Nejkonkrétnější aplikace bude mít vždy přednost – `budgets.finance.adventure-works.com` aplikace publikovaná tak, jak má přednost před `*.finance.adventure-works.com` `*.adventure-works.com`aplikací , která má zase přednost před aplikací .

Zástupný znak můžete také omezit tak, aby fungoval pouze pro určité aplikace prostřednictvím správy DNS. Jako osvědčený postup byste měli vytvořit položku CNAME, která obsahuje zástupný znak a odpovídá formátu externí adresy URL, kterou jste nakonfigurovali. Místo toho však můžete na identifikátory ZÁSTUPNÝCH znaků nasměrovat konkrétní adresy URL aplikace. Například místo `*.adventure-works.com`, `hr.adventure-works.com`point `expenses.adventure-works.com` `travel.adventure-works.com individually` a `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Pokud použijete tuto možnost, budete také potřebovat `AppId.domain`jinou položku CNAME pro hodnotu , například `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, také ukazuje na stejné umístění. **AppId** najdete na stránce vlastností aplikace aplikace se zástupnými kódy:

![Vyhledání ID aplikace na stránce vlastností aplikace](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Nastavení adresy URL domovské stránky pro panel MyApps

Aplikace se zástupnými symboly je v [panelu MyApps](https://myapps.microsoft.com)reprezentována pouze jednou dlaždicí . Ve výchozím nastavení je tato dlaždice skrytá. Chcete-li dlaždice zobrazit a mít uživatele přistát na konkrétní stránce:

1. Postupujte podle pokynů pro [nastavení adresy URL domovské stránky](application-proxy-configure-custom-home-page.md).
1. Na stránce vlastností aplikace nastavte **zobrazit aplikaci** na **hodnotu true.**

### <a name="kerberos-constrained-delegation"></a>Delegování omezenou protokolem Kerberos

Pro aplikace, které používají [delegování s omezenou hodnotou kerberos (KCD) jako metodu s přihlašování](application-proxy-configure-single-sign-on-with-kcd.md)k systému sdílení certifikátu , může hlavní seznam spn uvedený pro metodu s přihlašováním k systému ovou systému také vyžadovat zástupný znak. Například hlavní odkaz může `HTTP/*.adventure-works.com`být: . Stále musíte mít jednotlivé názvy SPN nakonfigurované `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com`na serverech back-end (například ).

## <a name="scenario-1-general-wildcard-application"></a>Scénář 1: Obecná zástupná aplikace

V tomto scénáři máte tři různé aplikace, které chcete publikovat:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Všechny tři aplikace:

- Jsou používány všemi uživateli
- Použití *integrovaného ověřování systému Windows*
- Mají stejné vlastnosti

Aplikaci se zástupnými kódy můžete publikovat pomocí kroků popsaných v [části Publikovat aplikace pomocí proxy aplikace Azure AD](application-proxy-add-on-premises-application.md). Tento scénář předpokládá:

- Tenant s následujícím ID:`000aa000-11b1-2ccc-d333-4444eee4444e`
- Byla nakonfigurována ověřená doména volaná. `adventure-works.com`
- Položka **CNAME,** `*.adventure-works.com` na `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` kterou odkazuje, byla vytvořena.

Podle [zdokumentovaných kroků](application-proxy-add-on-premises-application.md)vytvoříte novou aplikaci proxy aplikace ve vašem tenantovi. V tomto příkladu je zástupný znak v následujících polích:

- Interní adresa URL:

    ![Příklad: Zástupný znak v interní adrese URL](./media/application-proxy-wildcard/42.png)

- Externí adresa URL:

    ![Příklad: Zástupný znak v externí adrese URL](./media/application-proxy-wildcard/43.png)

- Název nového nového nebo nového název služby interní aplikace:

    ![Příklad: Zástupný znak v konfiguraci služby SPN](./media/application-proxy-wildcard/44.png)

Publikováním aplikace se zástupnými symboly můžete nyní přistupovat ke třem aplikacím tak, `travel.adventure-works.com`že přejdete na adresy URL, na které jste zvyklí (například ).

Konfigurace implementuje následující strukturu:

![Zobrazuje strukturu implementovanou ukázkovou konfigurací.](./media/application-proxy-wildcard/05.png)

| Barvy | Popis |
| ---   | ---         |
| Blue  | Aplikace explicitně publikované a viditelné na webu Azure Portal. |
| Šedé  | Aplikace, které můžete zpřístupnit prostřednictvím nadřazené aplikace. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scénář 2: Obecná zástupná aplikace s výjimkou

V tomto scénáři máte kromě tří obecných `finance.adventure-works.com`aplikací jinou aplikaci , která by měla být přístupná pouze oddělením Finance. S aktuální strukturou aplikace by vaše finanční aplikace byla přístupná prostřednictvím aplikace se zástupnými kódy a všemi zaměstnanci. Chcete-li to změnit, vyloučíte aplikaci ze zástupného znaku konfigurací aplikace Finance jako samostatné aplikace s více omezujícími oprávněními.

Musíte se ujistit, že existují záznamy `finance.adventure-works.com` CNAME, které odkazují na koncový bod specifický pro aplikaci, určený na stránce Proxy aplikace pro aplikaci. V tomto `finance.adventure-works.com` scénáři `https://finance-awcycles.msappproxy.net/`odkazuje na .

Následující kroky [zdokumentované](application-proxy-add-on-premises-application.md), tento scénář vyžaduje následující nastavení:

- V **interní adrese URL**nastavíte místo zástupného znaku **finance.**

    ![Příklad: Nastavení financí místo zástupného znaku v interní adrese URL](./media/application-proxy-wildcard/52.png)

- V **externí adrese URL**nastavíte místo zástupného znaku **finance.**

    ![Příklad: Nastavení financí místo zástupného znaku v externí adrese URL](./media/application-proxy-wildcard/53.png)

- Název SPN interní aplikace nastavíte **finance** namísto zástupného znaku.

    ![Příklad: Nastavení financí místo zástupného znaku v konfiguraci služby SPN](./media/application-proxy-wildcard/54.png)

Tato konfigurace implementuje následující scénář:

![Zobrazuje konfiguraci implementovanou ukázkový mů.](./media/application-proxy-wildcard/09.png)

Protože `finance.adventure-works.com` je konkrétnější adresa `*.adventure-works.com`URL než , má přednost. Uživatelé navigaci `finance.adventure-works.com` mít zkušenosti zadané v aplikaci Finanční prostředky. V tomto případě mají přístup `finance.adventure-works.com`pouze finanční zaměstnanci .

Pokud máte více aplikací publikovaných pro `finance.adventure-works.com` finance a máte jako ověřenou `*.finance.adventure-works.com`doménu, můžete publikovat jinou aplikaci se zástupnými kódy . Vzhledem k tomu, `*.adventure-works.com`že je konkrétnější než obecný , má přednost, pokud uživatel přistupuje k aplikaci v doméně finance.

## <a name="next-steps"></a>Další kroky

- Další informace o **vlastních doménách**najdete [v tématu Práce s vlastními doménami v proxy aplikací azure ad](application-proxy-configure-custom-domain.md).
- Další informace o **publikování aplikací**najdete [v tématu Publikování aplikací pomocí proxy aplikací Azure AD](application-proxy-add-on-premises-application.md)
