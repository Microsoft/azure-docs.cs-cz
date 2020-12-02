---
title: Obchodní partneři na webu Marketplace a přidělení zákaznického využití
description: Získejte přehled o sledování zákaznického využití pro Azure Marketplace řešení.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 11/4/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: e9e630f4199e0bfb67509f008f403c0a7a490887
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436687"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Obchodní partneři na webu Marketplace a přidělení zákaznického využití

Označení zákaznického využití je způsob, jak přidružit prostředky Azure běžící v zákaznických předplatných, které jste nasadili pro spuštění vašeho řešení, a jako partnera. Vytváření těchto přidružení v interních systémech Microsoftu přináší větší přehled o službě Azure s nároky na provoz vašeho softwaru. Když přijmete tuto funkci sledování, zarovnáváte se s prodejními týmy Microsoftu a získáte kredit pro partnerské programy Microsoftu.

Přidružení můžete vytvořit prostřednictvím Azure Marketplace, úložiště pro rychlé zprovoznění, privátních úložišť GitHubu a 1:1 zákaznických zapojení, která vytvářejí trvalá IP adresa (například vývoj aplikace).

Označení zákaznického využití podporuje tři možnosti nasazení:

- Azure Resource Manager šablony: partneři můžou pomocí šablon Správce prostředků nasadit služby Azure, aby mohli provozovat software partnera. Partneři můžou vytvořit šablonu Správce prostředků k definování infrastruktury a konfigurace jejich řešení Azure. Šablona Správce prostředků umožňuje vašim zákazníkům nasadit vaše řešení v celém životním cyklu. Máte jistotu, že se prostředky nasazují v konzistentním stavu.
- Rozhraní Azure Resource Manager API: partneři můžou volat rozhraní API Správce prostředků přímo, aby mohli nasadit šablonu Správce prostředků nebo aby generovala volání rozhraní API, která přímo zřídí služby Azure.
- Terraformu: partneři můžou pomocí Terraformu nasadit šablonu Správce prostředků nebo přímo nasadit služby Azure.

>[!IMPORTANT]
>- Označení zákaznického využití není určené ke sledování práce integrátorů systémů, poskytovatelů spravovaných služeb nebo nástrojů určených pro nasazení a správu softwaru běžícího na Azure.
>
>- Přidělení zákaznického využití je pro nová nasazení a nepodporuje označení existujících prostředků, které už byly nasazené.
>
>- Pro nabídky [aplikací Azure](./create-new-azure-apps-offer.md) publikované do Azure Marketplace se vyžaduje přidělení zákaznického využití.
>
>- Ne všechny služby Azure jsou kompatibilní se zákaznickým využitím. Služba Azure Kubernetes Services (AKS) a VM Scale Sets v současnosti známé problémy, které způsobují v hlášení o využití.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Vytvořit GUID

Identifikátor GUID je jedinečný referenční identifikátor, který obsahuje 32 hexadecimálních číslic. Pokud chcete pro sledování vytvořit GUID, měli byste použít generátor GUID, například přes PowerShell.

```powershell
[guid]::NewGuid()
```

Pro každou nabídku a distribuční kanál pro každý produkt doporučujeme vytvořit jedinečný identifikátor GUID. Pokud nechcete, aby vytváření sestav bylo rozděleno, můžete použít jeden identifikátor GUID pro více distribučních kanálů produktu.

Pokud nasadíte produkt pomocí šablony a je dostupný jak na Azure Marketplace, tak i na GitHubu, můžete vytvořit a zaregistrovat dva jedinečné identifikátory GUID:

- Produkt A v Azure Marketplace
- Produkt A na GitHubu

Vytváření sestav se provádí pomocí ID Microsoft Partner Network a identifikátoru GUID.

Využití můžete sledovat i na podrobnější úrovni registrací dalších identifikátorů GUID a změnou identifikátorů GUID mezi plány, kde jsou plány variantou nabídky.

