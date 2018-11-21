---
title: Řešení potíží s zobrazení nákladů Azure enterprise | Dokumentace Microsoftu
description: Přečtěte si o řešení problémů, na které bude pravděpodobně se zobrazeními náklady organizace na webu Azure portal.
author: rthorn17
manager: adpick
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: cwatson
ms.openlocfilehash: be74714667c2fed57a797a972ddcd7949cae5933
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274666"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Řešení potíží s firemních nákladů zobrazení 

V rámci podnikové registrace existuje několik nastavení, která by mohla způsobit, že uživatelé v rámci registrace nebudou moct zobrazit náklady.  Tato nastavení se spravují pomocí Správce registrace nebo partnerem, pokud registraci není zakoupili přímo s Microsoftem.  Tento článek vám pomůže pochopit nastavení jsou a jak by mohly mít dopad registraci. Tato nastavení jsou nezávislé [role Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 


## <a name="enabling-access-to-costs"></a>Povolení přístupu k náklady

Jste si zobrazovat zpráva Neautorizováno, nebo *"zobrazení nákladů jsou zakázaná v registraci."* Pokud hledáte informace o nákladech? ![neoprávněným](media/billing-enterprise-mgmt-groups/unauthorized.png)

Může to být způsobené jedním z následujících důvodů:

1. Azure zakoupené prostřednictvím partnera enterprise a partnera nevydala ještě ceny. Pokud chcete uvolnit, cenách, obraťte se na partnera se aktualizovat nastavení v rámci [Enterprise portal](https://ea.azure.com).
2. Případně pokud jste zákazník se smlouvou EA Direct, máte několik možností:
    * Jste vlastníkem účtu a správce registrace zakázal "AO zobrazení poplatky" nastavení.  
    * Jste správcem oddělení a registraci správce zakázal "DA zobrazení poplatky" nastavení.
    * Obraťte se na správce registrace získat přístup. Správce registrace najdete [Enterprise portal](https://ea.azure.com/manage/enrollment) a aktualizujte nastavení, jak je vidět tady:

![Nastavení portálu Enterprise](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>Prostředek není k dispozici? 
Pokud se vám zobrazuje chybová zpráva "Tento prostředek není k dispozici" při pokusu o přístup k předplatné nebo skupinu pro správu a pak nemají správnou roli zobrazíte tuto položku.  

![prostředek nebyla nalezena](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Obraťte se na správce předplatného nebo správu skupin má být poskytnut přístup.  
* Pro předplatná, referenční [Azure Role-Based řízení přístupu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) dokumentu nápovědy, na kterém je potřeba role.
