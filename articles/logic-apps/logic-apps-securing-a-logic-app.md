---
title: Zabezpečení přístupu a dat
description: Zabezpečený přístup ke vstupům, výstupům, aktivačním událostem založeným na požadavcích, historii spuštění, úlohám správy a přístupu k dalším prostředkům v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7979d1288cd99f8e28a421663383a930e0346357
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199606"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Zabezpečený přístup a data v Azure Logic Apps

Pro řízení přístupu a ochraně citlivých dat v Azure Logic Apps můžete nastavit zabezpečení pro tyto oblasti:

* [Přístup k aktivačním událostem založeným na žádostech](#secure-triggers)
* [Přístup k operacím aplikace logiky](#secure-operations)
* [Přístup ke vstupům a výstupům historie spouštění](#secure-run-history)
* [Přístup ke vstupům parametrů](#secure-action-parameters)
* [Přístup ke službám a systémům, které se nazývají aplikace logiky](#secure-outbound-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Přístup k aktivačním událostem založeným na žádostech

Pokud vaše aplikace logiky používá Trigger založený na požadavku, který přijímá příchozí volání nebo požadavky, jako je [požadavek](../connectors/connectors-native-reqres.md) nebo Trigger [Webhooku](../connectors/connectors-native-webhook.md) , můžete omezit přístup tak, aby vaše aplikace logiky mohli volat jenom autorizovaní klienti. Všechny požadavky přijaté aplikací logiky jsou šifrované a zabezpečené pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL), protokol.

Tady jsou možnosti, které vám pomůžou zabezpečit přístup k tomuto typu triggeru:

* [Generování sdílených přístupových podpisů](#sas)
* [Povolit Azure Active Directory Open Authentication (Azure AD OAuth)](#enable-oauth)
* [Omezit příchozí IP adresy](#restrict-inbound-ip-addresses)
* [Přidání Azure Active Directory Open Authentication (Azure AD OAuth) nebo jiného zabezpečení](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generování sdílených přístupových podpisů (SAS)

Každý koncový bod žádosti v aplikaci logiky má [sdílený přístupový podpis (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) v adrese URL koncového bodu, který se skládá z tohoto formátu:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Každá adresa URL obsahuje `sp` `sv` parametr dotazu, a, `sig` jak je popsáno v této tabulce:

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

Pokud chcete vygenerovat nový klíč pro přístup k zabezpečení, použijte Azure REST API nebo Azure Portal. Všechny dříve generované adresy URL, které používají starý klíč, jsou neověřené a už nebudou mít autorizaci k aktivaci aplikace logiky. Adresy URL, které načtete po obnovení, se podepisují pomocí nového přístupového klíče.

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky, která má klíč, který chcete znovu vygenerovat.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **přístupové klíče**.

1. Vyberte klíč, který chcete znovu vygenerovat a dokončit proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Vytvoření adres URL zpětného volání pro vypršení platnosti

Pokud sdílíte adresu URL koncového bodu pro aktivační událost na základě požadavku s ostatními stranami, můžete vygenerovat adresy URL zpětného volání, které používají konkrétní klíče a mají datum vypršení platnosti. Tímto způsobem můžete plynule vrátit klíče nebo omezit přístup k aktivaci aplikace logiky na základě konkrétního časového rozmezí. Chcete-li zadat datum vypršení platnosti adresy URL, použijte [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), například:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

V těle zahrňte `NotAfter` vlastnost pomocí řetězce data JSON. Tato vlastnost vrátí adresu URL zpětného volání, která je platná pouze do `NotAfter` data a času.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Vytváření adres URL s primárním nebo sekundárním tajným klíčem

Když vygenerujete nebo vypíšete adresy URL zpětného volání pro aktivační událost na základě požadavku, můžete zadat klíč, který se má použít k podepsání adresy URL. Chcete-li vygenerovat adresu URL, která je podepsána určitým klíčem, použijte [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), například:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

V těle zahrňte `KeyType` vlastnost buď `Primary` nebo `Secondary` . Tato vlastnost vrátí adresu URL, která je podepsána zadaným klíčem zabezpečení.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-oauth"></a>Povolit Azure Active Directory OAuth

Pokud vaše aplikace logiky začíná triggerem žádosti, můžete povolit [Azure Active Directory Open Authentication](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth) pro autorizaci příchozích volání triggeru žádosti. Než povolíte toto ověřování, přečtěte si tyto požadavky:

* Aplikace logiky je omezená na maximální počet zásad autorizace. Každá zásada autorizace má také maximální počet [deklarací identity](../active-directory/develop/developer-glossary.md#claim). Další informace najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Zásady autorizace musí zahrnovat aspoň deklaraci identity **vystavitele** , která má hodnotu začínající `https://sts.windows.net/` jako ID vystavitele Azure AD.

* Příchozí volání vaší aplikace logiky může používat pouze jedno schéma autorizace, buď Azure AD OAuth nebo [signatury sdíleného přístupu (SAS)](#sas).

* Tokeny OAuth jsou podporovány pouze pro aktivační událost žádosti.

* Pro tokeny OAuth se podporují jenom schémata autorizace [typu nosič](../active-directory/develop/active-directory-v2-protocols.md#tokens) .

Pokud chcete povolit službu Azure AD OAuth, postupujte podle těchto kroků a přidejte do své aplikace logiky jednu nebo více zásad autorizace.

1. V [Azure Portal](https://portal.microsoft.com)vyhledejte a otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **autorizace**. Po otevření podokna autorizace vyberte **Přidat zásadu**.

   ![Vyberte Authorization > přidat zásadu.](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Zadejte informace o zásadách autorizace zadáním typů a hodnot [deklarací identity](../active-directory/develop/developer-glossary.md#claim) , které vaše aplikace logiky očekává v ověřovacím tokenu, který prezentuje každé příchozí volání triggeru požadavku:

   ![Zadání informací pro zásady autorizace](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název zásady** | Ano | Název, který chcete použít pro zásady autorizace |
   | **Žádosti** | Ano | Typy a hodnoty deklarací, které vaše aplikace logiky přijímá při příchozích voláních. Tady jsou dostupné typy deklarací identity: <p><p>- **Stavil** <br>- **Osoby** <br>- **Závislosti** <br>- **ID JWT** (ID JSON web token) <p><p>Minimální seznam **deklarací identity** musí zahrnovat deklaraci identity **vystavitele** , která má hodnotu začínající `https://sts.windows.net/` ID vystavitele Azure AD. Další informace o těchto typech deklarací identity najdete [v tématu deklarace identity v tokenech zabezpečení Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Můžete také zadat vlastní typ a hodnotu deklarace identity. |
   |||

1. Pokud chcete přidat další deklaraci identity, vyberte si z těchto možností:

   * Pokud chcete přidat další typ deklarace identity, vyberte **Přidat standardní deklaraci identity**, vyberte typ deklarace a zadejte hodnotu deklarace identity.

   * Pokud chcete přidat vlastní deklaraci identity, vyberte **Přidat vlastní deklaraci identity**a zadejte hodnotu vlastní deklarace identity.

1. Pokud chcete přidat další zásady autorizace, vyberte **Přidat zásadu**. Zopakováním předchozích kroků zásadu nastavte.

1. Jakmile budete mít hotovo, vyberte **Uložit**.

Vaše aplikace logiky je teď nastavená tak, aby pro autorizaci příchozích požadavků používala Azure AD OAuth. Když aplikace logiky obdrží příchozí požadavek, který obsahuje ověřovací token, Azure Logic Apps porovnává deklarace identity tokenu vůči deklaracím v každé zásadě autorizace. Pokud existuje shoda mezi deklaracemi tokenu a všemi deklaracemi v alespoň jedné zásadě, autorizace pro příchozí požadavek bude úspěšná. Token může mít více deklarací identity než číslo zadané v zásadách autorizace.

Předpokládejme například, že vaše aplikace logiky má zásady autorizace, které vyžadují dva typy deklarací identity, Vystavitel a cílovou skupinu. Tento ukázkový dekódování [přístupového tokenu](../active-directory/develop/access-tokens.md) zahrnuje tyto typy deklarací identity:

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

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Omezit příchozí IP adresy

Spolu se sdíleným přístupovým podpisem (SAS) můžete chtít konkrétně omezit klienty, kteří můžou zavolat aplikaci logiky. Pokud například spravujete koncový bod žádosti pomocí Azure API Management, můžete aplikaci logiky omezit tak, aby přijímala požadavky pouze z IP adresy pro instanci API Management.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Omezit rozsahy příchozích IP adres v Azure Portal

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **Nastavení pracovního postupu**.

1. V části **Konfigurace řízení přístupu**  >  **povolená příchozí IP adresa**vyberte **konkrétní rozsahy IP**adres.

1. V části **rozsahy IP pro aktivační události**zadejte rozsahy IP adres, které aktivační událost akceptuje.

   Platný rozsah IP adres používá tyto formáty: *x. x. x. x/x* nebo *x. x. x. x-x. x.* x. x.

Pokud chcete, aby se aplikace logiky spouštěla jenom jako vnořená aplikace logiky, vyberte ze seznamu **povolené IP adresy** **jenom další Logic Apps**. Tato možnost zapíše prázdné pole do prostředku aplikace logiky. Tímto způsobem mohou spustit vnořenou aplikaci logiky pouze volání ze služby Logic Apps (nadřazené aplikace logiky).

> [!NOTE]
> Bez ohledu na IP adresu můžete přesto spustit aplikaci logiky, která má aktivační událost na základě požadavků, pomocí služby `/triggers/<trigger-name>/run` Azure REST API nebo prostřednictvím API Management. Tento scénář ale pořád vyžaduje [ověřování](../active-directory/develop/authentication-scenarios.md) proti REST API Azure. Všechny události se zobrazí v protokolu auditu Azure. Ujistěte se, že jste nastavili zásady řízení přístupu odpovídajícím způsobem.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Omezení rozsahů příchozích IP adres v šabloně Azure Resource Manager

Pokud [automatizujete nasazení Logic Apps pomocí Správce prostředků šablon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete určit rozsahy IP adres ve formátu *x. x. x. x/x* nebo *x. x. x. x-x* . x. x. x pomocí `accessControl` oddílu a zahrnutím `triggers` `actions` oddílů a do definice prostředků vaší aplikace logiky, například:

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
                  "allowedCallerIpAddresses:" : []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-open-authentication-or-other-security"></a>Přidat Azure Active Directory otevřené ověřování nebo jiné zabezpečení

Pokud chcete do aplikace logiky přidat další [ověřovací](../active-directory/develop/authentication-scenarios.md) protokoly, zvažte použití služby [Azure API Management](../api-management/api-management-key-concepts.md) . Tato služba vám pomůže vystavit aplikaci logiky jako rozhraní API a nabízí rozšířené monitorování, zabezpečení, zásady a dokumentaci pro libovolný koncový bod. API Management může vystavit veřejný nebo privátní koncový bod pro vaši aplikaci logiky. K autorizaci přístupu k tomuto koncovému bodu můžete použít [Azure Active Directory otevřít ověřování](#azure-active-directory-oauth-authentication) (Azure AD OAuth), [klientský certifikát](#client-certificate-authentication)nebo jiné standardy zabezpečení pro autorizaci přístupu k tomuto koncovému bodu. Když API Management obdrží požadavek, služba pošle požadavek do vaší aplikace logiky, což také vede k potřebným transformacím nebo omezením na cestě. Pokud chcete povolit jenom API Management aktivovat aplikaci logiky, můžete použít nastavení příchozího rozsahu IP adres vaší aplikace logiky.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Přístup k operacím aplikace logiky

Můžete povolit pouze konkrétní uživatele nebo skupiny, aby mohli spouštět konkrétní úkoly, jako je správa, úpravy a zobrazení Logic Apps. K řízení jejich oprávnění použijte [Access Control na základě rolí Azure (RBAC)](../role-based-access-control/role-assignments-portal.md) , abyste členům v předplatném Azure mohli přiřadit přizpůsobené nebo předdefinované role:

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

V historii spuštění aplikace logiky můžete omezit přístup k vstupům a výstupům, aby se tato data mohla zobrazit jenom z konkrétních rozsahů IP adres. Chcete-li například zablokovat komukoli přístup k vstupům a výstupům, zadejte rozsah IP adres, například `0.0.0.0-0.0.0.0` . Pouze osoba s oprávněními správce může toto omezení odebrat, což umožňuje "za běhu" přístup k datům aplikace logiky. Rozsahy IP adres, které se mají omezit, můžete zadat pomocí Azure Portal nebo v šabloně Azure Resource Manager, kterou používáte pro nasazení aplikace logiky.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Omezení rozsahů IP adres v Azure Portal

1. V Azure Portal otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **Nastavení pracovního postupu**.

1. V části **Konfigurace řízení přístupu**  >  **povolená příchozí IP adresa**vyberte **konkrétní rozsahy IP**adres.

1. V části **rozsahy IP adres pro obsah**zadejte rozsahy IP adres, které budou mít přístup k obsahu ze vstupů a výstupů. 

   Platný rozsah IP adres používá tyto formáty: *x. x. x. x/x* nebo *x. x. x. x-x. x.* x. x.

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Omezení rozsahů IP adres v šabloně Azure Resource Manager

Pokud [automatizujete nasazení Logic Apps pomocí Správce prostředků šablon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete rozsahy IP adres zadat pomocí `accessControl` oddílu s `contents` oddílem v definici prostředků vaší aplikace logiky, například:

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

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Zabezpečení dat v historii spouštění pomocí zmatení

Mnoho triggerů a akcí má nastavení pro zabezpečení vstupů, výstupů nebo obojího z historie spuštění aplikace logiky. Než použijete tato nastavení, která vám pomůžou zabezpečit tato data, [Přečtěte si tyto informace](#obfuscation-considerations).

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

Tady je několik [důležitých informací, které](#obfuscation-considerations) je potřeba zkontrolovat, když použijete tato nastavení, abyste mohli tato data zabezpečit.

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

#### <a name="considerations-when-securing-inputs-and-outputs"></a>Doporučení při zabezpečení vstupů a výstupů

* Když na Trigger nebo akci skryjete vstupy nebo výstupy, Logic Apps neodesílají zabezpečená data do Azure Log Analytics. Do této aktivační události nebo akce pro monitorování nemůžete také přidat [sledované vlastnosti](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) .

* [Rozhraní Logic Apps API pro zpracování historie pracovního postupu](https://docs.microsoft.com/rest/api/logic/) nevrací zabezpečené výstupy.

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

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Přístup ke vstupům parametrů

Pokud nasazujete v různých prostředích, zvažte Parametrizace hodnot v definici pracovního postupu, které se liší v závislosti na těchto prostředích. Tímto způsobem se můžete vyhnout pevně zakódovaným datům pomocí [šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) k nasazení aplikace logiky, ochraně citlivých dat definováním zabezpečených parametrů a předání těchto dat jako samostatných vstupů prostřednictvím [parametrů šablony](../azure-resource-manager/templates/template-parameters.md) pomocí [souboru parametrů](../azure-resource-manager/templates/parameter-files.md).

Pokud například ověříte akce HTTP pomocí [Azure Active Directory otevřete ověřování](#azure-active-directory-oauth-authentication) (Azure AD OAuth), můžete definovat a zakrýt parametry, které přijímají ID klienta a tajný klíč klienta, které se používají pro ověřování. Pro definování těchto parametrů ve vaší aplikaci logiky použijte `parameters` část v definici pracovního postupu vaší aplikace logiky a šablonu správce prostředků pro nasazení. Abychom vám pomohli zabezpečit hodnoty parametrů, které nechcete zobrazit při úpravách aplikace logiky nebo zobrazení historie spouštění, definujte parametry `securestring` pomocí `secureobject` typu nebo a podle potřeby použijte kódování. Parametry, které mají tento typ, nejsou vráceny s definicí prostředků a nejsou přístupné při zobrazení prostředku po nasazení. Pro přístup k těmto hodnotám parametrů za běhu použijte `@parameters('<parameter-name>')` výraz uvnitř definice pracovního postupu. Tento výraz je vyhodnocen pouze za běhu a je popsán [jazykem definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Pokud použijete parametr v hlavičce nebo těle žádosti, tento parametr může být viditelný při zobrazení historie spuštění vaší aplikace logiky a odchozího požadavku HTTP. Ujistěte se, že jste odpovídajícím způsobem také nastavili zásady přístupu k obsahu. K skrytí vstupů a výstupů v historii spuštění můžete také použít [zmatení](#obfuscate) . Autorizační hlavičky nejsou nikdy viditelné prostřednictvím vstupů nebo výstupů. Takže pokud se tam používá tajný klíč, nedá se tento tajný klíč získat.

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

| Název parametru | Popis |
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

<a name="secure-outbound-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Přístup ke službám a systémům, které se nazývají aplikace logiky

Tady je několik způsobů, jak můžete zvýšit zabezpečení koncových bodů, které přijímají volání nebo požadavky z vaší aplikace logiky:

* Přidání ověřování do odchozích požadavků.

  Když pracujete s triggerem založeným na protokolu HTTP nebo akcí, která umožňuje odchozí volání, jako je HTTP, HTTP + Swagger nebo Webhook, můžete do žádosti, kterou odesílá aplikace logiky, přidat ověřování. Můžete například vybrat tyto typy ověřování:

  * [Základní ověřování](#basic-authentication)

  * [Ověřování certifikátu klienta](#client-certificate-authentication)

  * [Ověřování služby Active Directory OAuth](#azure-active-directory-oauth-authentication)

  * [Ověřování spravovaných identit](#managed-identity-authentication)
  
  Další informace najdete v části [Přidání ověřování do odchozích volání](#add-authentication-outbound) dále v tomto tématu.

* Omezte přístup z IP adres aplikace logiky.

  Všechna volání koncových bodů z aplikací logiky pocházejí z konkrétních určených IP adres, které jsou založeny na oblastech vaší Logic Apps. Můžete přidat filtrování, které přijímá požadavky pouze z těchto IP adres. Pokud chcete získat tyto IP adresy, přečtěte si téma [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Zvyšte zabezpečení připojení k místním systémům.

  Azure Logic Apps poskytuje integraci s těmito službami, které vám pomůžou zajistit bezpečnější a spolehlivou místní komunikaci.

  * Místní brána dat

    Řada spravovaných konektorů v Azure Logic Apps usnadňuje zabezpečená připojení k místním systémům, jako je například systém souborů, SQL, SharePoint a DB2. Brána odesílá data z místních zdrojů do šifrovaných kanálů prostřednictvím Azure Service Bus. Veškerý provoz pochází z agenta brány jako zabezpečený odchozí provoz. Přečtěte si, [Jak funguje místní brána dat](logic-apps-gateway-install.md#gateway-cloud-service).

  * Připojení prostřednictvím Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) poskytuje možnosti místního připojení, jako je virtuální privátní síť Site-to-site a integrace ExpressRoute pro zabezpečený proxy a komunikaci s místními systémy. Z pracovního postupu aplikace logiky v návrháři aplikace logiky můžete vybrat rozhraní API, které je vystaveno API Management, které poskytuje rychlý přístup k místním systémům.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Přidání ověřování do odchozích volání

Koncové body HTTP a HTTPS podporují různé druhy ověřování. V závislosti na triggeru nebo akci, kterou použijete k provedení odchozích volání nebo požadavků, které přistupují k těmto koncovým bodům, můžete vybrat z různých rozsahů typů ověřování. Aby bylo zajištěno, že budete zabezpečit jakékoli citlivé informace, které vaše aplikace logiky zpracovává, používejte v případě potřeby zabezpečené parametry a zakódovat data. Další informace o použití a zabezpečení parametrů naleznete v tématu [přístup ke vstupům parametrů](#secure-action-parameters).

> [!NOTE]
> V návrháři aplikace logiky může být vlastnost **ověřování** skrytá na některých triggerech a akcích, kde můžete zadat typ ověřování. Chcete-li vlastnost v těchto případech zobrazit, otevřete u triggeru nebo akce seznam **Přidat nový parametr** a vyberte možnost **ověřování**. Další informace najdete v tématu [ověření přístupu ke spravované identitě](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Typ ověřování | Podporováno nástrojem |
|---------------------|--------------|
| [Základní](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, Webhook HTTP |
| [Certifikát klienta](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, Webhook HTTP |
| [Protokol OAuth pro Active Directory](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
| [Žádný](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
| [Spravovaná identita](#managed-identity-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Základní ověřování

Pokud je k dispozici možnost [základní](../active-directory-b2c/secure-rest-api.md) , zadejte tyto hodnoty vlastností:

| Property – vlastnost (Designer) | Property (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | Základní | Typ ověřování, který se má použít |
| **Jmen** | `username` | Ano | <*uživatelské jméno*>| Uživatelské jméno pro ověřování přístupu k cílovému koncovému bodu služby |
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

### <a name="client-certificate-authentication"></a>Ověřování certifikátu klienta

Pokud je k dispozici možnost [certifikát klienta](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) , zadejte tyto hodnoty vlastností:

| Property – vlastnost (Designer) | Property (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | **Certifikát klienta** <br>or <br>`ClientCertificate` | Typ ověřování, který se má použít pro certifikáty klientů TLS/SSL <p><p>**Poznámka**: Přestože jsou certifikáty podepsané svým držitelem podporovány, certifikáty podepsané svým držitelem pro TLS/SSL nejsou podporovány. Konektor HTTP nepodporuje zprostředkující certifikáty TLS/SSL. |
| **PFX** | `pfx` | Ano | <*Encoded – obsah-souboru PFX*> | Obsah kódovaný v kódování Base64 ze souboru PFX (Personal Information Exchange) <p><p>Chcete-li převést soubor PFX na formát s kódováním base64, můžete použít PowerShell pomocí následujících kroků: <p>1. Uložte obsah certifikátu do proměnné: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. převeďte obsah certifikátu pomocí `ToBase64String()` funkce a uložte tento obsah do textového souboru: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
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
* [Použijte certifikát TLS/SSL v kódu v Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory otevření ověřování

Na triggerech žádosti můžete pomocí [Azure Active Directory otevřít ověřování](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth) ověřit příchozí volání po [nastavení zásad autorizace Azure AD](#enable-oauth) pro vaši aplikaci logiky. Pro všechny ostatní triggery a akce, které poskytují typ ověřování **služby Active Directory OAuth** pro výběr, zadejte tyto hodnoty vlastností:

| Property – vlastnost (Designer) | Property (JSON) | Požaduje se | Hodnota | Popis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Ano | **Protokol OAuth pro Active Directory** <br>or <br>`ActiveDirectoryOAuth` | Typ ověřování, který se má použít. Logic Apps v současnosti následuje [protokol OAuth 2,0](../active-directory/develop/v2-overview.md). |
| **Autorita** | `authority` | Ne | <*Adresa URL pro vystavitele tokenu pro-Authority*> | Adresa URL pro autoritu, která poskytuje ověřovací token. Ve výchozím nastavení je tato hodnota `https://login.windows.net` . |
| **Tenant** | `tenant` | Ano | <*ID tenanta*> | ID tenanta pro tenanta Azure AD |
| **Cílová skupina** | `audience` | Ano | <*prostředek k autorizaci*> | Prostředek, který chcete použít pro autorizaci, například`https://management.core.windows.net/` |
| **ID klienta** | `clientId` | Ano | <*ID klienta*> | ID klienta pro aplikaci požadující autorizaci |
| **Typ přihlašovacích údajů** | `credentialType` | Ano | Certifikát <br>or <br>Tajný kód | Typ přihlašovacích údajů, který klient používá k vyžádání autorizace. Tato vlastnost a hodnota se nezobrazí v základní definici vaší aplikace logiky, ale určuje vlastnosti, které se zobrazí pro vybraný typ přihlašovacích údajů. |
| **Tajný kód** | `secret` | Ano, ale jenom pro typ přihlašovacích údajů tajného klíče | <*tajný kód klienta*> | Tajný klíč klienta pro vyžádání autorizace |
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

### <a name="managed-identity-authentication"></a>Ověřování spravovaných identit

Pokud je k dispozici možnost [spravovaná identita](../active-directory/managed-identities-azure-resources/overview.md) , může vaše aplikace logiky používat identitu přiřazenou systémem nebo *jedinou* ručně vytvořenou identitu přiřazenou uživateli pro ověřování přístupu k prostředkům v jiných klientech Azure Active Directory (Azure AD), aniž by se museli přihlašovat. Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte zadávat ani otáčet tajné kódy. Přečtěte si další informace o [službách Azure, které podporují spravované identity pro ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Aby mohla aplikace logiky používat spravovanou identitu, postupujte podle kroků v části [ověření přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Tyto kroky povolí spravovanou identitu ve vaší aplikaci logiky a nastaví přístup této identity k cílovému prostředku Azure.

1. Než může Azure Function používat spravovanou identitu, nejdřív [Povolte ověřování pro službu Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. V aktivační události nebo akci, kde chcete použít spravovanou identitu, zadejte tyto hodnoty vlastností:

   | Property – vlastnost (Designer) | Property (JSON) | Požaduje se | Hodnota | Popis |
   |---------------------|-----------------|----------|-------|-------------|
   | **Authentication** | `type` | Ano | **Spravovaná identita** <br>or <br>`ManagedServiceIdentity` | Typ ověřování, který se má použít |
   | **Spravovaná identita** | `identity` | Ano | * **Spravovaná identita přiřazená systémem** <br>or <br>`SystemAssigned` <p><p>* <*uživatelsky přiřazené-identity-Name*> | Spravovaná identita, která se má použít |
   | **Cílová skupina** | `audience` | Ano | <*cíl-Resource-ID*> | ID prostředku pro cílový prostředek, ke kterému chcete získat přístup. <p>Například `https://storage.azure.com/` zpřístupní [přístupové tokeny](../active-directory/develop/access-tokens.md) pro ověřování platné pro všechny účty úložiště. Můžete ale taky zadat adresu URL kořenové služby, například `https://fabrikamstorageaccount.blob.core.windows.net` pro konkrétní účet úložiště. <p>**Poznámka**: vlastnost **cílové skupiny** může být v některých triggerech nebo akcích skrytá. Chcete-li tuto vlastnost zviditelnit, otevřete v aktivační události nebo akci seznam **Přidat nový parametr** a vyberte možnost **cílová skupina**. <p><p>**Důležité**: Ujistěte se, že toto ID cílového prostředku *přesně odpovídá* hodnotě, kterou očekává služba Azure AD, včetně všech požadovaných koncových lomítek. `https://storage.azure.com/`ID prostředku pro všechny účty Azure Blob Storage vyžaduje koncové lomítko. ID prostředku pro konkrétní účet úložiště ale nevyžaduje koncové lomítko. Tato ID prostředků najdete v tématu [služby Azure, které podporují Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Když použijete [zabezpečené parametry](#secure-action-parameters) pro zpracování a zabezpečení citlivých informací, například v [šabloně Azure Resource Manager pro automatizaci nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), můžete použít výrazy pro přístup k těmto hodnotám parametrů za běhu. Tato ukázka definice akce HTTP Určuje ověřování `type` jako `ManagedServiceIdentity` a používá [funkci Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) k získání hodnot parametrů:

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

* [Automatizace nasazení pro Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorování aplikací logiky](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnostika chyb a problémů aplikace logiky](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
