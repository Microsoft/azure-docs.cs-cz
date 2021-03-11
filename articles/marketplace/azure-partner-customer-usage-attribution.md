---
title: Přidělení zákaznického využití Azure
description: Získejte přehled o sledování využití zákazníků pro aplikace Azure na komerčním webu Marketplace a dalších nasazených IP adres vyvinutých partnery.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 03/09/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 8f84d77b5a424d5f7273c7e748c35c52882819c6
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608389"
---
# <a name="azure-customer-usage-attribution"></a>Přidělení zákaznického využití Azure

Využití zákazníka připadá na používání prostředků Azure v zákaznických předplatných vytvořených během nasazování vaší IP adresy jako partner. Vytváření těchto přidružení v interních systémech Microsoftu přináší větší přehled o službě Azure s nároky na provoz vašeho softwaru. [V případě nabídek aplikací Azure na komerčním tržišti](#commercial-marketplace-azure-apps)vám tato funkce sledování pomůže zarovnat se prodejními týmy Microsoftu a získat kredit pro Microsoft partnerická programu.

Označení zákaznického využití podporuje tři možnosti nasazení:

1. Šablony Azure Resource Manager (společné možnosti využívání aplikací Azure, na které se také říká šablona řešení nebo spravované aplikace): partneři vytvářejí Správce prostředků šablony pro definování infrastruktury a konfigurace jejich řešení Azure. Šablona Správce prostředků umožňuje zákazníkům nasadit prostředky vašeho řešení do konzistentního a možného stavu, který lze opakovat.
1. Rozhraní Azure Resource Manager API: partneři můžou volat rozhraní API pro Správce prostředků, aby mohli nasadit šablonu Správce prostředků nebo přímo zřídit služby Azure.
1. Terraformu: partneři můžou pomocí Terraformu nasadit šablonu Správce prostředků nebo přímo nasadit služby Azure.

Existují sekundární případy použití pro označení zákaznického využití mimo komerční web Marketplace popsané [dále v tomto článku](#other-use-cases).

>[!IMPORTANT]
>- Označení zákaznického využití není určené ke sledování práce integrátorů systémů, poskytovatelů spravovaných služeb nebo nástrojů určených primárně pro nasazení a správu prostředků Azure.
>- Přidělení zákaznického využití je pro nová nasazení a nepodporuje sledování již nasazených prostředků.
>- Ne všechny služby Azure jsou kompatibilní se zákaznickým využitím. Služba Azure Kubernetes Services (AKS) a VM Scale Sets známé problémy, které způsobují nahlášení využití.

## <a name="commercial-marketplace-azure-apps"></a>Aplikace Azure pro komerční tržiště

Sledování využití Azure z aplikací Azure publikovaných na komerčním webu je převážně automatické. Když nahrajete šablonu Správce prostředků jako součást [technické konfigurace plánu vaší aplikace Azure Marketplace](https://docs.microsoft.com/azure/marketplace/create-new-azure-apps-offer-solution#define-the-technical-configuration), partnerským centrem se přidá ID sledování čitelné pomocí Azure Resource Manager.

Pokud používáte rozhraní Azure Resource Manager API, budete muset přidat ID sledování podle [pokynů níže](#use-resource-manager-apis) , abyste ho předávali Azure Resource Manager, jak kód nasazuje prostředky. Toto ID se zobrazuje v partnerském centru na stránce technické konfigurace vašeho plánu. 

> [!NOTE]
> U stávajících aplikací Azure se v březnu 2021 provedla jednorázová migrace, která aktualizuje ID sledování v technické konfiguraci jednotlivých plánů. Využití z minulých nasazení těchto nabídek zůstane sledováno v systémech Microsoftu.

## <a name="other-use-cases"></a>Jiné případy použití 

Ke sledování využití Azure, která nejsou k dispozici na komerčním tržišti, můžete použít označení zákaznického využití. Tato řešení se obvykle nacházejí v úložišti pro rychlé zprovoznění, v soukromých úložištích GitHubu nebo pocházejí z 1:1 zákaznických zapojení, která vytvářejí trvalá IP adresa (například nasaditelné a škálovatelné aplikace).

Vyžaduje se několik ručních kroků:

1. Vytvořte jeden nebo více identifikátorů GUID, které chcete použít jako ID sledování.
1. Zaregistrujte tyto identifikátory GUID v partnerském centru.
1. Do řetězců aplikace Azure a/nebo uživatelského agenta přidejte své registrované identifikátory GUID.

### <a name="create-guids"></a>Vytvořit GUID

Na rozdíl od ID sledování, které partnerské Centrum vytvoří vaším jménem pro aplikace Azure na komerčním webu Marketplace, jiné použití CUA vyžadují, abyste vytvořili identifikátor GUID, který se použije jako ID sledování. Identifikátor GUID je jedinečný referenční identifikátor, který obsahuje 32 hexadecimálních číslic. K vytváření identifikátorů GUID pro sledování byste měli použít generátor GUID, například přes PowerShell:

```powershell
[guid]::NewGuid()
```

Pro každý produkt a distribuční kanál byste měli vytvořit jedinečný identifikátor GUID. Pokud nechcete, aby vytváření sestav bylo rozděleno, můžete použít jeden identifikátor GUID pro více distribučních kanálů produktu. Generování sestav probíhá podle Microsoft Partner Network ID a identifikátor GUID.

### <a name="register-guids"></a>Registrace identifikátorů GUID

Identifikátory GUID musí být v partnerském centru zaregistrované, aby se vám mohly přidružit jako partneři:

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard).

1. Zaregistrujte se jako [komerční Vydavatel na webu Marketplace](https://aka.ms/JoinMarketplace).

1. V pravém horním rohu vyberte **Nastavení** (ikona ozubeného kolečka) a pak **Nastavení účtu**.

1. Vyberte **identifikátory profilů organizace**  >    >  **Přidat sledovací identifikátor GUID**.

1. Do pole **identifikátor GUID** zadejte identifikátor GUID sledování. Zadejte pouze identifikátor GUID bez `pid-` předpony. Do pole **Popis** zadejte název nebo popis řešení.

1. Pokud chcete zaregistrovat více než jeden identifikátor GUID, vyberte znovu **Přidat identifikátor GUID sledování** . Na stránce se zobrazí další pole.

1. Vyberte **Uložit**.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Přidání identifikátoru GUID do šablony Správce prostředků

Chcete-li přidat registrovaný identifikátor GUID do šablony Správce prostředků, proveďte jednu úpravu do souboru hlavní šablony:

1. Otevřete šablonu Správce prostředků.

1. Přidejte do hlavního souboru šablony nový prostředek typu [Microsoft. Resources/Deployments](/azure/templates/microsoft.resources/deployments) . Prostředek musí být v **mainTemplate.js** nebo **azuredeploy.jspouze v** souboru, nikoli v žádné vnořené nebo propojené šabloně.

1. Jako název prostředku zadejte hodnotu GUID za `pid-` předponou. Pokud je například identifikátor GUID eb7927c8-dd66-43e1-b0cf-c346a422063, název prostředku bude **PID-eb7927c8-dd66-43e1-b0cf-c346a422063**. Příklad:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. V šabloně vyhledejte chyby.

1. Znovu publikujte šablonu v příslušných úložištích.

1. [Ověřte úspěch identifikátoru GUID v nasazení šablony](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> Další informace o vytváření a publikování šablon Správce prostředků naleznete v tématu: [Vytvoření a nasazení první šablony Správce prostředků](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>Ověření nasazení sledovaných s identifikátorem GUID

Po úpravě šablony a spuštění testovacího nasazení použijte následující skript prostředí PowerShell k načtení prostředků, které jste nasadili a označili.

Pomocí skriptu můžete ověřit, že identifikátor GUID je úspěšně přidaný do šablony Správce prostředků. Tento skript se nevztahuje na Správce prostředků nasazení API nebo Terraformu.

Přihlaste se k Azure. Před spuštěním skriptu vyberte předplatné s nasazením, které chcete ověřit. Spusťte skript v rámci předplatného nasazení.

Parametry **GUID** (níže označované jako "Deployment") a **resourceGroupName** název nasazení jsou povinné.

[Původní skript](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) můžete získat na GitHubu.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Upozorněte vaše zákazníky

Partneři by měli informovat své zákazníky o nasazeních, která používají označení zákaznického využití. Microsoft oznamuje partnerovi využití Azure spojené s těmito nasazeními. Následující příklady zahrnují obsah, který můžete použít k informování zákazníků o těchto nasazeních. V příkladech nahraďte \<PARTNER> názvem vaší společnosti. Partneři by měli mít jistotu, že se oznámení zarovnají se zásadami ochrany osobních údajů a kolekcí dat, včetně možností pro vyloučení zákazníků ze sledování.

#### <a name="notification-for-resource-manager-template-deployments"></a>Oznámení pro nasazení šablon Správce prostředků

Po nasazení této šablony může společnost Microsoft identifikovat instalaci \<PARTNER> softwaru s nasazenými prostředky Azure. Společnost Microsoft může korelovat tyto prostředky, které se používají k podpoře softwaru. Společnost Microsoft tyto informace shromažďuje, aby poskytovala co nejvíc zkušeností s produkty a pracovala s jejich podnikáním. Data se shromažďují a řídí zásadami ochrany osobních údajů od Microsoftu, které najdete na adrese [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

#### <a name="notification-for-sdk-or-api-deployments"></a>Oznámení pro nasazení SDK nebo rozhraní API

Po nasazení \<PARTNER> softwaru může společnost Microsoft identifikovat instalaci \<PARTNER> softwaru s nasazenými prostředky Azure. Společnost Microsoft může korelovat tyto prostředky, které se používají k podpoře softwaru. Společnost Microsoft tyto informace shromažďuje, aby poskytovala co nejvíc zkušeností s produkty a pracovala s jejich podnikáním. Data se shromažďují a řídí zásadami ochrany osobních údajů od Microsoftu, které najdete na adrese [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="use-resource-manager-apis"></a>Použití rozhraní API pro Správce prostředků

V některých případech můžete provést volání přímo proti Správce prostředků rozhraní REST API pro nasazení služeb Azure. [Azure podporuje několik sad SDK](../index.yml?pivot=sdkstools) , aby tato volání mohla povolit. Můžete použít jednu ze sad SDK nebo volat rozhraní REST API přímo k nasazení prostředků.

Pokud chcete povolit přihlašování od zákazníka, při návrhu volání rozhraní API uveďte ID sledování v hlavičce uživatelského agenta v žádosti. Naformátujte řetězec `pid-` předponou. Příklady:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Pokud používáte rozhraní API Správce prostředků s aplikací Azure na komerčním webu Marketplace, použijte ID sledování poskytované v partnerském centru. Nepoužívejte identifikátor GUID.

Různé sady SDK komunikují s rozhraními API Správce prostředků odlišně a budou vyžadovat několik rozdílů ve vašem kódu. Níže uvedené příklady obsahují přístup k nekomerčnímu tržišti pomocí identifikátoru GUID a pokrývají celou řadu nejoblíbenějších sad Azure SDK.

#### <a name="example-python-sdk"></a>Příklad: Python SDK

Pro Python použijte atribut **config** . Atribut lze přidat pouze k vlastnosti UserAgent. Příklad:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Přidejte atribut pro každého klienta. Neexistuje žádná globální statická konfigurace. Můžete označit objekt pro vytváření klienta, aby se zajistilo sledování každého klienta. Další informace najdete v tématu tato [Ukázka klienta v GitHubu](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-net-sdk"></a>Příklad: .NET SDK

V případě .NET nezapomeňte nastavit agenta pro uživatele. Pomocí knihovny [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) nastavte uživatelského agenta s následujícím kódem (příklad v jazyce C#):

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Příklad: Azure PowerShell

Pokud prostředky nasazujete prostřednictvím Azure PowerShell, přidejte svůj identifikátor GUID pomocí této metody:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Příklad: Azure CLI

Když k připojení svého GUID použijete rozhraní příkazového řádku Azure, nastavte proměnnou prostředí **AZURE_HTTP_USER_AGENT** v oboru skriptu. Pro rozsah prostředí můžete také nastavit proměnnou globálně:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Další informace najdete v tématu [Azure SDK pro go](/azure/developer/go/).

## <a name="use-terraform"></a>Použití Terraformu

Podpora Terraformu je dostupná prostřednictvím 1.21.0 verze poskytovatele Azure: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) . To platí pro všechny partnery, kteří nasazují své řešení přes Terraformu, a všechny nasazené prostředky a měřené poskytovatelem Azure (verze 1.21.0 nebo novější).

Poskytovatel Azure pro Terraformu přidal nové volitelné pole s názvem [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) pro zadání identifikátoru GUID sledování používaného pro vaše řešení. Hodnota tohoto pole může být také zdrojová z proměnné prostředí *ARM_PARTNER_ID* .

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Nastavte hodnotu *partner_id* na registrovaný identifikátor GUID. Nepoužívejte předponu identifikátoru GUID pomocí "PID-", stačí ji nastavit na skutečný identifikátor GUID.

> [!IMPORTANT]
> Pokud používáte Terraformu s aplikací Azure na komerčním webu Marketplace, použijte celé ID sledování, které je k dispozici v partnerském centru. Nepoužívejte identifikátor GUID.

## <a name="get-support"></a>Získání podpory

Seznamte se s možnostmi podpory na komerčním tržišti v části [Podpora programu komerčního tržiště v partnerském centru](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Odeslání žádosti o technickou spolupráci

1. Navštivte [partnerské technické služby](https://aka.ms/TechnicalJourney).
1. Pokud chcete zobrazit technickou cestu, vyberte **cloudová infrastruktura a Správa** .
1. Vyberte možnost **služby pro nasazení**  >  **Odeslat žádost**.
1. Přihlaste se pomocí účtu MSA (MPN) nebo svého AAD (účet partnerského řídicího panelu).
1. Dokončete/zkontrolujte kontaktní informace ve formuláři, který se otevře. Informace o konzultacích můžou být předem vyplněné nebo můžou mít možnosti rozevíracího seznamu.
1. Zadejte název a podrobný popis problému.
1. Vyberte **Odeslat**.

Prohlédněte si podrobné pokyny k snímkům obrazovky s [použitím služeb technické služby předprodejní a nasazení](https://aka.ms/TechConsultInstructions).

Obraťte se na partnera Microsoftu, který vám poskytne odborného technického konzultanta k nastavení volání podle rozsahu vašich potřeb.

## <a name="report"></a>Sestava
Vytváření sestav o využití Azure sledovaných prostřednictvím přidělení zákaznického využití není dnes k dispozici pro partnery ISV. Přidávání sestav do programu komerčního tržiště v partnerském centru, které pokrývají přidělení zákaznického využití, je určené pro druhou polovinu 2021.

## <a name="faq"></a>Časté otázky

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>Po přidání ID sledování je možné ho změnit?

ID sledování pro aplikace Azure na komerčním tržišti jsou spravována automaticky partnerským centrem. Zákazník si ale může stáhnout šablonu a změnit nebo odebrat ID sledování. Partneři by měli proaktivně popsat roli ID sledování zákazníkům, aby nedocházelo k odebrání nebo úpravám. Změna ID sledování má vliv jenom na nová nasazení a prostředky, ne na existující.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>Můžu sledovat Šablony nasazené z jiného úložiště než od Microsoftu, jako je GitHub?

Ano, pokud je k dispozici ID sledování při nasazení šablony, je sledování využití sledováno. Pokud chcete zachovat přidružení mezi vámi a vaším vydavatelem a šablonou nasazenou z jiného úložiště než od Microsoftu, Stáhněte si nejdřív kopii publikované šablony (která bude obsahovat ID sledování) ze seznamu komerčních tržišť vaší nabídky v Azure Portal. Publikujte tuto verzi do GitHubu nebo jiné úložiště jiného typu než Microsoftu.

Pokud vaše šablona není uvedená na komerčním webu Marketplace a obsahuje registrovaný identifikátor GUID, ujistěte se, že se identifikátor GUID nachází ve verzi, kterou publikujete do GitHubu nebo v jiném úložišti jiného výrobce než Microsoftu.

#### <a name="does-the-customer-receive-reporting-as-well"></a>Obdrží zákazník hlášení také?

No. Zákazníci mohou sledovat využití všech prostředků nebo skupin prostředků v rámci Azure Portal. Zákazníkům se nezobrazuje využití rozdělené podle ID sledování CUA.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>Podobá se využívání zákazníků záznamu partnera DPOR (Digital Partner of Record) nebo partnerského odkazu pro správce (PAL)?

Označení zákaznického využití je mechanismem, který přidruží využití Azure k možnému partnerovi, který lze nasadit v době nasazení. PARTNERA DPOR a PAL mají za cíl přidružit poradenský partner (systémy) nebo správu (poskytovatele spravované služby) se zákaznickými nároky zákazníka na dobu, kdy partner pracuje se zákazníkem.