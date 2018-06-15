---
title: Uzly clusteru HPC Pack škálování | Microsoft Docs
description: Automaticky zvýšit nebo snížit počet výpočetních uzlů clusteru HPC Pack v Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: ''
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
ms.openlocfilehash: 4a2350183bc0cb9360e9315cd8a351be20b66584
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603840"
---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Automaticky zvýšit nebo snížit prostředků clusteru HPC Pack v Azure podle zatížení clusteru
Pokud nasazujete Azure "shluků" uzly v clusteru HPC Pack nebo vytváření clusteru HPC Pack ve virtuálních počítačích Azure, můžete způsob, jak automaticky zvětšovat a zmenšovat prostředků clusteru jako uzly nebo jader podle zatížení v clusteru. Škálování prostředků clusteru tímto způsobem umožňuje efektivněji používat vašich prostředků Azure a kontrolovat náklady.

Tento článek ukazuje, že dva způsoby, které poskytuje HPC Pack Chcete používat automatické škálování výpočetní prostředky:

* Vlastnost clusteru HPC Pack **AutoGrowShrink**

* **AzureAutoGrowShrink.ps1** skript prostředí PowerShell HPC

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Aktuálně lze pouze automaticky zvýšit nebo snížit HPC Pack výpočetní uzly, které je spuštěn operační systém Windows Server.


## <a name="set-the-autogrowshrink-cluster-property"></a>Nastavte vlastnost AutoGrowShrink clusteru
### <a name="prerequisites"></a>Požadavky

* **HPC Pack 2012 R2 Update 2 nebo novější clusteru** -hlavního uzlu clusteru může být nasazena místně nebo ve virtuálním počítači Azure. V tématu [nastavení hybridního clusteru pomocí sady HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) začít pracovat se hlavnímu uzlu místní a uzly Azure "shluků". Najdete v článku [skript nasazení HPC Pack IaaS](hpcpack-cluster-powershell-script.md) k rychlému nasazení clusteru HPC Pack ve virtuálních počítačích Azure.

* **Pro cluster s hlavního uzlu v Azure (modelu nasazení Resource Manager)** – od HPC Pack 2016, ověřování pomocí certifikátu v aplikaci Azure Active Directory se používá pro automaticky rostoucí a zmenšení clusteru virtuálních počítačů nasadit pomocí Azure Resource Manager. Nakonfigurujte certifikát takto:

  1. Po nasazení clusteru připojte pomocí vzdálené plochy na jeden hlavní uzel.

  2. Nahrajte certifikát (ve formátu PFX s privátním klíčem) do každé hlavního uzlu a nainstalujte Cert: \LocalMachine\My a Cert: \LocalMachine\Root.

  3. Otevřete prostředí Azure PowerShell jako správce a spusťte následující příkazy v jednom hlavního uzlu:

    ```powershell
        cd $env:CCP_HOME\bin

        Connect-AzureRmAccount
    ```
        
    Pokud váš účet je ve více než jednoho klienta Azure Active Directory nebo předplatné Azure, můžete spustit následující příkaz pro vybrat správný klienta a předplatné:
  
    ```powershell
        Connect-AzureRmAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    Spusťte následující příkaz k zobrazení aktuálně vybraného klienta a předplatného:
    
    ```powershell
        Get-AzureRMContext
    ```

  4. Spusťte následující skript

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    kde

    **DisplayName** -zobrazovaný název aplikace Azure aktivní. Pokud aplikace neexistuje, vytvoří se v Azure Active Directory.

    **Domovská stránka** -domovské stránce aplikace. Můžete nakonfigurovat fiktivní adresu URL, jako v předchozím příkladu.

    **IdentifierUri** -identifikátor aplikace. Můžete nakonfigurovat fiktivní adresu URL, jako v předchozím příkladu.

    **CertificateThumbprint** -kryptografický otisk certifikátu, který jste nainstalovali z hlavního uzlu v kroku 1.

    **TenantId** -ID služby Azure Active Directory klienta. ID klienta můžete získat z portálu Azure Active Directory **vlastnosti** stránky.

    Další podrobnosti o **ConfigARMAutoGrowShrinkCert.ps1**spusťte `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.


