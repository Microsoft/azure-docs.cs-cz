---
title: Zabezpečení přístupu a dat
description: Přidejte zabezpečení pro ochranu vstupů, výstupů, triggerů založených na požadavcích, historie spouštění, úloh správy a přístupu k dalším prostředkům v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 753977ed0516e934f661d81904b60ff9935aa423
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981179"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Zabezpečený přístup a data v Azure Logic Apps

K řízení přístupu a ochraně dat v Azure Logic Apps můžete nastavit zabezpečení v těchto oblastech:

* [Přístup k aktivačním událostem založeným na žádostech](#secure-triggers)
* [Přístup k operacím aplikace logiky](#secure-operations)
* [Přístup ke vstupům a výstupům historie spouštění](#secure-run-history)
* [Přístup ke vstupům parametrů](#secure-action-parameters)
* [Přístup ke službám a systémům, které se nazývají aplikace logiky](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Přístup k aktivačním událostem založeným na žádostech

Pokud vaše aplikace logiky používá Trigger založený na požadavku, který přijímá příchozí volání nebo požadavky, jako je [požadavek](../connectors/connectors-native-reqres.md) nebo Trigger [Webhooku](../connectors/connectors-native-webhook.md) , můžete omezit přístup tak, aby vaše aplikace logiky mohli volat jenom autorizovaní klienti. Všechny požadavky přijaté aplikací logiky jsou šifrované a zabezpečené pomocí protokolu SSL (Secure Sockets Layer) (SSL).

Tady jsou způsoby, kterými můžete zabezpečit přístup k tomuto typu triggeru:

* [Generování sdílených přístupových podpisů](#sas)
* [Omezit příchozí IP adresy](#restrict-inbound-ip-addresses)
* [Přidat Azure Active Directory OAuth nebo jiné zabezpečení](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generování sdílených přístupových podpisů (SAS)

Každý koncový bod žádosti v aplikaci logiky má [sdílený přístupový podpis (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) v adrese URL koncového bodu, který se skládá z tohoto formátu:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Každá adresa URL obsahuje parametr dotazu `sp`, `sv`a `sig`, jak je popsáno v této tabulce:

| Parametr dotazu | Popis |
|-----------------|-------------|
| `sp` | Určuje oprávnění povolených metod HTTP, které se mají použít. |
| `sv` | Určuje verzi SAS, která se má použít pro generování podpisu. |
| `sig` | Určuje podpis, který se má použít pro ověřování přístupu k triggeru. Tento podpis se vygeneruje pomocí algoritmu SHA256 s tajným přístupovým klíčem na všech cestách a vlastnostech adresy URL. Tento klíč zůstane zašifrovaný a uložený v aplikaci logiky, který není přístupný nebo publikovaný. Vaše aplikace logiky autorizuje jenom ty triggery, které obsahují platný podpis vytvořený s tajným klíčem. |
|||

Další informace o zabezpečení přístupu pomocí SAS najdete v těchto oddílech v tomto tématu:

* [Znovu vygenerovat přístupové klíče](#access-keys)
* [Vytvoření adres URL zpětného volání pro vypršení platnosti](#expiring-urls)
* [Vytváření adres URL s primárním nebo sekundárním klíčem](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Znovu vygenerovat přístupové klíče

Pokud chcete vygenerovat nový klíč zabezpečeného přístupu, použijte Azure REST API nebo Azure Portal. Všechny dříve generované adresy URL, které používají starý klíč, jsou neověřené a už nebudou mít autorizaci k aktivaci aplikace logiky. Adresy URL, které načtete po obnovení, se podepisují pomocí nového přístupového klíče.

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky, která má klíč, který chcete znovu vygenerovat.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **přístupové klíče**.

1. Vyberte klíč, který chcete znovu vygenerovat a dokončit proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Vytvoření adres URL zpětného volání pro vypršení platnosti

Pokud sdílíte adresu URL koncového bodu pro aktivační událost na základě požadavku s ostatními stranami, můžete vygenerovat adresy URL zpětného volání, které používají konkrétní klíče a mají datum vypršení platnosti. Tímto způsobem můžete plynule vrátit klíče nebo omezit přístup k aktivaci aplikace logiky na základě konkrétního časového rozmezí. Chcete-li zadat datum vypršení platnosti adresy URL, použijte [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), například:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

V těle zahrňte vlastnost `NotAfter`pomocí řetězce data JSON. Tato vlastnost vrátí adresu URL zpětného volání, která je platná pouze do `NotAfter` datum a čas.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Vytváření adres URL s primárním nebo sekundárním tajným klíčem

Když vygenerujete nebo vypíšete adresy URL zpětného volání pro aktivační událost na základě požadavku, můžete zadat klíč, který se má použít k podepsání adresy URL. Chcete-li vygenerovat adresu URL, která je podepsána určitým klíčem, použijte [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), například:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

V těle zahrňte vlastnost `KeyType` buď jako `Primary` nebo `Secondary`. Tato vlastnost vrátí adresu URL, která je podepsána zadaným zabezpečeným klíčem.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Omezit příchozí IP adresy

Spolu se sdíleným přístupovým podpisem (SAS) můžete chtít konkrétně omezit klienty, kteří můžou zavolat aplikaci logiky. Pokud například spravujete koncový bod žádosti pomocí Azure API Management, můžete aplikaci logiky omezit tak, aby přijímala požadavky pouze z IP adresy pro instanci API Management.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Omezit rozsahy příchozích IP adres v Azure Portal

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **Nastavení pracovního postupu**.

1. V části **Konfigurace řízení přístupu** > **povoleny příchozí IP adresy**vyberte **konkrétní rozsahy IP**adres.

1. V části **rozsahy IP pro aktivační události**zadejte rozsahy IP adres, které aktivační událost akceptuje.

   Platný rozsah IP adres používá tyto formáty: *x. x. x. x/x* nebo *x. x. x. x-x. x.* x. x.

Pokud chcete, aby se aplikace logiky spouštěla jenom jako vnořená aplikace logiky, vyberte ze seznamu **povolené IP adresy** **jenom další Logic Apps**. Tato možnost zapíše prázdné pole do prostředku aplikace logiky. Tímto způsobem mohou spustit vnořenou aplikaci logiky pouze volání ze služby Logic Apps (nadřazené aplikace logiky).

> [!NOTE]
> Bez ohledu na IP adresu můžete přesto spustit aplikaci logiky, která má aktivační událost na základě požadavků, a to pomocí `/triggers/<trigger-name>/run` prostřednictvím Azure REST API nebo prostřednictvím API Management. Tento scénář ale pořád vyžaduje ověřování proti REST API Azure. Všechny události se zobrazí v protokolu auditu Azure. Ujistěte se, že jste nastavili zásady řízení přístupu odpovídajícím způsobem.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Omezení rozsahů příchozích IP adres v šabloně Azure Resource Manager

Pokud [automatizujete nasazení Logic Apps pomocí Správce prostředků šablon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete rozsahy IP adres zadat pomocí `accessControl` oddílu `triggers` v definici prostředků vaší aplikace logiky, například:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Přidat Azure Active Directory OAuth nebo jiné zabezpečení

K přidání dalších autorizačních protokolů do aplikace logiky zvažte použití služby [Azure API Management](../api-management/api-management-key-concepts.md) . Tato služba vám pomůže vystavit aplikaci logiky jako rozhraní API a nabízí rozšířené monitorování, zabezpečení, zásady a dokumentaci pro libovolný koncový bod. API Management může vystavit veřejný nebo privátní koncový bod pro vaši aplikaci logiky. K autorizaci přístupu k tomuto koncovému bodu můžete použít [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), [klientský certifikát](#client-certificate-authentication)nebo jiné standardy zabezpečení pro autorizaci přístupu k tomuto koncovému bodu. Když API Management obdrží požadavek, služba pošle požadavek do vaší aplikace logiky, což také vede k potřebným transformacím nebo omezením na cestě. Pokud chcete povolit jenom API Management aktivovat aplikaci logiky, můžete použít nastavení příchozího rozsahu IP adres vaší aplikace logiky.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Přístup k operacím aplikace logiky

Můžete povolit pouze konkrétní uživatele nebo skupiny, aby mohli spouštět konkrétní úkoly, jako je správa, úpravy a zobrazení Logic Apps. K řízení jejich oprávnění použijte [Access Control na základě rolí Azure (RBAC)](../role-based-access-control/role-assignments-portal.md) , abyste členům v předplatném Azure mohli přiřadit přizpůsobené nebo předdefinované role:

* [Přispěvatel aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-contributor): umožňuje spravovat Logic Apps, ale nemůžete pro ně měnit přístup.

* [Operátor aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-operator): umožňuje číst, povolit a zakázat Logic Apps, ale nemůžete je upravovat ani aktualizovat.

Pokud chcete ostatním uživatelům zabránit ve změně nebo odstranění vaší aplikace logiky, můžete použít [Azure Resource Lock](../azure-resource-manager/management/lock-resources.md). Tato možnost zabraňuje ostatním změnám a odstraňování produkčních prostředků.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Přístup k datům historie spouštění

Během provádění aplikace logiky se všechna data [šifrují během přenosu](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) pomocí protokolu TLS (Transport Layer Security) a [v klidovém](../security/fundamentals/encryption-atrest.md)stavu. Až se vaše aplikace logiky dokončí, můžete zobrazit historii pro toto spuštění, včetně kroků, které byly spuštěny spolu se stavem, dobou trvání, vstupy a výstupy pro každou akci. Tato bohatá podrobnosti poskytuje přehled o tom, jak vaše aplikace logiky běžela a kde můžete začít řešit problémy, ke kterým dojde.

Když si zobrazíte historii spuštění vaší aplikace logiky, Logic Apps ověří váš přístup a pak poskytuje odkazy na vstupy a výstupy pro žádosti a odpovědi pro každé spuštění. U akcí, které zpracovávají všechna hesla, tajné klíče, klíče nebo jiné citlivé informace, ale chcete ostatním uživatelům zabránit v prohlížení těchto dat a přístupu k nim. Například pokud vaše aplikace logiky získá tajný kód z [Azure Key Vault](../key-vault/key-vault-overview.md) , který se má použít při ověřování akce HTTP, chcete tento tajný klíč skrýt ze zobrazení.

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

1. V části **Konfigurace řízení přístupu** > **povoleny příchozí IP adresy**vyberte **konkrétní rozsahy IP**adres.

1. V části **rozsahy IP adres pro obsah**zadejte rozsahy IP adres, které budou mít přístup k obsahu ze vstupů a výstupů. 

   Platný rozsah IP adres používá tyto formáty: *x. x. x. x/x* nebo *x. x. x. x-x. x.* x. x.

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Omezení rozsahů IP adres v šabloně Azure Resource Manager

Pokud [automatizujete nasazení Logic Apps pomocí Správce prostředků šablon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete rozsahy IP adres zadat pomocí `accessControl` oddílu `contents` v definici prostředků vaší aplikace logiky, například:

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

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

   ![Otevření aplikace logiky v návrháři aplikace logiky](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. V aktivační události nebo akci, kde chcete zabezpečit data, vyberte tlačítko se třemi tečkami ( **...** ) a pak vyberte **Nastavení**.

   ![Otevřít nastavení triggeru nebo akce](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Zapněte buď **zabezpečené vstupy**, **zabezpečené výstupy**, nebo obojí. Jakmile budete hotovi, vyberte **Hotovo**.

   ![Zapnutí zabezpečených vstupů nebo výstupů](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Akce nebo aktivační událost nyní zobrazuje ikonu zámku v záhlaví.

   ![Záhlaví akce nebo aktivační události zobrazuje ikonu zámku](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokeny, které představují zabezpečené výstupy z předchozích akcí, také zobrazují ikony zámku. Když například vyberete takový výstup ze seznamu dynamického obsahu, který chcete použít v akci, tento token zobrazí ikonu zámku.

   ![Vyberte token pro zabezpečený výstup.](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Po spuštění aplikace logiky můžete zobrazit historii pro daný běh.

   1. V podokně **Přehled** aplikace logiky vyberte běh, který chcete zobrazit.

   1. V podokně **spuštění aplikace logiky** rozbalte akce, které chcete zkontrolovat.

      Pokud se rozhodnete zabezpečit vstupy i výstupy, zobrazí se nyní tyto hodnoty jako skryté.

      ![Skryté vstupy a výstupy v historii spuštění](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

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

* Chcete-li zabezpečit výstupy z akce, která zabezpečuje vstupy nebo explicitně používá zabezpečené výstupy, ručně zapněte v této akci **zabezpečené výstupy** .

* Ujistěte se, že jste zapnuli **zabezpečené vstupy** nebo **zabezpečené výstupy** v podřízených akcích, u kterých očekáváte, že historie spouštění zabezpečuje tato data.

  **Nastavení zabezpečených výstupů**

  Když ručně zapnete **zabezpečené výstupy** v triggeru nebo akci, Logic Apps tyto výstupy zabezpečit v historii spuštění. Pokud akce pro příjem dat explicitně používá tyto zabezpečené výstupy jako vstupy, Logic Apps skrývá vstupy této akce v historii spuštění, ale *nepovolí* nastavení **zabezpečených vstupů** akce.

  ![Zabezpečené výstupy jako vstupy a přínosy při většině akcí](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Akce vytvořit, analyzovat JSON a odpověď mají pouze nastavení **zabezpečené vstupy** . Když je tato možnost zapnutá, tato nastavení také skryje výstupy těchto akcí. Pokud tyto akce explicitně používají výstupy proti nadřazenému objektu jako vstupy, Logic Apps skrývá vstupy a výstupy těchto akcí, ale *nepovoluje* nastavení **zabezpečených vstupů** těchto akcí. Pokud akce pro příjem dat explicitně používá skryté výstupy z akcí vytvořit, analyzovat JSON nebo odpovědět jako vstupy, Logic Apps *neskryje tyto vstupy a výstupy této akce*.

  ![Zabezpečené výstupy jako vstupy s vlivem na konkrétní akce na základě dat](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Nastavení zabezpečených vstupů**

  Když ručně zapnete **zabezpečené vstupy** v triggeru nebo akci, Logic Apps tyto vstupy zabezpečit v historii spuštění. Pokud akce pro příjem dat explicitně používá viditelné výstupy z této triggeru nebo akce jako vstupy, Logic Apps skrývá tyto vstupy v historii spuštění, ale *nepovoluje* v této akci **zabezpečené vstupy** a neskrývá výstupy této akce.

  ![Zabezpečené vstupy a důsledky pro všechny akce](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Pokud akce vytvořit, analyzovat JSON a odpověď explicitně použijí viditelné výstupy z triggeru nebo akce, která má zabezpečené vstupy, Logic Apps skryje tyto vstupy a výstupy těchto akcí, ale *nepovolí* nastavení **zabezpečených vstupů** těchto akcí. Pokud akce pro příjem dat explicitně používá skryté výstupy z akcí vytvořit, analyzovat JSON nebo odpovědět jako vstupy, Logic Apps *neskryje tyto vstupy a výstupy této akce*.

  ![Zabezpečené vstupy a důsledky pro konkrétní akce](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Přístup ke vstupům parametrů

Pokud nasazujete v různých prostředích, zvažte Parametrizace hodnot v definici pracovního postupu, které se liší v závislosti na těchto prostředích. Tímto způsobem se můžete vyhnout pevně zakódovaným datům pomocí [šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) k nasazení aplikace logiky, ochraně citlivých dat definováním zabezpečených parametrů a předání těchto dat jako samostatných vstupů prostřednictvím [parametrů šablony](../azure-resource-manager/templates/template-parameters.md) pomocí [souboru parametrů](../azure-resource-manager/templates/parameter-files.md).

Pokud například ověříte akce HTTP pomocí [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), můžete definovat a zabezpečit parametry, které přijímají ID klienta a tajný klíč klienta, které se používají pro ověřování. K definování těchto parametrů v aplikaci logiky použijte `parameters` v definici pracovního postupu vaší aplikace logiky a šablonu Správce prostředků pro nasazení. Chcete-li skrýt hodnoty parametrů, které nechcete zobrazit při úpravách aplikace logiky nebo zobrazení historie spuštění, definujte parametry pomocí `securestring` nebo `secureobject` typ a podle potřeby kódování použijte. Parametry, které mají tento typ, nejsou vráceny s definicí prostředků a nejsou přístupné při zobrazení prostředku po nasazení. Pro přístup k těmto hodnotám parametrů za běhu použijte výraz `@parameters('<parameter-name>')` uvnitř definice pracovního postupu. Tento výraz je vyhodnocen pouze za běhu a je popsán [jazykem definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Pokud použijete parametr v hlavičce nebo těle žádosti, tento parametr může být viditelný při zobrazení historie spuštění vaší aplikace logiky a odchozího požadavku HTTP. Ujistěte se, že jste odpovídajícím způsobem také nastavili zásady přístupu k obsahu. K skrytí vstupů a výstupů v historii spuštění můžete také použít [zmatení](#obfuscate) . Autorizační hlavičky nejsou nikdy viditelné prostřednictvím vstupů nebo výstupů. Takže pokud se tam používá tajný klíč, nedá se tento tajný klíč získat.

Další informace najdete v těchto oddílech v tomto tématu:

* [Zabezpečené parametry v definicích pracovních postupů](#secure-parameters-workflow)
* [Skrýt data z historie spouštění pomocí zmatenosti](#obfuscate)

Pokud [automatizujete nasazení pro Logic Apps pomocí Správce prostředků šablon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete definovat zabezpečené [parametry šablony](../azure-resource-manager/templates/template-parameters.md), které jsou vyhodnocovány při nasazení pomocí `securestring` a `secureobject` typů. Pro definování parametrů šablony použijte `parameters` sekci nejvyšší úrovně šablony, která je oddělená a odlišná od `parameters` oddílu definice pracovního postupu. Chcete-li zadat hodnoty parametrů šablony, použijte samostatný [soubor parametrů](../azure-resource-manager/templates/parameter-files.md).

Pokud například používáte tajné kódy, můžete definovat a používat zabezpečené parametry šablony, které tyto tajné kódy načítají z [Azure Key Vault](../key-vault/key-vault-overview.md) při nasazení. Pak můžete odkazovat na Trezor klíčů a tajný kód v souboru parametrů. Další informace najdete v těchto tématech:

* [Předejte citlivé hodnoty při nasazení pomocí Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
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

[Šablona správce prostředků](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) pro aplikaci logiky má několik oddílů `parameters`. K ochraně hesel, klíčů, tajných kódů a dalších citlivých informací můžete definovat zabezpečené parametry na úrovni šablony a definice pracovního postupu pomocí typu `securestring` nebo `secureobject`. Tyto hodnoty pak můžete uložit v [Azure Key Vault](../key-vault/key-vault-overview.md) a použít [soubor parametrů](../azure-resource-manager/templates/parameter-files.md) k odkazování na Trezor klíčů a tajný kód. Vaše šablona pak tyto informace načte při nasazení. Další informace najdete v tématu [předání citlivých hodnot při nasazení pomocí Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Zde jsou další informace o těchto `parameters` oddílech:

* V nejvyšší úrovni šablony `parameters` oddíl definuje parametry pro hodnoty, které šablona používá při *nasazení*. Například tyto hodnoty mohou zahrnovat připojovací řetězce pro konkrétní prostředí nasazení. Tyto hodnoty pak můžete uložit do samostatného [souboru parametrů](../azure-resource-manager/templates/parameter-files.md), což usnadňuje změnu těchto hodnot.

* V definici prostředků vaší aplikace logiky, ale mimo definici pracovního postupu, určuje `parameters` oddíl hodnoty pro parametry definice pracovního postupu. V této části můžete přiřadit tyto hodnoty pomocí výrazů šablony, které odkazují na parametry šablony. Tyto výrazy jsou vyhodnocovány při nasazení.

* V rámci definice pracovního postupu `parameters` oddíl definuje parametry, které vaše aplikace logiky používá za běhu. Pak můžete odkazovat na tyto parametry uvnitř pracovního postupu aplikace logiky pomocí výrazů definice pracovního postupu, které jsou vyhodnocovány za běhu.

Tento příklad šablony s více zabezpečenými definicemi parametrů, které používají `securestring` typ:

| Název parametru | Popis |
|----------------|-------------|
| `TemplatePasswordParam` | Parametr šablony, který přijímá heslo, které se pak předává do parametru `basicAuthPasswordParam` definice pracovního postupu |
| `TemplateUsernameParam` | Parametr šablony, který přijímá uživatelské jméno, které je pak předáno do parametru `basicAuthUserNameParam` definice pracovního postupu |
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

Tady je několik způsobů, jak můžete zabezpečit koncové body, které přijímají volání nebo požadavky z vaší aplikace logiky:

* Přidání ověřování do odchozích požadavků.

  Když pracujete s triggerem založeným na protokolu HTTP nebo akcí, která umožňuje odchozí volání, jako je HTTP, HTTP + Swagger nebo Webhook, můžete do žádosti, kterou odesílá aplikace logiky, přidat ověřování. Můžete například použít tyto typy ověřování:

  * [Základní ověřování](#basic-authentication)

  * [Ověřování certifikátu klienta](#client-certificate-authentication)

  * [Ověřování služby Active Directory OAuth](#azure-active-directory-oauth-authentication)

  * [Spravované ověřování identity](#managed-identity-authentication)
  
  Další informace najdete v části [Přidání ověřování do odchozích volání](#add-authentication-outbound) dále v tomto tématu.

* Omezte přístup z IP adres aplikace logiky.

  Všechna volání koncových bodů z aplikací logiky pocházejí z konkrétních určených IP adres, které jsou založeny na oblastech vaší Logic Apps. Můžete přidat filtrování, které přijímá požadavky pouze z těchto IP adres. Pokud chcete získat tyto IP adresy, přečtěte si téma [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Zabezpečená připojení k místním systémům.

  Azure Logic Apps poskytuje integraci s těmito službami pro zabezpečenou a spolehlivou místní komunikaci.

  * Místní brána dat

    Řada spravovaných konektorů v Azure Logic Apps poskytuje zabezpečená připojení k místním systémům, jako je například systém souborů, SQL, SharePoint a DB2. Brána odesílá data z místních zdrojů do šifrovaných kanálů prostřednictvím Azure Service Bus. Veškerý provoz vychází z agenta brány na zabezpečený odchozí provoz. Přečtěte si, [Jak funguje místní brána dat](logic-apps-gateway-install.md#gateway-cloud-service).

  * Připojení prostřednictvím Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) poskytuje možnosti místního připojení, jako je virtuální privátní síť Site-to-site a integrace ExpressRoute pro zabezpečený proxy a komunikaci s místními systémy. Z pracovního postupu aplikace logiky v návrháři aplikace logiky můžete vybrat rozhraní API, které je vystaveno API Management, které poskytuje rychlý přístup k místním systémům.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Přidání ověřování do odchozích volání

Koncové body HTTP a HTTPS podporují různé druhy ověřování. V závislosti na triggeru nebo akci, kterou použijete k provedení odchozích volání nebo požadavků, které přistupují k těmto koncovým bodům, můžete vybrat z různých rozsahů typů ověřování. Aby bylo zajištěno, že budete chránit jakékoli citlivé informace, které vaše aplikace logiky zpracovává, používejte v případě potřeby zabezpečené parametry a zakódovat data. Další informace o použití a zabezpečení parametrů naleznete v tématu [přístup ke vstupům parametrů](#secure-action-parameters).

| Typ ověřování | Podporováno rozhraním |
|---------------------|--------------|
| [Basic](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, Webhook HTTP |
| [Certifikát klienta](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, Webhook HTTP |
| [Protokol OAuth pro Active Directory](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
| [Získání](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
| [Spravovaná identita](#managed-identity-authentication) (jenom pro systém) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
|||

> [!NOTE]
> V návrháři aplikace logiky může být vlastnost **ověřování** skrytá na některých triggerech a akcích, kde můžete zadat typ ověřování. Chcete-li vlastnost v těchto případech zobrazit, otevřete u triggeru nebo akce seznam **Přidat nový parametr** a vyberte možnost **ověřování**. Další informace najdete v tématu [ověření přístupu ke spravované identitě](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Základní ověřování

Pokud je k dispozici možnost [základní](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) , zadejte tyto hodnoty vlastností:

| Property – vlastnost (Designer) | Property (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Ověřování** | `type` | Ano | Úroveň Basic | Typ ověřování, který se má použít |
| **Uživatelské jméno** | `username` | Ano | <*jméno uživatele*>| Uživatelské jméno pro ověřování přístupu k cílovému koncovému bodu služby |
| **Heslo** | `password` | Ano | <*hesla*> | Heslo pro ověřování přístupu k cílovému koncovému bodu služby |
||||||

Když použijete [zabezpečené parametry](#secure-action-parameters) k obsluze a ochraně citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tato ukázka definice akce HTTP určuje `type` ověřování jako `Basic` a používá [funkci Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

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

### <a name="client-certificate-authentication"></a>Ověřování certifikátu klienta

Pokud je k dispozici možnost [certifikát klienta](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) , zadejte tyto hodnoty vlastností:

| Property – vlastnost (Designer) | Property (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Ověřování** | `type` | Ano | **Certifikát klienta** <br>– nebo – <br>`ClientCertificate` | Typ ověřování, který se má použít pro SSL (Secure Sockets Layer) (SSL) klientské certifikáty. I když jsou certifikáty podepsané svým držitelem podporovány, certifikáty podepsané svým držitelem pro SSL nejsou podporovány. |
| **PFX** | `pfx` | Ano | <*kódovaný soubor-PFX-Content-content*> | Obsah kódovaný v kódování Base64 ze souboru PFX (Personal Information Exchange) <p><p>Chcete-li převést soubor PFX na formát s kódováním base64, můžete použít PowerShell pomocí následujících kroků: <p>1. Uložte obsah certifikátu do proměnné: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. pomocí funkce `ToBase64String()` převeďte obsah certifikátu a uložte ho do textového souboru: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Heslo** | `password`| Viz popis | <*hesla-pro-soubor pfx*> | Heslo pro přístup k souboru PFX. <p><p>**Poznámka**: Tato hodnota vlastnosti je povinná při práci v návrháři aplikace logiky a při práci v zobrazení *kódu se nevyžaduje* . |
|||||

Když použijete [zabezpečené parametry](#secure-action-parameters) k obsluze a ochraně citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tato ukázka definice akce HTTP určuje `type` ověřování jako `ClientCertificate` a používá [funkci Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

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

* [Zabezpečte rozhraní API pomocí ověřování klientských certifikátů v Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Zabezpečení back-endové služby pomocí ověřování klientských certifikátů v Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Zabezpečte službu RESTfuL pomocí klientských certifikátů.](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
* [Přihlašovací údaje certifikátu pro ověřování aplikací](../active-directory/develop/active-directory-certificate-credentials.md)
* [Použijte certifikát SSL v kódu aplikace v Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory ověřování OAuth

Pokud je k dispozici možnost [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) , zadejte tyto hodnoty vlastností:

| Property – vlastnost (Designer) | Property (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Ověřování** | `type` | Ano | **Protokol OAuth pro Active Directory** <br>– nebo – <br>`ActiveDirectoryOAuth` | Typ ověřování, který se má použít. Logic Apps v současnosti následuje [protokol OAuth 2,0](../active-directory/develop/v2-overview.md). |
| **Tenant** | `tenant` | Ano | <*tenant-ID*> | ID tenanta pro tenanta Azure AD |
| **Osoby** | `audience` | Ano | <*resource-to-authorize*> | Prostředek, který chcete použít pro autorizaci, například `https://management.core.windows.net/` |
| **ID klienta** | `clientId` | Ano | <*client-ID*> | ID klienta pro aplikaci požadující autorizaci |
| **Typ přihlašovacích údajů** | `credentialType` | Ano | Certifikát <br>– nebo – <br>Tajný kód | Typ přihlašovacích údajů, který klient používá k vyžádání autorizace. Tato vlastnost a hodnota se nezobrazí v základní definici vaší aplikace logiky, ale určuje vlastnosti, které se zobrazí pro vybraný typ přihlašovacích údajů. |
| **Tajný kód** | `secret` | Ano, ale jenom pro typ přihlašovacích údajů tajného klíče | <> *tajného klienta* | Tajný klíč klienta pro vyžádání autorizace |
| **PFX** | `pfx` | Ano, ale pouze pro typ přihlašovacích údajů certifikát | <*kódovaný soubor-PFX-Content-content*> | Obsah kódovaný v kódování Base64 ze souboru PFX (Personal Information Exchange) |
| **Heslo** | `password` | Ano, ale pouze pro typ přihlašovacích údajů certifikát | <*hesla-pro-soubor pfx*> | Heslo pro přístup k souboru PFX |
| **Dohled** | `authority` | Ne | <*URL-for-authority-token-issuer*> | Adresa URL pro autoritu, která poskytuje ověřovací token. Ve výchozím nastavení je tato hodnota `https://login.windows.net`. <p>**Poznámka**: Chcete-li tuto vlastnost zviditelnit v návrháři, otevřete u triggeru nebo akce seznam **Přidat nový parametr** a vyberte možnost **autorita**. |
|||||

Když použijete [zabezpečené parametry](#secure-action-parameters) k obsluze a ochraně citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tato ukázka definice akce HTTP určuje `type` ověřování jako `ActiveDirectoryOAuth`, typ přihlašovacích údajů jako `Secret`a pomocí [funkce Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) Získá hodnoty parametrů:

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

### <a name="raw-authentication"></a>Nezpracované ověřování

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

| Property – vlastnost (Designer) | Property (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Ověřování** | `type` | Ano | Nezpracováno | Typ ověřování, který se má použít |
| **Hodnota** | `value` | Ano | *autorizace <– hodnota hlavičky*> | Hodnota hlavičky autorizace, která se má použít pro ověřování |
||||||

Když použijete [zabezpečené parametry](#secure-action-parameters) k obsluze a ochraně citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tato ukázka definice akce HTTP určuje `type` ověřování jako `Raw`a používá [funkci Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

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

Pokud je k dispozici možnost [spravovaná identita](../active-directory/managed-identities-azure-resources/overview.md) , vaše aplikace logiky může používat identitu přiřazenou systémem pro ověřování přístupu k prostředkům v jiných klientech Azure Active Directory (Azure AD), aniž by se museli přihlašovat. Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte zadávat ani otáčet tajné klíče. Přečtěte si další informace o [službách Azure, které podporují spravované identity pro ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Předtím, než aplikace logiky bude moci používat identitu přiřazenou systémem, postupujte podle kroků v části [ověření přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Tyto kroky povolí spravovanou identitu ve vaší aplikaci logiky a nastaví přístup této identity k cílovému prostředku Azure.

2. Než může funkce Azure využít identitu přiřazenou systémem, nejdřív [Povolte ověřování pro službu Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

3. V aktivační události nebo akci, kde chcete použít spravovanou identitu, zadejte tyto hodnoty vlastností:

   | Property – vlastnost (Designer) | Property (JSON) | Požaduje se | Hodnota | Popis |
   |---------------------|-----------------|----------|-------|-------------|
   | **Ověřování** | `type` | Ano | **Spravovaná identita** <br>– nebo – <br>`ManagedServiceIdentity` | Typ ověřování, který se má použít |
   | **Osoby** | `audience` | Ano | <*target-Resource-ID*> | ID prostředku pro cílový prostředek, ke kterému chcete získat přístup. <p>`https://storage.azure.com/` například zpřístupňuje přístupové tokeny pro ověřování platné pro všechny účty úložiště. Můžete ale taky zadat adresu URL kořenové služby, například `https://fabrikamstorageaccount.blob.core.windows.net` pro konkrétní účet úložiště. <p>**Poznámka**: Tato vlastnost může být v některých triggerech nebo akcích skrytá. Chcete-li tuto vlastnost zviditelnit, otevřete v aktivační události nebo akci seznam **Přidat nový parametr** a vyberte možnost **cílová skupina**. <p><p>**Důležité**: Ujistěte se, že toto ID cílového prostředku přesně odpovídá hodnotě, kterou očekává služba Azure AD, včetně všech požadovaných koncových lomítek. ID prostředku `https://storage.azure.com/` pro všechny účty Azure Blob Storage vyžaduje koncové lomítko. ID prostředku pro konkrétní účet úložiště ale nevyžaduje koncové lomítko. Tato ID prostředků najdete v tématu [služby Azure, které podporují Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Když použijete [zabezpečené parametry](#secure-action-parameters) k obsluze a ochraně citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tato ukázka definice akce HTTP určuje `type` ověřování jako `ManagedServiceIdentity` a používá [funkci Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>Další kroky

* [Automatizace nasazení pro Azure Logic Apps](logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorování aplikací logiky](logic-apps-monitor-your-logic-apps.md)  
* [Diagnostika chyb a problémů aplikace logiky](logic-apps-diagnosing-failures.md)  
* [Automatizace nasazení aplikace logiky](logic-apps-azure-resource-manager-templates-overview.md)
