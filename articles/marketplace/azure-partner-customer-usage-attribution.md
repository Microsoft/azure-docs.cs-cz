---
title: Azure partnerů a zákazníků využití attribution | Azure Marketplace
description: Přehled o tom, jak sledovat využití ze strany zákazníků pro řešení Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: yijenj
ms.openlocfilehash: dcab4d24ca948980f28483fd09f29588e0329b63
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800979"
---
# <a name="azure-partner-customer-usage-attribution"></a>Přisuzování využití ze strany zákazníků partnerům Azure

Jako partner softwaru pro Azure vaše řešení nevyžaduje komponenty Azure nebo, je nutné nasadit přímo na infrastruktuře Azure. Zákazníci, kteří nasazení partnerského řešení a vytvořit svoje vlastní prostředky Azure můžete těžké získat přehled o stavu nasazení a získat optická dopad na Azure růst. Když přidáte vyšší úroveň viditelnosti, bylo v souladu s prodejním týmům Microsoftu a získáte kredit ve výši programy partnera společnosti Microsoft.

Microsoft teď nabízí způsob, jak partnerů lépe sledovat využití Azure pro zákaznická nasazení softwaru na Azure. Nová metoda používá Azure Resource Manageru můžete orchestrovat nasazení služby Azure.

Jako partner Microsoftu můžete přidružit využití Azure s jakýmikoli prostředky Azure, které zřizujete jménem zákazníka. Můžete formulář přidružení prostřednictvím Azure Marketplace, rychlý start úložiště, privátní úložiště GitHub a zapojení zákazníků od vyhrazeného pracovníka. Zákazník využití attribution podporuje tři možnosti nasazení:

- Šablony Azure Resource Manageru: Partneři mohou pomocí šablon Resource Manageru k nasazení služeb Azure ke spuštění softwaru partnera. Partneři můžete vytvořit šablonu Resource Manageru pro definování infrastrukturu a konfiguraci jejich řešení Azure. Šablony Resource Manageru umožňuje vám a vašim zákazníkům k nasazení svého řešení v průběhu životního cyklu. Máte jistotu, že se prostředky nasadí konzistentně.
- Rozhraní API Azure Resource Manageru: Partneři mohou volat rozhraní API Resource Manageru přímo k nasazení šablony Resource Manageru nebo ke generování volání rozhraní API přímo zřídit služby Azure.
- Terraform: Partneři slouží k nasazení šablony Resource Manageru nebo přímo nasadit služby Azure cloud orchestrátor, jako je Terraformu.

Využití attribution zákazníků je pro nové nasazení a nepodporuje označování stávající prostředky, které jsou už nasazené.

Attribution využití zákazníky je vyžadován na [aplikace Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): nabídka šablony řešení publikovaných na webu Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Použití šablon Resource Manageru
Mnoho partnerská řešení jsou nasazené na předplatné zákazníka pomocí šablon Resource Manageru. Pokud máte šablony Resource Manageru, který je dostupný v Tržišti Azure Marketplace, na Githubu nebo jako rychlý start, by měl být přímočaré procesu úpravy šablony pro povolení attribution využití zákazníka.

Další informace o vytváření a publikování šablon řešení najdete v tématu

