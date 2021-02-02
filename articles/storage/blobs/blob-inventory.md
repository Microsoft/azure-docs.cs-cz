---
title: Použití inventáře Azure Storage pro správu dat objektů BLOB (Preview)
description: Azure Storage Inventory je nástroj, který vám umožní získat přehled o všech datech objektů BLOB v rámci účtu úložiště.
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 12/03/2020
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: a41966c2b3ba73d7b68399b1b99d14313e220833
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257807"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Použití inventáře Azure Storage objektů BLOB pro správu dat objektů BLOB (Preview)

Funkce inventáře objektů blob Azure Storage poskytuje přehled o datech objektů BLOB v rámci účtu úložiště. Pomocí sestavy inventarizace můžete pochopit celkovou velikost dat, stáří, stav šifrování atd. Tato sestava poskytuje přehled vašich dat pro obchodní požadavky a požadavky na dodržování předpisů. Po povolení se sestava inventáře automaticky vytvoří denně.

## <a name="availability"></a>Dostupnost

Inventář objektů BLOB se podporuje pro účty pro obecné účely verze 2 (GPv2) i Premium Block BLOB Storage. Tato funkce je podporována s povolenou funkcí [hierarchického oboru názvů](data-lake-storage-namespace.md) nebo bez ní.

### <a name="preview-regions"></a>Oblasti verze Preview

Verze Preview inventáře objektů BLOB je dostupná na účtech úložiště v následujících oblastech:

- Francie – střed
- Střední Kanada
- Kanada – východ
- East US
- USA – východ 2

### <a name="pricing-and-billing"></a>Ceny a fakturace

Poplatek za sestavy inventáře není v období Preview účtován. Ceny se určí, když je tato funkce všeobecně dostupná.

## <a name="enable-inventory-reports"></a>Povolit sestavy inventáře

