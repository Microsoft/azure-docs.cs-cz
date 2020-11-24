---
title: Stažení seznamu skupin na portálu Azure Active Directory | Microsoft Docs
description: Stažení vlastností skupiny hromadně v centru pro správu Azure v Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd624abdcd358dfc1d26c3092e53691ad51cf75f
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503559"
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
