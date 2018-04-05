---
title: Přesunutí nové předplatné nebo prostředek skupiny prostředků Azure | Microsoft Docs
description: Azure Resource Manager využívat k přesunu prostředků do nové skupiny prostředků nebo předplatného.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: tomfitz
ms.openlocfilehash: 40e79ba584843787ad7744f91e14907deb99ccaa
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Přesunutím prostředků do nové skupiny prostředků nebo předplatného

Tento článek ukazuje, jak k přesunu prostředků do nového předplatného nebo novou skupinu prostředků ve stejném předplatném. Můžete na portálu, prostředí PowerShell, rozhraní příkazového řádku Azure nebo rozhraní REST API pro přesun prostředků. Operace přesunutí v tomto článku jsou vám k dispozici bez jakékoli pomoci z podpory Azure.

Při přesouvání prostředků, skupině zdrojové a cílové skupiny jsou zamčené během operace. Zápis a operace odstranění jsou zablokované na skupiny prostředků, až po dokončení přesunu. Tato Zámek znamená nelze přidat, aktualizovat nebo odstranit prostředky ve skupině prostředků, ale neznamená, že zmrazené prostředky. Pokud přesunete SQL Server a jeho databáze do nové skupiny prostředků, například aplikace, která používá databázi vyskytne nedojde k žádnému výpadku. Můžete dál číst a zapisovat do databáze.

Nelze změnit umístění prostředku. Přesunutí prostředku přesouvá pouze ji do nové skupiny prostředků. Nové skupiny prostředků může mít jiné umístění, ale které nelze změnit umístění prostředku.

> [!NOTE]
> Tento článek popisuje postup přesunutí prostředků v Azure existující účet nabízení. Pokud skutečně chcete změnit účtu Azure nabídky (například upgrade z průběžné platby předem věnovat) přitom dál pracovat se stávajícími prostředky, najdete v článku [vašeho předplatného Azure přepnout na jinou nabídku](../billing/billing-how-to-switch-azure-offer.md).
>
>

## <a name="checklist-before-moving-resources"></a>Kontrolní seznam před přesunutím prostředků

Před přesunutím prostředku je nutné provést několik důležitých kroků. Ověřením těchto podmínek se můžete vyhnout chybám.

1. Zdrojové a cílové odběry, musí existovat v rámci stejné [klienta Azure Active Directory](../active-directory/active-directory-howto-tenant.md). Chcete-li zkontrolovat, že oba odběry obsahují stejné ID klienta, použijte prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure.

  Pro Azure PowerShell použijte:

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Pokud používáte Azure CLI, použijte:

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  Pokud klient ID pro zdrojové a cílové předplatné nejsou stejné, použijte následující metody sjednotit klienta ID: 

  * [Přenos vlastnictví předplatného služby Azure na jiný účet](../billing/billing-subscription-transfer.md)
  * [Postup přidružení nebo přidat předplatné Azure do Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md)

2. Služba musí umožňovat operaci přesouvání prostředků. V tomto článku jsou uvedené služby, které Povolit přesunutí prostředků a služby, které nepovolíte přesunutí prostředků.
3. Cílové předplatné musí být registrováno pro poskytovatele přesouvaného prostředku. Pokud ne, se zobrazí chybová zpráva s informacemi, které **předplatné není zaregistrované pro typ prostředku**. K problému může dojít, pokud přesouváte prostředek do nového předplatného, ale toto předplatné nebylo pro příslušný typ prostředku nikdy použito.

  Pro prostředí PowerShell použijte následující příkazy a získat stav registrace:

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Registrace poskytovatele prostředků, použijte:

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Pro rozhraní příkazového řádku Azure použijte následující příkazy a získat stav registrace:

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  Registrace poskytovatele prostředků, použijte:

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

4. Přesun prostředků účet musí mít alespoň následující oprávnění:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** na zdrojové skupiny prostředků.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** v cílové skupině prostředků.

## <a name="when-to-call-support"></a>Při volání podpory

