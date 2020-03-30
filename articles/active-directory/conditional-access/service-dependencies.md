---
title: Závislosti služby podmíněného přístupu – Služba Azure Active Directory
description: Zjistěte, jak se podmínky používají v podmíněném přístupu Služby Azure Active Directory k aktivaci zásad.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39238575c05d35a2d87999e08c49c0c77e99bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74380014"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Co jsou závislosti služeb v podmíněném přístupu služby Azure Active Directory? 

Pomocí zásad podmíněného přístupu můžete určit požadavky na přístup k webům a službám. Vaše požadavky na přístup mohou například zahrnovat vyžadování vícefaktorového ověřování (MFA) nebo [spravovaných zařízení](require-managed-devices.md). 

Při přímém přístupu k webu nebo službě je obvykle snadné posoudit dopad souvisejících zásad. Pokud máte například zásadu, která vyžaduje nakonfigurovanou vícefaktorovou a finanční autoritu pro SharePoint Online, je vícefaktorové informace vynuceno pro každé přihlášení k webovému portálu SharePointu. Však není vždy přímočaré posoudit dopad zásady, protože existují cloudové aplikace se závislostmi na jiných cloudových aplikacích. Microsoft Teams mohou například poskytovat přístup k prostředkům v SharePointu Online. Takže při přístupu k Microsoft Teams v našem aktuálním scénáři se na vás také vztahují zásady SharePoint MFA.   

## <a name="policy-enforcement"></a>Vynucování zásad 

Pokud máte nakonfigurovanou závislost služby, může být zásada použita pomocí vynucení s časnou vazbou nebo pozdní vazbou. 

- **Vynucení zásad včasného připojení** znamená, že uživatel musí před přístupem k volající aplikaci splňovat zásady závislé služby. Uživatel musí například splňovat zásady služby SharePoint před přihlášením do Týmu MS. 
- **Opožděné vynucení zásad** nastane poté, co se uživatel přihlásí do volající aplikace. Vynucení je odloženo na při volání žádostí o aplikaci, token pro službu příjem dat. Mezi příklady patří MS Teams přístup k Planner a Office.com přístup k SharePointu. 

Následující diagram znázorňuje závislosti služeb MS Teams. Plné šipky označují včasné vynucení přerušované šipky pro Planner označuje vynucení s pozdní vazbou. 

![Závislosti služeb MS Teams](./media/service-dependencies/01.png)

Jako osvědčený postup byste měli nastavit společné zásady napříč souvisejícími aplikacemi a službami, kdykoli je to možné. Konzistentní stav zabezpečení poskytuje nejlepší uživatelské prostředí. Například nastavení společných zásad napříč Exchangeonline, SharePointonline, Microsoft Teams a Skype pro firmy výrazně snižuje neočekávané výzvy, které mohou vzniknout z různých zásad používaných pro podřízené služby. 

V následující tabulce jsou uvedeny další závislosti služeb, kde musí klientské aplikace splňovat  

| Klientské aplikace         | Následná služba                          | Výkonu |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portál a rozhraní API) | Brzy vázaná |
| Učebna Microsoftu | Výměna                                    | Brzy vázaná |
|                     | SharePoint                                  | Brzy vázaná |
| Microsoft Teams     | Výměna                                    | Brzy vázaná |
|                     | MS Plánovač                                  | Pozdní vazba  |
|                     | SharePoint                                  | Brzy vázaná |
|                     | Online Skype pro firmy                   | Brzy vázaná |
| Portál Office       | Výměna                                    | Pozdní vazba  |
|                     | SharePoint                                  | Pozdní vazba  |
| Skupiny aplikace Outlook      | Výměna                                    | Brzy vázaná |
|                     | SharePoint                                  | Brzy vázaná |
| PowerApps           | Microsoft Azure Management (portál a rozhraní API) | Brzy vázaná |
|                     | Windows Azure Active Directory              | Brzy vázaná |
| Project             | Dynamics CRM                                | Brzy vázaná |
| Skype pro firmy  | Výměna                                    | Brzy vázaná |
| Visual Studio       | Microsoft Azure Management (portál a rozhraní API) | Brzy vázaná |
| Microsoft Forms     | Výměna                                    | Brzy vázaná |
|                     | SharePoint                                  | Brzy vázaná |
| Microsoft To-Do     | Výměna                                    | Brzy vázaná |

## <a name="next-steps"></a>Další kroky

Informace o implementaci podmíněného přístupu ve vašem prostředí najdete v [tématu Plánování nasazení podmíněného přístupu ve službě Azure Active Directory](plan-conditional-access.md).
