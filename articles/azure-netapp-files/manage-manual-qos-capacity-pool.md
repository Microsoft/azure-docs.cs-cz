---
title: Správa ručního fondu kapacity technologie QoS pro Azure NetApp Files | Microsoft Docs
description: Popisuje, jak spravovat fond kapacit, který využívá ruční typ QoS, včetně nastavení ručního fondu kapacity QoS a změny fondu kapacit pro použití ruční technologie QoS.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 1c20190ba1a997ef85f4023a54ecea3c2a77ae53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183824"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>Správa fondu ručně zřizovaného kapacity QoS

Tento článek popisuje, jak spravovat fond kapacit, který využívá ruční typ QoS.  

Informace o typech QoS najdete v tématu [hierarchie úložiště Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) a [důležitých informací o výkonu pro Azure NetApp Files](azure-netapp-files-performance-considerations.md) .  

## <a name="register-the-feature"></a>Zaregistrujte funkci.
Funkce ručního typu QoS je aktuálně ve verzi Preview. Pokud tuto funkci používáte poprvé, musíte ji nejprve zaregistrovat.
  
1.  Zaregistrujte funkci:

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. Ověřte stav registrace funkce: 

    > [!NOTE]
    > **RegistrationState** může být ve `Registering` stavu až 60 minut, než se změní na `Registered` . Než budete pokračovat, počkejte, než se stav **zaregistruje** .

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
Můžete také použít [příkazy rozhraní příkazového řádku Azure](/cli/azure/feature) `az feature register` a `az feature show` zaregistrovat funkci a zobrazit stav registrace. 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>Nastavení nového ručně vytvořeného fondu kapacity QoS 

Vytvoření nového fondu kapacity pomocí ručního typu technologie QoS:

1. Postupujte podle kroků v [nastavení fondu kapacit](azure-netapp-files-set-up-capacity-pool.md).  

2. V okně Nový fond kapacit vyberte **Ruční typ QoS** .  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>Změna fondu kapacity pro použití ruční technologie QoS

Fond kapacit, který aktuálně používá typ auto QoS, můžete změnit tak, aby používal ruční typ QoS.  

> [!IMPORTANT]
> Nastavení typu kapacity na ruční QoS je trvalá změna. Ruční nástroj pro kapacitu typu QoS nejde převést na fond kapacity auto QoS.  
> V době převodu můžou být úrovně propustnosti omezené, aby odpovídaly limitům propustnosti pro svazky ručního typu QoS. Azure NetApp Files najdete v části [omezení prostředků](azure-netapp-files-resource-limits.md#resource-limits).

1. V okně pro správu účtu NetApp klikněte na **fondy kapacit** , abyste zobrazili existující fondy kapacity.   
 
2.  Klikněte na fond kapacit, který chcete změnit, aby se používala ruční technologie QoS.

3.  Klikněte na **změnit typ QoS**. Pak nastavte **nový typ QoS** na **Ruční**. Klikněte na **OK**. 

![Změnit typ QoS](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>Monitorování propustnosti ručního fondu kapacity QoS  

K dispozici jsou metriky, které vám pomůžou monitorovat propustnost čtení a zápisu svazku.  Viz [metriky pro Azure NetApp Files](azure-netapp-files-metrics.md).  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>Úprava přidělené propustnosti ručního svazku technologie QoS 

Pokud je svazek obsažený v manuálním fondu kapacity QoS, můžete podle potřeby upravit přidělenou propustnost svazku.

1. Ze stránky **svazky** vyberte svazek, jehož propustnost chcete upravit.   

2. Klikněte na tlačítko **změnit propustnost**. Určete **propustnost (MIB/S)** , kterou chcete. Klikněte na **OK**. 

    ![Změna propustnosti technologie QoS](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>Další kroky  

* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
* [Metriky pro Azure NetApp Files](azure-netapp-files-metrics.md)
* [Aspekty výkonu pro Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Řešení potíží s fondy kapacity](troubleshoot-capacity-pools.md)
* [Hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Model nákladů pro Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vytvoření svazku NFS](azure-netapp-files-create-volumes.md)
* [Vytvoření svazku SMB](azure-netapp-files-create-volumes-smb.md)
* [Vytvoření svazku se dvěma protokoly](create-volumes-dual-protocol.md)
