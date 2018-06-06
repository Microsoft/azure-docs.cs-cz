---
title: Jak používat protokol auditu v Azure AD Privileged Identity managementu | Microsoft Docs
description: Naučte se používat protokol auditu v rozšíření Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 45be09b76389471497f874dccf21453372d27a5e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698539"
---
# <a name="using-the-audit-log-in-pim"></a>Použití protokolu auditování v PIM
Protokol auditu Privileged Identity Management (PIM) můžete zobrazit všechny uživatele přiřazení a aktivace v daném časovém období. Pokud chcete zobrazit historii úplné auditu aktivity ve vašem klientovi, včetně správce, koncového uživatele a aktivity synchronizace můžete použít [přístupu a použití sestav Azure Active Directory.](active-directory-view-access-usage-reports.md)

## <a name="navigate-to-the-audit-log"></a>Přejděte na protokol auditu
Z [portál Azure](https://portal.azure.com) řídicí panel, vyberte **Azure AD Privileged Identity Management** aplikace. Odtud, přístup k protokolu auditu kliknutím **spravovat privilegované role** > **historie auditu** v řídicím panelu PIM.

## <a name="the-audit-log-graph"></a>Graf protokolu auditu
Chcete-li zobrazit celkový počet aktivací, maximální počet aktivací za den a průměrný počet aktivací za den ve spojnicovém grafu, můžete použít protokol auditu.  Data lze filtrovat také role, pokud je víc než jedné role v historie auditu.

Použití **čas**, **akce**, a **role** tlačítka seřadíte v protokolu.

## <a name="the-audit-log-list"></a>Seznam protokolu auditu
Sloupce v seznamu protokolů auditu jsou:

* **Žadatel** -uživatel, který požadovaný aktivaci role nebo změnit.  Pokud je hodnota "Azure systém", zkontrolujte protokol auditu Azure pro další informace.
* **Uživatel** -uživatel, který je přiřazený k roli nebo aktivace.
* **Role** – přiřazené roli, nebo uživatel aktivoval.
* **Akce** – akce prováděné žadatel. To může zahrnovat přiřazení, zrušení, aktivace nebo deaktivace.
* **Čas** – Pokud akce došlo k chybě.
* **Důvody** – Pokud jakýkoli text byl zadali do pole Důvod během aktivace, se zobrazí tady.
* **Vypršení platnosti** – pouze relevantní pro aktivaci role.

## <a name="filter-the-audit-log"></a>Filtrovat protokol auditu
Můžete filtrovat informace, které se zobrazí v protokolu auditování kliknutím **filtru** tlačítko.  **Aktualizace grafu parametry okno** se zobrazí.

Jakmile jste nastavili filtry, klikněte na tlačítko **aktualizace** k filtrování dat v protokolu.  Pokud data nezobrazí okamžitě, aktualizujte stránku.

### <a name="change-the-date-range"></a>Změnit rozsah dat
Použití **Dnes**, **minulého týdne**, **poslední měsíc**, nebo **vlastní** Chcete-li změnit časové rozmezí protokol auditu.

Pokud vyberete **vlastní** tlačítko, budete mít **z** pole pro datum a **k** pole pro datum k určení rozsahu dat pro protokol.  Můžete zadat data ve formátu MM/DD/RRRR nebo klikněte na **kalendáře** ikonu a vyberte datum z kalendáře.

### <a name="change-the-roles-included-in-the-log"></a>Změna role zahrnuté v protokolu
Zaškrtněte nebo zrušte zaškrtnutí políčka **Role** zaškrtávací políčko vedle každé role zahrnout nebo vyloučit z protokolu.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

