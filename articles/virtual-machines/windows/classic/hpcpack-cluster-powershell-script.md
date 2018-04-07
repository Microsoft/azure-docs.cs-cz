---
title: Skript prostředí PowerShell pro nasazení clusteru Windows HPC | Microsoft Docs
description: Spusťte skript prostředí PowerShell pro nasazení clusteru Windows HPC Pack 2012 R2 ve virtuálních počítačích Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 286b2be8-2533-40df-b02a-26156b1f1133
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: e05562aeac0ea89ec1c3d80d2967c8f59c68d332
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Vytvoření Windows vysoce výkonné výpočty (HPC) clusteru pomocí skriptu pro nasazení HPC Pack IaaS
Spusťte nasazení HPC Pack IaaS skript Powershellu pro nasazení dokončení clusteru HPC Pack 2012 R2 pro úlohy Windows ve virtuálních počítačích Azure. Cluster se skládá z služby Active Directory připojené k hlavnímu uzlu systémem Windows Server a Microsoft HPC Pack a dalších Windows výpočetní prostředky, které zadáte. Pokud chcete nasazení clusteru HPC Pack v Azure pro Linux zatížení, přečtěte si téma [vytvořit cluster Linux HPC pomocí skriptu pro nasazení HPC Pack IaaS](../../linux/classic/hpcpack-cluster-powershell-script.md). 

> [!IMPORTANT] 
> Skript prostředí PowerShell popsaný v tomto článku vytváří cluster s podporou Microsoft HPC Pack 2012 R2 v Azure pomocí modelu nasazení classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> Kromě toho skriptu popsaného v tomto článku nepodporuje HPC Pack 2016. Informace o šablonách Resource Manager pro prostředí HPC Pack 2012 R2 a prostředí HPC Pack 2016 najdete v tématu [možnosti nasazení clusteru HPC Pack v Azure](../hpcpack-cluster-options.md).

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Příklad konfigurační soubory
V následujících příkladech nahraďte vlastními hodnotami pro Id předplatného nebo název a název účtu a služby.

### <a name="example-1"></a>Příklad 1
Následující konfigurační soubor nasazení clusteru služby HPC Pack, která má hlavního uzlu s místní databází a pět výpočetní uzly s operačním systémem Windows Server 2012 R2. Cloudové služby jsou vytvořené přímo v umístění západní USA. Z hlavního uzlu funguje jako řadič domény v doménové struktuře domény.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Příklad 2
Následující konfigurační soubor nasadí cluster služby HPC Pack v existující doménové struktuře domény. Cluster má 1 hlavního uzlu s místní databází a 12 výpočetní uzly s příponou BGInfo virtuálních počítačů použít.
Automatická instalace aktualizací systému Windows je zakázáno pro všechny virtuální počítače v doménové struktuře domény. Cloudové služby jsou vytvořené přímo v umístění ve východní Asii. Výpočetní uzly jsou vytvořené v tři cloudové služby a tři účty úložiště: *MyHPCCN 0001* k *MyHPCCN 0005* v *MyHPCCNService01* a  *mycnstorage01*; *MyHPCCN-0006* k *MyHPCCN0010* v *MyHPCCNService02* a *mycnstorage02*; a  *MyHPCCN-0011* k *MyHPCCN 0012* v *MyHPCCNService03* a *mycnstorage03*). Výpočetní uzly jsou vytvořeny ze stávající privátní image zachyceného v výpočetního uzlu. Automatického zvětšovat a zmenšovat s výchozím nastavení je povolena služba zvětšovat a zmenšovat intervalech.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Příklad 3
Následující konfigurační soubor nasadí cluster služby HPC Pack v existující doménové struktuře domény. Cluster obsahuje jeden hlavní uzel, jeden databázový server s 500 GB datový disk, dvě zprostředkovatelské uzly s operačním systémem Windows Server 2012 R2 a pět výpočetní uzly s operačním systémem Windows Server 2012 R2. Cloudové služby MyHPCCNService se vytvoří ve skupině vztahů *MyIBAffinityGroup*, a vytvoří se ve skupině vztahů jiných cloudových služeb *MyAffinityGroup*. Jsou povoleny REST API Scheduleru úloh HPC a webového portálu HPC v hlavního uzlu.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Příklad 4
Následující konfigurační soubor nasadí cluster služby HPC Pack v existující doménové struktuře domény. Cluster má dvě hlavní uzel s místní databází, dvě šablony Azure uzlu vytvářejí a tři uzly Azure střední velikosti jsou vytvořeny pro šablonu Azure uzlu *AzureTemplate1*. Soubor skriptu se spouští z hlavního uzlu po dokončení konfigurace hlavního uzlu.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Řešení potíží
* **Chyba "Virtuální síť neexistuje"** – Pokud spustíte skript, který chcete nasadit více clusterů v Azure souběžně v rámci jednoho předplatného, jeden nebo více nasazení může selhat s chybou "virtuální sítě *VNet\_název* neexistuje ".
  Pokud k této chybě dojde, spusťte skript znovu pro neúspěšné nasazení.
