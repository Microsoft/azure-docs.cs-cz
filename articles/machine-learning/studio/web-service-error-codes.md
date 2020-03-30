---
title: Chybové kódy rozhraní REST API – Azure Machine Learning Studio (klasické) | Dokumenty společnosti Microsoft
description: Tyto kódy chyb může být vrácena operace na azure machine learning webové služby.
keywords: ''
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 9e25f2fbc10eb07cc71f2a7bd34247c0191f61b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217763"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klasické) kódy chyb rozhraní REST API

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
Následující kódy chyb může být vrácena operace na Azure Machine Learning Studio (klasické) webové služby.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (stavový kód HTTP 400)
 
Byl poskytnut neplatný argument.
 
Tato třída chyb znamená, že argument, který byl někde poskytnut, byl neplatný. Může se jedná o pověření nebo umístění úložiště Azure na něco, co bylo předáno webové službě. Zkontrolujte, prosím, na chybu "kód" pole v části "podrobnosti" diagnostikovat, který konkrétní argument byl neplatný.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| Hodnota chybného parametru | Zadaná hodnota parametru nesplňuje pravidlo parametru pro parametr. |
| Chybně id předplatného | Id předplatného, který se používá ke skóre není ten, který je přítomen v prostředku |
| Volání BadVersion | Během volání rozhraní API byl {0}předán neplatný parametr verze: . Zkontrolujte stránku nápovědy rozhraní API pro předání správné verze a zkuste to znovu. |
| BatchJobInputsNotSpecified | Následující požadované vstupy nebyly v požadavku zadány: {0}. Zkontrolujte, zda jsou zadána všechna vstupní data, a akci opakujte. |
| BatchJobInputsTooManySpecified | Požadavek zadal více vstupů, než bylo definováno ve službě. Seznam přijatých vstupů: {0}. Zkontrolujte, zda jsou všechna vstupní data zadána správně, a akci opakujte. |
| Název objektu BlobTooLong | Cesta úložiště objektů blob Azure k dispozici {0}pro diagnostický výstup je příliš dlouhá: . Zkraťte cestu a akci opakujte. |
| Objekt Blob nebyl nalezen. | Nelze získat přístup k zadaný {0}objekt blob Azure - .  Chybová zpráva {1}Azure: . |
| KontejnerIsPrázdný | Nebyl k dispozici žádný název kontejneru úložiště Azure. Zadejte platný název kontejneru a akci opakujte. |
| ContainerSegmentNeplatný | Neplatný název kontejneru. Zadejte platný název kontejneru a akci opakujte. |
| Ověření kontejneru se nezdařilo. | Ověření kontejneru objektů blob {0}se nezdařilo s touto chybou: . |
| DataTypeNotSupported | Nepodporovaný datový typ k dispozici. Zadejte platné typy dat a akci opakujte. |
| DuplicateInputInBatchCall | Dávkový požadavek je neplatný. Současně nelze zadat jeden i více vstupů. Odeberte jednu z těchto položek z požadavku a akci opakujte. |
| ExpiryTimeInThePast | Doba použitelnosti je v minulosti: {0}. Zadejte budoucí čas vypršení platnosti v utc a zkuste to znovu. Chcete-li nikdy nevyprší, nastavte čas vypršení platnosti na hodnotu NULL. |
| NeúplnéNastavení | Nastavení diagnostiky je neúplné. |
| InputBlobRelativní UmístěníNeplatné | Není k dispozici žádný název objektu blob úložiště Azure. Zadejte platný název objektu blob a akci opakujte. |
| Neplatné blob | Neplatná specifikace objektu {0}blob pro objekt blob: . Ověřte, zda je připojovací řetězec / relativní cesta nebo specifikace tokenu SAS správná, a akci opakujte. |
| InvalidBlobConnectionString | Připojovací řetězec určený pro jeden ze vstupních {0}a výstupních objektů BLOB v neplatném: . Opravte to a akci opakujte. |
| Rozšíření InvalidBlobExtension | Odkaz na objekt {0} blob: má neplatnou nebo chybějící příponu souboru. Podporované přípony souborů pro tento{1}typ výstupu jsou: " ". |
| Neplatné Názvy Input | V požadavku jsou zadány neplatné {0}vstupní názvy služby: . Namapujte vstupní data na správné vstupy služby a akci opakujte. |
| InvalidOutputOverrideName | Neplatný název přepsání výstupu: {0}. Služba nemá výstupní uzel s tímto názvem. Předejte správný název výstupního uzlu k přepsání (platí rozlišování malých a velkých písmen). |
| Parametr InvalidQueryParameter | Neplatný parametr{0}dotazu ' '. {1} |
| ChybějícíInputBlobInformace | Chybí informace o objektu blob úložiště Azure. Zadejte platný připojovací řetězec a relativní cestu nebo identifikátor URI a akci opakujte. |
| MissingJobId | Žádná pracovní id za předpokladu. Id úlohy je vrácena při odeslání úlohy poprvé. Ověřte, zda je Id úlohy správné, a akci opakujte. |
| Chybějící klávesy | Nejsou k dispozici žádné klíče nebo není k dispozici jeden z primárního nebo sekundárního klíče. |
| Chybějícíbalíček | Není k dispozici žádné Id balíčku modelu nebo balíček modelu. Zadejte platné ID balíčku modelu nebo balíček modelu a akci opakujte. |
| Chybějícíspecifikace Převyšovací hodu výstupu | V požadavku chybí specifikace objektu blob pro přepsání {0}výstupu . Zadejte platné umístění objektu blob s požadavkem nebo odeberte výstupní specifikaci, pokud není požadováno žádné přepsání umístění. |
| ChybějícíRequestInput | Webová služba očekává vstup, ale nebyl zadán žádný vstup. Ujistěte se, že platné vstupy jsou k dispozici na základě publikovaných vstupních portů v modelu a zkuste to znovu. |
| ChybějícíParametry Hodnotitík globálních parametrů | Ne všechny požadované parametry webové služby jsou k dispozici. Ověřte, zda jsou parametry očekávané pro modul (moduly) správné, a akci opakujte. |
| ChybějícíPřetečy výstupů | Při volání koncový bod šifrované služby je povinné předat přepsání výstupu pro všechny výstupy služby. Chybějící přepsání v tomto okamžiku pro tyto výstupy:{0} |
| Chybějící WebServiceGroupId | Není k dispozici žádné ID skupiny webových služeb. Zadejte platné ID skupiny webových služeb a akci opakujte. |
| MissingWebServiceId | Není k dispozici žádné Id webové služby. Zadejte platné ID webové služby a akci opakujte. |
| Chybějící balíček WebServicePackage | Není k dispozici žádný balíček webové služby. Zadejte platný balíček webové služby a akci opakujte. |
| Chybějící WorkspaceId | Není k dispozici žádné Id pracovního prostoru. Zadejte platné ID pracovního prostoru a akci opakujte. |
| Konfigurace modelu je neplatná. | Neplatná konfigurace modelu v balíčku modelu. Ujistěte se, že konfigurace modelu obsahuje definici výstupního koncového bodu, koncový bod chyby std a koncový bod std a zkuste to znovu. |
| Neplatný soubor PackagePackageId modelu | Neplatné ID balíčku modelu. Ověřte, zda je ID balíčku modelu správné, a akci opakujte. |
| RequestBodyNeNeplatné | Při rekonstrukci těla požadavku nebylo poskytnuto žádné tělo požadavku ani chyba. |
| Požadavekje prázdný | Nebyl anebyl/a žádný požadavek. Zadejte platný požadavek a akci opakujte. |
| Parametr UnexpectedParameter | Neočekávané parametry k dispozici. Ověřte, zda jsou všechny názvy parametrů zadány správně, jsou předány pouze očekávané parametry a akci opakujte. |
| Neznámýchyba | Neznámou chybu. |
| Parametr UživateleJeneplatný | {0} |
| WebServiceConcurrentRequestRequirementNe | Nelze změnit požadavky na {0} souběžné požadavky na webové služby. |
| WebServiceIdNeplatný | Bylo k dispozici neplatné id webové služby. Id webové služby by měl být platný identifikátor GUID. |
| WebServiceTooManyConcurrentRequestRequirement | Nelze nastavit požadavek na {0}souběžný požadavek na více než . |
| Typ_ _webová službaje neplatná. | Byl zadán neplatný typ webové služby. Ověřte, zda je platný typ webové služby správný, a akci opakujte. Platné typy webových služeb: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (stavový kód HTTP 400)
 
