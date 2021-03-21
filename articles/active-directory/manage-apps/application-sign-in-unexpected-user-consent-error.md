---
title: Neočekávaná chyba při provádění souhlasu s aplikací | Microsoft Docs
description: Popisuje chyby, ke kterým může dojít během procesu souhlasu s aplikací a o tom, co s nimi můžete dělat.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f829672f88ea848e4611000b54d9cc200bc166d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259973"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Neočekávaná chyba při provádění souhlasu s aplikací

Tento článek popisuje chyby, ke kterým může dojít během procesu souhlasu s aplikací. Pokud řešíte problémy s neočekávaným souhlasem, které neobsahují žádné chybové zprávy, přečtěte si téma [scénáře ověřování pro Azure AD](../develop/authentication-vs-authorization.md).

Mnoho aplikací, které jsou integrovány s Azure Active Directory, vyžaduje oprávnění k přístupu k jiným prostředkům, aby fungovaly. Pokud jsou tyto prostředky integrovány i Azure Active Directory, jsou oprávnění pro přístup k nim často požadována pomocí společného souhlasu architektury. Zobrazí se výzva k vyjádření souhlasu, která obvykle nastane při prvním použití aplikace, ale může se objevit i při dalším použití aplikace.

Aby mohl uživatel udělit souhlas s oprávněními, které aplikace vyžaduje, musí být splněny určité podmínky. Nejsou-li tyto podmínky splněny, může dojít k následujícím chybám.

## <a name="requesting-not-authorized-permissions-error"></a>Požaduje se chyba neautorizované oprávnění.
* **AADSTS90093:** &lt; clientAppDisplayName &gt; požaduje jedno nebo více oprávnění, která nemáte autorizaci udělit. Obraťte se na správce, který může tuto aplikaci vyjádřit vaším jménem.
* **AADSTS90094:** &lt; clientAppDisplayName &gt; potřebuje oprávnění pro přístup k prostředkům ve vaší organizaci, které může udělit jenom správce. Please ask an admin to grant permission to this app before you can use it. (Test udělení souhlasu vyžaduje ve vaší organizaci pro přístup k prostředkům oprávnění, které může udělit pouze správce. Než budete moct tuto aplikaci použít, požádejte správce o udělení oprávnění.)

K této chybě dochází, když se uživatel, který není globálním správcem, pokusí použít aplikaci, která požaduje oprávnění, která může udělit pouze správce. Tuto chybu může vyřešit správce, který uděluje přístup k aplikaci jménem své organizace.

K této chybě může dojít také v případě, že uživatel brání v souhlasu s aplikací kvůli tomu, že je žádost o oprávnění riskantní. V takovém případě se událost auditu bude protokolovat jako kategorie "ApplicationManagement", typ aktivity "souhlas s aplikací" a důvod stavu "riziková aplikace zjištěná".

K této chybě může dojít v jiném scénáři, pokud je pro aplikaci vyžadováno přiřazení uživatele, ale nebyl poskytnut žádný souhlas správce. V takovém případě musí správce nejdřív poskytnout souhlas správce.   

## <a name="policy-prevents-granting-permissions-error"></a>Zásada zabraňuje udělení oprávnění k chybě.
* **AADSTS90093:** Správce &lt; tenantDisplayName &gt; nastavil zásadu, která vám zabrání v udělení &lt; názvu aplikace oprávnění, která &gt; požaduje. Kontaktujte správce &lt; tenantDisplayName &gt; , který vám může udělit oprávnění k této aplikaci vaším jménem.

K této chybě dochází, pokud globální správce vypne možnost souhlasu uživatelů s aplikacemi, pak se uživatel bez oprávnění správce pokusí použít aplikaci, která vyžaduje souhlas. Tuto chybu může vyřešit správce, který uděluje přístup k aplikaci jménem své organizace.

## <a name="intermittent-problem-error"></a>Chyba přerušovaného problému
* **AADSTS90090:** Vypadá to, že při procesu přihlašování došlo k přerušovanému problému se záznamem oprávnění, která jste se pokusili udělit &lt; clientAppDisplayName &gt; . Zkuste to znovu později.

Tato chyba znamená, že došlo k občasnému problému na straně služby. Dá se vyřešit opakovaným pokusem o vyjádření souhlasu s aplikací.

