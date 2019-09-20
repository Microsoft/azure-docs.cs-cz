---
title: Seznam členství ve skupině hromadného stahování – Azure Active Directory portálu | Microsoft Docs
description: Hromadné přidání uživatelů do centra pro správu Azure.
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
ms.openlocfilehash: 94c2ac1d662851b5a0b44ec475becb5f5e0403c4
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146396"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Hromadné stažení členů skupiny (Preview) v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete hromadně stahovat členy skupiny ve vaší organizaci do souboru hodnot oddělených čárkami (CSV).

> [!NOTE]
> Hromadné operace Azure AD jsou funkcí veřejné verze Preview služby Azure AD a jsou dostupné s placeným licenčním plánem Azure AD. Další informace o tom, jak používat verzi Preview, najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="to-bulk-download-group-membership"></a>Postup hromadného stažení členství ve skupině

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupiny můžou také hromadně stahovat členy skupin, které vlastní.
1. V Azure AD vyberte **skupiny** > **všechny skupiny**.
1. Otevřete skupinu, jejíž členství chcete stáhnout, a pak vyberte **Členové**.
1. Na stránce **Členové** vyberte **Stáhnout členy** a Stáhněte si soubor CSV se seznamem členů skupiny.

   ![Příkaz pro stažení členů je na stránce profilu pro skupinu](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Kontrolovat stav stahování

Na stránce **výsledky hromadných operací (Preview)** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   ![Stránka s výsledky hromadných operací zobrazuje stav hromadné žádosti](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Omezení služby hromadného stahování

Každá Hromadná aktivita pro stažení seznamu členů skupiny může běžet až po dobu jedné hodiny. To vám umožní stáhnout seznam minimálně 500 000 členů.

## <a name="next-steps"></a>Další kroky

- [Členové skupiny hromadného importu](groups-bulk-import-members.md)
- [Hromadné odebrání členů skupiny](groups-bulk-download-members.md)
