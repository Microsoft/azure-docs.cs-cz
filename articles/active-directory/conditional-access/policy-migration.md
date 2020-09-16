---
title: Migrace zásad podmíněného přístupu – Azure Active Directory
description: Zjistěte, co potřebujete vědět o migraci klasických zásad v Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1811d5b9ae4d3e34b48e1cdc156438f2bad98d1
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601650"
---
# <a name="conditional-access-classic-policy-migration"></a>Migrace klasických zásad podmíněného přístupu

Podmíněný přístup je nástroj používaný Azure Active Directory k spojování signálů, k rozhodování a vymáhání zásad organizace. Podmíněný přístup je srdcem nové roviny ovládacího prvku na základě identity. I když je účel stále stejný, vydaná verze nového Azure Portal zavedla významná vylepšení, jak podmíněný přístup funguje.

Zvažte možnost Migrace zásad, které jste nevytvořili v Azure Portal z těchto důvodů:

- Nyní můžete řešit scénáře, které jste předtím nemohli zpracovat.
- Počet zásad, které budete muset spravovat, můžete snížit tak, že je konsolidujete.
- Všechny zásady podmíněného přístupu můžete spravovat v jednom centrálním umístění.
- Portál Azure Classic bude vyřazený.

Tento článek vysvětluje, co potřebujete znát, abyste mohli migrovat stávající zásady podmíněného přístupu do nové architektury.

## <a name="classic-policies"></a>Klasické zásady

V [Azure Portal](https://portal.azure.com)se zásady podmíněného přístupu dají najít v části **Azure Active Directory**  >  **Security**  >  **podmíněný přístup**zabezpečení. Vaše organizace může mít také starší zásady podmíněného přístupu, které nejsou vytvořeny pomocí této stránky. Tyto zásady se označují jako *klasické zásady*. Klasické zásady jsou zásady podmíněného přístupu, které jste vytvořili v:

- Portál Azure Classic
- Klasický portál Intune
- Portál Intune App Protection

Na stránce **podmíněný přístup** můžete získat přístup k klasickým zásadám kliknutím na [**klasické zásady**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) v části **Správa** . 

![Podmíněný přístup ve službě Azure AD znázorňující zobrazení klasických zásad](./media/policy-migration/71.png)

Zobrazení **klasických zásad** nabízí možnost:

- Filtrování klasických zásad.
- Zakáže klasické zásady.
- Zkontrolujte nastavení klasické zásady a zakažte ji.

   ![Podrobnosti o klasických zásadách včetně existující konfigurace zásad](./media/policy-migration/74.png)

> [!WARNING]
> Po zakázání se klasické zásady nedají znovu povolit.

Zobrazení podrobností klasických zásad vám umožní dokumentovat nastavení, upravit zahrnuté nebo vyloučené skupiny a zásadu Zakázat.

![Podrobnosti zásad – skupiny, které se mají zahrnout nebo vyloučit](./media/policy-migration/75.png)

Změnou vybraných skupin nebo vyloučením konkrétních skupin můžete otestovat účinek zakázaných klasických zásad pro několik testovacích uživatelů, než zakážete zásady pro všechny zahrnuté uživatele a skupiny.
 
## <a name="migration-considerations"></a>Posouzení migrace

V tomto článku se jako *nové zásady*označují taky zásady podmíněného přístupu Azure AD.
Vaše klasické zásady budou dál fungovat s novými zásadami, dokud je nezakážete nebo neodstraníte. 

V souvislosti s konsolidací zásad jsou důležité tyto aspekty:

- I když jsou klasické zásady vázané na určitou cloudovou aplikaci, můžete v nové zásadě vybrat tolik cloudových aplikací, kolik potřebujete.
- Ovládací prvky klasické zásady a nové zásady pro cloudovou aplikaci vyžadují splnění všech ovládacích prvků (*a*). 
- V nové zásadě můžete:
   - Pokud to váš scénář vyžaduje, kombinovat více podmínek. 
   - Vyberte několik požadavků grantu jako řízení přístupu a Zkombinujte je s logickým operátorem *or* (vyžadovat jeden z vybraných ovládacích prvků) nebo s logickým operátorem *and* (vyžadovat všechny vybrané ovládací prvky).

### <a name="exchange-online"></a>Exchange Online

Pokud chcete migrovat klasické zásady pro **Exchange Online** , které zahrnují **Exchange Active Sync** stav klientských aplikací, možná je nebudete moct sloučit do jedné nové zásady. 

Jedná se například o případ, kdy chcete podporovat všechny typy klientských aplikací. V nové zásadě, která má **Exchange Active Sync** jako podmínku klientské aplikace, nemůžete vybrat další klientské aplikace.

![Podmíněný přístup výběru klientských aplikací](./media/policy-migration/64.png)

Sloučení do jedné nové zásady není možné ani v případě, že klasické zásady obsahují několik podmínek. Nová zásada, která má nakonfigurovaný stav **Exchange Active Sync** jako klientské aplikace, nepodporuje další podmínky:   

![Exchange ActiveSync nepodporuje vybrané podmínky.](./media/policy-migration/08.png)

Pokud máte novou zásadu, která má nakonfigurované podmínky **Exchange Active Sync** jako klientské aplikace, musíte se ujistit, že nejsou nakonfigurované žádné další podmínky. 

![Podmínky podmíněného přístupu](./media/policy-migration/16.png)
 
Klasické zásady na základě aplikací pro Exchange Online, které zahrnují **Exchange Active Sync** jako podmínky klientských aplikací, umožňují použití **podporovaných** a **nepodporovaných** platforem zařízení. I když v souvisejících nových zásadách nemůžete konfigurovat jednotlivé platformy zařízení, můžete omezit podporu jenom na [podporované platformy zařízení](concept-conditional-access-conditions.md#device-platforms) . 

![Podmíněný přístup vybrat Exchange ActiveSync](./media/policy-migration/65.png)

Můžete konsolidovat více klasických zásad, které zahrnují **Exchange Active Sync** jako podmínku klientské aplikace, pokud mají:

- Pouze **Exchange Active Sync** jako podmínka 
- Několik požadavků pro udělení přístupu je nakonfigurované.

Jedním z běžných scénářů je konsolidace:

- Klasické zásady na základě zařízení z portálu Azure Classic 
- Klasické zásady na základě aplikace na portálu Intune App Protection 
 
V takovém případě můžete své klasické zásady konsolidovat do jedné nové zásady, která má obě požadavky vybrány.

![Ovládací prvky pro udělení podmíněného přístupu](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Platformy zařízení

Klasické zásady s ovládacími prvky založenými na aplikaci jsou předem nakonfigurované s iOS a Androidem jako s podmínkou platformy zařízení. 

V nové zásadě musíte vybrat [platformy zařízení](concept-conditional-access-conditions.md#device-platforms) , které chcete podporovat samostatně.

![Výběr platforem zařízení pro podmíněný přístup](./media/policy-migration/41.png)

## <a name="next-steps"></a>Další kroky

- [Použijte režim pouze pro sestavy pro podmíněný přístup k určení dopadu nových rozhodnutí o zásadách.](concept-conditional-access-report-only.md)
- Pokud chcete zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [běžné zásady podmíněného přístupu](concept-conditional-access-policy-common.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si článek [Postup: plánování nasazení podmíněného přístupu v Azure Active Directory](plan-conditional-access.md). 
