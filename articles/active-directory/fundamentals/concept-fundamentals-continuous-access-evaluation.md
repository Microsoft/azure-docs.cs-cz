---
title: Vyhodnocení nepřetržitého přístupu ve službě Azure AD
description: Rychlejší reakce na změny ve stavu uživatele pomocí vyhodnocení nepřetržitého přístupu ve službě Azure AD
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
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873300"
---
# <a name="continuous-access-evaluation"></a>Hodnocení nepřetržitého přístupu

Služby Microsoftu, jako je Azure Active Directory (Azure AD) a Office 365, používají otevřené standardy a protokoly k maximalizaci interoperability. Jedním z nejkritičtějších je Open ID Connect (OIDC). Když se klientská aplikace, jako je Outlook, připojí ke službě, jako je Exchange Online, jsou požadavky rozhraní API autorizovány pomocí přístupových tokenů OAuth 2.0. Ve výchozím nastavení jsou tyto přístupové tokeny platné po dobu jedné hodiny. Když vyprší jejich platnost, klient je přesměrován zpět do Služby Azure AD k jejich aktualizaci. To také poskytuje příležitost přehodnotit zásady pro přístup uživatelů – můžeme se rozhodnout neaktualizovat token z důvodu zásady podmíněného přístupu nebo proto, že uživatel byl zakázán v adresáři. 

Slyšeli jsme drtivou zpětnou vazbu od našich zákazníků: hodinová prodleva z důvodu životnosti tokenu přístupu pro opětovné použití zásad podmíněného přístupu a změn ve stavu uživatele (například: zakázáno z důvodu dovolené) není dost dobrá.

