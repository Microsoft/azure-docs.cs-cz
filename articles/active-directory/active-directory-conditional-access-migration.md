---
title: Co je migrace zásad podmíněného přístupu Azure Active Directory? | Dokumenty Microsoft
description: Zjistěte, co potřebujete vědět o migraci na portálu Azure classic zásad.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 4a9b3df66567c4170ba861d3e597261e37271bf1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232286"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Co je migrace zásad podmíněného přístupu Azure Active Directory? 


[Podmíněný přístup](active-directory-conditional-access-azure-portal.md) je funkce služby Azure Active directory (Azure AD), jak vám umožní řídit oprávnění uživatelům přístup cloudových aplikací. Účelem je pořád stejné, má verzi na nový portál Azure zavedla významná vylepšení na tom, jak podmíněný přístup funguje.

Měli byste zvážit, zásady, které jste dosud nevytvořili na portálu Azure, protože migrace:

- Scénáře, které nelze zpracovat před můžete vyřešit.

- Můžete snížit počet zásad, které budete muset spravovat konsolidovat.   

- Můžete spravovat všechny zásady podmíněného přístupu v jednom centrálním místě.

- Portál Azure classic vyřadí.   

Tento článek vysvětluje, co potřebujete vědět o migraci stávající zásady podmíněného přístupu na nové architektury.
 
## <a name="classic-policies"></a>Klasické zásady

V [portál Azure](https://portal.azure.com), [podmíněný přístup – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) stránka je zásady vaší vstupní bod do podmíněného přístupu. Ve vašem prostředí, může však také mít zásady podmíněného přístupu, které jste dosud nevytvořili pomocí této stránky. Tyto zásady se označují jako *classic zásady*. Zásady podmíněného přístupu jsou Classic zásady, které jste vytvořili v:

- Portál Azure classic
- Klasický portál služby Intune
- Na portálu ochrany aplikace Intune


Na **podmíněného přístupu** stránky, dostanete classic zásad kliknutím [ **zásady Classic (preview)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) v **spravovat** oddíl. 


![Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


**Classic zásady** zobrazení vám poskytuje možnost:

- Filtrujte classic zásad.
 
    ![Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- Zakážete classic zásady.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- Zkontrolujte nastavení classic zásad (a ji zakázat).

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


Pokud jste zakázali zásadu classic, bude možné už vrátit tento krok. Z tohoto důvodu můžete upravit členství ve skupině v classic zásad pomocí **podrobnosti** zobrazení. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

Změnou vybrané skupiny nebo vyloučení konkrétních skupin můžete otestovat efektu zakázané classic zásad pro několik testovací uživatele před jeho zakázáním zásady pro všechny zahrnuté uživatele a skupiny. 



## <a name="azure-ad-conditional-access-policies"></a>Zásady podmíněného přístupu Azure AD

Pomocí podmíněného přístupu na portálu Azure můžete spravovat všechny zásady v jednom centrálním místě. Protože se změnil výrazně implementace jak podmíněný přístup, byste si měli přečíst se základními koncepcemi před migrací classic zásad.

Přečtěte si:

- [Co je podmíněného přístupu v Azure Active Directory](active-directory-conditional-access-azure-portal.md) Další informace o základních koncepcích a technologiím.

- [Osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md) získat některé pokyny k nasazení podmíněného přístupu ve vaší organizaci.

- [Vícefaktorové ověřování vyžadovat pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](active-directory-conditional-access-app-based-mfa.md) Seznamte se s uživatelského rozhraní na portálu Azure.


 
## <a name="migration-considerations"></a>Posouzení migrace

V tomto článku zásady podmíněného přístupu Azure AD jsou také označovány jako *nové zásady*.
Classic zásad pokračovat v práci node souběžně s nové zásady, dokud nebude zakázat nebo smazat. 

Tyto aspekty jsou důležité v kontextu konsolidace zásad:

- Při classic zásady jsou svázané s konkrétní cloudové aplikace, můžete vybrat libovolný počet cloudových aplikací, jako je třeba v nové zásady.

- Ovládací prvky nové zásady pro cloudové aplikace a classic zásad vyžadovat všechny ovládací prvky (*a*) musí být splněny. 


- V nové zásady můžete:
 
    - Pokud to vyžaduje váš scénář, kombinovat více podmínek. 

    - Vyberte několik udělit požadavky na přístup řízení a zkombinovat s logickou *nebo* (vyžadují jeden z vybraných ovládacích prvků) nebo s logickou *a* (vyžadují všechny vybrané ovládací prvky).

        ![Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Pokud chcete migrovat classic zásady pro **Office 365 Exchange online** , zahrnout **protokolu Exchange Active Sync** jako podmínku aplikace klienta, nemusí být možné konsolidovat je do jedné nové zásady. 

To nastane, například pokud chcete podporovat všechny typy aplikací klienta. V nové zásady, který má **protokolu Exchange Active Sync** jako podmínku aplikace klienta, nelze vybrat jiné klientské aplikace.

![Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

Konsolidace do jedné nové zásady není možné v případě, že vaše classic zásady obsahují několik podmínek. Nová zásada, která má **protokolu Exchange Active Sync** podmínky, které jsou nakonfigurované jako klientské aplikace nepodporuje další podmínky:   

![Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

Pokud máte novou zásadu, která má **protokolu Exchange Active Sync** jako klientské aplikace podmínky nakonfigurované, musíte zajistit, aby nebyly nakonfigurovány všechny další podmínky. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

[Na základě aplikace](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) classic zásady pro Office 365 Exchange Online, které obsahují **protokolu Exchange Active Sync** jako podmínku aplikace klienta povolit **podporované** a **nepodporovaný** [platformy zařízení](active-directory-conditional-access-technical-reference.md#device-platform-condition). Při platformy jednotlivých zařízení nelze konfigurovat v související nové zásady, můžete omezit podporu, aby [podporované platformy zařízení](active-directory-conditional-access-technical-reference.md#device-platform-condition) pouze. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

Může konsolidovat více classic zásady, které obsahují **protokolu Exchange Active Sync** jako podmínku aplikace klienta, pokud mají:

- Pouze **protokolu Exchange Active Sync** jako podmínku 

- Několik požadavků pro udělení přístupu nakonfigurované

Jeden běžný scénář je konsolidací:

- Zásady klasické založené na zařízení z portálu Azure classic 
- Zásady klasické založené na aplikaci na portálu služby Intune aplikaci ochrany 
 
V takovém případě lze konsolidovat classic zásad do jednoho nová zásada, která má oba požadavky na vybrané.

![Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>Platformy zařízení

Classic zásad [ovládací prvky založené na aplikaci](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) jsou předem nakonfigurovaná s iOS a Android, jako [podmínku platformy zařízení](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

V nové zásady, budete muset vybrat možnost [platformy zařízení](active-directory-conditional-access-technical-reference.md#device-platform-condition) chcete podporují individuálně.

![Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>Další postup

- Pokud chcete vědět, jak konfigurovat zásadu podmíněného přístupu, najdete v článku [GRequire MFA pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md). 
