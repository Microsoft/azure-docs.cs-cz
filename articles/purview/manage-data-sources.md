---
title: Správa zdrojů dat ve službě Azure dosah (Preview)
description: Naučte se registrovat nové zdroje dat, spravovat kolekce zdrojů dat a zobrazovat zdroje ve službě Azure dosah (Preview).
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 8714c3c3794186d6c21a0513bd7700764c000b6d
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694783"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Správa zdrojů dat ve službě Azure dosah (Preview)

V tomto článku se dozvíte, jak registrovat nové zdroje dat, spravovat kolekce zdrojů dat a zobrazovat zdroje ve službě Azure dosah (Preview). Azure dosah podporuje následující zdroje dat:

* Místní SQL Server
* Azure Data Lake Storage Gen1 
* Azure Data Lake Storage Gen2
* Azure Blob Storage
* Průzkumník dat Azure
* Azure SQL DB
* Spravovaná instance Azure SQL DB
* Azure Synapse Analytics (dříve SQL DW)
* Azure Cosmos DB
* Power BI

## <a name="register-a-new-source"></a>Registrace nového zdroje

K registraci nového zdroje použijte následující postup.

1. Otevřete dosah Studio a vyberte dlaždici **Registrovat zdroje** .

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure dosah Studio":::

1. Vyberte možnost **Registrovat** a potom vyberte typ zdroje. V tomto příkladu se používá Azure Blob Storage. Vyberte **Pokračovat**.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="Výběr typu zdroje dat na stránce registrovat zdroje":::

1. Vyplňte formulář na stránce **Registrovat zdroje** . Vyberte název zdroje a zadejte příslušné informace. Pokud jste jako metodu výběru účtu zvolili možnost **z předplatného Azure** , zdroje v rámci vašeho předplatného se zobrazí v rozevíracím seznamu. Případně můžete zadat informace o zdroji ručně.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="Formulář pro informace o zdroji dat":::

1. Vyberte **Dokončit**.

## <a name="view-sources"></a>Zobrazit zdroje

Všechny zaregistrované zdroje můžete zobrazit na kartě **zdroje** v Azure dosah studiu. Existují dva typy zobrazení: zobrazení mapy a zobrazení seznamu.

### <a name="map-view"></a>Zobrazení mapy

V zobrazení mapy vidíte všechny vaše zdroje a kolekce. Na následujícím obrázku je k dispozici jeden zdroj služby Azure Blob Storage. Na každé dlaždici zdroje můžete upravit zdroj, spustit novou kontrolu nebo zobrazit podrobnosti o zdroji.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Zobrazení mapy zdroje dat Azure dosah":::

### <a name="list-view"></a>zobrazení seznamu

V zobrazení seznamu se zobrazí seznam zdrojů, které lze seřadit. Najeďte myší na zdroj, pokud chcete upravit možnosti, začněte novou kontrolu nebo odstraňte.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Zobrazení seznamu zdrojů dat Azure dosah":::

## <a name="manage-collections"></a>Spravovat kolekce

Zdroje dat můžete seskupit do kolekcí. Novou kolekci vytvoříte tak, že na stránce *zdroje* v Azure dosah studiu vyberete **+ Nová kolekce** . Zadejte název kolekce a jako nadřazený objekt vyberte *None (žádné* ). Nová kolekce se zobrazí v zobrazení mapy.

Chcete-li přidat zdroje do kolekce, vyberte možnost **Upravit** tužku na zdroji a zvolte kolekci z rozevírací nabídky **Vybrat kolekci** .

Chcete-li vytvořit hierarchii kolekcí, přiřaďte kolekce vyšší úrovně jako nadřazený ke kolekcím nižší úrovně. Na následujícím obrázku je *Fabrikam* nadřazená kolekce *finance* , která obsahuje zdroj dat Azure Blob Storage. Kolekce můžete sbalit nebo rozbalit kliknutím na kroužek připojeného k šipce mezi úrovněmi.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Hierarchie kolekcí v Azure dosah studiu":::

Zdroje z hierarchie můžete odebrat tak, že pro nadřazený prvek vyberete *None (žádné* ). Nenadřazené zdroje jsou seskupeny do pole s tečkami v zobrazení mapy bez šipky, které je propojuje s nadřazenými prvky.

## <a name="next-steps"></a>Další kroky

Naučte se registrovat a kontrolovat různé zdroje dat:

* [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)
* [Tenant Power BI](register-scan-power-bi-tenant.md)
* [Azure SQL Database](register-scan-azure-sql-database.md)
