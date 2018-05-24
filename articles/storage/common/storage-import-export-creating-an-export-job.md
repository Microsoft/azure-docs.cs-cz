---
title: Vytvoření exportu úlohy pro Azure Import/Export | Microsoft Docs
description: Naučte se vytvářet úlohy exportu pro službu Microsoft Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 613d480b-a8ef-4b28-8f54-54174d59b3f4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3fb3f2af5e5cebcac21f4372bc9d9dc9ee837202
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Vytvoření úlohy exportu pro službu Azure Import/Export
Vytvoření úlohy exportu pro službu Microsoft Azure Import/Export pomocí rozhraní REST API zahrnuje následující kroky:

-   Výběr objektů blob pro export.

-   Získání přesouvání umístění.

-   Vytvoření úlohy exportu.

-   Přesouvání prázdný jednotky společnosti Microsoft prostřednictvím podporovaných poskytovatel služby.

-   Úloha exportu aktualizace s informací o balíčku.

-   Získání jednotky zpět od společnosti Microsoft.

 V tématu [pomocí služby Windows Azure Import/Export přenos dat do úložiště objektů Blob](storage-import-export-service.md) přehled službu Import/Export a kurz, který ukazuje, jak používat [portál Azure](https://portal.azure.com/) pro vytváření a správu import a export úloh.

## <a name="selecting-blobs-to-export"></a>Výběr objektů blob pro export
 Pokud chcete vytvořit úlohy exportu, musíte poskytnout seznam objektů BLOB, které chcete exportovat z vašeho účtu úložiště. Vyberte objekty BLOB export několika způsoby:

-   Cesta relativní objektů blob můžete vybrat jediného objektu blob a všechny jeho snímků.

-   Cesta relativní objektů blob můžete vybrat jeden objekt blob, s výjimkou jeho snímky.

-   Vyberte jeden snímek můžete cesta relativní objektů blob a čas snímku.

-   Předponu objektu blob můžete vybrat všechny objekty BLOB a snímky s danou předponu.

-   Můžete exportovat všechny objekty BLOB a snímky v účtu úložiště.

 Další informace o zadání objektů blob pro export, najdete v článku [Put úlohy](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operaci.

## <a name="obtaining-your-shipping-location"></a>Získání vaši polohu přesouvání
Před vytvořením úlohy exportu, je nutné získat přenosů umístění názvu a adresy voláním [získat umístění](https://portal.azure.com) nebo [umístění seznamu](/rest/api/storageimportexport/listlocations) operaci. `List Locations` Vrátí seznam umístění a jejich poštovní adresy. Můžete vybrat umístění ze seznamu vrácených a dodávat vaše pevné disky na tuto adresu. Můžete také `Get Location` operace přesouvání adresu pro konkrétní umístění získat přímo.

Použijte následující postup k získání přesouvání umístění:

-   Určete název umístění účtu úložiště. Tuto hodnotu najdete v části **umístění** na účet úložiště **řídicí panel** ve službě Azure portálu nebo předmětem dotazu pro pomocí operace rozhraní API pro správu služby [získat vlastnosti účtu úložiště](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Načíst umístění, které jsou k dispozici pro zpracování tento účet úložiště pomocí volání `Get Location` operaci.

-   Pokud `AlternateLocations` vlastnost umístění obsahuje umístění, sám sebe a potom je to v pořádku pro toto umístění používat. Jinak volání `Get Location` operaci zopakovat s alternativní umístění. Do původního umístění může být pro údržbu dočasně ukončeny.

## <a name="creating-the-export-job"></a>Vytvoření úlohy exportu
 Chcete-li vytvořit úlohu export, zavolejte [Put úlohy](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operaci. Budete muset zadat následující informace:

-   Název úlohy.

-   Název účtu úložiště.

-   Přenosů název umístění, získaných v předchozím kroku.

-   Typ úlohy (exportovat).

-   Zpětná adresa, kde by měly být odeslány jednotky po dokončení úlohy exportu.

-   Seznam objektů BLOB (nebo objekt blob předpony) pro export.

## <a name="shipping-your-drives"></a>Přesouvání jednotky
 V dalším kroku nástrojem Azure Import/Export můžete určit počet jednotek, které potřebujete k odeslání, na základě objektů BLOB, které jste vybrali pro export a velikost disku. Najdete v článku [Azure Import/Export nástroj odkaz](storage-import-export-tool-how-to-v1.md) podrobnosti.

 Balíček jednotek v jednom balíčku a jejich odeslání na adresu získaných v předchozím kroku. Poznamenejte si číslo sledování vašeho balíčku pro další krok.

> [!NOTE]
>  Je nutné dodat jednotky prostřednictvím podporovaných poskytovatel služby, která bude poskytovat sledování Číslo pro svůj balíček.

## <a name="updating-the-export-job-with-your-package-information"></a>Aktualizace úlohy exportu s informací o balíčku
 Až budete mít vaše číslo sledování, volání [vlastnosti úlohy aktualizace](/rest/api/storageimportexport/jobs#Jobs_Update) operace aktualizovala poskytovatel název a číslo úlohy sledování. Volitelně můžete zadat počet jednotek, zpáteční adresu a také přesouvání datum.

## <a name="receiving-the-package"></a>Přijetí balíčku
 Po zpracování vaše úloha exportu, budou vráceny jednotky vám s šifrovaná data. Klíč nástroje BitLocker pro každou z jednotky můžete načíst pomocí volání [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) operaci. Potom můžete odemknout jednotku pomocí klíče. Soubor manifestu jednotku na každém disku obsahuje seznam souborů na jednotce, stejně jako původní adresu objektů blob pro každý soubor.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Další postup

* [Pomocí REST API služby importu a exportu](storage-import-export-using-the-rest-api.md)
