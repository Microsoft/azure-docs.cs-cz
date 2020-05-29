---
title: Vyhodnocování průběžného přístupu v Azure AD
description: Rychlejší reakce na změny v stavech uživatelů pomocí vyhodnocení průběžného přístupu v Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf904bb2c0d133ea07cd32274fad5b6601da5d9
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148119"
---
# <a name="continuous-access-evaluation"></a>Nepřetržité vyhodnocování přístupu

Služby Microsoftu, jako jsou Azure Active Directory (Azure AD) a Office 365, umožňují maximalizovat interoperabilitu pomocí otevřených standardů a protokolů. Jedním z nejdůležitějších z nich je Open ID Connect (OIDC). Když se klientská aplikace, jako je Outlook, připojí ke službě, jako je Exchange Online, požadavky rozhraní API se autorizují pomocí přístupových tokenů OAuth 2,0. Ve výchozím nastavení jsou tyto přístupové tokeny platné po dobu jedné hodiny. Po vypršení platnosti se klient přesměruje zpátky do služby Azure AD, aby je aktualizoval. To také umožňuje znovu vyhodnotit zásady pro přístup uživatelů – můžeme se rozhodnout neaktualizovat token z důvodu zásad podmíněného přístupu nebo proto, že uživatel byl v adresáři zakázán. 

Vypršení platnosti tokenu a aktualizace je standardní mechanismus v oboru. Proto si zákazníci vyjádřili obavy o prodlevu mezi tím, kdy se u uživatele změní rizikové podmínky (například přesun z firemní pobočky do místní kavárny nebo přihlašovací údaje uživatele zjištěné na černém trhu) a kdy je možné zásady uplatnit v souvislosti s touto změnou. Povedli jsme experimenty s přístupem "tupý objekt" s omezenou životností tokenu, ale zjistili jsme, že můžou snížit činnost uživatelů a spolehlivost bez eliminace rizik.

Včasná reakce na porušení zásad nebo problémy se zabezpečením vyžadují "konverzaci" mezi vystavitelem tokenu, jako je Azure AD, a předávající stranou, jako je Exchange Online. Tato obousměrná konverzace nabízí dvě důležité možnosti. Předávající strana si může všimnout, že se změnily nějaké věci, třeba když klient přichází z nového umístění, a sdělí vystaviteli tokenů. Také dává vydavateli tokenů způsob, jak říct předávající straně, aby zastavila dodržování tokenů pro daného uživatele z důvodu ohrožení bezpečnosti, zákazu nebo jiných obav. Mechanismus pro tuto konverzaci je vyhodnocování nepřetržitého přístupu (CAE).