* **Problémy s přístupem k Internetu z virtuální síť Azure** – Pokud vytvoříte cluster s nový řadič domény pomocí skriptu nasazení, nebo manuálně povýšit hlavního uzlu virtuálního počítače pro řadič domény, může zaznamenat problémy s připojením Virtuální počítače k Internetu. Tomuto problému může dojít, pokud je automaticky nakonfigurovaný server DNS pro předávání na řadiči domény, a tento server DNS pro předávání nepřekládá správně.
  
    K tomuto problému, přihlaste se k řadiči domény a buď odebrat nastavení konfigurace služby pro předávání nebo konfigurace serveru DNS platný server pro předávání. Toto nastavení nakonfigurovat ve Správci serveru klikněte na **nástroje** >
    **DNS** otevřít Správce DNS, a potom dvakrát klikněte na **předávání**.
* **Problémy s přístupem k síti RDMA z virtuálních počítačů náročné** – Pokud přidáte výpočetní systém Windows Server nebo zprostředkovatelský uzel virtuálních počítačů pomocí podporou RDMA velikost například A8 a A9, může zaznamenat problémy s připojením k síti aplikace RDMA těchto virtuálních počítačů. Jedním z důvodů, že k tomuto problému dochází, je, pokud rozšíření HpcVmDrivers není správně nainstalován, pokud jsou virtuální počítače přidat do clusteru. Například rozšíření zablokovaná v instalaci stavu.
  
    Chcete-li tento problém obejít, nejprve zkontrolujte stav rozšíření ve virtuálních počítačích. Pokud rozšíření není nainstalován správně, zkuste odebrat uzel z clusteru HPC a poté znovu přidejte uzly. Například můžete přidat výpočetním uzlu virtuální počítače pomocí skriptu přidat HpcIaaSNode.ps1 z hlavního uzlu.

## <a name="next-steps"></a>Další postup
* Zkuste spustit test zatížení v clusteru. Příklad najdete v tématu HPC Pack [Příručka Začínáme](https://technet.microsoft.com/library/jj884144).
* Skript nasazení clusteru a spuštění úlohy HPC, na adrese [začít pracovat s clusteru služby HPC Pack v Azure a spuštění aplikace Excel a SOA úloh](../../virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Zkuste nástroje HPC Pack spustit, zastavit, přidat a odebrat výpočetních uzlů z clusteru, který vytvoříte. V tématu [spravovat výpočetní uzly v prostředí HPC Pack clusteru v Azure](hpcpack-cluster-node-manage.md).
* Získat k odesílání úloh do clusteru z místního počítače, naleznete v tématu [clusteru HPC odeslání úloh na místní počítač do sady HPC Pack v Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

