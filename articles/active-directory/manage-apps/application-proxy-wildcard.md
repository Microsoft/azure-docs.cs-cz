---
title: Aplikace se zástupnými znaky v Azure Proxy aplikací služby AD
description: Naučte se používat aplikace se zástupnými znaky v proxy aplikace Azure Active Directory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770302"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplikace se zástupnými znaky v proxy aplikace Azure Active Directory

V Azure Active Directory (Azure AD) se může stát, že konfigurace velkého počtu místních aplikací rychle přestane být spravovatelná a přináší nepotřebná rizika pro chyby v konfiguraci, pokud jich mnoho vyžaduje stejné nastavení. Pomocí služby [Azure proxy aplikací služby AD](application-proxy.md)můžete vyřešit tento problém pomocí publikování aplikací se zástupnými znaky pro publikování a správu mnoha aplikací najednou. Toto řešení umožňuje:

- Zjednodušení administrativních režijních nákladů
- Snižte počet potenciálních chyb konfigurace
- Umožněte uživatelům zabezpečený přístup k více prostředkům.

Tento článek poskytuje informace, které potřebujete ke konfiguraci publikování aplikací se zástupnými znaky ve vašem prostředí.

## <a name="create-a-wildcard-application"></a>Vytvoření aplikace se zástupnými znaky

Můžete vytvořit zástupnou aplikaci (*), pokud máte skupinu aplikací se stejnou konfigurací. Možné kandidáty pro aplikaci se zástupnými znaky jsou aplikace sdílející následující nastavení:

- Skupina uživatelů, kteří k nim mají přístup
- Metoda jednotného přihlašování
- Přístupový protokol (http, https)

Můžete publikovat aplikace se zástupnými znaky, pokud jsou obě, interní i externí adresy URL v následujícím formátu:

> http (s)://*. \<doména\>

Například: `http(s)://*.adventure-works.com`.

I když interní a externí adresy URL můžou používat jiné domény, doporučuje se, aby byly stejné. Při publikování aplikace se zobrazí chyba, pokud jedna z adres URL neobsahuje zástupný znak.

Vytvoření aplikace se zástupnými znaky je založené na stejném [toku publikování aplikace](application-proxy-add-on-premises-application.md) , který je k dispozici pro všechny ostatní aplikace. Jediným rozdílem je, že v adresách URL zahrnete zástupný znak a případně konfiguraci jednotného přihlašování.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, ujistěte se, že jste splnili tyto požadavky.

### <a name="custom-domains"></a>Vlastní domény

I když jsou [vlastní domény](application-proxy-configure-custom-domain.md) volitelné pro všechny ostatní aplikace, jsou předpokladem pro aplikace se zástupnými znaky. Vytváření vlastních domén vyžaduje:

1. Vytvořte ověřenou doménu v rámci Azure.
1. Nahrajte do svého proxy aplikace certifikát TLS/SSL ve formátu PFX.

Doporučujeme použít certifikát se zástupnými znaky, aby se aplikace, kterou plánujete vytvořit, shodovala. Případně můžete také použít certifikát, který obsahuje pouze určité aplikace. V takovém případě budou prostřednictvím této aplikace se zástupnými znaky přístupné jenom aplikace uvedené v certifikátu.

Z bezpečnostních důvodů je to pevný požadavek a nebudeme podporovat zástupné znaky pro aplikace, které nemůžou pro externí adresu URL používat vlastní doménu.

### <a name="dns-updates"></a>Aktualizace DNS

Při použití vlastních domén potřebujete vytvořit záznam DNS s záznamem CNAME pro externí adresu URL (například `*.adventure-works.com`) odkazující na externí adresu URL koncového bodu proxy aplikace. V případě aplikací se zástupnými znaky musí záznam CNAME ukazovat na příslušné externí adresy URL:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Pokud chcete potvrdit, že jste svůj záznam CNAME správně nakonfigurovali, můžete použít nástroj [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) v jednom z cílových koncových bodů `expenses.adventure-works.com`, například.  Vaše odpověď by měla zahrnovat už zmíněný alias`<yourAADTenantId>.tenant.runtime.msappproxy.net`().

## <a name="considerations"></a>Požadavky

Tady je několik důležitých informací, které byste měli vzít v úvahu pro aplikace se zástupnými znaky.