## <a name="resource-not-available-error"></a>Chyba prostředku není k dispozici
* **AADSTS65005:** Aplikace &lt; clientAppDisplayName &gt; požadovala oprávnění pro přístup k &lt; resourceAppDisplayName prostředku &gt; , který není k dispozici. 

Obraťte se na vývojáře aplikace.

##  <a name="resource-not-available-in-tenant-error"></a>Prostředek není k dispozici v chybě tenanta.
* **AADSTS65005:** &lt; clientAppDisplayName &gt; žádá o přístup k resourceAppDisplayName prostředků &lt; &gt; , který není k dispozici ve vaší organizaci &lt; tenantDisplayName &gt; . 

Zajistěte, aby byl tento prostředek k dispozici, nebo se obraťte na správce služby &lt; tenantDisplayName &gt; .

## <a name="permissions-mismatch-error"></a>Chyba neshody oprávnění
* **AADSTS65005:** Aplikace požádala o souhlas s přístupem k &lt; resourceAppDisplayName prostředků &gt; . Tato žádost se nezdařila, protože neodpovídá tomu, jak byla aplikace předem nakonfigurovaná během registrace aplikace. Obraťte se na dodavatele aplikace. * *

K těmto chybám dochází, když aplikace, se kterou se uživatel snaží souhlasit, požaduje oprávnění k přístupu k aplikaci prostředků, kterou nelze najít v adresáři organizace (tenant). K této situaci může dojít z několika důvodů:

-   Vývojář klientské aplikace nakonfiguroval svou aplikaci nesprávně, což způsobuje, že žádá o přístup k neplatnému prostředku. V takovém případě musí vývojář aplikace aktualizovat konfiguraci klientské aplikace, aby tento problém vyřešil.

-   Instanční objekt představující cílovou aplikaci prostředků v organizaci neexistuje nebo existoval v minulosti, ale byl odebrán. Chcete-li tento problém vyřešit, musí být v organizaci zřízen instanční objekt pro aplikaci prostředků, aby klientská aplikace mohla pro něj požádat o oprávnění. Instanční objekt se dá zřídit řadou způsobů v závislosti na typu aplikace, včetně:

    -   Získání předplatného pro aplikaci prostředků (publikované aplikace Microsoftu)

    -   Souhlas s aplikací prostředků

    -   Udělení oprávnění aplikace prostřednictvím Azure Portal

    -   Přidání aplikace z Galerie aplikací Azure AD

## <a name="risky-app-error-and-warning"></a>Chybová zpráva a upozornění rizikové aplikace
* **AADSTS900941:** Je vyžadován souhlas správce. Aplikace se považuje za rizikové. (AdminConsentRequiredDueToRiskyApp)
* Tato aplikace může být riskantní. Pokud tuto aplikaci považujete za důvěryhodnou, požádejte správce, aby vám udělil přístup.
* **AADSTS900981:** Pro rizikovou aplikaci byla přijata žádost o souhlas správce. (AdminConsentRequestRiskyAppWarning)
* Tato aplikace může být riskantní. Pokračujte pouze v případě, že tuto aplikaci důvěřujete.

Obě tyto zprávy se zobrazí, když společnost Microsoft zjistí, že žádost o souhlas může být riskantní. V mnoha dalších faktorech to může nastat, pokud se [ověřený vydavatel](../develop/publisher-verification-overview.md) nepřidal do registrace aplikace. První kód chyby a zpráva se koncovým uživatelům zobrazí, když je [pracovní postup pro vyjádření souhlasu správce](configure-admin-consent-workflow.md) zakázán. Druhý kód a zpráva se zobrazí koncovým uživatelům, když je povolen pracovní postup souhlasu správce a správců. 

Koncoví uživatelé nebudou moci udělit souhlas aplikacím, které byly zjištěny jako rizikové. Správci jsou schopni, ale měli by zhodnotit, že aplikace je velmi opatrní a bude postupovat opatrně. Pokud se aplikace při další kontrole jeví jako podezřelá, může se Microsoftu ohlásit na obrazovce pro vyjádření souhlasu. 

## <a name="next-steps"></a>Další kroky 

[Aplikace, oprávnění a souhlas v Azure Active Directory (koncový bod V1)](../develop/quickstart-register-app.md)<br>

[Rozsahy, oprávnění a souhlas v Azure Active Directory (koncový bod verze 2.0)](../develop/v2-permissions-and-consent.md)