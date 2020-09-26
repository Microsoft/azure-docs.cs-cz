---
title: Architektura a scénáře HA virtuálních počítačů Azure pro SAP NetWeaver | Microsoft Docs
description: Architektura a scénáře s vysokou dostupností pro SAP NetWeaver v Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c837a4020ab1aaad1798d6b19e72e09edafea1f5
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360348"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Architektura a scénáře s vysokou dostupností pro SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

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
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
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
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:../../windows/manage-availability.md
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfigurace s vysokou dostupností pro SAP multi-SID)


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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../manage-availability.md


## <a name="terminology-definitions"></a>Definice terminologie

**Vysoká dostupnost**: odkazuje na sadu technologií, které minimalizují přerušení provozu tím, že zajišťuje kontinuitu podnikových služeb IT prostřednictvím redundantních součástí, odolných proti chybám nebo převzetí služeb při selhání ve *stejném* datovém centru. V našem případě se datové centrum nachází v jedné oblasti Azure.

**Zotavení po havárii**: také odkazuje na minimalizaci přerušení služeb IT a jejich obnovení, ale v *různých* datových centrech, které mohou být od sebe od sebe stovky kilometrů. V našem případě se datová centra můžou nacházet v různých oblastech Azure v rámci stejné geopolitické oblasti nebo v místech, jak si ji zavedli jako zákazník.


## <a name="overview-of-high-availability"></a>Přehled vysoké dostupnosti
Vysoká dostupnost SAP v Azure může být rozdělená na tři typy:

* **Vysoká dostupnost infrastruktury Azure**: 

    Vysoká dostupnost může například zahrnovat výpočetní prostředky (virtuální počítače), síť nebo úložiště a jejich výhody pro zvýšení dostupnosti aplikací SAP.

* **Využití restartování virtuálních počítačů infrastruktury Azure k dosažení *vyšší dostupnosti* aplikací SAP**: 

    Pokud se rozhodnete nepoužívat funkce, jako je například služba Windows Server Failover Clustering (WSFC) nebo Pacemaker v systému Linux, je využíván restart virtuálního počítače Azure. Chrání systémy SAP proti plánovanému a neplánovanému výpadku infrastruktury fyzického serveru Azure a celkové základní platformy Azure.

* **Vysoká dostupnost aplikace SAP**: 

    Pro zajištění vysoké dostupnosti systému SAP je nutné chránit všechny důležité součásti systému SAP. Příklad:
    * Redundantní aplikační servery SAP.
    * Jedinečné součásti. Příkladem může být komponenta s jedním bodem selhání (SPOF), jako je například instance SAP ASCS/SCS nebo systém správy databáze (DBMS).

Zařízení SAP s vysokou dostupností v Azure se liší od vysoké dostupnosti SAP v místním fyzickém nebo virtuálním prostředí. Následující papírová [NetWeaver vysoká dostupnost a provozní kontinuita ve virtuálních prostředích s VMware a Hyper-V v systému Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] popisuje standardní konfigurace SAP ve virtualizovaném prostředí ve Windows.

