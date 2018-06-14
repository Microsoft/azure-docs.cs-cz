---
title: Spravovat výpočetní uzly clusteru HPC Pack | Microsoft Docs
description: Další informace o nástroje skript prostředí PowerShell pro přidání, odebrání, spuštění a zastavení výpočetní uzly clusteru HPC Pack 2012 R2 v Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 453f53be15b24b96f183b4935cc45fc97ad058bd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914931"
---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Správa počtu a dostupnosti výpočetních uzlů v clusteru HPC Pack v Azure
Pokud jste vytvořili clusteru HPC Pack 2012 R2 ve virtuálních počítačích Azure, můžete chtít způsoby, jak snadno přidat, odebrat, (zřídit) spuštění nebo zastavení (deprovision) některé výpočetní uzel virtuální počítače v clusteru. Chcete-li provést tyto úlohy, spusťte prostředí Azure PowerShell skripty, které jsou nainstalované na hlavního uzlu virtuálního počítače. Tyto skripty vám pomůžou usnadnit řízení číslo a dostupnost prostředků clusteru HPC Pack, můžete řídit náklady.

> [!IMPORTANT] 
> Tento článek se týká pouze do clusterů HPC Pack 2012 R2 v Azure vytvořit pomocí modelu nasazení classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> Kromě toho nejsou k dispozici v prostředí HPC Pack 2016 skriptů prostředí PowerShell popsaných v tomto článku.

## <a name="prerequisites"></a>Požadavky
* **Cluster HPC Pack 2012 R2 ve virtuálních počítačích Azure**: vytvoření clusteru HPC Pack 2012 R2 v modelu nasazení classic. Například můžete automatizovat nasazení s použitím bitové kopie prostředí HPC Pack 2012 R2 virtuálních počítačů v Azure Marketplace a skript Azure PowerShell. Informace a požadavky najdete v tématu [vytvoření clusteru prostředí HPC pomocí skriptu pro nasazení HPC Pack IaaS](hpcpack-cluster-powershell-script.md).
  
    Po nasazení se najít uzel správy skripty ve složce % CCP\_DOMOVSKÉ složky bin % z hlavního uzlu. Všechny tyto skripty spusťte jako správce.
* **Certifikát správy nebo soubor nastavení publikování Azure**: je třeba provést jednu z následujících postupů z hlavního uzlu:
  
  * **Soubor nastavení publikování import Azure**. Chcete-li to provést, spusťte následující rutiny prostředí Azure PowerShell z hlavního uzlu:
    
    ```PowerShell
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **Konfigurace certifikátu pro správu Azure z hlavního uzlu**. Pokud máte soubor .cer, importujte ji do úložiště certifikátů CurrentUser\My a pak spusťte následující rutiny Azure Powershellu pro prostředí Azure (AzureCloud nebo AzureChinaCloud):
    
    ```PowerShell
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Přidat výpočetním uzlu virtuální počítače
Přidat výpočetní uzly s **přidat HpcIaaSNode.ps1** skriptu.

### <a name="syntax"></a>Syntaxe
```PowerShell
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Parametry
* **ServiceName**: název cloudové služby, která nový výpočet uzlu virtuální počítače jsou přidány do.
* **ImageName**: název bitové kopie virtuálního počítače Azure, který můžete získat prostřednictvím portálu Azure nebo rutiny Azure Powershellu **Get-AzureVMImage**. Obrázek musí splňovat následující požadavky:
  
  1. Musí být nainstalován operační systém Windows.
  2. HPC Pack musí být nainstalován v uzlu výpočetní roli.
  3. Obrázek musí být privátní bitové kopie v kategorii uživatelů, není veřejný image virtuálního počítače Azure.
* **Množství**: počet výpočetním uzlu virtuální počítače, který se má přidat.
* **InstanceSize**: velikost výpočetním uzlu virtuálních počítačů.
* **DomainUserName**: uživatelské jméno domény, který se používá k připojení k nové virtuální počítače k doméně.
* **DomainUserPassword**: heslo uživatele domény.
* **NodeNameSeries** (volitelné): pojmenování vzor pro výpočetní uzly. Musí být ve formátu &lt; *kořenové\_název*&gt;&lt;*spustit\_číslo*&gt;%. Například MyCN % 10 % prostředků a řadu od MyCN11 názvy výpočetního uzlu. Pokud není zadaný, skript používá uzlu nakonfigurované pojmenování řady v clusteru HPC.

### <a name="example"></a>Příklad:
Následující příklad přidá 20 velikost velké výpočetním uzlu virtuální počítače v cloudové službě *hpcservice1*, na bitovou kopii virtuálního počítače na bázi *hpccnimage1*.

```PowerShell
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Odebrat výpočetním uzlu virtuální počítače
Odebrat výpočetní uzly s **odebrat HpcIaaSNode.ps1** skriptu.

