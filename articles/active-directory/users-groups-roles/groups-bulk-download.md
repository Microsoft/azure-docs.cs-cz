---
title: Stažení seznamu skupin na portálu Azure Active Directory | Microsoft Docs
description: Stažení vlastností skupiny hromadně v centru pro správu Azure v Azure Active Directory.
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
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517151"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Hromadné stažení seznamu skupin (Preview) v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete hromadně stahovat seznam všech skupin ve vaší organizaci do souboru hodnot oddělených čárkami (CSV).

## <a name="to-download-a-list-of-groups"></a>Stažení seznamu skupin

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce v organizaci.
1. V Azure AD vyberte **skupiny**  > **stažení skupin**.
1. Na stránce **stažení skupin** vyberte možnost **Spustit** pro příjem souboru CSV se seznamem vašich skupin.

   ![Příkaz skupiny pro stahování se nachází na stránce všechny skupiny.](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Kontrolovat stav stahování

Na stránce **výsledky hromadných operací (Preview)** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   ![Stránka s výsledky hromadných operací zobrazuje stav hromadné žádosti](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Omezení služby hromadného stahování

Každá Hromadná aktivita ke stažení seznamu skupin může běžet po dobu až jedné hodiny. To vám umožní stáhnout seznam minimálně 300 000 skupin.

## <a name="next-steps"></a>Další kroky

- [Hromadné odebrání členů skupiny](groups-bulk-remove-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