Byl k dispozici neplatný uživatelský argument.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| Chyba InputSsmatchError | Vstupní data neodpovídají schématu vstupního portu. |
| Chyba InputParseError | Analýza vstupního vektoru se nezdařila.  Ověřte, zda vstupní vektor má správný počet sloupců a datových typů.  Další podrobnosti: {0}. |
| ChybějícíParametry Hodnotitík globálních parametrů | Parametry očekávané webovou službou chybí. Ověřte, zda jsou všechny požadované parametry očekávané webovou službou správné, a akci opakujte. |
| Parametr UnexpectedParameter | Ověřte, zda jsou předány pouze požadované parametry očekávané webovou službou a opakujte akci. |
| Parametr UživateleJeneplatný | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (stavový kód HTTP 400)
 
Požadavek je v aktuálním kontextu neplatný.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| Nelze spustit úlohu | Úlohu nelze spustit, protože {0} je ve stavu. |
| NekompatibilníModel | Model není kompatibilní s verzí požadavku. Verze požadavku podporuje pouze jeden datatable výstupní modely. |
| MultipleInputsNotAllowed | Model neumožňuje více vstupů. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (stavový kód HTTP 400)
 
Spuštění modulu došlo k chybě vnitřní knihovny.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (stavový kód HTTP 400)
 
