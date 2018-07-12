---
title: Použití protokolu auditu v Azure AD Privileged Identity Management | Dokumentace Microsoftu
description: Další informace o použití protokolu auditu v rozšíření Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: ca6bb987c0f977746a8359dfd40cf7a3d643950a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590293"
---
# <a name="using-the-audit-log-in-pim"></a>Použití protokolu auditu v PIM
Protokol auditu Privileged Identity Management (PIM) můžete zobrazit všechna přiřazení uživatelů a aktivace v daném časovém období. Pokud chcete zobrazit úplnou historii auditu aktivity ve vašem tenantovi, včetně správce, koncový uživatel a aktivitu synchronizace můžete použít [sestavy o přístupech a použití služby Azure Active Directory.](../active-directory-reporting-azure-portal.md)

## <a name="navigate-to-the-audit-log"></a>Přejděte do protokolu auditu
Z [webu Azure portal](https://portal.azure.com) řídicího panelu, vyberte **Azure AD Privileged Identity Management** aplikace. Odtud, přístup k protokolu auditu kliknutím **spravovat privilegované role** > **historie auditu** na řídicím panelu PIM.

## <a name="the-audit-log-graph"></a>Graf protokolu auditu
Chcete-li zobrazit celkový počet aktivací, maximální počet aktivací za den a průměrný počet aktivací za den ve spojnicovém grafu můžete použít protokol auditu.  Můžete také filtrovat data podle rolí, pokud existuje více než jedné role do historie auditu.

Použití **čas**, **akce**, a **role** tlačítka řazení v protokolu.

## <a name="the-audit-log-list"></a>Seznam protokolu auditu
Sloupce v seznamu protokolů auditu jsou:

* **Žadatel** – uživatel, který požadované aktivaci role nebo změnit.  Pokud je hodnota "Azure systém", zkontrolujte protokol auditování Azure pro další informace.
* **Uživatel** – uživatel, který je přiřazený k roli nebo aktivace.
* **Role** -roli přiřazeni nebo uživatel aktivoval.
* **Akce** – akce prováděné žadatel. To může zahrnovat přiřazení, zrušení, aktivace nebo deaktivace.
* **Čas** – kdy akce došlo k chybě.
* **Důvody** – Pokud jakýkoli text byl zadán do pole Důvod při aktivaci, se zobrazí tady.
* **Vypršení platnosti** – pouze relevantní pro aktivaci role.

## <a name="filter-the-audit-log"></a>Filtrovat protokol auditu
Můžete filtrovat informace, které se zobrazí v protokolu auditu kliknutím **filtr** tlačítko.  **Aktualizace grafu parametry okno** se zobrazí.

Po nastavení filtrů, klikněte na tlačítko **aktualizace** k filtrování dat v protokolu.  Pokud se data nezobrazí okamžitě, aktualizujte stránku.

### <a name="change-the-date-range"></a>Změnit rozsah
Použití **Dnes**, **minulý týden**, **poslední měsíc**, nebo **vlastní** tlačítka, chcete-li změnit časový rozsah protokolu auditu.

Při výběru **vlastní** tlačítko, budete mít **z** pole pro datum a **k** pole pro datum pro zadání rozsahu kalendářních dat protokolu.  Můžete zadat data ve formátu MM/DD/RRRR nebo klikněte na **kalendáře** ikonu a pak vyberte datum z kalendáře.

### <a name="change-the-roles-included-in-the-log"></a>Změna role součástí protokolu
Zaškrtněte nebo zrušte zaškrtnutí políčka **Role** zaškrtávací políčko vedle každé role pro zahrnutí nebo vyloučení z protokolu.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