Společnost Microsoft byla prvním účastníkem iniciativy CAEP (Continuous Access Evaluation Protocol) jako součást pracovní [skupiny Sdílené signály a události](https://openid.net/wg/sse/) v nadaci OpenID Foundation. Poskytovatelé identit a předávající strany budou moci využít události zabezpečení a signály definované pracovní skupinou k opětovnéautorizaci nebo ukončení přístupu. Je to vzrušující práce a zlepší bezpečnost napříč mnoha platformami a aplikacemi.

Vzhledem k tomu, že výhody zabezpečení jsou tak velké, zavádíme počáteční implementaci specifickou pro společnost Microsoft souběžně s naší pokračující prací v rámci normalizačních orgánů. Při práci na nasazení těchto funkcí průběžného hodnocení přístupu (CAE) napříč službami společnosti Microsoft jsme se hodně naučili a tyto informace sdílíme s komunitou standardů. Doufáme, že naše zkušenosti s nasazením mohou pomoci informovat ještě lepší průmyslový standard a jsou odhodláni tuto normu po ratifikaci implementovat, což umožní všem zúčastněným službám využívat výhod.

## <a name="how-does-cae-work-in-microsoft-services"></a>Jak cae funguje ve službách společnosti Microsoft?

Zaměřujeme se na naši počáteční implementaci průběžného hodnocení přístupu k exchange a týmům. Doufáme, že v budoucnu rozšíříme podporu na další služby společnosti Microsoft. Začneme povolit vyhodnocení nepřetržitého přístupu pouze pro klienty bez zásad podmíněného přístupu. Budeme používat naše poznatky z této fáze CAE informovat naše probíhající zavádění CAE.

## <a name="service-side-requirements"></a>Požadavky na straně služby

Vyhodnocení nepřetržitého přístupu se implementuje povolením služeb (poskytovatelů prostředků) k odběru kritických událostí ve službě Azure AD tak, aby tyto události bylo možné vyhodnotit a vynucovat téměř v reálném čase. V tomto počátečním zavedení CAE budou vynuceny následující události:

- Uživatelský účet je odstraněn nebo zakázán.
- Heslo pro uživatele se změní nebo resetuje
- Správce explicitně odvolá všechny aktualizační tokeny pro uživatele.
- Zvýšené riziko uživatele zjištěné službou Azure AD Identity Protection

Doufáme, že v budoucnu přidáme další události, včetně událostí, jako jsou změny polohy a stavu zařízení. **Zatímco naším cílem je, aby bylo vynucení okamžité, v některých případech může být kvůli době šíření událostí pozorována latence až 15 minut**. 

## <a name="client-side-claim-challenge"></a>Výzva k reklamaci na straně klienta

Před vyhodnocením nepřetržitého přístupu by se klienti vždy pokoušeli přehrát přístupový token ze své mezipaměti, pokud jeho platnost nevypršela. S CAE zavádíme nový případ, že poskytovatel prostředků může odmítnout token i v případě, že jeho platnost nevypršela. Aby bylo možné informovat klienty obejít jejich mezipaměti i v případě, že tokeny uložené v mezipaměti nebyly vypršela, zavádíme mechanismus s názvem **deklarace problém**. CAE vyžaduje aktualizaci klienta, aby pochopilvýzvu pro deklaraci. Nejnovější verze následujících aplikací níže nárok na podporu výzvu:

- Outlook pro Windows 
- Outlook iOS 
- Outlook Android 
- Outlook Mac 
- Týmy pro Windows
- Týmy iOS 
- Týmy Android 
- Týmy Mac 

## <a name="token-lifetime"></a>Živostnost tokenu

Vzhledem k tomu, že rizika a zásady jsou vyhodnocovány v reálném čase, budou klienti, kteří vyjednávají relace s ohledem na průběžné hodnocení přístupu, spoléhat na cae místo existujících zásad životnosti tokenu statického přístupu, což znamená, že konfigurovatelné zásady životnosti tokenu již nebudou dodržovány pro klienty podporující CAE, kteří vyjednávají relace podporující CAE.

Zvýšíme životnost přístupového tokenu na 24 hodin v relacích CAE. Odvolání je řízeno kritickými událostmi a vyhodnocením zásad, nikoli libovolným časovým obdobím. Tato změna zvyšuje stabilitu aplikací bez ovlivnění stavu zabezpečení. 

## <a name="example-flows"></a>Příklady toků

### <a name="user-revocation-event-flow"></a>Tok události odvolání uživatele:

![Tok události odvolání uživatele](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Klient podporující CAE představuje pověření nebo obnovovací token do služby AAD s žádostí o přístupový token pro určitý prostředek.
1. Přístupový token je vrácena spolu s dalšími artefakty klientovi.
1. Správce explicitně [odvolá všechny obnovovací tokeny pro uživatele](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Událost odvolání se odešlou poskytovateli prostředků z Azure AD.
1. Přístupový token je předložen poskytovateli prostředků. Poskytovatel prostředků vyhodnotí platnost tokenu a zkontroluje, zda pro uživatele existuje nějaká událost odvolání. Poskytovatel prostředků používá tyto informace k rozhodnutí udělit přístup k prostředku, nebo ne.
1. V takovém případě poskytovatel prostředků odepře přístup a odešle klientovi výzvu nároku 401+
1. Klient s podporou CAE rozumí výzvě 401+ nároku. Obchází mezipaměti a přejde zpět ke kroku 1, odesílání tokenu aktualizace spolu s deklarace zabezpečení výzvu zpět do Služby Azure AD. Azure AD pak přehodnotí všechny podmínky a vyzve uživatele k opětovnému ověření v tomto případě.
 
## <a name="faqs"></a>Nejčastější dotazy

### <a name="what-is-the-lifetime-of-my-access-token"></a>Jaká je životnost mého přístupového tokenu?

Pokud nepoužíváte klienty podporující CAE, bude vaše výchozí životnost přístupového tokenu stále 1 hodina, pokud jste nenakonfigurovali životnost přístupového tokenu pomocí funkce [náhledu životnosti konfigurovatelného tokenu (CTL).](../develop/active-directory-configurable-token-lifetimes.md)

Pokud používáte klienty podporující CAE, kteří vyjednávají relace podporující cae, bude vaše nastavení ctl pro životnost přístupového tokenu přepsáno a životnost přístupového tokenu bude 24 hodin.

### <a name="how-quick-is-enforcement"></a>Jak rychlé je vymáhání?

Zatímco naším cílem je, aby bylo vynucení okamžité, v některých případech může být kvůli době šíření událostí pozorována latence až 15 minut.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Jak bude CAE fungovat s frekvencí přihlášení?

Frekvence přihlášení bude uspokojena s CAE nebo bez něj.

## <a name="next-steps"></a>Další kroky

[Oznámení vyhodnocení průběžného přístupu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
