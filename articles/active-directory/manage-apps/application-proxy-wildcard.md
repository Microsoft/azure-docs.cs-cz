---
title: Aplikace se zástupnými znaky v Azure Proxy aplikací služby AD
description: Další informace o použití aplikace se zástupnými znaky v proxy aplikací Azure Active Directory.
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
ms.openlocfilehash: c5a9e7be5f582051e03cba08733fcbfa697cc8f5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275037"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplikace se zástupnými znaky v proxy aplikací Azure Active Directory

V Azure Active Directory (Azure AD), konfigurace velký počet místních aplikací může být nezvladatelné a zavádí zbytečné riziko pro chyby konfigurace, pokud mnoho z nich vyžadují stejné nastavení. Pomocí služby [Azure proxy aplikací služby AD](application-proxy.md)můžete vyřešit tento problém pomocí publikování aplikací se zástupnými znaky pro publikování a správu mnoha aplikací najednou. Toto je řešení, které vám umožní:

- Zjednodušit vaše režijní náklady na správu
- Snižte počet potenciální chyby konfigurace
- Zajistíte uživatelům zabezpečený přístup k více prostředkům

Tento článek obsahuje informace, na kterých je nutné nakonfigurovat publikování aplikací zástupných znaků ve vašem prostředí.

## <a name="create-a-wildcard-application"></a>Vytvořte aplikaci se zástupnými znaky

Pokud máte skupinu aplikací se stejnou konfigurací, můžete vytvořit aplikaci se zástupnými znaky (*). Potenciální kandidáty pro aplikaci se zástupnými znaky jsou aplikace pro sdílení obsahu následující nastavení:

- Skupiny uživatelů mají přístup k nim
- Metoda jednotného přihlašování
- Přístup k protokolu (http, https)

Aplikace se zástupnými znaky můžete publikovat, pokud obě, interní a externí adresy URL jsou v následujícím formátu:

> http (s)://*.\> domény\<

Například: `http(s)://*.adventure-works.com`.

Během interní a externí adresy URL můžete použít jako osvědčený postup různých doménách, musí být stejné. Při publikování aplikace, se zobrazí chyba, pokud jedna z adres URL neobsahuje zástupný znak.

Pokud máte další aplikace s různá nastavení konfigurace, je nutné tyto výjimky publikovat jako samostatné aplikace přepsat výchozí nastavení pro zástupný znak. Aplikace bez zástupný znak vždy přednost aplikace se zástupnými znaky. Z hlediska konfigurace jsou "pouze" běžné aplikace.

Vytvoření aplikace se zástupnými znaky je založené na stejném [toku publikování aplikace](application-proxy-add-on-premises-application.md) , který je k dispozici pro všechny ostatní aplikace. Jediným rozdílem je, že složku zahrnujete zástupné znaky adresy URL a potenciálně Konfigurace jednotného přihlašování.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, ujistěte se, že jste splnili tyto požadavky.

### <a name="custom-domains"></a>Vlastní domény

I když jsou [vlastní domény](application-proxy-configure-custom-domain.md) volitelné pro všechny ostatní aplikace, jsou předpokladem pro aplikace se zástupnými znaky. Vytváří se vlastní domény vyžaduje, abyste:

1. Vytvořte ověřenou doménu v rámci Azure.
1. Nahrajte certifikát SSL ve formátu PFX na vaše proxy aplikací.

Měli byste zvážit použití certifikát se zástupným znakem tak, aby odpovídaly aplikaci, kterou chcete vytvořit. Alternativně můžete také použít certifikát, který je uveden pouze konkrétní aplikace. Jenom aplikace uvedené v certifikátu v tomto případě bude přístupné prostřednictvím této aplikaci se zástupnými znaky.

Z bezpečnostních důvodů se jedná o striktní požadavek a nebude podporuje zástupné znaky pro aplikace, které nelze použít vlastní doménu pro externí adresu URL.

### <a name="dns-updates"></a>Aktualizace služby DNS

Při použití vlastních domén potřebujete vytvořit záznam DNS s záznamem CNAME pro externí adresu URL (například `*.adventure-works.com`) odkazující na externí adresu URL koncového bodu proxy aplikace. V případě aplikací se zástupnými znaky musí záznam CNAME ukazovat na příslušné externí adresy URL:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Chcete-li ověřit, že je váš záznam CNAME správně nakonfigurovaný, můžete použít nástroj [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) v jednom z cílových koncových bodů, například `expenses.adventure-works.com`.  Vaše odpověď by měla zahrnovat již zmíněný alias (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).

