---
title: Konfigurace skupiny dostupnosti (Azure Portal)
description: Pomocí Azure Portal můžete vytvořit cluster s podporou převzetí služeb při selhání systému Windows, naslouchací proces skupiny dostupnosti a interní nástroj pro vyrovnávání zatížení na virtuálním počítači s SQL Server v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4020f47184e141a69586fc958f641547d7bde94d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89482793"
---
# <a name="configure-an-availability-group-for-sql-server-on-azure-vm-azure-portal---preview"></a>Konfigurace skupiny dostupnosti pro SQL Server na virtuálním počítači Azure (Azure Portal-Preview)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek popisuje, jak pomocí [Azure Portal](https://portal.azure.com) nakonfigurovat skupinu dostupnosti pro SQL Server na virtuálních počítačích Azure. 

Pomocí Azure Portal vytvořte nový cluster nebo zaveďte existující cluster a pak vytvořte skupinu dostupnosti, naslouchací proces a interní nástroj pro vyrovnávání zatížení. 

   > [!NOTE]
   > Tato funkce je aktuálně ve verzi Preview a je nasazená, takže pokud vaše požadovaná oblast není k dispozici, vraťte se brzy. 


## <a name="prerequisites"></a>Požadavky

Ke konfiguraci skupiny dostupnosti Always On pomocí Azure Portal musíte mít následující požadavky: 

