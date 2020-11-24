---
title: Hromadné odebrání členů skupiny odesláním souboru CSV – Azure Active Directory | Microsoft Docs
description: Odeberte členy skupiny v hromadných operacích v centru pro správu Azure.
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
ms.openlocfilehash: 874d434e545555013812714df4d1bd23de9c8543
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488942"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Hromadné odebrání členů skupiny v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete odebrat velký počet členů ze skupiny pomocí souboru hodnot oddělených čárkami (CSV) pro hromadnou odebrání členů skupiny.

## <a name="understand-the-csv-template"></a>Princip šablony sdíleného svazku clusteru

Pokud chcete úspěšně přidat členy skupiny Azure AD hromadně, Stáhněte a vyplňte šablonu CSV hromadného nahrání. Vaše šablona CSV může vypadat jako v tomto příkladu:

![Tabulka pro nahrávání a volání s vysvětlením účelu a hodnot pro každý řádek a sloupec](./media/groups-bulk-remove-members/template-example.png)

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

## <a name="to-bulk-remove-group-members"></a>Hromadné odebrání členů skupiny

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupiny můžou také hromadně odebírat členy skupin, které vlastní.
1. V Azure AD vyberte **skupiny**  >  **všechny skupiny**.
1. Otevřete skupinu, ze které odebíráte členy, a pak vyberte **Členové**.
1. Na stránce **Členové** vyberte **odebrat členy**.
1. Na stránce **hromadné odebrání členů skupiny** vyberte **Stáhnout** a získejte šablonu souboru CSV s požadovanými vlastnostmi člena skupiny.

   ![Příkaz odebrat členy je na stránce profilu pro skupinu.](./media/groups-bulk-remove-members/remove-panel.png)

1. Otevřete soubor CSV a přidejte řádek pro každého člena skupiny, který chcete ze skupiny odebrat (požadované hodnoty jsou ID členského objektu nebo hlavní název uživatele). Pak soubor uložte.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Soubor CSV obsahuje jména a ID členů skupiny, které se mají odebrat.":::

1. Na stránce **hromadné odebrání členů skupiny** v části **nahrát soubor CSV** přejděte k souboru. Po výběru souboru se spustí ověření souboru CSV.
1. Po ověření obsahu souboru se zobrazí stránka hromadného importu **úspěšně nahrané soubory**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která odebere členy skupiny ze skupiny.
1. Po dokončení operace odebrání se zobrazí oznámení, že hromadná operace byla úspěšná.

## <a name="check-removal-status"></a>Stav odebrání kontroly

Na stránce **výsledků hromadných operací** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

[![Stav kontroly na stránce výsledků hromadných operací](./media/groups-bulk-remove-members/bulk-center.png)](./media/groups-bulk-remove-members/bulk-center.png#lightbox)

Chcete-li zobrazit podrobnosti o jednotlivých položkách řádku v rámci hromadné operace, vyberte hodnoty ve sloupcích **# úspěch**, **# selhání** nebo **Celkový počet požadavků** . Pokud dojde k chybám, zobrazí se důvody selhání.

## <a name="bulk-removal-service-limits"></a>Omezení služby hromadného odebrání

Každá Hromadná aktivita, ze které se mají odebrat seznam členů skupiny, může běžet po dobu až jedné hodiny. Tím se povolí odebrání seznamu minimálně 40 000 členů.

## <a name="next-steps"></a>Další kroky

- [Členové skupiny hromadného importu](groups-bulk-import-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
- [Stažení seznamu všech skupin](groups-bulk-download.md)
