---
title: Vytvoření nebo úprava nabídky – Azure Marketplace
description: Rozhraní API k vytvoření nové nebo aktualizace stávající nabídky.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87420222"
---
# <a name="create-or-modify-an-offer"></a>Vytvoření nebo úprava nabídky

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Toto volání aktualizuje konkrétní nabídku v rámci oboru názvů vydavatele nebo vytvoří novou nabídku.

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identifikátoru URI

|  **Název**         |  **Popis**                      |  **Datový typ**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  Identifikátor vydavatele, například `contoso` |   Řetězec |
| Hodnotami OfferId           |  Identifikátor nabídky                     |   Řetězec        |
| verze-api       |  Nejnovější verze rozhraní API            |   Date (Datum)           |
|  |  |  |

## <a name="header"></a>Hlavička

|  **Název**        |  **Hodnota**               |
|  ---------       |  ----------              | 
| Typ obsahu     | `application/json`       |
| Autorizace    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>Příklad textu

Následující příklad vytvoří nabídku s hodnotami OfferId `contosovirtualmachine` .

### <a name="request"></a>Žádost

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>Odpověď

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> Pokud chcete tuto nabídku upravit, přidejte do výše uvedené žádosti záhlaví **If-Match** nastavené na *. Použijte stejný text žádosti, jak je uvedeno výše, ale upravte hodnoty podle potřeby. 

### <a name="response-status-codes"></a>Stavové kódy odpovědí

| **Kód**  |  **Popis**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. Požadavek byl úspěšně zpracován a nabídka byla úspěšně změněna.           |
|  201      | `Created`. Požadavek byl úspěšně zpracován a nabídka byla úspěšně vytvořena.   |
|  400      | `Bad/Malformed request`. Tělo odpovědi na chyby může poskytnout další informace.            |
|  403      | `Forbidden`. Klient nemá přístup k požadovanému oboru názvů.                     |
|  404      | `Not found`. Entita, na kterou odkazuje klient, neexistuje.                           |
|  412      | Server nesplňuje jednu z podmínek, které žadatel zadal v žádosti. Klient by měl kontrolovat ETAG odeslaný s požadavkem. |
|  |  |

## <a name="uploading-artifacts"></a>Nahrávají se artefakty.

Artefakty, jako jsou obrázky a loga, byste měli sdílet tak, že je nahrajete do přístupného umístění na webu, a pak každý jako identifikátor URI v žádosti PUT, jako v předchozím příkladu. Systém zjistí, že tyto soubory nejsou k dispozici ve Azure Marketplacem úložišti, a tyto soubory Stáhněte do úložiště.  V důsledku toho zjistíte, že budoucí požadavky GET vrátí adresu URL služby Azure Marketplace pro tyto soubory.

## <a name="categories-and-industries"></a>Kategorie a odvětví

Při vytváření nové nabídky musíte na webu Marketplace zadat kategorii pro vaši nabídku. Volitelně můžete pro některé typy nabídek zadat také obory. Na základě typu nabídky poskytněte kategorie/obory, které se vztahují na nabídku, pomocí specifických klíčových hodnot z následujících tabulek.

### <a name="azure-marketplace-categories"></a>Azure Marketplace kategorie

Tyto kategorie a jejich příslušné klíče platí pro aplikace Azure, Virtual Machines, základní Virtual Machines, kontejnery, aplikace kontejneru, IoT Edge moduly a typy nabídky SaaS. Položky tučnou kurzívou (jako je ***Analytics***) jsou kategorie a standardní textové položky (jako data-přehledy) jsou podkategoriemi pod nimi. Používejte přesné hodnoty klíčů bez změny mezer nebo velkých a malých písmen.

