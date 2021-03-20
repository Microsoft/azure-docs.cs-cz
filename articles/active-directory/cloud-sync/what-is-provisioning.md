---
title: Co je zřizování identit pomocí Azure AD? | Dokumentace Microsoftu
description: Popisuje přehled zřizování identit.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c85f2a6d58a5dbeae93b951cea812d6aa91326
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614815"
---
# <a name="what-is-identity-provisioning"></a>Co je zřizování identit?

V současné době se podniky a společnosti stávají více a více než v různých místních i cloudových aplikacích.  Uživatelé vyžadují přístup k aplikacím místně i v cloudu. V rámci těchto různých aplikací je potřeba mít jedinou identitu (místní i Cloud).

Zřizování je proces vytváření objektu na základě určitých podmínek, udržování objektu v aktuálním stavu a odstranění objektu, pokud podmínky již nejsou splněny. Když se například do vaší organizace připojí nový uživatel, tento uživatel se zadává do systému HR.  V tomto okamžiku zřizování může vytvořit odpovídající uživatelský účet v cloudu, ve službě Active Directory a v různých aplikacích, ke kterým uživatel potřebuje mít přístup.  Díky tomu může uživatel začít pracovat a mít přístup k aplikacím a systémům, které potřebují k jednomu dni. 

![Diagram, který zobrazuje zřizování cloudu pomocí Azure Active Directory.](media/what-is-provisioning/cloud-1.png)

Pokud jde o Azure Active Directory, zřizování se dá rozdělit do následujících klíčových scénářů.  

- **[Zřizování na základě lidských zdrojů](#hr-driven-provisioning)**  
- **[Zřizování aplikací](#app-provisioning)**  
- **[Zřizování adresáře](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Zřizování na základě lidských zdrojů

![Diagram znázorňující zřizování na základě lidských zdrojů s využitím cloudového HR, místního HR a Azure Active Directory.](media/what-is-provisioning/cloud-2.png)

Zřizování z HR do cloudu zahrnuje vytváření objektů (uživatelů, rolí, skupin atd.) na základě informací, které jsou v systému HR.  

Nejběžnějším scénářem je, že když se do vaší společnosti připojí nový zaměstnanec, vstoupí do systému HR.  Jakmile k tomu dojde, budou zřízeny do cloudu.  V tomto případě Azure AD.  Zřizování z HR může pokrývat následující scénáře. 

- Připravují se **noví zaměstnanci** – když se do cloudového HR přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory, Azure Active Directory a volitelně Microsoft 365 a jiné aplikace SaaS podporované službou Azure AD, a to s zpětným zápisem e-mailové adresy do cloudového hr.
- **Aktualizace atributů a profilů zaměstnanců** – když se v cloudovém HR aktualizuje záznam zaměstnance (například jeho jméno, název nebo manažer), automaticky se aktualizuje jeho uživatelský účet ve službě Active Directory, Azure Active Directory a volitelně Microsoft 365 a dalších SaaS aplikací podporovaných službou Azure AD.
- **Ukončení zaměstnanců** – když se zaměstnanec v cloudovém HR ukončí, je jejich uživatelský účet automaticky zakázaný ve službě Active Directory, Azure Active Directory a volitelně Office 365 a další aplikace SaaS podporované službou Azure AD.
- **Pracovní zařazení zaměstnanců** – Pokud se zaměstnanec znovu přiřadí do CLOUDového hru, jeho starý účet se dá automaticky znovu aktivovat nebo znovu zřídit (v závislosti na vaší preferenci) se službou Active Directory, Azure Active Directory a volitelně Microsoft 365 a další aplikace SaaS podporované službou Azure AD.


## <a name="app-provisioning"></a>Zřizování aplikací

![Diagram, který znázorňuje zřizování aplikací pomocí místních aplikací, cloudových aplikací od jiných společností než Microsoftu a Azure Active Directory.](media/what-is-provisioning/cloud-3.png)

V Azure Active Directory (Azure AD) pojem **[zřizování aplikací](../app-provisioning/user-provisioning.md)** označuje automatické vytváření identit uživatelů a rolí v cloudových aplikacích, ke kterým uživatelé potřebují přístup. Kromě vytváření identit uživatelů zahrnuje Automatické zřizování také údržbu a odebírání identit uživatelů při změně stavu nebo rolí. Mezi běžné scénáře patří zřizování uživatelů Azure AD v aplikacích, jako jsou [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)a další.

## <a name="directory-provisioning"></a>Zřizování adresáře

![zřizování cloudu](media/what-is-provisioning/cloud-4.png)

Místní zřizování zahrnuje zřizování z místních zdrojů (jako je služba Active Directory) do Azure AD.  

Nejběžnějším scénářem je situace, kdy se uživatel ve službě Active Directory (AD) zřídí do služby Azure AD.

To bylo dosaženo Azure AD Connect synchronizace Azure AD Connect zřizování cloudu a Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Další kroky 

- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)
- [Instalace zřizování cloudu](how-to-install.md)