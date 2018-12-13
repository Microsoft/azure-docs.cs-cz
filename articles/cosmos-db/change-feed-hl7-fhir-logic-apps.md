---
title: Změna datového kanálu pro prostředky HL7 FHIR – Azure Cosmos DB
description: Zjistěte, jak nastavit oznamování změn pro pacienty zdravotní péče záznamy HL7 FHIR pomocí Azure Logic Apps služby Azure Cosmos DB a služby Service Bus.
keywords: hl7 fhir
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: sngun
ms.openlocfilehash: 5cc6bdfa9c16a6dfbdd0f6c87873a90b2a203169
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53089220"
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Upozornění pacientů změny zdravotní péče záznamu HL7 FHIR pomocí Logic Apps a Azure Cosmos DB

Azure MVP Howard Edidin byl nedávno kontaktovat zdravotní péče organizací, které chcete přidat nové funkce pro jejich portál pro pacienty. Jsou potřebné k odeslání oznámení pacientů, když v případě potřeby zapíná pacientů a tím moct přihlásit k odběru těchto aktualizací a jejich stavu záznam byl aktualizován. 

Tento článek vás provede změnu kanálu oznámení se řešení vytvořilo pro tento zdravotnické organizace pomocí služby Azure Cosmos DB, Logic Apps a služby Service Bus. 

## <a name="project-requirements"></a>Požadavky projektu
- Poskytovatelé poslat že hl7 konsolidované klinické architektura dokumentů (C-CDA) dokumenty ve formátu XML. Dokumenty C CDA zahrnovat téměř všechny typy klinické dokumentu, včetně klinické dokumenty, jako jsou záznamy očkování, stejně jako správce a řady historie pracovního postupu a finanční dokumenty. 
- C CDA dokumentům se převedou na [HL7 FHIR prostředky](https://hl7.org/fhir/2017Jan/resourcelist.html) ve formátu JSON.
- Upravené FHIR prostředku se odesílají prostřednictvím e-mailu ve formátu JSON.

## <a name="solution-workflow"></a>Pracovní postup řešení 

Na vysoké úrovni projektu vyžaduje následující kroky pracovního postupu: 
1. Převeďte dokumenty C CDA FHIR prostředky.
2. Proveďte opakovaný trigger cyklickém modifikované prostředky FHIR. 
2. Volání vlastní aplikaci FhirNotificationApi pro připojení k Azure Cosmos DB a dotazů pro nové nebo upravené dokumenty.
3. Uložte odpověď do fronty služby Service Bus.
4. Dotazování pro nové zprávy ve frontě služby Service Bus.
5. Odeslání e-mailová oznámení pro pacienty.

## <a name="solution-architecture"></a>Architektury řešení
Toto řešení vyžaduje tři Logic Apps splnění výš uvedených požadavků a dokončete pracovní postup řešení. Jsou tři aplikace logiky:
1. **Aplikace HL7 FHIR mapování**: obdrží HL7 C-CDA dokument, ho transformuje na prostředek FHIR a pak uloží jej do služby Azure Cosmos DB.
2. **Aplikace EHR**: dotazuje úložiště Azure Cosmos DB FHIR a uloží odpověď do fronty služby Service Bus. Tato aplikace logiky používá [aplikace API](#api-app) k načtení nové a změněné dokumenty.
3. **Proces oznámení aplikace**: odešle e-mailové oznámení s dokumenty FHIR prostředků v textu.

![Tři Logic Apps použité v řešení HL7 FHIR zdravotní péče](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Použité v řešení služby Azure

#### <a name="azure-cosmos-db-sql-api"></a>SQL API služby Azure Cosmos DB
Azure Cosmos DB je úložiště pro FHIR prostředky, jak je znázorněno na následujícím obrázku.

![Účet Azure Cosmos DB použité v tomto kurzu HL7 FHIR zdravotní péče](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Logic Apps zpracovávat procesu pracovního postupu. Na následujících snímcích obrazovky zobrazit aplikace logiky vytvořené pro toto řešení. 


1. **Aplikace HL7 FHIR mapování**: dokument HL7 C-CDA přijmout a transformovat ho na prostředek FHIR pomocí sady Enterprise Integration Pack pro Logic Apps. Enterprise Integration Pack zpracovává mapování C-CDA FHIR prostředky.

    ![Slouží k přijímání HL7 FHIR zdravotní záznamy aplikace logiky](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **Aplikace EHR**: dotazování úložiště Azure Cosmos DB FHIR a uložit odpověď do fronty služby Service Bus. Kód pro aplikace GetNewOrModifiedFHIRDocuments je níže.

    ![Aplikace logiky používá k dotazování služby Azure Cosmos DB](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Proces oznámení aplikace**: poslat e-mailové oznámení s dokumenty FHIR prostředků v textu.

    ![Aplikace logiky, která odešle e-mail o pacientech HL7 FHIR prostředku v textu](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
Následující obrázek znázorňuje pacientů fronty. Hodnota vlastnosti značka se používá pro předmět e-mailu.

![Fronty služby Service Bus použité v tomto kurzu HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>Aplikace API
Aplikace API připojí k Azure Cosmos DB a dotazy pro nové nebo upravené dokumenty FHIR podle typu prostředku. Tato aplikace nemá jeden řadič **FhirNotificationApi** pomocí jedné operace **GetNewOrModifiedFhirDocuments**, naleznete v tématu [zdroje pro aplikaci API](#api-app-source).

Používáme [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) třídy z rozhraní Azure Cosmos DB SQL .NET API. Další informace najdete v tématu [změnit informační kanál článku](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Operace GetNewOrModifiedFhirDocuments

**vstupy**
- ID databáze
- CollectionId
- Název typu prostředku HL7 FHIR
- Logická hodnota: Začít od začátku
- Int: Počet vrácených dokumentů

**Výstupy**
- Úspěchu: Stavový kód: 200, odpovědi: seznam dokumentů (pole JSON)
- Chyby: Kód stav: odpovědi 404,: "pro nenašly žádné dokumenty"*název prostředku "* typ prostředku"

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
                "from Last Run Date or Beginning of Collection creation"
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

Následující obrázek znázorňuje, jak se používá swagger k testování [FhirNotificationApi](#api-app-source).

![Soubor Swagger používá k testování aplikace API](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Řídicí panel Azure

Následující obrázek ukazuje všechny služby Azure pro toto řešení, které běží na webu Azure Portal.

![Na webu Azure portal zobrazující všechny služby použité v tomto kurzu HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Souhrn

- Jste se naučili, Azure Cosmos DB má nativní podpora pro oznámení pro nové nebo upravené dokumenty a jak snadné je používat. 
- S využitím Logic Apps, můžete vytvářet pracovní postupy bez psaní kódu.
- Ke zpracování distribuce pro dokumenty HL7 FHIR pomocí fronty Azure Service Bus.

## <a name="next-steps"></a>Další postup
Další informace o službě Azure Cosmos DB najdete v tématu [domovskou stránku služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Další informace o Logic Apps, najdete v části [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


