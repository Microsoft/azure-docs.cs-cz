---
title: Vytvoření registru schématu Azure Event Hubs
description: V tomto článku se dozvíte, jak vytvořit Registry schématu v oboru názvů Azure Event Hubs.
ms.topic: how-to
ms.date: 12/03/2020
ms.custom: references_regions
ms.openlocfilehash: 45791cd69772be97ca6768184ed17179e04ad9dc
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576804"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>Vytvoření registru schématu Azure Event Hubs (Preview)
V tomto článku se dozvíte, jak vytvořit skupinu schémat se schématy v registru schématu hostovaném v Azure Event Hubs. Přehled funkce registru schématu v Azure Event Hubs najdete v tématu [Azure Schema Registry v Event Hubs](schema-registry-overview.md).

> [!NOTE]
> - Funkce **registru schématu** je momentálně ve **verzi Preview** a nedoporučuje se pro produkční úlohy.
> - Tato funkce je k dispozici pouze ve **standardních** a **vyhrazených** vrstvách, nikoli na úrovni **Basic** .
> - Pokud je centrum událostí ve **virtuální síti**, nebudete moct v Azure Portal vytvářet schémata, pokud nebudete mít přístup k portálu z virtuálního počítače ve stejné virtuální síti. 

## <a name="prerequisites"></a>Předpoklady
[Vytvořte obor názvů Event Hubs](event-hubs-create.md#create-an-event-hubs-namespace). Můžete použít také existující obor názvů. 

## <a name="create-a-schema-group"></a>Vytvoření skupiny schémat
1. Přejděte na stránku **obor názvů Event Hubs** . 
1. V nabídce vlevo vyberte možnost **registr schématu** . Skupinu schémat vytvoříte tak, že na panelu nástrojů vyberete **+ Skupina schémat** . 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="Stránka registru schématu":::
1. Na stránce **vytvořit skupinu schémat** proveďte tyto kroky:
    1. Zadejte **název** skupiny schémat.
    1. Jako **typ serializace** vyberte formát serializace, který se vztahuje na všechna schémata ve skupině schémat. V současné době je **Avro** jediným podporovaným typem, takže vyberte **Avro**. 
    1. Vyberte **režim kompatibility** pro všechna schémata ve skupině. Pro **Avro** jsou podporovány režimy dopředné a zpětné kompatibility. 
    1. Pak vyberte **vytvořit** a vytvořte skupinu schémat. 
1. V seznamu skupin schémat vyberte název **skupiny schémat** .

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="V seznamu vyberte skupinu schémat.":::    
1. Pro skupinu se zobrazí stránka **Skupina schémat** .

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="Stránka skupiny schémat":::
    

## <a name="add-a-schema-to-the-schema-group"></a>Přidání schématu do skupiny schémat
V této části přidáte schéma do skupiny schémat pomocí Azure Portal. 

1. Na stránce **Skupina schémat** vyberte v panelu nástrojů možnost **+ schéma** . 
1. Na stránce **vytvořit schéma** proveďte tyto kroky:
    1. Zadejte **název** schématu.
    1. Do textového pole zadejte následující **schéma** . Můžete také vybrat soubor se schématem.
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. Vyberte **Vytvořit**. 
1. Vyberte **schéma** ze seznamu schémat. 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="Vybrat schéma":::
1. Zobrazí se stránka **Přehled schématu** schématu. 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="Stránka Přehled schématu":::    
1. Pokud existuje více verzí schématu, zobrazí se v rozevíracím seznamu **verze** . Vyberte verzi, kterou chcete přepnout na schéma verze. 

## <a name="create-a-new-version-of-schema"></a>Vytvořit novou verzi schématu

1. Aktualizujte schéma v textovém poli a vyberte **ověřit**. V následujícím příkladu bylo `id` do schématu přidáno nové pole. 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="Aktualizovat schéma":::    
    
1. Zkontrolujte stav ověřování a změny a vyberte **Uložit**. 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="Kontrola stavu ověření, změn a uložení":::     
1. `2`Na stránce **Přehled schématu** vidíte, že je vybrána možnost **verze** . 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="Nová verze schématu":::    
1. Výběrem `1` zobrazíte verzi 1 ve schématu. 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="Vybrat verzi":::    


## <a name="next-steps"></a>Další kroky
Další informace o registru schématu najdete v tématu [Registry schématu Azure v Event Hubs](schema-registry-overview.md).

