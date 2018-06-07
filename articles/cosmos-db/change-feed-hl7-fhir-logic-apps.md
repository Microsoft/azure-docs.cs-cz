---
title: Změna kanálu pro prostředky HL7 FHIR - Azure Cosmos DB | Microsoft Docs
description: Zjistěte, jak nastavit oznámení o změnách pro záznamy HL7 FHIR pacientů zdravotní péče pomocí Azure Logic Apps, Azure Cosmos DB a služby Service Bus.
keywords: hl7 fhir
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: sngun
ms.openlocfilehash: 9d05c41e7ebf9d1cc0735da8853e4ad1617eb810
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610493"
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Upozornění pacientů HL7 FHIR zdravotní péče záznam změn pomocí Logic Apps a Azure Cosmos DB

Azure MVP Howard Edidin byla nedávno kontaktovat zdravotní péče organizací, které chcete přidat nové funkce na svých pacientech portál. Jsou potřeba k odesílání oznámení do pacientů, když byla aktualizována jejich stavu záznamů a jejich potřeby pacientů moct přihlásit k odběru těchto aktualizací. 

Tento článek vás provede změnu kanálu oznámení řešení vytvořené pro tuto zdravotní péče organizaci, která používá databázi Cosmos Azure Logic Apps a Service Bus. 

## <a name="project-requirements"></a>Požadavky na projektu
- Zprostředkovatelé send že hl7 konsolidované klinické dokumentu architektura (C-CDA) dokumenty ve formátu XML. C – CDA dokumenty zahrnovat téměř každý typ klinické dokumentu, včetně klinické dokumenty jako rodiny historie a záznamy o očkování, stejně jako správce, pracovní postup a finanční dokumenty. 
- C – CDA dokumenty jsou převedeny na [HL7 FHIR prostředky](http://hl7.org/fhir/2017Jan/resourcelist.html) ve formátu JSON.
- Upravené dokumenty prostředků FHIR odesílá e-mailu ve formátu JSON.

## <a name="solution-workflow"></a>Pracovní postup řešení 

Na vysoké úrovni projekt vyžaduje následující kroky pracovního postupu: 
1. Převeďte C-CDA dokumenty FHIR prostředky.
2. Proveďte opakované aktivační událost dotazování pro upravené FHIR prostředky. 
2. Volání vlastní aplikaci, FhirNotificationApi pro připojení k databázi Cosmos Azure a dotazů pro nové nebo upravené dokumenty.
3. Ukládání odpovědi do fronty na Service Bus.
4. Dotazování pro nové zprávy ve frontě Service Bus.
5. Pacientům odešlete e-mailová oznámení.

## <a name="solution-architecture"></a>Architektura řešení
Toto řešení vyžaduje tři Logic Apps, abyste splňují výše uvedené požadavky a dokončení pracovního postupu řešení. Tři aplikace logiky jsou:
1. **Aplikace HL7. FHIR mapování**: obdrží HL7 C-CDA dokumentu, transformuje FHIR prostředek a pak uloží do databáze Azure Cosmos.
2. **Aplikace EHR**: dotazuje úložiště Azure Cosmos DB FHIR a uloží odpovědi do fronty Service Bus. Tato aplikace logiky používá [aplikace API](#api-app) načíst nové a změněné dokumenty.
3. **Proces oznámení aplikace**: odešle e-mail s oznámením s dokumenty prostředků FHIR v textu.

![Tři Logic Apps použité v řešení zdravotní péče HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Použít v řešení služby Azure

#### <a name="azure-cosmos-db-sql-api"></a>Rozhraní API pro Azure Cosmos databáze SQL
Azure Cosmos DB slouží jako úložiště pro prostředky FHIR, jak je znázorněno na následujícím obrázku.

![Účet Azure Cosmos DB použitý v tomto kurzu HL7 FHIR zdravotní péče](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Aplikace logiky zpracování procesu pracovního postupu. Na následujících snímcích obrazovky zobrazit Logic apps vytvořené pro toto řešení. 


1. **Aplikace HL7. FHIR mapování**: dokument HL7 C-CDA přijmout a transformují je na prostředek FHIR pomocí Enterprise integrační balíček pro Logic Apps. Integrační balíček Enterprise zpracovává mapování z C-CDA na FHIR prostředky.

    ![Aplikace logiky používá k přijetí HL7 FHIR zdravotní péče záznamů](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **Aplikace EHR**: dotazování úložiště Azure Cosmos DB FHIR a ukládání odpovědi do fronty Service Bus. Kód pro aplikace GetNewOrModifiedFHIRDocuments je níže.

    ![Aplikace logiky používá k dotazování databáze Cosmos Azure](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Proces oznámení aplikace**: Odeslat oznámení e-mailu s dokumenty prostředků FHIR v textu.

    ![Aplikace logiky, který odešle pacienta e-mailu pomocí prostředků HL7 FHIR v textu](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
Následující obrázek znázorňuje pacienty fronty. Hodnota vlastnosti značky se používá pro předmět e-mailu.

![Frontou Service Bus používá v tomto kurzu HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>Aplikace API
Aplikace API připojí k databázi Cosmos Azure a dotazů pro nové nebo upravené dokumenty FHIR podle typů prostředků. Tato aplikace má jeden řadič **FhirNotificationApi** s jednu operaci **GetNewOrModifiedFhirDocuments**, najdete v části [zdroje pro aplikaci API](#api-app-source).

Používáme [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) třídy z Azure SQL DB Cosmos .NET rozhraní API. Další informace najdete v tématu [změnu kanálu článku](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Operace GetNewOrModifiedFhirDocuments

**Vstupy**
- Hodnotu DatabaseId
- CollectionId
- Název typu prostředku FHIR HL7
- Logická hodnota: Začít od začátku
- Int: Počet vrácených dokumentů

**Výstupy**
- Úspěch: Stavový kód: odpovědi 200,: seznam dokumentů (pole JSON)
- Chyba: Stavový kód: odpovědi 404,: "nalezeny žádné dokumenty pro '*název prostředku '* typ prostředku"

<a id="api-app-source"></a>

**Zdroj pro aplikace API**

```csharp

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Testování FhirNotificationApi 

Následující obrázek ukazuje, jak byl swagger použit k testování [FhirNotificationApi](#api-app-source).

![Soubor Swagger používá k testování aplikace API](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Řídicí panel portálu Azure

Následující obrázek znázorňuje všechny služby Azure pro toto řešení systémem na portálu Azure.

![Portálu Azure znázorňující všechny služby použili v tomto kurzu HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Souhrn

- Jste zjistili, jestli má Azure Cosmos DB nativní podpora pro oznámení o nových nebo upravené dokumenty a jak snadné je použít. 
- S využitím Logic Apps, můžete vytvořit pracovní postupy bez psaní jakéhokoli kódu.
- Pomocí fronty služby Service Bus Azure pro zpracování distribuce pro HL7 FHIR dokumenty.

## <a name="next-steps"></a>Další postup
Další informace o databázi Cosmos Azure najdete v tématu [domovské stránky Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Více informací o Logic Apps, naleznete v části [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


