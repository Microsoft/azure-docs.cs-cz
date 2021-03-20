---
title: Co je zřizování pomocí Azure Active Directory? | Dokumentace Microsoftu
description: Popisuje přehled zřizování identit a scénářů ILM.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 640367d1b833f61e8a83fe9ce6b14d6d799cf9b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172448"
---
# <a name="what-is-provisioning"></a>Co je zřizování?

Zřizování a rušení zřizování jsou procesy, které zajišťují konzistenci digitálních identit napříč různými systémy.  Tyto procesy se obvykle využívají v rámci [správy životního cyklu identit](what-is-identity-lifecycle-management.md).

**Zřizování** je procesy vytvoření identity v cílovém systému na základě určitých podmínek.  Zrušení **zřizování** je proces odebrání identity z cílového systému, pokud podmínky již nejsou splněné. **Synchronizace** je proces zachování zajištěného objektu, který je aktuální, takže zdrojový objekt a cílový objekt jsou podobné.

Například když se nový zaměstnanec připojí do vaší organizace, zadává se do systému HR.  V tomto okamžiku může zřizování **z** HR **na** Azure Active Directory (Azure AD) vytvořit odpovídající uživatelský účet ve službě Azure AD. Aplikace, které dotazují Azure AD, uvidí účet pro tohoto nového zaměstnance.  Pokud jsou k dispozici aplikace, které nepoužívají **službu Azure AD** , je zajištěno, že uživatel bude moci získat **přístup ke všem** aplikacím, ke kterým uživatel potřebuje mít přístup.  Tento proces umožňuje uživateli začít pracovat a mít přístup k aplikacím a systémům, které potřebují k prvnímu dni.  Podobně platí, že pokud se jejich vlastnosti, jako je například jejich oddělení nebo stav zaměstnanosti, mění v systému HR, synchronizace těchto aktualizací ze systému HR do služby Azure AD a navíc k ostatním aplikacím a cílovým databázím zajišťuje konzistenci.

Azure AD v současné době nabízí tři oblasti automatického zřizování.  Jsou to tyto:  

- Zřizování z externího neadresářového autoritativního systému záznamu do služby Azure AD prostřednictvím zřizování na **[základě lidských zdrojů](#hr-driven-provisioning)**  
- Zřizování z Azure AD do aplikací prostřednictvím **[zřizování aplikací](#app-provisioning)**  
- Zřizování mezi službou Azure AD a službou Active Directory Domain Services prostřednictvím **[zřizování mezi adresáři](#inter-directory-provisioning)** 

![Správa životního cyklu identit](media/what-is-provisioning/provisioning.png)

## <a name="hr-driven-provisioning"></a>Zřizování na základě lidských zdrojů

![Zřizování lidských zdrojů](media/what-is-provisioning/cloud-2a.png)

Zřizování z HR do Azure AD zahrnuje vytváření objektů, typicky identity uživatelů představujících každého zaměstnance, ale v některých případech jiné objekty, které představují oddělení nebo jiné struktury, na základě informací v systému HR.  

Nejběžnějším scénářem je, že když se do vaší společnosti připojí nový zaměstnanec, vstoupí do systému HR.  Jakmile k tomu dojde, automaticky se zřídí jako nový uživatel ve službě Azure AD, a to bez nutnosti zásahu správce pro každé nové přijetí.  Obecně platí, že zřizování z HR může zahrnovat následující scénáře.

- **Nábor nových zaměstnanců** – když se do systému HR přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory, Azure AD a volitelně v adresářích pro jiné aplikace, které podporuje Azure AD, a to s zpětným zápisem e-mailové adresy do systému hr.
- **Aktualizace atributů a profilů zaměstnanců** – když se v tomto systému HR aktualizuje záznam zaměstnance (například jeho jméno, název nebo manažer), automaticky se aktualizuje jeho uživatelský účet ve službě Active Directory, Azure AD a volitelně i na jiné aplikace, které Azure AD podporuje.
- **Ukončení zaměstnanců** – Pokud je zaměstnanec v HR, je jeho uživatelský účet automaticky zablokován z přihlášení nebo odebrání ve službě Active Directory, Azure AD a v jiných aplikacích.
- **Pracovní zařazení zaměstnanců** – Pokud se zaměstnanec znovu přiřadí do CLOUDového hru, jeho starý účet se dá automaticky znovu aktivovat nebo znovu zřídit (v závislosti na vašich preferencích).

Zřizování na základě lidských zdrojů s Azure AD nabízí tři možnosti nasazení:

1. Pro organizace s jedním předplatným v Workday nebo SuccessFactors a nepoužívejte službu Active Directory
1. Organizace s jedním předplatným v Workday nebo SuccessFactors a mají službu Active Directory i Azure AD
1. Pro organizace s více systémy HR nebo místní systém pro personální oddělení, jako je SAP, Oracle eBusiness nebo PeopleSoft spouštěných místně

Další informace najdete v tématu [co je zřizování na základě lidských zdrojů?](what-is-hr-driven-provisioning.md)

## <a name="app-provisioning"></a>Zřizování aplikací

![zřizování aplikací](media/what-is-provisioning/cloud-3b.png)

Termín **[zřizování aplikací](../app-provisioning/user-provisioning.md)** v Azure AD odkazuje na automatické vytváření kopií identit uživatelů v aplikacích, ke kterým uživatelé potřebují přistupovat, pro aplikace, které mají své vlastní úložiště dat, liší se od Azure AD nebo Active Directory. Kromě vytváření identit uživatelů zahrnuje zřizování aplikací také údržbu a odebírání uživatelských identit z těchto aplikací, když se změní stav nebo role uživatele. Mezi běžné scénáře patří zřízení uživatele Azure AD v aplikacích, jako jsou [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md), protože každá z těchto aplikací má vlastní uživatelské úložiště odlišné od Azure AD.

Další informace najdete v tématu [co je zřizování aplikací?](what-is-app-provisioning.md) .

## <a name="inter-directory-provisioning"></a>Zřizování mezi adresáři

![zřizování mezi adresáři](media/what-is-provisioning/cloud-4a.png)

Mnoho organizací spoléhá na službu Active Directory i Azure AD a může mít aplikace připojené ke službě Active Directory, jako jsou třeba místní souborové servery.

Vzhledem k tomu, že mnoho organizací má historicky nasazené zřizování na základě lidských zdrojů, už může mít identity uživatelů pro všechny své zaměstnance ve službě Active Directory.   Nejběžnějším scénářem pro zřizování mezi adresáři je, že uživatel, který je už ve službě Active Directory, se zřídí do služby Azure AD.  Toto zřizování se obvykle provádí Azure AD Connect synchronizace nebo Azure AD Connect zřizování cloudu. 

Kromě toho organizace můžou chtít zřídit také místní systémy z Azure AD.  Organizace může například uvést hosty do adresáře služby Azure AD, ale tyto hosty budou potřebovat přístup k místním webovým aplikacím založeným na službě WIA (Windows Integrated Authentication) prostřednictvím proxy aplikací.  To vyžaduje zřizování místních účtů služby AD pro tyto uživatele ve službě Azure AD.

Další informace najdete v tématu [co je mezi adresářovým zřizováním?](what-is-inter-directory-provisioning.md)

 
## <a name="next-steps"></a>Další kroky 
- [Co je správa životního cyklu identit?](what-is-identity-lifecycle-management.md)
- [Co je zřizování na základě lidských zdrojů?](what-is-hr-driven-provisioning.md)
- [Co je zřizování aplikací?](what-is-app-provisioning.md)
- [Co je zřizování mezi adresářovými systémy?](what-is-inter-directory-provisioning.md)