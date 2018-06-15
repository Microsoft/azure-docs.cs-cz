---
title: Skript prostředí PowerShell pro nasazení clusteru HPC pro Linux | Microsoft Docs
description: Spusťte skript prostředí PowerShell pro nasazení clusteru Linux HPC Pack 2012 R2 ve virtuálních počítačích Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 66affb47190ba0c6fccaae8e8267b310682aee46
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841839"
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Vytvoření systémem Linux vysoce výkonné výpočty (HPC) clusteru pomocí skriptu pro nasazení HPC Pack IaaS
Spusťte nasazení HPC Pack IaaS skript Powershellu pro nasazení dokončení clusteru HPC Pack 2012 R2 pro Linux zatížení ve virtuálních počítačích Azure. Cluster se skládá z služby Active Directory připojené k hlavnímu uzlu systémem Windows Server a Microsoft HPC Pack a výpočetní uzly, které spusťte jeden z Linuxových distribucích podporována sadou HPC Pack. Pokud chcete nasazení clusteru HPC Pack v úlohách Azure pro Windows, přečtěte si téma [vytvořit cluster Windows HPC pomocí skriptu pro nasazení HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
> [!IMPORTANT] 
> Skript prostředí PowerShell popsaný v tomto článku vytváří cluster s podporou Microsoft HPC Pack 2012 R2 v Azure pomocí modelu nasazení classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> Kromě toho skriptu popsaného v tomto článku nepodporuje HPC Pack 2016. Informace o šablonách Resource Manager pro prostředí HPC Pack 2012 R2 a prostředí HPC Pack 2016 najdete v tématu [možnosti nasazení clusteru HPC Pack v Azure](../hpcpack-cluster-options.md).


[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Příklad konfiguračního souboru
Následující konfigurační soubor vytvoří řadič domény a doménové struktury domény a nasadí cluster služby HPC Pack, který má jeden hlavního uzlu s místní databází a 10 Linuxových výpočetních uzlů. Cloudové služby jsou vytvořené přímo v umístění ve východní Asii. Výpočetní uzly Linux jsou vytvořené v dvě cloudové služby a dva účty úložiště (tedy *MyLnxCN 0001* k *MyLnxCN 0005* v *MyLnxCNService01* a *mylnxstorage01*, a *MyLnxCN-0006* k *MyLnxCN 0010* v *MyLnxCNService02* a *mylnxstorage02* ). Výpočetní uzly jsou vytvořeny z image OpenLogic CentOS Linux verze 7.0. 

Nahraďte vlastními hodnotami pro vaše předplatné jméno a název účtu a služby.

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
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Řešení potíží
* **Chyba "Virtuální síť neexistuje"**. Pokud spustíte skript nasazení HPC Pack IaaS, který chcete nasadit více clusterů v Azure souběžně v rámci jednoho předplatného, jeden nebo více nasazení může selhat s chybou "virtuální sítě *VNet\_název* neexistuje".
  Pokud k této chybě dojde, znovu spusťte skript pro neúspěšné nasazení.
* **Problémy s přístupem k Internetu z virtuální síť Azure**. Pokud vytvoříte clusteru služby HPC Pack s nový řadič domény pomocí skriptu nasazení, nebo manuálně povýšit hlavního uzlu virtuálního počítače pro řadič domény, může zaznamenat problémy s připojením k Internetu virtuální počítače ve virtuální síť Azure. Tato situace může nastat, pokud je automaticky nakonfigurovaný server DNS pro předávání na řadiči domény, a tento server DNS pro předávání nepřekládá správně.
  
    K tomuto problému, přihlaste se k řadiči domény a buď odebrat nastavení konfigurace služby pro předávání nebo konfigurace serveru DNS platný server pro předávání. K tomu, ve Správci serveru klikněte na **nástroje** > **DNS** otevřít Správce DNS, a potom dvakrát klikněte na **předávání**.

## <a name="next-steps"></a>Další postup
* V tématu [začít pracovat s Linux výpočetní uzly v clusteru služby HPC Pack v Azure](hpcpack-cluster.md) informace o podporovaných Linuxových distribucích přesouvání dat a odesílání úloh do clusteru HPC Pack operačního systému Linux výpočetních uzlů.
* Kurzy, které pomocí skriptu vytvořte cluster a spustit úlohy Linux HPC najdete v části:
  * [Spuštění NAMD pomocí sady Microsoft HPC Pack v systému Linux výpočetních uzlech v Azure](hpcpack-cluster-namd.md)
  * [Spuštění OpenFOAM pomocí sady Microsoft HPC Pack v systému Linux výpočetních uzlech v Azure](hpcpack-cluster-openfoam.md)
  * [Spustit HVĚZDIČKY – CCM + pomocí sady Microsoft HPC Pack v systému Linux výpočetních uzlech v Azure](hpcpack-cluster-starccm.md)