* [Vytvoření a nasazení první šablony Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Nabídka Azure aplikace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Video: [Vytváření řešení šablony a spravované aplikace pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>Přidat identifikátor GUID do šablony

Chcete-li přidat globálně jedinečný identifikátor (GUID), provedete jeden úpravy souboru hlavní šablony:

1. [Vytvořit GUID](#create-guids) pomocí navrhované metody a [zaregistrovat GUID](#register-guids-and-offers).

1. Otevřete šablonu Resource Manageru.

1. Přidáte nový prostředek v souboru hlavní šablony. Prostředek musí být v **mainTemplate.json** nebo **azuredeploy.json** soubor pouze a ne v žádné vnořené nebo propojené šablony.

1. Zadejte hodnotu GUID po **pid -** předpona (například pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Zkontrolujte šablonu pro všechny chyby.

1. Znovu publikujte šablony v příslušné úložiště.

1. [Ověření úspěšné identifikátor GUID v nasazení šablony](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Kód šablony Resource Manageru ukázkový

Pokud chcete povolit sledování prostředků pro vaši šablonu, budete muset přidat následující dalších prostředků v části prostředky. Zkontrolujte, zda chcete-li změnit následující vzorový kód s vlastním vstupů při přidání do souboru hlavní šablony.
Prostředek musí být přidán do **mainTemplate.json** nebo **azuredeploy.json** soubor pouze a ne v žádné vnořené nebo propojené šablony.

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

## <a name="use-the-resource-manager-apis"></a>Použití rozhraní API Resource Manageru

V některých případech můžete chtít volat přímo proti rozhraní REST API Resource Manageru k nasazení služby Azure. [Azure podporuje více sad SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) umožňující těchto volání. Můžete použít jednu ze sad SDK nebo volání rozhraní REST API přímo k nasazení prostředků.

Pokud používáte šablonu Resource Manageru, by mělo být označení řešení podle pokynů, jak je popsáno výše. Pokud nepoužíváte šablony Resource Manageru a vytvoření přímého volání rozhraní API, můžete stále označit nasazení tak, aby přidružit využití prostředků Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Označení nasazení pomocí rozhraní API Resource Manageru

Umožňuje využití ze strany zákazníků attribution při návrhu vaše volání rozhraní API, zahrňte identifikátor GUID hlavičky uživatelského agenta v požadavku. Přidejte identifikátor GUID pro každou nabídku nebo SKU. Formátovací řetězec se **pid -** Předpona a zahrnují GUID generované partnera. Tady je příklad formát identifikátoru GUID pro vložení do uživatelského agenta:

![Příklad formátu identifikátoru GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Formát řetězce je důležité. Pokud **pid -** předpona není zahrnut, není možné zadávat dotazy na data. Různé sady SDK sledovat odlišně. Tuto metodu implementovat, najdete v tématu podporu a sledování přístupu pro vaše preferované Azure SDK.

#### <a name="example-the-python-sdk"></a>Příklad: Python SDK

Pro Python, použijte **config** atribut. Atribut lze přidat pouze do UserAgent. Tady je příklad:

![Přidat atribut pro uživatelského agenta](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Přidejte atribut pro každého klienta. Neexistuje žádné globální statické konfigurace. Může být označení objektu factory klienta k Ujistěte se, že se ke sledování každého klienta. Další informace najdete v tomto [klienta factory ukázka na Githubu](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Označení nasazení pomocí Azure Powershellu

Pokud provádíte nasazení prostředků pomocí Azure Powershellu, připojte vaši GUID pomocí následující metody:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Označení nasazení pomocí rozhraní příkazového řádku Azure

Při použití rozhraní příkazového řádku Azure k připojení vašeho identifikátor GUID, nastavte **AZURE_HTTP_USER_AGENT** proměnné prostředí. Tuto proměnnou lze nastavit v rámci oboru skriptu. Můžete také nastavit proměnnou globálně pro obor prostředí:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Další informace najdete v tématu [Azure SDK for Go](https://docs.microsoft.com/go/azure/).

## <a name="use-terraform"></a>Použití Terraformu

Podpora pro Terraform je dostupná prostřednictvím zprostředkovatele Azure 1.21.0 uvolnit: [ https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019 ](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Tato podpora se vztahuje na všechny partnery, kteří si nasadí jejich řešení pomocí Terraformu a všechny prostředky nasazené a měří pomocí zprostředkovatele služby Azure (verze 1.21.0 nebo novější).

Zprostředkovatel Azure pro Terraformu přidán nový volitelné pole s názvem [ *partner_id* ](https://www.terraform.io/docs/providers/azurerm/#partner_id) což je, kde můžete určit identifikátor GUID, který používáte pro vaše řešení pro sledování. Hodnota tohoto pole můžete také použít jako zdroj *ARM_PARTNER_ID* proměnné prostředí.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partneři, kteří chtějí získat jejich nasazování s využitím Terraformu sledovat využití attribution zákazníka potřebovat provést následující kroky:

* Vytvořit GUID (identifikátor GUID by měl přidat pro jednotlivé nabídky nebo skladové položky)
* Aktualizovat poskytovatele Azure k nastavení hodnoty *partner_id* na identifikátor GUID (nesmí před oprava identifikátor GUID s "pid-", stačí ji nastavit na skutečné GUID)

## <a name="create-guids"></a>Vytvořit GUID

Identifikátor GUID je jedinečný referenční číslo, které má 32 šestnáctkových číslic. Vytvořit GUID pro sledování, měli by používat GUID generator. Vytvořil tým Azure Storage [formátu identifikátoru GUID generátor](https://aka.ms/StoragePartners) , který bude e-mailem GUID ve správném formátu a lze opětovně použít napříč systémy různých sledování.

> [!Note]
> Důrazně doporučujeme použít [služby Azure Storage GUID generátor formuláře](https://aka.ms/StoragePartners) vytvořit váš identifikátor GUID. Další informace najdete v tématu naše [nejčastější dotazy k](#faq).

Doporučujeme že vytvořit jedinečný identifikátor GUID pro každou nabídku a distribuční kanál pro jednotlivé produkty. Můžete se rozhodnout použít jeden identifikátor GUID pro více distribučních kanálů produktu, pokud nechcete, aby vytváření sestav, který se má rozdělit.

Pokud nasadíte produktu pomocí šablony a je k dispozici na Azure Marketplace a na Githubu, můžete vytvořit a zaregistrovat 2 odlišné identifikátory GUID:

*   Produkt A na webu Azure Marketplace
*   Produkt A na Githubu

Vytváření sestav se provádí tak, že hodnota partnera (ID partnera společnosti Microsoft) a identifikátory GUID.

Můžete také sledovat identifikátory GUID na podrobnější úrovni jako SKU, ve kterém se tyto skladové položky varianty nabídky.

## <a name="register-guids-and-offers"></a>Zaregistrujte identifikátory GUID a nabídky

Identifikátory GUID musí být zaregistrovaná pro povolení attribution využití zákazníka.

Všechny registrace pro identifikátory GUID šablony se provádějí prostřednictvím Azure Marketplace Cloud Partner Portal (CPP).

Po přidání identifikátoru GUID šablony nebo uživatelský agent a zaregistrovat GUID v CPP, jsou sledovány všechna nasazení.

1. Platí pro [Azure Marketplace](https://aka.ms/listonazuremarketplace) a získejte přístup k CPP.

   * Partneři jsou potřeba [mají profil v CPP](https://docs.microsoft.com/azure/marketplace/become-publisher). Jste vyzkoušeli vypsat nabídky v Azure Marketplace nebo AppSource.
   * Partneři mohou registrovat více identifikátorů GUID.
   * Partneři mohou registrovat identifikátor GUID pro šablony mimo Marketplace řešení a nabídek.

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).

1. V pravém horním rohu vyberte ikonu vašeho účtu a pak vyberte **profilu vydavatele**.

   ![Vyberte profil vydavatele](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. Na **stránku profilu**vyberte **přidání sledování identifikátoru GUID.**

   ![Výběr možnosti Přidat sledování GUID](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. V **sledování GUID** zadejte vaše sledování identifikátor GUID. Zadejte jenom identifikátor GUID bez **pid -** předponu. V **vlastní popis** , zadejte název nabídky nebo popis.

   ![Stránka profilu](media/marketplace-publishers-guide/guid-dev-center-login.png)

   ![Zadejte identifikátor GUID a popis nabídky](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Pokud chcete registrovat více než jeden identifikátor GUID, vyberte **přidání identifikátoru GUID sledování** znovu. Na stránce se zobrazí další pole.

   ![Znovu vyberte Přidat sledování GUID](media/marketplace-publishers-guide/guid-dev-center-example-add.png)

   ![Zadejte jiný identifikátor GUID a popis nabídky](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Vyberte **Uložit**.

   ![Vyberte Uložit.](media/marketplace-publishers-guide/guid-dev-center-save.png)

Po přidání identifikátoru GUID šablony nebo uživatelský agent a zaregistrovat GUID v CPP, jsou sledovány všechna nasazení.

## <a name="verify-the-guid-deployment"></a>Ověření nasazení GUID

Po šablonu upravit a spustíte testovací nasazení, pomocí následujícího skriptu prostředí PowerShell k načtení prostředků, které jste nasadili a označené.

Skript můžete použít k ověření, že identifikátor GUID se úspěšně přidal do šablony Resource Manageru. Skript se nevztahuje na nasazení rozhraní API Resource Manageru.

Přihlaste se k Azure. Vyberte předplatné, které chcete ověřit před spuštěním skriptu pro nasazení pomocí. Spusťte skript nasazení v rámci předplatného.

**GUID** a **resourceGroup** název nasazení jsou povinné parametry.

Můžete získat [původní skript](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) na Githubu.

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

## <a name="report"></a>Nahlásit

Sestavy pro zákazníka attribution využití najdete na řídicím panelu Partner Center analyzovat. ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). Chcete-li zobrazit sestavu, budete muset k přihlášení použijte přihlašovací údaje partnerského centra. Pokud dojde k potížím s sestavy nebo přihlášení, vytvořte žádost o podporu podle pokynů v části Podpora Get.

Výběr šablony sledovány v rozevíracím seznamu Typ přidružení partnera zobrazíte sestavu.

![Sestavu využití attribution zákazníka](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Informovat vaše zákazníky

Partneři by měla být podkladem zákazníků o nasazení, která používají attribution využití zákazníka. Společnosti Microsoft sestavy využití Azure, který je spojen s Tato nasazení partnerovi. Následující příklady zahrnují obsah, který můžete použít k upozornění zákazníků o tato nasazení. V příkladech nahraďte \<partnera > s názvem vaší společnosti. Partneři musí Ujistěte se, že oznámení v souladu s jejich data zásady ochrany osobních údajů a kolekce, včetně možnosti pro zákazníky, kteří mají být vyloučeny z sledování.

### <a name="notification-for-resource-manager-template-deployments"></a>Oznámení pro nasazení šablony Resource Manageru

Při nasazení této šablony je schopen identifikovat instalaci Microsoft \<partnera > software s prostředky Azure, které jsou nasazeny. Společnost Microsoft si je mohla zjišťovat korelaci prostředky Azure, které se používají pro podporu tohoto softwaru. Společnost Microsoft shromažďuje tyto informace poskytnout nejlepší prostředí svoje produkty a provozovat svůj podnik. Data se shromažďují a řídí zásady ochrany osobních údajů společnosti Microsoft, které lze nalézt v https://www.microsoft.com/trustcenter.

### <a name="notification-for-sdk-or-api-deployments"></a>Oznámení pro nasazení sady SDK nebo rozhraní API

Při nasazení \<partnera > software Microsoft je schopen identifikovat instalaci \<partnera > software s prostředky Azure, které jsou nasazeny. Společnost Microsoft si je mohla zjišťovat korelaci prostředky Azure, které se používají pro podporu tohoto softwaru. Společnost Microsoft shromažďuje tyto informace poskytnout nejlepší prostředí svoje produkty a provozovat svůj podnik. Data se shromažďují a řídí zásady ochrany osobních údajů společnosti Microsoft, které lze nalézt v https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Získat podporu

Pokud narazíte na problémy s sestavy nebo se přihlaste do partnerského centra, vytvořte žádost o podporu s tým podpory partnerského centra tady: [https://partner.microsoft.com/en-US/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Pokud potřebujete pomoc pro Marketplace Onboarding a/nebo attribution využití zákazníka, postupujte podle těchto kroků.

1. Přejděte [stránku podpory](https://go.microsoft.com/fwlink/?linkid=844975).

1. V části **typ problému**vyberte **Marketplace připojování**.

1. Zvolte **kategorie** problému:

   - Přidružení problémy spotřeba energie, vyberte **jiných**.
   - Potíže s přístupem k s Azure Marketplace CPP, vyberte **potíže při přístupu k**.

     ![Vyberte kategorii problému](media/marketplace-publishers-guide/lu-article-incident.png)

1. Vyberte **požadavek na spuštění**.

1. Na další stránce zadejte požadované hodnoty. Vyberte **pokračovat**.

1. Na další stránce zadejte požadované hodnoty.

   > [!Important]
   > V **název incidentu** zadejte **sledování využití ISV**. Popište svůj problém podrobně.

   ![Název incidentu zadejte ISV sledování využití](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Vyplňte formulář a potom vyberte **odeslat**.

Můžete také získat technickou pomoc od Microsoft Partner technické konzultant pro technické předprodejní, nasazení a scénáře vývoje aplikací a začlenit attribution využití zákazníka.

### <a name="how-to-submit-a-technical-consultation-request"></a>Jak odeslat žádost o technickou konzultace

1. Navštivte [ https://aka.ms/TechnicalJourney ](https://aka.ms/TechnicalJourney).
1. Vyberte cloudové infrastruktury a správy a nová stránka se otevře umožní zobrazit technické cesty.
1. V rámci služby pro nasazení klikněte na Odeslat žádost tlačítka
1. Přihlaste se pomocí MSA (účtu MPN) nebo AAD (účtu řídicího panelu partnera); podle přihlašovacími údaji, otevře se formulář žádosti o online:
    * Dokončení/zkontrolujte kontaktní informace.
    * Podrobnosti o konzultaci může být předem vyplněný nebo vyberte z rozevírací nabídky.
    * Zadejte název a popis problému (zadejte co nejvíce podrobností nejvíce).
1. Klikněte na tlačítko Odeslat

Zobrazit podrobné pokyny včetně snímků obrazovky na [ https://aka.ms/TechConsultInstructions ](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Co se chystá

Vás bude kontaktovat partnera Microsoft technické konzultant nastavit volání k určení rozsahu vašim potřebám.

## <a name="faq"></a>Nejčastější dotazy

**Co je výhodou přidání identifikátoru GUID šablony?**

Společnost Microsoft poskytuje partnerům, kteří mají přehled zákaznická nasazení svého řešení a přehledy o jejich influenced využití. Microsoft a partnerem, můžete použít tyto informace k blíže zapojení mezi prodejní týmy. Microsoft a partnerem můžete data získat konzistentní zobrazení jednotlivých partnerů dopad na chod firmy na Azure růst.

**Po přidání identifikátoru GUID, můžete ho změnit?**

Ano, partner zákazníka nebo implementace může přizpůsobit šablonu a můžete změnit nebo odebrat identifikátor GUID. Doporučujeme vám, že partneři proaktivně popis role prostředků a identifikátor GUID pro jejich zákazníky a partnery, aby se zabránilo odstranění nebo úpravy na identifikátor GUID. Změna GUID ovlivní pouze nové, ne stávající nasazení a prostředky.

**Můžete sledovat nasazených z úložiště jiných společností než Microsoft, např. GitHub šablon?**

Ano, za předpokladu, identifikátor GUID je k dispozici při nasazení šablony, využití se sleduje. Partneři musí mít profil v CPP zaregistrovat GUID použitý pro nasazení mimo Azure Marketplace.

**Zákazník obdrží také vytváření sestav?**

Zákazníci můžou sledovat jejich používání jednotlivé prostředky nebo skupiny uživatelsky definovaných prostředků na webu Azure portal.

**Tento přístup se podobá k digitální DPOR Partner of Record ()?**

Tato nová metoda připojování nasazení a využití partnerské řešení poskytuje mechanismus pro propojení partnerského řešení pro využití Azure. Partnera DPOR slouží k přidružení konzultační (systémový integrátor) nebo partnera pro správu (Managed Service Provider) s předplatným Azure zákazníka.

**Co je výhoda pro použití služby Azure Storage GUID Generator formuláře?**

Azure Storage GUID Generator formuláře je zaručeno, že generování identifikátoru GUID požadovaný formát. Kromě toho pokud používáte některý z metody sledování rovina dat služby Azure Storage, můžete využívat stejný identifikátor GUID pro rovinu řízení Marketplace sledování. To umožňuje využívat jedinou jednotné identifikátor GUID pro partnera attribution aniž byste museli udržovat samostatných GUID.

**Můžete použít privátní, vlastní virtuálního pevného disku šablony nabídky řešení na webu Azure Marketplace?**

Ne, nemůžete. Image virtuálního počítače musí pocházet z Azure Marketplace, naleznete v tématu: [ https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines ](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

Můžete vytvořit nabídky virtuálních počítačů na marketplace pomocí vlastního virtuálního pevného disku a označte ji jako privátní, takže nikdo je můžou zobrazit. Potom odkaz na tento virtuální počítač do šablony řešení.

**Nepovedlo se aktualizovat *contentversion –* vlastnost hlavní šablony?**

Pravděpodobné chyby v některých případech při nasazení šablony pomocí TemplateLink z jiné šablony, které očekávají starší contentversion – z nějakého důvodu. Alternativním řešením je použití vlastnosti metadat:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
