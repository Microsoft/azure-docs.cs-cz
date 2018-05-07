---
title: Zabezpečený přístup k Azure Logic Apps | Microsoft Docs
description: Přidejte zabezpečení pro ochranu přístupu k aktivační události, vstupy a výstupy, parametrů akcí a služeb používaných u pracovních postupů v Azure Logic Apps.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: ''
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 3831c44a52fe4bb428021acac46188966087fdfe
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="secure-access-to-your-logic-apps"></a>Zabezpečený přístup k aplikacím logiky

Existují celou řadu nástrojů, které jsou k dispozici a pomáhá vám zabezpečit svou aplikaci logiky.

* Zabezpečení přístupu k aktivaci aplikace logiky (HTTP žádosti o aktivační události)
* Zabezpečení přístupu ke správě, upravit nebo číst aplikace logiky
* Zabezpečení přístupu k obsahu vstupy a výstupy pro spuštění
* Zabezpečení parametry nebo vstupy v rámci akce v pracovním postupu
* Zabezpečení přístupu ke službám, které přijímají požadavky z pracovního postupu

## <a name="secure-access-to-trigger"></a>Zabezpečený přístup k aktivaci

Pokud pracujete s aplikace logiky, která se spustí na požadavek HTTP ([požadavku](../connectors/connectors-native-reqres.md) nebo [Webhooku](../connectors/connectors-native-webhook.md)), můžete omezit přístup tak, aby pouze autorizovaní klienti můžete aktivovat aplikaci logiky. Všechny požadavky do aplikace logiky jsou zašifrovány a zabezpečené pomocí protokolu SSL.

### <a name="shared-access-signature"></a>Sdílený přístupový podpis