### <a name="accepted-formats"></a>Přijaté formáty

V případě aplikací se zástupnými znaky musí být **interní adresa URL** formátována jako `http(s)://*.<domain>`.

![Pro interní adresu URL použijte formát http (s)://*. \<> domény](./media/application-proxy-wildcard/22.png)

Při konfiguraci **externí adresy URL**je nutné použít následující formát:`https://*.<custom domain>`

![Pro externí adresu URL použijte formát https://*. \<vlastní doména>](./media/application-proxy-wildcard/21.png)

Jiné pozice zástupného znaku, vícenásobné zástupné znaky nebo jiné řetězce regulárního výrazu nejsou podporovány a způsobují chyby.

### <a name="excluding-applications-from-the-wildcard"></a>Vyloučení aplikací ze zástupného znaku

Aplikaci můžete z aplikace se zástupnými znaky vyloučit pomocí

- Publikování aplikace výjimky jako běžné aplikace
- Povolení zástupných znaků pro konkrétní aplikace prostřednictvím nastavení DNS

Publikování aplikace jako běžné aplikace je upřednostňovanou metodou pro vyloučení aplikace ze zástupného znaku. Vyloučené aplikace byste měli před aplikacemi se zástupnými znaky publikovat, aby se zajistilo, že se vaše výjimky vynutily od začátku. Většina konkrétní aplikace bude vždycky mít přednost – aplikace publikovaná jako `budgets.finance.adventure-works.com` má přednost před aplikací `*.finance.adventure-works.com`, která zase má přednost před aplikací. `*.adventure-works.com`

Zástupný znak můžete také omezit tak, aby fungoval jenom pro konkrétní aplikace přes správu DNS. V souladu s osvědčeným postupem byste měli vytvořit záznam CNAME, který obsahuje zástupný znak a bude odpovídat formátu externí adresy URL, kterou jste nakonfigurovali. Místo toho ale můžete na zástupné znaky odkazovat na konkrétní adresy URL aplikace. Například namísto `*.adventure-works.com`, `hr.adventure-works.com`nasměrování `expenses.adventure-works.com` a `travel.adventure-works.com individually` na. `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`

Použijete-li tuto možnost, budete také potřebovat jinou položku CNAME pro hodnotu `AppId.domain`, například `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, na stejné umístění. **AppID** můžete najít na stránce vlastností aplikace v aplikaci se zástupnými znaky:

![Najít ID aplikace na stránce vlastností aplikace](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Nastavení adresy URL domovské stránky pro panel MyApp

Aplikace se zástupnými znaky je reprezentována pouze jednou dlaždicí na [panelu MyApp](https://myapps.microsoft.com). Ve výchozím nastavení je tato dlaždice skrytá. Zobrazení dlaždice a její využívání uživateli na konkrétní stránce:

1. Postupujte podle pokynů pro [Nastavení adresy URL domovské stránky](application-proxy-configure-custom-home-page.md).
1. Na stránce Vlastnosti aplikace nastavte **Zobrazit aplikaci** na **hodnotu true** .

### <a name="kerberos-constrained-delegation"></a>Omezené delegování protokolu Kerberos

Pro aplikace, které používají [jako metodu jednotného přihlašování omezené delegování protokolu Kerberos (KCD)](application-proxy-configure-single-sign-on-with-kcd.md), může název SPN uvedený pro metodu jednotného přihlašování také vyžadovat zástupný znak. Například hlavní název služby (SPN) může `HTTP/*.adventure-works.com`být:. Na back-end serverech pořád potřebujete nakonfigurovat jednotlivé hlavní názvy služby (například `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Scénář 1: Obecná aplikace se zástupnými znaky

V tomto scénáři máte tři různé aplikace, které chcete publikovat:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Všechny tři aplikace:

- Používají všichni vaši uživatelé
- Použít *integrované ověřování systému Windows*
- Mají stejné vlastnosti

Pomocí kroků uvedených v části [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md)můžete publikovat aplikaci se zástupnými znaky. Tento scénář předpokládá:

- Tenant s následujícím ID:`000aa000-11b1-2ccc-d333-4444eee4444e`
- Byla nakonfigurována ověřená `adventure-works.com` doména s názvem.
- Záznam **CNAME** , který odkazuje `*.adventure-works.com` na `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` , byl vytvořen.

Podle [dokumentovaných kroků](application-proxy-add-on-premises-application.md)vytvoříte novou aplikaci proxy aplikací ve vašem tenantovi. V tomto příkladu je zástupný znak v následujících polích:

- Interní adresa URL:

    ![Příklad: zástupný znak v interní adrese URL](./media/application-proxy-wildcard/42.png)

- Externí adresa URL:

    ![Příklad: zástupný znak v externí adrese URL](./media/application-proxy-wildcard/43.png)

- Hlavní název služby (SPN) aplikace:

    ![Příklad: zástupný znak v konfiguraci hlavního názvu služby](./media/application-proxy-wildcard/44.png)

Publikováním aplikace se zástupnými znaky teď můžete k vašim třem aplikacím přistupovat tak, že přejdete na adresy URL, na které jste `travel.adventure-works.com`se použili (například).

Konfigurace implementuje následující strukturu:

![Zobrazuje strukturu implementovanou ukázkovou konfigurací.](./media/application-proxy-wildcard/05.png)

| Barvy | Popis |
| ---   | ---         |
| Blue  | Aplikace jsou explicitně publikované a viditelné v Azure Portal. |
| Les  | Aplikace, ke kterým můžete přistupovat přes nadřazenou aplikaci. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scénář 2: Obecná aplikace se zástupnými znaky s výjimkou

V tomto scénáři se kromě tří obecných aplikací zobrazí jiná aplikace, `finance.adventure-works.com`která by měla být přístupná pouze pro finanční oddělení. S aktuální aplikační strukturou bude vaše finanční aplikace přístupná prostřednictvím aplikace se zástupnými znaky a všemi zaměstnanci. Pokud to chcete změnit, vyloučíte svou aplikaci ze zástupného znaku tak, že nakonfigurujete finance jako samostatnou aplikaci s více omezujícími oprávněními.

Musíte zajistit, aby existovaly záznamy CNAME, které odkazují `finance.adventure-works.com` na koncový bod specifický pro aplikaci, zadaný na stránce proxy aplikace pro danou aplikaci. V tomto scénáři `finance.adventure-works.com` odkazuje na `https://finance-awcycles.msappproxy.net/`.

Podle [dokumentovaných kroků](application-proxy-add-on-premises-application.md)tento scénář vyžaduje následující nastavení:

- V **interní adrese URL**jste místo zástupného znaku nastavili **finance** .

    ![Příklad: nastavte finance místo zástupného znaku na interní adrese URL.](./media/application-proxy-wildcard/52.png)

- V **externí adrese URL**jste místo zástupného znaku nastavili **finance** .

    ![Příklad: nastavte finance místo zástupného znaku v externí adrese URL.](./media/application-proxy-wildcard/53.png)

- Vnitřní hlavní název aplikace, který jste nastavili jako **finance** , místo zástupného znaku.

    ![Příklad: nastavte finance místo zástupného znaku v konfiguraci hlavního názvu služby (SPN).](./media/application-proxy-wildcard/54.png)

Tato konfigurace implementuje následující scénář:

![Zobrazuje konfiguraci implementovanou ukázkovým scénářem.](./media/application-proxy-wildcard/09.png)

Vzhledem `finance.adventure-works.com` k tomu, že je konkrétnější `*.adventure-works.com`adresa URL než, má přednost. Uživatelé přejdou, `finance.adventure-works.com` aby měli zkušenosti zadané v aplikaci finanční prostředky. V takovém případě mají přístup `finance.adventure-works.com`pouze zaměstnanci.

Pokud máte k dispozici více aplikací publikovaných pro finance `finance.adventure-works.com` a máte jako ověřenou doménu, můžete publikovat jinou aplikaci `*.finance.adventure-works.com`se zástupnými znaky. Vzhledem k tomu, že je to konkrétnější `*.adventure-works.com`než obecné, má přednost, pokud uživatel přistupuje k aplikaci v doméně finance.

## <a name="next-steps"></a>Další kroky

- Další informace o **vlastních doménách**najdete v tématu [práce s vlastními doménami v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).
- Další informace o **publikování aplikací**najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md)
