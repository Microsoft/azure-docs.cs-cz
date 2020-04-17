---
title: Zabezpečení přístupu a dat
description: Zabezpečený přístup ke vstupům, výstupům, aktivačním událostem založeným na požadavcích, spuštění historie, úlohám správy a přístupu k dalším prostředkům v Aplikacích Logika Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: e591a7035db82425952a16f5c4c220e25d8517fe
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457174"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Zabezpečený přístup a data v Aplikacích Azure Logic Apps

Chcete-li řídit přístup a chránit data v Azure Logic Apps, můžete nastavit zabezpečení v těchto oblastech:

* [Přístup k aktivačním událostem založeným na požadavcích](#secure-triggers)
* [Přístup k operacím aplikace logiky](#secure-operations)
* [Přístup ke spuštění historických vstupů a výstupů](#secure-run-history)
* [Přístup k vstupům parametrů](#secure-action-parameters)
* [Přístup ke službám a systémům volaných z aplikací logiky](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Přístup k aktivačním událostem založeným na požadavcích

Pokud vaše aplikace logiky používá aktivační událost založenou na požadavcích, která přijímá příchozí volání nebo požadavky, jako je například aktivační [událost Požadavek](../connectors/connectors-native-reqres.md) nebo [Webhook,](../connectors/connectors-native-webhook.md) můžete omezit přístup tak, aby pouze oprávnění klienti mohli volat vaši aplikaci logiky. Všechny požadavky přijaté aplikací logiky jsou šifrovány a zabezpečeny pomocí zabezpečení transportní vrstvy (TLS), dříve známé jako SSL (Secure Sockets Layer).

Zde jsou možnosti, které vám mohou pomoci zabezpečit přístup k tomuto typu aktivační události:

* [Generování sdílených přístupových podpisů](#sas)
* [Omezení příchozích adres IP](#restrict-inbound-ip-addresses)
* [Přidání azure active directory oauth nebo jiné zabezpečení](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generovat sdílené přístupové podpisy (SAS)

Každý koncový bod požadavku v aplikaci logiky má [sdílený přístupový podpis (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) v adrese URL koncového bodu, který následuje podle tohoto formátu:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Každá adresa `sp`URL `sv`obsahuje `sig` parametr , a dotaz popsaný v této tabulce:

| Parametr dotazu | Popis |
|-----------------|-------------|
| `sp` | Určuje oprávnění pro povolené metody HTTP, které mají být používány. |
| `sv` | Určuje verzi SAS, která má být používána pro generování podpisu. |
| `sig` | Určuje podpis, který se má použít pro ověřování přístupu k aktivační události. Tento podpis je generován pomocí algoritmu SHA256 s tajným přístupovým klíčem na všech cestách adres URL a vlastnostech. Nikdy vystaveny nebo publikovány, tento klíč je uchovávány šifrované a uloženy s aplikací logiky. Aplikace logiky autorizuje pouze ty aktivační události, které obsahují platný podpis vytvořený pomocí tajného klíče. |
|||

Další informace o zabezpečení přístupu pomocí sas naleznete v těchto částech v tomto tématu:

* [Obnovení přístupových klíčů](#access-keys)
* [Vytvoření adres URL zpětného volání s končící platností](#expiring-urls)
* [Vytvoření adres URL s primárním nebo sekundárním klíčem](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Obnovení přístupových klíčů

Pokud chcete kdykoli vygenerovat nový přístupový klíč zabezpečení, použijte rozhraní Azure REST API nebo portál Azure. Všechny dříve generované adresy URL, které používají starý klíč, jsou neplatné a již nemají oprávnění k aktivaci aplikace logiky. Adresy URL, které načtete po regeneraci, jsou podepsány novým přístupovým klíčem.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky, která má klíč, který chcete znovu vygenerovat.

1. V nabídce aplikace logiky vyberte v části **Nastavení** **položku Access Keys**.

1. Vyberte klíč, který chcete znovu vygenerovat, a dokončete proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Vytvoření adres URL zpětného volání s končící platností

Pokud sdílíte adresu URL koncového bodu pro aktivační událost založenou na požadavcích s jinými stranami, můžete vygenerovat adresy URL zpětného volání, které používají určité klíče a mají data vypršení platnosti. Tímto způsobem můžete bez problémů rolovat klíče nebo omezit přístup k aktivaci aplikace logiky na základě určitého časovým rozpětí. Chcete-li zadat datum vypršení platnosti adresy URL, použijte [rozhraní REST API logic apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), například:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

V těle, zahrnout `NotAfter`vlastnost pomocí řetězce data JSON. Tato vlastnost vrátí adresu URL zpětného volání, která je platná pouze do `NotAfter` data a času.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Vytvoření adres URL s primárním nebo sekundárním tajným klíčem

Při generování nebo seznam adresy URL zpětného volání pro aktivační událost založené na požadavku, můžete zadat klíč, který má být používán pro podepsání adresy URL. Chcete-li vygenerovat adresu URL, která je podepsána určitým klíčem, použijte rozhraní [REST API logic apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), například:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

V těle, zahrnout `KeyType` vlastnost `Primary` jako `Secondary`buď nebo . Tato vlastnost vrátí adresu URL, která je podepsána zadaným bezpečnostním klíčem.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Omezení příchozích adres IP

Spolu se sdíleným přístupovým podpisem (SAS) můžete konkrétně omezit klienty, kteří můžou volat vaši aplikaci logiky. Pokud například spravujete koncový bod požadavku pomocí Azure API Management, můžete omezit aplikaci logiky tak, aby přijímala požadavky pouze z IP adresy pro instanci API Management.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Omezení příchozích rozsahů IP adres na webu Azure Portal

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

1. V nabídce aplikace logiky vyberte v části **Nastavení** **nastavení pracovního postupu**.

1. V části **Konfigurace** > řízení přístupu**Povolená příchozí adresy IP**vyberte **položku Rozsahy specifických IP adres**.

1. V **části Rozsahy IP adres pro aktivační události**zadejte rozsahy IP adres, které aktivační událost přijme.

   Platný rozsah IP adres používá tyto formáty: *x.x.x.x/x* nebo *x.x.x.x-x.x.x.x.x.x.x.x*

Pokud chcete, aby se aplikace logiky aktivovala pouze jako vnořená aplikace **logiky,** v seznamu Povolené příchozí IP adresy vyberte **Jenom ostatní aplikace logiky**. Tato možnost zapíše prázdné pole do prostředku aplikace logiky. Tímto způsobem pouze volání ze služby Logic Apps (nadřazené aplikace logiky) můžete aktivovat vnořené aplikace logiky.

> [!NOTE]
> Bez ohledu na IP adresu můžete stále spustit aplikaci logiky, `/triggers/<trigger-name>/run` která má aktivační událost založenou na požadavcích pomocí rozhraní AZURE REST API nebo prostřednictvím správy rozhraní API. Tento scénář však stále vyžaduje ověřování proti rozhraní API Azure REST. Všechny události se zobrazí v protokolu auditování Azure. Ujistěte se, že jste odpovídajícím způsobem nastavili zásady řízení přístupu.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Omezení rozsahů příchozích IP adres v šabloně Azure Resource Manager

Pokud [automatizujete nasazení pro aplikace logiky pomocí šablon Správce prostředků](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), `accessControl` můžete určit `triggers` rozsahy IP adres pomocí části s oddílem v definici prostředků aplikace logiky, například:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Přidání azure active directory oauth nebo jiné zabezpečení

Pokud chcete do aplikace logiky přidat další autorizační protokoly, zvažte použití služby [Azure API Management.](../api-management/api-management-key-concepts.md) Tato služba vám pomůže vystavit aplikaci logiky jako rozhraní API a nabízí bohaté monitorování, zabezpečení, zásady a dokumentaci pro libovolný koncový bod. Správa rozhraní API můžete vystavit veřejný nebo soukromý koncový bod pro aplikaci logiky. Chcete-li autorizovat přístup k tomuto koncovému bodu, můžete použít [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), [klientský certifikát](#client-certificate-authentication)nebo jiné standardy zabezpečení pro autorizaci přístupu k tomuto koncovému bodu. Když správa rozhraní API obdrží požadavek, služba odešle požadavek do aplikace logiky, také provádět všechny nezbytné transformace nebo omezení na cestě. Chcete-li ponechat pouze správu rozhraní API aktivovat aplikaci logiky, můžete použít nastavení rozsahu příchozí IP aplikace vaší aplikace logiky.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Přístup k operacím aplikace logiky

Můžete povolit pouze určitým uživatelům nebo skupinám spouštět určité úkoly, jako je správa, úpravy a zobrazení aplikací logiky. Chcete-li řídit jejich oprávnění, použijte [Řízení přístupu na základě rolí Azure (RBAC),](../role-based-access-control/role-assignments-portal.md) abyste mohli členům v předplatném Azure přiřadit vlastní nebo integrované role:

* [Přispěvatel aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-contributor): Umožňuje spravovat aplikace logiky, ale nelze změnit přístup k nim.

* [Operátor aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-operator): Umožňuje číst, povolit a zakázat aplikace logiky, ale nemůžete je upravovat ani aktualizovat.

Chcete-li zabránit ostatním v změně nebo odstranění aplikace logiky, můžete použít [Zámek prostředků Azure](../azure-resource-manager/management/lock-resources.md). Tato funkce zabraňuje ostatním ve změně nebo odstranění výrobních prostředků.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Přístup ke spuštění dat historie

Během spuštění aplikace logiky jsou všechna data [během přenosu šifrována](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) pomocí zabezpečení transportní vrstvy (TLS) a [v klidovém stavu](../security/fundamentals/encryption-atrest.md). Po dokončení spuštění aplikace logiky můžete zobrazit historii pro toto spuštění, včetně kroků, které byly spuštěny spolu se stavem, dobou trvání, vstupy a výstupy pro každou akci. Tento bohatý detail poskytuje přehled o tom, jak vaše aplikace logiky běžel a kde můžete začít řešit všechny problémy, které vzniknou.

Při zobrazení historie spuštění aplikace logiky aplikace logiky ověřuje váš přístup a pak poskytuje odkazy na vstupy a výstupy pro požadavky a odpovědi pro každé spuštění. U akcí, které zpracovávají všechna hesla, tajné klíče, klíče nebo jiné citlivé informace, však chcete ostatním zabránit v zobrazení a přístupu k těmto datům. Například pokud vaše aplikace logiky získá tajný klíč z [Azure Key Vault](../key-vault/general/overview.md) použít při ověřování akce HTTP, chcete skrýt tento tajný klíč ze zobrazení.

Chcete-li řídit přístup k vstupům a výstupům v historii spuštění aplikace logiky, máte tyto možnosti:

* [Omezte přístup podle rozsahu IP adres](#restrict-ip).

  Tato možnost pomáhá zabezpečit přístup ke spuštění historie na základě požadavků z určitého rozsahu IP adres.

* [Skryjte data z historie spuštění pomocí obfuskace](#obfuscate).

  V mnoha aktivačních událostech a akcích můžete skrýt jejich vstupy, výstupy nebo obojí z historie spuštění aplikace logiky.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Omezení přístupu podle rozsahu IP adres

Můžete omezit přístup k vstupům a výstupům v historii spuštění aplikace logiky tak, aby tato data mohly zobrazit pouze požadavky z určitých rozsahů IP adres. Chcete-li například zablokovat přístup k vstupům a výstupům `0.0.0.0-0.0.0.0`komukoli, zadejte rozsah adres IP, například . Toto omezení může odebrat pouze osoba s oprávněními správce, která poskytuje možnost přístupu "just-in-time" k datům vaší aplikace logiky. Rozsahy IP adres můžete určit, které se omezí buď pomocí portálu Azure, nebo v šabloně Azure Resource Manageru, kterou používáte pro nasazení aplikace logiky.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Omezení rozsahů IP adres na webu Azure Portal

1. Na webu Azure Portal otevřete aplikaci logiky v Návrháři aplikací logiky.

1. V nabídce aplikace logiky vyberte v části **Nastavení** **nastavení pracovního postupu**.

1. V části **Konfigurace** > řízení přístupu**Povolená příchozí adresy IP**vyberte **položku Rozsahy specifických IP adres**.

1. V **části Rozsahy IP adres pro obsah**zadejte rozsahy IP adres, které mají přístup k obsahu ze vstupů a výstupů. 

   Platný rozsah IP adres používá tyto formáty: *x.x.x.x/x* nebo *x.x.x.x-x.x.x.x.x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Omezení rozsahů IP adres v šabloně Azure Resource Manager

Pokud [automatizujete nasazení pro aplikace logiky pomocí šablon Správce prostředků](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), `accessControl` můžete určit `contents` rozsahy IP adres pomocí části s oddílem v definici prostředků aplikace logiky, například:

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

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Skrytí dat z historie spuštění pomocí ztunutí

Mnoho aktivačních událostí a akcí mají nastavení skrýt vstupy, výstupy nebo obojí z historie spuštění aplikace logiky. Zde jsou některé [důležité informace, které je třeba zkontrolovat](#obfuscation-considerations) při použití těchto nastavení, které vám pomohou zabezpečit tato data.

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>Skrytí vstupů a výstupů v návrháři

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

   ![Otevření aplikace logiky v Návrháři aplikací logiky](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Na aktivační události nebo akci, kde chcete skrýt citlivá data, vyberte tlačítko elipsy (**...**) a pak vyberte **Nastavení**.

   ![Otevření nastavení aktivační události nebo akce](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Zapněte **zabezpečené vstupy**, **zabezpečené výstupy**nebo obojí. Jakmile budete hotovi, vyberte **Hotovo**.

   ![Zapnutí "Zabezpečené vstupy" nebo "Zabezpečené výstupy"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Akce nebo aktivační událost nyní zobrazuje ikonu zámku v záhlaví.

   ![Ikona záhlaví akce nebo aktivační události zobrazuje ikonu zámku](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokeny, které představují zabezpečené výstupy z předchozích akcí také zobrazit ikony zámku. Například když vyberete takový výstup ze seznamu dynamického obsahu pro použití v akci, tento token zobrazí ikonu zámku.

   ![Vybrat token pro zabezpečený výstup](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Po spuštění aplikace logiky můžete zobrazit historii pro toto spuštění.

   1. V podokně **Přehled** aplikace logiky vyberte spuštění, které chcete zobrazit.

   1. V podokně **spuštění aplikace Logika** rozbalte akce, které chcete zkontrolovat.

      Pokud jste se rozhodli zakrýt vstupy i výstupy, tyto hodnoty se nyní zobrazí skryté.

      ![Skryté vstupy a výstupy v historii běhu](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>Skrytí vstupů a výstupů v zobrazení kódu

V základní definici aktivační události nebo `runtimeConfiguration.secureData.properties` akce přidejte nebo aktualizujte pole s některou z těchto hodnot:

* `"inputs"`: Zabezpečuje vstupy v historii běhu.
* `"outputs"`: Zabezpečuje výstupy v historii běhu.

Zde jsou některé [důležité informace, které je třeba zkontrolovat](#obfuscation-considerations) při použití těchto nastavení, které vám pomohou zabezpečit tato data.

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

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Důležité informace při skrytí vstupů a výstupů

* Když zakryjete vstupy nebo výstupy na aktivační události nebo akci, Logic Apps neodesílá zabezpečená data do Azure Log Analytics. Také nelze přidat [sledované vlastnosti,](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) které aktivační události nebo akce pro sledování.

* Rozhraní [API aplikace logiky pro zpracování historie pracovních postupů](https://docs.microsoft.com/rest/api/logic/) nevrací zabezpečené výstupy.

* Chcete-li skrýt výstupy z akce, která zakrývá vstupy nebo explicitně zakrývá výstupy, ručně zapněte **zabezpečené výstupy** v této akci.

* Ujistěte se, že zapnete **zabezpečené vstupy** nebo **zabezpečené výstupy** v akce navazujících akcí, kde očekáváte, že historie spuštění zakrýt data.

  **Nastavení zabezpečené výstupy**

  Když ručně zapnete **zabezpečené výstupy** v aktivační události nebo akci, Logic Apps zabezpečuje tyto výstupy v historii spuštění. Pokud akce příjem explicitně používá tyto zabezpečené výstupy jako vstupy, logic Apps skryje vstupy této akce v historii spuštění, ale *neumožňuje* nastavení **zabezpečené vstupy** akce.

  ![Zabezpečené výstupy jako vstupy a následný dopad na většinu akcí](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Akce Compose, Analyzovat JSON a Odpověď má pouze **nastavení Zabezpečené vstupy.** Když je zapnuto, nastavení také skryje výstupy těchto akcí. Pokud tyto akce explicitně použít upstream zabezpečené výstupy jako vstupy, Logic Apps skryje vstupy a výstupy těchto akcí, ale *neumožňuje* tyto akce **'zabezpečené vstupy** nastavení. Pokud akce příjem explicitně používá skryté výstupy z compose, analyzovat JSON nebo odpovědi akce jako vstupy, Logic Apps *neskrývá vstupy nebo výstupy této následné akce*.

  ![Zabezpečené výstupy jako vstupy s následným dopadem na konkrétní akce](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Nastavení zabezpečených vstupů**

  Když ručně zapnete **zabezpečené vstupy** v aktivační události nebo akci, Logic Apps zabezpečuje tyto vstupy v historii spuštění. Pokud akce příjem explicitně používá viditelné výstupy z této aktivační události nebo akce jako vstupy, aplikace logiky skryje vstupy této akce příjem dat v historii spuštění, ale *nepovolí* **zabezpečené vstupy** v této akci a neskrývá výstupy této akce.

  ![Zabezpečené vstupy a následný dopad na většinu akcí](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Pokud compose, Analyzovat JSON a reakce akce explicitně použít viditelné výstupy z aktivační události nebo akce, která má zabezpečené vstupy, Logic Apps skryje vstupy a výstupy těchto akcí, ale *neumožňuje* tyto akce **zabezpečené vstupy** nastavení. Pokud akce příjem explicitně používá skryté výstupy z compose, analyzovat JSON nebo odpovědi akce jako vstupy, Logic Apps *neskrývá vstupy nebo výstupy této následné akce*.

  ![Zabezpečené vstupy a následný dopad na konkrétní opatření](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Přístup k vstupům parametrů

Pokud nasadíte v různých prostředích, zvažte parametrizaci hodnot v definici pracovního postupu, které se liší v závislosti na těchto prostředích. Tímto způsobem se můžete vyhnout pevně zakódovaná data pomocí [šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) k nasazení aplikace logiky, ochranu citlivých dat definováním zabezpečených parametrů a předat tato data jako samostatné vstupy prostřednictvím parametrů [šablony](../azure-resource-manager/templates/template-parameters.md) pomocí [souboru parametrů](../azure-resource-manager/templates/parameter-files.md).

Pokud například ověřujete akce HTTP pomocí [služby Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), můžete definovat a zakrýt parametry, které přijímají ID klienta a tajný klíč klienta, které se používají k ověřování. Chcete-li definovat tyto parametry v `parameters` aplikaci logiky, použijte část v definici pracovního postupu aplikace logiky a šablonu Správce prostředků pro nasazení. Chcete-li skrýt hodnoty parametrů, které nechcete zobrazit při úpravách aplikace logiky nebo `securestring` `secureobject` zobrazení historie spuštění, definujte parametry pomocí typu nebo a podle potřeby použijte kódování. Parametry, které mají tento typ nejsou vráceny s definicí prostředku a nejsou přístupné při zobrazení prostředku po nasazení. Chcete-li získat přístup k těmto `@parameters('<parameter-name>')` hodnotám parametrů za běhu, použijte výraz uvnitř definice pracovního postupu. Tento výraz je vyhodnocován pouze za běhu a je popsán [v jazyce definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Pokud použijete parametr v záhlaví nebo textu požadavku, může být tento parametr viditelný při zobrazení historie spuštění aplikace logiky a odchozího požadavku HTTP. Ujistěte se, že jste také odpovídajícím způsobem nastavili zásady přístupu k obsahu. [Obfuskace](#obfuscate) můžete také použít ke skrytí vstupů a výstupů v historii běhu. Autorizační hlavičky nejsou nikdy viditelné prostřednictvím vstupů nebo výstupů. Takže pokud je použit tajný klíč, že tajemství není možné získat.

Další informace naleznete v těchto částech v tomto tématu:

* [Zabezpečené parametry v definicích pracovního postupu](#secure-parameters-workflow)
* [Skrytí dat z historie spuštění pomocí ztunutí](#obfuscate)

Pokud [automatizujete nasazení pro aplikace logiky pomocí šablon Správce prostředků](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete definovat parametry zabezpečené šablony , které jsou [vyhodnocovány](../azure-resource-manager/templates/template-parameters.md)při nasazení, pomocí `securestring` `secureobject` a typů. Chcete-li definovat parametry šablony, použijte `parameters` oddíl nejvyšší úrovně šablony, který je `parameters` samostatný a odlišný od oddílu definice pracovního postupu. Chcete-li zadat hodnoty parametrů šablony, použijte samostatný [soubor parametrů](../azure-resource-manager/templates/parameter-files.md).

Například pokud používáte tajné klíče, můžete definovat a používat parametry zabezpečené šablony, které načítají tyto tajné klíče z [Trezoru klíčů Azure](../key-vault/general/overview.md) při nasazení. Potom můžete odkazovat na trezor klíčů a tajný klíč v souboru parametrů. Další informace najdete v těchto tématech:

* [Předávat citlivé hodnoty při nasazení pomocí azure key vaultu](../azure-resource-manager/templates/key-vault-parameter.md)
* [Zabezpečené parametry v šablonách Azure Resource Manageru](#secure-parameters-deployment-template) dále v tomto tématu

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Zabezpečené parametry v definicích pracovního postupu

Chcete-li chránit citlivé informace v definici pracovního postupu aplikace logiky, použijte zabezpečené parametry, aby tyto informace nejsou viditelné po uložení aplikace logiky. Předpokládejme například, že máte akci HTTP vyžaduje základní ověřování, které používá uživatelské jméno a heslo. V definici pracovního postupu `parameters` `basicAuthPasswordParam` oddíl `basicAuthUsernameParam` definuje parametry `securestring` a pomocí typu. Definice akce pak odkazuje na tyto `authentication` parametry v části.

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Zabezpečené parametry v šablonách Azure Resource Manageru

[Šablona Správce prostředků](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) pro aplikaci logiky má více `parameters` oddílů. Chcete-li chránit hesla, klíče, tajné klíče a další citlivé informace, definujte zabezpečené `securestring` `secureobject` parametry na úrovni šablony a na úrovni definice pracovního postupu pomocí typu nebo. Tyto hodnoty pak můžete uložit do [úložiště klíčů Azure](../key-vault/general/overview.md) a použít soubor [parametrů](../azure-resource-manager/templates/parameter-files.md) k odkazování na trezor klíčů a tajný klíč. Šablona pak načte tyto informace při nasazení. Další informace najdete [v tématu Předání citlivých hodnot při nasazení pomocí služby Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Zde je více `parameters` informací o těchto částech:

* Na nejvyšší úrovni šablony `parameters` definuje oddíl parametry pro hodnoty, které šablona používá při *nasazení*. Tyto hodnoty mohou například zahrnovat připojovací řetězce pro konkrétní prostředí nasazení. Tyto hodnoty pak můžete uložit do samostatného [souboru parametrů](../azure-resource-manager/templates/parameter-files.md), což usnadňuje změnu těchto hodnot.

* Uvnitř definice prostředku aplikace logiky, ale mimo `parameters` definici pracovního postupu, oddíl určuje hodnoty parametrů definice pracovního postupu. V této části můžete přiřadit tyto hodnoty pomocí výrazů šablony, které odkazují na parametry šablony. Tyto výrazy jsou vyhodnocovány při nasazení.

* Uvnitř definice pracovního `parameters` postupu oddíl definuje parametry, které vaše aplikace logiky používá za běhu. Potom můžete odkazovat na tyto parametry v pracovním postupu aplikace logiky pomocí výrazů definice pracovního postupu, které jsou vyhodnocovány za běhu.

Tento příklad šablony, která má více `securestring` definic zabezpečených parametrů, které používají typ:

| Název parametru | Popis |
|----------------|-------------|
| `TemplatePasswordParam` | Parametr šablony, který přijímá heslo, které je poté `basicAuthPasswordParam` předáno parametru definice pracovního postupu |
| `TemplateUsernameParam` | Parametr šablony, který přijímá uživatelské jméno, které je poté `basicAuthUserNameParam` předáno parametru definice pracovního postupu |
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Přístup ke službám a systémům volaných z aplikací logiky

Tady je několik způsobů, jak můžete zabezpečit koncové body, které přijímají volání nebo požadavky z vaší aplikace logiky:

* Přidání ověřování k odchozím požadavkům.

  Při práci s aktivační událost založené http nebo akce, která umožňuje odchozí volání, jako je například HTTP, HTTP + Swagger nebo Webhook, můžete přidat ověřování na požadavek, který je odeslán aplikací logiky. Můžete například použít tyto typy ověřování:

  * [Základní ověřování](#basic-authentication)

  * [Ověření klientského certifikátu](#client-certificate-authentication)

  * [Ověřování o službě Active Directory OAuth](#azure-active-directory-oauth-authentication)

  * [Ověřování spravovaných identit](#managed-identity-authentication)
  
  Další informace naleznete v tématu [Přidání ověřování k odchozím voláním](#add-authentication-outbound) dále v tomto tématu.

* Omezte přístup z IP adres aplikace logiky.

  Všechna volání koncových bodů z aplikací logiky pocházejí z konkrétních určených IP adres, které jsou založeny na oblastech vašich aplikací logiky. Můžete přidat filtrování, které přijímá požadavky pouze z těchto IP adres. Pokud chcete získat tyto IP adresy, přečtěte si informace [o omezeních a konfiguraci pro Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Zlepšete zabezpečení pro připojení k místním systémům.

  Azure Logic Apps poskytuje integraci s těmito službami, které vám pomohou poskytovat bezpečnější a spolehlivější místní komunikaci.

  * Místní brána dat

    Mnoho spravovaných konektorů v Aplikacích Azure Logic Apps usnadňuje zabezpečené připojení k místním systémům, jako je souborový systém, SQL, SharePoint a DB2. Brána odesílá data z místních zdrojů na šifrovaných kanálech prostřednictvím služby Azure Service Bus. Veškerý provoz pochází jako zabezpečený odchozí provoz od agenta brány. [Zjistěte, jak místní brána dat funguje](logic-apps-gateway-install.md#gateway-cloud-service).

  * Připojení prostřednictvím správy rozhraní AZURE API

    [Azure API Management](../api-management/api-management-key-concepts.md) poskytuje možnosti místního připojení, jako je virtuální privátní síť site-to-site a integrace ExpressRoute pro zabezpečené proxy servery a komunikaci s místními systémy. Z pracovního postupu aplikace logiky v Návrháři aplikací logiky můžete vybrat rozhraní API, které je vystaveno službou API Management, která poskytuje rychlý přístup k místním systémům.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Přidání ověřování k odchozím hovorům

Koncové body HTTP a HTTPS podporují různé druhy ověřování. Na základě aktivační události nebo akce, které používáte k odchozím voláním nebo požadavkům, které přistupují k těmto koncovým bodům, můžete vybrat z různých rozsahů typů ověřování. Chcete-li se ujistit, že chráníte všechny citlivé informace, které vaše aplikace logiky zpracovává, použijte zabezpečené parametry a kódujte data podle potřeby. Další informace o použití a zabezpečení parametrů naleznete [v tématu Přístup ke vstupům parametrů](#secure-action-parameters).

> [!NOTE]
> V návrháři aplikace logiky může být vlastnost **Authentication** skryta u některých aktivačních událostí a akcí, kde můžete zadat typ ověřování. Chcete-li, aby se vlastnost v těchto případech zobrazila, otevřete na aktivační události nebo akci seznam **Přidat nový parametr** a vyberte možnost **Ověřování**. Další informace naleznete v [tématu Ověření přístupu se spravovanou identitou](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Typ ověřování | Podporováno |
|---------------------|--------------|
| [Základní](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Klientský certifikát](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Žádný](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Spravovaná identita](#managed-identity-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Základní ověřování

Pokud je k dispozici možnost [Základní,](../active-directory-b2c/secure-rest-api.md) zadejte tyto hodnoty vlastností:

| Nemovitost (návrhář) | Vlastnost (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | Základní | Typ ověřování, který se má použít |
| **Username** | `username` | Ano | <*uživatelské jméno*>| Uživatelské jméno pro ověřování přístupu ke koncovému bodu cílové služby |
| **Heslo** | `password` | Ano | <*Heslo*> | Heslo pro ověřování přístupu ke koncovému bodu cílové služby |
||||||

Při použití [zabezpečené parametry](#secure-action-parameters) pro zpracování a ochranu citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tento příklad definice akce HTTP `type` `Basic` určuje ověřování jako a používá [funkci parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

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

### <a name="client-certificate-authentication"></a>Ověření klientského certifikátu

Pokud je k dispozici možnost [Klientský certifikát,](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) zadejte tyto hodnoty vlastností:

| Nemovitost (návrhář) | Vlastnost (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | **Klientský certifikát** <br>– nebo – <br>`ClientCertificate` | Typ ověřování, který se má použít pro klientské certifikáty TLS/SSL. Zatímco certifikáty podepsané svým držitelem jsou podporovány, certifikáty podepsané svým držitelem pro TLS/SSL nejsou podporovány. |
| **Pfx** | `pfx` | Ano | <*kódovaný-pfx-soubor-obsah*> | Obsah kódovaný base64 ze souboru PFX (Personal Information Exchange) <p><p>Chcete-li převést soubor PFX do formátu kódu base64, můžete použít prostředí PowerShell následujícím způsobem: <p>1. Obsah certifikátu uložte do proměnné: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Převeďte obsah `ToBase64String()` certifikátu pomocí funkce a uložte jej do textového souboru: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Heslo** | `password`| Ne | <*heslo-pro-pfx-soubor*> | Heslo pro přístup k souboru PFX |
|||||

Při použití [zabezpečené parametry](#secure-action-parameters) pro zpracování a ochranu citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tento příklad definice akce HTTP `type` `ClientCertificate` určuje ověřování jako a používá [funkci parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

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

Další informace o zabezpečení služeb pomocí ověřování klientských certifikátů naleznete v těchto tématech:

* [Zlepšení zabezpečení rozhraní API pomocí ověřování klientského certifikátu ve správě rozhraní Azure API](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Zlepšení zabezpečení back-endových služeb pomocí ověřování klientských certifikátů ve správě rozhraní Azure API](../api-management/api-management-howto-mutual-certificates.md)
* [Zlepšete zabezpečení služby RESTfuL pomocí klientských certifikátů](../active-directory-b2c/secure-rest-api.md)
* [Pověření certifikátu pro ověřování aplikací](../active-directory/develop/active-directory-certificate-credentials.md)
* [Použití certifikátu TLS/SSL ve vašem kódu ve službě Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Ověřování Azure Active Directory OAuth

Pokud je k dispozici možnost [OAuth služby Active Directory,](../active-directory/develop/about-microsoft-identity-platform.md) zadejte tyto hodnoty vlastností:

| Nemovitost (návrhář) | Vlastnost (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | **Active Directory OAuth** <br>– nebo – <br>`ActiveDirectoryOAuth` | Typ ověřování, který chcete použít. Logic Apps aktuálně následuje [protokol OAuth 2.0](../active-directory/develop/v2-overview.md). |
| **Autorita** | `authority` | Ne | <*Url-for-a-uthority-token-issuer*> | Adresa URL pro autoritu, která poskytuje ověřovací token. Ve výchozím nastavení `https://login.windows.net`je tato hodnota . |
| **Tenant** | `tenant` | Ano | <*id klienta*> | ID klienta pro klienta Azure AD |
| **Cílová skupina** | `audience` | Ano | <*autorizovat zdroj*> | Prostředek, který chcete použít pro autorizaci, například`https://management.core.windows.net/` |
| **ID klienta** | `clientId` | Ano | <*ID klienta*> | ID klienta pro aplikaci požadující autorizaci |
| **Typ pověření** | `credentialType` | Ano | Certifikát <br>– nebo – <br>Tajný kód | Typ pověření, který klient používá pro vyžádání autorizace. Tato vlastnost a hodnota se nezobrazují v základní definici aplikace logiky, ale určuje vlastnosti, které se zobrazí pro vybraný typ pověření. |
| **Tajný kód** | `secret` | Ano, ale pouze pro typ pověření "Tajné" | <*tajný klíč klienta*> | Tajný klíč klienta pro vyžádání autorizace |
| **Pfx** | `pfx` | Ano, ale pouze pro typ pověření "Certifikát" | <*kódovaný-pfx-soubor-obsah*> | Obsah kódovaný base64 ze souboru PFX (Personal Information Exchange) |
| **Heslo** | `password` | Ano, ale pouze pro typ pověření "Certifikát" | <*heslo-pro-pfx-soubor*> | Heslo pro přístup k souboru PFX |
|||||

Při použití [zabezpečené parametry](#secure-action-parameters) pro zpracování a ochranu citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Definice akce HTTP v tomto `type` `ActiveDirectoryOAuth`příkladu určuje `Secret`ověřování jako , typ pověření as a používá [funkci parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

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

### <a name="raw-authentication"></a>Nezpracovaná autentizace

Pokud je k dispozici možnost **Raw,** můžete tento typ ověřování použít, pokud je třeba použít [schémata ověřování,](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) která nedodržují [protokol OAuth 2.0](https://oauth.net/2/). Pomocí tohoto typu ručně vytvoříte hodnotu hlavičky autorizace, kterou odešlete s odchozím požadavkem, a určíte hodnotu záhlaví v aktivační události nebo akci.

Zde je například ukázková hlavička pro požadavek HTTPS, která následuje po [protokolu OAuth 1.0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

V aktivační události nebo akci, která podporuje nezpracovaná ověřování, zadejte tyto hodnoty vlastností:

| Nemovitost (návrhář) | Vlastnost (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | Žádný | Typ ověřování, který se má použít |
| **Hodnotu** | `value` | Ano | <*hodnota hlavičky autorizace*> | Hodnota hlavičky autorizace, která se má použít pro ověřování |
||||||

Při použití [zabezpečené parametry](#secure-action-parameters) pro zpracování a ochranu citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tento příklad definice akce HTTP `type` `Raw`určuje ověřování jako a používá [funkci parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

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

### <a name="managed-identity-authentication"></a>Ověřování spravovaných identit

Pokud je dostupná možnost [Spravovaná identita,](../active-directory/managed-identities-azure-resources/overview.md) může vaše aplikace logiky použít systémem přiřazenou identitu nebo *jednu* ručně vytvořenou uživatelem přiřazenou identitu pro ověřování přístupu k prostředkům v jiných klientech Služby Azure Active Directory (Azure AD) bez přihlášení. Azure spravuje tuto identitu za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte poskytovat nebo otáčet tajné kódy. Další informace o [službách Azure, které podporují spravované identity pro ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Než vaše aplikace logiky bude moci používat spravovanou identitu, postupujte podle pokynů v [části Ověření přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Tyto kroky umožňují spravovanou identitu v aplikaci logiky a nastavují přístup této identity k cílovému prostředku Azure.

1. Než bude možné, aby funkce Azure používala spravovanou identitu, [povolte](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)nejdřív ověřování pro funkce Azure .

1. V aktivační události nebo akci, kde chcete použít spravovanou identitu, zadejte tyto hodnoty vlastností:

   | Nemovitost (návrhář) | Vlastnost (JSON) | Požaduje se | Hodnota | Popis |
   |---------------------|-----------------|----------|-------|-------------|
   | **Authentication** | `type` | Ano | **Spravovaná identita** <br>– nebo – <br>`ManagedServiceIdentity` | Typ ověřování, který se má použít |
   | **Spravovaná identita** | `identity` | Ano | * **Přiřazená spravovaná identita systému** <br>– nebo – <br>`SystemAssigned` <p><p>* <*uživatelem přiřazené jméno identity*> | Spravovanou identitu, která se má použít |
   | **Cílová skupina** | `audience` | Ano | <*cíl-id zdroje*> | ID prostředku pro cílový prostředek, ke kterému chcete získat přístup. <p>Například `https://storage.azure.com/` umožňuje přístupové tokeny pro ověřování platné pro všechny účty úložiště. Můžete však také zadat adresu URL `https://fabrikamstorageaccount.blob.core.windows.net` kořenové služby, například pro konkrétní účet úložiště. <p>**Poznámka:** Vlastnost **Okruh uživatelů** může být v některých aktivačních událostech nebo akcích skrytá. Chcete-li tuto vlastnost zviditelnit, otevřete v aktivační události nebo akci seznam **Přidat nový parametr** a vyberte **cílovou skupinu**. <p><p>**Důležité:** Ujistěte se, že toto cílové ID prostředku *přesně odpovídá* hodnotě, kterou služba Azure AD očekává, včetně všech požadovaných koncových lomítka. `https://storage.azure.com/` ID prostředků pro všechny účty úložiště objektů blob Azure tedy vyžaduje koncové lomítko. ID prostředku pro konkrétní účet úložiště však nevyžaduje koncové lomítko. Pokud chcete najít tato ID prostředků, přečtěte si informace [o službách Azure, které podporují Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Při použití [zabezpečené parametry](#secure-action-parameters) pro zpracování a ochranu citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tento příklad definice akce HTTP `type` `ManagedServiceIdentity` určuje ověřování jako a používá [funkci parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

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

## <a name="next-steps"></a>Další kroky

* [Automatizace nasazení pro aplikace Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorování aplikací logiky](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnostika selhání a problémů aplikace logiky](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
