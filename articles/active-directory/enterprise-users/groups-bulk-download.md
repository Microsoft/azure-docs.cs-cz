---
title: Stažení seznamu skupin na portálu Azure Active Directory | Microsoft Docs
description: Stažení vlastností skupiny hromadně v centru pro správu Azure v Azure Active Directory.
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
ms.openlocfilehash: db800f3827d1b818115c886412ff7d4ffea3e768
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375570"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Hromadné stažení seznamu skupin v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete hromadně stahovat seznam všech skupin ve vaší organizaci do souboru hodnot oddělených čárkami (CSV).

## <a name="to-download-a-list-of-groups"></a>Stažení seznamu skupin

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce v organizaci.
1. V Azure AD vyberte **skupiny**  >  **Stáhnout skupiny**.
1. Na stránce **stažení skupin** vyberte možnost **Spustit** pro příjem souboru CSV se seznamem vašich skupin.

   ![Příkaz skupiny pro stahování se nachází na stránce všechny skupiny.](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Kontrolovat stav stahování

Na stránce **výsledků hromadných operací** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

[![Ověřte stav na stránce výsledků hromadných operací.](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Omezení služby hromadného stahování

Každá Hromadná aktivita ke stažení seznamu skupin může běžet po dobu až jedné hodiny. To vám umožní stáhnout seznam minimálně 300 000 skupin.

## <a name="next-steps"></a>Další kroky

- [Hromadné odebrání členů skupiny](groups-bulk-remove-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
