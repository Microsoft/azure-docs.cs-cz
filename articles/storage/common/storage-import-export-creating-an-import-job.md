---
title: Vytvořit úlohu importu pro Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit importu pro službu Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 15ec4f006147cdffb46598d22a937aec429fd8ac
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729400"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Vytvoření úlohy importu pro službu Azure Import/Export

Vytvoření úlohy importu pomocí rozhraní REST API služby Microsoft Azure Import/Export zahrnuje následující kroky:

-   Příprava jednotky s nástrojem Import/Export Azure.

-   Získání umístění, do kterého k odeslání na jednotce.

-   Vytváří se úloha importu.

-   Přesouvání jednotky společnosti Microsoft prostřednictvím služby podporované operátora.

-   Aktualizuje se úloha importu podrobnosti o dopravě.

 V tématu [pomocí služby Microsoft Azure Import/Export pro přenos dat do úložiště objektů Blob](storage-import-export-service.md) Přehled služby Import/Export a kurz, který ukazuje, jak používat [webu Azure portal](https://portal.azure.com/) k vytvoření Správa import a export úloh.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Příprava jednotky pomocí nástroje Azure Import/Export

Kroky pro přípravu disků pro úlohu importu jsou stejné, ať už jobvia vytvoříte na portálu nebo prostřednictvím rozhraní REST API.

Níže uvádíme stručný přehled o přípravu jednotky. Odkazovat [referenčních informacích k Azure Import ExportTool](storage-import-export-tool-how-to-v1.md) úplné pokyny. Nástroj Azure Import/Export můžete stáhnout [tady](https://go.microsoft.com/fwlink/?LinkID=301900).

Příprava jednotky zahrnuje:

-   Identifikuje data, která mají být importována.

-   Určení cílové objektů BLOB ve službě Windows Azure Storage.

-   Použití nástroje Import/Export Azure ke zkopírování dat na jeden nebo více pevných disků.

 Nástroje Import/Export Azure také vygeneruje soubor manifestu pro každou z jednotky jako je připraven. Obsahuje soubor manifestu:

-   Výčet všech souborů, které jsou určené pro nahrávání a mapování z těchto souborů do objektů BLOB.

-   Kontrolní součty segmentů každého souboru.

-   Informace o vlastnosti, které chcete přidružit každý objekt blob a metadat.

-   Výpis akce má být provedena, pokud má stejný název jako existující objekt blob v kontejneru objektů blob, který se nahrává. Možnými volbami jsou: a) přepsat objekt blob se souborem, (b) zachovat existující objekt blob a přeskočení nahrávání souboru, c) připojit příponu k názvu tak, aby nejsou v konfliktu s jinými soubory.

## <a name="obtaining-your-shipping-location"></a>Získání vaše dodací umístění

Před vytvoření úlohy importu, je potřeba získat přenosů název umístění a adresa voláním [seznamu umístění](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) operace. `List Locations` Vrátí seznam umístění a jejich poštovních adres. Můžete vybrat umístění ze seznamu vrácených a dodávat pevných disků na tuto adresu. Můžete také použít `Get Location` operaci získat přímo dodací adresu pro konkrétní lokalitu.

 Podle následujících pokynů k získání umístění dopravě:

-   Určete název umístění účtu úložiště. Tuto hodnotu najdete v části **umístění** na účet úložiště **řídicí panel** v Azure portal nebo pro dotaz s použitím operace rozhraní API pro správu služby [získat účet úložiště Vlastnosti](/rest/api/storagerp/storageaccounts).

-   Načíst umístění, které je k dispozici pro zpracování tohoto účtu úložiště pomocí volání `Get Location` operace.

-   Pokud `AlternateLocations` vlastnost umístění obsahuje umístění, sama, pak je možné použít toto umístění. V opačném případě volat `Get Location` operaci znovu s některou z alternativních umístění. Původní umístění může být dočasně uzavřeno kvůli údržbě.

## <a name="creating-the-import-job"></a>Vytvoření úlohy importu
Chcete-li vytvořit úlohu importu, zavolejte [úlohy umístit](/rest/api/storageimportexport/jobs) operace. Budete muset zadat následující informace:

-   Název úlohy.

-   Název účtu úložiště.

-   Přesouvání název umístění, získaný v předchozím kroku.

-   Typ úlohy (Import).

-   Zpáteční adresu, které jednotky by měla být odeslána po dokončení úlohy importu.

-   Seznam jednotky v rámci úlohy. Pro každou jednotku musí obsahovat následující informace, se zjišťovala během kroku přípravy jednotky:

    -   Id jednotky

    -   Klíče Bitlockeru

    -   Relativní cesta k souboru manifestu na pevném disku

    -   Base16 kódovaný hash MD5 souboru manifestu

## <a name="shipping-your-drives"></a>Přesouvání jednotky
Je nutné dodat jednotky na adresu, kterou jste získali v předchozím kroku a službu Import/Export je nutné zadat pomocí sledovacího čísla balíčku.

> [!NOTE]
>  Je nutné dodat jednotky přes službu podporované operátora, který bude zajišťovat sledovací číslo pro svůj balíček.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Aktualizuje se úloha importu se vaše dodací informace
Jakmile budete mít sledovací číslo, zavolejte [aktualizovat vlastnosti úlohy](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Update) operace, která aktualizuje přenosů název operátora, sledovací číslo pro úlohu a číslo účtu dopravce pro vrácení. Volitelně můžete zadat počet jednotek a datem přesouvání.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Další postup

* [Pomocí rozhraní REST API služby Import/Export](storage-import-export-using-the-rest-api.md)
