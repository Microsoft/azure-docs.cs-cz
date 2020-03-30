---
title: Migrace zásad podmíněného přístupu – Azure Active Directory
description: Přečtěte si, co potřebujete vědět, abyste na webu Azure Portal migrovali klasické zásady.
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
ms.openlocfilehash: 33b1f76dd1489e00115d0f805add8d754038df84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185913"
---
# <a name="conditional-access-classic-policy-migration"></a>Migrace klasických zásad podmíněného přístupu

Podmíněný přístup je nástroj používaný službou Azure Active Directory k sbližování signálů, rozhodování a vynucování zásad organizace. Podmíněný přístup je jádrem nové řídicí roviny řízené identitou. Zatímco účel je stále stejný, vydání nového portálu Azure zavedla významná vylepšení fungování podmíněného přístupu.

Zvažte migraci zásad, které jste na webu Azure Portal nevytvořili, protože:

- Nyní můžete řešit scénáře, které jste dříve nemohli zpracovat.
- Můžete snížit počet zásad, které je třeba spravovat jejich sloučením.
- Všechny zásady podmíněného přístupu můžete spravovat v jednom centrálním umístění.
- Klasický portál Azure bude vyřazen.

Tento článek vysvětluje, co potřebujete vědět, abyste migrovali stávající zásady podmíněného přístupu do nového rámce.

## <a name="classic-policies"></a>Klasické zásady

Na [webu Azure Portal](https://portal.azure.com)najdete zásady podmíněného přístupu v části**Podmíněný přístup****služby** >  **Azure Active Directory** > . Vaše organizace může mít také starší zásady podmíněného přístupu, které nejsou vytvořeny pomocí této stránky. Tyto zásady jsou označovány jako *klasické zásady*. Klasické zásady jsou zásady podmíněného přístupu, které jste vytvořili v:

- Klasický portál Azure
- Klasický portál Intune
- Portál ochrany aplikací Intune

Na stránce **Podmíněný přístup** můžete přistupovat ke klasickým zásadám kliknutím na [**klasické zásady**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) v části **Spravovat.** 

![Podmíněný přístup ve službě Azure AD zobrazující zobrazení klasických zásad](./media/policy-migration/71.png)

Zobrazení **Klasické zásady** poskytuje možnost:

- Filtrujte klasické zásady.
- Zakažte klasické zásady.
- Zkontrolujte nastavení klasické zásady a zakažte ji.

   ![Klasické podrobnosti o zásadách včetně existující konfigurace zásad](./media/policy-migration/74.png)

> [!WARNING]
> Po zakázání nelze klasickou zásadu znovu povolit.

Zobrazení podrobností klasické zásady umožňuje dokumentovat nastavení, upravit zahrnuté nebo vyloučené skupiny a zakázat zásady.

![Podrobnosti o zásadách – skupiny, které mají být zahrnuty nebo vyloučeny](./media/policy-migration/75.png)

Změnou vybraných skupin nebo vyloučením konkrétních skupin můžete otestovat účinek zakázané klasické zásady pro několik testovacích uživatelů před zakázáním zásady pro všechny zahrnuté uživatele a skupiny.
 
## <a name="migration-considerations"></a>Požadavky na migraci

V tomto článku zásady podmíněného přístupu Azure AD se také označují jako *nové zásady*.
Klasické zásady nadále fungují vedle nových zásad, dokud je nezakážete nebo neodstraníte. 

V souvislosti s konsolidací politik jsou důležité tyto aspekty:

- Zatímco klasické zásady jsou vázané na konkrétní cloudovou aplikaci, můžete vybrat tolik cloudových aplikací, kolik potřebujete v nové zásadě.
- Ovládací prvky klasické zásady a nové zásady pro cloudovou aplikaci vyžadují splnění všech ovládacích prvků *(AND).* 
- V nové zásadě můžete:
   - Kombinovat více podmínek, pokud to vyžaduje váš scénář. 
   - Vyberte několik požadavků na udělení grantu jako řízení přístupu a zkombinujte je s logickým *operátorem OR* (vyžadovat jeden z vybraných ovládacích prvků) nebo s logickým *operátorem AND* (vyžaduje všechny vybrané ovládací prvky).

### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Pokud chcete migrovat klasické zásady pro **Office 365 Exchange online,** které zahrnují **Exchange Active Sync** jako podmínku klientských aplikací, nemusí být možné je konsolidovat do jedné nové zásady. 

To je například případ, pokud chcete podporovat všechny typy klientských aplikací. V nové zásadě, která má **Exchange Active Sync** jako podmínku klientských aplikací, nemůžete vybrat jiné klientské aplikace.

![Podmíněný přístup při výběru klientských aplikací](./media/policy-migration/64.png)

Konsolidace do jedné nové zásady také není možné, pokud vaše klasické zásady obsahují několik podmínek. Nová zásada, která má **Exchange Active Sync** jako podmínku klientských aplikací nakonfigurované nepodporuje další podmínky:   

![Exchange ActiveSync nepodporuje vybrané podmínky](./media/policy-migration/08.png)

Pokud máte novou zásadu, která má **Exchange Active Sync** jako podmínku klientských aplikací nakonfigurované, musíte se ujistit, že všechny ostatní podmínky nejsou nakonfigurovány. 

![Podmínky podmíněného přístupu](./media/policy-migration/16.png)
 
Klasické zásady založené na aplikacích pro Office 365 Exchange Online, které zahrnují **Exchange Active Sync** jako podmínku klientských aplikací, umožňují **podporované** a **nepodporované** platformy zařízení. I když nelze nakonfigurovat jednotlivé platformy zařízení v související nové zásady, můžete omezit podporu pouze na [podporované platformy zařízení.](concept-conditional-access-conditions.md#device-platforms) 

![Podmíněný přístup, výběr Exchange ActiveSync](./media/policy-migration/65.png)

Můžete konsolidovat více klasických zásad, které zahrnují **Exchange Active Sync** jako podmínku klientských aplikací, pokud mají:

- Pouze **Exchange Active Sync** jako podmínku 
- Několik požadavků na udělení nakonfigurovaného přístupu

Jedním z běžných scénářů je konsolidace:

- Klasická zásada založená na zařízení z klasického portálu Azure 
- Klasické zásady založené na aplikacích na portálu ochrany aplikací Intune 
 
V takovém případě můžete konsolidovat klasické zásady do jedné nové zásady, která má oba požadavky vybrané.

![Ovládací prvky udělení podmíněného přístupu](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Platformy zařízení

Klasické zásady s ovládacími prvky založenými na aplikacích jsou předem nakonfigurované s iOS a Androidem jako podmínkou platformy zařízení. 

V nové zásadě je třeba vybrat [platformy zařízení, které](concept-conditional-access-conditions.md#device-platforms) chcete podporovat jednotlivě.

![Výběr platforem zařízení podmíněného přístupu](./media/policy-migration/41.png)

## <a name="next-steps"></a>Další kroky

- [Použití režimu pouze pro sestavu pro podmíněný přístup k určení dopadu nových rozhodnutí zásad.](concept-conditional-access-report-only.md)
- Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu, [přečtěte si téma Běžné zásady podmíněného přístupu](concept-conditional-access-policy-common.md).
- Pokud jste připraveni nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, přečtěte si článek [How To: Plánování nasazení podmíněného přístupu ve službě Azure Active Directory](plan-conditional-access.md). 
