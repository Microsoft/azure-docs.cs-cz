---
title: Architektury řešení pomocí Azure NetApp Files | Microsoft Docs
description: Poskytuje odkazy na osvědčené postupy pro architektury řešení pomocí Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 01/19/2021
ms.author: b-juche
ms.openlocfilehash: f50cde2fa67351552d1f31500da09a5f800b2bcc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600109"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Architektury řešení s využitím Azure NetApp Files
Tento článek poskytuje odkazy na osvědčené postupy, které vám pomůžou pochopit architektury řešení pro použití Azure NetApp Files.  

Následující diagram shrnuje kategorie architektury řešení, které Azure NetApp Files nabízí:

![Kategorie architektury řešení](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Aplikace a databázová řešení pro Linux OSS

V této části najdete odkazy na řešení pro aplikace a databáze se systémem Linux OSS. 

### <a name="oracle"></a>Oracle

* [Výkon databází Oracle na jednoduchých svazcích Azure NetApp Files](performance-oracle-single-volumes.md)
* [Průvodce nasazením Oracle v Azure v praxi pomocí Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Image virtuálních počítačů Oracle a jejich nasazení na Microsoft Azure: možnosti konfigurace sdíleného úložiště](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Výhody použití Azure NetApp Files s Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>Machine Learning
*   [Cloudera Machine Learning](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)

## <a name="windows-apps-and-sql-server-solutions"></a>Aplikace pro Windows a SQL Server řešení

Tato část poskytuje odkazy na aplikace systému Windows a SQL Server řešení.

### <a name="file-sharing-and-global-file-caching"></a>Sdílení souborů a globální ukládání souborů do mezipaměti

* [Sestavte si vlastní Azure NFS? Sdílené složky wrestling Linux do cloudu](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Nasazení globální souborové mezipaměti/Azure NetApp Files nasazení](https://youtu.be/91LKb1qsLIM)
* [Dodržování předpisů cloudu pro Azure NetApp Files](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [Nasazení SQL Server přes protokol SMB pomocí Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
<!-- * [Deploy SQL Server Always-On Failover Cluster over SMB with Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q) --> 
<!-- * [Deploy Always-On Availability Groups with Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc) --> 

## <a name="sap-on-azure-solutions"></a>Řešení SAP v Azure

Tato část poskytuje odkazy na řešení SAP v Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>Obecné SAP a SAP NetWeaver 

* [Aplikace SAP na Microsoft Azure s využitím Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na Red Hat Enterprise Linux s Azure NetApp Files pro aplikace SAP](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure ve Windows pomocí protokolu SMB (Azure NetApp Files) pro aplikace SAP](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v Red Hat Enterprise Linux pro Průvodce pro aplikace SAP s více SID](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [Konfigurace úložiště virtuálních počítačů Azure SAP HANA](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [Vysoká dostupnost SAP HANA škálování s využitím Azure NetApp Files na Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [SAP HANA horizontálního navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [SAP HANA horizontálního navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)

### <a name="sap-anydb"></a>SAP AnyDB

* [Nasazení SAP AnyDB (Oracle 19c) pomocí Azure NetApp Files](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)

### <a name="sap-iq-nls"></a>SAP SWEETIQ – NLS

*   [Nasazení řešení SAP SWEETIQ-NLS HA pomocí Azure NetApp Files v SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)

### <a name="sap-tech-community-and-blog-posts"></a>Technická komunita SAP a příspěvky na blogu 

* [Azure NetApp Files – zálohování SAP HANA během několika sekund](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – obnovení databáze HANA ze zálohy snímku](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – SAP HANA přesměrování zálohování pomocí synchronizace cloudu](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Urychlení SAP HANA systémových kopií pomocí Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Cloudové svazky ONTAP a Azure NetApp Files: Snadná migrace systému SAP HANA](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>Řešení infrastruktury virtuálních klientských počítačů

V této části najdete odkazy na řešení infrastruktury virtuálních klientských počítačů.

### <a name="windows-virtual-desktop"></a>Windows Virtual Desktop

* [Výhody použití Azure NetApp Files s Windows Virtual Desktopem](solutions-windows-virtual-desktop.md)
* [Možnosti úložiště pro kontejnery profilů FSLogix ve virtuálním počítači s Windows](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [Vytvoření kontejneru profilu FSLogix pro fond hostitelů pomocí Azure NetApp Files](../virtual-desktop/create-fslogix-profile-container.md)
* [Windows Virtual Desktop v podnikovém měřítku](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [Microsoft FSLogix pro doporučené postupy pro podnikové Azure NetApp Files](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [Nastavení Azure NetApp Files pro připojení aplikace MSIX](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

## <a name="hpc-solutions"></a>Řešení HPC

V této části najdete referenční informace pro řešení s vysokým výkonem (HPC Computing Computing). 

### <a name="generic-hpc"></a>Obecné HPC

* [Azure NetApp Files: využití cloudového úložiště na maximum](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Spouštění úloh MPI s využitím Azure Batch a Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle Cloud: CycleCloud prostředí HPC na Azure NetApp Files](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Ropný a plynový

* [Výpočty s vysokým výkonem (HPC): ropný a plynový plyn v Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Spuštění simulace softwarového zásobníku v Azure](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Automatizace elektronického návrhu (EDA)

* [Výhody použití Azure NetApp Files pro automatizaci elektronických návrhů](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: EDA HPC Lab s Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [Azure pro Industry Semiconductor](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Analýzy

* [Azure NetApp Files: sdílený systém souborů, který se má používat s mřížkou SAS v Microsoft Azure](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [Osvědčené postupy pro používání Microsoft Azure s SAS®](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

## <a name="azure-platform-services-solutions"></a>Řešení služeb platformy Azure

Tato část poskytuje řešení pro služby platformy Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Služby Azure Kubernetes a Kubernetes

* [Integrace Azure NetApp Files se službou Azure Kubernetes](../aks/azure-netapp-files.md)
* [Špičkový výkon Kubernetes v Azure s využitím Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident – úložiště Orchestrator pro kontejnery](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Platforma Magento elektronického obchodování ve službě Azure Kubernetes Service (AKS)](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-batch"></a>Azure Batch

* [Spouštění úloh MPI s využitím Azure Batch a Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
