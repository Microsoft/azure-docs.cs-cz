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
ms.date: 02/11/2019
ms.author: tomfitz
ms.openlocfilehash: 1902d00bc69fd12e7d424a2c2ab3bea562ff4e79
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108768"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Přesunutí prostředků do nové skupiny prostředků nebo předplatného

Tento článek ukazuje, jak Azure prostředky přesunout do jiného předplatného Azure nebo jiné skupiny prostředků ve stejném předplatném. Webu Azure portal, prostředí Azure PowerShell, rozhraní příkazového řádku Azure nebo rozhraní REST API můžete použít k přesunutí prostředků. Absolvovat kurz, naleznete v tématu [kurzu: Přesunutí prostředků Azure do jiné skupiny prostředků nebo předplatného](./resource-manager-tutorial-move-resources.md).

Během operace přesunu jsou zamknuté skupiny zdrojové i cílové skupině. Zápis a odstranění operace jsou blokovány o skupinách prostředků, až do dokončení přechodu. Tento Zámek znamená, že nelze přidat, aktualizovat nebo odstranit prostředky ve skupinách prostředků, ale neznamená, že prostředky jsou zmražená. Například při přesunutí serveru SQL Server a jeho databázi do nové skupiny prostředků, aplikace, která používá databázi prostředí bez výpadků. Můžete nadále číst a zapisovat do databáze.

Přesunutí prostředku pouze přesune do nové skupiny prostředků. Operace přesunu nejde změnit umístění prostředku. Nová skupina prostředků může mít jiné umístění, ale to se nemění umístění prostředku.

