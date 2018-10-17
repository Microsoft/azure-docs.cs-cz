---
title: Azure attribution využití partnerů a zákazníků
description: Přehled o tom, jak sledovat využití ze strany zákazníků pro řešení Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: yijenj
ms.openlocfilehash: a0b3c220a1cd857bc8bea0eb5ab41625845fcc5d
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365613"
---
# <a name="azure-partner-customer-usage-attribution"></a>Attribution využití zákazníka partnerů Azure

Jako partner softwaru pro Azure vaše řešení nevyžaduje komponenty Azure nebo, je nutné nasadit přímo na infrastruktuře Azure. Zákazníci, kteří nasazení partnerského řešení a vytvořit svoje vlastní prostředky Azure můžete těžké získat přehled o stavu nasazení a získat optická dopad na Azure růst. Když přidáte vyšší úroveň viditelnosti, bylo v souladu s prodejním týmům Microsoftu a získáte kredit ve výši programy partnera společnosti Microsoft.   

Microsoft teď nabízí způsob, jak partnerů lépe sledovat využití Azure pro zákaznická nasazení softwaru na Azure. Nová metoda používá Azure Resource Manageru můžete orchestrovat nasazení služby Azure.

Jako partner Microsoftu můžete přidružit využití Azure s jakýmikoli prostředky Azure, které zřizujete jménem zákazníka. Můžete formulář přidružení prostřednictvím Azure Marketplace, rychlý start úložiště, privátní úložiště GitHub a zapojení zákazníků od vyhrazeného pracovníka. Pokud chcete povolit sledování, jsou k dispozici dva přístupy:

- Šablony Azure Resource Manageru: šablony Resource Manageru nebo šablony řešení k nasazení služeb Azure ke spuštění softwaru partnera. Partneři můžete vytvořit šablonu Resource Manageru pro definování infrastrukturu a konfiguraci jejich řešení Azure. Šablony Resource Manageru umožňuje vám a vašim zákazníkům k nasazení svého řešení v průběhu životního cyklu. Máte jistotu, že se prostředky nasadí konzistentně. 

- Rozhraní API Azure Resource Manageru: Partneři mohou volat rozhraní API Resource Manageru přímo k nasazení šablony Resource Manageru nebo ke generování volání rozhraní API přímo zřídit služby Azure. 

## <a name="use-resource-manager-templates"></a>Použití šablon Resource Manageru

Mnoho partnerská řešení jsou nasazené na předplatné zákazníka pomocí šablon Resource Manageru. Pokud máte šablony Resource Manageru, který je dostupný v Tržišti Azure Marketplace, na Githubu nebo jako rychlém startu procesu úpravy šablony pro povolení nový způsob sledování by měl být přímočaré. Pokud nechcete použít šablony Azure Resource Manageru, tady je pár odkazů, které pomůžou lépe pochopit šablon Resource Manageru a jak ji vytvořit: 