Obsahuje všechny koncové body požadavek pro aplikaci logiky [sdíleného přístupového podpisu (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) jako část adresy URL. Obsahuje každou adresu URL `sp`, `sv`, a `sig` parametr dotazu. Oprávnění jsou určené `sp`a odpovídat metody HTTP povolena, `sv` je verze použitá ke generování, a `sig` se používá k ověření přístupu k aktivaci. Podpis je generována pomocí algoritmus SHA256 s tajným klíčem na všechny cesty adresy URL a vlastnosti. Tajný klíč se nikdy zveřejněné a publikovali kde je udržována šifrovaná a uložené jako součást logiky aplikace. Aplikace logiky pouze autorizuje aktivační události, které obsahují platný podpis vytvořen s tajným klíčem.

#### <a name="regenerate-access-keys"></a>Opětovné vygenerování přístupových klíčů

Můžete vygenerovat nový zabezpečený klíč v kdykoli prostřednictvím portálu REST API nebo Azure. Všechny aktuální adresy URL, které byly vygenerovány dříve pomocí starého klíče jsou zrušena a už nemáte oprávnění k aktivovat aplikaci logiky.

1. Na portálu Azure otevřete aplikaci logiky, kterou chcete znovu vygenerovat klíč
1. Klikněte **přístupové klíče** položky nabídky v části **nastavení**
1. Vyberte klíč znovu vygenerovat a dokončete proces

Adresy URL, jež načíst po opětovné generování jsou podepsány pomocí nového přístupového klíče.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Vytvoření adresy URL zpětné volání s datum vypršení platnosti

Pokud sdílíte s ostatními stranami adresu URL, konkrétní klíče a data vypršení platnosti podle potřeby můžete vygenerovat adresy URL. Potom můžete bezproblémově vrátit klíče, nebo zajistěte, aby byl přístup k fire aplikace je omezený na určité časové rozpětí. Můžete zadat datum vypršení platnosti pro adresu URL prostřednictvím [aplikace logiky REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

V textu, obsahovat vlastnost `NotAfter` jako řetězec data JSON, který vrátí adresu URL zpětné volání, která je platná, dokud pouze `NotAfter` datum a čas.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Vytvoření adresy URL pomocí primární nebo sekundární tajný klíč

Při generování nebo seznam adres URL zpětného volání pro aktivační procedury založené na požadavku, můžete určete, který klíč sloužící k podepisování adresu URL.  Můžete generovat adresu URL podepsány pomocí konkrétního klíče [aplikace logiky REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) následujícím způsobem:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

V textu, obsahovat vlastnost `KeyType` jako `Primary` nebo `Secondary`.  Vrátí adresu URL podepsaný zabezpečeného zadaný klíč.

### <a name="restrict-incoming-ip-addresses"></a>Omezit příchozí IP adresy

Kromě sdíleného přístupového podpisu můžete chtít omezit volání aplikace logiky pouze z konkrétní klientů.  Například pokud budete spravovat váš koncový bod pomocí Azure API Management, můžete omezit aplikace logiky pouze žádost přijmout, když požadavek pochází z IP adresy instance API Management.

Toto nastavení lze konfigurovat v nastavení aplikace logiky:

1. Na portálu Azure otevřete aplikaci logiky, kterou chcete přidat omezení podle IP adresy
1. Klikněte **nastavení pracovních postupů** položky nabídky v části **nastavení**
1. Zadejte seznam rozsahů IP adres třeba přijmout, který má aktivační procedura

Platný rozsah IP má formát `192.168.1.1/255`. Pokud chcete aplikaci logiky má pouze provést jako aplikace vnořené logiky, vyberte **jenom jiné aplikace logiky** možnost. Tato možnost zapíše prázdné pole k prostředku význam jenom volání ze samotné (nadřazené logiku aplikace) služby úspěšně provést.

> [!NOTE]
> Pořád se dá spustit aplikaci logiky s aktivační událost žádosti přes rozhraní REST API nebo správu `/triggers/{triggerName}/run` bez ohledu na IP. Tento scénář vyžaduje ověřování proti REST API služby Azure a zobrazí všechny události v protokolu auditování Azure. Zásady řízení přístupu sadu odpovídajícím způsobem.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Nastavení rozsahy IP adres v definici prostředků

Pokud používáte [šablonu nasazení](logic-apps-create-deploy-template.md) k automatizaci nasazení, může být rozsah nastavení IP adresy nakonfigurované v šabloně prostředků.  

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

Chcete-li přidat další protokoly autorizace nad aplikace logiky, [Azure API Management](https://azure.microsoft.com/services/api-management/) nabízí rozšířené možnosti monitorování, zabezpečení, zásady a dokumentaci pro libovolný koncový bod se schopností vystavit aplikace logiky jako rozhraní API. Azure API Management můžou zpřístupnit veřejných nebo privátních koncový bod pro aplikaci logiky, která může použít Azure Active Directory, certifikát, OAuth nebo jiné standardy zabezpečení. Po přijetí žádost o službě Azure API Management předává požadavek do aplikace logiky (provádění všechny potřebné transformace ani neukládají omezení). Příchozí nastavení rozsahu IP na aplikaci logiky můžete použít možnost Povolit jenom aplikace logiky, aby se spouštěly ze správy rozhraní API.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Zabezpečený přístup ke správě nebo upravovat aplikace logiky

Pro operace správy v aplikaci logiky můžete omezit přístup tak, aby pouze konkrétní uživatele nebo skupiny se můžou k provádění operací na prostředek. Použití aplikace logiky Azure [řízení přístupu na základě Role (RBAC)](../role-based-access-control/role-assignments-portal.md) funkci a můžete přizpůsobit pomocí stejných nástrojů.  Existuje několik integrovaných rolí, které můžete přiřadit také členy předplatného:

* **Přispěvatel aplikace logiky** – poskytuje přístup k zobrazení, upravte a aktualizujte aplikaci logiky.  Nelze odebrat prostředek nebo provádět operace správy.
* **Operátor aplikace logiky** – můžete zobrazit aplikaci logiky a historie spouštění a povolit nebo zakázat.  Nelze upravit nebo aktualizovat definice.

Můžete také použít [Zámek prostředků Azure](../azure-resource-manager/resource-group-lock-resources.md) aby se zabránilo změně nebo odstranění aplikace logiky. Tato funkce se hodí v situaci zabránit produkční prostředky z změn nebo odstranění.

## <a name="secure-access-to-contents-of-the-run-history"></a>Zabezpečený přístup k obsahu historie spouštění

Můžete omezit přístup k obsahu vstupy nebo výstupy z předchozích spuštění na konkrétní rozsahy IP adres.  

Všechna data v rámci spuštění pracovního postupu se šifrují během přenosu i v klidu. Při volání do historie spouštění služby ověří žádost a poskytuje odkazy na žádosti a odpovědi vstupy a výstupy. Tento odkaz se dají chránit tak obsah vrátí pouze požadavky na Zobrazit obsah z určené rozsahu IP adres. Této funkci můžete použít pro ovládací prvek další přístup. Můžete zadat IP adresu jako i `0.0.0.0` , nemá nikdo přístup vstupy/výstupy. Pouze uživatel s oprávněními správce může odebrat toto omezení poskytuje možnost pro 'v běhu' přístup k obsahu pracovního postupu.

Toto nastavení lze nakonfigurovat v rámci nastavení prostředků na portálu Azure:

1. Na portálu Azure otevřete aplikaci logiky, kterou chcete přidat omezení podle IP adresy
2. Klikněte **konfigurace řízení přístupu** položky nabídky v části **nastavení**
3. Zadejte seznam rozsahů IP adres pro přístup k obsahu

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Nastavení rozsahy IP adres v definici prostředků

Pokud používáte [šablonu nasazení](logic-apps-create-deploy-template.md) k automatizaci nasazení, může být rozsah nastavení IP adresy nakonfigurované v šabloně prostředků.  

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

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Zabezpečení parametry a vstupy v pracovním postupu

Můžete chtít Parametrizace některých aspektů definice pracovního postupu pro nasazení v prostředí. Některé parametry mohou být také zabezpečené parametry, které nechcete, aby se objevily při úpravě pracovního postupu, jako je například ID klienta a tajný klíč klienta pro [ověřování Azure Active Directory](../connectors/connectors-native-http.md#authentication) akce HTTP.

### <a name="using-parameters-and-secure-parameters"></a>Použití parametrů a parametrů zabezpečení

Pro přístup k hodnotě parametru prostředků v době běhu [jazyk definic workflowů](http://aka.ms/logicappsdocs) poskytuje `@parameters()` operaci. Můžete také [zadejte parametry v šabloně nasazení prostředků](../azure-resource-manager/resource-group-authoring-templates.md#parameters). Ale pokud zadáte typem parametru jako `securestring`, parametr se zbytkem definice prostředku nejsou k dispozici a nebudou přístupné, a to pomocí zobrazení po nasazení.

> [!NOTE]
> Pokud vaše parametr se používá v záhlaví nebo obsahu požadavku, může být parametr viditelné historie spouštění a odchozí požadavek HTTP. Ujistěte se, že odpovídajícím způsobem nastavit zásady váš přístup k obsahu.
> Nikdy jsou viditelné prostřednictvím vstupů nebo výstupů autorizační hlavičky. Proto pokud tajný klíč se používá existuje, tajný klíč se dá načíst.

#### <a name="resource-deployment-template-with-secrets"></a>Šablonu nasazení prostředků s tajné klíče

Následující příklad ukazuje, nasazení, který odkazuje na zabezpečené parametr `secret` za běhu. V souboru samostatné parametry, můžete zadat hodnotu pro prostředí `secret`, nebo použijte [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) načíst tajné klíče v nasazení čas.

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

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Zabezpečený přístup k přijímání požadavků z pracovního postupu služby

Existuje mnoho způsobů pomáhají zabezpečit žádný koncový bod, který potřebuje přístup k aplikaci logiky.

### <a name="using-authentication-on-outbound-requests"></a>Pomocí ověřování pro odchozí požadavky

Při práci s HTTP, HTTP + Swagger (otevřené rozhraní API) nebo akce Webhooku, můžete přidat ověřování na žádost o odeslání. Můžete uvést základní ověřování, ověřování pomocí certifikátu nebo ověřování Azure Active Directory. Podrobnosti o tom, jak nakonfigurovat toto ověřování najdete [v tomto článku](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Omezení přístupu k logiku aplikace IP adresy

Všechna volání z aplikace logiky pocházet z konkrétní sadu IP adres podle oblastí. Můžete přidat další filtrování pouze přijímání požadavků z těchto určené IP adresy. Seznam těchto IP adres najdete v tématu [logiku aplikace omezení a konfigurace](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Místní připojení

Aplikace logiky poskytují integraci se službou několik zajistit zabezpečený a spolehlivý místní komunikace.

#### <a name="on-premises-data-gateway"></a>Místní brána dat

Mnoho spravovaných konektorů pro logic apps poskytuje zabezpečené připojení k místním systémům, včetně systému souborů, SQL, SharePoint, DB2 a další. Brána předává data z místního zdroje na šifrované kanály přes Azure Service Bus. Veškerý provoz pochází jako zabezpečené odchozí provoz z agenta brány. Další informace o [fungování bránu dat](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) možnosti místní připojení, včetně site-to-site VPN a ExpressRoute integrace pro zabezpečené proxy a komunikaci místních systémů. V návrháři aplikace logiky můžete rychle vybrat rozhraní API zveřejněné z Azure API Management v rámci pracovního postupu, že poskytuje rychlý přístup k místním systémům.

## <a name="next-steps"></a>Další postup
[Vytvoření šablony nasazení](logic-apps-create-deploy-template.md)  
[Zpracování výjimek](logic-apps-exception-handling.md)  
[Monitorování aplikací logiky](logic-apps-monitor-your-logic-apps.md)  
[Diagnostikování selhání aplikace logiky a problémy](logic-apps-diagnosing-failures.md)  
