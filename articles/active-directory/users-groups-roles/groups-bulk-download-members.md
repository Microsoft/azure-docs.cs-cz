---
title: Seznam členství ve skupině hromadného stahování – Azure Active Directory portálu | Microsoft Docs
description: Hromadné přidání uživatelů do centra pro správu Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65a6a622a0d3fac7b28cc699d860068f12b780fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728601"
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

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Omezení služby hromadného stahování

Každá Hromadná aktivita pro stažení seznamu členů skupiny může běžet až po dobu jedné hodiny. To vám umožní stáhnout seznam minimálně 500 000 členů.

## <a name="next-steps"></a>Další kroky

- [Členové skupiny hromadného importu](groups-bulk-import-members.md)
- [Hromadné odebrání členů skupiny](groups-bulk-download-members.md)