| Kategorie | SaaS klíče | Klíče aplikací Azure | Virtuální počítač, kontejnery, aplikace kontejneru, IoT Edge modul, klíče základního virtuálního počítače |
| --- | --- | --- | --- |
| ***Analýza** _ | _*_Analytics_*_ | _*_analýzy – Azure – aplikace_*_ | _ *_Analytics – amp_** |
| Přehledy dat | data – přehledy | data – přehledy | data – přehledy |
| Analýza dat | data – analýza | data – analýza | data – analýza |
| Velký objem dat | velké objemy dat | bigData | velké objemy dat |
| Prediktivní analýza | prediktivní analýza | prediktivní analýza | prediktivní analýza |
| Analýzy v reálném čase/streamování | streamování v reálném čase – analýza | streamování v reálném čase – analýza | streamování v reálném čase – analýza |
| Jiné | other | jiné – analýza | other |
| ***AI + Machine Learning** _ | _*_ArtificialIntelligence_*_ | _*_AI – plus – Machine – učení_*_ | _ *_AI a strojové učení_** |
| Služby robota | Robot – služby | Robot – služby | Robot – služby |
| Cognitive Services | rozpoznávání – služby | rozpoznávání – služby | rozpoznávání – služby |
| Služba ML | ml – služba | ml – služba | ml – služba |
| Automatizované strojové učení | automatizované – ml | automatizované – ml | automatizované – ml |
| Automatizace obchodních procesů | Business-robot-Process – automatizace | Business-robot-Process – automatizace | Business-robot-Process – automatizace |
| Označování dat | označování dat | označování dat | označování dat |
| Příprava dat | Příprava dat | Příprava dat | Příprava dat |
| Dolování znalostí | znalostní báze knowledgeing – dolování | znalostní báze knowledgeing – dolování | znalostní báze knowledgeing – dolování |
| Operace ML | ml – operace | ml – operace | ml – operace |
| Jiné | ostatní – AI a strojové učení | other | other |
| ***Blockchain** _ | _*_Blockchain_*_ | _*_Blockchain_*_ | _ *_blockchain_** |
| Akcelerátory aplikací | akcelerátory aplikací | akcelerátory aplikací | akcelerátory aplikací |
| Hlavní kniha s jedním uzlem | jeden uzel – hlavní kniha | jeden uzel – hlavní kniha | jeden uzel – hlavní kniha |
| Hlavní kniha s více uzly | více uzlů – hlavní kniha | více uzlů – hlavní kniha | více uzlů – hlavní kniha |
| nástroje | tools | tools | tools |
| Jiné | other | other | other |
| ***COMPUTE** _ | _*_COMPUTE – SaaS_*_ | _*_COMPUTE – Azure – aplikace_*_ | _ *_COMPUTE_** |
| Aplikační infrastruktura | appInfra | appInfrastructure | aplikační infrastruktura |
| Operační systémy | clientOS | clientOS | operační systémy |
| Mezipaměť | cache | cache | cache |
| Jiné | jiné – COMPUTE | jiné – COMPUTE | other |
| ***Kontejnery** _ | _*_kontejnery_*_ | _*_kontejnery_*_ | _ *_kontejnery_** |
| Aplikace kontejneru | kontejner – aplikace | kontejner – aplikace | kontejner – aplikace |
| Image kontejneru | kontejner – image | kontejner – image | kontejner – image |
| Začínáme s kontejnery | Začínáme s kontejnery | Začínáme s kontejnery | Začínáme s kontejnery |
| Jiné | other | other | other |
| ***Databáze** _ | _*_databáze – SaaS_*_ | _*_databáze_*_ | _ *_databáze_** |
| Databáze NoSQL | NoSQL – databáze | NoSQL – databáze | NoSQL – databáze |
| Relační databáze | relační databáze | relační databáze | relační databáze |
| Hlavní kniha a databáze blockchain | Hlavní kniha – blockchain – databáze | Hlavní kniha – blockchain – databáze | Hlavní kniha – blockchain – databáze |
| Datová jezera | data – jezera | data – jezera | data – jezera |
| Datový sklad | datový sklad | datový sklad | datový sklad |
| Jiné | ostatní databáze | ostatní databáze | other |
| ***Vývojářské nástroje** _ | _*_Vývojářské nástroje – SaaS_*_ | _*_Developer – nástroje – Azure – aplikace_*_ | _ *_– Nástroje pro vývojáře_** |
| nástroje | nástroje – Developer – Tools | nástroje – Developer – Tools | nástroje – Developer – Tools |
| Skripty | skripty | skripty | skripty |
| Služba pro vývojáře | devService | devService | Vývojář – služba |
| Jiné | ostatní – Developer Tools | ostatní – Developer Tools | other |
| ***DevOps** _ | _*_DevOps_*_ | _*_DevOps_*_ | _ *_DevOps_** |
| Jiné | other | other | other |
| ***Identita** _ | _*_odcizen_*_ | _*_odcizen_*_ | _ –*_Identita_** |
| Správa přístupu | Správa přístupu | Správa přístupu | Správa přístupu |
| Jiné | other | other | other |
| ***Integrace** _ | _*_spolupráci_*_ | _*_spolupráci_*_ | _ *_Integration_** |
| Zasílání zpráv | zprávy | zprávy | zprávy |
| Jiné | other | other | other |
| ***Internet věcí** _ | _*_IoT_*_ | _*_Internet věcí – Azure-Apps_*_ | _ *_Internet věcí_** |
| Služby IoT Core | – | IoT – Core – služby | IoT – Core – služby |
| Moduly IoT Edge | – | IoT – Edge – moduly | IoT – Edge – moduly |
| Řešení IoT | IoT – řešení | IoT – řešení | IoT – řešení |
| Vizualizace & analýzy dat | data-analýza a vizualizace | data-analýza a vizualizace | data-analýza a vizualizace |
| Připojení IoT | IoT – konektivita | IoT – konektivita | IoT – konektivita |
| Jiné | ostatní – Internet věcí | ostatní – Internet věcí | other |
| ***Nástroje pro správu IT &** _ | _*_ITandAdministration_*_ | _*_IT a Správa – nástroje – Azure-Apps_*_ | _ *_IT a-Management-Tools_** |
| Řešení pro správu | Správa – řešení | Správa – řešení | Správa – řešení |
| Podnikové aplikace | businessApplication | businessApplication | obchodní aplikace |
| Jiné | Správa jiných IT nástrojů | Správa jiných IT nástrojů | other |
| ***Monitorování diagnostiky &** _ | _*_monitorování a diagnostika_*_ | _*_monitorování a diagnostika_*_ | _ *_monitorování a diagnostika_** |
| Jiné | other | other | other |
| ***Médium** _ | _*_média_*_ | _*_média_*_ | _ *_média_** |
| Media Services | media-services | media-services | media-services |
| Content Protection | Ochrana obsahu | Ochrana obsahu | Ochrana obsahu |
| Live & streamování na vyžádání | Živé streamování a streamování na vyžádání | Živé streamování a streamování na vyžádání | Živé streamování a streamování na vyžádání |
| Jiné | other | other | other |
| ***Migrace** _ | _*_migrace_*_ | _*_migrace_*_ | *_migrace_ _** |
| Migrace dat | migrace dat | migrace dat | migrace dat |
| Jiné | other | other | other |
| ***Mixed reality** _ | _*_Mixed – realita_*_ | _*_Mixed – realita_*_ | _ *_Mixed – realita_** |
| Jiné | other | other | other |
| ***Síťové služby** _ | _*_sítě_*_ | _*_sítě_*_ | _ *_síťové služby_** |
| Správci zařízení | zařízení – správci | zařízení – správci | zařízení – správci |
| Připojení | připojení | připojení | připojení |
| brány firewall, | brány firewall | brány firewall | brány firewall |
| Nástroje pro vyrovnávání zatížení | nástroje pro vyrovnávání zatížení | nástroje pro vyrovnávání zatížení | nástroje pro vyrovnávání zatížení |
| Jiné | other | other | other |
| ***Zabezpečení** _ | _*_bezpečnost_*_ | _*_bezpečnost_*_ | _ *_zabezpečení_** |
| Správa identit & přístupu | Správa identit a přístupu | Správa identit a přístupu | Správa identit a přístupu |
| Ochrana před hrozbami | Ochrana před hrozbami | Ochrana před hrozbami | Ochrana před hrozbami |
| Information Protection | Ochrana informací | Ochrana informací | Ochrana informací |
| Jiné | other | other | other |
| ***Úložiště** _ | _*_úložiště – SaaS_*_ | _*_Storage – Azure – aplikace_*_ | _ *_Storage_** |
| Zálohování & obnovení | zálohování | zálohování | zálohování a obnovení |
| Hybridní úložiště pro podniky | podnik – hybridní úložiště | podnik – hybridní úložiště | podnik – hybridní úložiště |
| Sdílení souborů | sdílení souborů | sdílení souborů | sdílení souborů |
| Správa životního cyklu dat | data – Správa životního cyklu | data – Správa životního cyklu | data – Správa životního cyklu |
| Jiné | jiné úložiště | jiné úložiště | other |
| ***Web** _ | _*_webovém_*_ | _*_webovém_*_ | _ *_Web_** |
| Blogy & CMS | Blogy – a – CMS | Blogy – a – CMS | Blogy – a – CMS |
| Úvodní Web Apps | úvodní web – aplikace | úvodní web – aplikace | úvodní web – aplikace |
| Elektronického obchodování | elektronického obchodování | elektronického obchodování | elektronického obchodování |
| Web Apps architektury | webové aplikace – architektury | webové aplikace – architektury | webové aplikace – architektury |
| Web Apps | webové aplikace | webové aplikace | webové aplikace |
| Jiné | other | other | other |
||||