Microsoft byl prvním účastníkem v iniciativě protokolu CAEP (Continuous Evaluation Protocol), který je součástí [sdílených signálů a](https://openid.net/wg/sse/) pracovních skupin událostí na OpenID Foundation. Zprostředkovatelé identity a předávající strany budou moci využívat události zabezpečení a signály definované pracovní skupinou k reautorizaci nebo ukončení přístupu. Je skvělé pracovat a vylepšuje zabezpečení napříč mnoha platformami a aplikacemi.

Vzhledem k tomu, že výhody zabezpečení jsou tak skvělé, zavádíme pro souběžnou práci v rámci norem základní implementaci specifickou pro společnost Microsoft. Vzhledem k tomu, že pracujeme na nasazení těchto funkcí CAE (continuoed Access Evaluation) napříč službami Microsoftu, zjistili jsme spoustu informací a sdílíme s nimi tyto informace s komunitou standardů. Doufáme, že naše prostředí při nasazení může přispět i k lepšímu používání standardu a po případnému uplatnění tohoto standardu se zavazuje, že budou využívat všechny zúčastněné služby.

## <a name="how-does-cae-work-in-microsoft-services"></a>Jak funguje CAE ve službách Microsoftu?

Zaměřujeme se na naši počáteční implementaci vyhodnocování průběžného přístupu na Exchange a týmy. Doufáme, že budeme v budoucnu rozšiřovat podporu dalších služeb Microsoftu. Začneme začínat vyhodnocování průběžného přístupu jenom pro klienty bez zásad podmíněného přístupu. Naše studijní materiály z této fáze CAE budeme používat k informování našeho průběžného zavedení CAE.

## <a name="service-side-requirements"></a>Požadavky na stranu služby

Vyhodnocování průběžného přístupu se implementuje povolením služeb (poskytovatelé prostředků) k přihlášení k odběru kritických událostí ve službě Azure AD, aby se tyto události vyhodnotily a vynutily téměř v reálném čase. V tomto počátečním zavedení CAE se budou vymáhat tyto události:

- Uživatelský účet je odstraněný nebo zakázaný.
- Heslo pro uživatele se změnilo nebo resetuje.
- Správce explicitně odvolá všechny aktualizační tokeny pro uživatele.
- Azure AD Identity Protection zjištěno zvýšené riziko uživatele

V budoucnu se doufáme, že přidáte další události, včetně událostí, jako jsou například změny umístění a stavu zařízení. **I když je náš cíl vynucení okamžité, může být v některých případech latence až 15 minut v důsledku doby šíření události zjištěna**. 

## <a name="client-side-claim-challenge"></a>Výzva k deklaracím na straně klienta

Před vyhodnocením průběžného přístupu by se klienti vždy pokusili přehrát přístupový token z mezipaměti, dokud nevypršela jeho platnost. V CAE zavádíme nový případ, který poskytovatel prostředků může odmítnout token i v případě, že nevypršela jeho platnost. Aby klienti informovali o objednání své mezipaměti i v případě, že nevypršela platnost tokenů uložených v mezipaměti, zavádíme mechanismus s názvem **Challenge Challenge**. CAE vyžaduje aktualizaci klienta pro pochopení výzvy deklarací identity. Výzva pro deklaraci identity podporuje nejnovější verzi následujících aplikací:

- Outlook pro Windows 
- Outlook pro iOS 
- Outlook pro Android 
- Outlook pro Mac 
- Týmy pro Windows
- Týmy pro iOS 
- Týmy pro Android 
- Týmy pro Mac 

## <a name="token-lifetime"></a>Živostnost tokenu

Vzhledem k tomu, že rizika a zásady jsou vyhodnocovány v reálném čase, klienti, kteří vyjednají relace s průběžným vyhodnocováním přístupu, se budou spoléhat na CAE namísto existujících zásad životnosti statického přístupového tokenu, což znamená, že konfigurovatelné zásady životního cyklu tokenu již nebudou přijaty pro klienty podporující CAE, kteří vyjednávají

Platnost přístupového tokenu zvýšíme na 24 hodin v CAE relacích. Odvolání je založené na kritických událostech a vyhodnocování zásad, nikoli v libovolném časovém období. Tato změna zvyšuje stabilitu svých aplikací, aniž by to ovlivnilo stav zabezpečení. 

## <a name="example-flows"></a>Příklady toků

### <a name="user-revocation-event-flow"></a>Tok událostí odvolání uživatele:

![Tok událostí odvolaných uživatelem](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Klient podporující CAE prezentuje přihlašovací údaje nebo obnovovací token pro AAD, který žádá o přístupový token pro určitý prostředek.
1. Přístupový token je vrácen spolu s jinými artefakty klientovi.
1. Správce explicitně [odvolá všechny aktualizační tokeny pro uživatele](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Do poskytovatele prostředků z Azure AD se pošle událost odvolání.
1. Poskytovateli prostředků se zobrazí přístupový token. Poskytovatel prostředků vyhodnocuje platnost tokenu a zkontroluje, jestli pro uživatele existuje nějaká událost odvolání. Poskytovatel prostředků používá tyto informace k rozhodnutí o udělení přístupu k prostředku nebo ne.
1. V takovém případě poskytovatel prostředků odmítne přístup a pošle výzvu 401 + nárok zpátky klientovi.
1. Klient podporující CAE rozumí výzvě 401 + Claim. Obchází mezipaměti a vrací se ke kroku 1, odesílá obnovovací token spolu s výzvou k deklaraci identity zpátky do služby Azure AD. Služba Azure AD potom znovu vyhodnotí všechny podmínky a vyzve uživatele k opětovnému ověření v tomto případě.
 
## <a name="faqs"></a>Nejčastější dotazy

### <a name="what-is-the-lifetime-of-my-access-token"></a>Jaká je doba života přístupového tokenu?

Pokud nepoužíváte klienty podporující CAE, vaše výchozí doba platnosti přístupového tokenu bude pořád 1 hodina, pokud jste nenakonfigurovali dobu života přístupového tokenu pomocí funkce [(CTL)](../develop/active-directory-configurable-token-lifetimes.md) ve verzi Preview.

Pokud používáte klienty podporující CAE, kteří vyjednávají relace s podporou CAE, bude nastavení seznamu CTL pro dobu života přístupového tokenu přepsáno a doba platnosti přístupového tokenu bude 24 hodin.

### <a name="how-quick-is-enforcement"></a>Jak rychle je vynucená?

I když je náš cíl vynucení okamžité, může být v některých případech latence až 15 minut v důsledku doby šíření události zjištěna.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Jak bude CAE pracovat s frekvencí přihlášení?

Frekvence přihlašování se bude respektovat s CAE nebo bez ní.

## <a name="next-steps"></a>Další kroky

[Oznamujeme Průběžné vyhodnocování přístupu.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