## <a name="considerations"></a>Požadavky

Tady je několik důležitých informací, které byste měli vzít v úvahu pro aplikace se zástupnými znaky.

### <a name="accepted-formats"></a>Přijímané formáty

V případě aplikací se zástupnými znaky musí být **interní adresa URL** formátována jako `http(s)://*.<domain>`.

![Pro interní adresu URL použijte formát http (s)://*. > domény\<](./media/application-proxy-wildcard/22.png)

Při konfiguraci **externí adresy URL**je nutné použít následující formát: `https://*.<custom domain>`

![Pro externí adresu URL použijte formát https://*.\<vlastní > domény](./media/application-proxy-wildcard/21.png)

Další pozice zástupný znak, více zástupných znaků nebo jiných řetězců regulárního výrazu nejsou podporovány a jsou příčinou chyby.

### <a name="excluding-applications-from-the-wildcard"></a>Vyloučení aplikací z zástupný znak

Aplikace můžete vyloučit z aplikací se zástupnými znaky podle

- Publikování aplikace výjimek jako regulární aplikace
- Povolení zástupný znak pouze pro určité aplikace prostřednictvím nastavení serveru DNS

Publikování aplikace jako regulární aplikace je upřednostňovanou metodou k vyloučení z zástupný znak. Měli byste publikovat vyloučené aplikace před aplikace se zástupnými znaky zajistit, že vaše výjimky se vynucují od začátku. Většina konkrétní aplikace bude vždycky mít přednost – aplikace publikovaná jako `budgets.finance.adventure-works.com` má přednost před `*.finance.adventure-works.com`aplikace, která zase má přednost před `*.adventure-works.com`em aplikace.

Můžete také omezit zástupný znak a fungují jenom pro určité aplikace přes správu DNS. Jako osvědčený postup byste měli vytvořit záznam CNAME, který obsahuje zástupný znak a odpovídá formátu externí adresu URL, které jste nakonfigurovali. Adresy URL konkrétní aplikace, ale můžete místo toho přejděte na zástupné znaky. Například místo `*.adventure-works.com`, najeďte `hr.adventure-works.com`, `expenses.adventure-works.com` a `travel.adventure-works.com individually` na `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Pokud použijete tuto možnost, budete také potřebovat jinou položku CNAME pro hodnotu `AppId.domain`, například `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, na stejné umístění. **AppID** můžete najít na stránce vlastností aplikace v aplikaci se zástupnými znaky:

![Najít ID aplikace na stránce vlastností aplikace](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Adresa URL domovské stránky pro MyApps panel nastavení

Aplikace se zástupnými znaky je reprezentována pouze jednou dlaždicí na [panelu MyApp](https://myapps.microsoft.com). Ve výchozím nastavení je skrytý tuto dlaždici. Zobrazit dlaždici a pozemního uživatelů na konkrétní stránce:

1. Postupujte podle pokynů pro [Nastavení adresy URL domovské stránky](application-proxy-configure-custom-home-page.md).
1. Na stránce Vlastnosti aplikace nastavte **Zobrazit aplikaci** na **hodnotu true** .

### <a name="kerberos-constrained-delegation"></a>Omezené delegování protokolu Kerberos

Pro aplikace, které používají [jako metodu jednotného přihlašování omezené delegování protokolu Kerberos (KCD)](application-proxy-configure-single-sign-on-with-kcd.md), může název SPN uvedený pro metodu jednotného přihlašování také vyžadovat zástupný znak. Hlavní název služby (SPN) může například: `HTTP/*.adventure-works.com`. Na back-end serverech pořád potřebujete nakonfigurovat jednotlivé hlavní názvy služby (například `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Scénář 1: Aplikaci se zástupnými znaky obecné

V tomto scénáři máte tři různé aplikace, které chcete publikovat:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Všechny tři aplikace:

- Používají všichni uživatelé
- Použít *integrované ověřování systému Windows*
- Mají stejné vlastnosti

Pomocí kroků uvedených v části [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md)můžete publikovat aplikaci se zástupnými znaky. Tento scénář předpokládá:

- Tenant s následujícím ID: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Byla nakonfigurována ověřená doména s názvem `adventure-works.com`.
- Byl vytvořen záznam **CNAME** , který odkazuje `*.adventure-works.com` na `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Podle [dokumentovaných kroků](application-proxy-add-on-premises-application.md)vytvoříte novou aplikaci proxy aplikací ve vašem tenantovi. V tomto příkladu je zástupný znak do následujících polí:

- Interní adresa URL:

    ![Příklad: zástupný znak v interní adrese URL](./media/application-proxy-wildcard/42.png)

- Externí adresa URL:

    ![Příklad: zástupný znak v externí adrese URL](./media/application-proxy-wildcard/43.png)

- Interní aplikace hlavní název služby:

    ![Příklad: zástupný znak v konfiguraci hlavního názvu služby](./media/application-proxy-wildcard/44.png)

Publikováním aplikace se zástupnými znaky teď můžete k vašim třem aplikacím přistupovat tak, že přejdete na adresy URL, na které jste se použili (například `travel.adventure-works.com`).

Konfigurace implementuje následující strukturu:

![Zobrazuje strukturu implementovanou ukázkovou konfigurací.](./media/application-proxy-wildcard/05.png)

| Barva | Popis |
| ---   | ---         |
| Modrá  | Aplikace jsou explicitně publikované a viditelné v Azure Portal. |
| Šedá  | Aplikace můžete přístupné prostřednictvím nadřazená aplikace. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scénář 2: Aplikaci se zástupnými znaky obecné s výjimkou

V tomto scénáři se kromě tří obecných aplikací zobrazí jiná aplikace, `finance.adventure-works.com`, která by měla být přístupná jenom pro finanční rozdělení. Aktuální struktura aplikace finanční aplikace bude přístupný prostřednictvím aplikací se zástupnými znaky a všichni zaměstnanci. Pro toto nastavení změnit, vyloučíte vaší aplikace z vašeho zástupné nakonfigurováním Finance jako samostatné aplikace s víc omezující oprávnění.

Je nutné zajistit, aby záznamy CNAME existovaly, které odkazují `finance.adventure-works.com` na koncový bod konkrétní aplikace zadaný na stránce proxy aplikace pro danou aplikaci. V tomto scénáři `finance.adventure-works.com` odkazuje na `https://finance-awcycles.msappproxy.net/`.

Podle [dokumentovaných kroků](application-proxy-add-on-premises-application.md)tento scénář vyžaduje následující nastavení:

- V **interní adrese URL**jste místo zástupného znaku nastavili **finance** .

    ![Příklad: nastavte finance místo zástupného znaku na interní adrese URL.](./media/application-proxy-wildcard/52.png)

- V **externí adrese URL**jste místo zástupného znaku nastavili **finance** .

    ![Příklad: nastavte finance místo zástupného znaku v externí adrese URL.](./media/application-proxy-wildcard/53.png)

- Vnitřní hlavní název aplikace, který jste nastavili jako **finance** , místo zástupného znaku.

    ![Příklad: nastavte finance místo zástupného znaku v konfiguraci hlavního názvu služby (SPN).](./media/application-proxy-wildcard/54.png)

Tato konfigurace implementuje následující scénář:

![Zobrazuje konfiguraci implementovanou ukázkovým scénářem.](./media/application-proxy-wildcard/09.png)

Vzhledem k tomu, že `finance.adventure-works.com` je konkrétnější adresa URL než `*.adventure-works.com`, má přednost. Uživatelé, kteří přejdou na `finance.adventure-works.com`, mají zkušenosti zadané v aplikaci finanční prostředky. V takovém případě budou mít přístup k `finance.adventure-works.com`jenom zaměstnanci s financemi.

Pokud máte k dispozici více aplikací publikovaných pro finance a máte `finance.adventure-works.com` jako ověřenou doménu, můžete `*.finance.adventure-works.com`jinou aplikaci se zástupnými znaky publikovat. Vzhledem k tomu, že je to konkrétnější než obecné `*.adventure-works.com`, má přednost, pokud uživatel přistupuje k aplikaci v doméně finance.

## <a name="next-steps"></a>Další kroky

- Další informace o **vlastních doménách**najdete v tématu [práce s vlastními doménami v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).
- Další informace o **publikování aplikací**najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md)