*   [Vytvoření a nasazení první šablony Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Vytvořte šablonu řešení pro Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="add-a-guid-to-your-template"></a>Přidat identifikátor GUID do šablony

Chcete-li přidat globálně jedinečný identifikátor (GUID), provedete jeden úpravy souboru hlavní šablony:

1. [Vytvořit GUID](#create-guids) (například eb7927c8 dd66-43e1-b0cf-c346a422063) a [zaregistrovat GUID](#register-guids-and-offers).

1. Otevřete šablonu Resource Manageru.

1. Přidáte nový prostředek v souboru hlavní šablony. Prostředek musí být v **mainTemplate.json** nebo **azuredeploy.json** soubor pouze a ne v žádné vnořené nebo propojené šablony.

1. Zadejte hodnotu GUID po **pid -** předpona (například pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Zkontrolujte šablonu pro všechny chyby.

1. Znovu publikujte šablony v příslušné úložiště.

1. [Ověření úspěšné identifikátor GUID v nasazení šablony](#verify-the-guid-deployment).

### <a name="sample-template-code"></a>Vzorový kód šablony

![Vzorový kód šablony](media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG)

## <a name="use-the-resource-manager-apis"></a>Použití rozhraní API Resource Manageru

V některých případech můžete chtít volat přímo proti rozhraní REST API Resource Manageru k nasazení služby Azure. [Azure podporuje více sad SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) umožňující těchto volání. Můžete použít jednu ze sad SDK nebo volání rozhraní REST API přímo k nasazení prostředků.

Pokud používáte šablonu Resource Manageru, by mělo být označení řešení podle pokynů, jak je popsáno výše. Pokud nepoužíváte šablony Resource Manageru a vytvoření přímého volání rozhraní API, můžete stále označit nasazení tak, aby přidružit využití prostředků Azure. 

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Označení nasazení pomocí rozhraní API Resource Manageru

Pro tento přístup sledování zahrnout při návrhu volání rozhraní API, identifikátor GUID hlavičky uživatelského agenta v požadavku. Přidejte identifikátor GUID pro každou nabídku nebo SKU. Formátovací řetězec se **pid -** Předpona a zahrnují GUID generované partnera. Tady je příklad formát identifikátoru GUID pro vložení do uživatelského agenta: 

![Příklad formátu identifikátoru GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Formát řetězce je důležité. Pokud **pid -** předpona není zahrnut, není možné zadávat dotazy na data. Různé sady SDK sledovat odlišně. Tuto metodu implementovat, najdete v tématu podporu a sledování přístupu pro vaše preferované Azure SDK. 

### <a name="example-the-python-sdk"></a>Příklad: Python SDK

Pro Python, použijte **config** atribut. Atribut lze přidat pouze do UserAgent. Tady je příklad:

![Přidat atribut pro uživatelského agenta](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Přidejte atribut pro každého klienta. Neexistuje žádné globální statické konfigurace. Může být označení objektu factory klienta k Ujistěte se, že se ke sledování každého klienta. Další informace najdete v tomto [klienta factory ukázka na Githubu](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Označení nasazení pomocí Azure Powershellu

Pokud provádíte nasazení prostředků pomocí Azure Powershellu, připojte vaši GUID pomocí následující metody:

```
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Označení nasazení pomocí rozhraní příkazového řádku Azure

Při použití rozhraní příkazového řádku Azure k připojení vašeho identifikátor GUID, nastavte **AZURE_HTTP_USER_AGENT** proměnné prostředí. Tuto proměnnou lze nastavit v rámci oboru skriptu. Můžete také nastavit proměnnou globálně pro obor prostředí:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

## <a name="create-guids"></a>Vytvořit GUID

Identifikátor GUID je jedinečný referenční číslo, které má 32 šestnáctkových číslic. Vytvořit GUID pro sledování, měli by používat GUID generator. Doporučuje se, že využijete [služby Azure Storage GUID generátor formuláře](https://aka.ms/StoragePartners). Ale pokud nechcete používat Azure Storage GUID generator, jsou více [online GUID generátorů](https://www.bing.com/search?q=guid%20generator) , který vám pomůže.

> [!Note]
> Je důrazně doporučujeme použít [služby Azure Storage GUID generátor formuláře](https://aka.ms/StoragePartners) vytvořit váš identifikátor GUID. Další informace najdete v tématu naše [nejčastější dotazy k](#faq).

Vytvořte jedinečný GUID pro každou nabídku a distribuční kanál. Pokud nasadíte dvě řešení pomocí šablony a každý z nich je k dispozici na webu Azure Marketplace a na Githubu, je třeba vytvořit čtyři GUID:

*   Nabídky A webu Azure Marketplace 
*   Nabídky A na Githubu
*   Nabídky B v Azure Marketplace 
*   Nabídky B na Githubu

Vytváření sestav se provádí tak, že hodnota partnera (ID partnera společnosti Microsoft) a identifikátor GUID. 

Můžete také sledovat identifikátory GUID na podrobnější úrovni jako SKU, ve kterém se tyto skladové položky varianty nabídky.

## <a name="register-guids-and-offers"></a>Zaregistrujte identifikátory GUID a nabídky

Zahrnout identifikátor GUID v našich sledování, musí být zaregistrovaný identifikátor GUID.  

Všechny registrace pro identifikátory GUID šablony se provádějí prostřednictvím Azure Marketplace Cloud Partner Portal (CPP). 

Po přidání identifikátoru GUID šablony nebo uživatelský agent a zaregistrovat GUID v CPP, jsou sledovány všechna nasazení. 

1. Platí pro [Azure Marketplace](http://aka.ms/listonazuremarketplace) a získejte přístup k CPP.

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

```
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="notify-your-customers"></a>Informovat vaše zákazníky

Partneři by měla být podkladem zákazníků o nasazení, která používají sledování identifikátor GUID správce prostředků. Společnosti Microsoft sestavy využití Azure, který je spojen s Tato nasazení partnerovi. Následující příklady zahrnují obsah, který můžete použít k upozornění zákazníků o tato nasazení. V příkladech nahraďte \<partnera > s názvem vaší společnosti. Partneři musí Ujistěte se, že oznámení v souladu s jejich data zásady ochrany osobních údajů a kolekce, včetně možnosti pro zákazníky, kteří mají být vyloučeny z sledování. 

### <a name="notification-for-resource-manager-template-deployments"></a>Oznámení pro nasazení šablony Resource Manageru

Při nasazení této šablony je schopen identifikovat instalaci Microsoft \<partnera > software s prostředky Azure, které jsou nasazeny. Společnost Microsoft si je mohla zjišťovat korelaci prostředky Azure, které se používají pro podporu tohoto softwaru. Společnost Microsoft shromažďuje tyto informace poskytnout nejlepší prostředí svoje produkty a provozovat svůj podnik. Data se shromažďují a řídí zásady ochrany osobních údajů společnosti Microsoft, které lze nalézt v https://www.microsoft.com/trustcenter. 

### <a name="notification-for-sdk-or-api-deployments"></a>Oznámení pro nasazení sady SDK nebo rozhraní API

Při nasazení \<partnera > software Microsoft je schopen identifikovat instalaci \<partnera > software s prostředky Azure, které jsou nasazeny. Společnost Microsoft si je mohla zjišťovat korelaci prostředky Azure, které se používají pro podporu tohoto softwaru. Společnost Microsoft shromažďuje tyto informace poskytnout nejlepší prostředí svoje produkty a provozovat svůj podnik. Data se shromažďují a řídí zásady ochrany osobních údajů společnosti Microsoft, které lze nalézt v https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Získat podporu

Pokud potřebujete pomoc, postupujte podle těchto kroků.

1. Přejděte [stránku podpory](https://go.microsoft.com/fwlink/?linkid=844975). 

1. V části **typ problému**vyberte **Marketplace připojování**.

1. Zvolte **kategorie** problému:

   - Přidružení problémy spotřeba energie, vyberte **jiných**.
   - Potíže s přístupem k s Azure Marketplace CPP, vyberte **potíže při přístupu k**.
   
    ![Vyberte kategorii problému](media/marketplace-publishers-guide/lu-article-incident.png)

1. Vyberte **požadavek na spuštění**.

1. Na další stránce zadejte požadované hodnoty. Vyberte **Pokračovat**.

1. Na další stránce zadejte požadované hodnoty.

   > [!Important] 
   > V **název incidentu** zadejte **sledování využití ISV**. Popište svůj problém podrobně.
   
   ![Název incidentu zadejte ISV sledování využití](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Vyplňte formulář a potom vyberte **odeslat**.

## <a name="faq"></a>Nejčastější dotazy

**Co je výhodou přidání identifikátoru GUID šablony?**

Společnost Microsoft poskytuje partnerům, kteří mají zobrazení zákaznických nasazeních svoje šablony a přehledy o jejich influenced využití. Microsoft a partnerem, můžete použít tyto informace k blíže zapojení mezi prodejní týmy. Microsoft a partnerem můžete data získat konzistentní zobrazení jednotlivých partnerů dopad na chod firmy na Azure růst. 

**Identifikátor GUID, můžete přidat do šablony?**

Sledování prostředků je určený pro připojení partnerské řešení pro využití Azure zákazníka. Využití dat se váže k identitě partnera Microsoft Partner Network (MPN ID). Vytváření sestav je k dispozici pro partnery v CPP.

**Po přidání identifikátoru GUID, můžete ho změnit?**
 
Ano, partner zákazníka nebo implementace může přizpůsobit šablonu a můžete změnit nebo odebrat identifikátor GUID. Doporučujeme vám, že partneři proaktivně popis role prostředků a identifikátor GUID pro jejich zákazníky a partnery, abyste zabránili odstranění nebo úpravy sledování identifikátor GUID. Změna GUID ovlivní pouze nové, ne stávající nasazení a prostředky.

**Při vytváření sestav bude k dispozici?**

Beta verze generování sestav by měla brzy dostupné. Vytváření sestav, bude se integrovat do CPP.

**Můžete sledovat nasazených z úložiště jiných společností než Microsoft, např. GitHub šablon?**

Ano, za předpokladu, identifikátor GUID je k dispozici při nasazení šablony, využití se sleduje. Partneři musí mít profil v CPP registrace související šablony, které jsou publikovány mimo Azure Marketplace. 

**Pokud nasazení šablony z Azure Marketplace a jiných úložišť jako GitHub je nějaký rozdíl?**

Ano, partneři, kteří publikování nabídek na webu Azure Marketplace, může zobrazit podrobné údaje o nasazení z Azure Marketplace. Partneři, kteří těží z vystavení jejich nabídek zveřejněných na zákazníky na portálu Azure Marketplace a na webu Azure Portal. Nabídky na webu Azure Marketplace také generovat zájemce partnera.

**Co když mám vytvořit vlastní šablonu pro jednotlivé customer engagement?**

Si stále klidně můžete do šablony přidat identifikátor GUID. Pokud používáte existující identifikátor GUID registrované, je součástí generování sestav. Pokud vytvoříte nový identifikátor GUID, budete muset zaregistrovat nový identifikátor GUID jeho součástí sledování.

**Zákazník obdrží také vytváření sestav?**

Zákazníci můžou sledovat jejich používání jednotlivé prostředky nebo skupiny uživatelsky definovaných prostředků na webu Azure portal.   

**Tato metoda sledování se podobá k digitální DPOR Partner of Record ()?**

Tato nová metoda připojování nasazení a využití partnerské řešení poskytuje mechanismus pro propojení partnerského řešení pro využití Azure. Partnera DPOR slouží k přidružení konzultační (systémový integrátor) nebo partnera pro správu (Managed Service Provider) s předplatným Azure zákazníka.   

**Co je výhoda pro použití služby Azure Storage GUID Generator formuláře?**

Azure Storage GUID Generator formuláře je zaručeno, že generování identifikátoru GUID požadovaný formát. Kromě toho pokud používáte některý z metody sledování rovina dat služby Azure Storage, můžete využívat stejný identifikátor GUID pro rovinu řízení Marketplace sledování. To umožňuje využívat jedinou jednotné identifikátor GUID pro partnera attribution aniž byste museli udržovat samostatných GUID.
