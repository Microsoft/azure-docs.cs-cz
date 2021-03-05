---
title: Zabezpečení přístupu a dat
description: Zabezpečený přístup ke vstupům, výstupům, aktivačním událostem založeným na požadavcích, historii spuštění, úlohám správy a přístupu k dalším prostředkům v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla, rarayudu
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: 21edde3eba76b565332acb9c67225f3bbb0fe803
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177279"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Zabezpečený přístup a data v Azure Logic Apps

Azure Logic Apps spoléhá na [Azure Storage](../storage/index.yml) ukládání a automatické [šifrování dat v klidovém umístění](../security/fundamentals/encryption-atrest.md). Toto šifrování chrání vaše data a pomáhá splnit závazky zabezpečení vaší organizace a dodržování předpisů. Ve výchozím nastavení používá Azure Storage k šifrování vašich dat klíče spravované Microsoftem. Další informace najdete v tématu [Azure Storage šifrování pro](../storage/common/storage-service-encryption.md)neaktivní neaktivní data.

K řízení přístupu a ochraně citlivých dat v Azure Logic Apps můžete nastavit další zabezpečení v těchto oblastech:

* [Přístup pro příchozí volání aktivačních událostí na základě požadavků](#secure-inbound-requests)
* [Přístup k operacím aplikace logiky](#secure-operations)
* [Přístup ke vstupům a výstupům historie spouštění](#secure-run-history)
* [Přístup ke vstupům parametrů](#secure-action-parameters)
* [Přístup pro odchozí hovory na jiné služby a systémy](#secure-outbound-requests)
* [Blokovat vytváření připojení pro konkrétní konektory](#block-connections)
* [Pokyny k izolaci pro Logic Apps](#isolation-logic-apps)
* [Základní hodnoty zabezpečení Azure pro Azure Logic Apps](../logic-apps/security-baseline.md)

Další informace o zabezpečení v Azure najdete v těchto tématech:

* [Přehled šifrování v Azure](../security/fundamentals/encryption-overview.md)
* [Šifrování dat Azure – v klidovém případě](../security/fundamentals/encryption-atrest.md)
* [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>Přístup pro příchozí volání aktivačních událostí na základě požadavků

Příchozí volání, která aplikace logiky přijímá prostřednictvím triggeru založeného na žádostech, jako je Trigger [žádosti](../connectors/connectors-native-reqres.md) nebo Trigger [Webhooku protokolu HTTP](../connectors/connectors-native-webhook.md) , podporují šifrování a jsou zabezpečeny pomocí protokolu [TLS (Transport Layer Security) 1,2](https://en.wikipedia.org/wiki/Transport_Layer_Security)minimálně, dříve označovaného jako SSL (Secure Sockets Layer) (SSL). Logic Apps vynutila tuto verzi při přijetí příchozího volání triggeru požadavku nebo zpětného volání do triggeru nebo akce Webhooku protokolu HTTP. Pokud se zobrazí chyby handshake TLS, ujistěte se, že používáte TLS 1,2. Další informace najdete v tématu [řešení problému s protokolem TLS 1,0](/security/solving-tls1-problem).

Příchozí volání podporují tyto šifrovací sady:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Tady jsou další způsoby, jak omezit přístup k aktivačním událostem, které přijímají příchozí volání do vaší aplikace logiky, aby mohli volat aplikaci logiky jenom autorizovaní klienti:

* [Generování sdílených přístupových podpisů (SAS)](#sas)
* [Povolit Azure Active Directory Open Authentication (Azure AD OAuth)](#enable-oauth)
* [Vystavení aplikace logiky pomocí Azure API Management](#azure-api-management)
* [Omezit příchozí IP adresy](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generování sdílených přístupových podpisů (SAS)

Každý koncový bod žádosti v aplikaci logiky má [sdílený přístupový podpis (SAS)](/rest/api/storageservices/constructing-a-service-sas) v adrese URL koncového bodu, který se skládá z tohoto formátu:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Každá adresa URL obsahuje `sp` `sv` parametr dotazu, a, `sig` jak je popsáno v této tabulce:

| Parametr dotazu | Popis |
|-----------------|-------------|
| `sp` | Určuje oprávnění povolených metod HTTP, které se mají použít. |
| `sv` | Určuje verzi SAS, která se má použít pro generování podpisu. |
| `sig` | Určuje podpis, který se má použít pro ověřování přístupu k triggeru. Tento podpis se vygeneruje pomocí algoritmu SHA256 s tajným přístupovým klíčem na všech cestách a vlastnostech adresy URL. Tento klíč zůstane zašifrovaný a uložený v aplikaci logiky, který není přístupný nebo publikovaný. Vaše aplikace logiky autorizuje jenom ty triggery, které obsahují platný podpis vytvořený s tajným klíčem. |
|||

Příchozí volání koncového bodu požadavku můžou použít jenom jedno schéma autorizace, buď SAS, nebo [Azure Active Directory otevřít ověřování](#enable-oauth). I když použití jednoho schématu nezakáže jiné schéma, při použití obou schémat zároveň dojde k chybě, protože služba neví, které schéma zvolit.

Další informace o zabezpečení přístupu pomocí SAS najdete v těchto oddílech v tomto tématu:

* [Znovu vygenerovat přístupové klíče](#access-keys)
* [Vytvoření adres URL zpětného volání pro vypršení platnosti](#expiring-urls)
* [Vytváření adres URL s primárním nebo sekundárním klíčem](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Znovu vygenerovat přístupové klíče

Pokud chcete vygenerovat nový klíč pro přístup k zabezpečení, použijte Azure REST API nebo Azure Portal. Všechny dříve generované adresy URL, které používají starý klíč, jsou neověřené a už nebudou mít autorizaci k aktivaci aplikace logiky. Adresy URL, které načtete po obnovení, se podepisují pomocí nového přístupového klíče.

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky, která má klíč, který chcete znovu vygenerovat.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **přístupové klíče**.

1. Vyberte klíč, který chcete znovu vygenerovat a dokončit proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Vytvoření adres URL zpětného volání pro vypršení platnosti

Pokud sdílíte adresu URL koncového bodu pro aktivační událost na základě požadavku s ostatními stranami, můžete vygenerovat adresy URL zpětného volání, které používají konkrétní klíče a mají datum vypršení platnosti. Tímto způsobem můžete plynule vrátit klíče nebo omezit přístup k aktivaci aplikace logiky na základě konkrétního časového rozmezí. Chcete-li zadat datum vypršení platnosti adresy URL, použijte [Logic Apps REST API](/rest/api/logic/workflowtriggers), například:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

V těle zahrňte `NotAfter` vlastnost pomocí řetězce data JSON. Tato vlastnost vrátí adresu URL zpětného volání, která je platná pouze do `NotAfter` data a času.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Vytváření adres URL s primárním nebo sekundárním tajným klíčem

Když vygenerujete nebo vypíšete adresy URL zpětného volání pro aktivační událost na základě požadavku, můžete zadat klíč, který se má použít k podepsání adresy URL. Chcete-li vygenerovat adresu URL, která je podepsána určitým klíčem, použijte [Logic Apps REST API](/rest/api/logic/workflowtriggers), například:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

V těle zahrňte `KeyType` vlastnost buď `Primary` nebo `Secondary` . Tato vlastnost vrátí adresu URL, která je podepsána zadaným klíčem zabezpečení.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>Povolit Azure Active Directory Open Authentication (Azure AD OAuth)

Pro příchozí volání koncového bodu, který je vytvořený triggerem založeným na žádostech, můžete povolit [službu Azure AD OAuth](../active-directory/develop/index.yml) tím, že definujete nebo přidáte zásady autorizace pro vaši aplikaci logiky. Tímto způsobem příchozí volání využívají [přístupové tokeny](../active-directory/develop/access-tokens.md) OAuth pro autorizaci.

Když aplikace logiky obdrží příchozí požadavek, který obsahuje přístupový token OAuth, služba Azure Logic Apps porovnává deklarace identity tokenů s deklaracemi, které jsou zadané jednotlivými zásadami autorizace. Pokud existuje shoda mezi deklaracemi tokenu a všemi deklaracemi v alespoň jedné zásadě, autorizace pro příchozí požadavek bude úspěšná. Token může mít více deklarací identity než číslo zadané v zásadách autorizace.

#### <a name="considerations-before-you-enable-azure-ad-oauth"></a>Předpoklady před povolením služby OAuth pro Azure AD

* Příchozí volání koncového bodu může používat jenom jedno schéma autorizace, buď Azure AD OAuth, nebo [sdílený přístupový podpis (SAS)](#sas). I když použití jednoho schématu nezakáže jiné schéma, při použití obou schémat zároveň dojde k chybě, protože služba Logic Apps neví, jaké schéma zvolit.

* Pro přístupové tokeny Azure AD OAuth se podporují jenom schémata autorizace [typu nosiče](../active-directory/develop/active-directory-v2-protocols.md#tokens) , což znamená, že `Authorization` Hlavička pro přístupový token musí určovat `Bearer` typ.

* Aplikace logiky je omezená na maximální počet zásad autorizace. Každá zásada autorizace má také maximální počet [deklarací identity](../active-directory/develop/developer-glossary.md#claim). Další informace najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Zásady autorizace musí zahrnovat aspoň deklaraci identity **vystavitele** , která má hodnotu začínající buď `https://sts.windows.net/` nebo `https://login.microsoftonline.com/` (OAuth v2) jako ID vystavitele Azure AD.

  Předpokládejme například, že vaše aplikace logiky má zásady autorizace, které vyžadují dva typy deklarací identity, **cílovou skupinu** a **Vystavitel**. Tento vzorový [oddíl](../active-directory/develop/access-tokens.md#payload-claims) pro dekódování přístupového tokenu obsahuje oba typy deklarací, kde `aud` je hodnota **cílové skupiny** a `iss` je hodnotou **vystavitele** :

  ```json
  {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
      "iat": 1582056988,
      "nbf": 1582056988,
      "exp": 1582060888,
      "_claim_names": {
         "groups": "src1"
      },
      "_claim_sources": {
         "src1": {
            "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
         }
      },
      "acr": "1",
      "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
      "amr": [
         "rsa",
         "mfa"
      ],
      "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
      "appidacr": "2",
      "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
      "family_name": "Sophia Owen",
      "given_name": "Sophia Owen (Fabrikam)",
      "ipaddr": "167.220.2.46",
      "name": "sophiaowen",
      "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
      "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
      "puid": "1003000000098FE48CE",
      "scp": "user_impersonation",
      "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
      "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
      "unique_name": "SophiaOwen@fabrikam.com",
      "upn": "SophiaOwen@fabrikam.com",
      "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
      "ver": "1.0"
   }
   ```

#### <a name="enable-azure-ad-oauth-for-your-logic-app"></a>Povolení služby Azure AD OAuth pro vaši aplikaci logiky

Použijte následující postup pro Azure Portal nebo šablonu Azure Resource Manager:

<a name="define-authorization-policy-portal"></a>

#### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V [Azure Portal](https://portal.azure.com)přidejte do aplikace logiky jednu nebo více zásad autorizace:

1. V [Azure Portal](https://portal.microsoft.com)vyhledejte a otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **autorizace**. Po otevření podokna autorizace vyberte **Přidat zásadu**.

   ![Vyberte Authorization > přidat zásadu.](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Zadejte informace o zásadách autorizace zadáním typů a hodnot [deklarací identity](../active-directory/develop/developer-glossary.md#claim) , které vaše aplikace logiky očekává v přístupovém tokenu, který prezentuje každé příchozí volání triggeru požadavku:

   ![Zadání informací pro zásady autorizace](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Název zásad** | Ano | Název, který chcete použít pro zásady autorizace |
   | **Žádosti** | Ano | Typy a hodnoty deklarací, které vaše aplikace logiky přijímá při příchozích voláních. Hodnota deklarace identity je omezená na [maximální počet znaků](logic-apps-limits-and-config.md#authentication-limits). Tady jsou dostupné typy deklarací identity: <p><p>- **Stavil** <br>- **Osoby** <br>- **Závislosti** <br>- **ID JWT** (JSON web token identifikátor) <p><p>Seznam **deklarací** musí obsahovat minimálně deklaraci identity **vystavitele** , která má hodnotu začínající `https://sts.windows.net/` nebo `https://login.microsoftonline.com/` jako ID vystavitele Azure AD. Další informace o těchto typech deklarací identity najdete [v tématu deklarace identity v tokenech zabezpečení Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Můžete také zadat vlastní typ a hodnotu deklarace identity. |
   |||

1. Pokud chcete přidat další deklaraci identity, vyberte si z těchto možností:

   * Pokud chcete přidat další typ deklarace identity, vyberte **Přidat standardní deklaraci identity**, vyberte typ deklarace a zadejte hodnotu deklarace identity.

   * Pokud chcete přidat vlastní deklaraci identity, vyberte **Přidat vlastní deklaraci identity**. Další informace najdete v tématu [jak poskytnout volitelné deklarace identity vaší aplikaci](../active-directory/develop/active-directory-optional-claims.md). Vaše vlastní deklarace identity se pak uloží jako součást ID JWT; například `"tid": "72f988bf-86f1-41af-91ab-2d7cd011db47"` . 

1. Pokud chcete přidat další zásady autorizace, vyberte **Přidat zásadu**. Zopakováním předchozích kroků zásadu nastavte.

1. Jakmile budete mít hotovo, vyberte **Uložit**.

1. Chcete-li zahrnout `Authorization` hlavičku z přístupového tokenu v výstupech triggerů na základě požadavků, přečtěte si část [zahrnutí "autorizace" v výstupech triggeru žádosti](#include-auth-header).


Vlastnosti pracovního postupu, jako jsou zásady, se nezobrazují v zobrazení kódu vaší aplikace logiky v Azure Portal. Chcete-li získat přístup k zásadám prostřednictvím kódu programu, zavolejte následující rozhraní API prostřednictvím Azure Resource Manager (ARM): `https://management.azure.com/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Logic/workflows/{your-workflow-name}?api-version=2016-10-01&_=1612212851820` . Ujistěte se, že nahradíte zástupné hodnoty pro ID předplatného Azure, název skupiny prostředků a název pracovního postupu.


<a name="define-authorization-policy-template"></a>

#### <a name="resource-manager-template"></a>[Šablona Správce prostředků](#tab/azure-resource-manager)

V šabloně ARM Definujte zásadu autorizace podle těchto kroků a syntaxe níže:

1. V `properties` části pro [definici prostředků vaší aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)přidejte `accessControl` objekt, pokud žádný neexistuje, který obsahuje `triggers` objekt.

   Další informace o objektu najdete v `accessControl` tématu věnovaném [omezení rozsahů příchozích IP adres v šablonách Azure Resource Manager](#restrict-inbound-ip-template) a [odkazech na šablonu Microsoft. Logic](/azure/templates/microsoft.logic/2019-05-01/workflows).

1. Do `triggers` objektu přidejte `openAuthenticationPolicies` objekt, který obsahuje `policies` objekt, ve kterém definujete jednu nebo více zásad autorizace.

1. Zadejte název zásady autorizace, nastavte typ zásady na `AAD` a přidejte `claims` pole, ve kterém zadáte jeden nebo více typů deklarací identity.

   `claims`Pole musí obsahovat minimálně typ deklarace vystavitele, u kterého nastavíte `name` vlastnost deklarace na `iss` a nastavíte, aby byla `value` začínat `https://sts.windows.net/` nebo `https://login.microsoftonline.com/` jako ID vystavitele Azure AD. Další informace o těchto typech deklarací identity najdete [v tématu deklarace identity v tokenech zabezpečení Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Můžete také zadat vlastní typ a hodnotu deklarace identity.

1. Chcete-li zahrnout `Authorization` hlavičku z přístupového tokenu v výstupech triggerů na základě požadavků, přečtěte si část [zahrnutí "autorizace" v výstupech triggeru žádosti](#include-auth-header).

Tady je syntaxe, která se má provést:

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "value": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

---

<a name="include-auth-header"></a>

#### <a name="include-authorization-header-in-request-trigger-outputs"></a>Zahrnout do výstupů triggerů žádosti hlavičku Authorization

Pro Logic Apps, které [umožňují Azure Active Directory Open Authentication (Azure AD OAuth)](#enable-oauth) pro autorizaci příchozích volání s přístupem k aktivačním událostem založeným na žádostech, můžete povolit aktivační události triggeru požadavku nebo aktivační události Webhooku protokolu HTTP, které budou zahrnovat `Authorization` hlavičku z přístupového tokenu OAuth. V základní definici JSON triggeru přidejte a nastavte `operationOptions` vlastnost na `IncludeAuthorizationHeadersInOutputs` . Tady je příklad triggeru žádosti:

```json
"triggers": {
   "manual": {
      "inputs": {
         "schema": {}
      },
      "kind": "Http",
      "type": "Request",
      "operationOptions": "IncludeAuthorizationHeadersInOutputs"
   }
}
```

Další informace najdete v těchto tématech:

* [Referenční dokumentace schématu pro aktivační události a typy akcí – Trigger žádosti](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)
* [Referenční dokumentace schématu pro typy triggerů a akcí – Trigger Webhooku protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Referenční dokumentace schématu pro triggery a typy akcí – možnosti operací](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>Vystavení aplikace logiky pomocí Azure API Management

Pokud chcete do aplikace logiky přidat další [ověřovací protokoly](../active-directory/develop/authentication-vs-authorization.md) , zvažte použití služby [Azure API Management](../api-management/api-management-key-concepts.md) . Tato služba vám pomůže vystavit aplikaci logiky jako rozhraní API a nabízí rozšířené monitorování, zabezpečení, zásady a dokumentaci pro libovolný koncový bod. API Management může vystavit veřejný nebo privátní koncový bod pro vaši aplikaci logiky. Pokud chcete autorizovat přístup k tomuto koncovému bodu, můžete k autorizaci přístupu k tomuto koncovému bodu použít Azure AD OAuth, [klientský certifikát](#client-certificate-authentication)nebo jiné standardy zabezpečení. Když API Management obdrží požadavek, služba pošle požadavek do vaší aplikace logiky, což také vede k potřebným transformacím nebo omezením na cestě. Chcete-li povolit pouze API Management volání aplikace logiky, můžete [omezit příchozí IP adresy vaší aplikace logiky](#restrict-inbound-ip).

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Omezit příchozí IP adresy

Spolu se sdíleným přístupovým podpisem (SAS) můžete chtít konkrétně omezit klienty, kteří můžou zavolat aplikaci logiky. Pokud například spravujete koncový bod žádosti pomocí [Azure API Management](../api-management/api-management-key-concepts.md), můžete aplikaci logiky omezit tak, aby přijímala požadavky pouze z IP adresy pro [instanci služby API Management, kterou vytvoříte](../api-management/get-started-create-service-instance.md).

Bez ohledu na IP adresy, které zadáte, můžete pořád spustit aplikaci logiky, která má aktivační událost na základě požadavků, a to pomocí [Logic Apps REST API: triggery pracovního postupu – spustit](/rest/api/logic/workflowtriggers/run) požadavek nebo pomocí API Management. Tento scénář ale pořád vyžaduje [ověřování](../active-directory/develop/authentication-vs-authorization.md) proti REST API Azure. Všechny události se zobrazí v protokolu auditu Azure. Ujistěte se, že jste nastavili zásady řízení přístupu odpovídajícím způsobem.

Pokud chcete omezit příchozí IP adresy pro vaši aplikaci logiky, použijte následující postup pro Azure Portal nebo šablonu Azure Resource Manager:

<a name="restrict-inbound-ip-portal"></a>

#### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V [Azure Portal](https://portal.azure.com)má tento filtr vliv na triggery *a* akce v rozporu s popisem na portálu v části **povolené příchozí IP adresy**. Chcete-li tento filtr nastavit samostatně pro aktivační události a pro akce, použijte `accessControl` objekt v šabloně Azure Resource Manager pro vaši aplikaci logiky nebo [Logic Apps REST API: pracovní postup – operace vytvořit nebo aktualizovat](/rest/api/logic/workflows/createorupdate).

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **Nastavení pracovního postupu**.

1. V části **Konfigurace řízení přístupu** v části **povolené příchozí IP adresy** vyberte cestu k vašemu scénáři:

   * Aby se aplikace logiky mohla volat jenom jako vnořená aplikace logiky pomocí předdefinované [Azure Logic Apps akce](../logic-apps/logic-apps-http-endpoint.md), vyberte **jenom další Logic Apps**, která funguje *jenom* v případě, že použijete akci **Azure Logic Apps** k volání vnořené aplikace logiky.

     Tato možnost zapíše prázdné pole do prostředku aplikace logiky a vyžaduje, aby se vnořená aplikace logiky spustila jenom volání z nadřazených aplikací logiky, které používají vestavěnou **Azure Logic Apps** akci.

   * Pokud chcete aplikaci logiky volat jenom jako vnořenou aplikaci pomocí akce HTTP, vyberte **konkrétní rozsahy IP adres**, *ne* **jenom jiné Logic Apps**. Po zobrazení pole **rozsahy IP adres pro triggery** zadejte [odchozí IP adresy](../logic-apps/logic-apps-limits-and-config.md#outbound)nadřazené aplikace logiky. Platný rozsah IP adres používá tyto formáty: *x. x. x. x/x* nebo *x. x. x. x-x. x.* x. x.

     > [!NOTE]
     > Použijete-li **jedinou jinou možnost Logic Apps** a akci HTTP pro volání vnořené aplikace logiky, volání je blokováno a zobrazí se chyba "401 Neautorizováno".

   * U scénářů, ve kterých chcete omezit příchozí volání z jiných IP adres, když se zobrazí okno **rozsahy IP adres pro triggery** , zadejte rozsahy IP adres, které aktivační událost akceptuje. Platný rozsah IP adres používá tyto formáty: *x. x. x. x/x* nebo *x. x. x. x-x. x.* x. x.

1. Volitelně můžete v části **omezit volání na získat vstupní a výstupní zprávy z historie spouštění na zadané IP adresy** zadat rozsahy IP adres pro příchozí volání, která budou mít přístup k vstupním a výstupním zprávám v historii spuštění.

<a name="restrict-inbound-ip-template"></a>

#### <a name="resource-manager-template"></a>[Šablona Správce prostředků](#tab/azure-resource-manager)

V šabloně ARM určete povolené rozsahy příchozích IP adres v definici prostředků vaší aplikace logiky pomocí `accessControl` části. V této části použijte případně `triggers` `actions` volitelné `contents` oddíly, včetně `allowedCallerIpAddresses` oddílu s `addressRange` vlastností a nastavte hodnotu vlastnosti na povolený rozsah IP adres ve formátu *x. x. x* . x. x. x. x. x *. x. x-x. x* . x. x.

* Pokud vaše vnořená aplikace logiky používá **jedinou možnost Logic Apps** , která umožňuje příchozí volání jenom z jiných Logic Apps, které používají vestavěnou Azure Logic Apps akci, nastavte `allowedCallerIpAddresses` vlastnost na prázdné pole (**[]**) a *vynechejte* `addressRange` vlastnost.

* Pokud vaše vnořená aplikace logiky používá pro jiná příchozí volání možnost **konkrétní rozsahy IP** adres, jako jsou například jiné aplikace logiky, které používají akci HTTP, zahrňte `allowedCallerIpAddresses` část a nastavte `addressRange` vlastnost na povolený rozsah IP adres.

Tento příklad ukazuje definici prostředků pro vnořenou aplikaci logiky, která umožňuje příchozí volání pouze z aplikací logiky, které používají vestavěnou Azure Logic Apps akci:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": []
               },
               "actions": {
                  "allowedCallerIpAddresses": []
               },
               // Optional
               "contents": {
                  "allowedCallerIpAddresses": []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

Tento příklad ukazuje definici prostředků pro vnořenou aplikaci logiky, která umožňuje příchozí volání z aplikací logiky, které používají akci HTTP:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Přístup k operacím aplikace logiky

Můžete povolit pouze konkrétní uživatele nebo skupiny, aby mohli spouštět konkrétní úkoly, jako je správa, úpravy a zobrazení Logic Apps. Pokud chcete řídit svá oprávnění, použijte [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) , abyste členům v rámci předplatného Azure mohli přiřadit přizpůsobené nebo předdefinované role:

* [Přispěvatel aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-contributor): umožňuje spravovat Logic Apps, ale nemůžete pro ně měnit přístup.

* [Operátor aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-operator): umožňuje číst, povolit a zakázat Logic Apps, ale nemůžete je upravovat ani aktualizovat.

Pokud chcete ostatním uživatelům zabránit ve změně nebo odstranění vaší aplikace logiky, můžete použít [Azure Resource Lock](../azure-resource-manager/management/lock-resources.md). Tato možnost zabraňuje ostatním změnám a odstraňování produkčních prostředků.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Přístup k datům historie spouštění

Během provádění aplikace logiky se všechna data [šifrují během přenosu](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) pomocí protokolu TLS (Transport Layer Security) a [v klidovém](../security/fundamentals/encryption-atrest.md)stavu. Až se vaše aplikace logiky dokončí, můžete zobrazit historii pro toto spuštění, včetně kroků, které byly spuštěny spolu se stavem, dobou trvání, vstupy a výstupy pro každou akci. Tato bohatá podrobnosti poskytuje přehled o tom, jak vaše aplikace logiky běžela a kde můžete začít řešit problémy, ke kterým dojde.

Když si zobrazíte historii spuštění vaší aplikace logiky, Logic Apps ověří váš přístup a pak poskytuje odkazy na vstupy a výstupy pro žádosti a odpovědi pro každé spuštění. U akcí, které zpracovávají všechna hesla, tajné klíče, klíče nebo jiné citlivé informace, ale chcete ostatním uživatelům zabránit v prohlížení těchto dat a přístupu k nim. Například pokud vaše aplikace logiky získá tajný kód z [Azure Key Vault](../key-vault/general/overview.md) , který se má použít při ověřování akce HTTP, chcete tento tajný klíč skrýt ze zobrazení.

K řízení přístupu k vstupům a výstupům v historii spuštění aplikace logiky máte tyto možnosti:

* [Omezte přístup pomocí rozsahu IP adres](#restrict-ip).

  Tato možnost vám pomůže zabezpečit přístup k historii spouštění na základě požadavků z konkrétního rozsahu IP adres.

* [Zabezpečte data v historii spouštění pomocí zmatenosti](#obfuscate).

  V mnoha triggerech a akcích můžete zabezpečit vstupy, výstupy nebo obojí v historii spuštění aplikace logiky.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Omezení přístupu podle rozsahu IP adres

V historii spuštění aplikace logiky můžete omezit přístup k vstupům a výstupům, aby se tato data mohla zobrazit jenom z konkrétních rozsahů IP adres.

Chcete-li například zablokovat komukoli přístup k vstupům a výstupům, zadejte rozsah IP adres, například `0.0.0.0-0.0.0.0` . Pouze osoba s oprávněními správce může toto omezení odebrat, což umožňuje "za běhu" přístup k datům aplikace logiky.

Pokud chcete určit rozsahy povolených IP adres, použijte následující postup pro Azure Portal nebo šablonu Azure Resource Manager:

#### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **Nastavení pracovního postupu**.

1. V části **Konfigurace řízení přístupu**  >  **povolená příchozí IP adresa** vyberte **konkrétní rozsahy IP** adres.

1. V části **rozsahy IP adres pro obsah** zadejte rozsahy IP adres, které budou mít přístup k obsahu ze vstupů a výstupů.

   Platný rozsah IP adres používá tyto formáty: *x. x. x. x/x* nebo *x. x. x. x-x. x.* x. x.

#### <a name="resource-manager-template"></a>[Šablona Správce prostředků](#tab/azure-resource-manager)

V šabloně ARM určete rozsahy IP adres pomocí `accessControl` oddílu s `contents` oddílem v definici prostředků vaší aplikace logiky, například:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
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
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Zabezpečení dat v historii spouštění pomocí zmatení

Mnoho triggerů a akcí má nastavení pro zabezpečení vstupů, výstupů nebo obojího z historie spuštění aplikace logiky. Než použijete tato nastavení, která vám pomůžou zabezpečit tato data, přečtěte si tyto informace:

* Když na Trigger nebo akci skryjete vstupy nebo výstupy, Logic Apps neodesílají zabezpečená data do Azure Log Analytics. Do této aktivační události nebo akce pro monitorování nemůžete také přidat [sledované vlastnosti](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) .

* [Rozhraní Logic Apps API pro zpracování historie pracovního postupu](/rest/api/logic/) nevrací zabezpečené výstupy.

* Aby bylo možné zabezpečit výstupy z akce, která zakrývá vstupy nebo explicitně překrývá výstupy, ručně zapněte v této akci **zabezpečené výstupy** .

* Ujistěte se, že jste zapnuli **zabezpečené vstupy** nebo **zabezpečené výstupy** v rámci navazujících akcí, u kterých očekáváte, že historie spouštění bude tato data skrývat.

  **Nastavení zabezpečených výstupů**

  Když ručně zapnete **zabezpečené výstupy** v aktivační události nebo akci, Logic Apps tyto výstupy skryje v historii spuštění. Pokud akce pro příjem dat explicitně používá tyto zabezpečené výstupy jako vstupy, Logic Apps skrývá vstupy této akce v historii spuštění, ale *nepovolí* nastavení **zabezpečených vstupů** akce.

  ![Zabezpečené výstupy jako vstupy a přínosy při většině akcí](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Akce vytvořit, analyzovat JSON a odpověď mají pouze nastavení **zabezpečené vstupy** . Když je tato možnost zapnutá, tato nastavení také skryje výstupy těchto akcí. Pokud tyto akce explicitně používají výstupy proti nadřazenému objektu jako vstupy, Logic Apps skrývá vstupy a výstupy těchto akcí, ale *nepovoluje* nastavení **zabezpečených vstupů** těchto akcí. Pokud akce pro příjem dat explicitně používá skryté výstupy z akcí vytvořit, analyzovat JSON nebo odpovědět jako vstupy, Logic Apps *neskryje tyto vstupy a výstupy této akce*.

  ![Zabezpečené výstupy jako vstupy s vlivem na konkrétní akce na základě dat](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Nastavení zabezpečených vstupů**

  Když ručně zapnete **zabezpečené vstupy** v triggeru nebo akci, Logic Apps tyto vstupy v historii spuštění skrýt. Pokud akce pro příjem dat explicitně používá viditelné výstupy z této triggeru nebo akce jako vstupy, Logic Apps skrývá tyto vstupy v historii spuštění, ale *nepovoluje* v této akci **zabezpečené vstupy** a neskrývá výstupy této akce.

  ![Zabezpečené vstupy a důsledky pro všechny akce](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Pokud akce vytvořit, analyzovat JSON a odpověď explicitně použijí viditelné výstupy z triggeru nebo akce, která má zabezpečené vstupy, Logic Apps skryje tyto vstupy a výstupy těchto akcí, ale *nepovolí* nastavení **zabezpečených vstupů** těchto akcí. Pokud akce pro příjem dat explicitně používá skryté výstupy z akcí vytvořit, analyzovat JSON nebo odpovědět jako vstupy, Logic Apps *neskryje tyto vstupy a výstupy této akce*.

  ![Zabezpečené vstupy a důsledky pro konkrétní akce](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Zabezpečení vstupů a výstupů v Návrháři

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

   ![Otevření aplikace logiky v návrháři aplikace logiky](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. V aktivační události nebo akci, kde chcete zabezpečit citlivá data, vyberte tlačítko se třemi tečkami (**...**) a pak vyberte **Nastavení**.

   ![Otevřít nastavení triggeru nebo akce](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Zapněte buď **zabezpečené vstupy**, **zabezpečené výstupy**, nebo obojí. Jakmile budete hotovi, vyberte **Hotovo**.

   ![Zapnutí "zabezpečených vstupů" nebo "zabezpečených výstupů"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Akce nebo aktivační událost nyní zobrazuje ikonu zámku v záhlaví.

   ![Záhlaví akce nebo aktivační události zobrazuje ikonu zámku](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokeny, které představují zabezpečené výstupy z předchozích akcí, také zobrazují ikony zámku. Když například vyberete takový výstup ze seznamu dynamického obsahu, který chcete použít v akci, tento token zobrazí ikonu zámku.

   ![Vyberte token pro zabezpečený výstup.](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Po spuštění aplikace logiky můžete zobrazit historii pro daný běh.

   1. V podokně **Přehled** aplikace logiky vyberte běh, který chcete zobrazit.

   1. V podokně **spuštění aplikace logiky** rozbalte akce, které chcete zkontrolovat.

      Pokud se rozhodnete zakrýt vstupy i výstupy, zobrazí se teď tyto hodnoty jako skryté.

      ![Skryté vstupy a výstupy v historii spuštění](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Zabezpečené vstupy a výstupy v zobrazení kódu

V podkladové aktivační události nebo definici akce přidejte nebo aktualizujte `runtimeConfiguration.secureData.properties` pole pomocí jedné nebo obou hodnot:

* `"inputs"`: Zabezpečuje vstupy v historii spuštění.
* `"outputs"`: Zabezpečuje výstupy v historii spuštění.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Přístup ke vstupům parametrů

Pokud nasazujete v různých prostředích, zvažte Parametrizace hodnot v definici pracovního postupu, které se liší v závislosti na těchto prostředích. Tímto způsobem se můžete vyhnout pevně zakódovaným datům pomocí [šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) k nasazení aplikace logiky, ochraně citlivých dat definováním zabezpečených parametrů a předání těchto dat jako samostatných vstupů prostřednictvím [parametrů šablony](../azure-resource-manager/templates/template-parameters.md) pomocí [souboru parametrů](../azure-resource-manager/templates/parameter-files.md).

Pokud například ověříte akce HTTP pomocí [Azure Active Directory otevřete ověřování](#azure-active-directory-oauth-authentication) (Azure AD OAuth), můžete definovat a zakrýt parametry, které přijímají ID klienta a tajný klíč klienta, které se používají pro ověřování. Pro definování těchto parametrů ve vaší aplikaci logiky použijte `parameters` část v definici pracovního postupu vaší aplikace logiky a šablonu správce prostředků pro nasazení. Abychom vám pomohli zabezpečit hodnoty parametrů, které nechcete zobrazit při úpravách aplikace logiky nebo zobrazení historie spouštění, definujte parametry `securestring` pomocí `secureobject` typu nebo a podle potřeby použijte kódování. Parametry, které mají tento typ, nejsou vráceny s definicí prostředků a nejsou přístupné při zobrazení prostředku po nasazení. Pro přístup k těmto hodnotám parametrů za běhu použijte `@parameters('<parameter-name>')` výraz uvnitř definice pracovního postupu. Tento výraz je vyhodnocen pouze za běhu a je popsán [jazykem definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Pokud použijete parametr v hlavičce nebo těle žádosti, tento parametr může být viditelný při zobrazení historie spuštění vaší aplikace logiky a odchozího požadavku HTTP. Ujistěte se, že jste odpovídajícím způsobem také nastavili zásady přístupu k obsahu.
> K skrytí vstupů a výstupů v historii spuštění můžete také použít [zmatení](#obfuscate) . Autorizační hlavičky nejsou nikdy viditelné prostřednictvím vstupů nebo výstupů. Takže pokud se tam používá tajný klíč, nedá se tento tajný klíč získat.

Další informace najdete v těchto oddílech v tomto tématu:

* [Zabezpečené parametry v definicích pracovních postupů](#secure-parameters-workflow)
* [Zabezpečení dat v historii spouštění pomocí zmatení](#obfuscate)

Pokud [automatizujete nasazení pro Logic Apps pomocí Správce prostředků šablony](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete definovat zabezpečené [parametry šablony](../azure-resource-manager/templates/template-parameters.md), které jsou vyhodnocovány při nasazení pomocí `securestring` `secureobject` typů a. Chcete-li definovat parametry šablony, použijte sekci nejvyšší úrovně šablony `parameters` , která je oddělená a odlišná od oddílu definice pracovního postupu `parameters` . Chcete-li zadat hodnoty parametrů šablony, použijte samostatný [soubor parametrů](../azure-resource-manager/templates/parameter-files.md).

Pokud například používáte tajné kódy, můžete definovat a používat zabezpečené parametry šablony, které tyto tajné kódy načítají z [Azure Key Vault](../key-vault/general/overview.md) při nasazení. Pak můžete odkazovat na Trezor klíčů a tajný kód v souboru parametrů. Další informace najdete v těchto tématech:

* [Předejte citlivé hodnoty při nasazení pomocí Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Parametry zabezpečení v šablonách Azure Resource Manager](#secure-parameters-deployment-template) dále v tomto tématu.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Zabezpečené parametry v definicích pracovních postupů

Pokud chcete chránit citlivé informace v definici pracovního postupu vaší aplikace logiky, použijte zabezpečené parametry, aby tyto informace nebyly po uložení aplikace logiky viditelné. Předpokládejme například, že máte akci HTTP vyžaduje základní ověřování, které používá uživatelské jméno a heslo. V definici pracovního postupu `parameters` oddíl definuje `basicAuthPasswordParam` `basicAuthUsernameParam` parametry a pomocí `securestring` typu. Definice akce pak odkazuje na tyto parametry v `authentication` části.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
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

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Zabezpečené parametry v šablonách Azure Resource Manager

[Šablona správce prostředků](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) pro aplikaci logiky obsahuje několik `parameters` oddílů. K ochraně hesel, klíčů, tajných kódů a dalších citlivých informací můžete definovat zabezpečené parametry na úrovni šablony a definice pracovního postupu pomocí `securestring` typu nebo `secureobject` . Tyto hodnoty pak můžete uložit v [Azure Key Vault](../key-vault/general/overview.md) a použít [soubor parametrů](../azure-resource-manager/templates/parameter-files.md) k odkazování na Trezor klíčů a tajný kód. Vaše šablona pak tyto informace načte při nasazení. Další informace najdete v tématu [předání citlivých hodnot při nasazení pomocí Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Zde jsou další informace o těchto `parameters` oddílech:

* V nejvyšší úrovni šablony `parameters` definuje oddíl parametry pro hodnoty, které šablona používá při *nasazení*. Například tyto hodnoty mohou zahrnovat připojovací řetězce pro konkrétní prostředí nasazení. Tyto hodnoty pak můžete uložit do samostatného [souboru parametrů](../azure-resource-manager/templates/parameter-files.md), což usnadňuje změnu těchto hodnot.

* V definici prostředků vaší aplikace logiky, ale mimo definici pracovního postupu, `parameters` určuje část hodnoty pro parametry definice pracovního postupu. V této části můžete přiřadit tyto hodnoty pomocí výrazů šablony, které odkazují na parametry šablony. Tyto výrazy jsou vyhodnocovány při nasazení.

* V rámci definice pracovního postupu `parameters` oddíl definuje parametry, které vaše aplikace logiky používá za běhu. Pak můžete odkazovat na tyto parametry uvnitř pracovního postupu aplikace logiky pomocí výrazů definice pracovního postupu, které jsou vyhodnocovány za běhu.

Tento příklad šablony s více zabezpečenými definicemi parametrů, které používají `securestring` Typ:

| Název parametru | Description |
|----------------|-------------|
| `TemplatePasswordParam` | Parametr šablony, který přijímá heslo, které je pak předáno parametru definice pracovního postupu `basicAuthPasswordParam` |
| `TemplateUsernameParam` | Parametr šablony, který přijímá uživatelské jméno, které je pak předáno parametru definice pracovního postupu `basicAuthUserNameParam` |
| `basicAuthPasswordParam` | Parametr definice pracovního postupu, který přijímá heslo pro základní ověřování v akci HTTP |
| `basicAuthUserNameParam` | Parametr definice pracovního postupu, který přijímá uživatelské jméno pro základní ověřování v akci HTTP |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
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
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
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
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>Přístup pro odchozí hovory na jiné služby a systémy

V závislosti na schopnosti cílového koncového bodu odchozí hovory odesílané [triggerem http nebo akcí http](../connectors/connectors-native-http.md)podporují šifrování a jsou zabezpečené pomocí protokolu [TLS (Transport Layer Security) 1,0, 1,1 nebo 1,2](https://en.wikipedia.org/wiki/Transport_Layer_Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL). Logic Apps vyjednávat s cílovým koncovým bodem pomocí nejvyšší možné verze, která je podporována. Pokud například cílový koncový bod podporuje 1,2, aktivační událost HTTP nebo akce nejprve používá 1,2. V opačném případě konektor používá další nejvyšší podporovanou verzi.

Tady jsou informace o certifikátech podepsaných držitelem TLS/SSL:

* Pro Logic Apps v globálním prostředí Azure s více klienty nepovoluje konektor HTTP konektor protokolu TLS/SSL podepsaný svým držitelem. Pokud vaše aplikace logiky provede volání HTTP na server a uvede certifikát podepsaný svým držitelem (TLS/SSL), volání HTTP se nezdařila s `TrustFailure` chybou.

* Pro Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)umožňuje konektor http certifikáty podepsané svým držitelem pro ověřování TLS/SSL. Je však třeba nejprve [Povolit podporu certifikátu podepsaného svým držitelem](../logic-apps/create-integration-service-environment-rest-api.md#request-body) pro stávající ISE nebo nový ISE pomocí REST API Logic Apps a nainstalovat veřejný certifikát do `TrustedRoot` umístění.

Tady jsou další způsoby, jak můžete zabezpečit koncové body, které zpracovávají volání odesílaná z vaší aplikace logiky:

* [Přidání ověřování do odchozích požadavků](#add-authentication-outbound).

  Když použijete Trigger HTTP nebo akci k odeslání odchozích volání, můžete do žádosti přidat ověřování, které odesílá aplikace logiky. Můžete například vybrat tyto typy ověřování:

  * [Základní ověřování](#basic-authentication)

  * [Ověřování certifikátu klienta](#client-certificate-authentication)

  * [Ověřování služby Active Directory OAuth](#azure-active-directory-oauth-authentication)

  * [Ověřování spravovaných identit](#managed-identity-authentication)

* Omezte přístup z IP adres aplikace logiky.

  Všechna volání koncových bodů z aplikací logiky pocházejí z konkrétních určených IP adres, které jsou založeny na oblastech vaší Logic Apps. Můžete přidat filtrování, které přijímá požadavky pouze z těchto IP adres. Pokud chcete získat tyto IP adresy, přečtěte si téma [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md#firewall-ip-configuration).

* Zvyšte zabezpečení připojení k místním systémům.

  Azure Logic Apps poskytuje integraci s těmito službami, které vám pomůžou zajistit bezpečnější a spolehlivou místní komunikaci.

  * Místní brána dat

    Řada spravovaných konektorů v Azure Logic Apps usnadňuje zabezpečená připojení k místním systémům, jako je například systém souborů, SQL, SharePoint a DB2. Brána odesílá data z místních zdrojů do šifrovaných kanálů prostřednictvím Azure Service Bus. Veškerý provoz pochází z agenta brány jako zabezpečený odchozí provoz. Přečtěte si, [Jak funguje místní brána dat](logic-apps-gateway-install.md#gateway-cloud-service).

  * Připojení prostřednictvím Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) poskytuje možnosti místního připojení, jako je virtuální privátní síť Site-to-site a integrace [ExpressRoute](../expressroute/expressroute-introduction.md) pro zabezpečený proxy a komunikaci s místními systémy. Pokud máte rozhraní API, které poskytuje přístup k vašemu místnímu systému, a toto rozhraní API jste vystavili vytvořením [instance služby API Management](../api-management/get-started-create-service-instance.md), můžete toto rozhraní API volat v pracovním postupu vaší aplikace logiky tak, že v návrháři aplikace logiky vyberete integrovanou API Managementovou Trigger nebo akci.

    > [!NOTE]
    > Konektor zobrazuje pouze ty API Management služby, ve kterých máte oprávnění k zobrazení a připojení, ale nezobrazuje služby API Management založené na spotřebě.

    1. Do vyhledávacího pole zadejte v návrháři aplikace logiky `api management` . Vyberte krok na základě toho, jestli přidáváte Trigger nebo akci:<p>

       * Pokud přidáváte Trigger, který je vždycky prvním krokem pracovního postupu, vyberte možnost **zvolit Trigger služby Azure API Management**.

       * Pokud přidáváte akci, vyberte možnost **zvolit akci Azure API Management**.

       Tento příklad přidá aktivační událost:

       ![Přidat aktivační událost služby Azure API Management](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. Vyberte dříve vytvořenou instanci služby API Management.

       ![Vybrat instanci služby API Management](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. Vyberte volání rozhraní API, které se má použít.

       ![Vybrat existující rozhraní API](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>Přidání ověřování do odchozích volání

Koncové body HTTP a HTTPS podporují různé druhy ověřování. U některých triggerů a akcí, které používáte pro odeslání odchozích volání nebo požadavků do těchto koncových bodů, můžete zadat typ ověřování. V návrháři aplikace logiky triggery a akce, které podporují výběr typu ověřování, mají vlastnost **ověřování** . Tato vlastnost se však nemusí vždy zobrazit ve výchozím nastavení. V těchto případech v aktivační události nebo akci otevřete seznam **Přidat nový parametr** a vyberte **ověřování**.

> [!IMPORTANT]
> K ochraně citlivých informací, které vaše aplikace logiky zpracovává, používejte v případě potřeby zabezpečené parametry a zakódovat data.
> Další informace o použití a zabezpečení parametrů naleznete v tématu [přístup ke vstupům parametrů](#secure-action-parameters).

<a name="authentication-types-supported-triggers-actions"></a>

#### <a name="authentication-types-for-triggers-and-actions-that-support-authentication"></a>Typy ověřování pro aktivační události a akce, které podporují ověřování

Tato tabulka uvádí typy ověřování, které jsou k dispozici na triggerech a akcích, kde můžete vybrat typ ověřování:

| Typ ověřování | Podporované triggery a akce |
|---------------------|--------------------------------|
| [Basic](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, Webhook HTTP |
| [Certifikát klienta](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, Webhook HTTP |
| [Protokol OAuth pro Active Directory](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
| [Žádný](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
| [Spravovaná identita](#managed-identity-authentication) | **Předdefinované triggery a akce** <p><p>Azure API Management, Azure App Services, Azure Functions, HTTP, Webhook HTTP <p><p>**Spravované konektory** <p><p>Azure AD Identity Protection, Azure Automation, Azure Container instance, Azure Průzkumník dat, Azure Data Factory, Azure Data Lake, Azure Event Grid, Azure IoT Central v3, Azure Key Vault, Azure Log Analytics, protokoly Azure Monitor, Azure Resource Manager, Sentinel Azure, HTTP s Azure AD <p><p>**Poznámka**: podpora pro spravované konektory je momentálně ve verzi Preview. |
|||

<a name="basic-authentication"></a>

#### <a name="basic-authentication"></a>Základní ověřování

Pokud je k dispozici možnost [základní](../active-directory-b2c/secure-rest-api.md) , zadejte tyto hodnoty vlastností:

| Property – vlastnost (Designer) | Property (JSON) | Vyžadováno | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | Basic | Typ ověřování, který se má použít |
| **Uživatelské jméno** | `username` | Ano | <*uživatelské jméno*>| Uživatelské jméno pro ověřování přístupu k cílovému koncovému bodu služby |
| **Heslo** | `password` | Ano | <*zadáno*> | Heslo pro ověřování přístupu k cílovému koncovému bodu služby |
||||||

Když použijete [zabezpečené parametry](#secure-action-parameters) pro zpracování a zabezpečení citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tato ukázka definice akce HTTP Určuje ověřování `type` jako `Basic` a používá [funkci Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

#### <a name="client-certificate-authentication"></a>Ověřování certifikátu klienta

Pokud je k dispozici možnost [certifikát klienta](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) , zadejte tyto hodnoty vlastností:

| Property – vlastnost (Designer) | Property (JSON) | Vyžadováno | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | **Certifikát klienta** <br>nebo <br>`ClientCertificate` | Typ ověřování, který se má použít. Certifikáty můžete spravovat pomocí [API Management Azure](../api-management/api-management-howto-mutual-certificates.md). <p></p>**Poznámka**: vlastní konektory nepodporují ověřování na základě certifikátů pro příchozí i odchozí volání. |
| **PFX** | `pfx` | Ano | <*Encoded – obsah-souboru PFX*> | Obsah kódovaný v kódování Base64 ze souboru PFX (Personal Information Exchange) <p><p>Chcete-li převést soubor PFX na formát s kódováním base64, můžete použít PowerShell pomocí následujících kroků: <p>1. Uložte obsah certifikátu do proměnné: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. převeďte obsah certifikátu pomocí `ToBase64String()` funkce a uložte tento obsah do textového souboru: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` <p><p>**Řešení potíží**: Pokud použijete `cert mmc/PowerShell` příkaz, může se zobrazit tato chyba: <p><p>`Could not load the certificate private key. Please check the authentication certificate password is correct and try again.` <p><p>Chcete-li tuto chybu vyřešit, zkuste převést soubor PFX na soubor PEM a znovu ho pomocí `openssl` příkazu: <p><p>`openssl pkcs12 -in certificate.pfx -out certificate.pem` <br>`openssl pkcs12 -in certificate.pem -export -out certificate2.pfx` <p><p>Když následně získáte řetězec zakódovaný ve formátu base64 pro nově převedený soubor PFX certifikátu, řetězec teď funguje v Azure Logic Apps. |
| **Heslo** | `password`| Ne | <*heslo-pro-PFX – soubor*> | Heslo pro přístup k souboru PFX |
|||||

Když použijete [zabezpečené parametry](#secure-action-parameters) pro zpracování a zabezpečení citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tato ukázka definice akce HTTP Určuje ověřování `type` jako `ClientCertificate` a používá [funkci Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Další informace o zabezpečení služeb pomocí ověřování klientského certifikátu najdete v těchto tématech:

* [Vylepšení zabezpečení pro rozhraní API pomocí ověřování klientského certifikátu v Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Zvýšení zabezpečení pro back-endové služby pomocí ověřování klientského certifikátu v Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Zvýšení zabezpečení služby RESTfuL pomocí klientských certifikátů](../active-directory-b2c/secure-rest-api.md)
* [Přihlašovací údaje certifikátu pro ověřování aplikací](../active-directory/develop/active-directory-certificate-credentials.md)
* [Použití certifikátu TLS nebo SSL v kódu ve službě Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

#### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory otevření ověřování

Na triggerech žádosti můžete pomocí [Azure Active Directory otevřít ověřování (Azure AD OAuth)](../active-directory/develop/index.yml)ověřit příchozí volání po [nastavení zásad autorizace Azure AD](#enable-oauth) pro vaši aplikaci logiky. Pro všechny ostatní triggery a akce, které poskytují typ ověřování **služby Active Directory OAuth** pro výběr, zadejte tyto hodnoty vlastností:

| Property – vlastnost (Designer) | Property (JSON) | Vyžadováno | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | **Protokol OAuth pro Active Directory** <br>nebo <br>`ActiveDirectoryOAuth` | Typ ověřování, který se má použít. Logic Apps v současnosti následuje [protokol OAuth 2,0](../active-directory/develop/v2-overview.md). |
| **Autorita** | `authority` | Ne | <*Adresa URL pro vystavitele tokenu pro-Authority*> | Adresa URL pro autoritu, která poskytuje přístupový token. Ve výchozím nastavení je tato hodnota `https://login.windows.net` . |
| **Tenant** | `tenant` | Ano | <*ID tenanta*> | ID tenanta pro tenanta Azure AD |
| **Cílová skupina** | `audience` | Ano | <*prostředek k autorizaci*> | Prostředek, který chcete použít pro autorizaci, například `https://management.core.windows.net/` |
| **ID klienta** | `clientId` | Ano | <*ID klienta*> | ID klienta pro aplikaci požadující autorizaci |
| **Typ přihlašovacích údajů** | `credentialType` | Ano | Certifikát <br>nebo <br>Tajný kód | Typ přihlašovacích údajů, který klient používá k vyžádání autorizace. Tato vlastnost a hodnota se nezobrazí v základní definici vaší aplikace logiky, ale určuje vlastnosti, které se zobrazí pro vybraný typ přihlašovacích údajů. |
| **Otázku** | `secret` | Ano, ale jenom pro typ přihlašovacích údajů tajného klíče | <*tajný kód klienta*> | Tajný klíč klienta pro vyžádání autorizace |
| **PFX** | `pfx` | Ano, ale pouze pro typ přihlašovacích údajů certifikát | <*Encoded – obsah-souboru PFX*> | Obsah kódovaný v kódování Base64 ze souboru PFX (Personal Information Exchange) |
| **Heslo** | `password` | Ano, ale pouze pro typ přihlašovacích údajů certifikát | <*heslo-pro-PFX – soubor*> | Heslo pro přístup k souboru PFX |
|||||

Když použijete [zabezpečené parametry](#secure-action-parameters) pro zpracování a zabezpečení citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tato ukázka definice akce HTTP Určuje ověřování `type` jako `ActiveDirectoryOAuth` , typ přihlašovacích údajů `Secret` a používá [funkci Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

#### <a name="raw-authentication"></a>Nezpracované ověřování

Pokud je k dispozici možnost **raw** , můžete tento typ ověřování použít, když budete muset použít [schémata ověřování](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) , která nedodržují [protokol OAuth 2,0](https://oauth.net/2/). Pomocí tohoto typu ručně vytvoříte hodnotu hlavičky autorizace, kterou odešlete s odchozím požadavkem, a v aktivační události nebo akci zadejte tuto hodnotu záhlaví.

Tady je příklad hlavičky pro požadavek HTTPS, který následuje po [protokolu OAuth 1,0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

V aktivační události nebo akci, která podporuje nezpracované ověřování, zadejte tyto hodnoty vlastností:

| Property – vlastnost (Designer) | Property (JSON) | Vyžadováno | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | Žádný | Typ ověřování, který se má použít |
| **Hodnota** | `value` | Ano | <*autorizace – hlavička-hodnota*> | Hodnota hlavičky autorizace, která se má použít pro ověřování |
||||||

Když použijete [zabezpečené parametry](#secure-action-parameters) pro zpracování a zabezpečení citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tato ukázka definice akce HTTP Určuje ověřování `type` jako `Raw` a pomocí [funkce Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) Získá hodnoty parametrů:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

#### <a name="managed-identity-authentication"></a>Ověřování spravovaných identit

Když je možnost [spravovaná identita](../active-directory/managed-identities-azure-resources/overview.md) dostupná na [triggeru nebo akci, která podporuje ověřování spravované identity](#add-authentication-outbound), může vaše aplikace logiky používat identitu přiřazenou systémem nebo *jedinou* ručně vytvořenou identitu, která je chráněná pomocí služby Azure Active Directory (Azure AD), a ne přihlašovací údaje, tajné klíče nebo tokeny Azure AD. Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemáte oprávnění ke správě nebo přímo používat tokeny Azure AD. Přečtěte si další informace o [službách Azure, které podporují spravované identity pro ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Aby mohla aplikace logiky používat spravovanou identitu, postupujte podle kroků v části [ověření přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Tyto kroky povolí spravovanou identitu ve vaší aplikaci logiky a nastaví přístup této identity k cílovému prostředku Azure.

1. Než může Azure Function používat spravovanou identitu, nejdřív [Povolte ověřování pro službu Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

1. V aktivační události nebo akci, která podporuje použití spravované identity, zadejte tyto informace:

   **Předdefinované triggery a akce**

   | Property – vlastnost (Designer) | Property (JSON) | Vyžadováno | Hodnota | Popis |
   |---------------------|-----------------|----------|-------|-------------|
   | **Authentication** | `type` | Ano | **Spravovaná identita** <br>nebo <br>`ManagedServiceIdentity` | Typ ověřování, který se má použít |
   | **Spravovaná identita** | `identity` | Ano | * **Spravovaná identita přiřazená systémem** <br>nebo <br>`SystemAssigned` <p><p>* <*uživatelsky přiřazené-identity-Name*> | Spravovaná identita, která se má použít |
   | **Cílová skupina** | `audience` | Ano | <*cíl-Resource-ID*> | ID prostředku pro cílový prostředek, ke kterému chcete získat přístup. <p>Například `https://storage.azure.com/` zpřístupní [přístupové tokeny](../active-directory/develop/access-tokens.md) pro ověřování platné pro všechny účty úložiště. Můžete ale taky zadat adresu URL kořenové služby, například `https://fabrikamstorageaccount.blob.core.windows.net` pro konkrétní účet úložiště. <p>**Poznámka**: vlastnost **cílové skupiny** může být v některých triggerech nebo akcích skrytá. Chcete-li tuto vlastnost zviditelnit, otevřete v aktivační události nebo akci seznam **Přidat nový parametr** a vyberte možnost **cílová skupina**. <p><p>**Důležité**: Ujistěte se, že toto ID cílového prostředku *přesně odpovídá* hodnotě, kterou očekává služba Azure AD, včetně všech požadovaných koncových lomítek. `https://storage.azure.com/`ID prostředku pro všechny účty Azure Blob Storage vyžaduje koncové lomítko. ID prostředku pro konkrétní účet úložiště ale nevyžaduje koncové lomítko. Tato ID prostředků najdete v tématu [služby Azure, které podporují Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Když použijete [zabezpečené parametry](#secure-action-parameters) pro zpracování a zabezpečení citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Například tato definice akce HTTP Určuje ověřování `type` jako `ManagedServiceIdentity` a používá [funkci Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

   **Aktivační události a akce spravovaného konektoru**

   | Property – vlastnost (Designer) | Vyžadováno | Hodnota | Popis |
   |---------------------|----------|-------|-------------|
   | **Název připojení** | Ano | <*název připojení*> ||
   | **Spravovaná identita** | Ano | **Spravovaná identita přiřazená systémem** <br>nebo <br> <*uživatelsky přiřazené-spravované-identity-Name*> | Typ ověřování, který se má použít |
   |||||


<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Blokovat vytváření připojení

Pokud vaše organizace neumožňuje připojení ke konkrétním prostředkům pomocí jejich konektorů v Azure Logic Apps, můžete [zablokovat možnost vytvářet tato připojení](../logic-apps/block-connections-connectors.md) pro konkrétní konektory v pracovních postupech aplikace logiky pomocí [Azure Policy](../governance/policy/overview.md). Další informace najdete v tématu [bloková připojení vytvořená konkrétními konektory v Azure Logic Apps](../logic-apps/block-connections-connectors.md).

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>Pokyny k izolaci pro Logic Apps

Můžete použít Azure Logic Apps v [Azure Government](../azure-government/documentation-government-welcome.md) podporu všech úrovní dopadu v oblastech popsaných v části [Azure Government dopad na informace o izolaci 5](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) a v části [USA – Příručka zabezpečení cloud computingu požadavky (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html). Aby bylo možné tyto požadavky splnit, Logic Apps podporuje možnost vytvářet a spouštět pracovní postupy v prostředí s vyhrazenými prostředky, takže můžete snížit dopad na výkon jiných tenantů Azure na vašich aplikacích logiky a vyhnout se sdílení výpočetních prostředků s ostatními klienty.

* Chcete-li spustit vlastní kód nebo provést transformaci XML, [vytvořte a zavolejte funkci Azure Functions](../logic-apps/logic-apps-azure-functions.md)namísto použití [funkce vloženého kódu](../logic-apps/logic-apps-add-run-inline-code.md) nebo poskytněte [sestavení pro použití jako mapy](../logic-apps/logic-apps-enterprise-integration-maps.md)v uvedeném pořadí. Také nastavte hostující prostředí pro vaši aplikaci Function App tak, aby vyhovovalo vašim požadavkům na izolaci.

  Například pro splnění požadavků na úrovni 5 můžete vytvořit aplikaci Function App s [plánem App Service](../azure-functions/dedicated-plan.md) s využitím [ **izolované** cenové úrovně](../app-service/overview-hosting-plans.md) spolu s [App Service Environment (pomocným mechanismem)](../app-service/environment/intro.md) , který používá také **izolovanou** cenovou úroveň. V tomto prostředí fungují aplikace Function App na vyhrazených virtuálních počítačích Azure a vyhrazených virtuálních sítích Azure, které zajišťují izolaci sítě nad výpočetní izolací vašich aplikací a maximum možností škálování na více instancí. Další informace najdete v tématu [pokyny k izolaci Azure Government úrovně dopadu 5 – Azure Functions](../azure-government/documentation-government-impact-level-5.md#azure-functions).

  Další informace najdete v těchto tématech:<p>

  * [Plány Azure App Service](../app-service/overview-hosting-plans.md)
  * [Možnosti sítí Azure Functions](../azure-functions/functions-networking-options.md)
  * [Vyhrazení hostitelé Azure pro virtuální počítače](../virtual-machines/dedicated-hosts.md)
  * [Izolace virtuálních počítačů v Azure](../virtual-machines/isolation.md)
  * [Nasazení vyhrazených služeb Azure do virtuálních sítí](../virtual-network/virtual-network-for-azure-services.md)

* Pokud chcete vytvářet aplikace logiky, které běží na vyhrazených prostředcích a mají přístup k prostředkům chráněným pomocí služby Azure Virtual Network, můžete vytvořit [prostředí integrační služby (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Některé virtuální sítě Azure používají pro poskytování přístupu ke službám Azure PaaS, jako jsou například Azure Storage, Azure Cosmos DB nebo Azure SQL Database, partnerské služby nebo služby zákazníkům hostované v Azure, privátní koncové body ([privátní propojení Azure](../private-link/private-link-overview.md)). Pokud vaše aplikace logiky potřebují přístup k virtuálním sítím, které používají privátní koncové body, musíte tyto aplikace logiky vytvořit, nasadit a spustit v rámci ISE.

  * Pro lepší kontrolu šifrovacích klíčů, které používá Azure Storage, můžete nastavit, používat a spravovat vlastní klíč pomocí [Azure Key Vault](../key-vault/general/overview.md). Tato funkce se také označuje jako "Bring Your Own Key" (BYOK) a váš klíč se nazývá klíč spravovaný zákazníkem. Další informace najdete v tématu [nastavení klíčů spravovaných zákazníkem k šifrování dat v klidovém prostředí pro prostředí ISEs (Integration Service Environment) v Azure Logic Apps](../logic-apps/customer-managed-keys-integration-service-environment.md).

Další informace najdete v těchto tématech:

* [Izolace ve veřejném cloudu Azure](../security/fundamentals/isolation-choices.md)
* [Zabezpečení vysoce citlivých aplikací IaaS v Azure](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>Další kroky

* [Základní hodnoty zabezpečení Azure pro Azure Logic Apps](../logic-apps/security-baseline.md)
* [Automatizace nasazení pro Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Monitorování aplikací logiky](../logic-apps/monitor-logic-apps-log-analytics.md)
