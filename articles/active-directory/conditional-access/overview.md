---
title: Co je podmíněný přístup v Azure Active Directory? | Dokumenty Microsoft
description: Přečtěte si, jak podmíněný přístup v Azure Active Directory pomáhá implementovat rozhodnutí automatizovaného přístupu, která nejsou jenom založená na tom, kdo se pokusí o přístup k prostředku, ale také k tomu, jak se k prostředku přistupuje.
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
ms.openlocfilehash: 99d17b354e267d003e23e507ca190b951e3ed4a0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608091"
---
# <a name="what-is-conditional-access"></a>Co je podmíněný přístup?

Zabezpečení je největší starostí organizací, které využívají cloud. Při správě cloudových prostředků jsou klíčovým aspektem zabezpečení cloudu identita a přístup. Ve světě orientovaném na mobilní zařízení a cloud můžou uživatelé přistupovat k prostředkům vaší organizace odkudkoli pomocí různých zařízení a aplikací. Proto už nestačí soustředit se na to, kdo má přístup k prostředku. Pokud chcete zajistit optimální rovnováhu mezi zabezpečením a produktivitou, musíte při rozhodování o udělení přístupu zohlednit také způsob přístupu k prostředku. Pomocí podmíněného přístupu Azure Active Directory (Azure AD) můžete tento požadavek vyřešit. Podmíněný přístup je schopnost Azure Active Directory. Pomocí podmíněného přístupu můžete implementovat automatizované rozhodování o řízení přístupu pro přístup k vašim cloudovým aplikacím, které jsou založené na podmínkách.

Zásady podmíněného přístupu se vynutily po dokončení prvního faktoru ověřování. Proto podmíněný přístup není určený jako ochrana prvního řádku pro scénáře, jako jsou útoky DoS (Denial-of-Service), ale může využít signály z těchto událostí (například úroveň rizika přihlašování, umístění žádosti atd.) a určit tak přístup.  

![Ovládací prvek](./media/overview/81.png)

Tento článek obsahuje koncepční přehled podmíněného přístupu ve službě Azure AD.

## <a name="common-scenarios"></a>Obvyklé scénáře

Ve světě orientovaném na mobilní zařízení a cloud umožňuje Azure Active Directory jednotné přihlašování odkudkoli k zařízením, aplikacím a službám. S čím dál větším rozšířením zařízení (včetně používání vlastních zařízení), práce mimo podnikové sítě a aplikací SaaS třetích stran před vámi stojí dva protikladné cíle:

- Umožnit uživatelům být produktivní kdykoli a kdekoli
- Zajistit nepřetržitou ochranu podnikových prostředků

Pomocí zásad podmíněného přístupu můžete v případě požadovaných podmínek použít správné řízení přístupu. Podmíněný přístup Azure AD vám v případě potřeby poskytuje další zabezpečení, a pokud ne, nezáleží na tom, jaký je váš uživatel.

Níže jsou uvedeny některé běžné přístupy, které vám pomůžou s podmíněným přístupem:

