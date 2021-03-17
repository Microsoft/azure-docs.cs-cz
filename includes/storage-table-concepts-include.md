---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 91a52e7eac40c0ac2ab682f251a2ae0013259b25
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "77013695"
---
## <a name="what-is-table-storage"></a>Co je služba Table Storage
Služba Azure Table Storage ukládá velké objemy strukturovaných dat. Služba je úložištěm dat typu NoSQL, které přijímá ověřená volání z cloudu Azure i z prostředí mimo něj. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat. Mezi běžná použití služby Table Storage patří:

* Ukládání terabajtů strukturovaných dat, která můžou obsluhovat škálované webové aplikace
* Ukládání datových sad, které nevyžadují komplexní spojení, cizí klíče nebo uložené postupy a které můžete denormalizovat kvůli rychlému přístupu
* Rychle dotazování na data pomocí clusterovaného indexu
* Přístup k datům pomocí protokolu OData a dotazů LINQ s knihovnami .NET datové služby WCF

Službu Table Storage můžete použít k ukládání a dotazování obrovských sad strukturovaných, nerelačních dat a vaše tabulky se budou s rostoucími požadavky škálovat.

## <a name="table-storage-concepts"></a>Koncepty služby Table Storage
Služba Table Storage obsahuje následující komponenty:

![Diagram komponent služby Table Storage][Table1]

* **Formát URL:** Účty Azure Table Storage používají tento formát: `http://<storage account>.table.core.windows.net/<table>`

  Účty rozhraní API pro tabulky Azure Cosmos DB používají tento formát: `http://<storage account>.table.cosmosdb.azure.com/<table>`  

  Tabulky Azure můžete adresovat přímo pomocí této adresy s protokolem OData. Další informace najdete v tématu [OData.org][OData.org].
* **Účty:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Další informace o účtech úložiště najdete v tématu [Přehled účtu úložiště](../articles/storage/common/storage-account-overview.md).

    Veškerý přístup ke službě Azure Cosmos DB se provádí prostřednictvím účtu rozhraní API pro tabulky. Podrobnosti o vytvoření účtu rozhraní API pro tabulky najdete v tématu věnovaném [Vytvoření účtu rozhraní API pro tabulky](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account).
* **Tabulka**: Tabulka je kolekcí entit. Tabulky nevynucují u entit schéma, což znamená, že jedna tabulka může obsahovat entity s různými sadami vlastností.  
* **Entita**: Entita je sada vlastností, která se podobá řádku databáze. Entita ve službě Azure Storage může mít velikost až 1 MB. Entita ve službě Azure Cosmos DB může mít velikost až 2 MB.
* **Vlastnosti**: Vlastnost je pár název-hodnota. Každá entita může obsahovat až 252 vlastností pro ukládání dat. Každá entita má také tři systémové vlastnosti, které určují klíč oddílu, klíč řádku a časové razítko. Entity se stejným klíčem oddílu můžete dotazovat rychleji a můžete je vkládat nebo aktualizovat v atomických operacích. Klíč řádku entity je jedinečným identifikátorem v rámci oddílu.

Podrobnosti o pojmenovávání tabulek a vlastnostech najdete v článku [Principy datového modelu služby Table service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
