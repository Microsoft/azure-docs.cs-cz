---
title: Zabezpečený přístup a Azure Logic Apps dat
description: Chraňte vstupy parametrů, triggery požadavků HTTP, historii spuštění, operace aplikace logiky a připojení k dalším službám v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: c7b6abb9874c8b6bb2469595b9ce53541656834f
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430128"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Zabezpečený přístup a data v Azure Logic Apps

K řízení přístupu a ochraně dat v Azure Logic Apps můžete nastavit zabezpečení v těchto oblastech:

* [Přístup k aktivačním událostem požadavku HTTP](#secure-triggers)
* [Přístup k operacím aplikace logiky](#secure-operations)
* [Přístup ke vstupům a výstupům historie spouštění](#secure-run-history)
* [Přístup ke vstupům parametrů](#secure-action-parameters)
* [Přístup ke službám a systémům, které se nazývají aplikace logiky](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>Přístup k aktivačním událostem požadavku HTTP

Když aplikace logiky používá Trigger založený na požadavcích HTTP, jako je [požadavek](../connectors/connectors-native-reqres.md) nebo Trigger [Webhooku](../connectors/connectors-native-webhook.md) , můžete omezit přístup tak, aby aplikace logiky mohli spustit jenom autorizovaní klienti. Všechny požadavky přijaté aplikací logiky jsou šifrované a zabezpečené pomocí protokolu SSL (Secure Sockets Layer) (SSL). 

Tady jsou způsoby, kterými můžete zabezpečit přístup k tomuto typu triggeru:

* [Generování sdílených přístupových podpisů](#sas)
* [Omezit příchozí IP adresy](#restrict-incoming-ip-addresses)
* [Přidání Azure Active Directory, OAuth nebo jiného zabezpečení](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generování sdílených přístupových podpisů (SAS)

Každý koncový bod žádosti v aplikaci logiky má [sdílený přístupový podpis (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) v adrese URL koncového bodu, který se skládá z tohoto formátu:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Každá adresa URL obsahuje parametr dotazu `sp`, `sv` a `sig`, jak je popsáno v této tabulce:

| Parametr dotazu | Popis |
|-----------------|-------------|
| `sp` | Určuje oprávnění povolených metod HTTP, které se mají použít. |
| `sv` | Určuje verzi SAS, která se má použít pro generování podpisu. |
| `sig` | Určuje podpis, který se má použít pro ověřování přístupu k triggeru. Tento podpis se vygeneruje pomocí algoritmu SHA256 s tajným přístupovým klíčem na všech cestách a vlastnostech adresy URL. Tento klíč zůstane zašifrovaný a uložený v aplikaci logiky, který není přístupný nebo publikovaný. Vaše aplikace logiky autorizuje jenom ty triggery, které obsahují platný podpis vytvořený s tajným klíčem. |
|||

Další informace o zabezpečení přístupu pomocí sdíleného přístupového podpisu najdete v těchto oddílech v tomto tématu:

* [Znovu vygenerovat přístupové klíče](#access-keys)
* [Vytvoření adres URL zpětného volání pro vypršení platnosti](#expiring-urls)
* [Vytváření adres URL s primárním nebo sekundárním klíčem](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Znovu vygenerovat přístupové klíče

Pokud chcete vygenerovat nový klíč zabezpečeného přístupu, použijte Azure REST API nebo Azure Portal. Všechny dříve generované adresy URL, které používají starý klíč, jsou neověřené a už nebudou mít autorizaci k aktivaci aplikace logiky. Adresy URL, které načtete po obnovení, se podepisují pomocí nového přístupového klíče.

1. V Azure Portal otevřete aplikaci logiky, která má klíč, který chcete znovu vygenerovat.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **přístupové klíče**.

1. Vyberte klíč, který chcete znovu vygenerovat, a dokončete proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Vytvoření adres URL zpětného volání pro vypršení platnosti

Pokud sdílíte adresu URL koncového bodu pro Trigger založený na požadavcích HTTP s ostatními stranami, můžete vygenerovat adresy URL zpětného volání, které používají konkrétní klíče a mají datum vypršení platnosti. Tímto způsobem můžete plynule vrátit klíče nebo omezit přístup k aktivaci aplikace logiky na základě konkrétního časového rozmezí. Chcete-li zadat datum vypršení platnosti adresy URL, použijte [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), například:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Do těla zadejte `NotAfter`property pomocí řetězce data JSON. Tato vlastnost vrátí adresu URL zpětného volání, která je platná pouze do data a času `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Vytváření adres URL s primárním nebo sekundárním tajným klíčem

Když generujete nebo vypíšete adresy URL zpětného volání pro aktivační události založené na požadavku HTTP, můžete zadat klíč, který se má použít k podepsání adresy URL. Chcete-li vygenerovat adresu URL, která je podepsána určitým klíčem, použijte [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), například:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

V těle zahrňte vlastnost `KeyType` buď jako `Primary`, nebo `Secondary`. Tato vlastnost vrátí adresu URL, která je podepsána zadaným zabezpečeným klíčem.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Omezit příchozí IP adresy

Společně se sdíleným přístupovým podpisem můžete chtít omezit konkrétní klienty, kteří můžou zavolat vaší aplikaci logiky. Pokud například spravujete koncový bod žádosti pomocí Azure API Management, můžete aplikaci logiky omezit tak, aby přijímala požadavky pouze z IP adresy pro instanci API Management.

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>Omezit rozsahy příchozích IP adres v Azure Portal

1. V Azure Portal otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **Nastavení pracovního postupu**.

1. V části **Konfigurace řízení přístupu**@no__t – 1**povolená příchozí IP adresa**vyberte **konkrétní rozsahy IP**adres.

1. V části **rozsahy IP pro aktivační události**zadejte rozsahy IP adres, které aktivační událost akceptuje.

   Platný rozsah IP adres používá tyto formáty: *x. x. x. x/x* nebo *x. x. x. x-x. x.* x. x.

Pokud chcete, aby se aplikace logiky spouštěla jenom jako vnořená aplikace logiky, vyberte ze seznamu **povolené IP adresy** **jenom další Logic Apps**. Tato možnost zapíše prázdné pole do prostředku aplikace logiky. Tímto způsobem mohou spustit vnořenou aplikaci logiky pouze volání ze služby Logic Apps (nadřazené aplikace logiky).

> [!NOTE]
> Bez ohledu na IP adresu můžete přesto spustit aplikaci logiky, která má Trigger založený na požadavcích HTTP pomocí `/triggers/<trigger-name>/run` prostřednictvím Azure REST API nebo prostřednictvím API Management. Tento scénář ale pořád vyžaduje ověřování proti REST API Azure. Všechny události se zobrazí v protokolu auditu Azure. Ujistěte se, že jste nastavili zásady řízení přístupu odpovídajícím způsobem.

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>Omezení rozsahů příchozích IP adres v šabloně Azure Resource Manager

Pokud automatizujete nasazení aplikací logiky pomocí [Azure Resource Manager šablony](../logic-apps/logic-apps-create-deploy-template.md), můžete určit rozsahy IP adres pomocí oddílu `accessControl` s částí `triggers` v definici prostředků vaší aplikace logiky, například:

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
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Přidání Azure Active Directory, OAuth nebo jiného zabezpečení

K přidání dalších autorizačních protokolů do aplikace logiky zvažte použití [Azure API Management](../api-management/api-management-key-concepts.md). Tato služba poskytuje možnost zpřístupnit aplikaci logiky jako rozhraní API a nabízí bohatou kontrolu, zabezpečení, zásady a dokumentaci pro libovolný koncový bod. API Management může vystavit veřejný nebo privátní koncový bod pro vaši aplikaci logiky, která pak může používat Azure Active Directory, protokol OAuth, certifikát nebo jiné standardy zabezpečení. Když API Management obdrží požadavek, služba pošle požadavek do vaší aplikace logiky, což také vede k potřebným transformacím nebo omezením na cestě. Pokud chcete povolit jenom API Management aktivovat aplikaci logiky, můžete použít nastavení příchozího rozsahu IP adres vaší aplikace logiky.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Přístup k operacím aplikace logiky

Můžete povolit pouze konkrétní uživatele nebo skupiny, aby mohli spouštět konkrétní operace, jako je správa, úpravy a zobrazení Logic Apps. Pokud chcete řídit svá oprávnění, pomocí [Access Control na základě rolí Azure (RBAC)](../role-based-access-control/role-assignments-portal.md) přiřaďte členům v předplatném Azure vlastní nebo předdefinované role:

* [Přispěvatel aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-contributor): umožňuje spravovat Logic Apps, ale nemůžete pro ně měnit přístup.

* [Operátor aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-operator): umožňuje číst, povolit a zakázat Logic Apps, ale nemůžete je upravovat ani aktualizovat.

Pokud chcete ostatním uživatelům zabránit v změně nebo odstranění vaší aplikace logiky, můžete použít [Azure Resource Lock](../azure-resource-manager/resource-group-lock-resources.md), který ostatním uživatelům brání v změně nebo odstranění produkčních prostředků.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Přístup k datům historie spouštění

Během provádění aplikace logiky se všechna data [šifrují během přenosu](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) pomocí protokolu TLS (Transport Layer Security) a [v klidovém](../security/fundamentals/encryption-atrest.md)stavu. Až se vaše aplikace logiky dokončí, můžete zobrazit historii pro toto spuštění, včetně kroků, které byly spuštěny spolu se stavem, dobou trvání, vstupy a výstupy pro každou akci. Tato bohatá podrobnosti poskytuje přehled o tom, jak vaše aplikace logiky běžela a kde můžete začít řešit problémy, ke kterým dojde.

Když přistupujete k historii spuštění aplikace logiky, Logic Apps ověří váš přístup a poskytne odkazy na vstupy a výstupy z požadavků a odpovědí v běhu vaší aplikace logiky. U akcí, které zpracovávají všechna hesla, tajné klíče, klíče nebo jiné citlivé informace, ale chcete ostatním uživatelům zabránit v prohlížení těchto dat a přístupu k nim. Například pokud vaše aplikace logiky získá tajný kód z [Azure Key Vault](../key-vault/key-vault-overview.md) , který se má použít při ověřování akce HTTP, chcete tento tajný klíč skrýt ze zobrazení.

K řízení přístupu k vstupům a výstupům v historii spuštění aplikace logiky máte tyto možnosti:

* [Omezte přístup pomocí rozsahu IP adres](#restrict-ip).

  Tato možnost umožňuje zabezpečený přístup k historii spouštění na základě požadavků z konkrétního rozsahu IP adres.

* [Skryjte data z historie spouštění pomocí zmatenosti](#obfuscate).

  V mnoha aktivačních událostech a akcích můžete skrýt jejich vstupy, výstupy nebo obojí z historie spuštění aplikace logiky.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Omezení přístupu podle rozsahu IP adres

V historii spuštění aplikace logiky můžete omezit přístup k vstupům a výstupům, aby se tato data mohla zobrazit jenom z konkrétních rozsahů IP adres. Chcete-li například zablokovat komukoli přístup k vstupům a výstupům, zadejte rozsah IP adres, například `0.0.0.0-0.0.0.0`. Pouze osoba s oprávněními správce může toto omezení odebrat, což umožňuje "za běhu" přístup k datům aplikace logiky. Rozsahy IP adres, které se mají omezit, můžete zadat pomocí Azure Portal nebo v šabloně Azure Resource Manager, kterou používáte pro nasazení aplikace logiky.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Omezení rozsahů IP adres v Azure Portal

1. V Azure Portal otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **Nastavení pracovního postupu**.

1. V části **Konfigurace řízení přístupu**@no__t – 1**povolená příchozí IP adresa**vyberte **konkrétní rozsahy IP**adres.

1. V části **rozsahy IP adres pro obsah**zadejte rozsahy IP adres, které budou mít přístup k obsahu ze vstupů a výstupů. 

   Platný rozsah IP adres používá tyto formáty: *x. x. x. x/x* nebo *x. x. x. x-x. x.* x. x.

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Omezení rozsahů IP adres v šabloně Azure Resource Manager

Pokud automatizujete nasazení aplikací logiky pomocí [Azure Resource Manager šablony](../logic-apps/logic-apps-create-deploy-template.md), můžete určit rozsahy IP adres pomocí oddílu `accessControl` s částí `contents` v definici prostředků vaší aplikace logiky, například:

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

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Skrýt data z historie spouštění pomocí zmatenosti

Mnoho triggerů a akcí má nastavení pro skrytí vstupů, výstupů nebo obojího z historie spuštění aplikace logiky. Tady je několik [důležitých informací, které](#obfuscation-considerations) je potřeba zkontrolovat, když použijete tato nastavení k zabezpečení těchto dat.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Zabezpečení vstupů a výstupů v Návrháři

1. Pokud vaše aplikace logiky už není v [Azure Portal](https://portal.azure.com)otevřená, otevřete aplikaci logiky v návrháři aplikace logiky.

   ![Otevřít ukázkovou aplikaci logiky](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. V aktivační události nebo akci, kde chcete zabezpečit data, vyberte tlačítko se třemi tečkami ( **...** ) a pak vyberte **Nastavení**.

   ![Otevřete nastavení.](media/logic-apps-securing-a-logic-app/open-settings.png)

1. Zapněte buď **zabezpečené vstupy**, **zabezpečené výstupy**, nebo obojí. Jakmile budete hotovi, vyberte **Hotovo**.

   ![Zapnutí zabezpečených vstupů nebo výstupů](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Akce nebo aktivační událost nyní zobrazuje ikonu zámku v záhlaví.

   ![Ikona zámku v záhlaví](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   Tokeny, které představují zabezpečené výstupy z předchozích akcí, také zobrazují ikony zámku. Když například vyberete takový výstup ze seznamu dynamického obsahu, který chcete použít v akci, tento token zobrazí ikonu zámku.

   ![Vybrat výstup](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Po spuštění aplikace logiky můžete zobrazit historii pro daný běh.

   1. V podokně **Přehled** aplikace logiky vyberte běh, který chcete zobrazit.

   1. V podokně **spuštění aplikace logiky** rozbalte akce, které chcete zkontrolovat.

      Pokud se rozhodnete zabezpečit vstupy i výstupy, zobrazí se nyní tyto hodnoty jako skryté.

      ![Skrytá data v historii spuštění](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Zabezpečené vstupy a výstupy v zobrazení kódu

V podkladové aktivační události nebo definici akce přidejte nebo aktualizujte pole `runtimeConfiguration.secureData.properties` pomocí jedné nebo obou hodnot:

* `"inputs"`: zabezpečuje vstupy v historii spuštění.
* `"outputs"`: zabezpečuje výstupy v historii spuštění.

Tady je několik [důležitých informací, které](#obfuscation-considerations) je potřeba zkontrolovat, když použijete tato nastavení k zabezpečení těchto dat.

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

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Předpoklady při skrývání vstupů a výstupů

* Když zabezpečíte vstupy nebo výstupy na triggeru nebo akci, Logic Apps neodesílají zabezpečená data do Azure Log Analytics. Do této aktivační události nebo akce pro monitorování nemůžete také přidat [sledované vlastnosti](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) .

* [Rozhraní Logic Apps API pro zpracování historie pracovního postupu](https://docs.microsoft.com/rest/api/logic/) nevrací zabezpečené výstupy.

* V akci, kde zabezpečíte vstupy nebo explicitně používá zabezpečené výstupy, a tato akce vrátí odpověď se výstupy, které zahrnují Tato zabezpečená data, je nutné ručně zapnout **zabezpečené výstupy** v této akci a zabezpečit tak tyto výstupy.

* Ujistěte se, že jste zapnuli **zabezpečené vstupy** nebo **zabezpečené výstupy** v podřízených akcích, u kterých očekáváte, že historie spouštění zabezpečuje tato data.

  **Nastavení zabezpečených výstupů**

  Když ručně zapnete **zabezpečené výstupy** v triggeru nebo akci, Logic Apps tyto výstupy zabezpečit v historii spuštění. Pokud akce pro příjem dat explicitně používá tyto zabezpečené výstupy jako vstupy, Logic Apps skrývá vstupy této akce v historii spuštění, ale *nepovolí* nastavení **zabezpečených vstupů** akce.

  ![Zabezpečené výstupy jako vstupy a přínosy při většině akcí](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Akce vytvořit, analyzovat JSON a odpověď mají pouze nastavení **zabezpečené vstupy** . Když je tato možnost zapnutá, tato nastavení také skryje výstupy těchto akcí. Pokud tyto akce explicitně používají výstupy proti nadřazenému objektu jako vstupy, Logic Apps skrývá vstupy a výstupy těchto akcí, ale *nepovoluje* nastavení **zabezpečených vstupů** těchto akcí. Pokud akce pro příjem dat explicitně používá skryté výstupy z akcí vytvořit, analyzovat JSON nebo odpovědět jako vstupy, Logic Apps *neskryje tyto vstupy a výstupy této akce*.

  ![Zabezpečené výstupy jako vstupy s vlivem na konkrétní akce na základě dat](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Nastavení zabezpečených vstupů**

  Když ručně zapnete **zabezpečené vstupy** v triggeru nebo akci, Logic Apps tyto vstupy zabezpečit v historii spuštění. Pokud akce pro příjem dat explicitně používá viditelné výstupy z této triggeru nebo akce jako vstupy, Logic Apps v historii spuštění skrýt vstupy pro navazující akce, ale *nepovolí* v této akci **zabezpečené vstupy** a neskryje tuto akci. činnosti.

  ![Zabezpečené vstupy a důsledky pro všechny akce](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  Pokud akce vytvořit, analyzovat JSON a odpověď explicitně použijí viditelné výstupy z triggeru nebo akce, která má zabezpečené vstupy, Logic Apps skryje tyto vstupy a výstupy těchto akcí, ale *nepovolí* tyto **zabezpečené vstupy** těchto akcí. nastavením. Pokud akce pro příjem dat explicitně používá skryté výstupy z akcí vytvořit, analyzovat JSON nebo odpovědět jako vstupy, Logic Apps *neskryje tyto vstupy a výstupy této akce*.

  ![Zabezpečené vstupy a důsledky pro konkrétní akce](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Přístup ke vstupům parametrů

Pokud nasazujete v různých prostředích, zvažte Parametrizace hodnot v definici pracovního postupu, které se liší v závislosti na těchto prostředích. Tímto způsobem můžete použít [šablonu Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters) k nasazení aplikace logiky, ochraně citlivých informací definováním zabezpečených parametrů a zadání těchto parametrů samostatně prostřednictvím parametrů šablony pomocí [parametru. soubor](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Pokud například ověříte akce HTTP pomocí [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), můžete definovat a zabezpečit parametry, které přijímají ID klienta a tajný kód klienta používaný pro ověřování. Pro definování těchto parametrů pro vaši aplikaci logiky použijte část `parameters` v definici pracovního postupu vaší aplikace logiky. Pro ochranu hodnot parametrů, které nechcete zobrazit při úpravách aplikace logiky nebo zobrazení historie spuštění, definujte parametry pomocí typu `securestring` nebo `secureobject` a podle potřeby kódování použijte. Parametry, které mají tento typ, nejsou vráceny s definicí prostředků a nejsou přístupné při zobrazení prostředku po nasazení. Pro přístup k těmto hodnotám parametrů za běhu použijte výraz `@parameters('<parameter-name>')` uvnitř definice pracovního postupu. Tento výraz je vyhodnocen pouze za běhu a je popsán [jazykem definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Pokud použijete parametr v záhlaví nebo těle požadavku HTTP, může být tento parametr viditelný při zobrazení historie spuštění vaší aplikace logiky a odchozího požadavku HTTP. Ujistěte se, že jste odpovídajícím způsobem také nastavili zásady přístupu k obsahu. Autorizační hlavičky nejsou nikdy viditelné prostřednictvím vstupů nebo výstupů. Takže pokud se tam používá tajný klíč, nedá se tento tajný klíč získat.

Další informace najdete v části [zabezpečené parametry v definicích pracovních postupů](#secure-parameters-workflow) dále v tomto tématu.

Při automatizaci nasazení pomocí [šablon Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters)můžete definovat zabezpečené parametry šablony, které jsou vyhodnocovány při nasazení pomocí typů `securestring` a `secureobject`. Chcete-li definovat parametry šablony, použijte část nejvyšší úrovně šablony `parameters`, která je oddělená a odlišná od `parameters` definice pracovního postupu. Chcete-li zadat hodnoty parametrů šablony, použijte samostatný [soubor parametrů](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Pokud například používáte tajné kódy, můžete definovat a používat zabezpečené parametry šablony, které tyto tajné kódy načítají z [Azure Key Vault](../key-vault/key-vault-overview.md) při nasazení. Pak můžete odkazovat na Trezor klíčů a tajný kód v souboru parametrů. Další informace najdete v těchto tématech:

* [Pomocí Azure Key Vault předat hodnoty zabezpečeného parametru při nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* [Parametry zabezpečení v šablonách Azure Resource Manager](#secure-parameters-deployment-template) dále v tomto tématu.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Zabezpečené parametry v definicích pracovních postupů

Pokud chcete chránit citlivé informace v definici pracovního postupu vaší aplikace logiky, použijte zabezpečené parametry, aby tyto informace nebyly po uložení aplikace logiky viditelné. Předpokládejme například, že máte akci HTTP vyžaduje základní ověřování, které používá uživatelské jméno a heslo. V definici pracovního postupu oddíl `parameters` definuje parametry `basicAuthPasswordParam` a `basicAuthUsernameParam` pomocí typu `securestring`. Definice akce pak na tyto parametry odkazuje v části `authentication`.

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

Šablona Správce prostředků pro aplikaci logiky má několik částí `parameters`. K ochraně hesel, klíčů, tajných kódů a dalších citlivých informací můžete definovat zabezpečené parametry na úrovni šablony a definice pracovního postupu pomocí typu `securestring` nebo `secureobject`. Tyto hodnoty pak můžete uložit v [Azure Key Vault](../key-vault/key-vault-overview.md) a použít [soubor parametrů](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values) k odkazování na Trezor klíčů a tajný kód. Vaše šablona pak tyto informace načte při nasazení. Další informace najdete v tématu [použití Azure Key Vault k předání zabezpečených hodnot parametrů při nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md).

Další informace o těchto částech `parameters`:

* V části nejvyšší úrovně šablony definuje `parameters` parametry pro hodnoty, které šablona používá při *nasazení*. Například tyto hodnoty mohou zahrnovat připojovací řetězce pro konkrétní prostředí nasazení. Tyto hodnoty pak můžete uložit do samostatného [souboru parametrů](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values), což usnadňuje změnu těchto hodnot.

* V rámci definice prostředků vaší aplikace logiky, ale mimo definici pracovního postupu, určuje část `parameters` hodnoty pro parametry definice pracovního postupu. V této části můžete přiřadit tyto hodnoty pomocí výrazů šablony, které odkazují na parametry šablony. Tyto výrazy jsou vyhodnocovány při nasazení.

* V rámci definice pracovního postupu @no__t oddíl-0 definuje parametry, které vaše aplikace logiky používá za běhu. Pak můžete odkazovat na tyto parametry uvnitř pracovního postupu aplikace logiky pomocí výrazů definice pracovního postupu, které jsou vyhodnocovány za běhu.

Tento příklad šablony s více zabezpečenými definicemi parametrů, které používají typ `securestring`:

| Název parametru | Popis |
|----------------|-------------|
| `TemplatePasswordParam` | Parametr šablony, který přijímá heslo, které se pak předává parametru `basicAuthPasswordParam` definice pracovního postupu |
| `TemplateUsernameParam` | Parametr šablony, který přijímá uživatelské jméno, které je pak předáno parametru `basicAuthUserNameParam` definice pracovního postupu |
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
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
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

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Přístup ke službám a systémům, které se nazývají aplikace logiky

Tady je několik způsobů, jak můžete zabezpečit koncové body, ve kterých aplikace logiky potřebuje přístup k odeslání požadavků:

* Přidejte ověřování u odchozích požadavků.

  Když pracujete s akcí HTTP, HTTP + Swagger (Open API) nebo Webhook, můžete do žádosti odeslané vaší aplikací logiky přidat ověřování. Můžete například použít základní ověřování, ověřování certifikátů nebo Azure Active Directory ověřování. Další informace najdete v tématu [ověřování aktivačních událostí nebo akcí](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

* Omezte přístup z IP adres aplikace logiky.

  Všechna volání koncových bodů z aplikací logiky pocházejí z konkrétních určených IP adres, které jsou založeny na oblastech vaší Logic Apps. Můžete přidat filtrování, které přijímá požadavky pouze z těchto IP adres. Pokud chcete získat tyto IP adresy, přečtěte si téma [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Ověřování a přístup k prostředkům jako spravované identity.

  Pro přístup k prostředkům v jiných klientech Azure Active Directory (Azure AD) může vaše aplikace logiky ověřit vaši identitu bez přihlášení pomocí spravované identity (dřív označované jako Identita spravované služby nebo MSI) místo přihlašovacích údajů nebo tajných kódů. Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte zadávat ani otáčet tajné klíče. Další informace o tom, jak nastavit a používat spravovanou identitu přiřazenou systémem pro vaši aplikaci logiky, najdete v tématu [ověřování a přístup k prostředkům se spravovanými identitami v Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

* Zabezpečená připojení k místním systémům.

  Azure Logic Apps poskytuje integraci s těmito službami pro zabezpečenou a spolehlivou místní komunikaci.

  * Místní brána dat

    Řada spravovaných konektorů v Azure Logic Apps poskytuje zabezpečená připojení k místním systémům, jako je například systém souborů, SQL, SharePoint a DB2. Brána odesílá data z místních zdrojů do šifrovaných kanálů prostřednictvím Azure Service Bus. Veškerý provoz vychází z agenta brány na zabezpečený odchozí provoz. Přečtěte si, [Jak funguje místní brána dat](logic-apps-gateway-install.md#gateway-cloud-service).

  * Připojení prostřednictvím Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) poskytuje možnosti místního připojení, jako je virtuální privátní síť Site-to-site a integrace ExpressRoute pro zabezpečený proxy a komunikaci s místními systémy. Z pracovního postupu aplikace logiky v návrháři aplikace logiky můžete vybrat rozhraní API, které je vystaveno API Management, které poskytuje rychlý přístup k místním systémům.

## <a name="next-steps"></a>Další kroky

* [Vytvoření šablon nasazení](logic-apps-create-deploy-template.md)  
* [Monitorování aplikací logiky](logic-apps-monitor-your-logic-apps.md)  
* [Diagnostika chyb a problémů aplikace logiky](logic-apps-diagnosing-failures.md)  
* [Automatizace nasazení aplikace logiky](logic-apps-azure-resource-manager-templates-overview.md)
