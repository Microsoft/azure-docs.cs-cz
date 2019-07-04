---
title: Řešení potíží s Azure enterprise náklady na zobrazení
description: Přečtěte si o řešení problémů, na které bude pravděpodobně se zobrazeními náklady organizace na webu Azure portal.
author: bandersmsft
manager: amberb
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 83f7f424b265582a3830c02973cbbb9962ddfbfb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491262"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Řešení potíží s firemních nákladů zobrazení

V rámci podnikové registrace existuje několik nastavení, které by mohly způsobit uživatelům v rámci registrace nezobrazí náklady.  Toto nastavení spravuje správce registrace. Nebo, pokud se registrace není zakoupili přímo prostřednictvím společnosti Microsoft, jsou nastavení spravuje partner.  Tento článek vám pomůže pochopit nastavení jsou a jak by mohly mít dopad registraci. Tato nastavení jsou nezávislé na role řízení přístupu na základě Role v Azure.

## <a name="enable-access-to-costs"></a>Povolení přístupu k náklady

Jste si zobrazovat zpráva Neautorizováno, nebo *"zobrazení nákladů jsou zakázaná v registraci."* Pokud hledáte informace o nákladech?
![Snímek obrazovky zobrazující "neoprávněné" nákladů pro aktuální pole pro předplatné.](media/billing-enterprise-mgmt-groups/unauthorized.png)

Může to být z jednoho z následujících důvodů:

1. Azure jste zakoupili prostřednictvím partnera enterprise a partnera neuvolnila ještě ceny. Získáte od svého partnera se aktualizovat nastavení v rámci ceny [Enterprise portal](https://ea.azure.com).
2. Pokud jste zákazník se smlouvou EA Direct, máte několik možností:
    * Jste vlastníkem účtu a správce registrace zakázaná **AO zobrazit náklady** nastavení.  
    * Jste správcem oddělení a správce registrace zakázaná **DA zobrazit náklady** nastavení.
    * Obraťte se na správce registrace získat přístup. Správce registrace můžete aktualizovat nastavení [Enterprise portal](https://ea.azure.com/manage/enrollment).

      ![Snímek obrazovky zobrazující nastavení portálu Enterprise pro zobrazení poplatky.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Prostředek není k dispozici

Pokud se zobrazí chybová zpráva o **tento prostředek není k dispozici** při pokusu o přístup k předplatnému nebo skupině pro správu, pak nemáte správnou roli zobrazíte tuto položku.  

![Snímek obrazovky zobrazující zprávu "prostředek není k dispozici".](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Vaše předplatné nebo správu skupiny Správce služby Azure požádejte o přístup. Další informace najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další postup
- Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
