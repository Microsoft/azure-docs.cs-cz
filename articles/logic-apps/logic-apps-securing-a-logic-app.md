---
title: Zabezpečený přístup k Azure Logic Apps | Dokumentace Microsoftu
description: Ochrana přístupu k aktivačních událostí, vstupů a výstupů, parametry akce a služby v pracovních postupech pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 11/22/2016
ms.openlocfilehash: 0fe35b67a424caedcea2c71885d1757943ace9d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232592"
---
# <a name="secure-access-in-azure-logic-apps"></a>Zabezpečený přístup v Azure Logic Apps

Způsoby, abyste měli zabezpečený přístup k různým součástem ve vaší aplikaci logiky:

* Zabezpečený přístup, která aktivuje pracovní postup aplikace logiky s triggerem požadavku HTTP.
* Zabezpečený přístup pro správu, úpravy nebo načítání aplikace logiky.
* Zabezpečený přístup k obsahu uvnitř vstupů a výstupů pro běh aplikace logiky.
* Zabezpečte parametry nebo vstupy pro akce pracovní postup aplikace logiky.
* Zabezpečený přístup ke službám, které přijímají požadavky z pracovního postupu aplikace logiky.

## <a name="secure-access-to-trigger"></a>Zabezpečený přístup k aktivaci

Při práci s využitím aplikace logiky, který se aktivuje v požadavku HTTP ([žádosti](../connectors/connectors-native-reqres.md) nebo [Webhooku](../connectors/connectors-native-webhook.md)), můžete omezit přístup tak, aby pouze autorizovaní klienti můžou aktivovat aplikaci logiky. Všechny žádosti do aplikace logiky jsou zašifrované a zabezpečené pomocí protokolu SSL.

### <a name="shared-access-signature"></a>Sdílený přístupový podpis

