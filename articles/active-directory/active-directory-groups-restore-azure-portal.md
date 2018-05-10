---
title: Obnovit odstraněné skupiny Office 365 ve službě Azure Active Directory | Microsoft Docs
description: Jak obnovit odstraněné skupině, zobrazit obnovitelné skupiny a permamnently odstranit skupinu v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 388c5617a040da396cb0c6a05b5697fb5fd22248
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Obnovit odstraněné skupiny Office 365 ve službě Azure Active Directory

Pokud odstraníte skupinu služeb Office 365 ve službě Azure Active Directory (Azure AD), z odstraněné skupiny je zachované, ale nejsou viditelné po dobu 30 dnů od data odstranění. Toto je tak, aby skupiny a její obsah se dají obnovovat v případě potřeby. Tato funkce je omezen výhradně na skupiny Office 365 ve službě Azure AD. Není k dispozici pro skupiny zabezpečení a distribučních skupin.

> [!NOTE] 
> Nepoužívejte `Remove-MsolGroup` protože skupině trvale odstraní. Vždy používat `Remove-AzureADMSGroup` odstranění skupiny O365. 

Oprávnění potřebná k obnovení skupiny může být jedno z následujících:

Role  | Oprávnění 
--------- | ---------
Správce společnosti, partnera Tier2 podpory a správci služby InTune | Můžete obnovit všechny odstraněné skupiny Office 365 
Podpora uživatelského účtu správce a Tier1 partnera | Můžete obnovit všechny odstraněné skupiny Office 365 s výjimkou jsou přiřazena k roli správce společnosti 
Uživatel | Můžete obnovit všechny odstraněné skupiny Office 365, který se ve vlastnictví 


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>Zobrazit odstraněné skupiny Office 365, které jsou k dispozici pro obnovení
Chcete-li zobrazit tyto odstraněné skupiny ověřte, že jeden nebo ty, které vás zajímají nebyly dosud vyprázdnila trvale slouží následující rutiny. Tyto rutiny jsou součástí [modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/). Další informace o tomto modulu najdete v [Azure Active Directory PowerShell verze 2](/powershell/azure/install-adv2?view=azureadps-2.0) článku.

1.  Spusťte následující rutinu pro zobrazení všech odstranit skupiny Office 365 ve vašem klientovi, které jsou stále k dispozici pro obnovení.
  ```
  Get-AzureADMSDeletedGroup
  ```

2.  Případně pokud víte objectID určité skupiny (a můžete ho získat prostřednictvím rutinu v kroku 1), spusťte následující rutinu k ověření, že konkrétní odstraněné skupiny nebyl ještě vyprázdnila trvale.
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-your-deleted-office-365-group"></a>Jak obnovit odstraněné skupině Office 365
Jakmile si ověříte, zda je skupina stále k dispozici pro obnovení, obnovení odstraněné skupiny s jedním z následujících kroků. Pokud tato skupina obsahuje dokumenty, SP lokalit nebo jiné trvalé objekty, může trvat až 24 hodin plně obnovit skupinu a její obsah.

1.  Spusťte následující rutinu k obnovení skupiny a její obsah.
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

Následující rutiny můžete spustit taky, trvale odeberete z odstraněné skupiny.
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>Jak to šlo znáte?
Chcete-li ověřit, že jste úspěšně obnovit skupinu služeb Office 365, spusťte `Get-AzureADGroup –ObjectId <objectId>` rutiny zobrazíte informace o skupině. Po obnovení žádosti o dokončení:
- Skupiny se zobrazí v levém navigačního panelu na Exchange
- Plán pro skupinu se objeví v plánovače
- Všechny weby služby Sharepoint a veškerý jeho obsah bude k dispozici
- Skupiny je přístupná ze všech koncových bodů Exchange a další úlohy Office 365, které podporují skupiny Office 365


## <a name="next-steps"></a>Další postup
Tyto články poskytují další informace o skupinách Azure Active Directory.

* [Zobrazení existujících skupin](active-directory-groups-view-azure-portal.md)
* [Spravovat nastavení skupiny](active-directory-groups-settings-azure-portal.md)
* [Správa členů skupiny](active-directory-groups-members-azure-portal.md)
* [Správa členství ve skupině](active-directory-groups-membership-azure-portal.md)
* [Dynamická pravidla pro uživatele ve skupině pro správu](active-directory-groups-dynamic-membership-azure-portal.md)