### <a name="syntax"></a>Syntaxe
```PowerShell
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Parametry
* **Název**: názvy uzlů clusteru k odebrání. Jsou podporovány zástupné znaky. Název parametru sada je název. Nejde zadat oba seznamy **název** a **uzlu** parametry.
* **Uzel**: objekt HpcNode pro uzly odebrány, které lze získat pomocí rutiny prostředí HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Název parametru sada je uzel. Nejde zadat oba seznamy **název** a **uzlu** parametry.
* **DeleteVHD** (volitelné): nastavení, které chcete odstranit související disky pro virtuální počítače, které jsou odebrány.
* **Vynutit** (volitelné): nastavení přinutit HPC uzly offline před jejich odebráním.
* **Potvrďte** (volitelné): výzva k potvrzení před provedením příkazu.
* **WhatIf**: nastavení popisují, co by mohlo dojít, pokud se příkaz provedl, aniž by příkaz skutečně proveden.

### <a name="example"></a>Příklad:
Následující příklad vynutí offline uzly s názvy od *HPCNode-CN -* a jejich odebere uzlů a jejich přidružené disky.

```PowerShell
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Spuštění výpočetního uzlu virtuální počítače
Spuštění výpočetní uzly se **Start-HpcIaaSNode.ps1** skriptu.

### <a name="syntax"></a>Syntaxe
```PowerShell
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Parametry
* **Název**: názvy uzlů clusteru spustit. Jsou podporovány zástupné znaky. Název parametru sada je název. Nejde zadat oba seznamy **název** a **uzlu** parametry.
* **Uzel**-HpcNode objektu pro uzly má být spuštěn, které lze získat pomocí rutiny prostředí HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Název parametru sada je uzel. Nejde zadat oba seznamy **název** a **uzlu** parametry.

### <a name="example"></a>Příklad:
Následující příklad spustí uzly s názvy od *HPCNode-CN -*.

```PowerShell
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Zastavit výpočetním uzlu virtuální počítače
Zastavit výpočetní uzly s **Stop-HpcIaaSNode.ps1** skriptu.

### <a name="syntax"></a>Syntaxe
```PowerShell
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parametry
* **Název**-názvy uzlů clusteru, který má být zastaven. Jsou podporovány zástupné znaky. Název parametru sada je název. Nejde zadat oba seznamy **název** a **uzlu** parametry.
* **Uzel**: objekt HpcNode pro uzly zastavení, které lze získat pomocí rutiny prostředí HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Název parametru sada je uzel. Nejde zadat oba seznamy **název** a **uzlu** parametry.
* **Vynutit** (volitelné): nastavení přinutit HPC uzly do offline režimu před zastavením je.

### <a name="example"></a>Příklad:
Následující příklad vynutí offline uzly s názvy od *HPCNode-CN -* a poté se zastaví uzly.

```PowerShell
Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force
```

## <a name="next-steps"></a>Další postup
* Automaticky zvětšovat a zmenšovat uzly v clusteru podle aktuální zatížení úloh a úloh v clusteru, najdete v tématu [automaticky zvýšit nebo snížit prostředků clusteru HPC Pack v Azure podle zatížení clusteru](hpcpack-cluster-node-autogrowshrink.md).