## <a name="register-guids"></a>Registrace identifikátorů GUID

Identifikátory GUID musí být zaregistrované v partnerském centru, aby bylo možné přičíst zákazníky.

Po přidání identifikátoru GUID do šablony nebo uživatelského agenta a registraci identifikátoru GUID v partnerském centru jsou sledována budoucí nasazení.

> [!NOTE]
> Pokud publikujete nabídku [aplikace Azure](./create-new-azure-apps-offer.md) , která Azure Marketplace prostřednictvím partnerského centra, všechny nové GUID používané v šabloně se při nahrání šablony automaticky zaregistrují do vašeho profilu partnerského centra.  

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard).

1. Zaregistrujte se jako [komerční Vydavatel na webu Marketplace](https://aka.ms/JoinMarketplace).

   * Partneři musí [mít profil v partnerském centru](./partner-center-portal/create-account.md). Doporučujeme zobrazit seznam nabídek v Azure Marketplace nebo AppSource.
   * Partneři můžou registrovat víc identifikátorů GUID.
   * Partneři můžou registrovat GUID pro šablony řešení mimo Marketplace a nabídky.

1. V pravém horním rohu > **Nastavení účtu** vyberte **Nastavení** (ikona ozubeného kolečka).

1. V nabídce identifikátory **profilů organizace**  >  **Identifiers** vyberte **Přidat identifikátor GUID sledování**.

1. Do pole **identifikátor GUID** zadejte identifikátor GUID sledování. Zadejte pouze identifikátor GUID bez `pid-` předpony. Do pole **Popis** zadejte název nebo popis vaší nabídky.

1. Pokud chcete zaregistrovat více než jeden identifikátor GUID, vyberte znovu **Přidat identifikátor GUID sledování** . Na stránce se zobrazí další pole.

1. Vyberte **Uložit**.

## <a name="use-resource-manager-templates"></a>Použití šablon Resource Manageru
Mnohé z partnerských řešení se nasazují pomocí Azure Resource Manager šablon. Pokud máte šablonu Správce prostředků, která je k dispozici v Azure Marketplace, na GitHubu nebo jako rychlý Start, proces změny šablony tak, aby bylo možné označení zákaznického využívání rovného dopřed.

> [!NOTE]
> Další informace o vytváření a publikování šablon řešení najdete v tématu.
> * [Vytvořte a nasaďte první šablonu správce prostředků](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
>* [Nabídka aplikací Azure](./create-new-azure-apps-offer.md)
>* Video: [sestavování šablon řešení a spravovaných aplikací pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Chcete-li přidat globálně jedinečný identifikátor (GUID), proveďte jednu úpravu souboru hlavní šablony:

1. [Vytvořte identifikátor GUID](#create-guids) pomocí navrhované metody a [zaregistrujte identifikátor GUID](#register-guids).

1. Otevřete šablonu Správce prostředků.

1. Přidejte do hlavního souboru šablony nový prostředek typu [Microsoft. Resources/Deployments](/azure/templates/microsoft.resources/deployments) . Prostředek musí být v **mainTemplate.js** nebo **azuredeploy.jspouze v** souboru, a ne v žádné vnořené nebo propojené šabloně.

1. Jako název prostředku zadejte hodnotu GUID za `pid-` předponou. Pokud je například identifikátor GUID eb7927c8-dd66-43e1-b0cf-c346a422063, název prostředku bude _PID-eb7927c8-dd66-43e1-b0cf-c346a422063_.

1. V šabloně vyhledejte případné chyby.

1. Znovu publikujte šablonu v příslušných úložištích.

1. [Ověřte úspěch identifikátoru GUID v nasazení šablony](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Ukázka kódu Správce prostředků šablony

Chcete-li povolit sledování prostředků pro šablonu, je třeba přidat následující další prostředek do části prostředky. Nezapomeňte prosím, abyste při přidávání do hlavního souboru šablony upravili následující vzorový kód s vlastními vstupy.
Prostředek se musí přidat do **mainTemplate.js** nebo **azuredeploy.jsjenom v** souboru, a ne v žádné vnořené nebo propojené šabloně.

```json
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
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

## <a name="use-the-resource-manager-apis"></a>Použití rozhraní API pro Správce prostředků

V některých případech můžete chtít volat přímo proti Správce prostředků rozhraní REST API pro nasazení služeb Azure. [Azure podporuje několik sad SDK](../index.yml?pivot=sdkstools) , aby tato volání mohla povolit. Můžete použít jednu ze sad SDK nebo volat rozhraní REST API přímo k nasazení prostředků.

Pokud používáte šablonu Správce prostředků, měli byste označit své řešení podle pokynů popsaných výše. Pokud nepoužíváte šablonu Správce prostředků a provádíte Přímá volání rozhraní API, můžete si i nadále označit nasazení, aby bylo možné přidružit využití prostředků Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Označení nasazení pomocí Správce prostředků rozhraní API

Pokud chcete při návrhu volání rozhraní API povolit jeho přihlašování, uveďte v žádosti identifikátor GUID v hlavičce uživatelského agenta. Přidejte identifikátor GUID pro každou nabídku nebo SKU. Naformátujte řetězec `pid-` předponou a zahrňte identifikátor GUID generovaný partnerem. Tady je příklad formátu identifikátoru GUID pro vložení do uživatelského agenta:

![Příklad formátu GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> Formát řetězce je důležitý. Pokud `pid-` Předpona není zahrnutá, není možné zadat dotaz na data. Různé sady SDK sledují různě. Pokud chcete implementovat tuto metodu, přečtěte si přístup k podpoře a sledování pro preferovanou sadu Azure SDK.

#### <a name="example-the-python-sdk"></a>Příklad: sada Python SDK

Pro Python použijte atribut **config** . Atribut lze přidat pouze k vlastnosti UserAgent. Tady je příklad:

![Přidání atributu do uživatelského agenta](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Přidejte atribut pro každého klienta. Neexistuje žádná globální statická konfigurace. Můžete označit objekt pro vytváření klienta, aby se ujistil, že každý klient sleduje sledování. Další informace najdete v tématu tato [Ukázka klienta v GitHubu](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-the-net-sdk"></a>Příklad: sada .NET SDK

V případě .NET nezapomeňte nastavit agenta pro uživatele. Knihovna [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) se dá použít k nastavení uživatelského agenta s následujícím kódem (příklad v jazyce C#):

```csharp

var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Označení nasazení pomocí Azure PowerShell

Pokud prostředky nasazujete prostřednictvím Azure PowerShell, přidejte svůj identifikátor GUID pomocí následující metody:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Označení nasazení pomocí Azure CLI

Když k připojení svého GUID použijete rozhraní příkazového řádku Azure CLI, nastavte proměnnou prostředí **AZURE_HTTP_USER_AGENT** . Tuto proměnnou můžete nastavit v rámci oboru skriptu. Pro rozsah prostředí můžete také nastavit proměnnou globálně:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Další informace najdete v tématu [Azure SDK pro go](/azure/developer/go/).

## <a name="use-terraform"></a>Použití Terraformu

Podpora pro Terraformu je dostupná prostřednictvím 1.21.0 verze poskytovatele Azure: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) .  Tato podpora se vztahuje na všechny partnery, kteří nasazují své řešení prostřednictvím Terraformu, a všechny nasazené prostředky a měřené poskytovatelem Azure (verze 1.21.0 nebo novější).

Poskytovatel Azure pro Terraformu přidal nové volitelné pole s názvem [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) , kde můžete zadat sledovací identifikátor GUID, který používáte pro vaše řešení. Hodnota tohoto pole může být také zdrojová z proměnné prostředí *ARM_PARTNER_ID* .

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partneři, kteří chtějí získat své nasazení prostřednictvím Terraformuu, které sledují označení zákaznického využití, musí provádět tyto akce:

* Vytvořte identifikátor GUID (identifikátor GUID by měl být přidán pro každou nabídku nebo SKU).
* Aktualizujte svého poskytovatele Azure a nastavte hodnotu *partner_id* na identifikátor GUID (neopravte GUID pomocí "PID-", stačí ho nastavit na skutečný identifikátor GUID).

## <a name="verify-the-guid-deployment"></a>Ověření nasazení identifikátoru GUID

Po úpravě šablony a spuštění testovacího nasazení použijte následující skript prostředí PowerShell k načtení prostředků, které jste nasadili a označili.

Pomocí skriptu můžete ověřit, že identifikátor GUID je úspěšně přidaný do šablony Správce prostředků. Tento skript se nevztahuje na Správce prostředků nasazení API nebo Terraformu.

Přihlaste se k Azure. Před spuštěním skriptu vyberte předplatné s nasazením, které chcete ověřit. Spusťte skript v rámci předplatného nasazení.

**Identifikátor GUID** a název **zdrojové** sady nasazení jsou povinné parametry.

[Původní skript](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) můžete získat na GitHubu.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Sestava

Sestavu pro přidělení zákaznického využití najdete na řídicím panelu partnerského centra ( [https://partner.microsoft.com/dashboard/partnerinsights/analytics/overview](https://partner.microsoft.com/dashboard/partnerinsights/analytics/overview) ). Pokud chcete zobrazit sestavu, musíte se přihlásit pomocí přihlašovacích údajů partnerského centra. Pokud narazíte na nějaké problémy se sestavou nebo přihlášením, vytvořte [žádost o podporu](#get-support).

## <a name="notify-your-customers"></a>Upozorněte vaše zákazníky

Partneři by měli informovat své zákazníky o nasazeních, která používají označení zákaznického využití. Microsoft oznamuje partnerovi využití Azure, které je k těmto nasazením přidružené. Následující příklady zahrnují obsah, který můžete použít k informování zákazníků o těchto nasazeních. V příkladech nahraďte \<PARTNER> názvem vaší společnosti. Partneři by se měli ujistit, že se oznámení zarovnají se zásadami ochrany osobních údajů a kolekcí dat, včetně možností pro vyloučení zákazníků ze sledování.

### <a name="notification-for-resource-manager-template-deployments"></a>Oznámení pro nasazení šablon Správce prostředků

Když tuto šablonu nasadíte, Microsoft dokáže identifikovat instalaci \<PARTNER> softwaru s nasazenými prostředky Azure. Společnost Microsoft je schopná korelovat prostředky Azure, které se používají k podpoře softwaru. Společnost Microsoft tyto informace shromažďuje, aby poskytovala co nejvíc zkušeností s produkty a pracovala s jejich podnikáním. Data se shromažďují a řídí zásadami ochrany osobních údajů od Microsoftu, které najdete na adrese [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

### <a name="notification-for-sdk-or-api-deployments"></a>Oznámení pro nasazení SDK nebo rozhraní API

Když nasadíte \<PARTNER> software, společnost Microsoft dokáže identifikovat instalaci \<PARTNER> softwaru s nasazenými prostředky Azure. Společnost Microsoft je schopná korelovat prostředky Azure, které se používají k podpoře softwaru. Společnost Microsoft tyto informace shromažďuje, aby poskytovala co nejvíc zkušeností s produkty a pracovala s jejich podnikáním. Data se shromažďují a řídí zásadami ochrany osobních údajů od Microsoftu, které najdete na adrese [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="get-support"></a>Získání podpory

Seznamte se s možnostmi podpory na komerčním tržišti v části [Podpora programu komerčního tržiště v partnerském centru](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Odeslání žádosti o technickou spolupráci

1. Navštivte [partnerské technické služby](https://aka.ms/TechnicalJourney).
1. Vyberte cloudovou infrastrukturu a správu a otevře se nová stránka, která vám umožní zobrazit technickou cestu.
1. V části služby nasazení klikněte na tlačítko Odeslat žádost.
1. Přihlaste se pomocí účtu MSA (MPN) nebo svého AAD (účet partnerského řídicího panelu). na základě přihlašovacích údajů pro přihlášení se otevře formulář online žádosti:
    * Dokončete/zkontrolujte kontaktní údaje.
    * Informace o konzultacích můžou být předem vyplněné nebo si můžete vybrat z rozevíracích seznamu.
    * Zadejte název a popis problému (uveďte co nejvíc podrobností).

1. Klikněte na Submit (Odeslat).

Prohlédněte si podrobné pokyny k snímkům obrazovky s [použitím služeb technické služby předprodejní a nasazení](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Kam dál

Obraťte se na partnera Microsoftu, který vám poskytne odborného technického konzultanta k nastavení volání podle rozsahu vašich potřeb.

## <a name="faq"></a>Časté otázky

**Jaké jsou výhody přidání identifikátoru GUID do šablony?**

Microsoft poskytuje partnerům přehled o zákaznických nasazeních jejich řešení a přehledy o jejich ovlivněném využití. Microsoft i partner můžou tyto informace využít k tomu, aby proužívali užší zapojení mezi prodejními týmy. Microsoft i partner můžou data používat k získání jednotnějšího zobrazení dopadu jednotlivých partnerů na růst Azure.

**Po přidání identifikátoru GUID ho lze změnit?**

Ano, zákazník nebo implementační partner může šablonu přizpůsobit a může změnit nebo odebrat identifikátor GUID. Doporučujeme, aby partneři proaktivně popsali roli prostředku a identifikátor GUID svým zákazníkům a partnerům, aby se zabránilo odebrání nebo úpravám identifikátoru GUID. Změna identifikátoru GUID ovlivní jenom nové, neexistující, nasazení a prostředky.

**Můžu sledovat Šablony nasazené z jiného úložiště než od Microsoftu, jako je GitHub?**

Ano, pokud je k dispozici identifikátor GUID při nasazení šablony, je sledování využití sledováno. Partneři musí stále registrovat své identifikátory GUID.

**Obdrží zákazník hlášení také?**

Zákazníci mohou sledovat využití jednotlivých prostředků nebo skupin prostředků definovaných zákazníkem v rámci Azure Portal. Zákazníci nevidí použití členěné podle identifikátoru GUID.

**Je tato metodika podobná digitálnímu partnerovi záznamu (partnera DPOR)?**

Tato nová metoda propojení nasazení a využití s řešením partnera poskytuje mechanismus pro propojení partnerského řešení s využitím Azure. PARTNERA DPOR má k přidružení konzultačního poradce (systémů) nebo správy (poskytovatele spravované služby) k předplatnému Azure zákazníka.

**Můžu použít privátní vlastní virtuální pevný disk pro nabídku šablony řešení v Azure Marketplace?**

Ne, nemůžete. Image virtuálního počítače musí pocházet z Azure Marketplace, přečtěte si téma: [Průvodce publikováním pro nabídky virtuálních počítačů na Azure Marketplace](marketplace-virtual-machines.md).

Nabídku virtuálních počítačů můžete na webu Marketplace vytvořit pomocí vlastního virtuálního pevného disku a označit ji jako soukromou, takže ji nikdo neuvidí. Pak na tento virtuální počítač na šablonu řešení odkazujte.

**Nepovedlo se aktualizovat vlastnost *contentversion –* pro hlavní šablonu?**

To je nejspíš chyba v případech, kdy se šablona nasazuje pomocí TemplateLink z jiné šablony, která z nějakého důvodu očekává starší Contentversion –. Alternativním řešením je použití vlastnosti metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
