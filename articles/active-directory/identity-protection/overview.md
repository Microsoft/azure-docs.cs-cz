---
title: Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak Azure AD Identity Protection umožňuje omezit schopnost útočníka zneužít ohrožení zabezpečení identity nebo zařízení a zabezpečit identitu nebo zařízení, který byl dříve podezřelý nebo známé u něho ohrožena bezpečnost.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 3017d92115a61dbf5ce647e75a01360224843f44
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165026"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Co je Azure Active Directory Identity Protection?

Azure Active Directory Identity Protection je funkce verze Azure AD Premium P2, která umožňuje:

- Zjistit potenciální ohrožení zabezpečení dopadem na identity vaší organizace

- Nakonfigurovat automatické odpovědi na zjištěné podezřelé akce, které se vztahují na identity vaší organizace  

- Zkoumat podezřelé incidenty a přijímat vhodná opatření k jejich řešení   


## <a name="get-started"></a>Začínáme

Microsoft má zabezpečené cloudové identity pro více než deset let. S Azure Active Directory Identity Protection ve vašem prostředí, můžete použít samé systémy ochrany, které společnost Microsoft používá k zabezpečení identit.

Většinu narušení zabezpečení se použijí při útočníci získají přístup k prostředí krádeží identity uživatele. V průběhu let staly útočníci stále častěji efektivní využití porušení třetích stran a pomocí sofistikovaných útoků phishing útoky. Jakmile útočník získá přístup k i nízkou privilegovaných uživatelské účty, je pro ně k získání přístupu k prostředkům společnosti důležité prostřednictvím taktiky Lateral Movement je poměrně snadné.

Následkem toho budete muset:

- Chránit všechny identity bez ohledu na jejich úrovně oprávnění

- Proaktivně ohrožení zabezpečení identity zabránit se zneužít

Zjišťování ohrožení zabezpečení identit je žádný snadný úkol. Azure Active Directory používá algoritmy adaptivní strojového učení a heuristik detekovat anomálie a podezřelé incidenty, které indikují potenciální ohrožení identit. Ochrana Identity na základě těchto dat generuje sestavy a výstrahy, které vám umožní zjištěné problémy vyhodnotit a přijmout odpovídající omezení nebo nápravné akce.

Azure Active Directory Identity Protection je větší než monitorování a vytváření sestav nástroje. K ochraně identity ve vaší organizaci, můžete nakonfigurovat zásady, které automaticky reagují na zjištěné problémy, když se dosáhne úroveň zadané rizika. Tyto zásady, kromě jiných ovládacích prvků podmíněný přístup poskytuje Azure Active Directory a EMS, můžete buď automaticky blokovat nebo zahájit adaptivní nápravných akcí, které resetuje heslo včetně a vynucování služby Multi-Factor authentication.


#### <a name="identity-protection-capabilities"></a>Možnosti ochrany identit

**Zjišťování ohrožení zabezpečení a rizikových účty:**  

* Poskytuje vlastní doporučení k vylepšení celkové postavení zabezpečení zvýrazněním ohrožení zabezpečení
* Výpočet úrovně rizika přihlášení
* Výpočet úrovně rizika uživatele


**Zkoumání rizikových událostí:**

* Odesílání oznámení pro rizikové události
* Zkoumání pomocí relevantní a kontextové informace o rizikových událostí
* Poskytuje základní pracovní postupy ke sledování vyšetřování
* Poskytování snadného přístupu pro nápravné akce, jako je například resetování hesla

**Zásady podmíněného přístupu na základě rizik:**

* Zásady pro blokování přihlášení nebo vyžadování výzvy ověřování službou Multi-Factor Authentication zmírnění rizikových přihlášení
* Zásady na blokování nebo zabezpečené rizikové uživatelské účty
* Zásadu, která vyžaduje uživatelé zaregistrovali k ověření službou Multi-Factor Authentication



## <a name="identity-protection-roles"></a>Role identity Protection

Pro vyrovnávání zatížení správy aktivit kolem vaší implementace Identity Protection můžete přiřadit několik rolí. Azure AD Identity Protection podporuje 3 role adresáře:

| Role                         | Můžete provést                          | Nelze provést.
| :--                          | ---                                |  ---   |
| Globální správce         | Úplný přístup k Identity Protection, připojit Identity Protection| |
| Správce zabezpečení       | Úplný přístup k Identity Protection | Připojení Identity Protection, resetujte hesla pro uživatele |
| Čtenář zabezpečení              | Přístup k Identity Protection jen pro čtení | Oprava uživatelů připojení Identity Protection, nakonfigurovat zásady, resetování hesel |




Další podrobnosti najdete v tématu [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)



## <a name="detection"></a>Detection (Detekce)

### <a name="vulnerabilities"></a>Ohrožení zabezpečení

Azure Active Directory Identity Protection analyzuje konfiguraci a zjistí ohrožení zabezpečení, která může mít vliv na identity vašich uživatelů. Další podrobnosti najdete v tématu [chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Rizikové události

Azure Active Directory používá algoritmy adaptivní strojového učení a heuristik ke zjištění podezřelé akce, které se vztahují k identity vašich uživatelů. Systém vytvoří záznam pro každé zjištěné podezřelé akce. Tyto záznamy jsou také známé jako rizikové události.  
Další podrobnosti najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](../active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Šetření

Svou cestu prostřednictvím služby Identity Protection obvykle začíná řídicího panelu služby Identity Protection.

![Náprava](./media/overview/1000.png "nápravy")

Tento řídicí panel poskytuje přístup k:

* Sestavy, jako například **uživatelé označení příznakem rizika**, **rizikových událostí** a **ohrožení zabezpečení**
* Nastavení, jako je konfigurace vašeho **zásady zabezpečení**, **oznámení** a **registracích vícefaktorového ověřování**

Obvykle je vaším výchozím bodem pro šetření, což je proces kontroly aktivity, protokoly a další relevantní informace související s riziková událost se rozhodnout, zda jsou nezbytné kroky ke zmírnění nebo nápravy, a jak se identita dojde k ohrožení bezpečnosti a seznamte se s použití ohrožených identity.

Vaše aktivity šetření tak můžete navázat [oznámení](notifications.md) Azure Active Directory Protection odesílá na e-mailu.



## <a name="policies"></a>Zásady

Pokud chcete implementovat automatické odpovědi, Azure Active Directory Identity Protection nabízí tři zásady:

- [Zásady registrace pro vícefaktorové ověřování](howto-mfa-policy.md)

- [Zásady rizik uživatelů](howto-user-risk-policy.md)

- [Zásady rizik přihlašování](howto-sign-in-risk-policy.md)


## <a name="next-steps"></a>Další postup

- [Kanál 9: Azure AD a Identity zobrazit: Identity Protection ve verzi Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Povolení služby Azure Active Directory Identity Protection](enable.md)

