---
title: Přiřazení partnera a využití zákazníků na komerčním trhu
description: Získejte přehled o sledování využití zákazníků pro řešení Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: dsindona
ms.openlocfilehash: 5fec72ce5f86c1bee9ec0e978e458f5be454c8e3
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256565"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Přiřazení partnera a využití zákazníků na komerčním trhu

Přiřazení využití zákazníků je metoda, která přidruží prostředky Azure spuštěné v zákaznických předplatných, které jsou nasazeny ke spuštění vašeho řešení, s vámi jako partnerem. Vytváření těchto přidružení v interních systémech Microsoft přináší větší viditelnost na azure stopy spuštěný software. Při přijetí této funkce sledování zarovnáte s prodejními týmy společnosti Microsoft a získáte kredit pro partnerské programy společnosti Microsoft.

Přidružení můžete vytvořit prostřednictvím Azure Marketplace, úložiště QuickStart, soukromých úložišť GitHub a zákaznických interakcí 1:1, které vytvářejí trvalou IP adresu (například vývoj aplikace).

Přiřazení využití zákazníka podporuje tři možnosti nasazení:

- Šablony Azure Resource Manageru: Partneři můžou pomocí šablon Správce prostředků nasadit služby Azure ke spuštění softwaru partnera. Partneři můžou vytvořit šablonu Správce prostředků, která definuje infrastrukturu a konfiguraci svého řešení Azure. Šablona Správce prostředků umožňuje vám a vašim zákazníkům nasadit vaše řešení po celou dobu jeho životního cyklu. Můžete si být jisti, že vaše prostředky jsou nasazeny v konzistentním stavu.
- Azure Resource Manager ROZHRANÍ API: Partneři můžete volat rozhraní API Správce prostředků přímo nasadit šablonu Správce prostředků nebo generovat volání rozhraní API přímo zřídit služby Azure.
- Terraform: Partneři můžou pomocí Terraformu nasadit šablonu Správce prostředků nebo přímo nasadit služby Azure.

>[!IMPORTANT]
>- Přiřazení využití zákazníků není určeno ke sledování práce systémových integrátorů, poskytovatelů spravovaných služeb nebo nástrojů určených k nasazení a správě softwaru spuštěného v Azure.
>
>- Přiřazení využití zákazníka je pro nová nasazení a NEPODPORUJE označování existujících prostředků, které již byly nasazeny.
>
>- Přiřazení využití zákazníků je vyžadováno pro nabídky [aplikací Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer) publikované na Azure Marketplace.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Vytvoření identifikátorů GUID

