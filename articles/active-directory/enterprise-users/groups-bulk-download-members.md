---
title: Seznam členství ve skupině hromadného stahování – Azure Active Directory portálu | Microsoft Docs
description: Hromadné přidání uživatelů do centra pro správu Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59327b33a228dfde2c303b63393a372b12370a05
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647405"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Hromadné stažení členů skupiny v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete hromadně stahovat členy skupiny ve vaší organizaci do souboru hodnot oddělených čárkami (CSV).

## <a name="to-bulk-download-group-membership"></a>Postup hromadného stažení členství ve skupině

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupiny můžou také hromadně stahovat členy skupin, které vlastní.
1. V Azure AD vyberte **skupiny**  >  **všechny skupiny**.
1. Otevřete skupinu, jejíž členství chcete stáhnout, a pak vyberte **Členové**.
1. Na stránce **Členové** vyberte **Stáhnout členy** a Stáhněte si soubor CSV se seznamem členů skupiny.

   ![Příkaz pro stažení členů je na stránce profilu pro skupinu](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Kontrolovat stav stahování

Na stránce **výsledků hromadných operací** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

[![Ověřte stav na stránce výsledků hromadných operací.](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Omezení služby hromadného stahování

Každá Hromadná aktivita pro stažení seznamu členů skupiny může běžet až po dobu jedné hodiny. To vám umožní stáhnout seznam minimálně 500 000 členů.

## <a name="next-steps"></a>Další kroky

- [Členové skupiny hromadného importu](groups-bulk-import-members.md)
- [Hromadné odebrání členů skupiny](groups-bulk-download-members.md)
