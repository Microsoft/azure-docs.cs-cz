---
title: Vytvořte export úlohy pro Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit úlohu exportu pro službu Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 0066ec20c68d70fac9a6529f8f5b85855d6c5349
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666820"
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Vytvoření úlohy exportu pro službu Azure Import/Export
Vytvoření úlohy exportu pro službu Microsoft Azure Import/Export pomocí rozhraní REST API zahrnuje následující kroky:

-   Výběr objektů BLOB k exportu.

-   Získání umístění přesouvání.

-   Vytvoření úlohy exportu.

-   Přesouvání prázdné jednotky společnosti Microsoft prostřednictvím služby podporované operátora.

-   Aktualizuje se úloha exportu s informací o balíčku.

-   Přijetí jednotky zpět od Microsoftu.

 V tématu [pomocí služby Windows Azure Import/Export pro přenos dat do úložiště objektů Blob](storage-import-export-service.md) Přehled služby Import/Export a kurz, který ukazuje, jak používat [webu Azure portal](https://portal.azure.com/) k vytvoření a Správa import a export úloh.

## <a name="selecting-blobs-to-export"></a>Vyberte objekty BLOB k exportu
 Chcete-li vytvořit úlohu exportu, musíte poskytnout seznam objektů BLOB, které chcete exportovat z vašeho účtu úložiště. Existuje několik způsobů, jak vybrat objekty BLOB export:

-   Cesta relativní objektů blob můžete vybrat jeden objekt blob a všechny její snímky.

-   Cesta relativní objektů blob můžete vybrat jeden objekt blob, s výjimkou její snímky.

-   Cesta relativní objektů blob a čas snímku můžete vybrat jeden snímek.

-   Předpony objektů blob můžete vybrat všechny objekty BLOB a snímky s danou předponu.

-   Můžete exportovat všechny objekty BLOB a snímky v účtu úložiště.

 Další informace o zadávání objekty BLOB k exportu, najdete v článku [úlohy umístit](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operace.

## <a name="obtaining-your-shipping-location"></a>Získání vaše dodací umístění
Před vytvoření úlohy exportu, je potřeba získat přenosů název umístění a adresa voláním [získat umístění](https://portal.azure.com) nebo [seznamu umístění](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) operace. `List Locations` Vrátí seznam umístění a jejich poštovních adres. Můžete vybrat umístění ze seznamu vrácených a dodávat pevných disků na tuto adresu. Můžete také použít `Get Location` operaci získat přímo dodací adresu pro konkrétní lokalitu.

Podle následujících pokynů k získání umístění dopravě:

-   Určete název umístění účtu úložiště. Tuto hodnotu najdete v části **umístění** na účet úložiště **řídicí panel** v Azure portal nebo pro dotaz s použitím operace rozhraní API pro správu služby [získat účet úložiště Vlastnosti](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Načíst umístění, které jsou k dispozici pro zpracování tohoto účtu úložiště pomocí volání `Get Location` operace.

-   Pokud `AlternateLocations` vlastnost umístění obsahuje umístění, sama, pak je možné použít toto umístění. V opačném případě volat `Get Location` operaci znovu s některou z alternativních umístění. Původní umístění může být dočasně uzavřeno kvůli údržbě.

## <a name="creating-the-export-job"></a>Vytvoření úlohy exportu
 Chcete-li vytvořit úlohu exportu, zavolejte [úlohy umístit](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operace. Budete muset zadat následující informace:

-   Název úlohy.

-   Název účtu úložiště.

-   Přesouvání název umístění, kterou jste získali v předchozím kroku.

-   Typ úlohy (Export).

-   Zpáteční adresu, které jednotky by měla být odeslána po dokončení úlohy exportu.

-   Seznam objektů BLOB (nebo předpony objektů blob), nelze exportovat.

## <a name="shipping-your-drives"></a>Přesouvání jednotky
 Pak pomocí nástroje Import/Export Azure určit požadovaný počet jednotek, které potřebujete k odesílání, na základě vybraných nelze exportovat objekty BLOB a velikost jednotky. Zobrazit [referenčních informacích k Azure Import/Export nástroj](storage-import-export-tool-how-to-v1.md) podrobnosti.

 Balíček jednotek v jednom balíčku a jejich odeslání na adresu, kterou jste získali v dřívějším kroku. Poznamenejte si číslo pro sledování balíčku na další krok.

> [!NOTE]
>  Je nutné dodat jednotky přes službu podporované operátora, který bude zajišťovat sledovací číslo pro svůj balíček.

## <a name="updating-the-export-job-with-your-package-information"></a>Aktualizuje se úloha exportu s informacemi o balíčku
 Jakmile budete mít sledovací číslo, zavolejte [aktualizovat vlastnosti úlohy](/rest/api/storageimportexport/jobs#Jobs_Update) operace aktualizovat název operátora a sledovací číslo pro úlohu. Volitelně můžete zadat počet jednotek, zpáteční adresu a datem přesouvání.

## <a name="receiving-the-package"></a>Příjem balíček
 Po zpracování úlohy exportu jednotky vrátí se vám s šifrovaná data. Klíč Bitlockeru pro každou z jednotky můžete načíst pomocí volání [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) operace. Potom můžete odemknout jednotku s použitím klíče. Soubor manifestu jednotky na každém disku obsahuje seznam souborů na jednotce, stejně jako původní adresa objektu blob pro každý soubor.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Další postup

* [Pomocí rozhraní REST API služby Import/Export](storage-import-export-using-the-rest-api.md)