### <a name="microsoft-appsource-categories"></a>Microsoft AppSource kategorie

Tyto kategorie a jejich příslušné klíče platí pro SaaS, aplikaci PowerBI, Dynamics 365 Business Central, Dynamics 365 for Customer Engagement a Dynamics 365 for Operations Offer Types. Položky tučně kurzívou (jako je ***Analytics***) jsou kategorie a standardní textové položky (jako rozšířené analýzy) jsou podkategoriemi pod nimi. Používejte přesné hodnoty klíčů bez změny mezer nebo velkých a malých písmen.

| Kategorie | SaaS klíče | Dynamics 365 Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 pro klíče operací | Klíče aplikace PowerBI |
| --- | --- | --- | --- |
| ***Analýza** _ | _*_Analytics_*_ | _*_Analýzy_*_ | _ *_Analytics_** |
| Pokročilá analýza | Pokročilá analýza | Pokročilá analýza | Pokročilá analýza |
| Vytváření sestav & vizualizace | vizualizace – vytváření sestav | vizualizace – vytváření sestav | vizualizace – vytváření sestav |
| Jiné | other | jiné – analýza | jiné – analýza |
| ***AI + Machine Learning** _ | _*_ArtificialIntelligence_*_ | _*_AI-plus-Machine-Learning-Dynamics_*_ | _ *_AI-plus-Machine-Learning-appsource_** |
| AI pro firmy | AI pro firmy | AI pro firmy | AI pro firmy |
| Robot – aplikace | Robot – aplikace | Robot – aplikace | Robot – aplikace |
| Jiné | ostatní – AI a strojové učení | ostatní – AI a strojové učení | ostatní – AI a strojové učení |
| ***Spolupráce** | _*_Prostřednictvím_*_ | _*_Prostřednictvím_*_ | _ *_spolupráce_** |
| Kontaktování & lidí | kontaktní osoby | kontaktní osoby | kontakty a osoby |
| Správa schůzek | Správa schůzek | Správa schůzek | Správa schůzek |
| Správa & pro návrh webu | Správa návrhu lokality | Správa návrhu lokality | Lokalita – návrh a Správa |
| Úkol & řízení projektu | úkol – Správa projektů | úkol – Správa projektů | Správa úloh a projektů |
| Videokonference pro hlasové & | Hlasová – videokonference | Hlasová – videokonference | videohovory a videokonference |
| Jiné | jiná spolupráce | jiná spolupráce | other |
| ***Dodržování předpisů & právní** _ | _*_dodržování_*_ | _*_dodržování_*_ | _ *_dodržování předpisů – právní_** |
| Audit daňových & | daň – audit | daň – audit | daň a audit |
| Právní informace | Právní informace | Právní informace | legalit |
| Data, zásady správného řízení & ochrany osobních údajů | řízení dat – ochrana osobních údajů | řízení dat – ochrana osobních údajů | data-zásady správného řízení a ochrany osobních údajů |
| Bezpečnost & stavu | zdravotní zabezpečení | zdravotní zabezpečení | stav a bezpečnost |
| Jiné | jiné dodržování předpisů – právní | jiné dodržování předpisů – právní | other |
| ***Služba zákazníkům** _ | _*_CustomerService_*_ | _*_CustomerService_*_ | _ *_Zákazník – služba_** |
| Kontaktní centrum | kontakt – centrum | kontakt – centrum | kontakt – centrum |
| Tvář na službu obličeje | tvář – služba | tvář – služba | tvář – služba |
| Zpětná služba Office & Employee Service | Back-Office-Employee-Service | Back-Office-Employee-Service | Back-Office-a-Employee-Service |
| Správa případů znalostní & | znalostní báze – Správa malých a velkých písmen | znalostní báze – Správa malých a velkých písmen | znalostní báze a správa malých a velkých písmen |
| Zapojení sociálních médií & Omnichannel | sociální média – omnichannel-Engagement | sociální média – omnichannel-Engagement | Social-Media-a-omnichannel-Engagement |
| Jiné | jiný zákazník – služba | jiný zákazník – služba | other |
| ***Finance** _ | _*_Finance_*_ | _*_Finance_*_ | _ *_finance_** |
| Účetnictví | accounting (účetnictví) | accounting (účetnictví) | accounting (účetnictví) |
| Správa aktiv | Správa prostředků | Správa prostředků | Správa prostředků |
| Analýzy, konsolidace & vytváření sestav | analýzy – konsolidace – vytváření sestav | analýzy – konsolidace – vytváření sestav | analýzy – konsolidace a vytváření sestav |
| Kolekce & kreditů | kredit – kolekce | kredit – kolekce | kredity a kolekce |
| Řízení rizik & dodržování předpisů | dodržování předpisů – Správa rizik | dodržování předpisů – Správa rizik | dodržování předpisů a Správa rizik |
| Jiné | ostatní – finance | ostatní – finance | other |
| ***Lidské zdroje** _ | _*_Lidskézdroje_*_ | _*_Lidskézdroje_*_ | _ *_lidské zdroje_** |
| Získání talentů | talentů – získání | talentů – získání | talentů – získání |
| Správa talentů | talentů – Správa | talentů – Správa | talentů – Správa |
| Operace HR | HR – operace | HR – operace | HR – operace |
| Plánování zaměstnanců & Analytics | pracovní síly – plánování – analýza | pracovní síly – plánování – analýza | pracovní síly – plánování a analýza |
| Jiné | jiné – lidské zdroje | jiné – lidské zdroje | other |
| ***Internet věcí** _ | _*_IoT_*_ | _*_Internet věcí – Dynamics_*_ | _ *_Internet věcí – appsource_** |
| Operace & správy prostředků | Správa prostředků – operace | Správa prostředků – operace | Správa prostředků a operací |
| Připojené produkty | připojené – produkty | připojené – produkty | připojené – produkty |
| Inteligentní dodavatelský řetězec | Inteligentní-dodavatelský řetězec | Inteligentní-dodavatelský řetězec | Inteligentní-dodavatelský řetězec |
| Prediktivní údržba | prediktivní údržba | prediktivní údržba | prediktivní údržba |
| Vzdálené monitorování | vzdálené monitorování | vzdálené monitorování | vzdálené monitorování |
| Bezpečnostní & zabezpečení | bezpečnost – zabezpečení | bezpečnost – zabezpečení | bezpečnost a zabezpečení |
| Prostředky & inteligentní infrastruktury | inteligentní infrastruktura – prostředky | inteligentní infrastruktura – prostředky | inteligentní infrastruktura a prostředky |
| Prostředky & mobility | vozidla – mobilita | vozidla – mobilita | vozidla a mobilita |
| Jiné | ostatní – Internet věcí | ostatní – Internet věcí | other |
| ***Nástroje pro správu IT &** _ | _*_ITandAdministration_*_ | _*_ITandAdministration_*_ | _ *_IT a-Management-Tools_** |
| Řešení pro správu | Správa – řešení | Správa – řešení | Správa – řešení |
| Podnikové aplikace | businessApplication | businessApplication | obchodní aplikace |
| Jiné | Správa jiných IT nástrojů | Správa jiných IT nástrojů | other |
| ***Marketing** _ | _*_Marketing_*_ | _*_Marketing_*_ | _ *_Marketing_** |
| Reklamní | reklamní | reklamní | reklamní |
| Analýzy | analýzy – marketing | analýzy – marketing | analýzy – marketing |
| Automatizace & správy kampaní | Správa kampaně – automatizace | Správa kampaně – automatizace | Správa kampaně-a – automatizace |
| E-mail marketingu | e-mail – marketing | e-mail – marketing | e-mail – marketing |
| L2 – události & Správa prostředků | události – Správa prostředků | události – Správa prostředků | události a Správa prostředků |
| Analýza & výzkumu | výzkum – analýza | výzkum – analýza | výzkum a analýza |
| Sociální sítě | sociální média | sociální média | sociální média |
| Jiné | jiný marketing | jiný marketing | other |
| ***Operace & dodavatelského řetězce** _ | _*_OperationsSupplyChain_*_ | _*_OperationsSupplyChain_*_ | _ *_operace-and-dodavatelský-řetěz_** |
| Správa produkčního prostředí Asset & | Asset – Správa v produkčním prostředí | Asset – Správa v produkčním prostředí | Správa prostředků a výroby |
| Prognózování poptávky | Prognózování poptávky | Prognózování poptávky | Prognózování poptávky |
| Správa informací & připojení | Správa informací – připojení | Správa informací – připojení | Správa informací a připojení |
| Plánování, nákup & vytváření sestav | plánování – nákup – vytváření sestav | plánování – nákup – vytváření sestav | plánování – nákup a vytváření sestav |
| Správa kvality & služeb | kvalita – Správa služeb | kvalita – Správa služeb | Správa kvality a služeb |
| Správa objednávek prodejních & | prodejní objednávka – Správa | prodejní objednávka – Správa | Správa prodeje a objednávek |
| Správa datového skladu pro dopravu & | přeprava – Správa skladu | přeprava – Správa skladu | Správa přenosu a skladu |
| Jiné | jiné operace – dodavatelský řetězec | jiné operace – dodavatelský řetězec | other |
| ***Produktivita** _ | _*_Produktivita_*_ | _*_Produktivita_*_ | _ *_produktivita_** |
| Vytváření & správy obsahu | vytváření obsahu – Správa | vytváření obsahu – Správa | vytváření obsahu a Správa |
| Překlad jazyka & | Jazykové překlady | Jazykové překlady | jazyk a překlad |
| Document Management | Správa dokumentů | Správa dokumentů | Správa dokumentů |
| Správa e-mailů | Správa e-mailů | Správa e-mailů | Správa e-mailů |
| Hledat & reference | hledání – referenční informace | hledání – referenční informace | hledání a reference |
| Jiné | jiné – produktivita | jiné – produktivita | other |
| Gamifikace | Gamifikace | Gamifikace | gamifikační |
| ***Prodej** _ | _*_Sales_*_ | _*_Sales_*_ | _ *_Sales_** |
| Prodej | prodej | prodej | prodej |
| Konfigurace, Price, Quota (CPQ) | Konfigurace – cenová nabídka | Konfigurace – cenová nabídka | Konfigurace – cenová nabídka |
| Správa smluv | Správa smluv | Správa smluv | Správa smluv |
| APLIKACE | crm | crm | crm |
| Elektronický obchod | elektronický obchod | elektronický obchod | elektronický obchod |
| Obohacení obchodních dat | podnikání – obohacení dat | podnikání – obohacení dat | podnikání – obohacení dat |
| Povolení prodeje | prodej – povolení | prodej – povolení | prodej – povolení |
| Jiné | Ostatní – prodej | Ostatní – prodej | Ostatní – prodej |
| ***Geografická poloha** _ | _*_Zeměpisná poloha_*_ | _*_Zeměpisná poloha_*_ | _ *_zeměpisná poloha_** |
| Maps | maps | maps | maps |
| Novinky & počasí | Novinky a počasí | Novinky a počasí | Novinky a počasí |
| Jiné | jiné zeměpisné umístění | jiné zeměpisné umístění | jiné zeměpisné umístění |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSourcech odvětví