Identifikátor GUID je jedinečný referenční identifikátor, který má 32 šestnáctkových číslic. Chcete-li vytvořit identifikátory GUID pro sledování, měli byste použít generátor identifikátorů GUID. Tým služby Azure Storage vytvořil [formulář generátoru IDENTIFIKÁTORŮ GUID,](https://aka.ms/StoragePartners) který vám pošle e-mail s identifikátorem GUID správného formátu a může být znovu použit v různých sledovacích systémech.

> [!NOTE]
> Důrazně doporučujeme použít [formulář generátoru GUID služby Azure Storage](https://aka.ms/StoragePartners) k vytvoření identifikátoru GUID. Další informace naleznete v [nejčastějších dotazech](#faq).

Doporučujeme vytvořit jedinečný identifikátor GUID pro každou nabídku a distribuční kanál pro každý produkt. Pokud nechcete, aby bylo hlášení rozděleno, můžete použít jeden identifikátor GUID pro více distribučních kanálů produktu.

Pokud nasadíte produkt pomocí šablony a je k dispozici na Azure Marketplace i na GitHubu, můžete vytvořit a zaregistrovat dva odlišné identifikátory GUID:

- Produkt A na Azure Marketplace
- Produkt A na GitHubu

Vytváření sestav provádí ID sítě partnerů společnosti Microsoft a identifikátor GUID.

Můžete také sledovat využití na podrobnější úrovni registrací dalších identifikátorů GUID a změnou identifikátorů GUID mezi plány, kde jsou plány variantami nabídky.

## <a name="register-guids"></a>Registrace identifikátorů GUID

Identifikátory GUID musí být registrovány v Partnerském centru, aby bylo možné přiřadit autorství využití zákazníka.

Po přidání identifikátoru GUID do šablony nebo do uživatelského agenta a registraci identifikátoru GUID v Centru partnerů budou sledována budoucí nasazení.

1. Přihlaste se do [Centra partnerů](https://partner.microsoft.com/dashboard).

1. Zaregistrujte se jako [vydavatel komerčního tržiště](https://aka.ms/JoinMarketplace).

   * Partneři musí [mít profil v Partnerském centru](https://docs.microsoft.com/azure/marketplace/become-publisher). Doporučujeme uvést nabídku na Azure Marketplace nebo AppSource.
   * Partneři mohou zaregistrovat více identifikátorů GUID.
   * Partneři mohou registrovat identifikátory GUID pro šablony a nabídky řešení, které nejsou součástí tržiště.

1. V pravém horním rohu vyberte ikonu ozubeného kola nastavení a pak vyberte **Nastavení vývojáře**.

1. Na **stránce Nastavení účtu**vyberte **Přidat identifikátor GUID sledování.**

1. Do pole **GUID** zadejte identifikátor GUID pro sledování. Zadejte pouze GUID bez **pid-** prefix. Do pole **Popis** zadejte název nabídky nebo popis.

1. Chcete-li zaregistrovat více identifikátorů GUID, vyberte znovu **přidat identifikátor GUID sledování.** Na stránce se zobrazí další pole.

1. Vyberte **Uložit**.

## <a name="use-resource-manager-templates"></a>Použití šablon Resource Manageru
Mnoho partnerských řešení se nasazuje pomocí šablon Azure Resource Manager. Pokud máte šablonu Správce prostředků, která je dostupná na Azure Marketplace, na GitHubu nebo jako rychlý start, proces úpravy šablony tak, aby umožňoval přiřazení využití zákazníků, je přímočarý.

> [!NOTE]
> Další informace o vytváření a publikování šablon řešení naleznete v tématu
> * [Vytvořte a nasaďte svou první šablonu Správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
>* [Nabídka aplikací Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
>* Video: [Vytváření šablon řešení a spravovaných aplikací pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Chcete-li přidat globálně jedinečný identifikátor (GUID), proveďte jednu změnu hlavního souboru šablony:

1. [Vytvořte identifikátor GUID](#create-guids) pomocí navrhované metody a [zaregistrujte identifikátor GUID](#register-guids).

1. Otevřete šablonu Správce prostředků.

1. Přidejte nový prostředek do hlavního souboru šablony. Prostředek musí být pouze v **mainTemplate.json** nebo **azuredeploy.json** souboru a ne v žádné vnořené nebo propojené šablony.

1. Za **předponu pid-** zadejte hodnotu GUID (např. pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Zkontrolujte, zda šablona neobsahuje chyby.

1. Znovu publikujte šablonu v příslušných úložištích.

1. [Ověřte úspěšnost identifikátorů GUID při nasazení šablony](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Ukázkový kód šablony Správce prostředků

Chcete-li povolit sledování prostředků pro šablonu, je třeba přidat následující další prostředek v části zdroje. Ujistěte se, že upravit níže ukázkový kód s vlastními vstupy při přidání do hlavního souboru šablony.
Prostředek je třeba přidat pouze do souboru **mainTemplate.json** nebo **azuredeploy.json** a ne v žádné vnořené nebo propojené šablony.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
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

## <a name="use-the-resource-manager-apis"></a>Použití api Správce prostředků

V některých případech můžete raději volat přímo proti api REST Správce prostředků k nasazení služeb Azure. [Azure podporuje více sad SDK](https://docs.microsoft.com/azure/?pivot=sdkstools) pro povolení těchto volání. Můžete použít jednu z sad SDK nebo volání REST API přímo k nasazení prostředků.

Pokud používáte šablonu Správce prostředků, měli byste své řešení označit podle výše popsaných pokynů. Pokud nepoužíváte šablonu Správce prostředků a provádíte volání rozhraní API přímo, můžete stále označit vaše nasazení a přidružit využití prostředků Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Označení nasazení pomocí api Správce prostředků

Chcete-li povolit přiřazení využití zákazníka, při navrhování volání rozhraní API, zahrňte identifikátor GUID v záhlaví uživatelského agenta v požadavku. Přidejte identifikátor GUID pro každou nabídku nebo skladovou položku. Naformátujte řetězec pomocí **předpony pid** a zahrňte identifikátor GUID generovaný partnerem. Tady je příklad formátu GUID pro vložení do uživatelského agenta:

![Příklad formátu GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> Formát řetězce je důležitý. Pokud není zahrnuta předpona **pid,** není možné zadat dotaz na data. Různé sady SDK sledují odlišně. Chcete-li implementovat tuto metodu, zkontrolujte podporu a sledování přístup pro upřednostňované Azure SDK.

#### <a name="example-the-python-sdk"></a>Příklad: Python SDK

Pro Python použijte atribut **config.** Atribut můžete přidat pouze do useragent. Tady je příklad:

![Přidání atributu do uživatelského agenta](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Přidejte atribut pro každého klienta. Neexistuje žádná globální statická konfigurace. Můžete označit továrnu klienta, abyste měli jistotu, že každý klient sleduje. Další informace naleznete v této [ukázce továrního připojení klienta na GitHubu](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Označení nasazení pomocí Azure PowerShellu

Pokud nasadíte prostředky přes Azure PowerShell, přidejte identifikátor GUID pomocí následující metody:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Označení nasazení pomocí příkazového příkazového příkazu Azure

Když použijete rozhraní příkazového uživatelského rozhraní Azure k připojení identifikátoru GUID, nastavte proměnnou **prostředí AZURE_HTTP_USER_AGENT.** Tuto proměnnou můžete nastavit v rámci oboru skriptu. Můžete také nastavit proměnnou globálně pro obor prostředí:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Další informace naleznete v [tématu Azure SDK for Go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Použití Terraform

Podpora terraformu je dostupná prostřednictvím verze 1.21.0 poskytovatele Azure: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Tato podpora platí pro všechny partnery, kteří nasazují své řešení prostřednictvím Terraform, a všechny prostředky nasazené a účtované poskytovatelem Azure (verze 1.21.0 nebo novější).

Poskytovatel Azure pro Terraform přidal nové volitelné pole s názvem [*partner_id,*](https://www.terraform.io/docs/providers/azurerm/#partner_id) ve kterém zadáte identifikátor GUID pro sledování, který používáte pro své řešení. Hodnota tohoto pole může být také získána z *proměnné prostředí ARM_PARTNER_ID.*

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partneři, kteří chtějí, aby jejich nasazení prostřednictvím Terraform sledováno přiřazením využití zákazníka, musí udělat následující:

* Vytvoření identifikátoru GUID (identifikátor GUID by měl být přidán pro každou nabídku nebo skladovou položku)
* Aktualizujte svého zprostředkovatele Azure, aby nastavil hodnotu *partner_id* na identifikátor GUID (NEOPRAVUJTE identifikátor GUID předem pomocí "pid-", stačí jej nastavit na skutečný identifikátor GUID)


## <a name="verify-the-guid-deployment"></a>Ověření nasazení identifikátoru GUID

Po úpravě šablony a spuštění testovacího nasazení načtěte prostředky, které jste nasadili a označili, pomocí následujícího skriptu prostředí PowerShell.

Pomocí skriptu můžete ověřit, zda byl identifikátor GUID úspěšně přidán do šablony Správce prostředků. Skript se nevztahuje na rozhraní API Správce prostředků nebo nasazení Terraform.

Přihlaste se k Azure. Vyberte předplatné s nasazením, které chcete ověřit před spuštěním skriptu. Spusťte skript v kontextu předplatného nasazení.

**Guid** a **název skupiny prostředků** nasazení jsou požadované parametry.

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

Přehled pro přiřazení využití zákazníků najdete na řídicím[https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)panelu Partnerského centra ( ). Chcete-li sestavu zobrazit, musíte k přihlášení použít přihlašovací údaje centra partnerů. Pokud narazíte na nějaké problémy se sestavou nebo přihlášením, vytvořte žádost o podporu podle pokynů v části Získat podporu.

Chcete-li sestavu zobrazit, zvolte v rozevíracím seznamu typ přidružení partnera možnost Sestopá šablona.

![Sestava pro přiřazení využití zákazníka](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Upozorněte své zákazníky

Partneři by měli informovat své zákazníky o nasazeních, která používají přiřazení využití zákazníků. Microsoft hlásí využití Azure, které je přidruženo k těmto nasazením, partnerovi. Mezi následující příklady patří obsah, který můžete použít k upozornění zákazníků na tato nasazení. V příkladech nahraďte \<partnera PARTNER> názvem vaší společnosti. Partneři by měli zajistit, aby oznámení bylo v souladu s jejich zásadami ochrany osobních údajů a shromažďování osobních údajů, včetně možností, aby byli zákazníci vyloučeni ze sledování.

### <a name="notification-for-resource-manager-template-deployments"></a>Oznámení o nasazení šablon Správce prostředků

Když nasadíte tuto šablonu, Microsoft \<je schopen identifikovat instalaci softwaru PARTNER> s prostředky Azure, které jsou nasazeny. Microsoft je schopen korelovat prostředky Azure, které se používají k podpoře softwaru. Společnost Microsoft shromažďuje tyto informace, aby poskytla nejlepší zkušenosti se svými produkty a provozovala své podnikání. Data jsou shromažďována a řídí se zásadami ochrany osobních https://www.microsoft.com/trustcenterúdajů společnosti Microsoft, které naleznete na adrese .

### <a name="notification-for-sdk-or-api-deployments"></a>Oznámení pro nasazení sady SDK nebo rozhraní API

Když nasazujete \<software PARTNER>, \<Microsoft je schopen identifikovat instalaci softwaru PARTNER> s prostředky Azure, které jsou nasazeny. Microsoft je schopen korelovat prostředky Azure, které se používají k podpoře softwaru. Společnost Microsoft shromažďuje tyto informace, aby poskytla nejlepší zkušenosti se svými produkty a provozovala své podnikání. Data jsou shromažďována a řídí se zásadami ochrany osobních https://www.microsoft.com/trustcenterúdajů společnosti Microsoft, které naleznete na adrese .

## <a name="get-support"></a>Získat podporu

Existují dva kanály podpory v závislosti na problémech, kterým čelíte.

Pokud narazíte na nějaké problémy v Partnerském centru, jako je zobrazení sestavy atribuce využití zákazníka nebo přihlášení, vytvořte žádost o podporu s týmem podpory Partnerského centra zde:[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Pokud potřebujete pomoc při registraci marketplace a/nebo přiřazení využití zákazníka obecně, například jak nastavit přiřazení využití zákazníka, postupujte podle následujících kroků:

1. Přejděte na [stránku podpory](https://go.microsoft.com/fwlink/?linkid=844975).

1. V části **Typ problému**vyberte možnost **Marketplace Onboarding**.

1. Vyberte **kategorii** pro váš problém:

   - Chcete-li problémy s přidružením použití, vyberte **možnost Jiné**.
   - Pokud chcete vyřešit problémy s Azure Marketplace, vyberte **Problém s přístupem**.

     ![Výběr kategorie problému](media/marketplace-publishers-guide/lu-article-incident.png)

1. Vyberte **možnost Spustit požadavek**.

1. Na další stránce zadejte požadované hodnoty. Vyberte **Pokračovat**.

1. Na další stránce zadejte požadované hodnoty.

   > [!IMPORTANT]
   > Do pole **Název incidentu** zadejte sledování **využití nepoužívání .** Popište svůj problém podrobně.

   ![Zadejte sledování využití isv pro název incidentu](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Vyplňte formulář a pak vyberte **Odeslat**.

Můžete také získat technické pokyny od technického konzultanta společnosti Microsoft pro technické scénáře předprodeje, nasazení a vývoje aplikací, abyste pochopili a zahrnuli přiřazení využití zákazníků.

### <a name="how-to-submit-a-technical-consultation-request"></a>Jak podat žádost o technickou konzultaci

1. Navštivte [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Vyberte Cloudová infrastruktura a správa a otevře se nová stránka, kde můžete zobrazit technickou cestu.
1. V části Deployment Services klikněte na tlačítko Odeslat žádost.
1. Přihlaste se pomocí msa (účet MPN) nebo ad (účet partnerského řídicího panelu); na základě přihlašovacích údajů se otevře online formulář žádosti:
    * Vyplňte/zkontrolujte kontaktní údaje.
    * Podrobnosti konzultace mohou být předem vyplněny nebo mohou být vyvoleny z rozevíracích seznamů.
    * Zadejte název a popis problému (uveďte co nejvíce podrobností).
1. Klikněte na Submit (Odeslat).

Zobrazení podrobných pokynů se snímky [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)obrazovky na adrese .

### <a name="whats-next"></a>Kam dál

Budete kontaktováni technickým poradcem partnera společnosti Microsoft, abyste nastavili volání, které bude rozsahem vašich potřeb.

## <a name="faq"></a>Nejčastější dotazy

**Jaká je výhoda přidání identifikátoru GUID do šablony?**

Společnost Microsoft poskytuje partnerům přehled o nasazení zákazníků jejich řešení a přehledy o jejich ovlivněném využití. Společnost Microsoft i partner mohou tyto informace použít k podpoře užšího zapojení mezi prodejními týmy. Microsoft i partner můžou data použít k získání konzistentnějšího zobrazení dopadu jednotlivých partnerů na růst Azure.

**Po přidání identifikátoru GUID lze jej změnit?**

Ano, zákazník nebo partner implementace může šablonu přizpůsobit a může změnit nebo odebrat identifikátor GUID. Doporučujeme, aby partneři proaktivně popisovat roli prostředku a GUID svým zákazníkům a partnerům, aby se zabránilo odebrání nebo úpravy GUID. Změna identifikátoru GUID ovlivní pouze nové, nikoli existující, nasazení a prostředky.

**Můžu sledovat šablony nasazené z úložiště, které není microsoftem, jako je GitHub?**

Ano, pokud je identifikátor GUID k dispozici při nasazení šablony, je sledováno použití. Partneři musí stále registrovat své identifikátory GUID.

**Dostává zákazník také hlášení?**

Zákazníci můžou sledovat využití jednotlivých prostředků nebo skupin prostředků definovaných zákazníkem v rámci portálu Azure. Zákazníkům se nezobrazí použití rozdělené identifikátorem GUID.

**Je tato metodika podobná digitálnímu partnerovi záznamu (DPOR)?**

Tato nová metoda připojení nasazení a využití k řešení partnera poskytuje mechanismus pro propojení partnerského řešení s využitím Azure. DPOR je určen k přidružení partnera pro poradenství (systémový integrátor) nebo správu (Managed Service Provider) k předplatnému Azure zákazníka.

**Jaké jsou výhody používání formuláře generátoru GUID služby Azure Storage?**

Azure Storage je GUID generátor formulář je zaručeno, že generovat GUID požadovaného formátu. Navíc pokud používáte některou z metod sledování roviny dat služby Azure Storage, můžete využít stejný identifikátor GUID pro sledování roviny ovládacího prvku Marketplace. To vám umožní využít jeden sjednocený identifikátor GUID pro přiřazení partnera bez nutnosti udržovat samostatné identifikátory GUID.

**Můžu použít soukromý vlastní virtuální pevný disk pro nabídku šablony řešení na Azure Marketplace?**

Ne, to nemůžeš. Image virtuálního počítače musí pocházet z [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)Azure Marketplace, viz: .

Nabídku virtuálních podniků můžete vytvořit na marketplace pomocí vlastního virtuálního pevného disku a označit ji jako soukromou, aby ji nikdo neviděl. Pak odkaz na tento virtuální počítač v šabloně řešení.

**Nepodařilo se aktualizovat vlastnost *contentVersion* pro hlavní šablonu?**

Pravděpodobně chyba v některých případech při nasazení šablony pomocí TemplateLink z jiné šablony, které očekávají starší contentVersion z nějakého důvodu. Řešení je použití vlastnosti metadat:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
