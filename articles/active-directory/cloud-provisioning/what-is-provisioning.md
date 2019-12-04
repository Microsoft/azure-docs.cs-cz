---
title: Co je zřizování identit pomocí Azure AD? | Microsoft Docs
description: Popisuje přehled zřizování identit.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acde7f30649e03d44fd891c959b53113b0e46f51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793511"
---
# <a name="what-is-identity-provisioning"></a>Co je zřizování identit?

V současné době se podniky a společnosti stávají více a více než v různých místních i cloudových aplikacích.  Uživatelé vyžadují přístup k aplikacím místně i v cloudu. V rámci těchto různých aplikací je potřeba mít jedinou identitu (místní i Cloud).

Zřizování je proces vytváření objektu na základě určitých podmínek, zachování objektu až data a odstranění objektu, pokud podmínky již nejsou splněné. Když se například do vaší organizace připojí nový uživatel, tento uživatel se zadává do systému HR.  V tomto okamžiku zřizování může vytvořit odpovídající uživatelský účet v cloudu, ve službě Active Directory a v různých aplikacích, ke kterým uživatel potřebuje mít přístup.  Díky tomu může uživatel začít pracovat a mít přístup k aplikacím a systémům, které potřebují k jednomu dni. 

![zřizování cloudu](media/what-is-provisioning/cloud1.png)

Pokud jde o Azure Active Directory, zřizování se dá rozdělit do následujících klíčových scénářů.  

- **[Zřizování na základě lidských zdrojů](#hr-driven-provisioning)**  
- **[Zřizování aplikací](#app-provisioning)**  
- **[Zřizování adresáře](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Zřizování na základě lidských zdrojů

![zřizování cloudu](media/what-is-provisioning/cloud2.png)

Zřizování z HR do cloudu zahrnuje vytváření objektů (uživatelů, rolí, skupin atd.) na základě informací, které jsou v systému HR.  

Nejběžnějším scénářem je, že když se do vaší společnosti připojí nový zaměstnanec, vstoupí do systému HR.  Jakmile k tomu dojde, budou zřízeny do cloudu.  V tomto případě Azure AD.  Zřizování z HR může pokrývat následující scénáře. 

- Připravují se **noví zaměstnanci** – když se do cloudového HR přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory, Azure Active Directory a volitelně na Office 365 a další aplikace SaaS podporované službou Azure AD, a to s zpětným zápisem e-mailové adresy do cloudového hr.
- **Aktualizace atributů a profilů zaměstnanců** – když se v cloudovém HR aktualizuje záznam zaměstnance (například jeho jméno, název nebo manažer), automaticky se aktualizuje jeho uživatelský účet ve službě Active Directory, Azure Active Directory a volitelně v Office 365 a dalších aplikacích SaaS podporovaných službou Azure AD.
- **Ukončení zaměstnanců** – když se zaměstnanec v cloudovém HR ukončí, je jejich uživatelský účet automaticky zakázaný ve službě Active Directory, Azure Active Directory a volitelně Office 365 a další aplikace SaaS podporované službou Azure AD.
- **Pracovní zařazení zaměstnanců** – Pokud se zaměstnanec znovu přiřadí do CLOUDového hru, jeho starý účet se dá automaticky znovu aktivovat nebo znovu zřídit (v závislosti na vaší preferenci) se službou Active Directory, Azure Active Directory a volitelně Office 365 a dalšími aplikacemi SaaS, které Azure AD podporuje.


## <a name="app-provisioning"></a>Zřizování aplikací

![zřizování cloudu](media/what-is-provisioning/cloud3.png)

Zřizování aplikací zahrnuje zřizování uživatelů a rolí v aplikacích, ke kterým uživatel potřebuje mít přístup.  

Nejběžnějším scénářem je, že když se uživatel ve službě Azure AD zřídí do O365 nebo Salesforce.

## <a name="directory-provisioning"></a>Zřizování adresáře

![zřizování cloudu](media/what-is-provisioning/cloud4.png)

Místní zřizování zahrnuje zřizování z místních zdrojů (jako je služba Active Directory) do Azure AD.  

Nejběžnějším scénářem je situace, kdy se uživatel ve službě Active Directory (AD) zřídí do služby Azure AD.

To bylo dosaženo Azure AD Connect synchronizace Azure AD Connect zřizování cloudu a Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