Tyto obory a jejich příslušné klíče platí pro SaaS, aplikaci PowerBI, Dynamics 365 Business Central, Dynamics 365 for Customer Engagement a Dynamics 365 pro typy nabídek operací. Položky tučně kurzívou (například ***automobil***) jsou kategorie a standardní textové položky (například AutomotiveL2) jsou podkategoriemi pod nimi. Používejte přesné hodnoty klíčů bez změny mezer nebo velkých a malých písmen.

| Obor | SaaS, Dynamics 365 Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 pro klíče operací | Klávesy aplikací PowerBI |
| --- | --- | --- |
| ***Automobilový** _ | _*_Automobilový průmysl_*_ | _ *_automobil_** |
| Automobilový průmysl | AutomotiveL2 | AutomotiveL2 |
| ***Zemědělství** _ | _*_Zemědělství_*_ | _ *_zemědělství_** |
| Jiné – nesegmentované | \_OtherUnsegmented zemědělství | jiné – nesegmentované |
| ***Distribuce** _ | _*_Distribuce_*_ | _ *_distribuce_** |
| Hromadné | Hromadné | hromadné |
| Odeslání balíčku & balíčků | ParcelAndPackageShipping | přepravování balíčků a balení |
| ***Vzdělávání** _ | _*_Vzdělávání_*_ | _ *_vzdělávání_** |
| Vyšší vzdělávání | HigherEducation | vyšší vzdělávání |
| Sekundární vzdělávání primárního &/K-12 | PrimaryAndSecondaryEducationK12 | primární a sekundární – vzdělávání |
| Knihovny & muzeí | LibrariesAndMuseums | knihovny a-muzeí |
| ***Finanční služby** _ | _*_FinancialServices_*_ | _ *_finanční – služby_** |
| Bankovní & kapitálové trhy | BankingAndCapitalMarkets | bankovní a kapitálové trhy |
| Pojišťovací | Pojišťovací | pojišťovací |
| ***Státní správa** | _*_Státní správa_*_ | _ *_státní správy_** |
| Obrana & Intelligence | DefenseAndIntelligence | Obrana a – Intelligence |
| Veřejné zabezpečení & spravedlnosti | PublicSafetyAndJustice | veřejné zabezpečení a spravedlnosti |
| Civilní samospráva | CivilianGovernment | civilní instituce |
| ***Zdravotnictví** _ | _*_HealthCareandLifeSciences_*_ | _ *_zdravotnictví_** |
| Plátce stavu | HealthPayor | Stav – plátce |
| Poskytovatel stavu | HealthProvider | poskytovatel stavu |
| Pharmaceuticals | Pharmaceuticals | Pharmaceuticals |
| ***Prostředky výrobního &** _ | _*_Výroba_*_ | _ *_výroby a prostředků_** |
| Chemický & Agrochemical | ChemicalAndAgrochemical | chemikálie a agrochemical |
| Diskrétní výroba | DiscreteManufacturing | diskrétní – výroba |
| Energetický sektor | Energetický sektor | energy |
| ***Maloobchodní & spotřební zboží** | _*_RetailandConsumerGoods_*_ | _ *_maloobchodní a spotřebitel – zboží_** |
| Spotřební zboží | ConsumerGoods | spotřebitel – zboží |
| Maloobchodní | Maloobchodní | maloobchodní |
| ***Media & komunikace** _ | _*_MediaAndCommunications_*_ | _ *_média a komunikace_** |
| Media & Entertainment | MediaandEntertainment | média a zábava |
| Telekomunikace | Telekomunikace | telekomunikace |
| ***Profesionální služby** _ | _*_ProfessionalServices_*_ | _ *_– profesionální služby_** |
| Právní informace | Právní informace | legalit |
| Profesionální služby partnerů | PartnerProfessionalServices | partner – profesionální služby |
| ***Architektura & konstrukce** _ | _*_ArchitectureAndConstruction_*_ | _ *_– Architektura a konstrukce_** |
| Jiné – nesegmentované | ArchitectureAndConstruction \_ OtherUnsegmented | jiné – nesegmentované |
| ***Pohostinství & cestování** | _*_HospitalityandTravel_*_ | _ *_pohostinství a cestování_** |
|    Hotely & volný čas | HotelsAndLeisure | Hotely a-volný čas |
| Cestovní & Transport | TravelAndTransportation | cestovné a přeprava |
| Restaurace & potravinové služby | RestaurantsAndFoodServices | Restaurace a stravování – služby |
| ***Další odvětví veřejného sektoru** _ | _*_OtherPublicSectorIndustries_*_ | _ *_jiné – veřejný sektor_ – odvětví** |
| Lesní & rybolov | ForestryAndFishing | lesnictví a rybolov |
| Neziskové organizace | Neziskové organizace | neziskové organizace |
| ***Nemovitosti _** | _*_RealEstate_*_ | _ *_reálné – nemovitosti_** |
| Jiné – nesegmentované | RealEstate \_ OtherUnsegmented | jiné – nesegmentované |
|||
