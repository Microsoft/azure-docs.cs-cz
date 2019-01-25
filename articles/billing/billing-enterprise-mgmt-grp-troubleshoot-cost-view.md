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
ms.date: 02/22/2017
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d35996b16d615a198b9a6039386f6b295172f388
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900183"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Řešení potíží s firemních nákladů zobrazení

V rámci podnikové registrace existuje několik nastavení, které by mohly způsobit uživatelům v rámci registrace nezobrazí náklady.  Toto nastavení spravuje správce registrace. Nebo, pokud se registrace není zakoupili přímo prostřednictvím společnosti Microsoft, jsou nastavení spravuje partner.  Tento článek vám pomůže pochopit nastavení jsou a jak by mohly mít dopad registraci. Tato nastavení jsou nezávislé na role řízení přístupu na základě Role v Azure.

## <a name="enabling-access-to-costs"></a>Povolení přístupu k náklady

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

Pokud se zobrazí chybová zpráva "Tento prostředek není k dispozici" při pokusu o přístup k předplatné nebo skupinu pro správu a pak nemají správnou roli zobrazíte tuto položku.  

![Snímek obrazovky zobrazující zprávu "prostředek není k dispozici".](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Vaše předplatné nebo správu skupiny Správce služby Azure požádejte o přístup. Další informace najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).
