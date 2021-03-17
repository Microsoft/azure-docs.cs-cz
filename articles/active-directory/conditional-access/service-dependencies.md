---
title: Závislosti služby podmíněného přístupu – Azure Active Directory
description: Přečtěte si, jak se v Azure Active Directory podmíněný přístup používají podmínky pro aktivaci zásad.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6592add5e33ba240c0f1d9fdbd23d82adfe5229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258604"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Jaké jsou závislosti služby v Azure Active Directory podmíněný přístup? 

Pomocí zásad podmíněného přístupu můžete zadat požadavky na přístup k webům a službám. Vaše požadavky na přístup například můžou zahrnovat vyžadování vícefaktorového ověřování (MFA) nebo [spravovaných zařízení](require-managed-devices.md). 

Když přímo přistupujete k webu nebo službě, je možné, že dopad souvisejících zásad se obvykle snadno vyhodnocuje. Například pokud máte zásadu, která vyžaduje službu Multi-Factor Authentication (MFA) pro SharePoint Online, vynutí se pro každé přihlášení k webovému portálu služby SharePoint MFA. Není však vždy přímo předáván k vyhodnocení dopadu zásady, protože existují cloudové aplikace se závislostmi na jiné cloudové aplikace. Týmy Microsoft můžou například poskytnout přístup k prostředkům v SharePointu Online. Takže při přístupu k Microsoft Teams v našem scénáři se vztahují i na zásady ověřování serveru SharePoint. 

> [!TIP]
> Použití aplikace [office 365](concept-conditional-access-cloud-apps.md#office-365) bude cílit na všechny aplikace Office, aby se předešlo problémům se závislostmi služeb v sadě Office.

## <a name="policy-enforcement"></a>Vynucování zásad 

Pokud máte nakonfigurovanou závislost služby, dá se zásada použít s vynuceným vynuceným nebo pozdním vynucováním. 

- **Vynucení zásad s časnou vazbou** znamená, že uživatel musí před přístupem k volající aplikaci splňovat závislé zásady služby. Uživatel musí například před přihlášením do MS Teams splňovat zásady služby SharePoint. 
- K **vynucení zásad s pozdní vazbou** dojde poté, co se uživatel přihlásí do volající aplikace. Vynucení se odvozuje při volání požadavků aplikace, tokenu pro službu pro příjem dat. Mezi příklady patří MS Teams přistupující k Planneru a Office.com přístupu k SharePointu. 

Následující diagram znázorňuje závislosti služby MS Teams. Plné šipky označují vynucení s časnou vazbou, která je čárkovanou šipkou pro Planner, indikuje vynucené vynuceně vázané. 

![Závislosti služby MS Teams](./media/service-dependencies/01.png)

V souladu s osvědčenými postupy byste měli v případě potřeby nastavit běžné zásady napříč souvisejícími aplikacemi a službami. Konzistentní stav zabezpečení poskytuje nejlepší uživatelské prostředí. Například nastavení společné zásady napříč Exchangem Online, SharePointem Online, Microsoft teams a Skype pro firmy výrazně zkracuje neočekávané výzvy, které mohou vzniknout z různých zásad, které se vztahují na služby pro příjem dat. 

Skvělým způsobem, jak toho dosáhnout pomocí aplikací v sadě Office Stack, je použít [aplikaci Office 365](concept-conditional-access-cloud-apps.md#office-365) místo cílení na jednotlivé aplikace.

Následující tabulka uvádí další závislosti služby, ve kterých musí klientské aplikace splňovat požadavky.  

| Klientské aplikace         | Služba pro příjem dat                          | Výkonem |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Správa Microsoft Azure (portál a rozhraní API) | S časnou vazbou |
| Učebna Microsoftu | Výměna                                    | S časnou vazbou |
|                     | SharePoint                                  | S časnou vazbou |
| Microsoft Teams     | Výměna                                    | S časnou vazbou |
|                     | MS Planner                                  | S pozdní vazbou  |
|                     | Microsoft Stream                            | S pozdní vazbou  |
|                     | SharePoint                                  | S časnou vazbou |
|                     | Online Skype pro firmy                   | S časnou vazbou |
| Portál Office       | Výměna                                    | S pozdní vazbou  |
|                     | SharePoint                                  | S pozdní vazbou  |
| Skupiny Outlooku      | Výměna                                    | S časnou vazbou |
|                     | SharePoint                                  | S časnou vazbou |
| PowerApps           | Správa Microsoft Azure (portál a rozhraní API) | S časnou vazbou |
|                     | Windows Azure Active Directory              | S časnou vazbou |
| Project             | Dynamics CRM                                | S časnou vazbou |
| Skype pro firmy  | Výměna                                    | S časnou vazbou |
| Visual Studio       | Správa Microsoft Azure (portál a rozhraní API) | S časnou vazbou |
| Microsoft Forms     | Výměna                                    | S časnou vazbou |
|                     | SharePoint                                  | S časnou vazbou |
| Microsoft To-Do     | Výměna                                    | S časnou vazbou |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak implementovat podmíněný přístup ve vašem prostředí, najdete v tématu [Plánování nasazení podmíněného přístupu v Azure Active Directory](plan-conditional-access.md).