- [Předplatné Azure](https://azure.microsoft.com/free/)
- Skupina prostředků s řadičem domény. 
- Jeden nebo víc virtuálních počítačů připojených k doméně [v Azure se spuštěným SQL Server 2016 (nebo novější) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) ve *stejné* skupině dostupnosti nebo v *různých* zónách dostupnosti, které jsou [zaregistrované u poskytovatele prostředků virtuálních počítačů SQL, v režimu úplné správy](sql-vm-resource-provider-register.md) a používají stejný účet domény pro službu SQL Server na každém virtuálním počítači.
- K dispozici jsou dvě (nepoužívané v žádné entitě) IP adresy. Jedna je určena pro interní nástroj pro vyrovnávání zatížení. Druhá je pro naslouchací proces skupiny dostupnosti ve stejné podsíti jako skupina dostupnosti. Pokud používáte existující Nástroj pro vyrovnávání zatížení, budete pro naslouchací proces skupiny dostupnosti potřebovat jenom jednu dostupnou IP adresu. 

## <a name="permissions"></a>Oprávnění

Ke konfiguraci skupiny dostupnosti pomocí Azure Portal potřebujete následující oprávnění: 

- Existující účet uživatele domény, který má v doméně oprávnění **vytvořit objekt počítače** . Například účet správce domény má obvykle dostatečná oprávnění (například: account@domain.com ). _Tento účet by měl být taky součástí místní skupiny správců na každém virtuálním počítači, aby se vytvořil cluster._
- Uživatelský účet domény, který řídí SQL Server. Mělo by se jednat o stejný účet pro každý SQL Server virtuální počítač, který máte v úmyslu přidat do skupiny dostupnosti. 

## <a name="configure-cluster"></a>Konfigurovat cluster

Nakonfigurujte cluster pomocí Azure Portal. Můžete buď vytvořit nový cluster, nebo pokud už máte nějaký cluster, můžete ho připojit k poskytovateli prostředků SQL VM pro správu portálu.


### <a name="create-a-new-cluster"></a>Vytvoření nového clusteru

Pokud už cluster máte, přeskočte tuto část a přejděte na místo toho, aby se [připojil existující cluster](#onboard-existing-cluster) . 

Pokud ještě nemáte existující cluster, vytvořte ho pomocí Azure Portal s těmito kroky:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 
1. Přejděte ke zdroji [virtuálních počítačů SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. V části **Nastavení**vyberte **Vysoká dostupnost** . 
1. Vyberte **+ nový cluster s podporou převzetí služeb při selhání ve Windows serveru** a otevřete stránku **konfigurovat cluster Windows s podporou převzetí**  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

1. Pojmenujte svůj cluster a poskytněte účtu úložiště, který se má použít jako disk s kopií cloudu. Použijte existující účet úložiště, nebo vyberte **vytvořit novou** a vytvořte nový účet úložiště. Název účtu úložiště musí být dlouhý 3 až 24 znaků a obsahovat jenom číslice a malá písmena.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

1. Rozbalte možnost **přihlašovací údaje clusteru s podporou převzetí služeb při selhání systému Windows Server** a poskytněte [přihlašovací údaje](https://docs.microsoft.com/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) pro účet služby SQL Server a také operátor clusteru a počáteční účty, pokud se liší od účtu používaného pro službu SQL Server. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster."
    ```
    


### <a name="onboard-existing-cluster"></a>Připojit existující cluster

Pokud už máte ve svém prostředí SQL Server VM nakonfigurovaný cluster, můžete ho připojit od Azure Portal.

To můžete provést pomocí těchto kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 
1. Přejděte ke zdroji [virtuálních počítačů SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. V části **Nastavení**vyberte **Vysoká dostupnost** . 
1. Vyberte připojit **existující cluster s podporou převzetí služeb při selhání se systémem Windows Server** a otevřete stránku **clusteru se systémem Windows Server failover cluster** . 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

1. Zkontrolujte nastavení clusteru. 
1. Pokud chcete pokračovat, vyberte **použít** pro připojení clusteru a pak na výzvu vyberte **Ano** .




## <a name="create-availability-group"></a>Vytvořit skupinu dostupnosti

Po vytvoření nebo zprovoznění clusteru vytvořte skupinu dostupnosti pomocí Azure Portal. To můžete provést pomocí těchto kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 
1. Přejděte ke zdroji [virtuálních počítačů SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. V části **Nastavení**vyberte **Vysoká dostupnost** . 
1. Vyberte **+ Nová skupina dostupnosti Always On** a otevřete stránku **vytvořit skupinu dostupnosti** .

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

1. Zadejte název skupiny dostupnosti. 
1. Výběrem **Konfigurovat naslouchací proces** otevřete stránku **Konfigurace naslouchacího procesu skupiny dostupnosti** . 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

1. Vyplňte hodnoty a buď použijte existující Nástroj pro vyrovnávání zatížení, nebo vyberte **vytvořit novou** a vytvořte nový nástroj pro vyrovnávání zatížení.  Pokud chcete uložit nastavení a vytvořit naslouchací proces a nástroj pro vyrovnávání zatížení, vyberte **použít** . 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

1. Kliknutím na **+ Vybrat repliku** otevřete stránku **Konfigurovat repliky skupiny dostupnosti** .
1. Vyberte virtuální počítače, které chcete přidat do skupiny dostupnosti, a zvolte nastavení skupiny dostupnosti, která nejlépe vyhovuje vašim obchodním potřebám. Vyberte **použít** a uložte nastavení. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

1. Ověřte nastavení skupiny dostupnosti a pak výběrem možnosti **použít** vytvořte skupinu dostupnosti. 

Stav nasazení můžete zjistit v **protokolu aktivit** , který je přístupný z ikony zvonku v horním navigačním panelu. 

  > [!NOTE]
  > **Stav synchronizace** na stránce s **vysokou dostupností** Azure Portal bude zobrazen jako **nev pořádku** , dokud do skupiny dostupnosti nepřidáte databáze. 


## <a name="add-database-to-availability-group"></a>Přidat databázi do skupiny dostupnosti

Až se nasazení dokončí, přidejte své databáze do skupiny dostupnosti. Níže uvedené kroky používají SQL Server Management Studio (SSMS), ale můžete také použít [jazyk Transact-SQL nebo PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) . 

K přidání databází do skupiny dostupnosti pomocí SQL Server Management Studio použijte následující postup:

1. Připojte se k jednomu z vašich SQL Server virtuálních počítačů pomocí preferované metody, jako je například Připojení ke vzdálené ploše (RDP). 
1. Otevřete SQL Server Management Studio (SSMS).
1. Připojte se k instanci SQL Server. 
1. V **Průzkumník objektů**rozbalte možnost **vždy na vysokou dostupnost** .
1. Rozbalte **skupiny dostupnosti**, klikněte pravým tlačítkem na vaši skupinu dostupnosti a vyberte **Přidat databázi...**.

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

1. Podle pokynů vyberte databáze, které chcete přidat do skupiny dostupnosti. 
1. Výběrem **OK** uložte nastavení a přidejte svoji databázi do skupiny dostupnosti. 
1. Po přidání databáze aktualizujte **Průzkumník objektů** , abyste potvrdili stav databáze jako `synchronized` . 

Po přidání databáze můžete zjistit stav skupiny dostupnosti v Azure Portal: 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

## <a name="add-more-vms"></a>Přidat další virtuální počítače

Pokud chcete do clusteru přidat další SQL Server virtuálních počítačů, postupujte takto: 

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 
1. Přejděte ke zdroji [virtuálních počítačů SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. V části **Nastavení**vyberte **Vysoká dostupnost** . 
1. Vyberte **konfigurovat cluster s podporou převzetí služeb při selhání Windows serveru** a otevřete stránku **konfigurovat cluster Windows Server failover cluster** . 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

1. Rozbalte možnost **přihlašovací údaje clusteru s podporou převzetí služeb při selhání systému Windows Server** a zadejte účty používané pro službu SQL Server, operátora clusteru a spouštěcí účty clusteru. 
1. Vyberte SQL Server virtuálních počítačů, které chcete přidat do clusteru. 
1. Vyberte **Použít**. 

Stav nasazení můžete zjistit v **protokolu aktivit** , který je přístupný z ikony zvonku v horním navigačním panelu. 


## <a name="modify-availability-group"></a>Upravit skupinu dostupnosti 


Můžete **Přidat další repliky** do skupiny dostupnosti, **nakonfigurovat naslouchací proces**nebo **Odstranit naslouchací proces** ze stránky s **vysokou dostupností** v Azure Portal tak, že vyberete tři tečky (...) vedle vaší skupiny dostupnosti: 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster.":::

## <a name="remove-cluster"></a>Odebrat cluster

Odeberte všechny SQL Server virtuálních počítačů z clusteru, abyste je zničili, a pak odeberte metadata clusteru z poskytovatele prostředků virtuálního počítače SQL. Můžete to provést pomocí nejnovější verze rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nebo PowerShellu. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nejdřív odeberte všechny SQL Server virtuálních počítačů z clusteru. Tím dojde k fyzickému odebrání uzlů z clusteru a zničení clusteru:  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Pokud se jedná o jediné virtuální počítače v clusteru, bude cluster zničen. Pokud v clusteru dojde k jiným virtuálním počítačům z SQL Server odebraných virtuálních počítačů, ostatní virtuální počítače se neodeberou a cluster nebude zničen. 

Pak z poskytovatele prostředků virtuálního počítače SQL odeberte metadata clusteru: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nejdřív odeberte všechny SQL Server virtuálních počítačů z clusteru. Tím dojde k fyzickému odebrání uzlů z clusteru a zničení clusteru: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Pokud se jedná o jediné virtuální počítače v clusteru, bude cluster zničen. Pokud v clusteru dojde k jiným virtuálním počítačům z SQL Server odebraných virtuálních počítačů, ostatní virtuální počítače se neodeberou a cluster nebude zničen. 


Pak z poskytovatele prostředků virtuálního počítače SQL odeberte metadata clusteru: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problémy, můžete zkontrolovat historii nasazení a zkontrolovat běžné chyby i jejich rozlišení. 

### <a name="check-deployment-history"></a>Zkontroluje historii nasazení.

Změny clusteru a skupiny dostupnosti prostřednictvím portálu se provádějí prostřednictvím nasazení. Pokud se vyskytnou problémy s vytvořením nebo zprovozněním clusteru nebo vytvořením skupiny dostupnosti, může tato historie nasazení poskytnout více podrobností.

Chcete-li zobrazit protokoly pro nasazení a vyhledat historii nasazení, postupujte podle následujících kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Přejděte do skupiny prostředků.
1. V části **Nastavení**vyberte **nasazení** .
1. Pokud chcete získat další informace o nasazení, vyberte nasazení, které vás zajímá. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Vytvořte nový cluster tak, že na portálu vyberete + nový cluster." :::

### <a name="common-errors"></a>Běžné chyby

Projděte si následující běžné chyby a jejich řešení. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>Účet, který se používá ke spuštění služby SQL, není účet domény.

To znamená, že poskytovatel prostředků nemohl získat přístup ke službě SQL Server pomocí zadaných přihlašovacích údajů. Některá běžná řešení:
- Ujistěte se, že je řadič domény spuštěný.
- Ověřte, že přihlašovací údaje zadané na portálu odpovídají nastavením služby SQL Server. 


## <a name="next-steps"></a>Další kroky


Další informace o skupinách dostupnosti najdete v těchto tématech:

- [Přehled skupin dostupnosti](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Správa skupiny dostupnosti](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorování skupin dostupnosti &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Příkazy Transact-SQL skupiny dostupnosti ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Příkazy prostředí PowerShell pro skupiny dostupnosti](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

Další informace o SQL Server virtuálních počítačích najdete v tématech: 

* [Přehled SQL Server virtuálních počítačů](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Poznámky k verzi pro virtuální počítače s SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Nejčastější dotazy týkající se SQL Server virtuálních počítačů](frequently-asked-questions-faq.md)
