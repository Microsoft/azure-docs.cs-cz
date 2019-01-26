---
title: Zobrazení historie auditů pro role adresáře Azure AD v PIM | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit historie auditů pro role adresáře Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 191d384d51fe3aecbd967586516cfb198256af81
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911723"
---
# <a name="view-audit-history-for-azure-ad-directory-roles-in-pim"></a>Zobrazení historie auditů pro role adresáře Azure AD v PIM
Pokud chcete zobrazit všechna přiřazení uživatelů a aktivace v daném časovém období pro všechny privilegované role můžete použít historie auditu Privileged Identity Management (PIM). Pokud chcete zobrazit úplnou historii auditu aktivity ve vašem tenantovi, včetně správce, koncový uživatel a aktivitu synchronizace můžete použít [sestavy o přístupech a použití služby Azure Active Directory.](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-audit-history"></a>Přejděte do historie auditu
Z [webu Azure portal](https://portal.azure.com) řídicího panelu, vyberte **Azure AD Privileged Identity Management** aplikace. Odtud přejít do historie auditu kliknutím **spravovat privilegované role** > **historie auditu** na řídicím panelu PIM.

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Řazení dat podle akce a vyhledejte "Schválení aktivace"


## <a name="audit-history-graph"></a>Graf historie auditu
Chcete-li zobrazit celkový počet aktivací, maximální počet aktivací za den a průměrný počet aktivací za den ve spojnicovém grafu můžete do historie auditu.  Můžete také filtrovat data podle rolí, pokud existuje více než jedné role do historie auditu.

Použití **čas**, **akce**, a **role** tlačítka řazení v historii.

## <a name="audit-history-list"></a>Seznam historie auditu
Sloupce v seznamu historie auditu jsou:

* **Žadatel** – uživatel, který požadované aktivaci role nebo změnit.  Pokud je hodnota "Azure systém", zkontrolujte historii auditování Azure pro další informace.
* **Uživatel** – uživatel, který je přiřazený k roli nebo aktivace.
* **Role** -roli přiřazeni nebo uživatel aktivoval.
* **Akce** – akce prováděné žadatel. To může zahrnovat přiřazení, zrušení, aktivace nebo deaktivace.
* **Čas** – kdy akce došlo k chybě.
* **Důvody** – Pokud jakýkoli text byl zadán do pole Důvod při aktivaci, se zobrazí tady.
* **Vypršení platnosti** – pouze relevantní pro aktivaci role.

## <a name="filter-audit-history"></a>Filtrovat historie auditu
Informace, který se zobrazuje historie auditu kliknutím můžete filtrovat **filtr** tlačítko.  **Aktualizace grafu parametry okno** se zobrazí.

Po nastavení filtrů, klikněte na tlačítko **aktualizace** k filtrování dat v historii.  Pokud se data nezobrazí okamžitě, aktualizujte stránku.

### <a name="change-the-date-range"></a>Změnit rozsah
Použití **Dnes**, **minulý týden**, **poslední měsíc**, nebo **vlastní** tlačítka, chcete-li změnit časové rozmezí historie auditu.

Při výběru možnosti **vlastní** tlačítko, budete mít **z** pole pro datum a **k** pole pro datum pro zadání rozsahu kalendářních dat historie.  Můžete zadat data ve formátu MM/DD/RRRR nebo klikněte na **kalendáře** ikonu a pak vyberte datum z kalendáře.

### <a name="change-the-roles-included-in-the-history"></a>Změna role v historii
Zaškrtněte nebo zrušte zaškrtnutí políčka **Role** zaškrtávací políčko vedle každé role pro zahrnutí nebo vyloučení z historie.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup

- [Zobrazit historii aktivit a auditu pro role prostředků Azure v PIM](azure-pim-resource-rbac.md)
