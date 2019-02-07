---
title: Zabezpečený přístup k Azure Logic Apps | Dokumentace Microsoftu
description: Přidání zabezpečení pro Azure Logic Apps, včetně aktivačních událostí, vstupů a výstupů, parametry a další služby
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: c3057934d960efd0a846ef31c5fac5abd63a21f6
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768464"
---
# <a name="secure-access-in-azure-logic-apps"></a>Zabezpečený přístup v Azure Logic Apps

Tady jsou prvky v aplikaci logiky, ve kterém se dají zabezpečit přístup:

* [Požadavek nebo triggery Webhooků](#secure-triggers)
* [Operace, jako je správa, úpravách nebo prohlížení](#secure-operations) aplikace logiky
* [Vstupy a výstupy](#secure-run-history) historie spuštění vaší aplikace logiky
* [Parametry akce a vstupy](#secure-action-parameters)
* [Služby, které žádosti o získání](#secure-requests) vaší aplikace logiky

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>Zabezpečený přístup k vyžádání aktivace

Když vaše aplikace logiky používá triggerem založené na požadavku HTTP, jako [požadavek](../connectors/connectors-native-reqres.md) nebo [Webhooku](../connectors/connectors-native-webhook.md) aktivační událost, můžete omezit přístup pouze autorizovaní klienti můžou začít svou aplikaci logiky. Všechny požadavky přijaté aplikací logiky je šifrovaný a zabezpečený pomocí vrstvy SSL (Secure Sockets) protokolů. Tady jsou různé způsoby, jak můžete zabezpečit přístup k tomuto typu triggeru:

* [Generování sdílených přístupových podpisů](#sas)
* [Omezit příchozí IP adresy](#restrict-incoming-IP)
* [Přidat Azure Active Directory, OAuth nebo jiných zabezpečení](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>Generování sdílených přístupových podpisů

Každý koncový bod žádosti na aplikace logiky zahrnuje [sdíleného přístupového podpisu (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) v adrese URL koncového bodu. Obsahuje všechny adresy URL `sp`, `sv`, a `sig` parametr dotazu:

* `sp` Určuje oprávnění, která mapují na povolené metody HTTP pro použití.
* `sv` Určuje verzi sloužit ke generování podpis.
* `sig` slouží k ověřování přístupu k triggeru.

Podpis je generována pomocí algoritmus SHA256 s tajný přístupový klíč na cestách URL a vlastnosti. Tajný klíč není vystavený ani publikování a zůstane zašifrované a uložené s aplikací logiky. Aplikace logiky autorizuje pouze triggery, které obsahují platný podpis vytvořené pomocí tajného klíče. 

Zde jsou další informace o zabezpečení přístupu pomocí sdíleného přístupového podpisu:

* [Opětovné vygenerování přístupových klíčů](#access-keys)
* [Vytvoření, u nichž vyprší platnost adresy URL zpětného volání](#expiring-URLs)
* [Vytvoření adresy URL s primární nebo sekundární klíč](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Opětovné vygenerování přístupových klíčů

Pokud chcete znovu vygenerovat nový klíč zabezpečený přístup kdykoli, použijte rozhraní Azure REST API nebo webu Azure portal. Všechny dříve vygenerovaný adresy URL, které používá starý klíč nejsou zneplatněny a nadále již nebudou oprávnění ke spuštění aplikace logiky. Adresy URL, kterou načtete po obnovení jsou podepsány pomocí nového přístupového klíče.

1. Na webu Azure Portal otevřete aplikaci logiky, který má klíč, který chcete znovu vygenerovat.

1. V nabídce aplikace logiky podle **nastavení**vyberte **přístupové klíče**.

1. Vyberte klíč, který chcete znovu vygenerovat a dokončení procesu.

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>Vytvoření adresy URL zpětného volání s datem vypršení platnosti

Pokud adresa URL koncového bodu na základě požadavku triggeru s jiné smluvní strany, můžete vygenerovat URL zpětného volání konkrétní klíče a data vypršení platnosti podle potřeby. Můžete pak bezproblémově vrátit klíče, nebo omezit přístup pro spuštění aplikace logiky na konkrétní časový interval. Datum vypršení platnosti můžete zadat adresu URL pomocí [rozhraní REST API služby Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), například:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

V textu, zahrnout `NotAfter`vlastnost JSON pomocí data řetězce. Tato vlastnost vrátí adresu URL zpětného volání, který je platný pouze dokud `NotAfter` datum a čas.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Vytvoření adresy URL s primární a sekundární tajný klíč

Při generování nebo seznam adres URL zpětného volání pro triggery založené na požadavku, můžete také zadat klíče pro podepisování adresu URL. Můžete generovat adresu URL, která je podepsána s použitím konkrétního klíče [rozhraní REST API služby Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), například:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

V textu, zahrnout `KeyType` vlastnost jako buď `Primary` nebo `Secondary`. Tato vlastnost vrátí adresu URL, která je podepsána zadaným zabezpečený klíč.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Omezit příchozí IP adresy

Spolu se sdíleným přístupovým podpisem můžete chtít omezit konkrétních klientů, které může volat aplikaci logiky.  
Pokud spravujete vašeho požadavku koncového bodu ve službě Azure API Management, můžete omezit aplikace logiky tak, aby přijímal požadavky jenom z IP adresy instance API Management. 

#### <a name="set-ip-ranges---azure-portal"></a>Nastavení rozsahů IP adres – Azure portal

Nastavit toto omezení na webu Azure Portal, přejděte do nastavení aplikace logiky: 

1. Na webu Azure Portal otevřete svůj prostředek v návrháři aplikace logiky. 

1. V nabídce aplikace logiky podle **nastavení**vyberte **nastavení pracovního postupu**.

1. V části **konfigurace řízení přístupu** > 
**povolené příchozí IP adresy**vyberte **konkrétními rozsahy IP adres**.

1. V části **rozsahy IP adres pro triggery**, zadejte rozsahy IP adres, které přijímá aktivační událost. Platný rozsah IP adres používá tyto formáty: *x.x.x.x/x* nebo *x.x.x.x x.x.x.x.* 

Pokud chcete svou aplikaci logiky, která se aktivuje pouze jako vnořenou aplikaci logiky, z **povolené příchozí IP adresy** seznamu vyberte **pouze jiných funkcí Logic Apps**. Tato možnost zapíše prázdné pole prostředek aplikace logiky, takže pouze volání ze služby Logic Apps (nadřazený logiky aplikace), mohou aktivovat vnořenou aplikaci logiky.

> [!NOTE]
> Bez ohledu na IP adresu, můžete spustit aplikaci logiky, který má aktivační událost na základě požadavku s použitím `/triggers/{triggerName}/run` prostřednictvím rozhraní Azure REST API nebo prostřednictvím služby API Management. Ale tento scénář stále vyžaduje ověřování pomocí rozhraní Azure REST API a zobrazí všechny události do protokolu auditu v Azure. Ujistěte se, že je odpovídajícím způsobem nastavit zásady řízení přístupu.

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>Nastavení rozsahů IP adres – šablony nasazení aplikace logiky

Pokud používáte automatizaci nasazení aplikací logiky s použitím [šablony nasazení Azure Resource Manageru](../logic-apps/logic-apps-create-deploy-template.md), rozsahy IP adres můžete nastavit v šabloně, například:

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "triggers": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Přidat Azure Active Directory, OAuth nebo jiných zabezpečení

Chcete-li přidat další ověřovací protokoly do aplikace logiky, zvažte použití [Azure API Management](https://azure.microsoft.com/services/api-management/). Tato služba nabízí bohaté možnosti monitorování, zabezpečení, zásady a dokumentace pro libovolný koncový bod a poskytuje schopnost zpřístupnit vaše aplikace logiky jako rozhraní API. API Management můžete zveřejnit koncový bod veřejné nebo soukromé pro aplikaci logiky, která pak můžete použít Azure Active Directory, OAuth, certifikát nebo jiné standardy zabezpečení. Žádost o přijetí rozhraní API správy služby odešle požadavek na aplikaci logiky, což také všechny potřebné transformace nebo omezení na cestě. Umožňuje pouze rozhraní API Management aktivaci vaší aplikace logiky můžete svou aplikaci logiky nastavení rozsahu příchozí IP adresy. 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>Zabezpečený přístup k operacím aplikace logiky

Aby mohl jenom konkrétní uživatelé nebo skupiny spuštění operace na svou aplikaci logiky, můžete omezit přístup na úlohy, jako je správa, úpravy a zobrazení. Logic Apps podporuje [Azure Role-Based řízení přístupu (RBAC)](../role-based-access-control/role-assignments-portal.md), které můžete přizpůsobit nebo přiřazení předdefinované role pro členy ve vašem předplatném, například:

* **Přispěvatel aplikace logiky**: Uživatelé mohou zobrazit, upravit a aktualizovat aplikace logiky. Tuto roli nejde odstranit aplikaci logiky ani spouštět operace správce.
* **Operátor aplikace logiky**: Uživatelům můžete svou aplikaci logiky a historie spuštění, zobrazit a povolit nebo zakázat svou aplikaci logiky. Tato role se nedají upravit ani aktualizovat aplikaci logiky.

Pokud chcete zabránit ostatním uživatelům změna nebo odstranění aplikace logiky, můžete použít [zámek prostředku Azure](../azure-resource-manager/resource-group-lock-resources.md). Tato funkce umožňuje zabránit ostatním uživatelům měnit nebo odstraňovat produkční prostředky.

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>Zabezpečený přístup k historii spuštění aplikace logiky

Pokud chcete chránit obsah se předává jako vstupy nebo výstupy z předchozích spuštění aplikace logiky, můžete omezit přístup na konkrétní rozsahy IP adres. Tato funkce nabízí další řízení přístupu. Všechna data při spuštění aplikace logiky se šifrují během přenosu a v klidovém stavu. Pokud budete požadovat historie spouštění vaší aplikace logiky, Logic Apps ověřuje žádosti a obsahuje odkazy na vstupy a výstupy z požadavků a odpovědí v pracovním postupu vaší aplikace logiky. Tyto odkazy můžete chránit tak vrátit pouze požadavky z konkrétní IP adresu tohoto obsahu. Například může dokonce zadat IP adresu jako `0.0.0.0-0.0.0.0` tak, aby nemá nikdo přístup vstupy a výstupy. Pouze osoba s oprávněním správce můžete odebrat toto omezení, poskytuje možnost "just-in-time" přístup k obsahu vaší aplikace logiky.

### <a name="set-ip-ranges---azure-portal"></a>Nastavení rozsahů IP adres – Azure portal

Nastavit toto omezení na webu Azure Portal, přejděte do nastavení aplikace logiky:

1. Na webu Azure Portal otevřete svůj prostředek v návrháři aplikace logiky. 

1. V nabídce aplikace logiky podle **nastavení**vyberte **nastavení pracovního postupu**.

1. V části **konfigurace řízení přístupu** > 
    **povolené příchozí IP adresy**vyberte **konkrétními rozsahy IP adres**.

1. V části **rozsahy IP adres pro obsah**, zadejte rozsahy IP adres, které můžete přístup k obsahu z vstupy a výstupy. 
   Platný rozsah IP adres používá tyto formáty: *x.x.x.x/x* nebo *x.x.x.x x.x.x.x.* 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>Nastavení rozsahů IP adres – šablony nasazení aplikace logiky

Pokud používáte automatizaci nasazení aplikací logiky s využitím [šablony nasazení Azure Resource Manageru](../logic-apps/logic-apps-create-deploy-template.md), rozsahy IP adres můžete nastavit v šabloně, například:

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "contents": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>Zabezpečení parametry akce a vstupy

Při nasazení v různých prostředích, můžete chtít parametrizovat určité prvky v definici pracovního postupu aplikace logiky. Tímto způsobem je zadat vstupy založené na prostředí můžete použít a chránit citlivé informace. Například, pokud jste ověřování akce HTTP s [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), definovat a parametry, které přijímají ID klienta a tajný kód klienta používá k ověřování zabezpečení. Pro tyto parametry definici aplikace logiky má vlastní `parameters` oddílu.
Chcete-li přistupovat k hodnotám parametrů za běhu, můžete použít `@parameters('parameterName')` výraz, který je poskytován [jazyka definice pracovního postupu](https://aka.ms/logicappsdocs). 

Pokud chcete chránit, parametry a hodnoty, které nechcete zobrazit při úpravě vaší aplikaci logiky nebo historii spuštění zobrazení, můžete definovat parametry s `securestring` zadejte a používat kódování podle potřeby. Parametry, které obsahují tento typ nebudou zobrazeny s definicí prostředků a nejsou dostupné při prohlížení prostředku po nasazení.

> [!NOTE]
> Pokud použijete parametr v záhlaví a text požadavku, tento parametr může být viditelný, při přístupu k historii spuštění aplikace logiky a odchozí požadavek HTTP. Ujistěte se, že můžete také nastavit přístup k obsahu zásady odpovídajícím způsobem.
> Autorizační hlavičky nejsou nikdy zobrazit vstupy nebo výstupy. Takže pokud tajného kódu je použita tam, není možné získat tento tajný kód.

Další informace o zabezpečení parametry v logice definic aplikací, najdete v části [zabezpečení parametry v logice aplikace definice](#secure-parameters-workflow) později na této stránce.

Pokud používáte automatizaci nasazení s [šablony nasazení Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md#parameters), můžete použít také zabezpečené parametry v těchto šablon. Například můžete použít parametry pro získání tajné kódy KeyVault při vytváření aplikace logiky. Vaše definice šablony nasazení má vlastní `parameters` oddílu, nezávisle na aplikaci logiky `parameters` oddílu. Další informace o zabezpečení parametrů v šablonách nasazení najdete v tématu [zabezpečení parametrů v šablonách nasazení](#secure-parameters-deployment-template) později na této stránce.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>Parametry v logice zabezpečení definic aplikací

Kvůli ochraně citlivých informací v definici pracovního postupu aplikace logiky, používají zabezpečené parametry tak tyto informace není viditelný po uložení aplikace logiky. Předpokládejme například, že používáte `Basic` ověřování v definici akce HTTP. Tento příklad zahrnuje `parameters` oddíl, který definuje parametry pro definici akcí a navíc `authentication` oddíl, který přijímá `username` a `password` hodnoty parametrů. K poskytnutí hodnot pro tyto parametry, můžete použít soubor oddělené parametry, například:

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "http://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

Pokud používáte tajné kódy, můžete získat tyto tajné kódy v době nasazení pomocí [trezor klíčů Azure Resource Manageru](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Zabezpečené parametrů v šablonách nasazení Azure Resource Manageru

Tento příklad ukazuje nasazení šablony Resource Manageru, která používá více než jeden parametr modulu runtime s `securestring` typu:

* `armTemplatePasswordParam`, který je vstupem pro definici aplikace logiky `logicAppWfParam` parametr

* `logicAppWfParam`, což je zadání údajů pro akce HTTP pomocí základního ověřování

Tento příklad obsahuje vnitřní `parameters` části, která patří do definice pracovního postupu aplikace logiky a vnějším `parameters` oddílu, který patří do šablony nasazení. K určení hodnoty prostředí pro parametry, můžete použít soubor oddělené parametry. 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "http://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

Pokud používáte tajné kódy, můžete získat tyto tajné kódy v době nasazení pomocí [trezor klíčů Azure Resource Manageru](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>Zabezpečený přístup ke službám, které přijímají požadavky

Tady je několik způsobů, jak se dají zabezpečit libovolný koncový bod, kde vaše aplikace logiky potřebuje přístup a požadavky bude odesílat.

### <a name="add-authentication-on-outbound-requests"></a>Přidání ověřování pro odchozí požadavky

Při práci s HTTP, HTTP + Swagger (Open API) nebo akce Webhooku, přidejte do žádosti odeslané aplikací logiky ověřování. Můžete například základní ověřování, ověřování pomocí certifikátu nebo ověřování Azure Active Directory. Další informace najdete v tématu [ověření aktivační události nebo akce](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

### <a name="restrict-access-to-logic-app-ip-addresses"></a>Omezení přístupu na IP adresy aplikace logiky

Všechna volání z aplikace logiky pocházet z konkrétní určené IP adres podle oblasti. Můžete přidat filtrování, které přijímají požadavky jenom z těchto IP adres. Tyto IP adresy, naleznete v tématu [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

### <a name="secure-on-premises-connectivity"></a>Zabezpečené místní připojení

Služba Azure Logic Apps poskytuje integraci s těmito službami pro bezpečné a spolehlivé místní komunikace.

#### <a name="on-premises-data-gateway"></a>Místní brána dat

Mnoho spravované konektory pro Azure Logic Apps poskytuje zabezpečené připojení k místním systémům, jako je například systém souborů, SQL, SharePoint, DB2 a další. Brána odešle data z místních zdrojů přes šifrované kanálů prostřednictvím služby Azure Service Bus. Veškerý provoz pochází jako zabezpečené odchozího provozu z agenta brány. Přečtěte si [jak místní brána dat funguje](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) poskytuje možnosti místního připojení, jako je například virtuální privátní sítě site-to-site a ExpressRoute integrace pro zabezpečené proxy a komunikace s místními systémy. V návrháři aplikace logiky můžete vybrat rozhraní API z pracovního postupu aplikace logiky, vystavený službou API Management poskytuje rychlý přístup k místním systémům.

## <a name="next-steps"></a>Další postup

* [Vytvoření šablony nasazení](logic-apps-create-deploy-template.md)  
* [Zpracování výjimek](logic-apps-exception-handling.md)  
* [Monitorování aplikací logiky](logic-apps-monitor-your-logic-apps.md)  
* [Diagnostikování selhání aplikací logiky a problémů](logic-apps-diagnosing-failures.md)  
