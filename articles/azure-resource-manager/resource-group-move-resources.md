---
title: Přesunout prostředky Azure pro nové předplatné nebo skupinu prostředků | Dokumentace Microsoftu
description: Použití Azure Resource Manageru k přesunutí prostředků do nové skupiny prostředků nebo předplatného.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: tomfitz
ms.openlocfilehash: 35bd895636bcedf0fd3fad073819d238c7850326
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783334"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Přesunutí prostředků do nové skupiny prostředků nebo předplatného

Tento článek ukazuje, jak přesunutí prostředků do nové předplatné nebo novou skupinu prostředků ve stejném předplatném. Můžete se přesunout prostředek na portálu, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API. Operace přesunu v tomto článku jsou k dispozici, bez jakékoli pomoc od podpory Azure.

Při přesouvání prostředků, zdrojová skupina a cílová skupina jsou zamčené během operace. Zápis a odstranění operace jsou blokovány o skupinách prostředků, až do dokončení přechodu. Tento Zámek znamená, že nelze přidat, aktualizovat nebo odstranit prostředky ve skupinách prostředků, ale neznamená, že prostředky jsou zmražená. Například při přesunutí serveru SQL Server a jeho databázi do nové skupiny prostředků, aplikace, která používá databázi prostředí bez výpadků. Můžete nadále číst a zapisovat do databáze.

Nelze změnit umístění prostředku. Přesunutí prostředku pouze přesune do nové skupiny prostředků. Nová skupina prostředků může mít jiné umístění, ale to se nemění umístění prostředku.

> [!NOTE]
> Tento článek popisuje, jak přesunout prostředky v rámci Azure existující účet nabídky. Pokud skutečně chcete změnit svého účtu Azure (například upgrade z modelu průběžné platby předem zaplatit) nabízí přitom dál fungují se stávajícími prostředky, najdete v článku [přepnout vašeho předplatného Azure na jinou nabídku](../billing/billing-how-to-switch-azure-offer.md).
>
>

## <a name="checklist-before-moving-resources"></a>Kontrolní seznam před přesunutím prostředků

Před přesunutím prostředku je nutné provést několik důležitých kroků. Ověřením těchto podmínek se můžete vyhnout chybám.