Při spuštění modulu došlo k chybě.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (stavový kód HTTP 400)
 
Neplatný balíček webové služby. Ověřte, zda je poskytnutý balíček webové služby správný, a akci opakujte.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| FormatError | Balíček webové služby je poškozen. Podrobnosti:{0} |
| Chyba runtimeserror | Graf balíčku webových služeb je neplatný. Podrobnosti:{0} |
| Validationerror | Graf balíčku webových služeb je neplatný. Podrobnosti:{0} |
 
## <a name="unauthorized-http-status-code-401"></a>Neautorizovaný (stavový kód HTTP 401)
 
Požadavek není oprávněný pro přístup k prostředku.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| AdminRequestNeauthorized | Neautorizováno |
| Požadavek na správu Neautorizován | Neautorizováno |
| ScoreRequestNeauthorized | Byla poskytnuta neplatná pověření. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (stavový kód HTTP 404)
 
Zdroj nebyl nalezen.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| ModelPackageNotFound. | Balíček modelu nebyl nalezen. Ověřte, zda je ID balíčku modelu správné, a akci opakujte. |
| WebServiceIdNotFoundInWorkspace | Webová služba v tomto pracovním prostoru nebyla nalezena. Existuje neshoda mezi webServiceId a workspaceId. Ověřte, zda je zakalená webová služba součástí pracovního prostoru, a akci opakujte. |
| WebserviceNotFound | Webová služba nebyla nalezena. Ověřte, zda je ID webové služby správné, a akci opakujte. |
| Pracovní prostor nebyl nalezen | Pracovní prostor nebyl nalezen. Ověřte, zda je Id pracovního prostoru správné, a akci opakujte. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (stavový kód HTTP 408)
 
Operaci nelze dokončit v povoleném čase.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| Požadavek byl zrušen. | Požadavek byl klientem zrušen. |
| ScoreRequestTimeout | Časový čas požadavku na spuštění byl vyhotoven. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (stavový kód HTTP 409)
 
Prostředek již existuje.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Neplatný název výstupního parametru. Zkuste pomocí modulu editoru metadat přejmenovat sloupce a zkuste to znovu. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (stavový kód HTTP 413)
 
Model překročil kvótu paměti, která mu byla přiřazena.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| Limit mimo paměť | Model spotřeboval více paměti, než bylo přivlastněno. Maximální povolená paměť {0} modelu je MB. Zkontrolujte, zda váš model pro problémy. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (stavový kód HTTP 500)
 
