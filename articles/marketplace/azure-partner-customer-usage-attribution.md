---
title: Azure attribution využití partnerů a zákazníků
description: Přehled o tom, jak sledovat využití ze strany zákazníků pro řešení Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: 95ad327380707dcfe14aa5aa3d91b8da2309eb05
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630887"
---
# <a name="azure-partner-customer-usage-attribution"></a>Attribution využití zákazníka partnerů Azure

Jako partner softwaru pro Azure, vaše řešení buď vyžadují komponenty Azure nebo k nasazení přímo na infrastruktuře Azure.  Ještě dnes když zákazník nasazuje partnerského řešení a zřizuje svoje vlastní prostředky Azure, je těžké partnera k získání přehledu na stav tohoto nasazení a obtížně získat optická dopad na Azure růstu. Přidání vyšší úroveň viditelnosti pomáhá partnerům bylo v souladu s prodejním týmům Microsoftu a získáte kredit ve výši programy partnera společnosti Microsoft.   

Microsoft je vytvoření nové metody pro partnerů lépe sledovat využití Azure, která je výsledkem zákaznických nasazení softwaru na Azure. Tato nová metoda vychází pomocí Azure Resource Manageru můžete orchestrovat nasazení služby Azure.

Jako partner Microsoftu můžete přidružit využití Azure s jakýmikoli prostředky Azure, které zřizujete jménem zákazníka.  Toto přidružení můžete to udělat pomocí webu Azure Marketplace, úložiště rychlý start, úložiště github privátní a i 1 na 1 customer engagement teprve.  Pokud chcete povolit sledování, existují dva přístupy k dispozici:

- Šablony Azure Resource Manageru: Šablon Azure Resource Manageru nebo šablony řešení k nasazení služeb Azure ke spuštění softwaru partnera. Partneři můžete vytvořit šablony Azure Resource Manageru, která definuje infrastrukturu a konfiguraci vašeho řešení Azure. Vytvoření šablony Azure Resource Manageru umožňuje vám a vašim zákazníkům nasazovat vaše řešení v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně. 

- Rozhraní API Azure Resource Manageru: partnery můžete volat rozhraní API Azure Resource Manageru přímo do buď nasazení šablony Azure Resource Manageru nebo ke generování rozhraní API volá přímo zřízení služby Azure. 

## <a name="method-1-azure-resource-manager-templates"></a>Metoda 1: Šablony Azure Resource Manageru 

Mnoho partnerská řešení jsou dnes nasazené na předplatné zákazníka pomocí šablon Azure Resource Manageru.  Pokud už šablony Azure Resource Manageru, která je k dispozici na webu Azure Marketplace, na Githubu nebo jako rychlý start, proces úpravy šablony pro povolení tato nová metoda sledování by měla být přímočaré.  Pokud nepoužíváte šablony Azure Resource Manageru ještě dnes, tady je pár odkazů, které vám pomůžou lépe porozumět šablon Azure Resource Manageru a jak ji vytvořit: 