1. Zdrojové a cílové předplatné musí existovat v rámci stejného [tenanta Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Pokud chcete zkontrolovat, že oba odběry obsahují stejné ID tenanta, pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure.

  Pro prostředí Azure PowerShell použijte:

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Pokud používáte Azure CLI, použijte:

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  Pokud ID tenantů pro zdrojové a cílové předplatné nejsou stejné, použijte následující metody k odsouhlasení ID tenantů:

  * [Přenos vlastnictví předplatného služby Azure na jiný účet](../billing/billing-subscription-transfer.md)
  * [Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Cílové předplatné musí být registrováno pro poskytovatele přesouvaného prostředku. Pokud ne, zobrazí chybová zpráva oznamující, že **předplatné není zaregistrované pro typ prostředku**. K problému může dojít, pokud přesouváte prostředek do nového předplatného, ale toto předplatné nebylo pro příslušný typ prostředku nikdy použito.

  Pokud používáte PowerShell, použijte následující příkazy získat stav registrace:

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Zaregistrovat poskytovatele prostředků, použijte:

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Azure CLI použijte následující příkazy se získat stav registrace:

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  Zaregistrovat poskytovatele prostředků, použijte:

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

1. Účet přesunutí prostředků musí mít alespoň následující oprávnění:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** na zdrojovou skupinu prostředků.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** na cílová skupina prostředků.

1. Před přesunutím prostředků, zkontrolujte kvóty předplatného pro předplatné, které přesouváte prostředky, které. Přesunutí prostředků znamená, že předplatné překročí maximum, budete muset zkontrolovat, jestli můžete požádat o zvýšení kvóty. Seznam omezení a tom, jak požádat o zvýšení najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).

1. Pokud je to možné, přerušení velké přesune do operací přesunu samostatných. Resource Manager okamžitě nezdaří pokusy o přesunutí více než 800 prostředků v rámci jedné operace. Ale přesun prostředků menší než 800 selhat také podle vypršení časového limitu.

1. Služba musí umožňovat operaci přesouvání prostředků. Chcete-li zjistit, zda bude přesunutí úspěšné, [ověřit vaši žádost o přesunutí](#validate-move). Viz níže uvedené části v tomto článku [služby umožňují přesunutí prostředků](#services-that-can-be-moved) a které [není služby umožňují přesunutí prostředků](#services-that-cannot-be-moved).

## <a name="when-to-call-support"></a>Kdy se má zavolat na podporu

Většinu prostředků prostřednictvím samoobslužné operace uvedené v tomto článku můžete přesunout. Použijte samoobslužné operace pro:

* Přesouvání prostředků Resource Manageru
* Přesouvání klasických prostředků podle [omezeními klasického nasazení](#classic-deployment-limitations).

Kontakt [podporují](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) když potřebujete:

* Prostředky přesunout do nového účtu Azure (a tenanta Azure Active Directory) a potřebujete pomoc s pokyny uvedenými v předchozí části.
* Přesouvání klasických prostředků, ale máte potíže s omezeními.

## <a name="validate-move"></a>Ověří přesun

[Ověření operace přesunu](/rest/api/resources/resources/validatemoveresources) umožňuje testovat váš scénář přesunu bez skutečně přesouvání prostředků. Pomocí této operace můžete zjistit, pokud bude úspěšné přesunutí. K provedení této operace, je nutné:

* Název zdrojové skupiny prostředků
* ID prostředku cílová skupina prostředků
* ID prostředku každého prostředku přesunout
* [přístupový token](/rest/api/azure/#acquire-an-access-token) pro váš účet

Odeslání žádosti o následující:

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

S hlavní část žádosti:

```json
{
 "resources": ['<resource-id-1>', '<resource-id-2>'],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Pokud je požadavek správně naformátován, operace vrátí:

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 stavový kód označuje byla přijata žádost o ověření, ale ještě nebyl určen, pokud bude úspěšné operaci přesunutí. `location` Hodnota obsahuje adresu URL, kterou lze použít ke kontrole stavu dlouhotrvající operace.  

Pokud chcete zkontrolovat stav, odešlete požadavek na následující:

```
GET <location-url>
Authorization: Bearer <access-token>
```

Během operace je stále spuštěna, můžete nadále přijímat 202 stavový kód. Počkejte, počet sekund, které jsou uvedené v `retry-after` hodnotu než to zkusíte znovu. Pokud operace přesunu ověří úspěšně, obdržíte 204 stavový kód. Pokud selže ověření přesunu, obdržíte chybovou zprávu, jako například:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="services-that-can-be-moved"></a>Služby, které je možné přesunout

Služby, které umožňují přesun na novou skupinu prostředků a předplatném jsou:

* Analysis Services
* API Management
* Aplikace služby App Service (webové aplikace) – viz [omezení App Service](#app-service-limitations)
* Certifikáty App Service
* Application Insights
* Automation
* Azure Active Directory B2C
* Azure Cosmos DB
* Azure Maps
* Azure Relay
* Azure Stack – registrace
* Batch
* BizTalk Services
* Bot Service
* CDN
* Cloudové služby - viz [omezení klasického nasazení](#classic-deployment-limitations)
* Cognitive Services
* Container Registry
* Content Moderator
* Cost Management
* Customer Insights
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Grid
* Event Hubs
* Clustery HDInsight – viz [omezení HDInsight](#hdinsight-limitations)
* Iot Central
* IoT Huby
* Key Vault
* Nástroje pro vyrovnávání zatížení – viz [omezení nástroje pro vyrovnávání zatížení](#lb-limitations)
* Log Analytics
* Logic Apps
* Machine Learning – Machine Learning Studio webové služby je možné přesunout do skupiny prostředků ve stejném předplatném, ale jiné předplatné. Další prostředky služby Machine Learning je možné přesunout mezi předplatnými.
* Spravovaná identita - přiřazená uživatelem
* Media Services
* Mobile Engagement
* Notification Hubs
* Operational Insights
* Správa operací
* Portálu řídicích panelů
* Power BI – jak Power BI Embedded a Power BI pracovního prostoru kolekce
* Veřejná IP adresa – viz [omezení veřejné IP adresy](#pip-limitations)
* Redis Cache
* Scheduler
* Search
* Service Bus
* Service Fabric
* Service Fabric sítě
* Služby SignalR
* Úložiště
* Úložiště (classic) – viz [omezení klasického nasazení](#classic-deployment-limitations)
* Stream Analytics – Stream Analytics úlohy nelze přesunout, při spuštění ve stavu.
* Server služby SQL Database – databáze a serveru se musí nacházet ve stejné skupině prostředků. Přesunete-li SQL server, přesunou také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure SQL Data Warehouse.
* Time Series Insights
* Traffic Manager
* Virtual Machines – virtuální počítače se spravovanými disky nejde přesunout. Zobrazit [omezení virtuálních počítačů](#virtual-machines-limitations)
* Virtuální počítače (classic) – viz [omezení klasického nasazení](#classic-deployment-limitations)
* Škálovací sady virtuálních počítačů – viz [omezení virtuálních počítačů](#virtual-machines-limitations)
* Virtuální sítě – viz [omezení virtuální sítě](#virtual-networks-limitations)
* Visual Studio Team Services – účty VSTS pomocí rozšíření od jiných výrobců nákupy musí [zrušit jejich nákupů](https://go.microsoft.com/fwlink/?linkid=871160) než účet může pokračovat napříč předplatnými.
* VPN Gateway

## <a name="services-that-cannot-be-moved"></a>Služby, které nelze přesunout

Služby, které aktuálně nepovolí přesunutí prostředku jsou:

* AD Domain Services
* Hybridní AD Health Service
* Application Gateway
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure Database Migration
* Azure Databricks
* Azure Migrate
* Batch AI
* Certifikáty – certifikáty App Service je možné přesunout, ale mají odeslané certifikáty [omezení](#app-service-limitations).
* Container Instances
* Container Service
* Data Box
* Vývoj mezery
* Dynamics LCS
* ExpressRoute
* Kubernetes Service
* Je povoleno Lab Services - přesunout do nové skupiny prostředků ve stejném předplatném, ale přesun mezi předplatnými není povolen.
* Nástroje pro vyrovnávání zatížení – viz [omezení nástroje pro vyrovnávání zatížení](#lb-limitations)
* Managed Applications
* Managed Disks – viz [omezení virtuálních počítačů](#virtual-machines-limitations)
* Microsoft Genomics
* NetApp
* Veřejná IP adresa – viz [omezení veřejné IP adresy](#pip-limitations)
* Trezor služby Recovery Services - také není přesunout prostředky Compute, Network a Storage přidružených k trezoru služby Recovery Services naleznete v tématu [omezení Recovery Services](#recovery-services-limitations).
* SAP HANA v Azure
* Zabezpečení
* Site Recovery
* Správce zařízení StorSimple
* Zobrazit virtuální sítě (klasické) - [omezení klasického nasazení](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Omezení virtuálních počítačů

Spravované disky nepodporují přesunout. Toto omezení znamená, že několik související prostředky se nedají přesunout příliš. Nelze přesunout:

* Managed Disks
* Virtuální počítače se spravovanými disky
* Obrázky vytvořené ze spravované disky
* Snímky vytvořené ze spravované disky
* Skupiny dostupnosti s virtuálními počítači se spravovanými disky

I když nelze přesunout spravovaného disku, můžete vytvořit kopii a pak vytvořit nový virtuální počítač z existujícího spravovaného disku. Další informace naleznete v tématu:

* Kopírování spravovaných disků do stejného předplatného nebo jiném předplatném s [PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md) nebo [rozhraní příkazového řádku Azure](../virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md)
* Vytvoření virtuálního počítače pomocí existujícího spravovaného disku operačního systému s [PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md) nebo [rozhraní příkazového řádku Azure](../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md).

Virtuální počítače vytvořené z Marketplace prostředky s plány připojené se nedají přesouvat mezi skupinami prostředků nebo předplatných. Zrušení zřízení virtuálního počítače v rámci aktuálního předplatného a znovu nasadit v rámci nového předplatného.

Virtuální počítače pomocí certifikátu uloženého ve službě Key Vault můžete přesunout do nové skupiny prostředků ve stejném předplatném, ale ne napříč předplatnými.

## <a name="virtual-networks-limitations"></a>Omezení virtuální sítě

Při přesunu virtuální síť, musíte také přesunout její závislé prostředky. Pro brány sítě VPN musíte přesunout IP adresy brány virtuální sítě a všechny prostředky přidružené připojení. Brány místní sítě může být v jiné skupině prostředků.

Pokud chcete přesunout partnerské virtuální síti, musíte nejprve zakázat, partnerský vztah virtuální sítě. Jakmile zakázaná, můžete přesunout virtuální sítě. Po přesunutí znovu povolte partnerský vztah virtuální sítě.

Virtuální síť nelze přesunout do jiného předplatného, pokud virtuální síť obsahuje podsítě pomocí navigačních odkazů. Například pokud prostředek služby Redis Cache se nasazuje do podsítě, má této podsíti navigační odkaz prostředku.

## <a name="app-service-limitations"></a>Omezení služby App Service

Omezení pro přesun prostředků App Service se liší v závislosti na tom, jestli přesouváte prostředky v rámci předplatného nebo do nového předplatného.

Omezení popsaná v této části platí pro odeslané certifikáty, ne certifikáty App Service. Certifikáty App Service můžete přesunout do nové skupiny prostředků nebo předplatné bez omezení. Pokud máte více webových aplikací, které používají stejný certifikát App Service, nejprve přesunout všechny webové aplikace, přesuňte tento certifikát.

### <a name="moving-within-the-same-subscription"></a>Přesouvat v rámci stejného předplatného

Při přesunu webovou aplikaci _v rámci stejného předplatného_, nelze přesunout odeslané certifikáty SSL. Ale webové aplikace můžete přesunout do nové skupiny prostředků bez přesouvání jeho nahraného certifikátu SSL a funkce vaší aplikace SSL stále funguje.

Pokud chcete přesunout certifikát SSL s webovou aplikací, postupujte podle těchto kroků:

1.  Odstranění se nahraný certifikát z webové aplikace.
2.  Přesun webové aplikace.
3.  Nahrajte certifikát do přesunutý webové aplikace.

### <a name="moving-across-subscriptions"></a>Přesun mezi předplatnými

Při přesunu webovou aplikaci _napříč předplatnými_, platí následující omezení:

- Cílová skupina prostředků nesmí mít nějaké stávající prostředky App Service. Prostředky App Service patří:
    - Web Apps
    - Plány služby App Service
    - Nahrané nebo importované certifikáty SSL
    - Prostředí App Service
- Všechny prostředky App Service ve skupině prostředků daly přesunout najednou.
- Prostředky App Service lze pouze přesunout ze skupiny prostředků, ve kterém byly původně vytvořeny. Pokud prostředek služby App Service je už v jeho původní skupiny prostředků, je nutné je přesunout zpět do této skupiny původní prostředek nejprve a pak ji lze přesunout mezi předplatnými.

## <a name="classic-deployment-limitations"></a>Omezeními klasického nasazení

Možnosti pro přesun prostředky nasazené prostřednictvím klasického modelu liší v závislosti na tom, jestli přesouváte prostředky v rámci předplatného nebo do nového předplatného.

### <a name="same-subscription"></a>Stejné předplatné

Při přesouvání prostředků z jedné skupiny prostředků do jiné skupiny prostředků v rámci stejného předplatného, platí následující omezení:

* Nelze přesunout virtuální sítě (classic).
* Virtuální počítače (classic) musí přesunout s cloudovou službou.
* Cloudová služba se dá přesunout jedině po přesunutí zahrnuje všechny virtuální počítače.
* Pouze jednu cloudovou službu se dají přesunout najednou.
* Pouze jeden účet úložiště (classic) je možné přesunout najednou.
* Účet úložiště (classic) se nedají přesouvat v rámci jedné operace se virtuální počítač nebo cloudovou službu.

Přesouvání klasických prostředků do nové skupiny prostředků v rámci stejného předplatného, můžete přesunout standardní operace, ať už [portál](#use-portal), [prostředí Azure PowerShell](#use-powershell), [rozhraní příkazového řádku Azure](#use-azure-cli), nebo [rozhraní REST API](#use-rest-api). Můžete použít stejné operace jako při přesouvání prostředků Resource Manageru.

### <a name="new-subscription"></a>Nové předplatné

Při přesouvání prostředků do nového předplatného, platí následující omezení:

* V rámci jedné operace musí přesunout všechny klasické prostředky v předplatném.
* Cílové předplatné nesmí obsahovat žádné klasické prostředky.
* Přesunutí je možné jenom požádat prostřednictvím samostatných rozhraní REST API pro klasické přesuny. Standardní příkazy Resource Manager přesunout nefungují při přesouvání klasických prostředků do nového předplatného.

Klasické prostředky přesunout do nového předplatného, pomocí operace REST, které jsou specifické pro klasické prostředky. Použití REST, proveďte následující kroky:

1. Zaškrtněte, pokud zdrojové předplatné mohl podílet na přesun mezi předplatnými. Použijte následující operace:

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     V textu požadavku patří:

  ```json
  {
    "role": "source"
  }
  ```

     Odpověď pro operace ověření je v následujícím formátu:

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Zaškrtněte, pokud cílové předplatné se mohou účastnit přesun mezi předplatnými. Použijte následující operace:

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     V textu požadavku patří:

  ```json
  {
    "role": "target"
  }
  ```

     Odpověď je ve stejném formátu jako zdroj ověření předplatného.
3. Pokud obě předplatná projít ověřením, přesune všechny klasické prostředky z jednoho předplatného do jiného předplatného pomocí následující operace:

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    V textu požadavku patří:

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

Operace může běžet několik minut.

## <a name="recovery-services-limitations"></a>Omezení Recovery Services

Přesunutí není povolená pro úložiště, sítě nebo výpočetní prostředky, použít ke konfiguraci zotavení po havárii pomocí Azure Site Recovery.

Předpokládejme například, jste nastavili replikaci vašich místních počítačů do účtu úložiště (Storage1) a chcete chráněný počítač aktivován po převzetí služeb při selhání do Azure jako virtuální počítač (VM1) připojené k virtuální síti (Network1). Některé z těchto prostředků Azure - Storage1 VM1 a Network1 - nelze přesunout, mezi skupinami prostředků v rámci stejného předplatného nebo napříč předplatnými.

Přesunutí virtuálního počítače zaregistrované v **Azure backup** mezi skupinami prostředků:
 1. Dočasně zastavit zálohování a uchování zálohovaných dat
 2. Přesuňte virtuální počítač na cílovou skupinu prostředků
 3. Znovu nastavit ochranu v rámci stejné/nový trezor, které uživatelé mohou obnovit z bodů obnovení k dispozici vytvořené před přesunutím.
Pokud se uživatel přesune zálohovaných virtuálních počítačů mezi předplatnými, kroky 1 a 2 zůstávají stejné. V kroku 3 uživatel musí pro ochranu virtuálního počítače v rámci nové úložiště k dispozici / vytvořený v cílovém předplatném. Trezor služby Recovery Services nepodporuje záloh mezi předplatnými.

## <a name="hdinsight-limitations"></a>Omezení HDInsight

Clustery HDInsight můžete přesunout do nové předplatné nebo skupinu prostředků. Však nelze přesouvat mezi předplatných přidružených síťových prostředků do clusteru HDInsight (například virtuální sítě, síťové karty nebo nástroje pro vyrovnávání zatížení). Kromě toho nelze přesunout do nové skupiny prostředků síťové rozhraní, který je připojen k virtuálnímu počítači pro cluster.

Při přesunu do nového předplatného clusteru služby HDInsight, nejprve přesuňte dalších prostředků (např. účet úložiště). Přesuňte clusteru HDInsight samostatně.

## <a name="search-limitations"></a>Omezení vyhledávání

Nelze přesunout více hledat prostředky, které jsou umístěny v různých oblastech všechny najednou.
V takovém případě budete muset přesunout samostatně.

## <a name="lb-limitations"></a> Omezení nástroje pro vyrovnávání zatížení

Load balancer úrovně Basic SKU se dají přesunout.
Nástroj pro vyrovnávání zatížení standardní SKU nejde přesunout.

## <a name="pip-limitations"></a> Omezení veřejné IP

Základní veřejnou IP adresu SKU se dají přesunout.
Standardní veřejné IP adresy skladové položky nelze přesunout.

## <a name="use-portal"></a>Použít portál

Přesunout prostředky, vyberte skupinu prostředků obsahující prostředky a pak vyberte **přesunout** tlačítko.

![přesunutí prostředků](./media/resource-group-move-resources/select-move.png)

Vyberte, jestli přesouváte prostředky do nové skupiny prostředků nebo si nové předplatné.

Vyberte prostředky k přesunutí a cílová skupina prostředků. Potvrďte, že je potřeba aktualizovat skripty pro tyto prostředky a vyberte **OK**. Pokud vyberete ikonu pro úpravy odběru v předchozím kroku, musíte také vybrat cílové předplatné.

![Vyberte cíl](./media/resource-group-move-resources/select-destination.png)

V **oznámení**, uvidíte, že je spuštěná operace přesunutí.

![Zobrazit stav přesunu](./media/resource-group-move-resources/show-status.png)

Po jeho dokončení budete informováni o výsledek.

![Zobrazit výsledek přesunutí](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Použití prostředí PowerShell

Chcete-li stávající prostředky přesunout do jiné skupiny prostředků nebo předplatného, použijte [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) příkazu. Následující příklad ukazuje, jak přesunout více prostředků do nové skupiny prostředků.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Chcete-li přesunout do nového předplatného, zahrnout hodnotu pro `DestinationSubscriptionId` parametru.

## <a name="use-azure-cli"></a>Použití Azure CLI

Chcete-li stávající prostředky přesunout do jiné skupiny prostředků nebo předplatného, použijte [přesunutí prostředku az](/cli/azure/resource?view=azure-cli-latest#az-resource-move) příkaz. Zadejte ID prostředků pro přesun prostředků. Následující příklad ukazuje, jak přesunout více prostředků do nové skupiny prostředků. V `--ids` parametr, zadejte místo oddělený seznam ID pro přesun prostředků.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Pokud chcete přesunout do nového předplatného, zadejte `--destination-subscription-id` parametru.

## <a name="use-rest-api"></a>Použití rozhraní REST API

Pokud chcete stávající prostředky přesunout do jiné skupiny prostředků nebo předplatného, spusťte:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

V textu požadavku zadejte cílovou skupinu prostředků a prostředky, které chcete přesunout. Další informace o operaci přesunutí REST, naleznete v tématu [přesunutí prostředků](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Další postup

* Další informace o rutinách prostředí PowerShell pro správu předplatného, naleznete v tématu [pomocí Azure Powershellu s Resource Managerem](powershell-azure-resource-manager.md).
* Další informace o příkazech rozhraní příkazového řádku Azure pro správu předplatného, naleznete v tématu [pomocí rozhraní příkazového řádku Azure s využitím Resource Manageru](xplat-cli-azure-resource-manager.md).
* Další informace o funkce portálu pro správu předplatného najdete v tématu [pomocí webu Azure portal ke správě prostředků](resource-group-portal.md).
* Další informace o použití logické uspořádání vašich prostředků najdete v tématu [použití značek k uspořádání prostředků](resource-group-using-tags.md).
