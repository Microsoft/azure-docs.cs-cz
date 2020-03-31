---
title: Seznam členství ve skupině hromadného stahování – portál Azure Active Directory | Dokumenty společnosti Microsoft
description: Přidejte uživatele hromadně v Centru pro správu Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517156"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Hromadná stahování členů skupiny (preview) ve službě Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete hromadně stáhnout členy skupiny ve vaší organizaci do souboru hodnot oddělených čárkami (CSV).

## <a name="to-bulk-download-group-membership"></a>Hromadné stahování členství ve skupině

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupin mohou také hromadně stahovat členy skupin, které vlastní.
1. Ve službě Azure AD vyberte **Skupiny** > **všechny skupiny**.
1. Otevřete skupinu, jejíž členství chcete stáhnout, a vyberte **členové**.
1. Na stránce **Členové** vyberte **Stáhnout členy** a stáhněte soubor CSV se seznamem členů skupiny.

   ![Příkaz Stáhnout členy je na stránce profilu skupiny.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Zkontrolovat stav stahování

Stav všech nevyřízených hromadných požadavků můžete zobrazit na stránce **Výsledky hromadné operace (preview).**

   ![Stránka Výsledky hromadných operací zobrazuje stav hromadnéžádosti](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limity služby hromadného stahování

Každá hromadná aktivita ke stažení seznamu členů skupiny může běžet až jednu hodinu. To vám umožní stáhnout seznam alespoň 500 000 členů.

## <a name="next-steps"></a>Další kroky

- [Členové skupiny hromadného importu](groups-bulk-import-members.md)
- [Hromadné odebrání členů skupiny](groups-bulk-download-members.md)
