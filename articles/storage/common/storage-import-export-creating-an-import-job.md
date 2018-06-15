---
title: Vytvořit úlohu importu pro Azure Import/Export | Microsoft Docs
description: Naučte se vytvářet importu pro službu Microsoft Azure Import/Export.
author: muralikk
manager: syadav
editor: syadav
services: storage
documentationcenter: ''
ms.assetid: 8b886e83-6148-4149-9d0f-5d48ec822475
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: a80d2169f346238f997c727f0e9d82666897b608
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365878"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Vytvoření úlohy importu do služby Azure Import/Export

Vytvoření úlohy importu do služby Microsoft Azure Import/Export pomocí rozhraní REST API zahrnuje následující kroky:

-   Příprava jednotky pomocí nástroje Azure Import/Export.

-   Získání umístění, do kterého pro odeslání disk.

-   Vytvoření úlohy importu.

-   Přesouvání jednotky společnosti Microsoft prostřednictvím podporovaných poskytovatel služby.

-   Aktualizace úlohy importu s podrobnostmi o přesouvání.

 V tématu [pomocí služby Microsoft Azure Import/Export přenos dat do úložiště objektů Blob](storage-import-export-service.md) přehled službu Import/Export a kurz, který ukazuje, jak používat [portál Azure](https://portal.azure.com/) pro vytváření a správu import a export úloh.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Příprava jednotky pomocí nástroje Azure Import/Export

Kroky při přípravě jednotky pro úlohy importu jsou stejné, vytvořte jobvia portálu nebo pomocí rozhraní REST API.

Níže je stručný přehled přípravy jednotky. Odkazovat [Azure Import ExportTool odkaz](storage-import-export-tool-how-to-v1.md) úplné pokyny. Si můžete stáhnout nástroj Azure Import/Export [zde](http://go.microsoft.com/fwlink/?LinkID=301900).

Příprava vašeho disku zahrnuje:

-   Určení dat, která bude importována.

-   Identifikace cílové objekty BLOB v systému Windows Azure Storage.

-   Pomocí nástroje Azure Import/Export ke kopírování dat na jeden nebo více pevných disků.

 Nástroj Azure Import/Export bude také generovat soubor manifestu pro každou z jednotky jako je připraveno. Soubor manifestu obsahuje:

-   Výčet všech souborů určených pro odeslání a mapování z těchto souborů na objekty BLOB.

-   Kontrolní součty segmentů každého souboru.

-   Informace o metadatech a vlastnosti, které chcete přidružit každý objekt blob.

-   Seznam pro případ, pokud objekt blob, který se nahrává má stejný název jako existující objekt blob v kontejneru. Dostupné možnosti patří: a) se souborem přepsat objektu blob, b) zachovat existující objekt blob a přeskočit nahrání souboru, c) přidejte příponu k názvu tak, aby nedošlo ke konfliktu s ostatními soubory.

## <a name="obtaining-your-shipping-location"></a>Získání vaši polohu přesouvání

Před vytvořením úlohy importu, je nutné získat přenosů umístění názvu a adresy voláním [umístění seznamu](/rest/api/storageimportexport/listlocations) operaci. `List Locations` Vrátí seznam umístění a jejich poštovní adresy. Můžete vybrat umístění ze seznamu vrácených a dodávat vaše pevné disky na tuto adresu. Můžete také `Get Location` operace přesouvání adresu pro konkrétní umístění získat přímo.

 Použijte následující postup k získání přesouvání umístění:

-   Určete název umístění účtu úložiště. Tuto hodnotu najdete v části **umístění** na účet úložiště **řídicí panel** ve službě Azure portálu nebo předmětem dotazu pro pomocí operace rozhraní API pro správu služby [získat vlastnosti účtu úložiště](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Načíst umístění, které je k dispozici pro zpracování tento účet úložiště pomocí volání `Get Location` operaci.

-   Pokud `AlternateLocations` vlastnost umístění obsahuje umístění, sám sebe a potom je to v pořádku pro toto umístění používat. Jinak volání `Get Location` operaci zopakovat s alternativní umístění. Do původního umístění může být pro údržbu dočasně ukončeny.

## <a name="creating-the-import-job"></a>Vytvoření úlohy importu
Chcete-li vytvořit úlohu importu, zavolejte [Put úlohy](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operaci. Budete muset zadat následující informace:

-   Název úlohy.

-   Název účtu úložiště.

-   Přenosů název umístění, získané z předchozího kroku.

-   Typ úlohy (Importovat).

-   Zpětná adresa, kde by měly být odeslány jednotky po dokončení úlohy importu.

-   Seznam jednotek v úloze. Pro každou jednotku musí zahrnovat následující informace, které se zjišťovala během přípravy krok jednotky:

    -   Id jednotky

    -   Klíč nástroje BitLocker

    -   Relativní cesta souboru manifestu na pevný disk.

    -   Hodnota hash souboru manifestu MD5 kódovaný Base16

## <a name="shipping-your-drives"></a>Přesouvání jednotky
Je nutné dodat jednotky na adresu, kterou jste získali v předchozím kroku, a musíte poskytnout službu Import/Export číslem sledování balíčku.

> [!NOTE]
>  Je nutné dodat jednotky prostřednictvím podporovaných poskytovatel služby, která bude poskytovat sledování Číslo pro svůj balíček.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Aktualizace úlohy importu se vaše přenosů informace
Až budete mít vaše číslo sledování, volání [aktualizovat vlastnosti úlohy](/api/storageimportexport/jobs#Jobs_Update) operace aktualizace přenosů poskytovatel název, číslo sledování pro úlohu a poskytovatel číslo účtu pro návratový přesouvání. Volitelně můžete zadat počet jednotek a i přesouvání datum.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Další postup

* [Pomocí REST API služby importu a exportu](storage-import-export-using-the-rest-api.md)
