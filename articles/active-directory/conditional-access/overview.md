---
title: Co je podmíněný přístup v Azure Active Directory? | Microsoft Docs
description: Zjistěte, jak podmíněný přístup v Azure Active Directory pomáhá implementovat automatizované rozhodování o udělení přístupu. Toto rozhodování není založené pouze na tom, kdo se pokouší získat přístup k prostředku, ale také na způsobu přístupu.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 3e96e251e655c7c7ad862fbf875111e08c0dfbdf
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42022967"
---
# <a name="what-is-conditional-access-in-azure-active-directory"></a>Co je podmíněný přístup v Azure Active Directory?

Zabezpečení je největší starostí organizací, které využívají cloud. Při správě cloudových prostředků jsou klíčovým aspektem zabezpečení cloudu identita a přístup. Ve světě orientovaném na mobilní zařízení a cloud můžou uživatelé přistupovat k prostředkům vaší organizace odkudkoli pomocí různých zařízení a aplikací. Proto už nestačí soustředit se na to, kdo má přístup k prostředku. Pokud chcete zajistit optimální rovnováhu mezi zabezpečením a produktivitou, musíte při rozhodování o udělení přístupu zohlednit také způsob přístupu k prostředku. Tento požadavek můžete vyřešit pomocí podmíněného přístupu služby Azure AD. Podmíněný přístup je funkce služby Azure Active Directory. S využitím podmíněného přístupu můžete implementovat automatizované rozhodování ohledně řízení přístupu k vašim cloudovým aplikacím na základě podmínek. 

![Řízení](./media/overview/81.png)

Tento článek poskytuje koncepční přehled podmíněného přístupu ve službě Azure AD.



## <a name="common-scenarios"></a>Obvyklé scénáře

Ve světě orientovaném na mobilní zařízení a cloud umožňuje Azure Active Directory jednotné přihlašování odkudkoli k zařízením, aplikacím a službám. S čím dál větším rozšířením zařízení (včetně používání vlastních zařízení), práce mimo podnikové sítě a aplikací SaaS třetích stran před vámi stojí dva protikladné cíle:

- Umožnit uživatelům být produktivní kdykoli a kdekoli
- Zajistit nepřetržitou ochranu podnikových prostředků

S využitím zásad podmíněného přístupu můžete implementovat správné řízení přístupu v závislosti na požadovaných podmínkách. Podmíněný přístup služby Azure AD poskytuje v případě potřeby dodatečné zabezpečení a neplete se uživatelům do cesty, když to není potřeba. 

Následuje několik běžných problémů s přístupem, se kterými vám podmíněný přístup může pomoct:



- **[Riziko přihlášení:](conditions.md#sign-in-risk)** Azure AD Identity Protection rozpoznává rizika přihlášení. Jak omezit přístup, pokud zjištěné riziko přihlášení značí pochybného aktéra? Co kdybyste chtěli získat přesvědčivější důkazy o tom, že přihlášení provedl legitimní uživatel? Co když jsou vaše pochybnosti dostatečně silné na to, abyste určitým uživatelům chtěli dokonce zablokovat přístup k aplikaci?  

- **[Síťové umístění:](location-condition.md)** Služba Azure AD je přístupná odkudkoli. Co když k pokusu o získání přístupu dojde ze síťového umístění, které není pod kontrolou vašeho oddělení IT? Kombinace uživatelského jména a hesla může být dostatečným dokladem identity pro pokusy o získání přístupu k vašim prostředkům z vaší podnikové sítě. Co když požadujete silnější doklad identity pro pokusy o získání přístupu, ke kterým dojde z neočekávaných zemí nebo oblastí světa? Co když dokonce chcete zablokovat pokusy o získání přístupu z určitých oblastí?  

- **[Správa zařízení:](conditions.md#device-platforms)** Ve službě Azure AD můžou uživatelé přistupovat ke cloudovým aplikacím z široké škály zařízení, včetně mobilních a také osobních zařízení. Co když požadujete, aby se pokusy o získání přístupu mohly provádět pouze na zařízeních, která spravuje vaše oddělení IT? Co když chcete určitým zařízením dokonce zablokovat přístup ke cloudovým aplikacím ve vašem prostředí? 

- **[Klientská aplikace:](conditions.md#client-apps)** V současné době můžete přistupovat k mnoha cloudovým aplikacím různých typů, jako jsou například webové aplikace, mobilní aplikace nebo desktopové aplikace. Co když dojde k pokusu o získání přístupu pomocí typu klientské aplikace, který způsobuje známé problémy? Co když u určitých typů aplikací požadujete použití zařízení, které spravuje vaše oddělení IT? 

Tyto otázky a související odpovědi představují běžné scénáře přístupu pro podmíněný přístup služby Azure AD. Podmíněný přístup je funkce služby Azure Active Directory, která umožňuje zpracovávat scénáře přístupu na základě zásad.


## <a name="conditional-access-policies"></a>Zásady podmíněného přístupu

Zásada podmíněného přístupu představuje definici scénáře přístupu pomocí následujícího vzoru:

![Řízení](./media/overview/10.png)

**Then do this** (Pak se provede toto) definuje reakci vaší zásady. Je důležité si uvědomit, že cílem zásady podmíněného přístupu není udělit přístup ke cloudové aplikaci. Ve službě Azure AD se udělování přístupu ke cloudovým aplikacím provádí prostřednictvím přiřazení uživatelů. Pomocí zásady podmíněného přístupu řídíte, jakým způsobem můžou autorizovaní uživatelé (uživatelé s uděleným přístupem ke cloudové aplikaci) přistupovat ke cloudovým aplikacím za určitých podmínek. V rámci reakce vynucujete další požadavky, jako je vícefaktorové ověřování, použití spravovaného zařízení a další. V kontextu podmíněného přístupu služby Azure AD se požadavky, které vaše zásady vynucují, označují jako řízení přístupu. V té nejvíce omezující podobě můžou vaše zásady blokovat přístup. Další informace najdete v tématu [Řízení přístupu v rámci podmíněného přístupu služby Azure Active Directory](controls.md).
     

**When this happens** (Když se stane toto) definuje důvod aktivace vaší zásady. Tento důvod je charakterizování skupinou splněných podmínek. V podmíněném přístupu služby Azure AD mají dvě podmínky přiřazení zvláštní roli:

- **[Uživatelé:](conditions.md#users-and-groups)** Uživatelé, kteří se pokoušejí o získání přístupu (**Kdo**). 

- **[Cloudové aplikace:](conditions.md#cloud-apps)** Cíle pokusů o získání přístupu (**Co**).    

Tyto dvě podmínky musí obsahovat každá zásada podmíněného přístupu. Kromě těchto dvou povinných podmínek můžete použít také další podmínky, které popisují způsob provedení pokusu o získání přístupu. Mezi běžné příklady patří použití mobilních zařízení nebo oblastí mimo vaši podnikovou síť. Další informace najdete v tématu [Podmínky v rámci podmíněného přístupu služby Azure Active Directory](conditions.md).   

Kombinace podmínek a řízení přístupu představuje zásadu podmíněného přístupu. 

![Řízení](./media/overview/51.png)

Pomocí podmíněného přístupu služby Azure AD můžete řídit způsob, jakým můžou autorizovaní uživatelé přistupovat k vašim cloudovým aplikacím. Cílem zásady podmíněného přístupu je vynucovat u pokusů o získání přístupu ke cloudové aplikaci další řízení přístupu na základě způsobu provedení pokusu o získání přístupu.

Jednou z výhod využívání přístupu založeného na zásadách k ochraně přístupu ke cloudovým aplikacím je, že můžete začít navrhovat požadavky zásad pro vaše prostředí s využitím struktury popsané v tomto článku, aniž byste se museli starat o technickou implementaci. 


## <a name="license-requirements-for-using-conditional-access"></a>Licenční požadavky pro využívání podmíněného přístupu

K využívání podmíněného přístupu se vyžaduje licence Azure AD Premium. Pokud chcete najít správnou licenci pro vaše požadavky, přečtěte si [porovnání obecně dostupných funkcí edic Free, Basic a Premium](https://azure.microsoft.com/pricing/details/active-directory/).


## <a name="next-steps"></a>Další kroky

- Tady najdete další informace:
    - Podmínky: viz [Podmínky v rámci podmíněného přístupu služby Azure Active Directory](conditions.md).

    - Řízení přístupu: viz [Řízení přístupu v rámci podmíněného přístupu služby Azure Active Directory](controls.md).

- Pokud se chcete seznámit s konfigurací zásad podmíněného přístupu, přečtěte si téma [Vyžadování MFA pro specifické aplikace pomocí podmíněného přístupu služby Azure Active Directory](app-based-mfa.md).

- Pokud jste připraveni nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, přečtěte si [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md). 

- Pokud chcete podrobný plán nasazení s doporučenými zásadami, přečtěte si o [plánu nasazení podmíněného přístupu](http://aka.ms/conditionalaccessdeploymentplan).