* **Pro cluster s hlavního uzlu v Azure (modelu nasazení classic)** – Pokud používáte skript nasazení HPC Pack IaaS k vytvoření clusteru v modelu nasazení classic, povolte **AutoGrowShrink** vlastnost pomocí clusteru nastavení možnosti AutoGrowShrink v konfiguračním souboru na clusteru. Podrobnosti najdete v tématu doprovodné dokumentaci [stahování skriptu](https://www.microsoft.com/download/details.aspx?id=44949).

    Můžete taky povolit **AutoGrowShrink** clusteru vlastnost po nasazení clusteru pomocí příkazů prostředí HPC PowerShell popsaných v následující části. Příprava na to, proveďte následující kroky:

  1. Nakonfigurujte certifikát pro správu Azure z hlavního uzlu a v rámci předplatného Azure. Pro testovací nasazení můžete použít výchozí Microsoft HPC Azure certifikát podepsaný svým držitelem, který nainstaluje sady HPC Pack hlavního uzlu a pak nahrajte tento certifikát do vašeho předplatného Azure. Možnosti a kroky najdete v tématu [pokyny v knihovně TechNet](https://technet.microsoft.com/library/gg481759.aspx).

  2. Spustit **regedit** z hlavního uzlu, přejděte na HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo a přidejte hodnotu řetězce. Nastavte název hodnoty na "Kryptografický otisk" a hodnota dat kryptografický otisk certifikátu v kroku 1.

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Příkazy prostředí HPC PowerShell nastavit vlastnost AutoGrowShrink
Toto jsou příkazy prostředí HPC PowerShell ukázkový nastavit **AutoGrowShrink** a optimalizaci své chování s další parametry. V tématu [AutoGrowShrink parametry](#AutoGrowShrink-parameters) dále v tomto článku pro úplný seznam nastavení.

Ke spuštění těchto příkazů, spusťte z hlavního uzlu clusteru prostředí HPC PowerShell jako správce.

**Chcete-li povolit vlastnost AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**Chcete-li zakázat vlastnost AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**Chcete-li změnit zvětšit interval v minutách**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**Chcete-li změnit zmenšení interval v minutách**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**Chcete-li zobrazit aktuální konfiguraci AutoGrowShrink**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**Chcete vyloučit z AutoGrowShrink uzel skupiny**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> Tento parametr je podporované počínaje HPC Pack 2016
>

### <a name="autogrowshrink-parameters"></a>Parametry AutoGrowShrink
Toto jsou AutoGrowShrink parametry, které lze upravit pomocí **Set-HpcClusterProperty** příkaz.

* **EnableGrowShrink** -přepínače, které chcete povolit nebo zakázat **AutoGrowShrink** vlastnost.
* **ParamSweepTasksPerCore** -počtu úkolů čištění parametrů růst jednoho jádra. Ve výchozím nastavení se růst jednoho jádra daná úloha.

  > [!NOTE]
  > Změny HPC Pack QFE KB3134307 **ParamSweepTasksPerCore** k **TasksPerResourceUnit**. Ho je založený na typu prostředku úlohy a může být uzlu, soketu nebo jádra.
  >
  >
* **GrowThreshold** – prahová hodnota zařazených do fronty úloh pro spuštění automatického zvětšování. Výchozí hodnota je 1, což znamená, že, pokud jsou ve stavu zařazených do fronty, 1 nebo více úloh, automaticky rozšiřovat uzly.
* **GrowInterval** – Interval v minutách pro spuštění automatického zvětšování. Výchozí interval je 5 minut.
* **ShrinkInterval** – Interval v minutách pro spuštění automatického zmenšit. Výchozí interval je 5 minut. |
* **ShrinkIdleTimes** -počet průběžné kontroly zmenšení udávajících uzly jsou nečinnosti. Výchozí hodnota je 3 x. Například pokud **ShrinkInterval** je 5 minut, HPC Pack zkontroluje, zda je uzel nečinnosti každých 5 minut. Pokud uzly jsou ve stavu nečinnosti, po 3 průběžné kontroluje (15 minut), HPC Pack zmenší tento uzel.
* **ExtraNodesGrowRatio** -další procento uzly růst pro úlohy Message Passing Interface (MPI). Výchozí hodnota je 1, což znamená zvětšování HPC Pack uzlů pro úlohy MPI 1 %.
* **GrowByMin** – přepínač indikující, zda autogrow zásad je založena na minimální prostředky potřebné pro úlohu. Výchozí hodnota je nastavena hodnota false, což znamená, že HPC Pack zvětšování uzlů pro úlohy, které jsou založeny na maximální prostředky potřebné pro úlohy.
* **SoaJobGrowThreshold** -růst prahovou hodnotu příchozí požadavky SOA pro spuštění automatického procesu. Výchozí hodnota je 50000.

  > [!NOTE]
  > Tento parametr je podporované počínaje HPC Pack 2012 R2 Update 3.
  >
  >
* **SoaRequestsPerCore** -počet příchozích SOA požadavků růst jednoho jádra. Výchozí hodnota je 20000.

  > [!NOTE]
  > Tento parametr je podporované počínaje HPC Pack 2012 R2 Update 3.
  >
* **ExcludeNodeGroups** – uzly ve skupině zadaný uzel automaticky zvýšit nebo snížit.
  
  > [!NOTE]
  > Tento parametr je podporované počínaje HPC Pack 2016.
  >

### <a name="mpi-example"></a>Příklad MPI
Ve výchozím nastavení HPC Pack zvětšování 1 % navíc uzlů pro úlohy MPI (**ExtraNodesGrowRatio** je nastaven na hodnotu 1). Důvodem je, že MPI může vyžadovat více uzlů, a úlohu lze spustit pouze když jsou všechny uzly připravené. Při spuštění uzlů Azure příležitostně jeden uzel může potřebujete víc času spuštění než jiné způsobuje další uzly na nečinnost při čekání na tento uzel připravit. Způsobeného nárůstem další uzly, HPC Pack snižuje dobu čekání na tento prostředek a potenciálně šetří náklady. Chcete-li zvyšovat procento navíc uzlů pro úlohy MPI (například na 10 %), spusťte příkaz podobný

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Příklad SOA
Ve výchozím nastavení **SoaJobGrowThreshold** je nastaven na 50000 a **SoaRequestsPerCore** je nastaven na 20000. Pokud odešlete jeden úlohy architektury SOA s 70000 požadavky, je ve frontě úloh a 70000 jsou příchozí požadavky. V takovém případě HPC Pack zvětšování 1 jádro pro úlohu ve frontě a pro příchozí požadavky, zvětšování (70000-50000) nebo základní 20000 = 1, takže v celkem zvětšování 2 jádra pro tuto úlohu SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Spusťte skript AzureAutoGrowShrink.ps1
### <a name="prerequisites"></a>Požadavky

* **HPC Pack 2012 R2 Update 1 nebo novější clusteru** – **AzureAutoGrowShrink.ps1** skriptu je nainstalován ve složce % CCP_HOME % Koš. Z hlavního uzlu clusteru může být nasazena místně nebo ve virtuálním počítači Azure. V tématu [nastavení hybridního clusteru pomocí sady HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) začít pracovat se hlavnímu uzlu místní a uzly Azure "shluků". Najdete v článku [skript nasazení HPC Pack IaaS](hpcpack-cluster-powershell-script.md) rychle nasazení clusteru HPC Pack ve virtuálních počítačích Azure nebo použijte [šablony Azure rychlý Start](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).
* **Prostředí Azure PowerShell 1.4.0** -skript aktuálně závisí na konkrétní verzi prostředí Azure PowerShell.
* **Pro cluster s Azure burst uzly** – spusťte tento skript na klientském počítači, kde je nainstalován HPC Pack nebo z hlavního uzlu. Pokud běží na klientském počítači, zajistěte, že nastavíte proměnnou $env: CCP_SCHEDULER tak, aby odkazoval k hlavnímu uzlu. Uzly Azure "shluků" musí být přidaný do clusteru, ale může být ve stavu není nasazen.
* **Pro cluster s podporou nasazené ve virtuálních počítačích Azure (modelu nasazení Resource Manager)** -pro cluster s podporou virtuálních počítačů Azure nasazené v modelu nasazení Resource Manager, skript podporuje dvě metody pro ověřování Azure: přihlásit k účtu Azure a spustit skript pokaždé, když (spuštěním `Connect-AzureRmAccount`, nebo nakonfigurovat hlavní název služby k ověřování pomocí certifikátu. HPC Pack poskytne skript **ConfigARMAutoGrowShrinkCert.ps** k vytvoření objektu služby pomocí certifikátu. Skript vytvoří aplikaci služby Azure Active Directory (Azure AD) a hlavní název služby a přiřadí role Přispěvatel instanční objekt. Chcete-li spustit skript, otevřete prostředí Azure PowerShell jako správce a spusťte následující příkazy:

    ```powershell
    cd $env:CCP_HOME\bin

    Connect-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    Další podrobnosti o **ConfigARMAutoGrowShrinkCert.ps1**spusťte `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`,

* **Pro cluster s podporou nasazené ve virtuálních počítačích Azure (modelu nasazení classic)** -spuštění skriptu na hlavního uzlu virtuálního počítače, protože závisí na **Start-HpcIaaSNode.ps1** a **Stop-HpcIaaSNode.ps1** skriptů které se instalují existuje. Tyto skripty navíc vyžadují certifikát pro správu Azure nebo soubor nastavení publikování (viz [spravovat výpočetní uzly v prostředí HPC Pack clusteru v Azure](hpcpack-cluster-node-manage.md)). Zajistěte, aby všechny výpočetním uzlu je třeba virtuální počítače jsou již přidán do clusteru. Mohou být v zastaveném stavu.



### <a name="syntax"></a>Syntaxe
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>Parametry
* **NodeTemplates** -názvy šablon uzel k definování oboru pro uzly zvětšovat a zmenšovat. Není-li zadána (výchozí hodnota je @()), všechny uzly v **AzureNodes** uzlu skupiny jsou v oboru při **NodeType** má hodnotu AzureNodes a všech uzlech v **ComputeNodes**uzlu skupiny jsou v oboru při **NodeType** má hodnotu ComputeNodes.
* **JobTemplates** -názvy šablon úlohy k definování oboru pro uzly zvětšovat.
* **Typ uzlu** – typ uzlu zvětšovat a zmenšovat. Podporované hodnoty jsou:

  * **AzureNodes** – u uzlů Azure PaaS (shluků) v místní nebo v clusteru Azure IaaS.
  * **ComputeNodes** – pro výpočetní uzel virtuální počítače pouze v clusteru služby Azure IaaS.

* **NumOfQueuedJobsPerNodeToGrow** -počet úloh zařazených do fronty, které jsou potřeba růst jeden uzel.
* **NumOfQueuedJobsToGrowThreshold** – prahová hodnota počtu úloh zařazených do fronty ke spuštění procesu zvětšit.
* **NumOfActiveQueuedTasksPerNodeToGrow** – počet aktivních zařazených do fronty úkoly vyžadované ke zvětšení jeden uzel. Pokud **NumOfQueuedJobsPerNodeToGrow** je zadán s hodnotou větší než 0, tento parametr je ignorován.
* **NumOfActiveQueuedTasksToGrowThreshold** – prahová hodnota počtu aktivních úloh zařazených do fronty ke spuštění procesu zvětšit.
* **NumOfInitialNodesToGrow** – počáteční minimální počet uzlů roste s tím, pokud jsou všechny uzly v oboru **není nasazena** nebo **zastaveno (Deallocated)**.
* **GrowCheckIntervalMins** – interval v minutách mezi kontrolami zvětšovat.
* **ShrinkCheckIntervalMins** – interval v minutách mezi kontrolami zmenšení.
* **ShrinkCheckIdleTimes** -počet průběžné zmenšit kontroly (oddělených **ShrinkCheckIntervalMins**) k označení uzly jsou nečinnosti.
* **UseLastConfigurations** -předchozí konfigurace uložené v souboru argument.
* **ArgFile**– název souboru argument používá k uložení a aktualizovat konfiguraci pro spuštění skriptu.
* **LogFilePrefix** – předpona názvu souboru protokolu. Můžete zadat cestu. Ve výchozím nastavení protokol je zapsán do aktuální pracovní adresář.

### <a name="example-1"></a>Příklad 1
Následující příklad konfiguruje uzlů Azure shluků nasazené pomocí výchozí šablony AzureNode zvětšovat a zmenšovat automaticky. Pokud jsou všechny uzly v původně **není nasazena** stavu, jsou spuštěny aspoň 3 uzly. Pokud počet úloh zařazených do fronty překračuje 8, skript spustí uzlů, dokud se jejich počet překračuje poměr zařazených do fronty úloh **NumOfQueuedJobsPerNodeToGrow**. Pokud je zjištěno nečinnosti 3 po sobě jdoucích nečinnosti časů uzlu, je zastavena.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Příklad 2
Následující příklad konfiguruje Azure výpočetním uzlu virtuální počítače nasazené pomocí výchozí šablony ComputeNode zvětšovat a zmenšovat automaticky.
Úlohy, konfigurovat tak, že výchozí šablony úlohy definovat rozsah zatížení v clusteru. Pokud jsou všechny uzly původně zastavena, nejméně 5 uzly jsou spuštěny. Pokud počet aktivních úloh zařazených do fronty překročí 15, skript spustí uzlů, dokud se jejich počet překračuje poměr aktivních úloh zařazených do fronty pro **NumOfActiveQueuedTasksPerNodeToGrow**. Pokud je zjištěno nečinnosti v 10krát po sobě jdoucích nečinnosti uzlu, je zastavena.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
