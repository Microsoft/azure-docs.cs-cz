---
title: Osvědčené postupy pro podmíněný přístup v Azure Active Directory | Microsoft Docs
description: Seznamte se s informacemi, které byste měli vědět, a k čemu byste se měli vyhnout při konfiguraci zásad podmíněného přístupu.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 161b02ec962df5c5e1c8dee7e124ef78b3ca4db3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601975"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Osvědčené postupy pro podmíněný přístup v Azure Active Directory

Pomocí [podmíněného přístupu Azure Active Directory (Azure AD)](./overview.md)můžete řídit, jak autorizovaným uživatelům přistupují k vašim cloudovým aplikacím. V tomto článku najdete informace o:

- Co byste měli znát 
- K tomu byste se měli vyhnout při konfiguraci zásad podmíněného přístupu. 

V tomto článku se předpokládá, že máte zkušenosti s koncepty a terminologie, která je popsaný v tématu [co je podmíněný přístup v Azure Active Directory?](./overview.md)

## <a name="whats-required-to-make-a-policy-work"></a>Co je potřeba k práci se zásadami?

Když vytvoříte novou zásadu, neexistují žádní uživatelé, skupiny, aplikace nebo ovládací prvky přístupu.

![Cloudové aplikace](./media/best-practices/02.png)

Aby zásada fungovala, musíte nakonfigurovat:

| Co           | Jak                                  | Proč |
| :--            | :--                                  | :-- |
| **Cloudové aplikace** |Vyberte alespoň jednu aplikaci.  | Cílem zásad podmíněného přístupu je umožnit vám řídit, jak můžou autorizovaní uživatelé přistupovat ke cloudovým aplikacím.|
| **Uživatelé a skupiny** | Vyberte nejméně jednoho uživatele nebo skupinu, kteří mají autorizaci pro přístup k vybraným cloudovým aplikacím. | Neaktivují se zásady podmíněného přístupu, které nemají přiřazené žádné uživatele a skupiny. |
| **Ovládací prvky přístupu** | Vyberte alespoň jeden ovládací prvek přístupu. | Při splnění podmínek musí procesor zásad vědět, co má dělat. |

## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="how-are-conditional-access-policies-applied"></a>Jak se používají zásady podmíněného přístupu?

Při přístupu ke cloudové aplikaci se můžou použít víc než jedna zásada podmíněného přístupu. V takovém případě musí být splněné všechny zásady, které platí. Pokud například jedna zásada vyžaduje vícefaktorové ověřování (MFA) a jiná vyžaduje vyhovující zařízení, je nutné provést ověřování MFA a použít vyhovující zařízení. 

Všechny zásady se vynutily ve dvou fázích:

- Fáze 1: shromáždění podrobností relace 
   - Shromážděte podrobnosti o relaci, jako je umístění uživatele a identita zařízení, které bude nutné pro vyhodnocení zásad. 
   - V průběhu této fáze se uživatelům může zobrazit výzva k zadání certifikátu, pokud je dodržování předpisů zařízením součástí zásad podmíněného přístupu. Tato výzva se může vyskytnout pro aplikace prohlížeče, pokud operační systém zařízení není Windows 10. 
   - Fáze 1 vyhodnocení zásad probíhá pro povolené zásady a zásady v [režimu pouze sestavy](concept-conditional-access-report-only.md).
- Fáze 2: vynucení 
   - Pomocí podrobností o relacích shromážděných ve fázi 1 identifikujte všechny požadavky, které nebyly splněny. 
   - Pokud máte zásadu, která je nakonfigurovaná tak, aby blokovala přístup, pomocí ovládacího prvku udělení bloku zastavte vynucení a uživatel se zablokuje. 
   - Uživatel pak bude vyzván k dokončení dalších požadavků na řízení udělení, které nebyly splněny během fáze 1 v následujícím pořadí, dokud nesplní zásady:  
      - Ověřování pomocí služby Multi-Factor Authentication 
      - Schválená klientská aplikace/zásada ochrany aplikací 
      - Spravované zařízení (s odpovídajícím nebo hybridním připojením k Azure AD) 
      - Podmínky použití 
      - Vlastní ovládací prvky  
      - Jakmile budou uděleny ovládací prvky, použijte ovládací prvky relace (vynutila aplikace, Microsoft Cloud App Security a životnost tokenu). 
   - Fáze 2 vyhodnocení zásad probíhá u všech povolených zásad. 

