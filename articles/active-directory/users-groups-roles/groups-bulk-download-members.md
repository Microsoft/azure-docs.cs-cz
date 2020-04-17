---
title: Seznam členství ve skupině hromadného stahování – portál Azure Active Directory | Dokumenty společnosti Microsoft
description: Přidejte uživatele hromadně v Centru pro správu Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3faca8d1a2538ed03a917d6db8d54323fe626369
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533668"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Hromadná stahování členů skupiny ve službě Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete hromadně stáhnout členy skupiny ve vaší organizaci do souboru hodnot oddělených čárkami (CSV).

## <a name="to-bulk-download-group-membership"></a>Hromadné stahování členství ve skupině

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupin mohou také hromadně stahovat členy skupin, které vlastní.
1. Ve službě Azure AD vyberte **Skupiny** > **všechny skupiny**.
1. Otevřete skupinu, jejíž členství chcete stáhnout, a vyberte **členové**.
1. Na stránce **Členové** vyberte **Stáhnout členy** a stáhněte soubor CSV se seznamem členů skupiny.

   ![Příkaz Stáhnout členy je na stránce profilu skupiny.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Zkontrolovat stav stahování

Stav všech nevyřízených hromadných požadavků můžete zobrazit na stránce **S výsledky hromadné operace.**

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity služby hromadného stahování

Každá hromadná aktivita ke stažení seznamu členů skupiny může běžet až jednu hodinu. To vám umožní stáhnout seznam alespoň 500 000 členů.

## <a name="next-steps"></a>Další kroky

- [Členové skupiny hromadného importu](groups-bulk-import-members.md)
- [Hromadné odebrání členů skupiny](groups-bulk-download-members.md)
