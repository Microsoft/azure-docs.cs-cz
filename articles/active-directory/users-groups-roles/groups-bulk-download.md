---
title: Stažení seznamu skupin na portálu Azure Active Directory | Dokumenty společnosti Microsoft
description: V Centru pro správu Azure ve službě Azure Active Directory si hromadně stáhněte vlastnosti skupiny.
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
ms.openlocfilehash: 59983678c1b14d6aa87a7b500605e3abeb6a9b85
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533530"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Hromadné stažení seznamu skupin ve službě Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete hromadně stáhnout seznam všech skupin ve vaší organizaci do souboru hodnot oddělených čárkami (CSV).

## <a name="to-download-a-list-of-groups"></a>Stažení seznamu skupin

1. Přihlaste se [k portálu Azure](https://portal.azure.com) pomocí účtu správce v organizaci.
1. Ve službě Azure AD vyberte **skupiny** > **skupin y stáhnout skupiny**.
1. Na stránce **Skupiny ke stažení** vyberte **Start,** chcete-li obdržet soubor CSV se seznamem skupin.

   ![Příkaz Skupiny stahování je na stránce Všechny skupiny.](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Zkontrolovat stav stahování

Stav všech nevyřízených hromadných požadavků můžete zobrazit na stránce **S výsledky hromadné operace.**

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity služby hromadného stahování

Každá hromadná aktivita ke stažení seznamu skupin může běžet až jednu hodinu. To vám umožní stáhnout seznam alespoň 300 000 skupin.

## <a name="next-steps"></a>Další kroky

- [Hromadné odebrání členů skupiny](groups-bulk-remove-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