Každý požadavek koncový bod pro aplikaci logiky. zahrnuje [sdíleného přístupového podpisu (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) jako část adresy URL. Každá adresa URL obsahuje `sp`, `sv`, a `sig` parametr dotazu. Oprávnění jsou určena podle `sp`a odpovídat na metody HTTP povolené, `sv` je verze použitá ke generování, a `sig` se používá k ověření přístupu k aktivaci. Podpis je generována algoritmem SHA256 s tajným klíčem na cestách URL a vlastnosti. Tajný klíč je nikdy vystavena a publikování a zůstane zašifrované a uložené jako součást aplikace logiky. Aplikace logiky pouze autorizuje aktivačních událostí, které obsahují platný podpis vytvořené pomocí tajného klíče.

#### <a name="regenerate-access-keys"></a>Opětovné vygenerování přístupových klíčů

Nové zabezpečený klíč na kdykoli přes rozhraní REST API nebo Azure portal můžete vygenerovat znovu. Všechny aktuální adresy URL, které byly generovány dříve pomocí starého klíče jsou zneplatněny a už nemá oprávnění, která se aktivuje aplikace logiky.

1. Na webu Azure Portal otevřete aplikaci logiky, kterou chcete znovu vygenerovat klíč
1. Klikněte na tlačítko **přístupové klíče** položky nabídky v části **nastavení**
1. Zvolte klíč znovu vygenerujte a dokončení procesu

Adresy URL, kterou načtete po obnovení jsou podepsány pomocí nového přístupového klíče.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Vytváří se datum vypršení platnosti adresy URL zpětného volání

Pokud sdílíte s ostatními stranami adresu URL, můžete vytvořit adresy URL s konkrétní klíče a data vypršení platnosti podle potřeby. Můžete pak bezproblémově vrátit klíče, nebo zajistěte, aby přístup k aktivuje aplikace je omezen na určité časové rozpětí. Můžete zadat datum vypršení platnosti adresy URL prostřednictvím [logic apps rozhraní REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

V textu, patří vlastnost `NotAfter` jako řetězec data JSON, který vrátí adresu URL zpětného volání, která je jenom platné až do `NotAfter` datum a čas.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Vytvoření adresy URL s primární a sekundární tajný klíč

Při generování nebo seznamu adres URL zpětného volání pro triggery založené na požadavku, můžete také zadat klávesy, která se má použít k podepsání adresu URL.  Můžete generovat podepsány konkrétního klíče prostřednictvím adresy URL [logic apps rozhraní REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) následujícím způsobem:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

V textu, patří vlastnost `KeyType` jako `Primary` nebo `Secondary`.  Vrátí adresu URL podepsány zabezpečené zadaný klíč.

### <a name="restrict-incoming-ip-addresses"></a>Omezit příchozí IP adresy

Kromě sdíleného přístupového podpisu můžete chtít omezit volání aplikace logiky pouze z konkrétních klientů.  Například pokud váš koncový bod prostřednictvím služby Azure API Management spravujete, můžete omezit aplikace logiky tak, aby přijímal pouze žádost, když požadavek pochází z IP adresy instance API Management.

Tato nastavení se dá nakonfigurovat v nastavení aplikace logiky:

1. Na webu Azure Portal otevřete aplikaci logiky, které chcete přidat omezení podle IP adresy
1. Klikněte na tlačítko **nastavení pracovního postupu** položky nabídky v části **nastavení**
1. Zadejte seznam rozsahů IP adres na přijetí aktivační událost

Platný rozsah IP adres má formát `192.168.1.1/32`. Pokud chcete aplikaci logiky tak, aby se aktivovaly jenom jako vnořenou aplikaci logiky, vyberte **pouze jiných funkcí logic apps** možnost. Tato možnost zapíše prázdné pole k prostředku význam jen volá službu samotnou (nadřazená aplikace logiky) úspěšně vyvolat.

> [!NOTE]
> Můžete spustit aplikaci logiky s triggerem požadavku prostřednictvím rozhraní REST API nebo správu `/triggers/{triggerName}/run` bez ohledu na IP. Tento scénář vyžaduje ověřování pomocí rozhraní Azure REST API a všechny události se objeví v protokolu auditu Azure. Zásady řízení přístupu sady odpovídajícím způsobem.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Nastavení rozsahů IP adres v definici prostředků

Pokud používáte [šablonu nasazení](logic-apps-create-deploy-template.md) automatizovat vaše nasazení, nastavení rozsahu IP adresy lze nakonfigurovat podle šablony prostředků.  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Přidání služby Azure Active Directory, OAuth nebo jiných zabezpečení

Chcete-li přidat další ověřovací protokoly na aplikaci logiky, [Azure API Management](https://azure.microsoft.com/services/api-management/) nabízí bohaté možnosti monitorování, zabezpečení, zásady a dokumentace pro libovolný koncový bod s možností zveřejnit aplikaci logiky jako rozhraní API. Azure API Management můžete zveřejnit koncový bod veřejné nebo soukromé pro aplikaci logiky, která může použít Azure Active Directory, certifikát, OAuth nebo jiné standardy zabezpečení. Při přijetí požadavku Azure API Management předá tento požadavek na aplikaci logiky (provádí se všechny potřebné transformace nebo omezení vydávaných za pochodu). Příchozí nastavení rozsahu IP adresy na aplikaci logiky můžete povolit jenom aplikace logiky, až se spustí ze služby API Management.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Zabezpečený přístup ke správě nebo úprava aplikací logiky

Můžete omezit přístup k operace správy v aplikaci logiky tak, aby jenom konkrétní uživatelé nebo skupiny můžou provádět operace u daného prostředku. Pomocí Azure Logic apps [řízení přístupu na základě Role (RBAC)](../role-based-access-control/role-assignments-portal.md) funkcí a je možné přizpůsobit pomocí stejných nástrojů.  Existuje několik předdefinovaných rolí, které můžou přiřazovat členy vašeho předplatného, které se také:

* **Přispěvatel aplikace logiky** – poskytuje přístup k zobrazit, upravit a aktualizovat aplikace logiky.  Nelze odebrat prostředek nebo provádění operací správy.
* **Operátor aplikace logiky** – můžete zobrazit aplikace logiky a historie spuštění a povolit nebo zakázat.  Nelze upravovat nebo aktualizovat definici.

Můžete také použít [zámek prostředku Azure](../azure-resource-manager/resource-group-lock-resources.md) zabránit změně nebo odstranění aplikace logiky. Tato možnost je důležité zabránit produkční prostředky z změny nebo odstranění.

## <a name="secure-access-to-contents-of-the-run-history"></a>Zabezpečený přístup k obsahu z historie spuštění

Můžete omezit přístup k obsahu vstupy nebo výstupy z předchozích spuštění na konkrétní rozsahy IP adres.  

Všechna data v rámci běh pracovního postupu se šifrují přenášená i neaktivní uložená. Pokud je uskutečněn hovor na historie spuštění, službě ověří žádost a poskytuje odkazy na žádostí a odpovědí vstupy a výstupy. Tento odkaz se dají chránit obsah vrátit pouze požadavky, chcete-li zobrazit obsah z určeného rozsahu IP adres. Tuto funkci můžete použít pro řízení přístupu na další. Můžete zadat i IP adresy jako `0.0.0.0` tak nemá nikdo přístup vstupy/výstupy. Jenom člověk s oprávněními správce může odebrat toto omezení, poskytuje možnost "just-in-time" přístup k obsahu pracovního postupu.

Tato nastavení se dá nakonfigurovat v rámci nastavení prostředků na webu Azure portal:

1. Na webu Azure Portal otevřete aplikaci logiky, které chcete přidat omezení podle IP adresy
2. Klikněte na tlačítko **konfigurace řízení přístupu** položky nabídky v části **nastavení**
3. Zadejte seznam rozsahů IP adres pro přístup k obsahu

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Nastavení rozsahů IP adres v definici prostředků

Pokud používáte [šablonu nasazení](logic-apps-create-deploy-template.md) automatizovat vaše nasazení, nastavení rozsahu IP adresy lze nakonfigurovat podle šablony prostředků.  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Zabezpečení parametry a vstupy v rámci pracovního postupu

Můžete chtít parametrizovat některé aspekty definice pracovního postupu pro nasazení napříč prostředími. Některé parametry můžou být zabezpečené parametry, které nechcete zobrazit při úpravách pracovního postupu, jako je například ID klienta a tajný kód klienta pro [ověřování Azure Active Directory](../connectors/connectors-native-http.md#authentication) akce HTTP.

### <a name="using-parameters-and-secure-parameters"></a>Pomocí parametrů a zabezpečené parametry

Pro přístup k hodnotě parametru prostředků v době běhu [jazyka definice pracovního postupu](https://aka.ms/logicappsdocs) poskytuje `@parameters()` operace. Můžete také [zadejte parametry v šabloně prostředku nasazení](../azure-resource-manager/resource-group-authoring-templates.md#parameters). Ale pokud zadáte typ parametru jako `securestring`, parametr se nevrátí se zbytkem definice prostředku a zobrazením prostředek po nasazení budou nepřístupná.

> [!NOTE]
> Pokud váš parametr se používá v záhlaví a text žádosti, může být parametr viditelné díky přístupu do historie spouštění a odchozí požadavek HTTP. Ujistěte se, že se odpovídajícím způsobem nastavit zásady přístup k obsahu.
> Autorizační hlavičky nejsou nikdy zobrazit vstupy nebo výstupy. Proto pokud tajný klíč je používán existuje, není získat tajný kód.

#### <a name="resource-deployment-template-with-secrets"></a>Šablona nasazení prostředků s tajnými kódy

Následující příklad ukazuje nasazení, které odkazuje parametr zabezpečené `secret` za běhu. V souboru oddělené parametry můžete zadat hodnotu prostředí `secret`, nebo použijte [trezor klíčů Azure Resource Manageru](../azure-resource-manager/resource-manager-keyvault-parameter.md) načíst tajné kódy v nasazení čas.

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "secretDeploymentParam": {
         "type": "securestring"
      }
   },
   "variables": {},
   "resources": [ {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
         "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
         "definition": {
            "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
            "actions": {
               "Call_External_API": {
                  "type": "Http",
                  "inputs": {
                     "headers": {
                        "Authorization": "@parameters('secret')"
                     },
                     "body": "This is the request"
                  },
                  "runAfter": {}
               }
            },
            "parameters": {
               "secret": {
                  "type": "SecureString"
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
            "secret": {
               "value": "[parameters('secretDeploymentParam')]"
            }
         }
      }
   } ],
   "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Zabezpečený přístup ke službám přijímání požadavků z pracovního postupu

Existuje mnoho způsobů pomáhají zabezpečit libovolný koncový bod, který potřebuje přístup k aplikaci logiky.

### <a name="using-authentication-on-outbound-requests"></a>Použití ověřování na odchozí žádosti

Při práci s HTTP, HTTP + Swagger (Open API) nebo akce Webhooku, můžete přidat ověřování na žádost o odeslání. Můžete zahrnout základní ověřování, ověřování pomocí certifikátu nebo ověřování Azure Active Directory. Najdete podrobnosti o tom, jak nakonfigurovat ověřování [v tomto článku](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Omezení přístupu na IP adresy aplikace logiky

Všechna volání z aplikace logiky pocházet z konkrétní sady IP adres v jedné oblasti. Můžete přidat další filtrování pouze přijímání požadavků z aplikace tyto určené IP adresy. Seznam těchto IP adres najdete v tématu [logic app omezení a konfigurace](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Místní připojení

Logic apps poskytují integraci s několika službami a zajistit bezpečné a spolehlivé místní komunikace.

#### <a name="on-premises-data-gateway"></a>Místní brána dat

Mnoho spravované konektory pro logic apps poskytují zabezpečené připojení do místních systémů, včetně systému souborů, SQL, SharePoint, DB2 a další. Brána přenášet data z místních zdrojů přes šifrované kanálů prostřednictvím služby Azure Service Bus. Veškerý provoz pochází jako zabezpečené odchozího provozu z agenta brány. Další informace o [jak brána dat funguje](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) má místní možnosti připojení, včetně integrace site-to-site VPN a ExpressRoute pro zabezpečené proxy a komunikace s místními systémy. V návrháři aplikace logiky můžete rychle vybrat rozhraní API zveřejní ze služby Azure API Management v pracovním postupu, poskytuje rychlý přístup k místním systémům.

## <a name="next-steps"></a>Další postup
[Vytvoření šablony nasazení](logic-apps-create-deploy-template.md)  
[Zpracování výjimek](logic-apps-exception-handling.md)  
[Monitorování aplikací logiky](logic-apps-monitor-your-logic-apps.md)  
[Diagnostikování selhání aplikací logiky a problémů](logic-apps-diagnosing-failures.md)  