Pro Linux není k dispozici žádná sapinstá konfigurace SAP s vysokou dostupností, protože je k dispozici pro Windows. Informace o službě SAP s vysokou dostupností v místním prostředí pro Linux najdete v tématu [informace o partnerovi vysoké dostupnosti][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Vysoká dostupnost infrastruktury Azure

### <a name="sla-for-single-instance-virtual-machines"></a>SLA pro virtuální počítače s jednou instancí

V současné době je smlouva SLA pro jeden virtuální počítač 99,9% s Premium Storage. Chcete-li získat představu o dostupnosti jednoho virtuálního počítače, můžete si sestavit produkt různých dostupných [smluv o úrovni služeb Azure][azure-sla].

Základem pro výpočet je 30 dní za měsíc nebo 43 200 minut. Například výpadek 0,05% odpovídá 21,6 minut. V obvyklých případech je dostupnost různých služeb počítána následujícím způsobem:

(Služba dostupnosti #1/100) * (služba dostupnosti #2/100) * (služba dostupnosti #3/100) \* ...

Příklad:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 nebo Celková dostupnost 99,75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Několik instancí virtuálních počítačů ve stejné skupině dostupnosti
U všech virtuálních počítačů, které mají nasazené dvě nebo víc instancí ve stejné *skupině dostupnosti*, Zaručujeme, že budete mít připojení virtuálního počítače k minimálně jedné instanci minimálně 99,95% času.

Pokud jsou dva nebo více virtuálních počítačů součástí stejné skupiny dostupnosti, je každému virtuálnímu počítači ve skupině dostupnosti přiřazena *Doména aktualizace* a *Doména selhání* základní platformou Azure.

* **Aktualizace domén** zaručuje, že během plánované údržby infrastruktury Azure se nerestartuje víc virtuálních počítačů současně. V jednom okamžiku se restartuje jenom jeden virtuální počítač.

* **Domény selhání** zaručují, že virtuální počítače se nasazují na hardwarové součásti, které nesdílejí společný zdroj napájení a síťový přepínač. Když se servery, síťový přepínač nebo zdroj napájení dotýkají neplánovaného výpadku, bude ovlivněn pouze jeden virtuální počítač.

Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů s Windows v Azure][azure-virtual-machines-manage-availability].

Skupina dostupnosti se používá k dosažení vysoké dostupnosti pro:

* Redundantní aplikační servery SAP.  
* Clustery se dvěma nebo více uzly (například virtuální počítače), které chrání SPOFs, jako je například instance SAP ASCS/SCS nebo DBMS.


### <a name="azure-availability-zones"></a>Zóny dostupnosti Azure
V rámci Azure probíhá zavádění konceptů [zóny dostupnosti Azure](../../../availability-zones/az-overview.md) v různých [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/). V oblastech Azure, kde jsou nabízeny Zóny dostupnosti, mají oblasti Azure více datových center, které jsou nezávislé na napájení zdroje energie, chlazení a sítě. Důvodem pro nabídku různých zón v rámci jedné oblasti Azure je, abyste mohli nasadit aplikace v rámci dvou nebo tří nabízených Zóny dostupnosti. Za předpokladu, že problémy ve zdrojích napájení nebo v síti ovlivňují jenom jednu infrastrukturu zóny dostupnosti, vaše nasazení aplikace v oblasti Azure je pořád plně funkční. Nakonec s menší kapacitou, protože některé virtuální počítače v jedné zóně můžou být ztraceny. Ale virtuální počítače v ostatních dvou zónách jsou pořád v provozu. Oblasti Azure, které nabízí zóny, jsou uvedené v části [zóny dostupnosti Azure](../../../availability-zones/az-overview.md).

Při použití Zóny dostupnosti existuje několik věcí, které je potřeba vzít v úvahu. Seznam hledisek jako:

- Skupiny dostupnosti Azure nemůžete nasadit v rámci zóny dostupnosti. Musíte vybrat buď zónu dostupnosti, nebo skupinu dostupnosti jako rámec nasazení pro virtuální počítač.
- [Základní Load Balancer](../../../load-balancer/load-balancer-overview.md) nelze použít k vytvoření řešení clusteru s podporou převzetí služeb při selhání založeného na clusterových službách s podporou převzetí služeb při selhání nebo Linux Místo toho musíte použít [SKU Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) .
- Zóny dostupnosti Azure neposkytují žádné záruky určité vzdálenosti mezi různými zónami v rámci jedné oblasti.
- Latence sítě mezi různými Zóny dostupnosti Azure v různých oblastech Azure se může lišit od oblasti Azure do oblasti. V některých případech se může jednat o případ, kdy jako zákazník bude možné rozumně spustit aplikační vrstvu SAP nasazenou v různých zónách, protože latence sítě z jedné zóny na aktivní virtuální počítač DBMS je stále přijatelná od dopadu na obchodní proces. V případě zákaznických scénářů, kde latence mezi aktivním virtuálním počítačem DBMS v jedné zóně a instancí aplikace SAP na virtuálním počítači v jiné zóně může být příliš rušivá a nepřijatelná pro obchodní procesy SAP. V důsledku toho musí být architektura nasazení odlišná s architekturou aktivní/aktivní pro aplikaci nebo aktivní/pasivní architekturou, pokud je latence příliš vysoká.
- Použití služby [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) je povinné pro nasazení do zóny dostupnosti Azure 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Plánovaná a neplánovaná údržba virtuálních počítačů

Dostupnost virtuálních počítačů můžou ovlivnit dva typy událostí platformy Azure:

* **Plánované události údržby** jsou pravidelné aktualizace prováděné Microsoftem na základní platformu Azure. Tyto aktualizace zlepšují celkovou spolehlivost, výkon a zabezpečení infrastruktury platformy, na které běží vaše virtuální počítače.

* K **neplánovaným** událostem údržby dochází, když se hardwarová nebo fyzická infrastruktura na virtuálním počítači nějakým způsobem nezdařila. Může to zahrnovat selhání místní sítě, selhání místního disku nebo jiné chyby na úrovni racku. Když se takové selhání detekuje, platforma Azure automaticky migruje virtuální počítač z nesprávného fyzického serveru, který hostuje váš virtuální počítač, na fyzický server, který je v pořádku. Tyto události jsou vzácné, ale můžou taky způsobit, že se virtuální počítač restartuje.

Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů s Windows v Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Azure Storage redundance
Data v účtu úložiště se vždycky replikují, aby se zajistila odolnost a vysoká dostupnost, a to i na základě Azure Storage smlouvy SLA, a to i v případě přechodných selhání hardwaru.

Vzhledem k tomu, že Azure Storage udržuje tři image dat ve výchozím nastavení, není nutné používat RAID 5 nebo RAID 1 na více discích Azure.

Další informace najdete v tématu [replikace Azure Storage][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Spravované disky Azure
Managed Disks je typ prostředku v Azure Resource Manager, který se doporučuje používat místo virtuálních pevných disků (VHD), které jsou uložené v účtech Azure Storage. Spravované disky se automaticky zarovnají se sadou dostupnosti Azure virtuálního počítače, ke kterým jsou připojené. Zvyšují dostupnost vašeho virtuálního počítače a služeb, které jsou v něm spuštěné.

Další informace najdete v tématu  [Přehled služby Azure Managed disks][azure-storage-managed-disks-overview].

Doporučujeme používat spravované disky, protože zjednodušují nasazení a správu virtuálních počítačů.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Využití infrastruktury Azure s vysokou dostupností k zajištění *vyšší dostupnosti* aplikací SAP

Pokud se rozhodnete, že nebudete používat funkce, jako je WSFC nebo Pacemaker v systému Linux (aktuálně podporované pouze pro SUSE Linux Enterprise Server [SLES] 12 a novější), je využíván restart virtuálního počítače Azure. Chrání systémy SAP proti plánovanému a neplánovanému výpadku infrastruktury fyzického serveru Azure a celkové základní platformy Azure.

Další informace o tomto přístupu najdete v tématu [využití restartování virtuálních počítačů infrastruktury Azure pro zajištění vyšší dostupnosti systému SAP][sap-higher-availability].

## <a name="high-availability-of-sap-applications-on-azure-iaas"></a><a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Vysoká dostupnost aplikací SAP v Azure IaaS

Pro zajištění vysoké dostupnosti systému SAP je nutné chránit všechny důležité součásti systému SAP. Příklad:
  * Redundantní aplikační servery SAP.
  * Jedinečné součásti. Příkladem může být komponenta s jedním bodem selhání (SPOF), jako je například instance SAP ASCS/SCS nebo systém správy databáze (DBMS).

V dalších částech se podíváte, jak dosáhnout vysoké dostupnosti pro všechny tři důležité součásti systému SAP.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Architektura vysoké dostupnosti pro aplikační servery SAP

> Tato část se týká:
>
> ![Logo Windows][Logo_Windows] Okna a ![Logo Linux.][Logo_Linux] Linux
>

Obvykle nepotřebujete pro instance aplikačního serveru SAP a dialogových serverů SAP konkrétní řešení s vysokou dostupností. Dosáhnete vysoké dostupnosti redundancí a nakonfigurujete několik instancí dialogů v různých instancích virtuálních počítačů Azure. Ve dvou instancích virtuálních počítačů Azure byste měli mít nainstalované aspoň dvě instance aplikace SAP.

![Obrázek 1: aplikační Server SAP s vysokou dostupností][sap-ha-guide-figure-2000]

_**Obrázek 1:** Aplikační Server SAP s vysokou dostupností_

Všechny virtuální počítače, které hostují instance aplikačního serveru SAP, musíte umístit do stejné skupiny dostupnosti Azure. Skupina dostupnosti Azure zajišťuje:

* Všechny virtuální počítače nejsou součástí stejné aktualizační domény.  
    Aktualizační doména zajišťuje, že virtuální počítače se během plánovaných výpadků údržby neaktualizují současně.

    Základní funkce, které se vytvářejí v různých doménách aktualizace a selhání v rámci jednotky škálování Azure, už byla zavedená v části [aktualizace domén][planning-guide-3.2.2] .

* Všechny virtuální počítače nejsou součástí stejné domény selhání.  
    Doména selhání zajišťuje nasazení virtuálních počítačů, aby nedošlo k žádnému jedinému bodu selhání, který by měl mít vliv na dostupnost všech virtuálních počítačů.

Počet domén aktualizace a selhání, které může použít Skupina dostupnosti Azure v rámci jednotky škálování Azure, je omezený. Pokud přidáváte virtuální počítače do jedné skupiny dostupnosti, dva nebo víc virtuálních počítačů nakonec skončí ve stejné chybě nebo doméně aktualizace.

Pokud ve svých vyhrazených virtuálních počítačích nasadíte několik instancí aplikačního serveru SAP za předpokladu, že máme pět aktualizačních domén, objeví se následující obrázek. Skutečný maximální počet domén aktualizace a selhání v rámci skupiny dostupnosti se může v budoucnu změnit:

![Obrázek 2: vysoká dostupnost aplikačních serverů SAP v sadě dostupnosti Azure na ][planning-guide-figure-3000]
 _**obrázku 2:** vysoká dostupnost aplikačních serverů SAP v sadě dostupnosti Azure_

Další informace najdete v tématu [Správa dostupnosti virtuálních počítačů s Windows v Azure][azure-virtual-machines-manage-availability].

Další informace najdete v části [skupiny dostupnosti Azure][planning-guide-3.2.3] v dokumentu plánování a implementace virtuálních počítačů Azure pro službu SAP NetWeaver.

**Pouze nespravované disky:** Vzhledem k tomu, že účet Azure Storage je potenciálním jediným bodem selhání, je důležité mít aspoň dva účty úložiště Azure, ve kterých jsou distribuované aspoň dva virtuální počítače. V ideálním případě by se disky každého virtuálního počítače, na kterém běží instance dialogového okna SAP, nasadily v jiném účtu úložiště.

> [!IMPORTANT]
> Důrazně doporučujeme, abyste pro instalace SAP s vysokou dostupností používali službu Azure Managed disks. Vzhledem k tomu, že se spravované disky automaticky zarovnají se skupinou dostupnosti virtuálního počítače, ke kterým jsou připojené, zvyšují dostupnost vašeho virtuálního počítače a služeb, které jsou v něm spuštěné.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Architektura vysoké dostupnosti pro instanci SAP ASCS/SCS ve Windows

> ![Logo Windows][Logo_Windows] Windows
>

K ochraně instance SAP ASCS/SCS můžete použít řešení služby WSFC. Řešení má dvě varianty:

* **Vytvoření clusteru instance SAP ASCS/SCS pomocí clusterovaných sdílených disků**: Další informace o této architektuře najdete v tématu věnovaném [vytvoření instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru][sap-high-availability-guide-wsfc-shared-disk].   

* Vytvořte **cluster instance SAP ASCS/SCS pomocí sdílené složky**: Další informace o této architektuře najdete v tématu věnovaném vytvoření [instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdílené složky][sap-high-availability-guide-wsfc-file-share].

* Vytvořte **cluster instance SAP ASCS/SCS pomocí ANF sdílené složky SMB**: Další informace o této architektuře najdete v tématu cluster clusterů: [instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí ANF sdílené složky protokolu SMB](./high-availability-guide-windows-netapp-files-smb.md).

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Architektura vysoké dostupnosti pro instanci SAP ASCS/SCS v systému Linux

> ![Logo Linux.][Logo_Linux] Linux
> 
> Další informace o clusteringu instance SAP ASCS/SCS pomocí rozhraní clusterů SLES najdete v tématu [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP][sap-suse-ascs-ha]. Alternativní architektura HA na SLES, která nevyžaduje vysoce dostupný systém souborů NFS, najdete v tématu [Průvodce vysokou dostupností pro SAP NetWeaver na SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP][sap-suse-ascs-ha-anf].

Další informace o clusteringu instance SAP ASCS/SCS pomocí architektury systému Red Hat naleznete v tématu [Azure Virtual Machines High Availability for SAP NetWeaver on Red Hat Enterprise Linux](./high-availability-guide-rhel.md)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Konfigurace více identifikátorů SID SAP NetWeaver pro clusterovanou instanci SAP ASCS/SCS

> ![Logo Windows][Logo_Windows] Windows
> 
> Pro službu WSFC je podporována podpora více identifikátorů SID pomocí sdílené složky a sdíleného disku.
> 
> Další informace o architektuře s vysokou dostupností ve Windows pro více SID najdete v těchto tématech:

* [Vysoká dostupnost služby SAP ASCS/SCS instance multi-SID pro clustering s podporou převzetí služeb při selhání Windows serveru a sdílení souborů][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Vysoká dostupnost služby SAP ASCS/SCS instance multi-SID pro clustering s podporou převzetí služeb při selhání a sdílený disk s Windows serverem][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Logo Linux.][Logo_Linux] Linux
> 
> Clustering s více identifikátory SID je podporován v clusterech se systémem Linux Pacemaker pro SAP ASCS/OLAJÍCÍCH, které jsou omezeny na **pět** identifikátorů SID SAP ve stejném clusteru.
> Další informace o architektuře s vysokou dostupností s více SID v systému Linux najdete v těchto tématech:

* [HA pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP – příručka pro multi-SID](./high-availability-guide-suse-multi-sid.md)
* [HA pro SAP NW na virtuálních počítačích Azure v RHEL pro aplikace SAP – příručka pro multi-SID](./high-availability-guide-rhel-multi-sid.md)

### <a name="high-availability-dbms-instance"></a>Instance DBMS s vysokou dostupností

DBMS je také jedním kontaktním bodem v systému SAP. Budete je muset chránit pomocí řešení s vysokou dostupností. Následující obrázek ukazuje řešení vysoce dostupného AlwaysOn SQL Server v Azure s clusteringem s podporou převzetí služeb při selhání Windows serveru a interním nástrojem pro vyrovnávání zatížení Azure. SQL Server AlwaysOn replikuje DBMS data a soubory protokolu pomocí vlastní replikace systému DBMS. V takovém případě nepotřebujete sdílený disk clusteru, což zjednodušuje celou instalaci.

![Obrázek 3: příklad vysoce dostupného systému SAP DBMS s SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Obrázek 3:** Příklad vysoce dostupného systému SAP DBMS s SQL Server AlwaysOn_

Další informace o clusteringu SQL Server DBMS v Azure pomocí modelu nasazení Azure Resource Manager najdete v těchto článcích:

* [Ruční konfigurace skupiny dostupnosti AlwaysOn na virtuálních počítačích Azure pomocí Správce prostředků][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Konfigurace interního nástroje pro vyrovnávání zatížení Azure pro skupinu dostupnosti AlwaysOn v Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Další informace o clusteringu SAP HANA DBMS v Azure pomocí modelu nasazení Azure Resource Manager najdete v tématu [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure (VM)][sap-hana-ha].
