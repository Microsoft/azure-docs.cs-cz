---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779942"
---
### <a name="event-system-properties-mapping"></a>Mapování vlastností systému událostí

Pokud jste v části **zdroj dat** v tabulce výše vybrali **Vlastnosti systému událostí** , pro správné vytvoření mapování spusťte příslušné příkazy KQL v [uživatelském rozhraní webu](https://dataexplorer.azure.com/) .

   **Pro mapování CSV:**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **Pro mapování JSON:**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * Do mapování je nutné zahrnout všechny vybrané vlastnosti. 
   > * Pořadí vlastností je důležité v mapování CSV. Vlastnosti systému musí být uvedeny před všemi ostatními vlastnostmi a ve stejném pořadí, v jakém jsou uvedeny v rozevíracím seznamu **Vlastnosti systému událostí** .