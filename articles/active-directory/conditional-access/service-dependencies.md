---
title: Jaké jsou závislosti služby v Azure Active Directory podmíněný přístup? | Dokumenty Microsoft
description: Přečtěte si, jak se v Azure Active Directory podmíněný přístup používají podmínky pro aktivaci zásad.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c7f2abda282d0219dd8787a9f6a2b6c1cda15df
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257917"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Jaké jsou závislosti služby v Azure Active Directory podmíněný přístup? 

Pomocí zásad podmíněného přístupu můžete zadat požadavky na přístup k webům a službám. Vaše požadavky na přístup například můžou zahrnovat vyžadování vícefaktorového ověřování (MFA) nebo [spravovaných zařízení](require-managed-devices.md). 

Když přímo přistupujete k webu nebo službě, je možné, že dopad souvisejících zásad se obvykle snadno vyhodnocuje. Například pokud máte zásadu, která vyžaduje vícefaktorové ověřování pro SharePoint Online, pro každé přihlášení k webovému portálu služby SharePoint se vynutí MFA. Není však vždy přímo předáván k vyhodnocení dopadu zásady, protože existují cloudové aplikace se závislostmi na jiné cloudové aplikace. Týmy Microsoft můžou například poskytnout přístup k prostředkům v SharePointu Online. Takže při přístupu k Microsoft Teams v našem scénáři se vztahují i na zásady ověřování serveru SharePoint.   

## <a name="policy-enforcement"></a>Vynucování zásad 

Pokud máte nakonfigurovanou závislost služby, dá se zásada použít s vynuceným vynuceným nebo pozdním vynucováním. 

- **Vynucení zásad s časnou vazbou** znamená, že uživatel musí před přístupem k volající aplikaci splňovat závislé zásady služby. Uživatel musí například před přihlášením do MS Teams splňovat zásady služby SharePoint. 
- K **vynucení zásad s pozdní vazbou** dojde poté, co se uživatel přihlásí do volající aplikace. Vynucení se odvozuje při volání požadavků aplikace, tokenu pro službu pro příjem dat. Mezi příklady patří MS Teams přistupující k Planneru a Office.com přístupu k SharePointu. 

Následující diagram znázorňuje závislosti služby MS Teams. Plné šipky označují vynucení s časnou vazbou, která je čárkovanou šipkou pro Planner, indikuje vynucené vynuceně vázané. 

![Závislosti služby MS Teams](./media/service-dependencies/01.png)

V souladu s osvědčenými postupy byste měli v případě potřeby nastavit běžné zásady napříč souvisejícími aplikacemi a službami. Konzistentní stav zabezpečení poskytuje nejlepší uživatelské prostředí. Například nastavení společné zásady napříč Exchangem Online, SharePointem Online, Microsoft teams a Skype pro firmy výrazně zkracuje neočekávané výzvy, které mohou vzniknout z různých zásad, které se vztahují na služby pro příjem dat. 

Následující tabulka uvádí další závislosti služby, ve kterých musí klientské aplikace splňovat požadavky.  

| Klientské aplikace         | Služba pro příjem dat                          | Výkonem |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Správa Microsoft Azure (portál a rozhraní API) | S časnou vazbou |
| Učebna Microsoftu | Exchange                                    | S časnou vazbou |
|                     | SharePoint                                  | S časnou vazbou |
| Microsoft Teams     | Exchange                                    | S časnou vazbou |
|                     | MS Planner                                  | S pozdní vazbou  |
|                     | SharePoint                                  | S časnou vazbou |
|                     | Online Skype pro firmy                   | S časnou vazbou |
| Portál Office       | Exchange                                    | S pozdní vazbou  |
|                     | SharePoint                                  | S pozdní vazbou  |
| Skupiny Outlooku      | Exchange                                    | S časnou vazbou |
|                     | SharePoint                                  | S časnou vazbou |
| PowerApps           | Správa Microsoft Azure (portál a rozhraní API) | S časnou vazbou |
|                     | Azure Active Directory Windows              | S časnou vazbou |
| Projekt             | Dynamics CRM                                | S časnou vazbou |
| Skype pro firmy  | Exchange                                    | S časnou vazbou |
| Visual Studio       | Správa Microsoft Azure (portál a rozhraní API) | S časnou vazbou |
| Microsoft Forms     | Exchange                                    | S časnou vazbou |
|                     | SharePoint                                  | S časnou vazbou |
| Microsoft To-Do     | Exchange                                    | S časnou vazbou |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak implementovat podmíněný přístup ve vašem prostředí, najdete v tématu [Plánování nasazení podmíněného přístupu v Azure Active Directory](plan-conditional-access.md).