### <a name="how-are-assignments-evaluated"></a>Jak se vyhodnocuje přiřazení?

Všechna přiřazení jsou logicky **ANDed**. Pokud máte nakonfigurované více než jedno přiřazení, musí být pro aktivaci zásady splněné všechna přiřazení.  

Pokud potřebujete nakonfigurovat podmínku umístění, která bude platit pro všechna připojení vytvořená mimo síť vaší organizace:

- Zahrnout **všechna umístění**
- Vyloučit **všechny důvěryhodné IP adresy**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Co dělat, když jste na portálu pro správu Azure AD uzamčeni?

Pokud se z portálu Azure AD zamknete z důvodu nesprávného nastavení v zásadách podmíněného přístupu:

- Ověřte, že ve vaší organizaci jsou jiní správci, kteří nejsou ještě blokovaný. Správce s přístupem k Azure Portal může zakázat zásadu, která má vliv na vaše přihlášení. 
- Pokud žádné z správců ve vaší organizaci nemůžou zásady aktualizovat, musíte odeslat žádost o podporu. Podpora Microsoftu může zkontrolovat a aktualizovat zásady podmíněného přístupu, které zabraňují přístupu.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Co se stane, když máte zásady na portálu Azure Classic a Azure Portal nakonfigurovaná?  

Obě zásady se vynutily Azure Active Directory a uživatel získá přístup jenom v případě, že jsou splněné všechny požadavky.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Co se stane, když máte zásady na portálu Silverlight pro Intune a Azure Portal?

Obě zásady se vynutily Azure Active Directory a uživatel získá přístup jenom v případě, že jsou splněné všechny požadavky.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Co se stane, když mám nakonfigurovaná víc zásad pro stejného uživatele?  

Pro každé přihlášení Azure Active Directory vyhodnotí všechny zásady a zajistí splnění všech požadavků před udělením přístupu uživateli. Zablokovat přístup trumfy všechna ostatní nastavení konfigurace. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>Pracuje podmíněný přístup s Exchange ActiveSync?

Ano, v zásadách podmíněného přístupu můžete použít Exchange ActiveSync.

Některé cloudové aplikace, jako je SharePoint nebo Exchange Online, podporují i starší protokoly ověřování. Když klientská aplikace může pro přístup ke cloudové aplikaci použít starší verzi ověřovacího protokolu, Azure AD nemůže vynutilit zásady podmíněného přístupu u tohoto pokusu o přístup. Pokud chcete, aby klientská aplikace nemohly obejít vynucování zásad, měli byste ověřit, jestli je možné povolit jenom moderní ověřování u ovlivněných cloudových aplikací.

### <a name="how-should-you-configure-conditional-access-with-microsoft-365-apps"></a>Jak byste měli nakonfigurovat podmíněný přístup pomocí aplikací Microsoft 365?

Vzhledem k tomu, že Microsoft 365 aplikace jsou vzájemně propojeny, doporučujeme při vytváření zásad přiřazovat běžně používané aplikace.

Mezi běžné propojené aplikace patří Microsoft Flow, Microsoft Planner, Microsoft teams, Exchange Online, SharePoint a Yammer.

