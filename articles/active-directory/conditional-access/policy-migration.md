---
title: Migrace zásad podmíněného přístupu – Azure Active Directory
description: Zjistěte, co potřebujete vědět o migraci klasických zásad v Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380542"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Co je migrace zásad v Azure Active Directory podmíněný přístup? 

[Podmíněný přístup](../active-directory-conditional-access-azure-portal.md) je schopnost Azure Active Directory (Azure AD), která umožňuje řídit, jak autorizovaným uživatelům přistupují k vašim cloudovým aplikacím. I když je účel stále stejný, vydaná verze nového Azure Portal zavedla významná vylepšení, jak podmíněný přístup funguje.

Zvažte možnost Migrace zásad, které jste nevytvořili v Azure Portal z těchto důvodů:

- Nyní můžete řešit scénáře, které jste předtím nemohli zpracovat.
- Počet zásad, které budete muset spravovat, můžete snížit tak, že je konsolidujete.   
- Všechny zásady podmíněného přístupu můžete spravovat v jednom centrálním umístění.
- Portál Azure Classic bude vyřazený.   

Tento článek vysvětluje, co potřebujete znát, abyste mohli migrovat stávající zásady podmíněného přístupu do nové architektury.
 
## <a name="classic-policies"></a>Klasické zásady

Na stránce [Azure Portal](https://portal.azure.com)je stránka [podmíněného přístupu-policies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) vaším vstupním bodem pro zásady podmíněného přístupu. Ve vašem prostředí ale taky můžete mít zásady podmíněného přístupu, které jste nevytvořili pomocí této stránky. Tyto zásady se označují jako *klasické zásady*. Klasické zásady jsou zásady podmíněného přístupu, které jste vytvořili v:

- Portál Azure Classic
- Klasický portál Intune
- Portál Intune App Protection

Na stránce **podmíněný přístup** můžete získat přístup k klasickým zásadám kliknutím na [**klasické zásady (Preview)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) v části **Správa** . 

![Azure Active Directory](./media/policy-migration/71.png)

Zobrazení **klasických zásad** nabízí možnost:

- Filtrování klasických zásad.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- Zakáže klasické zásady.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Zkontrolujte nastavení klasických zásad (a zakažte je).

   ![Azure Active Directory](./media/policy-migration/74.png)

Pokud jste zakázali klasické zásady, nemůžete tento krok již vrátit zpět. To je důvod, proč můžete členství ve skupině upravit v klasických zásadách pomocí zobrazení **podrobností** . 

![Azure Active Directory](./media/policy-migration/75.png)

Změnou vybraných skupin nebo vyloučením konkrétních skupin můžete otestovat efekt zakázané klasické zásady pro několik testovacích uživatelů ještě před tím, než zakážete zásady pro všechny zahrnuté uživatele a skupiny. 

## <a name="azure-ad-conditional-access-policies"></a>Zásady podmíněného přístupu Azure AD

Pomocí podmíněného přístupu v Azure Portal můžete spravovat všechny zásady v jednom centrálním umístění. Vzhledem k tomu, že implementace podmíněného přístupu se změnila, měli byste se před migrací klasických zásad seznámit se základními koncepty.

Další informace:

- [Princip podmíněného přístupu v Azure Active Directory](../active-directory-conditional-access-azure-portal.md) k získání informací o základních konceptech a terminologii.
- [Osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md) k získání některých pokynů k nasazení podmíněného přístupu ve vaší organizaci.
- [Vyžadovat MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md) , abyste se seznámili s uživatelským rozhraním Azure Portal.
 
## <a name="migration-considerations"></a>Požadavky na migraci

V tomto článku se jako *nové zásady*označují taky zásady podmíněného přístupu Azure AD.
Vaše klasické zásady budou dál fungovat s novými zásadami, dokud je nezakážete nebo neodstraníte. 

V souvislosti s konsolidací zásad jsou důležité tyto aspekty:

- I když jsou klasické zásady vázané na určitou cloudovou aplikaci, můžete v nové zásadě vybrat tolik cloudových aplikací, kolik potřebujete.
- Ovládací prvky klasické zásady a nové zásady pro cloudovou aplikaci vyžadují splnění všech ovládacích prvků (*a*). 
- V nové zásadě můžete:
   - Pokud to váš scénář vyžaduje, kombinovat více podmínek. 
   - Vyberte několik požadavků grantu jako řízení přístupu a Zkombinujte je s logickým operátorem *or* (vyžadovat jeden z vybraných ovládacích prvků) nebo s logickým operátorem *and* (vyžadovat všechny vybrané ovládací prvky).

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Pokud chcete migrovat klasické zásady pro **Office 365 Exchange Online** , které zahrnují **Exchange Active Sync** jako podmínku klientské aplikace, možná je nebudete moct sloučit do jedné nové zásady. 

Jedná se například o případ, kdy chcete podporovat všechny typy klientských aplikací. V nové zásadě, která má **Exchange Active Sync** jako podmínku klientské aplikace, nemůžete vybrat další klientské aplikace.

![Azure Active Directory](./media/policy-migration/64.png)

Sloučení do jedné nové zásady není možné ani v případě, že klasické zásady obsahují několik podmínek. Nová zásada, která má nakonfigurovaný stav **Exchange Active Sync** jako klientské aplikace, nepodporuje další podmínky:   

![Azure Active Directory](./media/policy-migration/08.png)

Pokud máte novou zásadu, která má nakonfigurované podmínky **Exchange Active Sync** jako klientské aplikace, musíte se ujistit, že nejsou nakonfigurované žádné další podmínky. 

![Azure Active Directory](./media/policy-migration/16.png)
 
Klasické zásady [založené na](technical-reference.md#approved-client-app-requirement) aplikacích pro Office 365 Exchange Online, které zahrnují **Exchange Active Sync** jako stav klientských aplikací, umožňují použití **podporovaných** a **nepodporovaných** [platforem zařízení](technical-reference.md#device-platform-condition). I když v souvisejících nových zásadách nemůžete konfigurovat jednotlivé platformy zařízení, můžete omezit podporu jenom na [podporované platformy zařízení](technical-reference.md#device-platform-condition) . 

![Azure Active Directory](./media/policy-migration/65.png)

Můžete konsolidovat více klasických zásad, které zahrnují **Exchange Active Sync** jako podmínku klientské aplikace, pokud mají:

- Pouze **Exchange Active Sync** jako podmínka 
- Několik požadavků pro udělení přístupu je nakonfigurované.

Jedním z běžných scénářů je konsolidace:

- Klasické zásady na základě zařízení z portálu Azure Classic 
- Klasické zásady na základě aplikace na portálu Intune App Protection 
 
V takovém případě můžete své klasické zásady konsolidovat do jedné nové zásady, která má obě požadavky vybrány.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Platformy zařízení

Klasické zásady s [ovládacími prvky založenými na aplikaci](technical-reference.md#approved-client-app-requirement) jsou předem nakonfigurované s iOS a Androidem jako s [podmínkou platformy zařízení](technical-reference.md#device-platform-condition). 

V nové zásadě musíte vybrat [platformy zařízení](technical-reference.md#device-platform-condition) , které chcete podporovat samostatně.

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [vyžádání MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md). 