Spuštění došlo k vnitřní chybě.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| Ověření správce se nezdařilo. |  |
| Chyba backendargumentu |  |
| BackendBadPožadavek |  |
| ClusterConfigBlobMiskonfigurován |  |
| ContainerProcessTerminatedWithSystemError | Proces kontejneru havaroval s chybou systému |
| ContainerProcessTerminatedWithUnknownError | Proces kontejneru havaroval s neznámou chybou |
| Ověření kontejneru se nezdařilo. | Ověření kontejneru objektů blob {0}se nezdařilo s touto chybou: . |
| Odstranění prostředku webservice se nezdařilo. |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| Služba FailedStoringWebService |  |
| Neplatná konfigurace paměti | Neplatná konfigurace paměti, hodnota konfigurace:{0} |
| Neplatná konfigurace mezipaměti prostředků |  |
| Neplatná konfigurace skupiny ResourceDownloadConfiguration |  |
| Neplatnéwebservicezdroje |  |
| Instance chybějící úlohy | Nejsou k dispozici žádné argumenty. Ověřte, zda jsou předané platné argumenty, a akci opakujte. |
| ModelPackageInvalid |  |
| ModulProvedení se nezdařilo. |  |
| ModulLoadFailed |  |
| ModulObjectCloneFailed |  |
| VýstupConversionFailed |  |
| PortDataTypeNotSupported | Port id{0} = má nepodporovaný {1}datový typ: . |
| ResourceDownload |  |
| Nepodařilo se načíst prostředky |  |
| ServiceUrisNotfound |  |
| SwaggerGenerace | Swagger generace selhala, Podrobnosti:{0} |
| NeočekávanéskóreStatus |  |
| NeznámýBackendErrorResponse |  |
| Neznámýchyba |  |
| NeznámýjobStatusCode | Neznámý kód {0}stavu úlohy . |
| Chyba neznámého modulu |  |
| UpdateWebServiceResourceResourceFailed |  |
| WebServiceGroupNotFound |  |
| Balíček webové služby neplatný | InvalidWebServicePackage, podrobnosti:{0} |
| Autorizace pracovníka Se nezdařila. |  |
| Pracovníknedostupný |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (stavový kód HTTP 500)
 
Spuštění došlo k vnitřní chybě. Systém má málo paměti. Zkuste to prosím znovu.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (stavový kód HTTP 500)
 
Neplatný balíček modelu. Ověřte, zda je poskytnutý balíček modelu správný, a akci opakujte.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (stavový kód HTTP 500)
 
Neplatný balíček webové služby. Ověřte, zda je poskytnutý webový balíček správný, a akci opakujte.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| ModulChyba | Graf balíčku webových služeb je neplatný. Podrobnosti:{0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>Inicializace kontejnerů (stavový kód HTTP 503)
 
Požadavek nelze spustit, protože kontejnery jsou inicializovány.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (stavový kód HTTP 503)
 
Služba je dočasně nedostupná.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| NoMoreZdroje | Pro vyžádání nejsou k dispozici žádné prostředky. |
| RequestThrottled | Požadavek byl {0} omezen pro koncový bod. Maximální souběžnost pro koncový {1}bod je . |
| TooManyConcurrentRequests | Bylo odesláno příliš mnoho souběžných požadavků. |
| TooManyHostsInitialized | Příliš mnoho hostitelů je inicializováno současně. Zvažte škrcení / opakování. |
| TooManyHostsBeingInitializedPerModel TooManyHostsBeingInitializedPerModel TooMany | Příliš mnoho hostitelů je inicializováno současně. Zvažte škrcení / opakování. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (stavový kód HTTP 504)
 
Operaci nelze dokončit v povoleném čase.
 
| Kód chyby | Uživatelská zpráva |
| ---------- |--------------|
| Back-endInitializationTimeout | Inicializaci webové služby nelze dokončit v povoleném čase. |
| BackendScoreTimeout | Spuštění požadavku webové služby nelze dokončit v povoleném čase. |
 
