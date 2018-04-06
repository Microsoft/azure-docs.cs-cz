---
title: Spustit HVĚZDIČKY – CCM + s HPC Pack na virtuální počítače s Linuxem | Microsoft Docs
description: Nasazení clusteru s podporou sady Microsoft HPC Pack v Azure a spuštění HVĚZDIČKOU – CCM + úlohy na několika Linux výpočetní uzly přes sítě RDMA.
services: virtual-machines-linux
documentationcenter: ''
author: xpillons
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 75523406-d268-4623-ac3e-811c7b74de4b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/13/2016
ms.author: xpillons
ms.openlocfilehash: 8689d7abfd5ab45277df3b5672a1f6e7e874d88e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Spustit HVĚZDIČKY – CCM + pomocí sady Microsoft HPC Pack na Linux RDMA cluster v Azure
Tento článek ukazuje, jak nasadit cluster sady Microsoft HPC Pack na Azure a spusťte [HVĚZDIČKY CD adapco-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) úlohy na několika výpočetních uzlech Linux, které jsou vzájemně propojeny InfiniBand.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack poskytuje funkce pro spouštění různých rozsáhlé HPC a paralelní aplikace, včetně aplikací MPI, v clusterech virtuální počítače Microsoft Azure. HPC Pack také podporuje spuštěné aplikace prostředí HPC Linux virtuálních počítačů Linux výpočetní uzly, které jsou nasazené na clusteru HPC Pack. Úvod do používání Linux výpočetní uzly s HPC Pack, naleznete v části [začít pracovat s Linux výpočetní uzly v clusteru služby HPC Pack v Azure](hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Nastavení clusteru služby HPC Pack
Stáhněte si skripty nasazení HPC Pack IaaS z [Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=44949) a extrahovat je místně.

Prostředí Azure PowerShell je požadována. Pokud PowerShell není nakonfigurovaný v místním počítači, najdete v článku [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

V době psaní tohoto textu Linux obrázky z Azure Marketplace, (který obsahuje ovladače InfiniBand pro Azure) jsou pro SLES 12, CentOS 6.5 a CentOS 7.1. Tento článek je založena na použití SLES 12. Pokud chcete načíst název všechny Image Linux, které podporují prostředí HPC v Marketplace, spuštěním následujícího příkazu Powershellu:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

Výstup obsahuje umístění, ve kterém jsou k dispozici tyto bitové kopie a název bitové kopie (**ImageName**) mají být použity v šabloně nasazení později.

Před nasazením clusteru, budete muset vytvořit soubor HPC Pack nasazení šablony. Protože jsme se cílení na clusteru s podporou malé, hlavního uzlu bude řadič domény a hostování místní databáze SQL.

Následující šablony bude nasazení hlavního uzlu, vytvořte soubor XML s názvem **MyCluster.xml**a nahraďte hodnoty **SubscriptionId**, **StorageAccount**, **umístění**, **VMName**, a **ServiceName** s tímto počítačem.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Spuštění vytvoření hlavního uzlu tak, že v příkazovém řádku se zvýšenými oprávněními spustíte příkaz prostředí PowerShell:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Po 20-30 minutách hlavního uzlu musí být připravené. Můžete připojit k němu z portálu Azure klepnutím **Connect** ikona virtuálního počítače.

Nakonec bude pravděpodobně nutné opravit předávání DNS. Uděláte to tak, spusťte Správce DNS.

1. Klikněte pravým tlačítkem na název serveru ve Správci DNS, vyberte **vlastnosti**a klikněte **předávání** kartě.
2. Klikněte **upravit** tlačítko Odebrat všechny servery pro předávání a potom klikněte na **OK**.
3. Ujistěte se, že **pomocí odkazů na kořenový server, pokud jsou k dispozici žádné servery pro předávání** zaškrtávací políčko je vybraná a pak klikněte na tlačítko **OK**.

## <a name="set-up-linux-compute-nodes"></a>Nastavit Linuxových výpočetních uzlů
Výpočetní uzly Linux nasadíte pomocí stejné šablony nasazení, který jste použili k vytvoření hlavního uzlu.

Zkopírujte soubor **MyCluster.xml** ze svého místního počítače k hlavnímu uzlu a aktualizací **NodeCount** značka s číslem uzlů, které chcete nasadit (< = 20). Buďte opatrní tak, aby měl dostatek dostupné jader v rámci svojí Azure kvóty, protože každá instance A9 spotřebuje 16 jader v rámci vašeho předplatného. Pokud chcete použít další virtuální počítače ve stejném rozpočtu, můžete použít A8 instancí (8 jader) namísto A9.

Z hlavního uzlu zkopírujte skriptů nasazení HPC Pack IaaS.

V příkazovém řádku se zvýšenými oprávněními spusťte následující příkazy prostředí Azure PowerShell:

1. Spustit **Add-AzureAccount** pro připojení k předplatnému Azure.
2. Pokud máte více předplatných, spusťte **Get-AzureSubscription** je.
3. Nastavit výchozí předplatné spuštěním **Select-AzureSubscription - Název_předplatného xxxx-výchozí** příkaz.
4. Spustit **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** zahájíte nasazení Linuxových výpočetních uzlů.
   
   ![Nasazení hlavního uzlu v akci][hndeploy]

Otevřete nástroj Správce clusterů HPC Pack. Za několik minut bude pravidelně Linux výpočetní uzly zobrazí v seznamu výpočetních uzlů clusteru. V režimu nasazení classic se vytvoří virtuální počítače IaaS postupně. Proto pokud počet uzlů je důležité, pak získávání všechny nasazené může trvat dlouhou dobu.

![Linuxové uzly ve Správci clusteru HPC Pack][clustermanager]

Teď, když jsou všechny uzly jsou spuštěny v clusteru, existují další infrastrukturu nastavení je třeba provést.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Nastavit sdílená Azure pro Windows a Linux uzly
Služba Azure souborů můžete použít k ukládání skriptů, balíčky aplikací a datových souborů. Azure File nabízí funkce CIFS nad úložiště objektů Blob v Azure jako trvalé úložiště. Uvědomte si, že to není nejvíce škálovatelným řešením, ale je ta nejjednodušší a nevyžaduje vyhrazených virtuálních počítačích.

Vytvoření Azure sdílené složky podle pokynů v článku [Začínáme s Azure File storage ve Windows](../../../storage/files/storage-dotnet-how-to-use-files.md).

Zachovat název účtu úložiště jako **saname**, název sdílené složky souborů jako **sharename**a klíč účtu úložiště jako **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Připojení Azure sdílené složky z hlavního uzlu
Otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz k uložení pověření v úložišti místního počítače:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Potom připojit sdílenou složku Azure File, spusťte příkaz:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Připojení Azure sdílené složky na Linuxových výpočetních uzlů
Užitečné nástroj, který se dodává s HPC Pack se nástroj clusrun. Tento nástroj příkazového řádku můžete spustit stejný příkaz současně se sadou výpočetních uzlů. V našem případě se používá k připojení Azure sdílené složky a k uložení mohla zůstat platné i po restartování počítače.
V příkazovém řádku se zvýšenými oprávněními z hlavního uzlu spusťte následující příkazy.

Vytvoření adresáře připojení:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Chcete-li připojit sdílenou složku Azure File:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Chcete-li zachovat připojení sdílené složky:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Nainstalujte HVĚZDIČKY – CCM +
Instancemi Azure virtuální počítač A8 a A9 poskytnout podporu InfiniBand a RDMA. Ovladače jádra, které povolují tyto funkce jsou dostupné pro Windows Server 2012 R2, SUSE 12, CentOS 6.5 a CentOS 7.1 bitové kopie v Azure Marketplace. Microsoft MPI a Intel MPI (verze 5.x) jsou dvě knihovny MPI, které podporují tyto ovladače v Azure.

CD adapco HVĚZDIČKY – CCM + verze 11.x a později je instalován s verzí Intel MPI 5.x, tak, aby zahrnuté InfiniBand podpora pro Azure.

Získat Linux64 HVĚZDIČKY – CCM + balíček z [CD adapco portál](https://steve.cd-adapco.com). V našem případě jsme použili verze 11.02.010 ve smíšeném přesnost.

Z hlavního uzlu v **/hpcdata** Azure File sdílenou složku, vytvořit skript prostředí s názvem **setupstarccm.sh** s následujícím obsahem. Tento skript se spustí na každém výpočetním uzlu nastavit HVĚZDIČKY – CCM + místně.

#### <a name="sample-setupstarcmsh-script"></a>Ukázkový skript setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Nyní nastavit HVĚZDIČKY – CCM + na všechny Linux výpočetní uzly, otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Když je spuštěný příkaz, můžete sledovat využití procesoru pomocí heat mapa ze Správce clusteru. Za několik minut všechny uzly by měl být správně nastavena.

## <a name="run-star-ccm-jobs"></a>Spustit HVĚZDIČKY – CCM + úlohy
HPC Pack se používá pro možnosti plánovače úloh fungování HVĚZDIČKY – CCM + úlohy. To pokud chcete udělat, budeme potřebovat podporu několik skriptů, které se používají ke spuštění úlohy a spuštění HVĚZDIČKY – CCM +. Vstupní data se ukládají na sdílenou složku Azure File první pro jednoduchost.

Následující skript prostředí PowerShell slouží k fronty HVĚZDU – CCM + úlohy. Trvá tři argumenty:

* Název modelu
* Počet uzlů, který se má použít
* Počet jader na každém uzlu, který se má použít

Protože HVĚZDIČKY – CCM + můžete vyplnit šířky pásma paměti, je vhodnější použít menší počet jader na výpočetní uzly a přidat nové uzly. Přesný počet jader na uzel, bude záviset na třídu procesoru a rychlost propojení.

Uzly jsou přiděleny výhradně pro úlohu a nemohou být sdíleny s ostatními úlohami. Úloha není spuštěna jako úloha MPI přímo. **Runstarccm.sh** Spouštěč MPI se spustit skript prostředí.

Vstupní modelu a **runstarccm.sh** skriptů jsou uložené v **/hpcdata** sdílené složce, která byla dříve připojena.

Soubory protokolu jsou pojmenované s ID úlohy a jsou uloženy v **/hpcdata sdílení**, společně s HVĚZDIČKOU – CCM + výstupní soubory.

#### <a name="sample-submitstarccmjobps1-script"></a>Ukázkový skript SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job     
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Nahraďte **runner.java** vaše upřednostňované hvězdičkou-Spouštěče modelu CCM + Java a kód protokolování.

#### <a name="sample-runstarccmsh-script"></a>Ukázkový skript runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

V našem testu jsme použili token licence Power na vyžádání. Pro tento token, budete muset nastavit **$CDLMD_LICENSE_FILE** proměnnou prostředí **1999@flex.cd-adapco.com** a klíč v **- podkey** možnost příkazového řádku.

Po inicializaci, skript extrahuje--z **$CCP_NODES_CORES** proměnné prostředí tohoto HPC Pack nastavit – seznam uzlů k sestavení hostfile, který Spouštěč MPI používá. Tato hostfile bude obsahovat seznam názvů výpočetní uzel, které se používají pro úlohy, jeden název na každý řádek.

Formát **$CCP_NODES_CORES** následuje tento vzor:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Kde:

* `<Number of nodes>` je počet uzlů přidělených pro tuto úlohu.
* `<Name of node_n_...>` je název každého uzlu přidělené této úlohy.
* `<Cores of node_n_...>` je počet jader na uzel přidělené této úlohy.

Počet jader (**$NBCORES**) se také vypočítává podle počtu uzlů (**$NBNODES**) a počet jader na uzel (jako parametr **$NBCORESPERNODE**).

Možnosti MPI jsou ty, které se používají s Intel MPI ve službě Azure:

* `-mpi intel` Chcete-li určit Intel MPI.
* `-fabric UDAPL` používat příkazy Azure InfiniBand.
* `-cpubind bandwidth,v` za účelem optimalizace šířky pásma pro MPI s HVĚZDIČKOU – CCM +.
* `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"` Chcete-li pracovat s Azure InfiniBand MPI Intel a nastavit požadovaný počet jader na uzel.
* `-batch` Spusťte HVĚZDIČKY – CCM + v dávkovém režimu s žádné uživatelské rozhraní.

Nakonec chcete-li spustit úlohu, ujistěte se, že uzly jsou spuštěny a jsou online ve Správci clusteru. Z příkazového řádku prostředí PowerShell, spusťte toto:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Zastavit uzly
Později po dokončení testů, můžete použít následující příkazy prostředí PowerShell HPC Pack zastavení a spuštění uzly:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Další postup
Zkuste spuštěny další zátěže systému Linux. Například v tématu:

* [Spuštění NAMD pomocí sady Microsoft HPC Pack v systému Linux výpočetních uzlech v Azure](hpcpack-cluster-namd.md)
* [Spustit OpenFOAM na clusteru s podporou Linux RDMA v Azure pomocí sady Microsoft HPC Pack](hpcpack-cluster-openfoam.md)

<!--Image references-->
[hndeploy]:media/hpcpack-cluster-starccm/hndeploy.png
[clustermanager]:media/hpcpack-cluster-starccm/ClusterManager.png
