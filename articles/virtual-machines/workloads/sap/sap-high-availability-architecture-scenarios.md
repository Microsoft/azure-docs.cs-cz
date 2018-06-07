---
title: Azure Architektura vysoké dostupnosti virtuálních počítačů a scénáře pro SAP NetWeaver | Microsoft Docs
description: Architektura vysoké dostupnosti a scénáře pro SAP NetWeaver ve virtuálních počítačích Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6612e3fb5368d8d5a4f59c0e5eefc8ef24c04aec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656920"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Architektura vysoké dostupnosti a scénáře pro SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:http://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:http://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:http://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfigurace s vysokou dostupností více SID SAP)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="terminology-definitions"></a>Definic termínů

**Vysoká dostupnost**: odkazuje na sadu technologií, které minimalizují IT přerušení tím, že poskytuje kontinuity podnikových procesů IT služeb prostřednictvím redundantní, odolný proti chybám nebo převzetí služeb při selhání chráněné součástí uvnitř *stejné*datového centra. V našem případě datové centrum nachází v jedné oblasti Azure.

**Zotavení po havárii**: také odkazuje na minimalizovat narušení služeb IT a jejich využití, ale napříč *různé* datových center, které mohou být stovky miles od sebe navzájem. V našem případě datových Center může nacházet v různých oblastech Azure v rámci stejné geopolitické oblasti nebo v umístěních, jak vytvořit vy jako zákazník.


## <a name="overview-of-high-availability"></a>Přehled vysokou dostupnost
SAP vysoké dostupnosti v Azure lze rozdělit do tří typů:

* **Vysoká dostupnost infrastruktury Azure**: 

    Zajištění vysoké dostupnosti může obsahovat třeba výpočetní (VM), sítě, nebo úložiště a jeho výhody pro zvýšení dostupnosti aplikací SAP.

* **Využívá infrastrukturu Azure virtuální počítač restartovat, aby dosáhnout *vyšší dostupnosti* aplikací SAP**: 

    Pokud se rozhodnete nepoužívat funkce jako je Windows Server Failover Clustering (WSFC) nebo kardiostimulátor v systému Linux, je použít restartování virtuálního počítače Azure. Chrání systémy SAP proti plánovaných a neplánovaných výpadků infrastruktury Azure fyzického serveru a celkové základní platformy Azure.

* **Vysoká dostupnost aplikace SAP**: 

    K dosažení úplné SAP systém vysoké dostupnosti, je nutné chránit všechny důležité součásti systému SAP. Příklad:
    * Redundantní SAP aplikační servery.
    * Jedinečný komponenty. Příkladem může být jediný bod selhání (SPOF) součásti, např. instance SAP ASC nebo SCS nebo systému pro správu databází (databázového systému).

SAP vysoké dostupnosti v Azure se liší od SAP vysoké dostupnosti v místním fyzickém nebo virtuálním prostředí. Následující dokument [SAP NetWeaver vysokou dostupnost a provozní kontinuita ve virtuálních prostředích s VMware a Hyper-V v systému Windows] [ sap-ha-bc-virtual-env-hyperv-vmware-white-paper] popisuje standardní SAP vysokou dostupnost konfigurace ve virtualizovaném prostředí v systému Windows.

