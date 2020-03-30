---
title: Co je zřizování identity s Azure AD? | Dokumentace Microsoftu
description: Popisuje přehled zřizování identity.
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
ms.openlocfilehash: 28513c57101af67695d10056b3dc8e6537dcddb2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76712550"
---
# <a name="what-is-identity-provisioning"></a>Co je zřizování identit?

Dnes se firmy a korporace stávají stále více směsicí místních a cloudových aplikací.  Uživatelé vyžadují přístup k aplikacím v místním i v cloudu. Je potřeba mít jednu identitu v rámci těchto různých aplikací (místní i cloud).

Zřizování je proces vytváření objektu na základě určitých podmínek, udržování objektu aktuální a odstranění objektu, pokud podmínky již nejsou splněny. Například když se nový uživatel připojí k vaší organizaci, je tento uživatel zadán do systému lidských zdrojů.  V tomto okamžiku zřizování můžete vytvořit odpovídající uživatelský účet v cloudu, ve službě Active Directory a různé aplikace, které uživatel potřebuje přístup.  To umožňuje uživateli začít pracovat a mít přístup k aplikacím a systémům, které potřebují v první den. 

![zřizování cloudu](media/what-is-provisioning/cloud1.png)

Pokud jde o Azure Active Directory zřizování lze rozdělit do následujících klíčových scénářů.  

- **[Zřizování založené na lidských;](#hr-driven-provisioning)**  
- **[Zřizování aplikací](#app-provisioning)**  
- **[Zřizování adresáře](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Zřizování založené na lidských;

![zřizování cloudu](media/what-is-provisioning/cloud2.png)

Zřizování z HR do cloudu zahrnuje vytváření objektů (uživatelů, rolí, skupin atd.) na základě informací, které jsou ve vašem HR systému.  

Nejběžnějším scénářem by bylo, že když se nový zaměstnanec připojí k vaší společnosti, je zapsán do hr systému.  Jakmile k tomu dojde, jsou zřízeny do cloudu.  V tomto případě Azure AD.  Zřizování z HR může zahrnovat následující scénáře. 

- **Nábor nových zaměstnanců** – když se do cloudového hr přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory, Azure Active Directory a volitelně office 365 a dalších aplikacích SaaS podporovaných službou Azure AD s zpětným zápisem e-mailové adresy do cloudového hr.
- **Aktualizace atributů a profilů zaměstnanců** – když se záznam zaměstnance aktualizuje v cloudu HR (například jejich jméno, titul nebo správce), jejich uživatelský účet se automaticky aktualizuje ve službě Active Directory, službě Azure Active Directory a volitelně Office 365 a dalších aplikacích SaaS podporovaných službou Azure AD.
- **Ukončení zaměstnanců** – když je zaměstnanec ukončen v cloudu HR, jeho uživatelský účet se automaticky deaktivuje ve službě Active Directory, Azure Active Directory a volitelně Office 365 a dalších aplikacích SaaS podporovaných službou Azure AD.
- Opětovné přijetí zaměstnanců – když je zaměstnanec znovu přijat v cloudu HR, jeho starý účet může být automaticky znovu aktivován nebo znovu zřízen (v závislosti na vašich preferencích) do **služby** Active Directory, služby Azure Active Directory a volitelně Office 365 a dalších aplikací SaaS podporovaných službou Azure AD.


## <a name="app-provisioning"></a>Zřizování aplikací

![zřizování cloudu](media/what-is-provisioning/cloud3.png)

Ve službě Azure Active Directory (Azure AD) termín **[zřizování aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** odkazuje na automatické vytváření identit uživatelů a rolí v cloudových aplikacích, ke kterým uživatelé potřebují přístup. Kromě vytváření identit uživatelů zahrnuje automatické zřizování údržbu a odebrání identit uživatelů při změně stavu nebo rolí. Běžné scénáře zahrnují zřizování uživatele Azure AD do aplikací, jako [je Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)a další.

## <a name="directory-provisioning"></a>Zřizování adresáře

![zřizování cloudu](media/what-is-provisioning/cloud4.png)

Místní zřizování zahrnuje zřizování z místních zdrojů (například služby Active Directory) do služby Azure AD.  

Nejběžnější scénář by bylo, když uživatel ve službě Active Directory (AD) je zřízena do Služby Azure AD.

Toho bylo dosaženo synchronizací Azure AD Connect, zřizováním cloudu Azure AD Connect a Microsoft Identity Managerem. 
 
## <a name="next-steps"></a>Další kroky 

- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
- [Instalace zřizování cloudu](how-to-install.md)
