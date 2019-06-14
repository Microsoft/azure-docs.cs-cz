---
title: Co jsou závislosti služby podmíněného přístupu Azure Active Directory? | Dokumenty Microsoft
description: Zjistěte, jak se používají podmínky podmíněného přístupu Azure Active Directory k aktivaci zásad.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f727fc7133ebc9ee124e63253e8a266862b0d908
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60354349"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Co jsou závislosti služby podmíněného přístupu Azure Active Directory? 


Díky zásadám podmíněného přístupu můžete určit požadavky na přístup k webům a službám. Požadavků na přístup může obsahovat třeba vyžadování vícefaktorového ověřování (MFA) nebo [spravovaných zařízeních](require-managed-devices.md). 


Při přístupu k webu nebo služby přímo, je obvykle snadno posoudit dopad související zásady. Například pokud máte zásadu, která vyžaduje vícefaktorové ověřování pro SharePoint Online nakonfigurované vícefaktorové ověřování se vynucuje pro každé přihlášení na webovém portálu služby SharePoint. Však není vždy přímočaré k vyhodnocení dopadu zásady, protože existují cloudové aplikace se závislostmi s jinými cloudovými aplikacemi. Například Microsoft Teams využívá Sharepointu online. Proto při přístupu k Microsoft Teams v tomto scénáři aktuální, nesete také závisí to na zásadách vícefaktorové ověřování služby SharePoint.   


## <a name="policy-enforcement"></a>Vynucování zásad 

Pokud máte nakonfigurovaný závislosti služby mohou zásady použijí pomocí časné vazby nebo s pozdní vazbou. 

**Vynucení zásad časné vazby** znamená, že uživatel musí splňovat zásady závislých služeb před přístupem k volání aplikace. Například uživatel musí splňovat zásady pro SharePoint před přihlášením MS Teams. 

**Vynucení zásad s pozdní vazbou** dojde poté, co uživatel přihlašuje k volající aplikaci. Vynucení je odložena na při volání metody žádostí aplikace, token pro příjem dat služby. Mezi příklady patří MS Teams přístup k plánování a Office.com přístupu Sharepointu. 

Následující diagram znázorňuje závislostí služby MS Teams. Plná šipky označují vynucení časné vazby na přerušovanou šipku pro Planner označuje vynucení s pozdní vazbou. 



![MS Teams závislostí služby](./media/service-dependencies/01.png)



  

Jako osvědčený postup byste měli nastavit běžné zásady mezi souvisejícími aplikacemi a službami, kdykoli je to možné. S stavu konzistentní zabezpečení vám poskytne nejlepší uživatelské prostředí. Například běžné zásady nastavení v Exchangi Online, SharePoint Online, MS Teams a Skype pro firmy významně snižuje neočekávané výzvy, které mohou vzniknout při použití navazujících službách různých zásad. 

Následující tabulka uvádí závislosti další služby, ve kterém musí splňovat klientské aplikace  

| Klientské aplikace.         | Příjem dat služby                          | Vynucení |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portál a rozhraní API) | Časné vazby |
| Microsoft Classroom | Exchange                                    | Časné vazby |
|                     | SharePoint                                  | Časné vazby  |
| Microsoft Teams     | Exchange                                    | Časné vazby |
|                     | MS Planner                                  | S pozdní vazbou  |
|                     | SharePoint                                  | Časné vazby |
|                     | Online Skype pro firmy                   | Časné vazby |
| Portál Office       | Exchange                                    | S pozdní vazbou  |
|                     | SharePoint                                  | S pozdní vazbou  |
| Aplikace Outlook skupiny      | Exchange                                    | Časné vazby |
|                     | SharePoint                                  | Časné vazby |
| PowerApps           | Microsoft Azure Management (portál a rozhraní API) | Časné vazby |
|                     | Windows Azure Active Directory              | Časné vazby |
| Projekt             | Dynamics CRM                                | Časné vazby |
| Skype pro firmy  | Exchange                                    | Časné vazby |
| Visual Studio       | Microsoft Azure Management (portál a rozhraní API) | Časné vazby |



## <a name="next-steps"></a>Další postup

Zjistěte, jak implementovat podmíněný přístup ve vašem prostředí, najdete v článku [plánování nasazení podmíněného přístupu v Azure Active Directory](plan-conditional-access.md).