*   [Vytvoření a nasazení první šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Průvodce Vytvořit šablonu řešení pro Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>Návod: Přidání identifikátor GUID do stávající šablony Azure Resource Manageru

Přidání identifikátoru GUID je jeden úprav souboru hlavní šablony:
 1. Vytvořit identifikátor GUID, Řekněme, že je generovaná hodnota eb7927c8 dd66-43e1-b0cf-c346a422063
 2. Otevřete šablonu Azure Resource Manageru
 3. Přidáte nový prostředek v souboru hlavní šablony. Prostředek pouze musí být v mainTemplate.json nebo azuredeploy.json není v žádném vnořené nebo propojené šablony.
 4. Zadejte identifikátor GUID po "pid-", jak je znázorněno výše.

   Ji by měl vypadat přibližně jako v tomto příkladu: `pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. Zkontrolujte šablonu pro všechny chyby
 6. Znovu publikovat šablony v příslušné úložiště

## <a name="sample-template-code"></a>Vzorový kód šablony

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG?token=Ak8ZDB0JzsBdUGlKEIeHNJRS7b0BWn4Gks5bbMwwwA%3D%3D)


## <a name="method-2-azure-resource-manager-apis"></a>Metoda 2: Rozhraní API Azure Resource Manageru

V některých případech můžete chtít volat přímo proti rozhraní REST API Azure Resource Manageru k nasazení služby Azure. [Azure podporuje více sad SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) aby to bylo.  Můžete použít jednu ze sad SDK nebo volání rozhraní REST API přímo k nasazení prostředků.

Pokud používáte šablonu Azure Resource Manageru, by mělo být označení řešení podle výše uvedených pokynů.  Pokud nejsou pomocí šablony Azure Resource Manageru a vytvoření přímého volání rozhraní API můžete označit stále nasazení tak, aby přidružit využití prostředků Azure. 

**Jak označit nasazení pomocí rozhraní API Azure Resource Manageru:** pro tento přístup při navrhování volání rozhraní API v hlavičce uživatelského agenta v požadavku bude obsahovat identifikátor GUID. Měli byste přidat identifikátor GUID pro jednotlivé nabídky nebo SKU.  Řetězec muset být naformátovaná za použití předponu pid – a poté zahrnout partnera vygenerovat identifikátor GUID.   

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG?token=Ak8ZDDiokRcj4PJj0aMkZmfF8BdOuOTzks5bbM35wA%3D%3D)

>[!Note] 
>Formát identifikátoru GUID pro vložení do uživatelský agent: pid-eb7927c8-dd66-43e1-b0cf-c346a422063 / / zadejte vaše GUID po "pid-"

Formát řetězce je důležité. Pokud není zahrnutý předponu "pid-", není možné zadávat dotazy na data. Různé sady SDK to udělali jinak.  Implementace této metody, je potřeba zkontrolovat podpory a přístup pro upřednostňované sady Azure SDK. 

**Příklad pomocí sady Python SDK:** pro Python, budete muset použít atribut "konfigurace". Můžete přidat pouze do UserAgent. Zde naleznete příklad:

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/python-for-lu.PNG?token=Ak8ZDK5Um4J6oY-7x25tuBpa168BEiYMks5bbMuUwA%3D%3D)

>To je třeba provést pro každého klienta, neexistuje žádné globální statické konfigurace (můžete provést objektu factory klienta k Ujistěte se, že každý klient dělá. 
>[Další referenční informace](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

Jak označit nasazení pomocí prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure: Pokud nasadíte prostředkům přes AzurePowerShell, můžete připojit váš identifikátor GUID pomocí následující metody:

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

Při použití rozhraní příkazového řádku Azure, připojit vaše identifikátor GUID, nastavte proměnnou prostředí AZURE_HTTP_USER_AGENT.  Tuto proměnnou lze nastavit v rámci oboru skriptu nebo nastavit globálně, pro použití oboru prostředí:

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>Registrace identifikátory GUID a nabídky

Aby GUID, které mají být zahrnuty v našich sledování je nutné jej zaregistrovat.  

Všechny registrace pro identifikátory GUID šablony se provést prostřednictvím Azure Marketplace Cloud Partner Portal (CPP). 

Platí pro [Azure Marketplace](http://aka.ms/listonazuremarketplace) dnes a získejte přístup k portálu Cloud Partner.

*   Partneři budou muset [mají profil v CPP](https://docs.microsoft.com/azure/marketplace/become-publisher) a podporovat vypsat nabídky v Azure Marketplace nebo AppSource 
*   Partneři budou moci zaregistrovat více identifikátorů GUID 
*   Partneři budou moci zaregistrovat GUID šablony a nabídky řešení mimo Marketplace

Po přidání identifikátoru GUID šablony, nebo uživatelský agent a zaregistrován identifikátoru GUID v CPP všechna nasazení se bude sledovat. 

## <a name="verification-of-guid-deployment"></a>Ověření nasazení GUID 

Po úpravě vaší šablony a provést testovací nasazení, můžete použít následující skript prostředí PowerShell k načtení prostředků nasadili a označí. 

Slouží k ověření, pokud identifikátor GUID je přidaný do šablony Azure Resource Manageru úspěšně. Nevztahuje se na nasazení rozhraní API Azure Resource Manageru.

Připojte se k Azure a vyberte předplatné, které obsahuje, kterou chcete ověřit před spuštěním skriptu nasazení. Musí se spustit nasazení v rámci předplatného.

Identifikátor GUID a skupiny prostředků název nasazení jsou povinné parametry.

Můžete najít původní skript [tady](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1).

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>Pokyny k vytváření identifikátory GUID

Identifikátor GUID (globálně jedinečný identifikátor) je jedinečný referenční číslo 32 šestnáctkových číslic. Pokud chcete vytvořit identifikátor GUID, používali GUID generator k vytváření identifikátorů GUID pro účely sledování.  Existuje více [online GUID generátorů](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E) můžete použít.

Můžete se doporučuje vytvořit jedinečný identifikátor GUID pro každou nabídku a distribuční kanál.  Například pokud máte obě tato řešení a oba jsou nasazené prostřednictvím šablony a jsou k dispozici v Azure Marketplace a na Githubu.  Vytvořte čtyři GUID:

*   Nabídky A webu Azure Marketplace 
*   Nabídky A na Githubu
*   Nabídky B v Azure Marketplace 
*   Nabídky B na Githubu

Vytváření sestav se provede tak, že partner (ID partnera společnosti Microsoft) a identifikátor GUID. 

Můžete také sledovat identifikátory GUID na podrobnější úrovni tedy SKU (kde skladové položky jsou varianty nabídky).

## <a name="guidance-on-privacy-and-data-collection"></a>Pokyny pro ochranu osobních údajů a shromažďování dat

Partneři by měla poskytnout zasílání zpráv pro své zákazníky, které nasazení, které zahrnují sledování identifikátor GUID správce prostředků Azure vám umožní Microsoftu k hlášení využití Azure spojené s těchto nasazení partnera.  Některé jazykové příkladu jsou uvedeny níže. Pokud znamená "PARTNER" byste měli vyplnit název společnosti. Kromě toho partneři byste se ujistit, že jazyk v souladu s vlastními daty zásady ochrany osobních údajů a kolekci včetně možností pro zákazníky, kteří mají být vyloučeny z track: 

**Pro nasazení šablony Azure Resource Manageru**

Při nasazování této šablony, Microsoft bude možné identifikovat instalace PARTNERSKÝ software s prostředky Azure, které jsou nasazené.  Microsoft bude možnost provést korelaci prostředky Azure používané pro podporu tohoto softwaru.  Společnost Microsoft shromažďuje tyto informace poskytnout nejlepší prostředí svoje produkty a provozovat svůj podnik. Tato data se shromažďují a řídí zásady ochrany osobních údajů společnosti Microsoft, které lze nalézt v https://www.microsoft.com/trustcenter. 

**Pro nasazení sady SDK nebo rozhraní API**

Při nasazení softwaru partnera, Microsoft bude možné Identifikujte instalace PARTNERSKÝ software s prostředky Azure, které jsou nasazené.  Microsoft bude možnost provést korelaci prostředky Azure používané pro podporu tohoto softwaru.  Společnost Microsoft shromažďuje tyto informace poskytnout nejlepší prostředí svoje produkty a provozovat svůj podnik. Tato data se shromažďují a řídí zásady ochrany osobních údajů společnosti Microsoft, které lze nalézt v https://www.microsoft.com/trustcenter.

## <a name="support"></a>Podpora

Potřebujete pomoc, postupujte následujících kroků:
 1. Navštivte stránku podpory umístění [go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975)
 2. Problémy s využití přidružení – vyberte typ problému: **Marketplace připojování** a kategorie: **Další** a potom klikněte na tlačítko **začít požadavek.** 
>[!Note]
>Pro problémy s přístupem k Azure Marketplace portál Cloud Partner – vyberte typ problému: **Marketplace připojování** a kategorie: **potíže při přístupu k** a potom klikněte na tlačítko **začít požadavek.**
 3. Vyplňte požadovaná pole na další stránce a klikněte na tlačítko **pokračovat.**
 4. Vyplňte pole volný text na další stránku.  
 

 
>[!Important] 
>Vyplňte název incidentu s **"Sledování využití ISV"** a popište svůj problém podrobně v poli velký volný text po.  Dokončete formuláři a klikněte na tlačítko **odeslat**.

## <a name="faqs"></a>Nejčastější dotazy

**Co je výhodou přidání identifikátoru GUID šablony?**

Microsoft bude poskytovat partnerům, kteří mají zobrazení zákaznických nasazeních svoje šablony a přehledy o jejich influenced využití.  Microsoft a partnerem můžete také tyto informace k blíže zapojení mezi prodejních týmů. Microsoft a partnerem také slouží k získání konzistentnější zobrazení jednotlivých partnerů dopad na chod firmy na Azure růst. 

**Identifikátor GUID, můžete přidat do šablony?**

Sledování prostředků je určený pro připojení partnerské řešení zákazníkům Azure využití.  Využití dat se váže k identitě partnera Microsoft Partner Network (MPN ID) a vytváření sestav bude k dispozici pro partnery v portál Cloud Partner (CPP).  

**Po přidání identifikátoru GUID je možné změnit?**
 
Ano, partner zákazníka nebo implementace může přizpůsobit šablonu a může změnit nebo odebrat identifikátor GUID. Doporučujeme vám, že partneři proaktivně popis role prostředků a identifikátor GUID pro jejich zákazníky a partnery, abyste zabránili odstranění nebo úpravy sledování identifikátor GUID.  Změna GUID ovlivní pouze nové, ne stávající nasazení a prostředky.

**Při vytváření sestav bude k dispozici?**

Beta verze generování sestav by měla brzy dostupné.  Vytváření sestav bude možné integrovat do portálu Cloud Partner (CPP).

**Můžete sledovat nasazených z úložiště jiných společností než Microsoft, např. GitHub šablon?**

Ano, se jako identifikátor GUID je k dispozici při nasazení šablony, bude sledovat využití.  
Partneři musí mít profil v portál partnerů cloudu k registraci související šablony publikovaných mimo Azure Marketplace. 

**Pokud nasazení šablony z Azure Marketplace a jiných úložišť jako GitHub je nějaký rozdíl?**

Ano, partneři, kteří publikování nabídek na webu Azure Marketplace obdržet podrobné údaje o nasazení z webu Azure Marketplace.  Partneři, kteří budou těží z vystavení jejich nabídek zveřejněných na portálu Azure Marketplace a na portálu Azure management portal zákazníkům. Nabídky Azure Marketplace také generovat zájemce partnera.

**Co když mám vytvořit vlastní šablonu pro jednotlivé customer engagement?**

Určitě stále můžete do šablony přidat identifikátor GUID.  Pokud používáte existující identifikátor GUID, který byl zaregistrován, ji bude součástí vytváření sestav.  Pokud vytvoříte nový identifikátor GUID, budou muset zaregistrovat nový identifikátor GUID její součástí sledování.

**Zákazník obdrží také vytváření sestav?**

Zákazníci mají nyní možnost sledovat jejich používání jednotlivé prostředky nebo skupiny prostředků zákazníkem definovaná v rámci portálu pro správu Azure.   

**Tato metoda sledování se podobá k digitální DPOR Partner of Record ()?**

Tato nová metoda připojování nasazení a využití na řešení partnera slouží poskytují mechanismus pro propojení partnerského řešení pro využití Azure.  Partnera DPOR slouží k přidružení konzultační (systémový integrátor) nebo partnera pro správu (Managed Service Provider) s předplatným Azure zákazníka.   