> [!NOTE]
> Tento článek popisuje, jak přesouvání prostředků mezi existující předplatná Azure. Pokud chcete skutečně upgradovat vaše předplatné Azure (např. přechod z bezplatné na průběžné platby), musíte převést vaše předplatné.
> * Upgrade bezplatné zkušební verze, najdete v článku [upgradujte vaše předplatné Microsoft Imagine Azure nebo bezplatná zkušební verze na průběžné platby](..//billing/billing-upgrade-azure-subscription.md).
> * Chcete-li změnit účet s průběžnými platbami, [změnit předplatné Azure s průběžnými platbami na jinou nabídku](../billing/billing-how-to-switch-azure-offer.md).
> * Pokud nelze převést předplatné [vytvoření žádosti o podporu Azure](../azure-supportability/how-to-create-azure-support-request.md). Vyberte **správy předplatných** typ problému.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="when-to-call-azure-support"></a>Při volání podpory Azure

Většinu prostředků prostřednictvím samoobslužné operace uvedené v tomto článku můžete přesunout. Použijte samoobslužné operace pro:

* Přesouvání prostředků Resource Manageru
* Přesouvání klasických prostředků podle [omezeními klasického nasazení](#classic-deployment-limitations).

Kontakt [podporují](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) když potřebujete:

* Prostředky přesunout do nového účtu Azure (a tenanta Azure Active Directory) a potřebujete pomoc s pokyny uvedenými v předchozí části.
* Přesouvání klasických prostředků, ale máte potíže s omezeními.

## <a name="services-that-can-be-moved"></a>Služby, které je možné přesunout

Následující seznam obsahuje obecný přehled služby Azure, které lze přesunout do nové skupiny prostředků a předplatném. Víc podrobností najdete v části [přesunout operace Podpora prostředků](move-support-resources.md).

* Analysis Services
* API Management
* Aplikace služby App Service (webové aplikace) – viz [omezení App Service](#app-service-limitations)
* Certifikáty služby App Service – viz [omezení služby App Service Certificate](#app-service-certificate-limitations)
* Automation
* Azure Active Directory B2C
* Azure Cosmos DB
* Průzkumník dat Azure
* Azure Database for MariaDB
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure DevOps – nákupy na Azure DevOps institucím rozšíření od jiných výrobců musí [zrušit jejich nákupů](https://go.microsoft.com/fwlink/?linkid=871160) než účet může pokračovat napříč předplatnými.
* Azure Maps
* Azure Relay
* Azure Stack – registrace
* Batch
* BizTalk Services
* Bot Service
* CDN
* Cloudové služby - viz [omezení klasického nasazení](#classic-deployment-limitations)
* Cognitive Services
* Container Registry – registr kontejnerů nejde přesunout, pokud geografická replikace je povolena.
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
* Přední dveře
* Clustery HDInsight – viz [omezení HDInsight](#hdinsight-limitations)
* Iot Central
* IoT Huby
* Služby Key Vault – Key Vault používá pro šifrování disků nelze přesunout do skupiny prostředků v rámci stejného předplatného nebo napříč předplatnými.
* Nástroje pro vyrovnávání zatížení – nástroj pro vyrovnávání zatížení základní SKU se dají přesunout. Nástroj pro vyrovnávání zatížení standardní SKU nejde přesunout.
* Log Analytics
* Logic Apps
* Machine Learning – Machine Learning Studio webové služby je možné přesunout do skupiny prostředků ve stejném předplatném, ale jiné předplatné. Další prostředky služby Machine Learning je možné přesunout mezi předplatnými.
* Managed Disks – viz [omezení virtuálních počítačů pro omezení](#virtual-machines-limitations)
* Spravovaná identita - přiřazená uživatelem
* Media Services
* Monitorování – zkontrolujte, že přesun do nového předplatného nepřekračuje [kvóty předplatných](../azure-subscription-service-limits.md#monitor-limits)
* Notification Hubs
* Operational Insights
* Správa operací
* Portálu řídicích panelů
* Power BI – jak Power BI Embedded a Power BI pracovního prostoru kolekce
* Veřejná IP adresa – základní veřejnou IP adresu SKU je možné přesunout. Standardní veřejné IP adresy skladové položky nelze přesunout.
* Služby Recovery Services vault – zaregistrujte se do [ve verzi preview](#recovery-services-limitations).
* Mezipaměť Azure pro Redis - li ukládání do mezipaměti Azure pro instanci Redis je nakonfigurovaný s virtuální sítí, instance nelze přesunout do jiného předplatného. Zobrazit [omezení virtuální sítě](#virtual-networks-limitations).
* Scheduler
* Search – několik hledání prostředky nejde přesunout v různých oblastech v rámci jedné operace. Místo toho přesuňte je v samostatné operace.
* Service Bus
* Service Fabric
* Service Fabric Mesh
* Služba SignalR
* Úložiště – účty úložiště v různých oblastech se nedají přesouvat v rámci jedné operace. Místo toho použijte samostatné operace pro každou oblast.
* Úložiště (classic) – viz [omezení klasického nasazení](#classic-deployment-limitations)
* Stream Analytics – Stream Analytics úlohy nelze přesunout, při spuštění ve stavu.
* Server služby SQL Database – databáze a serveru musí být ve stejné skupině prostředků. Přesunete-li SQL server, přesunou také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure SQL Data Warehouse.
* Time Series Insights
* Traffic Manager
* Virtual Machines – pro virtuální počítače pomocí spravované disky, najdete v článku [omezení virtuálních počítačů](#virtual-machines-limitations)
* Virtuální počítače (classic) – viz [omezení klasického nasazení](#classic-deployment-limitations)
* Škálovací sady virtuálních počítačů – viz [omezení virtuálních počítačů](#virtual-machines-limitations)
* Virtuální sítě – viz [omezení virtuální sítě](#virtual-networks-limitations)
* VPN Gateway

### <a name="services-that-cannot-be-moved"></a>Služby, které nelze přesunout

Následující seznam obsahuje obecný přehled služby Azure, které nelze přesunout do nové skupiny prostředků a předplatném. Víc podrobností najdete v části [přesunout operace Podpora prostředků](move-support-resources.md).

* AD Domain Services
* Hybridní AD Health Service
* Application Gateway
* Azure Database Migration
* Azure Databricks
* Brána Azure Firewall
* Azure Migrate
* Certifikáty – certifikáty App Service je možné přesunout, ale mají odeslané certifikáty [omezení](#app-service-limitations).
* Container Instances
* Container Service
* Data Box
* Vývoj mezery
* Dynamics LCS
* ExpressRoute
* Kubernetes Service
* Je povoleno Lab Services - přesunout do nové skupiny prostředků ve stejném předplatném, ale přesun mezi předplatnými není povolen.
* Managed Applications
* Microsoft Genomics
* NetApp
* SAP HANA v Azure
* Zabezpečení
* Site Recovery
* Správce zařízení StorSimple
* Zobrazit virtuální sítě (klasické) - [omezení klasického nasazení](#classic-deployment-limitations)

## <a name="limitations"></a>Omezení

V části najdete popis toho, jak zvládnout složité scénáře pro přesun prostředků. Omezení jsou:

* [Omezení virtuálních počítačů](#virtual-machines-limitations)
* [Omezení virtuální sítě](#virtual-networks-limitations)
* [Omezení služby App Service](#app-service-limitations)
* [App Service Certificate omezení](#app-service-certificate-limitations)
* [Omezeními klasického nasazení](#classic-deployment-limitations)
* [Omezení Recovery Services](#recovery-services-limitations)
* [Omezení HDInsight](#hdinsight-limitations)

### <a name="virtual-machines-limitations"></a>Omezení virtuálních počítačů

Z 24. září 2018 můžou přesunout spravované disky. Tato podpora znamená, že přesunete virtuální počítače se spravovanými disky, bitové kopie spravovaného, spravované snímky a skupiny dostupnosti s virtuálními počítači, které používají spravované disky.

Zatím nejsou podporovány následující scénáře:

* Virtuální počítače pomocí certifikátu uloženého ve službě Key Vault můžete přesunout do nové skupiny prostředků ve stejném předplatném, ale ne napříč předplatnými.
* Spravované disky v zónách dostupnosti nelze přesunout do jiného předplatného
* Škálovací sady virtuálních počítačů pomocí nástroje pro vyrovnávání zatížení standardní SKU nebo standardní veřejnou IP Adresou skladové položky nelze přesunout.
* Virtuální počítače vytvořené z Marketplace prostředky s plány připojené se nedají přesouvat mezi skupinami prostředků nebo předplatných. Zrušení zřízení virtuálního počítače v rámci aktuálního předplatného a znovu nasadit v rámci nového předplatného.

Pokud chcete přesunout virtuální počítače nakonfigurované s Azure Backup, použijte následující alternativní řešení:

* Nalezení vašeho virtuálního počítače.
* Najít skupinu prostředků pomocí následující vzor pro pojmenování: `AzureBackupRG_<location of your VM>_1` například AzureBackupRG_westus2_1
* Pokud na webu Azure portal, pak zaškrtněte "Zobrazit skryté typy"
* Pokud v prostředí PowerShell, použijte `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` rutiny
* Pokud v rozhraní příkazového řádku, použijte `az resource list -g AzureBackupRG_<location of your VM>_1`
* Najít prostředek s typem `Microsoft.Compute/restorePointCollections` , který má vzor pro pojmenování `AzureBackup_<name of your VM that you're trying to move>_###########`
* Odstraníte tento prostředek. Tato operace odstraní pouze body obnovení rychlé, ne zálohovaná data v trezoru.
* Po dokončení odstranění budete se moct přesunout virtuální počítač. Trezor a virtuální počítače můžete přesunout do cílového odběru. Po přesunutí můžete pokračovat v zálohování bez ztráty v datech.
* Informace o přesunutí trezory služby Recovery Service pro zálohování, naleznete v tématu [omezení Recovery Services](#recovery-services-limitations).

### <a name="virtual-networks-limitations"></a>Omezení virtuální sítě

Při přesunu virtuální síť, musíte také přesunout její závislé prostředky. Pro brány sítě VPN musíte přesunout IP adresy brány virtuální sítě a všechny prostředky přidružené připojení. Brány místní sítě může být v jiné skupině prostředků.

Pokud chcete přesunout partnerské virtuální síti, musíte nejprve zakázat, partnerský vztah virtuální sítě. Jakmile zakázaná, můžete přesunout virtuální sítě. Po přesunutí znovu povolte partnerský vztah virtuální sítě.

Virtuální síť nelze přesunout do jiného předplatného, pokud virtuální síť obsahuje podsítě pomocí navigačních odkazů. Například pokud Azure pro prostředek Redis Cache se nasazuje do podsítě, má této podsíti navigační odkaz prostředku.

### <a name="app-service-limitations"></a>Omezení služby App Service

Omezení pro přesun prostředků App Service se liší v závislosti na tom, jestli přesouváte prostředky v rámci předplatného nebo do nového předplatného. Pokud vaše webová aplikace používá certifikát App Service, přečtěte si téma [omezení služby App Service Certificate](#app-service-certificate-limitations)

#### <a name="moving-within-the-same-subscription"></a>Přesouvat v rámci stejného předplatného

Při přesunu webovou aplikaci _v rámci stejného předplatného_, certifikátů SSL třetí strany se nedá přesunout. Však webové aplikace můžete přesunout do nové skupiny prostředků bez přesouvání svůj certifikát třetí strany a funkce vaší aplikace SSL stále funguje.

Pokud chcete přesunout certifikát SSL s webovou aplikací, postupujte podle těchto kroků:

1. Odstranit certifikát třetí strany z webové aplikace, ale ponechat si kopii certifikátu
2. Přesun webové aplikace.
3. Nahrajte certifikát třetí strany do přesunutý webové aplikace.

#### <a name="moving-across-subscriptions"></a>Přesun mezi předplatnými

Při přesunu webovou aplikaci _napříč předplatnými_, platí následující omezení:

- Cílová skupina prostředků nesmí mít nějaké stávající prostředky App Service. Prostředky App Service patří:
    - Web Apps
    - Plány služby App Service
    - Nahrané nebo importované certifikáty SSL
    - Prostředí App Service
- Všechny prostředky App Service ve skupině prostředků daly přesunout najednou.
- Prostředky App Service lze pouze přesunout ze skupiny prostředků, ve kterém byly původně vytvořeny. Pokud prostředek služby App Service je už v jeho původní skupiny prostředků, je nutné je přesunout zpět do této skupiny původní prostředek nejprve a pak ji lze přesunout mezi předplatnými.

### <a name="app-service-certificate-limitations"></a>App Service Certificate omezení

Certifikát App Service můžete přesunout do nové skupiny prostředků nebo předplatného. Pokud certifikát App Service je svázaný s webovou aplikací, je nutné provést některé kroky před přesunutím prostředků do nového předplatného. Odstraňte vazbu SSL a privátní certifikát z webové aplikace před přesunutím prostředků. App Service Certificate se nemusí odstranit, stačí privátního certifikátu ve webové aplikaci.

### <a name="classic-deployment-limitations"></a>Omezeními klasického nasazení

Možnosti pro přesun prostředky nasazené prostřednictvím klasického modelu liší v závislosti na tom, jestli přesouváte prostředky v rámci předplatného nebo do nového předplatného.

#### <a name="same-subscription"></a>Stejné předplatné

Při přesouvání prostředků z jedné skupiny prostředků do jiné skupiny prostředků v rámci stejného předplatného, platí následující omezení:

* Nelze přesunout virtuální sítě (classic).
* Virtuální počítače (classic) musí přesunout s cloudovou službou.
* Cloudová služba se dá přesunout jedině po přesunutí zahrnuje všechny virtuální počítače.
* Pouze jednu cloudovou službu se dají přesunout najednou.
* Pouze jeden účet úložiště (classic) je možné přesunout najednou.
* Účet úložiště (classic) se nedají přesouvat v rámci jedné operace se virtuální počítač nebo cloudovou službu.

Přesouvání klasických prostředků do nové skupiny prostředků v rámci stejného předplatného, můžete přesunout standardní operace, ať už [portál](#use-portal), Azure Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API. Můžete použít stejné operace jako při přesouvání prostředků Resource Manageru.

#### <a name="new-subscription"></a>Nové předplatné

Při přesouvání prostředků do nového předplatného, platí následující omezení:

* V rámci jedné operace musí přesunout všechny klasické prostředky v předplatném.
* Cílové předplatné nesmí mít ostatní klasické prostředky.
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

### <a name="recovery-services-limitations"></a>Omezení Recovery Services

 Pro přesun trezoru služby Recovery Services, musíte se zaregistrovat [omezené veřejné verzi preview](../backup/backup-azure-move-recovery-services-vault.md).

V současné době můžete přesunout jeden trezor služby Recovery Services na oblast, najednou. Nelze přesunout trezorů, které se zálohování souborů Azure, Azure File Sync nebo SQL na virtuálních počítačích IaaS.

Pokud virtuální počítač čárka nepohybuje v trezoru, aktuálním bodům obnovení virtuálního počítače zůstat v trezoru, dokud nevyprší jejich platnost. Jestli se virtuální počítač přesunul v trezoru, nebo Ne, můžete obnovit virtuální počítač z historie zálohování v trezoru.

Trezor služby Recovery Services nepodporuje záloh mezi předplatnými. Pokud přesunete trezor pomocí zálohování dat virtuálních počítačů mezi předplatnými, musíte přesunout virtuální počítače do stejného předplatného a pokračovat v zálohování pomocí stejné cílová skupina prostředků.

Zásady zálohování, které jsou definovány pro trezor uchovávají po přesunutí trezoru. Monitorování a vytváření sestav musí být nastavit znovu trezoru po přesunutí.

K přesunutí virtuálního počítače do nového předplatného bez přesouvání do trezoru služby Recovery Services:

 1. Dočasně zastavit zálohování
 1. [Odstranit bod obnovení](#virtual-machines-limitations). Tato operace odstraní pouze body obnovení rychlé, ne zálohovaná data v trezoru.
 1. Přesunout virtuální počítače do nového předplatného
 1. Znovu nastavit ochranu v části v tomto předplatném nový trezor

Přesunutí není povolená pro úložiště, sítě nebo výpočetní prostředky, použít ke konfiguraci zotavení po havárii pomocí Azure Site Recovery. Předpokládejme například, jste nastavili replikaci vašich místních počítačů do účtu úložiště (Storage1) a chcete chráněný počítač aktivován po převzetí služeb při selhání do Azure jako virtuální počítač (VM1) připojené k virtuální síti (Network1). Některé z těchto prostředků Azure - Storage1 VM1 a Network1 - nelze přesunout, mezi skupinami prostředků v rámci stejného předplatného nebo napříč předplatnými.

### <a name="hdinsight-limitations"></a>Omezení HDInsight

Clustery HDInsight můžete přesunout do nové předplatné nebo skupinu prostředků. Však nelze přesouvat mezi předplatných přidružených síťových prostředků do clusteru HDInsight (například virtuální sítě, síťové karty nebo nástroje pro vyrovnávání zatížení). Kromě toho nelze přesunout do nové skupiny prostředků síťové rozhraní, který je připojen k virtuálnímu počítači pro cluster.

Při přesunu do nového předplatného clusteru služby HDInsight, nejprve přesuňte dalších prostředků (např. účet úložiště). Přesuňte clusteru HDInsight samostatně.

## <a name="checklist-before-moving-resources"></a>Kontrolní seznam před přesunutím prostředků

Existuje několik důležitých kroků provedete před přesunutím prostředku je. Ověřením těchto podmínek se můžete vyhnout chybám.

1. Zdrojové a cílové předplatné musí být aktivní. Pokud máte potíže s povolením účtu, který byl zakázán, [vytvoření žádosti o podporu Azure](../azure-supportability/how-to-create-azure-support-request.md). Vyberte **správy předplatných** typ problému.

1. Zdrojové a cílové předplatné musí existovat v rámci stejného [tenanta Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Pokud chcete zkontrolovat, že oba odběry obsahují stejné ID tenanta, pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure.

  Pro prostředí Azure PowerShell použijte:

  ```azurepowershell-interactive
  (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Pokud používáte Azure CLI, použijte:

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  Pokud ID tenantů pro zdrojové a cílové předplatné nejsou stejné, použijte následující metody k odsouhlasení ID tenantů:

  * [Přenos vlastnictví předplatného služby Azure na jiný účet](../billing/billing-subscription-transfer.md)
  * [Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Cílové předplatné musí být registrováno pro poskytovatele přesouvaného prostředku. Pokud ne, zobrazí chybová zpráva oznamující, že **předplatné není zaregistrované pro typ prostředku**. Tato chyba může zobrazit při přesunutí prostředku do nového předplatného, ale, že předplatné nikdy použito příslušný typ prostředku.

  Pokud používáte PowerShell, použijte následující příkazy získat stav registrace:

  ```azurepowershell-interactive
  Set-AzContext -Subscription <destination-subscription-name-or-id>
  Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Zaregistrovat poskytovatele prostředků, použijte:

  ```azurepowershell-interactive
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
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

1. Pokud je to možné, přerušení velké přesune do operací přesunu samostatných. Resource Manager okamžitě vrátí chybu, pokud existuje více než 800 prostředky v rámci jedné operace. Ale přesun prostředků menší než 800 selhat také podle vypršení časového limitu.

1. Služba musí umožňovat operaci přesouvání prostředků. Chcete-li zjistit, zda bude přesunutí úspěšné, [ověřit vaši žádost o přesunutí](#validate-move). Viz níže uvedené části v tomto článku [služby umožňují přesunutí prostředků](#services-that-can-be-moved) a které [není služby umožňují přesunutí prostředků](#services-that-cannot-be-moved).

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
 "resources": ["<resource-id-1>", "<resource-id-2>"],
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

## <a name="move-resources"></a>Přesunutí prostředků

### <a name="a-nameuse-portal-by-using-azure-portal"></a><a name="use-portal" />Pomocí webu Azure portal

Přesunout prostředky, vyberte skupinu prostředků pomocí těchto prostředků a pak vyberte **přesunout** tlačítko.

![přesunutí prostředků](./media/resource-group-move-resources/select-move.png)

Vyberte, jestli přesouváte prostředky do nové skupiny prostředků nebo si nové předplatné.

Vyberte prostředky k přesunutí a cílová skupina prostředků. Potvrďte, že je potřeba aktualizovat skripty pro tyto prostředky a vyberte **OK**. Pokud vyberete ikonu pro úpravy odběru v předchozím kroku, musíte také vybrat cílové předplatné.

![Vyberte cíl](./media/resource-group-move-resources/select-destination.png)

V **oznámení**, uvidíte, že je spuštěná operace přesunutí.

![Zobrazit stav přesunu](./media/resource-group-move-resources/show-status.png)

Po jeho dokončení budete informováni o výsledek.

![Zobrazit výsledek přesunutí](./media/resource-group-move-resources/show-result.png)

### <a name="by-using-azure-powershell"></a>Pomocí Azure Powershellu

K přesunutí stávajících prostředků do jiné skupiny prostředků nebo předplatného, použijte [přesunout AzResource](/powershell/module/az.resources/move-azresource) příkazu. Následující příklad ukazuje, jak několika prostředky přesunout do nové skupiny prostředků.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Chcete-li přesunout do nového předplatného, zahrnout hodnotu pro `DestinationSubscriptionId` parametru.

### <a name="by-using-azure-cli"></a>Pomocí rozhraní příkazového řádku Azure

Chcete-li stávající prostředky přesunout do jiné skupiny prostředků nebo předplatného, použijte [přesunutí prostředku az](/cli/azure/resource?view=azure-cli-latest#az-resource-move) příkaz. Zadejte ID prostředků pro přesun prostředků. Následující příklad ukazuje, jak několika prostředky přesunout do nové skupiny prostředků. V `--ids` parametr, zadejte místo oddělený seznam ID pro přesun prostředků.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Pokud chcete přesunout do nového předplatného, zadejte `--destination-subscription-id` parametru.

### <a name="by-using-rest-api"></a>Pomocí rozhraní REST API

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