Je důležité pro zásady, které vyžadují interakci s uživatelem, jako je Multi-Factor Authentication, když se přístup řídí na začátku relace nebo úlohy. Pokud to neuděláte, uživatelé nebudou moci dokončit některé úkoly v rámci aplikace. Například pokud požadujete službu Multi-Factor Authentication na nespravovaných zařízeních pro přístup k SharePointu, ale ne k e-mailu, uživatelé, kteří pracují s e-maily, nebudou moci připojit soubory SharePoint ke zprávě. Další informace najdete v článku [co jsou závislosti služby v Azure Active Directory podmíněný přístup?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>K čemu byste se měli vyhnout

Rozhraní podmíněného přístupu poskytuje skvělou flexibilitu konfigurace. Ale skvělá flexibilita také znamená, že před vydáním byste měli pečlivě zkontrolovat všechny zásady konfigurace, aby nedocházelo k nežádoucím výsledkům. V tomto kontextu byste měli věnovat zvláštní pozornost přiřazením, které mají vliv na kompletní sady, například na **všechny uživatele/skupiny/cloudové aplikace**.

Ve vašem prostředí byste se měli vyhnout následujícím konfiguracím:

**Pro všechny uživatele Cloud Apps:**

- **Blokovat přístup** – Tato konfigurace blokuje celou organizaci, což znamená, že nebudete mít dobrou představu.
- **Vyžadovat vyhovující zařízení** – pro uživatele, kteří ještě nezaregistrovali svá zařízení, tato zásada blokuje veškerý přístup, včetně přístupu k portálu Intune. Pokud jste správcem bez zaregistrovaného zařízení, tato zásada vás zablokuje, abyste se znovu přihlásili k Azure Portal ke změně zásad.
- **Vyžadovat připojení k doméně** – tato zásada blokuje přístup také k blokování přístupu pro všechny uživatele ve vaší organizaci, pokud ještě nemáte zařízení připojené k doméně.
- **Vyžadovat zásady ochrany aplikací** – tato zásada blokuje přístup i pro všechny uživatele ve vaší organizaci, pokud nemáte zásady Intune. Pokud jste správce bez klientské aplikace, která má zásady ochrany aplikací Intune, bude vám tato zásada Blokovat návrat do portálů, jako je Intune a Azure.

**Pro všechny uživatele, všechny cloudové aplikace, všechny platformy zařízení:**

- **Blokovat přístup** – Tato konfigurace blokuje celou organizaci, což znamená, že nebudete mít dobrou představu.

## <a name="how-should-you-deploy-a-new-policy"></a>Jak byste měli nasadit novou zásadu?

Jako první krok byste měli vyhodnotit zásady pomocí [nástroje co if](what-if-tool.md).

Až budou nové zásady připravené pro vaše prostředí, nasaďte je do fází:

1. Použijte zásadu na malou skupinu uživatelů a ověřte, že se chová podle očekávání. 
1. Když rozbalíte zásadu, aby zahrnovala více uživatelů. Pokračujte v vyloučení všech správců ze zásad, abyste měli jistotu, že mají stále přístup, a pokud je potřeba změnit, můžete zásady aktualizovat.
1. Zásadu použijte pro všechny uživatele, pouze pokud je to nutné. 

Osvědčeným postupem je vytvořit uživatelský účet, který je:

- Vyhrazeno pro správu zásad 
- Vyloučené ze všech vašich zásad

## <a name="policy-migration"></a>Migrace zásad

Zvažte možnost Migrace zásad, které jste nevytvořili v Azure Portal z těchto důvodů:

- Nyní můžete řešit scénáře, které jste předtím nemohli zpracovat.
- Počet zásad, které budete muset spravovat, můžete snížit tak, že je konsolidujete.   
- Všechny zásady podmíněného přístupu můžete spravovat v jednom centrálním umístění.
- Portál Azure Classic byl vyřazený.   

Další informace najdete v článku [Migrace zásad z portálu Classic na portálu Azure Portal](policy-migration.md).

## <a name="next-steps"></a>Další kroky

Chcete-li znát:

- Postup konfigurace zásad podmíněného přístupu najdete v tématu [vyžádání MFA pro konkrétní aplikace s Azure Active Directorym podmíněným přístupem](../authentication/tutorial-enable-azure-mfa.md).
- Postup plánování zásad podmíněného přístupu najdete v tématu [postup plánování nasazení podmíněného přístupu v Azure Active Directory](plan-conditional-access.md).