Většina prostředkům prostřednictvím operace samoobslužné služby uvedené v tomto článku se můžete přesunout. Pomocí operace samoobslužné služby, které se:

* Přesouvání prostředků Resource Manageru
* Přesunout klasické prostředky podle požadavků [omezení nasazení classic](#classic-deployment-limitations).

Obraťte se na [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) když potřebujete:

* Přesuňte vašich prostředků na nový účet Azure (a klienta Azure Active Directory) a potřebujete pomoc s podle pokynů v předchozí části.
* Přesunout klasické prostředky ale dochází k potížím s omezeními.

## <a name="services-that-can-be-moved"></a>Služby, které lze přesunout

Služby, které umožňují přesun na novou skupinu prostředků a předplatného jsou:

* API Management
* Aplikace služby App Service (webové aplikace) – viz [omezení služby App Service](#app-service-limitations)
* Certifikáty App Service
* Application Insights
* Automation
* Azure Cosmos DB
* Batch
* Mapy Bingu
* CDN
* Cloudové služby - viz [omezení nasazení Classic](#classic-deployment-limitations)
* Kognitivní služby
* Content Moderator
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Hubs
* Clustery HDInsight - najdete v části [omezení HDInsight](#hdinsight-limitations)
* IoT Huby
* Key Vault
* Nástroje pro vyrovnávání zatížení – viz [omezení pro vyrovnávání zatížení](#lb-limitations)
* Logic Apps
* Machine Learning - Machine Learning Studio webové služby lze přesunout do skupiny prostředků ve stejném předplatném, ale není jiný odběr. Další prostředky Machine Learning lze přesunout mezi odběry.
* Media Services
* Mobile Engagement
* Notification Hubs
* Operational Insights
* Správa operací
* Power BI
* Veřejná IP adresa - najdete v části [omezení veřejnou IP adresu](#pip-limitations)
* Redis Cache
* Scheduler
* Search
* Správa serveru
* Service Bus
* Service Fabric
* Úložiště
* Najdete v části úložiště (klasické) - [omezení nasazení Classic](#classic-deployment-limitations)
* Stream Analytics - Stream Analytics úlohy nelze přesunout, při spuštění ve stavu.
* Databáze SQL server – databáze a server se musí nacházet ve stejné skupině prostředků. Když přesouváte systému SQL server, budou přesunuty také všechny její databáze.
* Traffic Manager
* Nelze přesunout virtuální počítače – virtuální počítače s spravované disky. V tématu [omezení virtuální počítače](#virtual-machines-limitations)
* Virtuální počítače (klasické) - najdete v části [omezení nasazení Classic](#classic-deployment-limitations)
* Sady škálování virtuálního počítače – viz [omezení virtuální počítače](#virtual-machines-limitations)
* Najdete v části virtuální sítě - [omezení virtuální sítě](#virtual-networks-limitations)
* VPN Gateway

## <a name="services-that-cannot-be-moved"></a>Služby, které nelze přesunout.

Služby, které aktuálně nepovolujte přesunutí prostředku jsou:

* AD Domain Services
* AD Hybrid Health Service
* Application Gateway
* BizTalk Services
* Container Service
* ExpressRoute
* DevTest Labs – přesunout do nové skupiny prostředků v rámci stejného předplatného je povoleno, ale přesunutí křížové předplatného není povolená.
* Dynamics LCS
* Nástroje pro vyrovnávání zatížení – viz [omezení pro vyrovnávání zatížení](#lb-limitations)
* Spravované aplikace
* Najdete v části spravované disky - [omezení virtuální počítače](#virtual-machines-limitations)
* Veřejná IP adresa - najdete v části [omezení veřejnou IP adresu](#pip-limitations)
* Trezor služeb zotavení – také proveďte není přesunout prostředky výpočty, síť a úložiště přidružený k trezoru služeb zotavení, najdete v části [služeb zotavení omezení](#recovery-services-limitations).
* Zabezpečení
* Správce zařízení StorSimple
* Najdete v části virtuální sítě (klasické) - [omezení nasazení Classic](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Omezení virtuální počítače

Spravované disky nepodporují move. Toto omezení znamená, že několik související prostředky nejde přesunout příliš. Nemůžete přesunout:

* Managed Disks
* Virtuální počítače s spravované disky
* Bitové kopie vytvořené z spravovaných disků
* Snímků vytvořených z spravovaných disků
* Sady dostupnosti s virtuálními počítači s spravované disky

Virtuální počítače vytvořené z Marketplace prostředků pomocí plánů připojené nelze přesunout mezi skupinami prostředků nebo předplatných. Zrušení zřízení virtuálního počítače v aktuálním předplatném a znovu nasaďte v rámci nového předplatného.

Virtuální počítače s certifikát uložený v Key Vault můžete přesunout do nové skupiny prostředků v rámci stejného předplatného, ale ne odběry.

## <a name="virtual-networks-limitations"></a>Omezení virtuální sítě

Pokud chcete přesunout peered virtuální sítě, je nutné nejdřív zakázat partnerského vztahu virtuální sítě. Jakmile je zakázaná, můžete přesunout virtuální sítě. Po přesunu znovu povolte partnerského vztahu virtuální sítě.

Virtuální síť nemůžete přesunout do jiného předplatného, pokud virtuální síť obsahuje podsíť s odkazy na zdroje navigace. Například pokud prostředek Redis Cache je nasazený do podsítě, této podsíti má navigační odkaz na prostředek.

## <a name="app-service-limitations"></a>Omezení služby App Service

Omezení pro přesun prostředků služby App Service se liší v závislosti na tom, jestli jsou přesun prostředků v rámci předplatného nebo do nového předplatného. 

Omezení popsaná v těchto částech platí pro nahraném certifikáty, není služby App Service Certificate. Služby App Service Certificate můžete přesunout do nové skupiny prostředků nebo předplatného bez omezení. Pokud máte více webových aplikací, které používají stejný certifikát služby aplikace, nejprve přesunout všechny webové aplikace, pak přesuňte certifikát.

### <a name="moving-within-the-same-subscription"></a>Přesunutí v rámci stejného předplatného.

Při přesunu webové aplikace _v rámci stejného předplatného_, nemůžete přesunout nahrané certifikáty SSL. Ale webové aplikace do nové skupiny prostředků můžete přesunout bez přesouvání jeho odeslaný certifikát SSL a funkce SSL vaší aplikace stále funguje. 

Pokud chcete přesunout certifikát SSL s webovou aplikaci, postupujte podle těchto kroků:

1.  Odstraníte se nahraný certifikát z webové aplikace.
2.  Přesuňte webové aplikace.
3.  Nahrajte certifikát do přesunutý webové aplikace.

### <a name="moving-across-subscriptions"></a>Přesouvání mezi odběrů

Při přesunu webové aplikace _ve předplatných_, platí následující omezení:

- Cílové skupiny prostředků nesmí mít žádné existující prostředky služby App Service. Služby App Service prostředky zahrnují:
    - Web Apps
    - Plány služby App Service
    - Nahrané nebo importované certifikáty SSL
    - Prostředí App Service
- Všechny prostředky služby App Service ve skupině prostředků je třeba přesunout společně.
- Prostředky aplikace služby lze přesunout pouze ze skupiny prostředků, ve které byly původně vytvořili. Pokud prostředek služby App Service je již v jeho původní skupin prostředků, je nutné je přesunout zpět do této původní skupiny prostředků nejprve a pak ho lze přesunout mezi odběry. 

## <a name="classic-deployment-limitations"></a>Omezení nasazení Classic

Možnosti pro přesun prostředků nasazené pomocí klasického modelu se liší v závislosti na tom, jestli jsou přesun prostředků v rámci předplatného nebo do nového předplatného.

### <a name="same-subscription"></a>Stejného předplatného.

Při přesouvání prostředků z jedné skupiny prostředků do jiné skupiny prostředků v rámci stejného předplatného, platí následující omezení:

* Nelze přesunout virtuální sítě (klasické).
* Virtuální počítače (klasické) je třeba přesunout s cloudovou službou.
* Cloudové služby se dají přesunout pouze při přesunutí zahrnuje všechny virtuální počítače.
* Současně lze přesunout jenom jeden cloudové služby.
* Současně lze přesouvat pouze jeden účet úložiště (klasické).
* Účet úložiště (klasické) nelze přesunout do stejné operace virtuálního počítače nebo cloudové služby.

K přesunutí klasické prostředky na novou skupinu prostředků v rámci stejného předplatného, použijte standardní Přesunutí operací prostřednictvím [portál](#use-portal), [prostředí Azure PowerShell](#use-powershell), [rozhraní příkazového řádku Azure](#use-azure-cli), nebo [rozhraní REST API](#use-rest-api). Můžete použít stejné operace jako použijte pro přesun prostředků Resource Manager.

### <a name="new-subscription"></a>Nové předplatné

Při přesunu prostředků do nového předplatného, platí následující omezení:

* Všechny klasické prostředky v předplatném je třeba přesunout v rámci jedné operace.
* Cílové předplatné nesmí obsahovat všechny klasické prostředky.
* Přesunutí může požadovat pouze přes samostatné REST API pro classic přesune. Standardní příkazy přesunutí Resource Manager nefungují při přesunu klasické prostředky na nové předplatné.

Chcete-li přesunout klasické prostředky do nového předplatného, použijte operace REST, které jsou specifické pro klasické prostředky. Abyste mohli použít REST, proveďte následující kroky:

1. Zkontrolujte, pokud zdrojové předplatné mohl účastnit přesunu mezi předplatnými. Použijte následující operace:

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

2. Zkontrolujte, pokud cílového odběru mohl účastnit přesunu mezi předplatnými. Použijte následující operace:

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
3. Pokud oba odběry projít ověřením, přesune všechny klasické prostředky z jedno předplatné do jiného předplatného s následující operace:

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    V textu požadavku patří:

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

Operaci může běžet několik minut.

## <a name="recovery-services-limitations"></a>Omezení služby obnovení

Přesunutí není povolen pro úložiště, sítě, nebo výpočetní prostředky, které jsou používána k nastavení zotavení po havárii s Azure Site Recovery.

Předpokládejme například, jste nastavili replikaci počítačů na místě na účet úložiště (Storage1) a chcete chráněného počítače přijít po převzetí služeb při selhání do Azure jako virtuální počítač (VM1) připojených k virtuální síti (Network1). Některé z těchto prostředků Azure - Storage1, VM1 a Network1 - nelze přesunout skupiny prostředků v rámci stejného předplatného nebo pro odběry.

Chcete-li přesunout virtuální počítač zaregistrovaný v **zálohování Azure** mezi skupinami prostředků:
 1. Dočasně zastavení zálohování a zachovat zálohovaná data
 2. Přesuňte virtuální počítač cílová skupina prostředků
 3. Nastavte ji znovu v rámci stejné nebo nové úložiště, které uživatelé mohou obnovit z bodů obnovení k dispozici vytvořil před operaci přesunutí.
Pokud se uživatel přesune virtuální počítač zálohovaná ve předplatných, kroky 1 a 2 zůstávají stejné. V kroku 3 musí uživatel ochranu virtuálního počítače v části nový trezor přítomen / vytvořené v cílové předplatné. Trezor služeb zotavení nepodporuje zálohování křížové předplatného.

## <a name="hdinsight-limitations"></a>Omezení HDInsight

Clustery HDInsight se můžete přesunout do nové předplatné nebo skupinu prostředků. Však nelze přesouvat mezi odběry síťových prostředků propojené ke clusteru HDInsight (například virtuální sítě, síťové karty nebo nástroj pro vyrovnávání zatížení). Kromě toho nelze přesunout do nové skupiny prostředků síťový adaptér, který je připojen k virtuálnímu počítači pro cluster.

Při přesunu clusteru HDInsight na nové předplatné, nejprve přesunete jiné prostředky (například účet úložiště). HDInsight cluster, pak přesuňte samostatně.

## <a name="search-limitations"></a>Omezení vyhledávání

Nelze přesunout více hledat prostředky, které jsou umístěny v různých oblastech všechny najednou.
V takovém případě budete muset přesuňte je samostatně.

## <a name="lb-limitations"></a> Omezení nástroje pro vyrovnávání zatížení

Základní SKU pro vyrovnávání zatížení se dají přesunout.
Nástroj pro vyrovnávání zatížení standardní SKU nelze přesunout.

## <a name="pip-limitations"></a> Veřejné IP omezení

Základní SKU veřejné IP se dají přesunout.
Standardní SKU veřejné IP nelze přesunout.

## <a name="use-portal"></a>Použít portál

Chcete-li přesunout prostředky, vyberte skupinu prostředků obsahující tyto prostředky a pak vyberte **přesunout** tlačítko.

![Přesunutí prostředků](./media/resource-group-move-resources/select-move.png)

Vyberte, jestli přecházíte prostředky na novou skupinu prostředků nebo nové předplatné.

Vyberte zdroje, které chcete přesunout a cílové skupiny prostředků. Na vědomí, že budete muset aktualizovat skripty pro tyto prostředky a vyberte **OK**. Pokud jste v předchozím kroku vybrali na ikonu pro úpravu předplatné, musíte také vybrat cílového odběru.

![Vyberte cíl](./media/resource-group-move-resources/select-destination.png)

V **oznámení**, uvidíte, že je spuštěna operaci přesunutí.

![Zobrazit stav přesunutí](./media/resource-group-move-resources/show-status.png)

Pokud ho dokončit, zobrazí se zpráva výsledku.

![Zobrazit přesunout výsledků](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Použití prostředí PowerShell

Chcete-li existující prostředky přesunout do jiné skupiny prostředků nebo předplatného, použijte [přesunutí AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) příkaz. Následující příklad ukazuje, jak přesunout více prostředků do nové skupiny prostředků.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Pokud chcete přesunout do nového předplatného, obsahovat hodnotu pro `DestinationSubscriptionId` parametr.

## <a name="use-azure-cli"></a>Použití Azure CLI

Chcete-li existující prostředky přesunout do jiné skupiny prostředků nebo předplatného, použijte [přesunutí prostředku az](/cli/azure/resource?view=azure-cli-latest#az_resource_move) příkaz. Zadejte ID prostředků pro přesun prostředků. Následující příklad ukazuje, jak přesunout více prostředků do nové skupiny prostředků. V `--ids` parametr, zadejte místo oddělený seznam ID pro přesun prostředků.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Chcete-li přesunout do nového předplatného, zadat `--destination-subscription-id` parametr.

## <a name="use-rest-api"></a>Použití rozhraní REST API

Chcete-li existující prostředky přesunout do jiné skupiny prostředků nebo předplatného, spusťte:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

V těle žádosti je zadat cílová skupina prostředků a prostředky, které chcete přesunout. Další informace o operaci REST přesunutí najdete v tématu [přesunout prostředky](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Další postup

* Další informace o rutinách prostředí PowerShell pro správu předplatného, najdete v části [pomocí prostředí Azure PowerShell s Resource Managerem](powershell-azure-resource-manager.md).
* Další informace o rozhraní příkazového řádku Azure pro správu předplatného najdete v tématu [pomocí rozhraní příkazového řádku Azure s Resource Managerem](xplat-cli-azure-resource-manager.md).
* Další informace o portálu funkce pro správu předplatného najdete v tématu [pomocí portálu Azure ke správě prostředků](resource-group-portal.md).
* Další informace o použití logického organizace pro vaše prostředky najdete v tématu [použití značek k uspořádání prostředků](resource-group-using-tags.md).