Neexistuje žádné integrované sapinst SAP konfigurace s vysokou dostupností pro Linux není pro systém Windows. Informace o SAP vysokou dostupnost místní pro Linux najdete v tématu [informace o partnerovi vysokou dostupnost][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Vysoká dostupnost infrastruktury Azure

### <a name="sla-for-single-instance-virtual-machines"></a>SLA pro jednoduchou virtuální počítače

Není aktuálně single-VM SLA 99,9 % Storage úrovně premium. Kde získáte představu o tom, co může být dostupnost jeden virtuální počítač, můžete vytvořit produkt k dispozici různé [smlouvy o úrovni služeb Azure][azure-sla].

Základ pro výpočet je 30 dní, měsíčně nebo 43 200 minut. Například výpadek hodnotu 0,05 % odpovídá 21,6 minut. Dostupnost jednotlivých služeb je jako obvykle vypočítat následujícím způsobem:

(Služba dostupnosti č. 1/100) * (služba dostupnosti č. 2/100) * (služba dostupnosti č. 3/100) \*...

Příklad:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 nebo celkové dostupnosti 99.75 %.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Více instancí virtuálních počítačů ve stejné sadě dostupnosti
Pro všechny virtuální počítače, které mají dva nebo více instancí nasazené ve stejné *skupinu dostupnosti*, nemůžeme zaručit, že budete mít připojení virtuálních počítačů do alespoň jedné instance alespoň 99,95 % času.

Pokud dva nebo více virtuálních počítačů jsou součástí stejné skupiny dostupnosti, každý virtuální počítač v sadě dostupnosti je přiřazen *aktualizace domény* a *doména selhání* základní platformou Azure.

* **Aktualizovat domén** zaručit, že nejsou ve stejnou dobu během plánované údržby Azure infrastruktury restartovat víc virtuálních počítačů. Restartuje se jenom jeden virtuální počítač najednou.

* **Poruch domén** zaručit, že virtuální počítače jsou nasazeny na hardwarové součásti, které nesdílí běžné power zdroje a síťový přepínač. Pokud servery, síťový přepínač nebo zdroji napájení podstoupit k neplánovanému výpadku, má vliv jenom jednoho virtuálního počítače.

Další informace najdete v tématu [Správa dostupnosti virtuálních počítačích s Windows v Azure][azure-virtual-machines-manage-availability].

Skupina dostupnosti se používá k dosažení vysoké dostupnosti:

* Redundantní SAP aplikační servery.  
* Clustery s dva nebo víc uzlů (virtuální počítače, např.), které chrání SPOFs například instance SAP ASC nebo SCS nebo databázového systému.

### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Plánovaných a neplánovaných údržby virtuálních počítačů

Dva typy událostí platformy Azure může ovlivnit dostupnost vašich virtuálních počítačů:

* **Plánované údržby** události jsou pravidelné aktualizace provedené microsoftem základní platformy Azure. Aktualizace zvýšit celkový spolehlivosti, výkonu a zabezpečení virtuální počítače spuštěné v infrastruktuře platformy.

* **Neplánovaná údržba** události dojít, když hardware nebo fyzické infrastruktuře základní virtuální počítač se nezdařilo nějakým způsobem. Může obsahovat místní sítě chyb, selhání místního disku nebo jiné chyby úrovně rack. Když se taková selhání detekuje, platformu Azure automaticky migruje virtuální počítač z není v pořádku fyzického serveru, který je hostitelem virtuálního počítače v pořádku fyzický server. Tyto události vyskytují jen vzácně, ale taky můžou způsobit virtuální počítač restartovat.

Další informace najdete v tématu [Správa dostupnosti virtuálních počítačích s Windows v Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Azure redundance úložiště
Data ve vašem účtu úložiště je vždycky replikují, aby zajistila stálost a vysoká dostupnost, splňuje smlouvy SLA pro úložiště Azure i při krátkodobém výpadku hardwaru.

Protože Azure úložiště udržuje tři bitové kopie dat ve výchozím nastavení, použití diskového pole RAID 5 nebo RAID 1 na více disků Azure je zbytečné.

Další informace najdete v tématu [replikace Azure Storage][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
Spravované disků je nový typ prostředku v Azure Resource Manager, který lze použít místo virtuální pevné disky (VHD), které jsou uložené v účtech úložiště Azure. Spravované disky automaticky zarovná ke skupině dostupnosti virtuálního počítače, které jsou připojené k. Zvyšují dostupnost virtuálního počítače a služby, které jsou v rámci něj spuštěna.

Další informace najdete v tématu [přehled Azure spravované disky][azure-storage-managed-disks-overview].

Doporučujeme používat spravované disky, protože se zjednodušit nasazení a správu virtuálních počítačů.

SAP aktuálně podporuje pouze spravované pro prémiové disky. Další informace najdete v tématu SAP Poznámka [1928533].

## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Použití infrastruktury Azure k dosažení vysoké dostupnosti *vyšší dostupnosti* aplikací SAP

Pokud se rozhodnete nepoužívat funkce jako je například WSFC nebo kardiostimulátor v systému Linux (aktuálně podporuje jenom pro SUSE Linux Enterprise Server [SLES] 12 a novější), je použít restartování virtuálního počítače Azure. Chrání systémy SAP proti plánovaných a neplánovaných výpadků infrastruktury Azure fyzického serveru a celkové základní platformy Azure.

Další informace o tento přístup, najdete v části [infrastruktury využívat Azure virtuální počítač restartovat, aby dosáhnout vyšší dostupnosti systému SAP][sap-higher-availability].

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Vysoká dostupnost aplikací SAP na Azure IaaS

K dosažení úplné SAP systém vysoké dostupnosti, je nutné chránit všechny důležité součásti systému SAP. Příklad:
  * Redundantní SAP aplikační servery.
  * Jedinečný komponenty. Příkladem může být jediný bod selhání (SPOF) součásti, např. instance SAP ASC nebo SCS nebo systému pro správu databází (databázového systému).

Další části popisují postup dosáhnout vysoké dostupnosti pro všechny tři důležité SAP součásti systému.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Architektura vysoké dostupnosti pro SAP aplikační servery

> Tato část platí pro:
>
> ![Windows][Logo_Windows] Windows a ![Linux][Logo_Linux] Linux
>

Obvykle nepotřebujete specifického řešení vysoké dostupnosti pro instance serveru a dialogové okno aplikace SAP. Dosažení vysoké dostupnosti pomocí redundance a nakonfigurujete více instancí dialogové okno v různých instancí virtuální počítače Azure. Měli byste mít aspoň dvě instance SAP aplikace nainstalované v dvě instance virtuálních počítačů Azure.

![Obrázek 1: Vysoká dostupnost SAP aplikační server][sap-ha-guide-figure-2000]

_**Obrázek 1:** vysokou dostupnost SAP aplikační server_

Je nutné umístit všechny virtuální počítače, které nastavit instancí hostitele SAP aplikačních serverů ve stejné Azure dostupnosti. Nastavení Azure dostupnosti zajišťuje, že:

* Všechny virtuální počítače jsou součástí stejné domény aktualizace.  
    Doména aktualizace zajistí, že virtuální počítače nejsou aktualizovány ve stejnou dobu během plánované údržby. výpadků.

    Základní funkce, která je založena na jiné aktualizace a domén selhání v rámci služby Azure škálovací jednotky, již byla zavedena v [aktualizovat domén] [ planning-guide-3.2.2] části.

* Všechny virtuální počítače jsou součástí stejné domény selhání.  
    Domény selhání zajistí, jsou nasazené virtuální počítače tak, aby žádný jediný bod selhání má vliv na dostupnost všech virtuálních počítačů.

Počet aktualizací a chyby domén, které můžete využít Azure dostupnosti nastavit v rámci jednotky škálování Azure je omezený. Pokud do skupiny jednoho dostupnosti zachovat přidávání virtuálních počítačů, dvě nebo více virtuálních počítačů bude nakonec stát ve stejné doméně selhání nebo aktualizace.

Pokud nasadíte několik instancí SAP aplikačních serverů v jejich vyhrazených virtuálních počítačích, za předpokladu, že obsahuje pět aktualizace domén ukáže na následujícím obrázku. Skutečný maximální počet aktualizací a chyby domén v rámci skupiny dostupnosti může v budoucnu změnit:

![Obrázek 2: Vysoké dostupnosti SAP aplikačních serverů v nastavení dostupnosti Azure][planning-guide-figure-3000]
_**obrázek 2:** vysokou dostupnost SAP aplikační servery v nastavení dostupnosti Azure_

Další informace najdete v tématu [Správa dostupnosti virtuálních počítačích s Windows v Azure][azure-virtual-machines-manage-availability].

Další informace najdete v tématu [skupiny dostupnosti Azure] [ planning-guide-3.2.3] části virtuální počítače Azure plánování a implementace pro SAP NetWeaver dokumentu.

**Nespravované jen disky:** účet úložiště Azure je potenciální jediný bod selhání, je důležité mít alespoň dva účty úložiště Azure, ve kterých jsou distribuovány aspoň dva virtuální počítače. V ideální instalační program by se nasadí disky každého virtuálního počítače, na kterém běží instance SAP dialogové okno jiný účet úložiště.

> [!IMPORTANT]
> Důrazně doporučujeme použít Azure spravované disky pro vysokou dostupnost instalací SAP. Protože spravované disky automaticky zarovnat ke skupině dostupnosti virtuálního počítače, které jsou připojené k, zvyšují dostupnost virtuálního počítače a služby, které jsou v rámci něj spuštěna.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Architektura vysoké dostupnosti pro instance SAP ASC nebo SCS v systému Windows

> ![Windows][Logo_Windows] Windows
>

Můžete k ochraně instance SAP ASC nebo SCS řešení služby WSFC. Řešení má dvě varianty:

* **Pomocí Clusterované sdílené disky clusteru instance SAP ASC nebo SCS**: Další informace o této architektury, najdete v části [instance SAP ASC nebo SCS clusteru v clusteru s podporou převzetí služeb při selhání systému Windows s použitím sdíleného disku clusteru] [ sap-high-availability-guide-wsfc-shared-disk].   

* **Clusteru instance SAP ASC nebo SCS pomocí sdílené složky**: Další informace o této architektury, najdete v části [instance SAP ASC nebo SCS clusteru v clusteru převzetí služeb při selhání se systémem Windows pomocí sdílené složky] [ sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Architektura vysoké dostupnosti pro instance SAP ASC nebo SCS v systému Linux

> ![Linux][Logo_Linux] Linux
>
Další informace o clusteringu instance SAP ASC nebo SCS pomocí rozhraní clusteru SLES najdete v tématu [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP] [ sap-suse-ascs-ha].

### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Konfigurace více SID SAP NetWeaver pro clusterovou instanci SAP ASC nebo SCS

> ![Windows][Logo_Windows] Windows
>
> Více SID v současné době jsou podporovány pouze pro služby WSFC. Více SID je podporována při použití sdílené složky a sdíleného disku.
>
Další informace o architektuře více SID vysokou dostupnost najdete v tématu:

* [SAP ASC nebo SCS instanci více SID vysokou dostupnost pro Windows Server Failover Clustering a sdílené složky][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASC nebo SCS instanci více SID vysokou dostupnost pro Windows Server Failover Clustering a sdíleného disku][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Instance databázového systému vysokou dostupnost

Systému správy databáze je také jeden kontaktní bod v systému SAP. Potřebujete chránit pomocí řešení vysoké dostupnosti. Následující obrázek znázorňuje řešení vysoké dostupnosti AlwaysOn serveru SQL v Azure, Windows Server Failover Clustering a Azure interní nástroj pro vyrovnávání zatížení. SQL Server AlwaysOn replikuje databázového systému souborů protokolu a data pomocí replikace vlastní databázového systému. V takovém případě nepotřebujete sdíleného disku clusteru, který zjednodušuje celé nastavení.

![Obrázek 3: Příklad databázového systému vysoké dostupnosti SAP, s SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Obrázek 3:** příklad databázového systému vysoké dostupnosti SAP, s SQL Server AlwaysOn_

Další informace o clusteringu databázového systému SQL Server v Azure pomocí modelu nasazení Azure Resource Manager najdete v těchto článcích:

* [Konfigurace skupiny dostupnosti AlwaysOn ve virtuálních počítačích Azure ručně pomocí Resource Manageru][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Konfigurace k nástroji pro vyrovnávání zatížení Azure interní pro skupinu dostupnosti AlwaysOn v Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Další informace o clusteringu databázového systému SAP HANA v Azure pomocí modelu nasazení Azure Resource Manager najdete v tématu [vysokou dostupnost SAP HANA na virtuálních počítačích Azure (VM)][sap-hana-ha].