Povolte sestavy inventáře objektů BLOB přidáním zásady do svého účtu úložiště. Přidat, upravit nebo odebrat zásadu pomocí [Azure Portal](https://portal.azure.com/).

1. Přejít na [Azure Portal](https://portal.azure.com/)
1. Vyberte jeden z vašich účtů úložiště.
1. V části **BLOB Service** vyberte **inventář objektů BLOB** .
1. Ujistěte se, že je zaškrtnuté políčko **inventář objektů BLOB** .
1. Vyberte **Přidat pravidlo** .
1. Pojmenování nového pravidla
1. Vyberte **typy objektů BLOB** pro sestavu inventáře.
1. Přidání shody předpony k filtrování sestavy inventáře
1. Vyberte, jestli se mají **Zahrnout verze objektů BLOB** a **zahrnout snímky** do sestavy inventáře. Na účtu musí být povolené verze a snímky, aby se uložilo nové pravidlo s povolenou odpovídající možností.
1. Vyberte **Uložit**.

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Snímek obrazovky ukazující, jak přidat pravidlo inventáře objektů BLOB pomocí Azure Portal":::

Zásady inventáře jsou čteny nebo zapsány v plném rozsahu. Částečné aktualizace nejsou podporované.

> [!IMPORTANT]
> Pokud pro účet úložiště povolíte pravidla firewallu, může docházet k blokování požadavků na inventář. Tyto požadavky můžete odblokovat přidáním výjimek pro důvěryhodné služby Microsoftu. Další informace najdete v části výjimky v tématu [Konfigurace bran firewall a virtuálních sítí](../common/storage-network-security.md#exceptions).

Spuštění inventáře objektu BLOB je automaticky naplánováno každý den. Spuštění inventáře může trvat až 24 hodin. Sestavu inventáře nakonfigurujete tak, že přidáte zásadu inventáře s jedním nebo více pravidly.

## <a name="inventory-policy"></a>Zásady inventáře

Zásady inventarizace jsou kolekce pravidel v dokumentu JSON.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Kliknutím na kartu **zobrazení kódu** v části **inventář objektů BLOB** Azure Portal zobrazíte JSON pro zásady inventáře.

| Název parametru | Typ parametru        | Poznámky | Povinné? |
|----------------|-----------------------|-------|-----------|
| destination    | Řetězec                | Cílový kontejner, ve kterém budou všechny soubory inventáře vygenerovány. Cílový kontejner již musí existovat. | Ano |
| enabled        | Logická hodnota               | Používá se k zakázání celé zásady. Při nastavení na **hodnotu true** přepíše pole povolená úroveň pravidla tento parametr. V případě zakázání bude inventář všech pravidel zakázán. | Ano |
| pravidla          | Pole objektů pravidel | V zásadě je vyžadováno alespoň jedno pravidlo. Podporuje se až 10 pravidel. | Ano |

## <a name="inventory-rules"></a>Pravidla inventáře

Pravidlo zachytí podmínky filtrování a výstupní parametry pro generování sestavy inventáře. Každé pravidlo vytvoří sestavu inventáře. Pravidla mohou mít překrývající se předpony. Objekt BLOB se může ve více než jednom inventáři objevit v závislosti na definicích pravidel.

Každé pravidlo v zásadě má několik parametrů:

| Název parametru | Typ parametru                 | Poznámky | Povinné? |
|----------------|--------------------------------|-------|-----------|
| name           | Řetězec                         | Název pravidla může obsahovat až 256 alfanumerických znaků s rozlišením malých a velkých písmen. Název musí být v rámci zásady jedinečný. | Ano |
| enabled        | Logická hodnota                        | Příznak umožňující povolit nebo zakázat pravidlo. Výchozí hodnota je **true (pravda**). | Ano |
| definice     | Definice pravidla inventáře JSON | Každá definice se skládá ze sady filtrů pravidel. | Ano |

Příznak globálního **soupisu povoleného objektu BLOB** má přednost před *povoleným* parametrem v pravidle.

### <a name="rule-filters"></a>Filtry pravidla

K dispozici je několik filtrů pro přizpůsobení sestavy inventáře objektů BLOB:

| Název filtru         | Typ filtru                     | Poznámky | Povinné? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Pole předdefinovaných hodnot výčtu | Platné hodnoty jsou `blockBlob` a `appendBlob` pro hierarchické účty s povoleným oborem názvů, a, `blockBlob` `appendBlob` a `pageBlob` pro další účty. | Ano |
| prefixMatch         | Pole až deseti řetězců, pro které mají být předpony spárovány. Předpona musí začínat názvem kontejneru, například "container1/foo". | Pokud nedefinujete *prefixMatch* nebo neposkytnete prázdnou předponu, pravidlo se vztahuje na všechny objekty BLOB v účtu úložiště. | Ne |
| includeSnapshots    | Logická hodnota                         | Určuje, zda by měl inventář zahrnovat snímky. Výchozí hodnota je **false**. | Ne |
| includeBlobVersions | Logická hodnota                         | Určuje, jestli by měl inventář obsahovat verze objektů BLOB. Výchozí hodnota je **false**. | Ne |

Kliknutím na kartu **zobrazení kódu** v části **inventář objektů BLOB** Azure Portal zobrazte pravidla pro inventarizaci. Filtry jsou zadané v definici pravidla.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>Výstup inventáře

Každé spuštění inventarizace vygeneruje sadu souborů ve formátu CSV v zadaném kontejneru cíle inventarizace. Výstup inventáře se vygeneruje v následující cestě: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` kde:

- název *účtu BLOB Storage* účtu Azure
- *inventář – cílový* kontejner je cílový kontejner, který jste zadali v zásadách inventarizace.
- *RRRR/MM/DD/hh-mm-SS* je čas, kdy se začalo spustit inventarizace.

### <a name="inventory-files"></a>Soubory inventáře

Každé spuštění inventarizace generuje následující soubory:

- **Soubor CSV pro inventář**: soubor hodnot oddělených čárkami (CSV) pro každé pravidlo inventáře. Každý soubor obsahuje odpovídající objekty a jejich metadata. První řádek v každém souboru ve formátu CSV je vždy řádek schématu. Následující obrázek ukazuje soubor CSV ve formátu inventáře otevřený v aplikaci Microsoft Excel.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Snímek obrazovky se souborem CSV inventáře otevřeným v aplikaci Microsoft Excel":::

- **Soubor manifestu**: manifest.jsv souboru, který obsahuje podrobnosti o souborech inventáře generovaných pro každé pravidlo v daném spuštění. Soubor manifestu také zachycuje definici pravidla poskytnutou uživatelem a cestu k inventáři pro toto pravidlo.

- **Soubor kontrolního součtu**: soubor manifest. Checksum obsahující kontrolní součet MD5 obsahu manifest.jsv souboru. Generování souboru manifest. Checksum označuje dokončení spuštění inventáře.

## <a name="inventory-completed-event"></a>Událost dokončeného inventáře

Přihlaste se k odběru události dokončeno inventarizaci, která vám pošle oznámení po dokončení spuštění inventáře. Tato událost je generována při vytvoření souboru kontrolního součtu manifestu. Událost dokončeno inventarizace se také vyskytne v případě, že při spuštění inventáře dojde k chybě uživatele předtím, než začne běžet. Událost může aktivovat například neplatná zásada nebo cílový kontejner, který není přítomný. Událost je publikována v tématu inventáře objektů BLOB.

Událost vzorku:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>Další kroky

[Správa životního cyklu Azure Blob Storage](storage-lifecycle-management-concepts.md)