- **[Riziko přihlášení](conditions.md#sign-in-risk)** : Azure AD Identity Protection detekuje rizika přihlašování. Jak omezit přístup, pokud zjištěné riziko přihlášení značí pochybného aktéra? Co kdybyste chtěli získat přesvědčivější důkazy o tom, že přihlášení provedl legitimní uživatel? Co když jsou vaše pochybnosti dostatečně silné na to, abyste určitým uživatelům chtěli dokonce zablokovat přístup k aplikaci?  
- **[Umístění v síti](location-condition.md)** : Služba Azure AD je přístupná odkudkoli. Co když k pokusu o získání přístupu dojde ze síťového umístění, které není pod kontrolou vašeho oddělení IT? Kombinace uživatelského jména a hesla může být dostatečným dokladem identity pro pokusy o získání přístupu z vaší podnikové sítě. Co když požadujete silnější doklad identity pro pokusy o získání přístupu, ke kterým dojde z neočekávaných zemí nebo oblastí světa? Co když dokonce chcete zablokovat pokusy o získání přístupu z určitých oblastí?  
- **[Správa zařízení](conditions.md#device-platforms)** : Ve službě Azure AD můžou uživatelé přistupovat ke cloudovým aplikacím z široké škály zařízení, včetně mobilních a i osobních zařízení. Co když požadujete, aby se pokusy o získání přístupu mohly provádět pouze na zařízeních, která spravuje vaše oddělení IT? Co když chcete určitým zařízením dokonce zablokovat přístup ke cloudovým aplikacím ve vašem prostředí?
- **[Klientská aplikace](conditions.md#client-apps)** : V současné době můžete k mnoha cloudovým aplikacím přistupovat pomocí různých typů aplikací, jako jsou webové aplikace, mobilní aplikace nebo desktopové aplikace. Co když dojde k pokusu o získání přístupu pomocí typu klientské aplikace, který způsobuje známé problémy? Co když u určitých typů aplikací požadujete použití zařízení, které spravuje vaše oddělení IT?

Tyto otázky a související odpovědi reprezentují běžné scénáře přístupu pro podmíněný přístup Azure AD.
Podmíněný přístup je schopnost Azure Active Directory, která umožňuje zpracovávat scénáře přístupu pomocí přístupu založeného na zásadách.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Zásady podmíněného přístupu

Zásada podmíněného přístupu je definicí scénáře přístupu pomocí následujícího vzoru:

![Ovládací prvek](./media/overview/10.png)


**When this happens** (Když se stane toto) definuje důvod aktivace vaší zásady. Tento důvod je charakterizování skupinou splněných podmínek. V rámci podmíněného přístupu Azure AD se dvě podmínky přiřazení Dohrají speciální rolí:

- **[Uživatelé](conditions.md#users-and-groups)** : Uživatelé, kteří provádějí pokus o přístup (**kteří**).
- **[Cloudové aplikace](conditions.md#cloud-apps-and-actions)** : Cíle pokusu o přístup (**co**).

Tyto dvě podmínky jsou v zásadách podmíněného přístupu povinné. Kromě těchto dvou povinných podmínek můžete použít také další podmínky, které popisují způsob provedení pokusu o získání přístupu. Mezi běžné příklady patří použití mobilních zařízení nebo oblastí mimo vaši podnikovou síť. Další informace najdete v tématu [podmínky v Azure Active Directory podmíněný přístup](conditions.md).

Kombinace podmínek s ovládacími prvky přístupu představuje zásadu podmíněného přístupu.

![Ovládací prvek](./media/overview/51.png)

Pomocí podmíněného přístupu Azure AD můžete řídit, jak můžou autorizovaní uživatelé přistupovat k vašim cloudovým aplikacím. Cílem zásad podmíněného přístupu je vyhovět dalším ovládacím prvkům pro přístup k pokusu o přístup ke cloudové aplikaci na základě toho, jak se pokus o přístup provádí.

Ochrana přístupu ke cloudovým aplikacím na základě zásad vám umožní začít navrhovat požadavky zásad pro vaše prostředí s využitím struktury popsané v tomto článku, aniž byste se museli starat o technickou implementaci.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Podmíněný přístup Azure AD a federované ověřování

Zásady podmíněného přístupu fungují bez problémů [](../../security/fundamentals/choose-ad-authn.md#federated-authentication)s federovaném ověřováním. Tato podpora zahrnuje všechny podporované podmínky a ovládací prvky a přehled o tom, jak se zásady aplikují na přihlášení k aktivnímu uživateli pomocí [sestav Azure AD](../reports-monitoring/concept-sign-ins.md).

*Federované ověřování pomocí Azure AD* znamená, že ověřování uživatelů v Azure AD zpracovává důvěryhodná ověřovací služba. Důvěryhodná ověřovací služba je například Active Directory Federation Services (AD FS) nebo jakákoli jiná federační služba. V této konfiguraci se ve službě provádí primární ověřování uživatelů a Azure AD pak slouží k přihlašování k jednotlivým aplikacím. Podmíněný přístup Azure AD se použije dřív, než se udělí přístup k aplikaci, ke které uživatel přistupuje. 

Když nakonfigurovaná zásada podmíněného přístupu vyžaduje vícefaktorové ověřování, Azure AD použije Azure MFA. Pokud k vícefaktorovému ověřování využíváte federační službu, můžete v Azure AD nakonfigurovat přesměrování do této federační služby v případě, že je potřeba vícefaktorové ověřování, a to nastavením parametru `-SupportsMFA` na hodnotu `$true` v [PowerShellu](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). Toto nastavení funguje pro služby federovaného ověřování, které podporují požadavky na ověřovací testy MFA vydané službou Azure AD pomocí `wauth= http://schemas.microsoft.com/claims/multipleauthn`.

Po přihlášení uživatele ke službě federovaného ověřování zpracuje Azure AD ostatní požadavky zásad, jako je dodržování předpisů u zařízení nebo schválená aplikace.

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Zákazníci s [licencemi Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) mají taky přístup k funkcím podmíněného přístupu. 

## <a name="next-steps"></a>Další postup

Informace o tom, jak implementovat podmíněný přístup ve vašem prostředí, najdete v tématu [Plánování nasazení podmíněného přístupu v Azure Active Directory](plan-conditional-access.md).
