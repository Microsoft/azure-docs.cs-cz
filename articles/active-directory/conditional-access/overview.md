---
title: Co je podmíněný přístup v Azure Active Directory? | Dokumenty Microsoft
description: Zjistěte, jak vám podmíněný přístup ve službě Azure Active Directory pomůže provádět rozhodnutí o automatický přístup, která nejsou založena pouze na který se pokusí o přístup k prostředku, ale také způsob přístupu k prostředku.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 02/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae2cbf8f493fab4b94abf9379a89a7d356195e39
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052437"
---
# <a name="what-is-conditional-access"></a>Co je podmíněný přístup?

Zabezpečení je největší starostí organizací, které využívají cloud. Při správě cloudových prostředků jsou klíčovým aspektem zabezpečení cloudu identita a přístup. Ve světě orientovaném na mobilní zařízení a cloud můžou uživatelé přistupovat k prostředkům vaší organizace odkudkoli pomocí různých zařízení a aplikací. Proto už nestačí soustředit se na to, kdo má přístup k prostředku. Pokud chcete zajistit optimální rovnováhu mezi zabezpečením a produktivitou, musíte při rozhodování o udělení přístupu zohlednit také způsob přístupu k prostředku. S podmíněným přístupem Azure Active Directory (Azure AD) abyste mohli vyřešit tento požadavek. Podmíněný přístup je funkce Azure Active Directory. S podmíněným přístupem můžete implementovat rozhodovat o automatizovaného přístupu pro přístup k vašim cloudovým aplikacím, které jsou založeny na podmínkách.

Po dokončení první dvojúrovňové ověřování se vynucují zásady podmíněného přístupu. Proto podmíněného přístupu není určen jako první linií obrany řádku pro scénáře, jako jsou útoky s cílem odepření služeb (DoS), ale můžete využít signály z těchto událostí (například úroveň rizika přihlášení, umístění žádosti a tak dále) a zjistí přístup k.  

![Ovládací prvek](./media/overview/81.png)

Tento článek poskytuje koncepční přehled podmíněný přístup ve službě Azure AD.

## <a name="common-scenarios"></a>Obvyklé scénáře

Ve světě orientovaném na mobilní zařízení a cloud umožňuje Azure Active Directory jednotné přihlašování odkudkoli k zařízením, aplikacím a službám. S čím dál větším rozšířením zařízení (včetně používání vlastních zařízení), práce mimo podnikové sítě a aplikací SaaS třetích stran před vámi stojí dva protikladné cíle:

- Umožnit uživatelům být produktivní kdykoli a kdekoli
- Zajistit nepřetržitou ochranu podnikových prostředků

Pomocí zásad podmíněného přístupu můžete použít ovládací prvky přístupu v části požadované podmínky. Azure AD podmíněný přístup vám poskytuje dodatečné zabezpečení, když potřeba a zůstane mimo způsob, jak uživatele, kdy už není.

Toto jsou některé běžné problémy přístupu, které vám s podmíněným přístupem může pomoct:

- **[Riziko přihlášení](conditions.md#sign-in-risk)** : Azure AD Identity Protection zjistí rizika přihlášení. Jak omezit přístup, pokud zjištěné riziko přihlášení značí pochybného aktéra? Co kdybyste chtěli získat přesvědčivější důkazy o tom, že přihlášení provedl legitimní uživatel? Co když jsou vaše pochybnosti dostatečně silné na to, abyste určitým uživatelům chtěli dokonce zablokovat přístup k aplikaci?  

- **[Síťové umístění](location-condition.md)** : Azure AD je přístupná odkudkoli. Co když k pokusu o získání přístupu dojde ze síťového umístění, které není pod kontrolou vašeho oddělení IT? Kombinace uživatelského jména a hesla může být dostatečným dokladem identity pro pokusy o získání přístupu z vaší podnikové sítě. Co když požadujete silnější doklad identity pro pokusy o získání přístupu, ke kterým dojde z neočekávaných zemí nebo oblastí světa? Co když dokonce chcete zablokovat pokusy o získání přístupu z určitých oblastí?  

- **[Správa zařízení](conditions.md#device-platforms)** : Ve službě Azure AD uživatelé můžou používat cloudové aplikace z širokou škálu zařízení včetně mobilních a také osobní zařízení. Co když požadujete, aby se pokusy o získání přístupu mohly provádět pouze na zařízeních, která spravuje vaše oddělení IT? Co když chcete určitým zařízením dokonce zablokovat přístup ke cloudovým aplikacím ve vašem prostředí?

- **[Klientská aplikace](conditions.md#client-apps)** : V současné době můžete přistupovat mnoho cloudových aplikací s využitím různých typů aplikací jako jsou webové aplikace, mobilní aplikace nebo aplikace klasické pracovní plochy. Co když dojde k pokusu o získání přístupu pomocí typu klientské aplikace, který způsobuje známé problémy? Co když u určitých typů aplikací požadujete použití zařízení, které spravuje vaše oddělení IT?

Tyto otázky a odpovědi související představují běžné situace přístupu pro podmíněný přístup Azure AD.
Podmíněný přístup je funkce Azure Active Directory, která umožňuje zpracovávat scénáře přístupu pomocí přístupu na základě zásad.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Zásady podmíněného přístupu

Zásady podmíněného přístupu je definice scénáře přístupu pomocí následujícího vzorce:

![Ovládací prvek](./media/overview/10.png)


**When this happens** (Když se stane toto) definuje důvod aktivace vaší zásady. Tento důvod je charakterizování skupinou splněných podmínek. Podmíněný přístup Azure AD přehrávat podmínek přiřazení dva speciální role:

- **[Uživatelé](conditions.md#users-and-groups)** : Uživatelé, provádí se pokus o přístup (**kdo**).

- **[Cloudové aplikace](conditions.md#cloud-apps-and-actions)** : Cíle pokus o přístup (**co**).

Tyto dvě podmínky jsou povinné v zásadách podmíněného přístupu. Kromě těchto dvou povinných podmínek můžete použít také další podmínky, které popisují způsob provedení pokusu o získání přístupu. Mezi běžné příklady patří použití mobilních zařízení nebo oblastí mimo vaši podnikovou síť. Další informace najdete v tématu [podmínky v Azure Active Directory podmíněného přístupu](conditions.md).

Kombinací podmínek s ovládacími prvky přístup představuje zásad podmíněného přístupu.

![Ovládací prvek](./media/overview/51.png)

Pomocí podmíněného přístupu Azure AD, můžete určit, jak Autorizovaní uživatelé můžou přistupovat k vašim cloudovým aplikacím. Cílem zásad podmíněného přístupu je vynutit kontrolu další možnosti přístupu pokusu o přístup ke cloudové aplikace založené na tom, jak provést pokus o přístup.

Ochrana přístupu ke cloudovým aplikacím na základě zásad vám umožní začít navrhovat požadavky zásad pro vaše prostředí s využitím struktury popsané v tomto článku, aniž byste se museli starat o technickou implementaci.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Podmíněný přístup Azure AD a federovaného ověřování

Zásady podmíněného přístupu bezproblémově fungují s [federovaného ověřování](../../security/azure-ad-choose-authn.md#federated-authentication). Tato podpora zahrnuje všechny podporované podmínky a ovládací prvky a přehled o Princip použití zásady na aktivního uživatele přihlásil pomocí [vytvářením sestav Azure AD](../reports-monitoring/concept-sign-ins.md).

*Federované ověřování pomocí Azure AD* znamená, že ověřování uživatelů v Azure AD zpracovává důvěryhodná ověřovací služba. Důvěryhodná ověřovací služba je například Active Directory Federation Services (AD FS) nebo jakákoli jiná federační služba. V této konfiguraci se ve službě provádí primární ověřování uživatelů a Azure AD pak slouží k přihlašování k jednotlivým aplikacím. Podmíněný přístup Azure AD je použita před udělením přístupu k aplikaci, kterou uživatel přistupuje. 

Pokud nakonfigurovaná zásada podmíněného přístupu vyžaduje vícefaktorové ověřování, Azure AD ve výchozím nastavení využívá Azure MFA. Pokud k vícefaktorovému ověřování využíváte federační službu, můžete v Azure AD nakonfigurovat přesměrování do této federační služby v případě, že je potřeba vícefaktorové ověřování, a to nastavením parametru `-SupportsMFA` na hodnotu `$true` v [PowerShellu](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). Toto nastavení funguje pro služby federovaného ověřování, které podporují požadavky na ověřovací testy MFA vydané službou Azure AD pomocí `wauth= http://schemas.microsoft.com/claims/multipleauthn`.

Po přihlášení uživatele ke službě federovaného ověřování zpracuje Azure AD ostatní požadavky zásad, jako je dodržování předpisů u zařízení nebo schválená aplikace.

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Zákazníci s [licence Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-business-service-description) také mají přístup k funkcím podmíněného přístupu. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak implementovat podmíněný přístup ve vašem prostředí, najdete v článku [plánování nasazení podmíněného přístupu v Azure Active Directory](plan-conditional-access.md).
