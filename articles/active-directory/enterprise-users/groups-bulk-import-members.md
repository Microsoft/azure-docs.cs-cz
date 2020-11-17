---
title: Hromadné nahrání pro přidání nebo vytvoření členů skupiny – Azure Active Directory | Microsoft Docs
description: Přidejte členy skupiny hromadně v centru pro správu Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 227a8261aef7953c07bcc12f7754a56dd8e7fafa
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647422"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Hromadné přidání členů skupiny v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete do skupiny přidat velký počet členů pomocí souboru hodnot oddělených čárkami (CSV) pro hromadnou import členů skupiny.

## <a name="understand-the-csv-template"></a>Princip šablony sdíleného svazku clusteru

Pokud chcete úspěšně přidat členy skupiny Azure AD hromadně, Stáhněte a vyplňte šablonu CSV hromadného nahrání. Vaše šablona CSV může vypadat jako v tomto příkladu:

![Tabulka pro nahrávání a volání s vysvětlením účelu a hodnot pro každý řádek a sloupec](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>Struktura šablony CSV

Řádky ve stažené šabloně CSV jsou následující:

- **Číslo verze**: první řádek obsahující číslo verze musí být zahrnut do souboru CSV pro nahrávání.
- **Záhlaví sloupců**: formát záhlaví sloupců je &lt; *název položky* &gt; [PropertyName] &lt; *povinný nebo prázdný* &gt; . Například, `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Některé starší verze šablony mohou mít drobné variace. Pro změny členství ve skupinách máte možnost, který identifikátor použít: ID objektu člena nebo hlavní název uživatele.
- **Řádek příklady**: v šabloně jsme zahrnuli řádek příkladů přípustných hodnot pro každý sloupec. Řádek příklady musíte odebrat a nahradit ho vlastními položkami.

### <a name="additional-guidance"></a>Další doprovodné materiály

- První dva řádky šablony nahrávání se nesmí odebrat ani změnit, jinak se nahrávání nedá zpracovat.
- Požadované sloupce jsou uvedeny jako první.
- Nedoporučujeme přidávat do šablony nové sloupce. Všechny další sloupce, které přidáte, se ignorují a nezpracovávají.
- Doporučujeme si stáhnout nejnovější verzi šablony CSV, jak je to možné.
- K úspěšnému nahrání souboru přidejte alespoň dva hlavní názvy uživatelů (UPN) nebo ID objektů.

## <a name="to-bulk-import-group-members"></a>Hromadný import členů skupiny

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupiny můžou také hromadně importovat členy skupin, které vlastní.
1. V Azure AD vyberte **skupiny**  >  **všechny skupiny**.
1. Otevřete skupinu, do které přidáváte členy, a pak vyberte **Členové**.
1. Na stránce **Členové** vyberte **importovat členy**.
1. Na stránce **Hromadný import členů skupiny** vyberte **Stáhnout** a získejte šablonu souboru CSV s požadovanými vlastnostmi člena skupiny.

    ![Příkaz Importovat členy je na stránce profilu pro skupinu.](./media/groups-bulk-import-members/import-panel.png)

1. Otevřete soubor CSV a přidejte řádek pro každého člena skupiny, který chcete do skupiny importovat (požadované hodnoty jsou buď **ID členského objektu** , nebo **hlavní název uživatele**). Pak soubor uložte.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Soubor CSV obsahuje názvy a ID členů k importu.":::

1. Na stránce **hromadně importovat skupiny členů** v části **nahrát soubor CSV** přejděte k souboru. Po výběru souboru se spustí ověření souboru CSV.
1. Po ověření obsahu souboru se zobrazí stránka hromadného importu **úspěšně nahrané soubory**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která importuje členy skupiny do skupiny.
1. Po dokončení operace importu se zobrazí oznámení, že hromadná operace byla úspěšná.

## <a name="check-import-status"></a>Ověřit stav importu

Na stránce **výsledků hromadných operací** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

[![Ověřte stav na stránce výsledků hromadných operací.](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

Chcete-li zobrazit podrobnosti o jednotlivých položkách řádku v rámci hromadné operace, vyberte hodnoty ve sloupcích **# úspěch**, **# selhání** nebo **Celkový počet požadavků** . Pokud dojde k chybám, zobrazí se důvody selhání.

## <a name="bulk-import-service-limits"></a>Omezení služby hromadného importu

Každá Hromadná aktivita pro import seznamu členů skupiny může běžet až po dobu jedné hodiny. To umožňuje import seznamu nejvýše 40 000 členů.

## <a name="next-steps"></a>Další kroky

- [Hromadné odebrání členů skupiny](groups-bulk-remove-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
- [Stažení seznamu všech skupin](groups-bulk-download.md)
