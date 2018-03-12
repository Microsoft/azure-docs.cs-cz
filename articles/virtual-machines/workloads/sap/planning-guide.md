---
title: "Azure virtuálních počítačů, plánování a implementace pro SAP NetWeaver | Microsoft Docs"
description: "Azure virtuálních počítačů, plánování a implementace pro SAP NetWeaver"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9cd12808f7e3bbb8a4edfe0d8de1e5b0a007770a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure virtuálních počítačů, plánování a implementace pro SAP NetWeaver
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure umožňuje společnostem získat výpočetní a úložnou kapacitu v minimálním čase bez zdlouhavé nákup cykly. Virtuální počítače Azure umožňují společnostem klasického aplikace, můžete nasadit jako SAP NetWeaver na základě aplikace do Azure a rozšířit jejich spolehlivosti a dostupnosti bez nutnosti další prostředky k dispozici místně. Služby virtuálního počítače Azure také podporuje připojení mezi různými místy, která umožňuje společnostem aktivně integrovat virtuálních počítačích Azure do místní domény, jejich privátních Cloudů a jejich šířku systému SAP.
Tento dokument popisuje základní informace o virtuálním počítači Microsoft Azure a poskytuje návod aspekty plánování a implementace pro SAP NetWeaver instalace v Azure a jako takový musí být dokument k načtení před zahájením skutečné nasazení SAP NetWeaver v Azure.
Dokumentu doplňuje SAP instalace dokumentace a poznámky k SAP, které představují primární prostředky pro instalace a nasazení SAP softwaru na dané platformy.

## <a name="summary"></a>Souhrn
Cloud Computing je často používaný výrazu, který je získání více důležitost v IT odvětví, z malých společností až velkých a mezinárodních společnosti.

Microsoft Azure je Cloudová platforma služby společnosti Microsoft, který nabízí široké spektrum nové možnosti. Nyní zákazníci mohou rychle zřizovat a deaktivace zřízení aplikace jako službu v cloudu, takže nejsou omezeny na technické nebo rozpočet omezení. Společnosti můžete místo času a investovat do hardwaru infrastruktury, soustředit na aplikace, podnikové procesy a jeho výhody pro zákazníky a uživatele.

Pomocí služeb Microsoft Azure pro virtuální počítače nabízí společnost Microsoft komplexní platformu infrastruktury jako služby (IaaS). Služba Azure Virtual Machines teď v rámci IaaS podporuje aplikace využívající SAP NetWeaver. Tento dokument White Paper popisuje, jak naplánovat a implementovat SAP NetWeaver na základě aplikací v rámci Microsoft Azure jako platformu výběru.

Dokument, samotné se zaměřuje na dvě hlavní aspekty:

* První část popisuje dva vzory podporované nasazení SAP NetWeaver na základě aplikací na platformě Azure. Také popisuje obecné zpracování Azure s nasazení SAP v paměti.
* Druhá část Podrobnosti implementace dva různé scénáře, které jsou popsané v první části.

Další zdroje naleznete v kapitole [prostředky] [ planning-guide-1.2] v tomto dokumentu.

### <a name="definitions-upfront"></a>Definice předem
V dokumentu můžeme použít následující podmínky:

* IaaS: Infrastruktury jako služby
* PaaS: Platforma jako služba
* SaaS: Software jako služba
* ARM: Azure Resource Manager
* Součást SAP: jednotlivých SAP aplikace například ECC, BW, správce řešení nebo podnikovém portálu.  SAP součástí může být založen na tradičních technologií ABAP nebo Java nebo jiných NetWeaver na základě aplikaci, například obchodních objektů.
* Prostředí SAP: jeden nebo více součástí SAP logicky seskupeny provést obchodní funkce jako je například vývoj, QAS, školení, zotavení po Havárii nebo produkční.
* SAP na šířku: Vztahuje se k celé SAP prostředků v zákazníka na šířku IT. Na šířku SAP zahrnuje všechny produkční a mimo provozní prostředí.
* Systém SAP: Kombinace databázového systému vrstvu a aplikační vrstvu služby, například SAP ERP vývojového systému SAP BW testovací systém, produkční systému SAP CRM, atd.. V nasazeních Azure není možné rozdělit tyto dvě vrstvy mezi místními a Azure. To znamená, že systému SAP buď je nasazena místně nebo je nasazené v Azure. Můžete však nasadit různých systémech šířku SAP do Azure nebo místní. Můžete například nasazení SAP CRM vývoj a testování systémy v Azure, ale SAP CRM produkční systému místní.
* Nasazení jenom cloudu: nasazení, kde není připojen k předplatnému Azure prostřednictvím site-to-site nebo připojením ExpressRoute na místní infrastrukturu sítě. Společné dokumentace k Azure tyto typy nasazení jsou také popsány jako "Jen cloudu" nasazení. Virtuální počítače nasazené pomocí této metody jsou přístupné prostřednictvím Internetu a veřejnou IP adresu nebo veřejný název DNS přiřazené k virtuálním počítačům v Azure. Pro Microsoft Windows v místní službě Active Directory (AD) a DNS není rozšířené k Azure v těchto typů nasazení. Proto virtuální počítače nejsou součástí místní služby Active Directory. Totéž platí pro implementace Linux, například pomocí OpenLDAP + protokolu Kerberos.

> [!NOTE]
> Čistě cloudové nasazení v tomto dokumentu je definován jako dokončení krajiny SAP běží výhradně v Azure bez přípony služby Active Directory nebo OpenLDAP nebo překladu názvů z místní do veřejného cloudu. Jenom pro cloud konfigurace nejsou podporovány pro produkční systémy SAP nebo konfigurace, kdy moduly STM SAP nebo jiných místních prostředků je potřeba použít mezi systémy SAP hostované v Azure a prostředky, které se nacházejí na místní.
>
>

* Mezi různými místy: Popisuje scénář, kde jsou nasazené virtuální počítače k předplatnému Azure, který má site-to-site, více lokalit nebo připojení ExpressRoute mezi místní datových centrech a Azure. Dokumentace k společné Azure, tyto typy nasazení jsou také popsány jako mezi různými místy scénáře. Z důvodu pro připojení je rozšířit místní domény, OpenLDAP Active Directory v místě a místní DNS do Azure. Na šířku místní je rozšířeno na Azure prostředky předplatného. S touto příponou, virtuálních počítačů může být součástí místní domény. Uživatelé domény místní domény, můžete přístup k serverům a službu lze spouštět na těchto virtuálních počítačů (např. služby databázového systému). Komunikace a název rozlišení mezi virtuální počítače nasazené na místě a nasazené virtuální počítače Azure je možné. Je tento scénář Očekáváme, že většina SAP prostředky se mají nasadit v. Další informace najdete v tématu [to] [ vpn-gateway-cross-premises-options] článku a [to][vpn-gateway-site-to-site-create].

> [!NOTE]
> Mezi různými místy nasazení SAP systémy, kde Azure Virtual Machines s SAP systémy jsou členy místní domény jsou podporovány pro produkční systémy SAP. Konfigurace mezi různými místy jsou podporovány pro nasazení částí nebo dokončení krajiny SAP do Azure. I spuštění povahu dokončení SAP v Azure vyžaduje, že tyto virtuální počítače byly součástí místní domény a služby Active Directory nebo OpenLDAP. V předchozí verze dokumentace, už jsme mluvili o IT hybridní scénáře, kde termín *hybridní* je integrován do skutečnost, že je připojení mezi různými místy mezi místními a Azure. Plus skutečnost, že virtuální počítače v Azure jsou součástí služby Active Directory v místě nebo OpenLDAP.
>
>

Některé dokumentaci Microsoft popisuje scénáře mezi různými místy trochu jinak, zejména pro konfigurace HA databázového systému. V případě SAP související dokumenty mezi různými místy scénář právě boils na situaci, kdy site-to-site nebo připojení k privátní (ExpressRoute) a fakt, že je povahu SAP distribuovat mezi místními a Azure.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Prostředky
Následující další příručky jsou k dispozici pro téma nasazení SAP v Azure:

* [Azure virtuálních počítačů, plánování a implementace pro SAP NetWeaver (Tento dokument)][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP NetWeaver][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP NetWeaver][dbms-guide]

> [!IMPORTANT]
> Kdekoli možné odkaz odkazující Instalační příručka slouží (referenční dokumentace InstGuide-01, najdete v části <http://service.sap.com/instguides>). Pokud jde o proces instalace a požadavky, příručky SAP NetWeaver instalace by měla vždycky číst pečlivě, tento dokument popisuje jenom konkrétní úlohy pro SAP NetWeaver systémy nainstalované v virtuální počítač Microsoft Azure.
>
>

Následující poznámky k SAP souvisí s tématem SAP v Azure:

| Poznámka: číslo | Nadpis |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: podporované produkty a velikosti |
| [2015553] |SAP na platformě Microsoft Azure: podporovat požadavky |
| [1999351] |Řešení potíží s rozšířenou Azure monitorování pro SAP |
| [2178632] |Klíč monitorování metriky pro SAP na platformě Microsoft Azure |
| [1409604] |Virtualizace v systému Windows: rozšířené monitorování |
| [2191498] |SAP v systému Linux s Azure: rozšířené monitorování |
| [2243692] |Linux na Microsoft Azure (IaaS) virtuálních počítačů: problémy licence SAP |
| [1984787] |Systému SUSE LINUX Enterprise Server 12: Poznámky k instalaci |
| [2002167] |Red Hat Enterprise Linux 7.x: instalace a Upgrade |
| [2069760] |Oracle Linux 7.x SAP instalace a Upgrade |
| [1597355] |Doporučení odkládacího prostoru pro Linux |

Přečíst také [oznámení změny stavu Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) obsahující všechny SAP poznámky pro Linux.

Obecné výchozí omezení a maximální omezení předplatná Azure naleznete v [v tomto článku][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Možné scénáře
SAP je často považovat za jednu z nejvíce důležitých aplikací v rámci firmy. Operace těchto aplikací a architektura je většinou velmi složitý a zajistíte, že splňujete požadavky na dostupnosti a výkonu je důležité.

Proto podniků má zamyslet pečlivě o tom, které aplikace lze spustit v prostředí veřejného cloudu, nezávisle na poskytovateli zvolený cloud.

Typy možné systému pro nasazení SAP NetWeaver na základě aplikací v rámci veřejného cloudu, prostředí, které jsou uvedeny níže:

1. Střední produkční systémy.
2. Vývoj pro systémy
3. Testování systémy
4. Systémy prototypu
5. Učení / ukázkový systémy

Chcete-li úspěšně nasadit systémy SAP do Azure IaaS nebo IaaS obecně, je důležité si uvědomit, významné rozdíly mezi nabídky tradiční outsourcers nebo hostitelé a nabídkou IaaS. Zatímco tradiční hostitel nebo pro externí dodavatele přizpůsobuje infrastrukturu (typ sítě, úložiště a server) pro zatížení, které chce zákazník hostitele, se místo toho odpovědnosti zákazníka vybrat správné zatížení pro nasazení IaaS.

Jako první krok potřeba zákazníků, zkontrolujte následující položky:

* SAP podporované typy virtuálních počítačů Azure
* SAP podporované produkty/verze v Azure
* Podporované operační systém a databázového systému verze pro konkrétní verze SAP v Azure
* Protokoly SAP propustnost poskytované různé identifikátory SKU Azure

Odpovědi na tyto otázky lze číst v Poznámka SAP [1928533].

Jako druhý krok musí být porovnáno více prostředků skutečné spotřebě místních systémů Azure omezení prostředků a šířku pásma. Zákazníci proto musí být obeznámeni s jinou možností Azure typy podporované s SAP v oblasti:

* Prostředky procesoru a paměti různých typů virtuálních počítačů a
* Šířka pásma IOPS různé typy virtuálních počítačů a
* Možnosti sítě různých typů virtuálních počítačů.

Většina těchto dat lze nalézt [zde (Linux)] [ virtual-machines-sizes-linux] a [zde (Windows)][virtual-machines-sizes-windows].

Mějte na paměti, že omezení uvedená v výše uvedený odkaz jsou horní meze. Neznamená to, omezení pro některý ze zdrojů, například IOPS lze zadat za všech okolností. Výjimky, když jsou prostředky procesoru a paměti typu zvolené virtuálních počítačů. Pro typy virtuálních počítačů nepodporuje SAP prostředky procesoru a paměti jsou vyhrazeny a jako takový k dispozici v libovolném bodě v čase pro používání v rámci virtuálního počítače.

Platforma Microsoft Azure jako jiné platformy IaaS je platforma pro více klientů. To znamená, že jsou mezi klienty sdílené úložiště, sítě a dalším prostředkům. Inteligentní omezení a kvóty logiku se používá k jednoho klienta zabrání závažný způsobem, které mají vliv na výkon jiného klienta (aktivní sousedním). Přestože logiku v Azure se pokusí zachovat odchylky v šířky pásma došlo malé, vysoce sdílené platformy zpravidla zavádět větší odchylky v dostupnosti prostředků nebo šířky pásma, než mnoho zákazníků se používají k v jejich nasazeních v místě. V důsledku toho může dojít k různých úrovních šířky pásma týkající se sítěmi nebo úložištěm vstupně-výstupní operace (na svazku a také latence) z minutu minutu. Pravděpodobnost, že systému SAP v Azure může na základě zkušeností uživatelů odchylky větší než v místním systému je potřeba vzít v úvahu.

Posledním krokem je k vyhodnocení požadavků na dostupnost. Může se stát, že základní infrastrukturu Azure potřebuje aktualizovat a vyžaduje spuštěných virtuálních počítačů až po restartování hostitele. V těchto případech by se vypínají a restartují i virtuálních počítačů spuštěných na těchto hostitelích. Načasování takové údržby se provádí v pracovní době vedlejší pro konkrétní oblasti, ale je poměrně široké okno potenciální několik hodin, během které dojde k restartování. Existují různé technologie v rámci Azure platformy, na které je možné nakonfigurovat na zmírnit některé nebo všechny dopad tyto aktualizace. Budoucí vylepšení platformy Azure, databázového systému a SAP aplikací navržených pro minimalizaci dopadů takové restartování.

Chcete-li úspěšně nasadit systém SAP do Azure, místní SAP systémy operační systém, databáze a aplikace SAP musí být na matici podpory SAP Azure, nevejde se v rámci prostředků Azure může poskytnout infrastruktury a který můžete pracovat s nabídkami dostupnost SLA Microsoft Azure. Tyto systémy jsou určeny, musíte při rozhodování o jednu z následujících scénářů dvě nasazení.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Jenom pro cloud - nasazení virtuálních počítačů do Azure bez závislosti na místní sítě zákazníka
![Jeden virtuální počítač s ukázkovou SAP nebo scénáře školení nasazené v Azure][planning-guide-figure-100]

Tento scénář je typické pro školení nebo ukázku systémy, kde jsou nainstalovány všechny součásti SAP a softwaru jiných SAP v rámci jednoho virtuálního počítače. Produkční SAP systémy nejsou podporovány v tomto scénáři nasazení. Obecně platí tento scénář splňuje následující požadavky:

* Vlastních virtuálních počítačů jsou přístupné přes veřejnou síť. Přímé připojení pro aplikace spuštěné v rámci virtuálních počítačů k místní síti společnosti vlastnící demoaplikace nebo školení obsah nebo zákazník není nutné.
* V případě představující školení nebo ukázkový scénář několika virtuálních počítačů síťové komunikace a název řešení musí fungovat mezi virtuálními počítači. Ale komunikace mezi sadu virtuálních počítačů musí být izolované tak, aby několik sady virtuálních počítačů můžete nasadit vedle sebe bez narušení.  
* Připojení k Internetu je vyžadována pro koncového uživatele na vzdálené přihlášení do pro virtuální počítače hostované v Azure. V závislosti na hostovaného operačního systému, Terminálové služby a služby RDS nebo VNC na ssh slouží k přístupu virtuálních počítačů pro splnění úlohy školení nebo provést ukázky. Pokud SAP porty například 3200, 3300 & 3600 může také zpřístupnit instance aplikace na SAP je přístupná na jakémkoli počítači připojené Internetu.
* Systémy SAP (a VM(s)) představují samostatné scénář v Azure, což pouze veřejné připojení k Internetu vyžaduje pro přístup koncových uživatelů a nevyžaduje připojení k ostatním virtuálním počítačům v Azure.
* SAPGUI a prohlížeče jsou nainstalovat a spustit přímo na virtuálním počítači.
* Je vyžadován rychlé resetování virtuálního počítače do původního stavu a opakujte nové nasazení tohoto původního stavu.
* V případě scénáře školení a ukázku, která jsou rozpoznat v několika virtuálních počítačů, služby Active Directory / OpenLDAP nebo DNS služby je zapotřebí pro každou sadu virtuálních počítačů.

![Skupinu Virtuálního počítače představující jednu ukázku nebo školení scénář v cloudové službě Azure][planning-guide-figure-200]

Je důležité mít na paměti, že virtuální počítače v jednotlivých sad musí být nasazeny současně, kde názvy virtuálních počítačů v každé sady jsou stejné.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Mezi různými místy - nasazení jedné nebo více virtuálních počítačů SAP do Azure s požadavkem je plně integrován do místní sítě
![Síť VPN se připojení Site-To-Site (mezi různými místy)][planning-guide-figure-300]

Tento scénář je mezi různými místy scénář s mnoha schémat možné nasazení. Lze popsat jednoduše systémem některé části SAP šířku místní a dalšími částmi sady SAP šířku v Azure. Všechny aspekty skutečnost, že součástí komponent SAP běží v Azure by měl být transparentní pro koncové uživatele. Proto přenosu oprava systému (moduly STM) SAP, RFC komunikace, tisk, zabezpečení (jako je jednotné přihlašování) atd fungovat bez problémů systémů SAP spuštěné v Azure. Ale mezi různými místy scénář také popisuje scénář, kde povahu dokončení SAP spuštění v Azure s doménou zákazníka a DNS rozšířit do Azure.

> [!NOTE]
> Toto je tento scénář nasazení, který je podporován pro produktivní systémem SAP.
>
>

Čtení [v tomto článku] [ vpn-gateway-create-site-to-site-rm-powershell] Další informace o tom, jak připojit místní sítě k Microsoft Azure

> [!IMPORTANT]
> Když jsme mluvíme o scénářích mezi různými místy mezi Azure a místními zákaznických nasazení, jsme před sebou členitost celou systémy SAP. Scénáře, které jsou *nepodporuje* pro mezi různými místy scénáře jsou:
>
> * Spuštění různých úrovní aplikace SAP v různých metod nasazení. Například spuštění databázového systému vrstvy místní, ale aplikační vrstvu SAP ve virtuálních počítačích nasazených ve virtuálních počítačích Azure a naopak.
> * Některé součásti SAP vrstvy v Azure a některé na místě. Příklad rozdělení instance SAP aplikační vrstvy mezi místní a virtuální počítače Azure.
> * Není dostupná podpora distribuce virtuálních počítačů spuštěné instance SAP jeden systém nad několika oblastmi Azure.
>
> Důvod tato omezení je požadavkem pro vysoce výkonné síť s velmi nízkou latencí v rámci jednoho systému SAP, zejména mezi instancemi aplikace a vrstva databázového systému SAP systému.
>
>

### <a name="supported-os-and-database-releases"></a>Podporované operační systém a verze databáze
* Podporované služby virtuálního počítače Azure je uvedené v tomto článku serverového softwaru společnosti Microsoft: <http://support.microsoft.com/kb/2721672>.
* Podporované operační systém verze, vydání systému databáze podporované na služby virtuálního počítače Azure ve spojení s SAP softwaru jsou popsané v Poznámka SAP [1928533].
* Aplikace SAP a verzí podporované na virtuální počítač služby Azure jsou dokumentovány v článku Poznámka SAP [1928533].
* Ke spuštění jako virtuální počítače hostované v Azure pro SAP scénáře jsou podporované Image pouze 64bitové. Také to znamená, že jsou podporovány pouze 64bitové aplikace SAP a databází.

## <a name="microsoft-azure-virtual-machine-services"></a>Služby virtuálního počítače Microsoft Azure
Platformy Microsoft Azure je platforma internetových cloudové služby hostované a pracuje v datových centrech společnosti Microsoft. Platforma obsahuje virtuální počítač služby Microsoft Azure (infrastruktura jako služba nebo IaaS) a sadu bohaté platforma jako služba (PaaS) možnosti.

Platformy Azure omezuje potřebu počáteční technologie a nákupech infrastruktury. Zjednodušuje údržbu a provoz aplikace tím, že poskytuje na vyžádání výpočetního prostředí a úložiště pro hostování, škálování a Správa webové aplikace a propojených aplikací. Správu infrastruktury je automatické s platformu, která je určená pro vysokou dostupnost a dynamické škálování tak, aby odpovídaly potřebám použití s parametrem průběžnými platbami cenový model.

![Umístění služby virtuálního počítače Microsoft Azure][planning-guide-figure-400]

Pomocí služby virtuálního počítače Azure je povolení Microsoft nasadit bitové kopie vlastní server do Azure jako instance IaaS (viz obrázek 4). Virtuální počítače v Azure jsou založené na technologii Hyper-V virtuální pevné disky (VHD) a je možné spouštět různé operační systémy jako hostovaného operačního systému.

Z provozního hlediska nabízí služba virtuálního počítače Azure podobné možnosti jako virtuální počítače nasazené na místní. Ale má významné výhody, který nemusíte zakoupit, spravovat a správě infrastruktury. Vývojáři a správci mít plnou kontrolu nad bitovou kopii operačního systému v rámci těchto virtuálních počítačů. Správci můžou na přihlásit vzdáleně těchto virtuálních počítačů k provádění údržby a řešení potíží s úlohy a také úlohy nasazení softwaru. S ohledem na nasazení jsou pouze omezení velikosti a možnosti virtuálních počítačích Azure. Nemusí se jednat o jako jemné granulární v konfiguraci, jako tomu lze místně. Při volbě typů virtuálních počítačů, které představují kombinaci:

* Počet Vcpu,
* Paměť
* Počet virtuálních pevných disků, které je možné připojit,
* Šířek pásma sítě a úložiště.

Velikost a omezení velikostí různé virtuální počítače různých nabízí, můžete zobrazit v tabulce v [v tomto článku (Linux)] [ virtual-machines-sizes-linux] a [v tomto článku (Windows)][virtual-machines-sizes-windows].

Jak si myslíte, existují různé rodiny nebo řadu virtuálních počítačů. Možné rozlišit následující rodiny virtuálních počítačů:

* Virtuální počítač a0 A7 typy: Ne všechny z nich jsou certifikovány pro SAP. První virtuální počítač série, která získali zavedené Azure IaaS.
* Virtuální počítač a8-A11 typy: instance s vysokým výkonem. Běžet na různých lepší provádění výpočetní hostitelů než ostatní virtuální počítače A-series.
* Počítač D/Dv2-Series typy: lépe než A0 A7 provádění. Ne všechny typy virtuálních počítačů jsou certifikované s SAP.
* Typy virtuálních počítačů služby DS, DSv2-Series: podobná D/Dv2-series, ale jsou možné se připojit k Azure Premium Storage (naleznete v kapitole [Azure Premium Storage] [ planning-guide-3.3.2] tohoto dokumentu). Znovu ne všechny typy virtuálních počítačů, které jsou certifikované s SAP.
* Virtuální počítač G-Series typy: typy vysokého využití paměti virtuálního počítače.
* Virtuální počítač GS-Series typy: jako G-Series, ale včetně možnosti používání Azure Premium Storage (naleznete v kapitole [Azure Premium Storage] [ planning-guide-3.3.2] tohoto dokumentu). Při používání virtuálních počítačů GS-Series jako databázové servery, je nutné používat úložiště úrovně Premium pro soubory protokolů databáze dat a transakce

V řadě různých virtuálních počítačů můžete zjistit stejném procesoru a paměti konfigurace. Nicméně když hledáte propustnost těchto virtuálních počítačů mimo jiné řady se mohou výrazně lišit. Přes stejnou konfiguraci procesoru a paměti. Důvodem je, že základní hardware serveru hostitele na zavedení různé typy virtuálních počítačů mají různé propustnost vlastnosti.  Obvykle rozdíl ukazuje propustnost je odráží rovněž v cenu různé virtuální počítače.

Ne všechny řady různých virtuálních počítačů může nabízena v každé z nich oblastí Azure (pro oblasti Azure naleznete v kapitole Další). Nezapomínejte, že ne všechny virtuální počítače nebo řadu virtuálních počítačů jsou certifikovány pro SAP.

> [!IMPORTANT]
> Pro používání SAP NetWeaver na základě aplikací, pouze podmnožinu typy virtuálních počítačů a konfigurace uvedené v Poznámka SAP [1928533] jsou podporovány.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Oblasti Azure
Microsoft umožňuje nasazení virtuálních počítačů do tak nazývá *oblasti Azure*. Oblast Azure může být jeden nebo více datových center, které se nacházejí v těsné blízkosti. Pro většinu geopolitické oblasti na světě. Společnost Microsoft nemá alespoň dva oblasti Azure. Například v Evropě je oblast Azure z *Severní Evropa* a jedno z *západní Evropa*. Tyto dvě Azure oblastem v geopolitické oblasti jsou odděleny dostatečně velkou vzdálenost tak, aby přírodních nebo technických havárie neovlivní obou oblastí Azure ve stejné geopolitické oblasti. Vzhledem k tomu, že Microsoft vytrvale vytváří se nové oblasti Azure v různých oblastech geopolitické globálně, počet tyto oblasti vytrvale roste a od prosince 2015 dosaženo počtu 20 oblasti Azure s další oblasti oznámeno již. Systémy SAP vy jako zákazník můžete nasadit do všech těchto oblastech, včetně dvou oblastí Azure v Číně. Aktuální aktuální informace o oblastech Azure najdete v tomto webu: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Koncept virtuálního počítače Microsoft Azure
Microsoft Azure nabízí infrastruktura jako služba (IaaS) řešení hostitele virtuálních počítačů s podobné funkce jako místní řešení virtualizace. Budete moci vytvářet virtuální počítače z portálu Azure, PowerShell nebo rozhraní příkazového řádku, které také nabízí nasazení a možnosti správy.

Azure Resource Manager umožňuje zřizovat aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Použijete stejné šablony k opakovaně nasazovat aplikace během každé fáze životního cyklu aplikace.

Další informace o používání šablon Resource Manageru naleznete zde:

* [Nasadit a spravovat virtuální počítače pomocí šablony Azure Resource Manager a rozhraní příkazového řádku Azure] [../../linux/create-ssh-secured-vm-from-template.md]
* [Správa virtuálních počítačů pomocí Azure Resource Manageru a prostředí PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Jiné zajímavé funkce je možnost vytvářet bitové kopie z virtuálních počítačů, které umožňuje připravit určité úložiště, ze kterých budete moci rychle nasadit instancí virtuálního počítače, které odpovídají vašim požadavkům.

Další informace o vytváření bitové kopie z virtuálních počítačů naleznete v [v tomto článku (Linux)] [ virtual-machines-linux-capture-image-resource-manager] a [v tomto článku (Windows)][virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domén selhání
Fyzická jednotka nezdaří, velmi úzce související fyzické infrastruktuře obsažené v datových centrech, a když fyzické okno nebo rack lze považovat za domény selhání představují domén selhání, neexistuje žádné přímé mapování 1: 1 mezi nimi.

Při nasazení více virtuálních počítačů v rámci jednoho systému SAP v služeb virtuálního počítače Microsoft Azure, můžete ovlivnit Kontroleru prostředků infrastruktury Azure k nasazení aplikace do různých domén selhání, a tím splňující požadavky smlouvy SLA pro Microsoft Azure. Distribuce domén selhání v jednotky škálování služby Azure (shromažďování stovky výpočetních uzlů nebo uzlů úložiště a sítě) nebo přiřazení virtuálních počítačů do konkrétní domény selhání je však něco přes které nemají přímou kontrolu. Aby bylo možné směrovat kontroleru prostředků infrastruktury Azure k nasazení sadu virtuálních počítačů přes různých domén selhání, budete muset přiřadit skupiny dostupnosti Azure virtuální počítače v době nasazení. Další informace o Azure skupiny dostupnosti, naleznete v kapitole [skupiny dostupnosti Azure] [ planning-guide-3.2.3] v tomto dokumentu.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Upgrade Domains
Domén upgradu představují logické jednotce, která vám pomoci určit, jak se virtuální počítač v rámci systému SAP, která se skládá z instancí SAP spuštěných v několika virtuálními počítači, aktualizuje. Když dojde k upgradu, Microsoft Azure projde proces aktualizace těchto domén upgradu po jednom. Tak, že se virtuální počítače v době nasazení v různých doménách upgradu, můžete chránit váš systém SAP částečně z potenciální výpadek. Chcete-li vynutit Azure k nasazení virtuálních počítačů v různých doménách Upgrade systému SAP, budete muset nastavit konkrétní atribut v době nasazení každého virtuálního počítače. Podobně jako u domén selhání, představuje jednotku škálování Azure je rozdělené do několika domén upgradu. Aby bylo možné směrovat řadičem prostředků infrastruktury Azure k nasazení sadu virtuálních počítačů v různých doménách upgradu, budete muset přiřadit skupiny dostupnosti Azure virtuální počítače v době nasazení. Další informace o Azure skupiny dostupnosti, naleznete v kapitole [skupiny dostupnosti Azure] [ planning-guide-3.2.3] níže.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Skupiny dostupnosti Azure
Virtuální počítače Azure v rámci jedné skupiny dostupnosti Azure rozdělené podle Kontroleru prostředků infrastruktury Azure přes různé selhání a upgradu domény. Účelem distribuce přes různé selhání a upgradu domény je zabránit vypnut v případě údržbu infrastruktury nebo selhání v jedné doméně selhání všech virtuálních počítačů systému SAP. Ve výchozím nastavení virtuální počítače nejsou součástí skupiny dostupnosti. Zapojení virtuálního počítače do skupiny dostupnosti je definována v době nasazení nebo později tak, že změna konfigurace a opětovné nasazení virtuálního počítače.

Porozumět koncepci skupinami dostupnosti služby Azure a způsob, jakým skupiny dostupnosti se týkají selhání a upgradu domény, přečtěte si [v tomto článku][virtual-machines-manage-availability]

Definovat skupiny dostupnosti pro ARM prostřednictvím šablonu json najdete v tématu [specifikace rozhraní rest api](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) a vyhledejte "dostupnosti".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Úložiště: Microsoft Azure Storage a datové disky
Microsoft Azure virtuální počítače využívat různé typy úložišť. Při implementaci SAP na služby virtuálního počítače Azure, je důležité znát rozdíly mezi tyto dva hlavní typy úložiště:

* Bez trvalé, volatile úložiště.
* Trvalé úložiště.

Dočasnou úložiště je přímo připojený k běžící virtuální počítače a se nachází na výpočetních uzlech, sami, místní instance úložiště (dočasné úložiště). Velikost závisí na velikosti virtuálního počítače, které vybrali při spuštění nasazení. Tento typ úložiště je volatile a proto je disk inicializován při restartování instanci virtuálního počítače. Stránkovací soubor pro operační systém se obvykle nachází na toto dočasný disku.

- - -
> ![Windows][Logo_Windows] Windows
>
> Na virtuálních počítačích Windows je dočasná disk připojit jako jednotku D:\ v nasazených virtuálních počítačů.
>
> ![Linux][Logo_Linux] Linux
>
> Na virtuální počítače s Linuxem je připojené jako /mnt/resource nebo /mnt. Naleznete zde podrobnosti:
>
> * [Tom, jak připojit datový Disk pro virtuální počítač s Linuxem][virtual-machines-linux-how-to-attach-disk]
> * <https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux#temporary-disk>
>
>

- - -
Skutečné jednotka je volatile, protože je získávání uložený na samotném serveru hostitele. Pokud se přesune virtuální počítač v opětovné nasazení (například z důvodu údržby v hostiteli nebo vypnutí a restartování) obsah disku dojde ke ztrátě. Proto není možnost k ukládání důležitých dat na této jednotce. Typ média pro tento typ úložiště použít se liší mezi jinou sérii virtuálních počítačů se příliš neliší výkonové charakteristiky, které od června 2015 vypadat podobně jako:

* A5-A7: Velmi omezená výkonu. Není doporučeno pro všechno, co je nad rámec stránkovacího souboru
* A8-A11: Velmi dobré výkonové charakteristiky se některé deset tisíc IOPS a > propustnost 1GB/s.
* D-Series: Velmi dobré výkonové charakteristiky se některé pak tisíců IOPS a > propustnost 1GB/s.
* DS-Series: Velmi dobré výkonové charakteristiky se některé deset tisíc IOPS a > propustnost 1GB/s.
* G-Series: Velmi dobré výkonové charakteristiky se některé deset tisíc IOPS a > propustnost 1GB/s.
* GS-Series: Velmi dobré výkonové charakteristiky se některé deset tisíc IOPS a > propustnost 1GB/s.

Výše uvedené příkazy se má použít pro typy virtuálních počítačů, které jsou certifikované s SAP. Virtuální počítač series s vynikající IOPS a propustnost dosáhlo nároku na využívání některé funkce databázového systému. Další informace najdete v tématu [Průvodce nasazením databázového systému][dbms-guide].

Microsoft Azure Storage poskytuje trvalou úložiště a typické úrovně ochrany a redundance jsme si ukazovali tady úložiště SAN. Disky založené na Azure Storage jsou virtuální pevný disk (VHD) v služby Azure Storage. Místní Disk operačního systému (Windows C:\, Linux/dev/sda1) je uložen v úložišti Azure, a další svazky nebo disky připojené k virtuálnímu počítači získat v ní uloženy, příliš.

Je možné nahrát existující virtuální pevný disk z místní nebo prázdných z v rámci Azure a vytvořte do nasazené virtuální počítače.

Po vytvoření nebo odesílání virtuálního pevného disku do úložiště Azure, je možné připojit a připojit, aby se stávající virtuální počítač a zkopírovat stávající virtuální pevný disk (nepřipojené).

Tyto virtuální pevné disky jsou nastavené jako trvalé, data a změny v těch, které jsou bezpečné při restartování a znovu vytvořit instanci virtuálního počítače. I když je odstraněna instance, tyto virtuální pevné disky zůstal v bezpečí a můžete znovu nasadit nebo v případě disky bez operačního systému může být připojen k ostatním virtuálním počítačům.

V rámci sítě Azure Storage, můžete nakonfigurovat různé redundance úrovně:

* Minimální úroveň, kterou je možné vybrat je *místní redundanci*, což je totéž jako tři repliky dat ve stejném datovém centru oblast Azure (naleznete v kapitole [oblasti Azure] [ planning-guide-3.1]).
* Redundantní úložiště zóny, které šíří tři Image přes různé datové centra v rámci stejné oblasti Azure.
* Výchozí úroveň redundance je geografická redundance, která asynchronně replikuje obsahu do jiného tři bitové kopie dat do jiné oblasti Azure, který je hostován ve stejné geopolitické oblasti.

Také najdete v tabulce v tomto článku týkající se možnosti různých redundance: <https://azure.microsoft.com/pricing/details/storage/>

Další informace o službě Azure Storage naleznete zde:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://azure.microsoft.com/services/site-recovery>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Úložiště Azure úrovně Standard
Standardní úložiště Azure se typ úložiště, které jsou k dispozici, pokud byl vydán Azure IaaS. Nebyly IOPS kvóty na jednom disku. Latence došlo nebyla ve stejné třídy jako zařízení sítě SAN nebo NAS obvykle nasazuje pro vyšší kategorie systémy SAP hostované na místě. Azure Standard Storage ukázalo dostatečná pro mnoho stovky systémy SAP mezitím nasazené v Azure.

Disky, které jsou uložené na standardních účtech úložiště Azure budou účtovat podle skutečné data, která je uložená, objem transakce úložiště, odchozí přenosy dat a zvolená možnost redundance. Mnoho disky se dají vytvořit v maximální 1TB, velikost, ale také ty zůstat prázdné je bezplatná. Pokud pak zadejte jeden virtuální pevný disk s 100GB, budou se účtovat pro ukládání 100GB a ne pro nominální velikost, které s nebyl vytvořen virtuální pevný disk.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Premium Storage
V duben 2015 zavedl Microsoft Azure Premium Storage. Storage úrovně Premium získali zavedl s cílem zajistit:

* Lepší latence vstupně-výstupní operace.
* Lepší propustnost.
* Menší je jejich variabilita latence vstupně-výstupní operace.

K tomuto účelu mnoho změn zavedených nejvýznamnějších dva jsou:

* Využití disků SSD na uzlech Azure Storage
* Nový číst mezipaměti, které je založeno na místní SSD Azure výpočetního uzlu

V opačném standardního úložiště, kde se nezměnila možnosti závisí na velikosti disku (nebo VHD), Storage úrovně Premium aktuálně má tří kategorií jiný disk, které jsou uvedeny v tomto článku: <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Uvidíte, zda jsou závislé na kategorii velikost disků nebo disku a disku propustnost nebo disku

Základní náklady v případě Storage úrovně Premium není svazek skutečná data uložená v těchto discích, ale velikost kategorii takový disk, nezávisle na množství dat, která jsou uložená v disku.

Také můžete vytvořit disky na Storage úrovně Premium, které nejsou přímo mapování do kategorií velikost vidět. To může být případ, zejména v případě, že kopírování disků ze standardního úložiště do úložiště úrovně Premium. V takových případech se provádí mapování na možnost další disk největší Storage úrovně Premium.

Upozorňujeme, že pouze určité řadu virtuálních počítačů mohou těžit z výhod Azure Premium Storage. Od prosince 2015 jedná se o řady GS a DS. DS-series je v podstatě stejný jako D-series s tím rozdílem, že DS-series má schopnost Storage úrovně Premium připojení virtuálních počítačů kromě na disky, které jsou hostované na základě Azure Standard Storage. Totéž platí pro G-series ve srovnání s GS-series.

Pokud se odhlašuje součástí DS-series virtuálních počítačů v [v tomto článku (Linux)] [ virtual-machines-sizes-linux] a [v tomto článku (Windows)][virtual-machines-sizes-windows], si myslíte, že jsou data svazku omezení na Storage úrovně Premium disky na členitost úroveň virtuálního počítače. Různé DS-series nebo GS-series virtuálních počítačů také mít různá omezení namapoval počet datových disků, které může být připojen. Tato omezení jsou popsané v článku také uvedených výše. Ale v podstatě znamená, že pokud, například připojíte 32 x P30 disků, aby se jeden virtuální počítač DS14 není získáte 32 x maximální propustnost P30 disku. Místo toho maximální propustnost na úrovni virtuálních počítačů, jak je uvedeno v následujícím článku omezuje propustnost dat.

Další informace o Storage úrovně Premium naleznete zde: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Spravované disky
Spravované disky jsou nového typu prostředku v Azure Resource Manager, který lze použít místo virtuální pevné disky, které jsou uložené v účtech úložiště Azure. Spravované disky automaticky zarovnané s skupina dostupnosti virtuálního počítače, které jsou připojené k a proto zvýšit dostupnost virtuálního počítače a služby, které jsou spuštěny na virtuálním počítači. Další informace najdete v tématu [článek s přehledem](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Doporučujeme použít spravované disku, protože se zjednodušit nasazení a správu virtuálních počítačů.
SAP aktuálně podporuje jenom disky spravované Premium. Další informace najdete v tématu Poznámka SAP [1928533].

#### <a name="azure-storage-accounts"></a>Účty služby Azure Storage
Při nasazování služeb nebo virtuálních počítačů v Azure, mohou být uspořádány nasazení virtuální pevné disky a Image virtuálních počítačů v jednotek nazývaných účtech úložiště Azure. Při plánování nasazení služby Azure, budete muset pečlivě zvažte omezení Azure. Na jedné straně je omezený počet účtů úložiště podle předplatného Azure. I když každý účet úložiště Azure pojme velký počet soubory virtuálního pevného disku, je pevný limit na celkový počet IOPS na účet úložiště. Při nasazování stovky SAP virtuálních počítačů se systémy databázového systému vytváření významné volání vstupně-výstupní operace, se doporučuje distribuovat vysoké databázového systému virtuální počítače IOPS mezi více účtů úložiště Azure. Musí dát pozor nechcete překračovat aktuální limit účtech úložiště Azure za předplatné. Vzhledem k tomu, že úložiště je důležitou součástí nasazení databáze systému SAP, tento koncept je podrobněji popsána v již odkazovaná [Průvodce nasazením databázového systému][dbms-guide].

Další informace o účtech Azure Storage najdete v [v tomto článku][storage-scalability-targets]. Přečtení tohoto článku, zjistíte, že nejsou v omezení rozdíly mezi standardních účtech úložiště Azure a účty úložiště Premium. Hlavní rozdíly mezi nimi jsou objem dat, které můžou být uložené v rámci účtu úložiště. V Standard Storage svazek je větší než Storage úrovně Premium rozsahem. Na druhé straně standardní účet úložiště vážně omezenou IOPS (viz sloupec **celkový počet požadavků**), zatímco prémiový účet úložiště Azure má žádné takové omezení. Se budeme zabývat podrobnosti a výsledky tyto rozdíly když hovoříte o nasazení SAP systémy, hlavně servery databázového systému.

V rámci účtu úložiště máte možnost vytvořit různé kontejnery za účelem uspořádání a kategorizaci jiný virtuální pevné disky. Tyto kontejnery se obvykle používají k například samostatné virtuální pevné disky různé virtuální počítače. Neexistují žádné ovlivnit výkon při použití kontejner jenom jeden nebo více kontejnerů pod jeden účet úložiště Azure.

Název disku VHD v rámci Azure zahrnuje následující pojmenování připojení, které je potřeba zadat jedinečný název pro virtuální pevný disk v rámci Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Jak je uvedeno výše řetězec musí k jednoznačné identifikaci virtuální pevný disk, který je uložen v úložišti Azure.

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Sítě Microsoft Azure
Microsoft Azure poskytuje síťovou infrastrukturu, která umožňuje mapování všechny scénáře, které chceme mějte na paměti SAP softwarem. Možnosti jsou:

* Přístup z venku, přímo k virtuálním počítačům prostřednictvím Terminálová služba systému Windows nebo ssh/VNC
* Přístup ke službám a určité porty používané aplikacemi v rámci virtuálních počítačů
* Interní komunikaci a překlad mezi skupinou virtuální počítače nasazené jako virtuální počítače Azure
* Připojení mezi různými místy mezi místní sítí zákazníka a síť Azure
* Křížové oblast Azure nebo datových center připojení mezi sítěmi Azure

Další informace naleznete zde: <https://azure.microsoft.com/documentation/services/virtual-network/>

Existuje mnoho různých možností, jak konfigurovat název a IP řešení v Azure. V tomto dokumentu jenom pro Cloud scénáře spoléhají na výchozí hodnotu pomocí Azure DNS (na rozdíl od definování vlastní služby DNS). Je také novou službu Azure DNS, který může být použit místo nastavení serveru DNS. Další informace naleznete v [v tomto článku] [ virtual-networks-manage-dns-in-vnet] a na [tuto stránku](https://azure.microsoft.com/services/dns/).

Pro scénáře mezi různými místy, jsme se spoléhat na fakt, na který místní AD a OpenLDAP/DNS bylo rozšířeno prostřednictvím sítě VPN nebo privátní připojení do Azure. Pro určité scénáře podle postupu uvedeného v tomto poli může být potřeba mít repliku AD nebo OpenLDAP nainstalován v Azure.

Protože sítě a překladu je důležitou součástí nasazení databáze systému SAP, tento koncept je podrobněji popsána v [Průvodce nasazením databázového systému][dbms-guide].

##### <a name="azure-virtual-networks"></a>Virtuální sítě Azure
Sestavením virtuální síť Azure, můžete definovat rozsah adres privátní IP adresy přidělené funkce Azure protokolu DHCP. Ve scénářích mezi různými místy rozsah IP adres definované je pořád ještě přidělená použití protokolu DHCP v Azure. Ale překlad názvu domény se provádí místně (za předpokladu, že virtuální počítače jsou součástí místní domény) a proto může překládat adresy mimo jiné cloudové služby Azure.

Každý virtuální počítač v Azure musí být připojen k virtuální síti.

Další podrobnosti naleznete v [v tomto článku] [ resource-groups-networking] a na [tuto stránku](https://azure.microsoft.com/documentation/services/virtual-network/).

[comment]: <> (MShermannd TODO nelze nalézt článek, který obsahuje téma OpenLDAP + ARM; )
[comment]: <> (MSSedusch <https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [!NOTE]
> Ve výchozím nastavení Jakmile je nasazen virtuální počítač nejde změnit konfiguraci virtuální sítě. Nastavení protokolu TCP/IP musí být ponechány na server Azure DHCP. Výchozí chování je přiřazování dynamické IP adres.
>
>

Adresa MAC virtuální síťové karty může například změnit po změny velikosti a systému Windows nebo Linux hostovaného operačního systému převezme nové síťové karty a automaticky používá protokol DHCP k přiřazení adres IP a DNS v tomto případě.

##### <a name="static-ip-assignment"></a>Přiřazování statických IP adres
Je možné přiřadit pevné nebo vyhrazené IP adresy k virtuálním počítačům ve virtuální síti Azure. Spuštění virtuálních počítačů v Azure Virtual Network otevře skvělé možnost využít tuto funkci, pokud potřebné nebo požadované pro některé scénáře. Přiřazování IP adres zůstává v platnosti po existenci virtuální počítač, nezávisle na tom, jestli je virtuální počítač, spuštění nebo vypnutí. V důsledku toho budete muset provést celkový počet virtuálních počítačů (virtuálních počítačů spuštěných a zastavených) v úvahu při definování rozsahu IP adres pro virtuální síť. IP adresa zůstane přiřazená až do odstranění virtuálního počítače a jeho síťové rozhraní, nebo dokud nebude IP adresa získá zrušte znovu přiřadit. Další informace najdete v tématu [v tomto článku][virtual-networks-static-private-ip-arm-pportal].

##### <a name="multiple-nics-per-vm"></a>Několik síťových adaptérů na virtuální počítač
Můžete definovat několik virtuálních síťových karet (vNIC) pro virtuální počítač Azure. Možnost mít více vNICs, které lze spustit nastavit síťový provoz oddělení, kde, například přenosy klienta směrován přes jednu vNIC a back-end provoz směrován přes druhý vNIC. Závisí na typu virtuálního počítače existuje jiný omezení jsou namapoval počet vNICs. Přesné podrobnosti, funkce a omezení naleznete v těchto článcích:

* [Vytvoření virtuálního počítače s Windows s více síťovými kartami][virtual-networks-multiple-nics-windows]
* [Vytvoření virtuálního počítače s Linuxem s více síťovými kartami][virtual-networks-multiple-nics-linux]
* [Nasazení více seskupování virtuálních počítačů pomocí šablony][virtual-network-deploy-multinic-arm-template]
* [Nasazení více seskupování virtuálních počítačů pomocí prostředí PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Nasazení více seskupování virtuálních počítačů pomocí rozhraní příkazového řádku Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Připojení Site-to-Site
Mezi různými místy je virtuálních počítačích Azure a místními propojené s transparentní, trvalé připojení VPN. Očekává se stane Nejběžnější vzor nasazení SAP v Azure. Předpokladem je, že by měl transparentně fungovat provozních postupů a procesů pomocí instance SAP v Azure. Tyto prostředky, které byste měli nelze tisknout z těchto systémů stejně jako použití SAP přenosu správu systému (TMS) k přenosu změní z vývojového systému v Azure na testovací systém, který je nasazen na místě. Další dokumentaci ohledně site-to-site lze nalézt v [v tomto článku][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Zařízení tunelové propojení VPN
Chcete-li vytvořit připojení site-to-site (místního datového centra k datovému centru Azure), musíte buď získat a nakonfigurovat zařízení VPN nebo použít směrování a vzdálený přístup (RRAS) zavedenou jako součást softwaru se systémem Windows Server 2012.

* [Vytvoření virtuální sítě s připojením VPN typu site-to-site pomocí prostředí PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Informace o zařízeních VPN pro připojení Site-to-Site VPN Gateway][vpn-gateway-about-vpn-devices]
* [Nejčastější dotazy k branám VPN][vpn-gateway-vpn-faq]

![Připojení Site-to-site mezi místními a Azure][planning-guide-figure-600]

Výše uvedené schéma ukazuje, že dvě předplatná Azure podrozsahů na adresu IP, které jsou vyhrazené pro použití ve virtuálních sítích v Azure. Prostřednictvím sítě VPN je vytvořeno připojení z místní sítě do Azure.

#### <a name="point-to-site-vpn"></a>Point-to-Site VPN
Point-to-site VPN vyžaduje každý klientský počítač se připojit s vlastním VPN do Azure. Pro scénáře SAP, které jsme prohlížení není praktické připojení point-to-site. Proto jsou uvedeny žádné další odkazy k připojení k síti VPN point-to-site.

Další informace naleznete zde
* [Konfigurace připojení typu Point-to-Site k virtuální síti pomocí webu Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurace připojení typu Point-to-Site k virtuální síti pomocí PowerShellu](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Více servery VPN
Azure v současné době také nabízí možnost vytvořit připojení VPN více lokalit pro jedno předplatné. V rámci jednoho předplatného dříve omezovala na jedno připojení site-to-site VPN. Toto omezení se rychle s připojeními VPN typu více lokalit pro v rámci jednoho předplatného. Díky tomu je možné využívat více než jedné oblasti Azure pro konkrétní předplatné prostřednictvím konfigurace mezi různými místy.

Další dokumentaci, najdete v tématu [v tomto článku][vpn-gateway-create-site-to-site-rm-powershell]

[comment]: <> (MShermannd TODO nalezen žádný odkaz doku ARM)

#### <a name="vnet-to-vnet-connection"></a>Virtuální síť připojení virtuální sítě
Použití více servery VPN, musíte nakonfigurovat samostatné virtuální sítě Azure v každé z oblastí. Máte ale velmi často požadavek, který by měl vzájemně komunikovat softwarové komponenty v různých oblastech. V ideálním případě tato komunikace by neměl směrovat z jedné oblasti Azure k místnímu a z ní do jiné oblasti Azure. Na zástupce Azure nabízí možnost konfigurace připojení z jedné virtuální sítě Azure v jedné oblasti s jinou virtuální sítí Azure hostovaná v jiné oblasti. Tato funkce je volána připojení VNet-to-VNet. Další podrobnosti o této funkci naleznete zde: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Privátní připojení k Azure ExpressRoute
Microsoft Azure ExpressRoute umožňuje vytvářet privátní připojení mezi datových center Azure a buď místní infrastruktury zákazníka nebo v prostředí ve společném umístění. ExpressRoute nabízí různé MPLS poskytovatelé sítě VPN (přepínáním paketů) nebo jiní poskytovatelé služeb sítě. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Připojení ExpressRoute nabízejí vyšší zabezpečení, spolehlivost další prostřednictvím více paralelních okruhů, vyšší rychlost a nižší latenci než Typická připojení přes Internet.

Najdete další informace o Azure ExpressRoute a nabídky tady:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Express Route umožňuje víc předplatných Azure prostřednictvím jeden okruh ExpressRoute, jak je uvedeno v tomto poli

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Vynucené tunelování v případě mezi různými místy
Pro virtuální počítače připojení k doménám místní prostřednictvím site-to-site, point-to-site nebo ExpressRoute budete muset nastavení internetového proxy serveru jsou získávání nasazení pro všechny uživatele v těchto virtuálních počítačů i. Ve výchozím nastavení software na těchto virtuálních počítačů nebo uživatelů pro přístup k Internetu pomocí prohlížeče by jít přes proxy server společnosti, ale by připojení přímo přes Azure k Internetu. Ale i nastavení proxy není 100 % řešení pro řízení provozu prostřednictvím proxy serveru společnosti, protože se jedná o odpovědnost softwaru a služeb ke kontrole pro proxy server. Pokud software spuštěný ve virtuálním počítači není učinit nebo správce manipuluje nastavení, provoz na Internetu může znovu detoured přímo přes Azure k Internetu.

Chcete-li předejít, můžete nakonfigurovat vynucené tunelování připojení site-to-site mezi místními a Azure. Podrobný popis funkce vynucené tunelování je zde publikována. <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Vynucené tunelové připojení s ExpressRoute je povoleno zákazníci inzeruje výchozí trasu prostřednictvím relace partnerského vztahu protokolu BGP ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Shrnutí sítě Azure
Tato kapitola obsahuje mnoho důležitých bodů o sítí Azure. Zde je souhrn hlavní body:

* Virtuální sítě Azure umožňuje nastavení sítě podle vlastních potřeb
* Virtuální sítě Azure můžete využít k přiřazení rozsahy IP adres k virtuálním počítačům nebo přiřadit pevné IP adresy virtuálních počítačů
* Nastavení připojení Site-To-Site nebo Point-To-Site, musíte nejprve vytvořit virtuální síť Azure
* Po nasazení virtuálního počítače se už nedají změnit ve virtuální síti přiřazené k virtuálnímu počítači

### <a name="quotas-in-azure-virtual-machine-services"></a>Kvóty pro služby Azure virtuálního počítače
Je potřeba mít o fakt, že úložiště a síťové infrastruktury jsou sdílena mezi virtuálních počítačů spuštěných v infrastruktuře Azure řady služeb. A stejně jako v datových centrech vlastní zákazníka, předimenzování některých prostředky infrastruktury v míře. Platforma Microsoft Azure používá disk, procesoru, sítě a jiných kvóty pro omezení spotřeby prostředků a chcete zachovat výkon konzistentní a deterministický.  Různé typy virtuálních počítačů (A5, A6 atd.) mají různé kvóty pro počet disků, procesoru, paměti RAM a sítě.

> [!NOTE]
> Prostředky procesoru a paměti typů virtuálních počítačů nepodporuje SAP jsou předběžně přidělených v hostitelských uzlech. To znamená, že po nasazení virtuálního počítače prostředků na hostiteli jsou k dispozici, jak jsou definovány podle typu virtuálního počítače.
>
>

Při plánování a změna velikosti SAP na řešení Azure je třeba zvážit kvóty pro každou velikost virtuálního počítače. Kvóty virtuálního počítače jsou popsány [zde (Linux)] [ virtual-machines-sizes-linux] a [zde (Windows)][virtual-machines-sizes-windows].

Kvót popsané představují teoretický maximální hodnoty.  Limit IOPS na disku lze dosáhnout s malé IOs (8kb), ale pravděpodobně nemusí být dosaženo s velké IOs (1 Mb).  IOPS limit se vynucuje u členitost jediný disk.

Jako hrubý rozhodovací strom rozhodnout, jestli se systému SAP zapadá do služby virtuálního počítače Azure a jeho funkce nebo jestli stávajícího systému musí být nakonfigurována jinak než k nasazení systému v Azure se dají použít rozhodovacího stromu níže:

![Rozhodovací strom k rozhodování o možnost nasazení SAP v Azure][planning-guide-figure-700]

**Krok 1**: nejdůležitější informace je spustit s protokoly SAP požadavkem pro daný systém SAP. Požadavky na protokoly SAP musí být oddělené do části databázového systému a část aplikace SAP, i v případě, že je v systému SAP již nasadit místně v konfiguraci vrstvy 2. Stávajících systémů můžete určit nebo odhadované podle existující srovnávacích testů SAP přístupové body související do hardwaru, který používá často. Výsledky naleznete zde: <http://global.sap.com/campaigns/benchmark/index.epx>.
Pro nově nasazené systémy SAP by měl prošli velikosti cvičení, které by měl určit požadavky na protokoly SAP systému.
Další informace tomto blogu a přiložený dokument pro SAP velikosti v Azure: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Krok 2**: stávajících systémů svazek vstupně-výstupních operací a vstupně-výstupních operací za sekundu na serveru databázového systému by se mělo měřit. Pro nově plánované systémy cvičení nastavení velikosti pro nový systém také měl dát hrubý nápady požadavky na vstupně-výstupních operací na straně databázového systému. Pokud si jisti, nakonec potřebujete provést testování konceptu.

**Krok 3**: porovnejte protokoly SAP požadavky pro server databázového systému s přístupové body, můžete zadat různé typy virtuálních počítačů Azure. Informace o protokoly SAP různých typů virtuální počítač Azure je popsána v Poznámka SAP [1928533]. Fokus musí být ve virtuálním počítači databázového systému nejprve vzhledem k tomu, že v databázové vrstvě je vrstva SAP NetWeaver systému, které není horizontální navýšení kapacity pro většinu nasazení. Naproti tomu SAP aplikační vrstvu lze škálovat. Pokud žádná z SAP podporované typy virtuální počítač Azure může poskytnout požadovaná přístupové body, zatížení plánované systému SAP nelze spustit v Azure. Buď musíte k nasazení systému místní nebo budete muset změnit svazek zatížení systému.

**Krok 4**: jak se píše [zde (Linux)] [ virtual-machines-sizes-linux] a [zde (Windows)][virtual-machines-sizes-windows], Azure vynucuje kvóty IOPS na disk nezávislé, zda používáte Standard Storage nebo Premium Storage. Závisí na typu virtuálního počítače, počet datových disků, které může být připojen se liší. V důsledku toho můžete vypočítat maximální IOPS číslo, které lze dosáhnout s jednotlivými různé typy virtuálních počítačů. Závisí na rozložení souboru databáze, vám může rozkládají disky se jeden svazek v hostovaný operační systém. Ale pokud aktuální svazek IOPS nasazené systému SAP překračuje počítané omezení typu největší virtuálních počítačů Azure a pokud neexistuje možnost odpovídajícím způsobem s více paměti, zatížení systému SAP ovlivněné můžou být vážně. V takových případech můžete dosáhl bodu, kde není vhodné nasazovat systému v Azure.

**Krok 5**: hlavně v systémech SAP, které jsou nasazené na místě v konfiguracích vrstvě 2, pravděpodobné, systém může být potřeba nakonfigurovat v Azure v konfiguraci vrstvy 3. V tomto kroku budete muset zkontrolovat, zda je součást v aplikační vrstvě SAP, který nelze škálovat na více systémů a který by začlenit do prostředků procesoru a paměti, které nabízejí různé typy virtuálního počítače Azure. Pokud skutečně existuje takový komponentu, SAP systému a jeho úlohy nelze nasadit do Azure. Ale pokud můžete škálovat součásti aplikace SAP do více virtuálních počítačů Azure, systém lze nasadit do Azure.

**Krok 6**: Pokud databázového systému a SAP součásti vrstvy aplikace lze spustit ve virtuálních počítačích Azure, musí být definován s ohledem na konfiguraci:

* Počet virtuálních počítačů Azure
* Typy virtuálních počítačů pro jednotlivé součásti
* Počet virtuálních pevných disků ve virtuálním počítači databázového systému zajistit dostatek IOPS

## <a name="managing-azure-assets"></a>Správa prostředků Azure
### <a name="azure-portal"></a>Azure Portal
Portál Azure je jedním z tři rozhraní pro správu nasazení virtuálního počítače Azure. Základní správu úkoly, jako je nasazení virtuálních počítačů z bitové kopie, lze provést prostřednictvím portálu Azure. Kromě toho vytváření účtů úložiště, virtuální sítě a jiných součástí Azure jsou také úlohy, které na portálu Azure může zpracovat velmi dobře. Funkce, jako jsou virtuální pevné disky nahrávat z místního do Azure nebo kopírování virtuálního pevného disku v rámci Azure jsou však úlohy, které vyžadují nástroje třetích stran nebo správu pomocí prostředí PowerShell nebo rozhraní příkazového řádku.

![Portál Microsoft Azure – Přehled virtuálních počítačů][planning-guide-figure-800]

[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

Úlohy správy a konfigurace pro instanci virtuálního počítače je možné z portálu Azure.

Kromě toho, restartování a vypínání virtuálního počítače můžete připojit, odpojte a vytvořte datových disků pro instanci virtuálního počítače, k zaznamenání instance pro přípravy bitové kopie a nakonfigurujte velikost instance virtuálního počítače.

Portál Azure poskytuje základní funkce můžete nasadit a nakonfigurovat virtuální počítače a mnoha dalším službám Azure. Ale ne všechny dostupné funkce se vztahuje na portálu Azure. Na portálu Azure není možné provést úkoly, jako je:

* Virtuální pevné disky se nahrávají na Azure
* Kopírování virtuálních počítačů

[comment]: <> (MShermannd TODO, co o automatizaci služby pro SAP virtuální počítače? )
[comment]: <> (MSSedusch nasazení více virtuálních počítačů os mezitím možné)
[comment]: <> (Jakýkoli typ automatizace týkající se nasazení také MSSedusch není možné pomocí portálu Azure. Úlohy, jako je skriptované nasazení více virtuálních počítačů není možná prostřednictvím portálu Azure.)

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Správa pomocí rutin prostředí PowerShell Microsoft Azure
Prostředí Windows PowerShell je výkonný a rozšiřitelný rámec, který má byla široce přijat zákazníky, kteří instalují velký počet systémů v Azure. Po instalaci rutin prostředí PowerShell na plochu, přenosných počítačů nebo vyhrazené správy stanice dá se spouštět rutiny prostředí PowerShell vzdáleně.

Povolit místní desktop nebo přenosný počítač pro použití rutin prostředí Azure PowerShell a postup konfigurace těch, které pro použití s předplatná Azure je popsaná v procesu [v tomto článku][powershell-install-configure].

Podrobnější postup k instalaci, aktualizaci a nakonfigurovat Azure PowerShell rutin najdete také v [této kapitoly v Průvodci nasazením][deployment-guide-4.1].

Zkušeností zákazníků, pokud byl prostředí PowerShell (PS) je určitě více výkonný nástroj pro nasazení virtuálních počítačů a vytvořit vlastní kroky v nasazení virtuálních počítačů. Všechny zákazníky spuštěné instance SAP v Azure používají rutiny PS doplníte úlohy správy udělat na portálu Azure, případně i pomocí rutiny PS výhradně ke správě jejich nasazeních v Azure. Protože rutiny specifické pro Azure sdílet stejné zásady vytváření názvů jako více než 2000 rutiny související s Windows, není jednoduchý úkol správcům systému Windows využívají tyto rutiny.

Podívejte se sem příklad: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd TODO popisují nový příkaz rozhraní příkazového řádku při testování )
Nasazení rozšíření monitorování Azure pro SAP (naleznete v kapitole [řešením pro monitorování Azure pro SAP] [ planning-guide-9.1] v tomto dokumentu) je možné pouze prostřednictvím prostředí PowerShell nebo rozhraní příkazového řádku. Proto je nutné vytvořit a nakonfigurovat prostředí PowerShell nebo rozhraní příkazového řádku pro nasazování nebo správu systém SAP NetWeaver v Azure.  

Jak Azure poskytuje další funkce, nové rutiny PS se chystáte přidat, který vyžaduje aktualizace rutin. Proto má smysl na serveru Azure stáhnout alespoň jednou v měsíci <https://azure.microsoft.com/downloads/> pro novou verzi rutiny. Nainstaluje se nová verze na starší verzi.

Obecnější seznam souvisejících s Azure PowerShell příkazy najdete tady: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Správu prostřednictvím Microsoft rozhraní příkazového řádku Azure
Pro zákazníky, kteří použijte Linux a chcete spravovat Azure nemusí být prostředků Powershell možnost. Společnost Microsoft nabízí jako alternativu rozhraní příkazového řádku Azure.
Rozhraní příkazového řádku Azure poskytuje sadu softwaru open source, příkazy a platformy pro práci s platformou Azure. Rozhraní příkazového řádku Azure poskytuje mnohem stejné funkce najít na portálu Azure.

Informace o instalaci, konfiguraci a používání rozhraní příkazového řádku najdete v části příkazy k provádění úloh Azure

* [Instalace rozhraní příkazového řádku Azure CLI][xplat-cli]
* [Nasadit a spravovat virtuální počítače pomocí šablony Azure Resource Manager a rozhraní příkazového řádku Azure] [../../linux/create-ssh-secured-vm-from-template.md]
* [Použití Azure CLI pro Mac, Linux a Windows pomocí Azure Resource Manageru][xplat-cli-azure-resource-manager]

Také číst kapitoly [rozhraní příkazového řádku Azure pro virtuální počítače s Linuxem] [ deployment-guide-4.5.2] v [Průvodce nasazením] [ planning-guide] týkající se používání rozhraní příkazového řádku Azure k nasazení rozšíření monitorování Azure pro SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Různé způsoby k nasazení virtuálních počítačů pro SAP v Azure
V této kapitoly zjistíte různé způsoby, jak nasadit virtuální počítač v Azure. V této kapitole jsou popsané postupy další přípravy, jakož i zpracování virtuální pevné disky a virtuální počítače v Azure.

### <a name="deployment-of-vms-for-sap"></a>Nasazení virtuálních počítačů pro SAP
Microsoft Azure nabízí několik způsobů pro nasazení virtuálních počítačů a přidruženými disky. Proto je důležité pochopit rozdíly, od přípravy virtuálních počítačů se můžou lišit v závislosti na metodě nasazení. Obecně platí jsme podívejte se na následující scénáře:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Přesunutí virtuálního počítače z místního do Azure s diskem zobecněn
Máte v úmyslu přesunout z místní konkrétního systému SAP do Azure. Tento krok můžete provést tím, že nahrajete virtuální pevný disk, který obsahuje operační systém, SAP binární soubory a binární soubory databázového systému plus virtuální pevné disky se soubory protokolu a data z databázového systému Azure. Rozdíl k [scénář #2 níže][planning-guide-5.1.2], zachovat název hostitele, identifikátor SID SAP, a SAP uživatelských účtů ve virtuálním počítači Azure, jak byla nakonfigurována v místním prostředí. Proto generalizací bitovou kopii není nutné. Najdete v kapitolách [přípravy pro přesun virtuálního počítače z místního do Azure s diskem zobecněn] [ planning-guide-5.2.1] tohoto dokumentu pro místní přípravné kroky a odesílání-zobecněný virtuální počítače nebo virtuální pevné disky do Azure. Čtení kapitoly [scénář 3: přesunutí virtuálního počítače z místního virtuálního pevného disku není zobecněný Azure pomocí SAP] [ deployment-guide-3.4] v [Průvodce nasazením] [ deployment-guide] podrobný postup nasazení takové bitovou kopii v Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Nasazení virtuálního počítače s bitovou kopii zákaznické
Z důvodu opravy specifické požadavky vaší verzí operačního systému nebo databázového systému nemusí zadané bitové kopie v Azure Marketplace podle vašich potřeb. Proto může být potřeba vytvořit virtuální počítač pomocí vlastní privátní bitové kopie operačního systému nebo databázového systému virtuálních počítačů, které mohou být nasazeny několikrát později. Pro přípravu privátní image pro duplikaci, musí být považovány za následující položky:

- - -
> ![Windows][Logo_Windows] Windows
>
> Zobrazte další podrobnosti zde: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> nastavení systému Windows (např. Windows SID a název hostitele) musí být zobecněn na místní virtuální počítač pomocí příkazu sysprep jejich abstrahované.
>
>
> ![Linux][Logo_Linux] Linux
>
> Postupujte podle kroků popsaných v těchto článcích pro [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd], nebo [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle], připravit virtuální pevný disk má nahrát do Azure.
>
>

- - -
Pokud jste již nainstalovali SAP obsah v místní virtuální počítač (hlavně u systémy vrstvě 2), můžete upravit nastavení systému SAP po nasazení virtuálního počítače Azure pomocí instance přejmenovat postup nepodporuje správce zřizování softwaru SAP (Poznámka SAP [1619720]). Najdete v kapitolách [přípravy pro nasazení virtuálního počítače s bitovou kopii zákaznické pro SAP] [ planning-guide-5.2.2] a [odesílání virtuálního pevného disku z místního do Azure] [ planning-guide-5.3.2] tohoto dokumentu pro místní přípravné kroky a odesílání zobecněný virtuální počítač do Azure. Čtení kapitoly [scénář 2: nasazení virtuálního počítače s vlastní image pro SAP] [ deployment-guide-3.3] v [Průvodce nasazením] [ deployment-guide] podrobný postup nasazení takové bitovou kopii v Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Nasazení virtuálních počítačů z Azure Marketplace
Chcete používat Microsoft nebo třetích stran, pokud image virtuálního počítače v Azure Marketplace pro nasazení virtuálního počítače. Po nasazení virtuálního počítače v Azure použijte stejné pokyny a nástroje pro instalaci softwaru SAP nebo databázového systému uvnitř virtuálního počítače, jako byste to udělali v místním prostředí. Podrobnější popis nasazení, získáte v kapitole [scénář 1: nasazení virtuálního počítače z Azure Marketplace pro SAP] [ deployment-guide-3.2] v [Průvodce nasazením][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Příprava na Azure virtuálních počítačů s SAP
Před nahráním virtuálních počítačů do Azure, budete muset zajistěte, aby virtuální počítače a virtuální pevné disky splnit určité požadavky. Existují malé rozdíly v závislosti na metodě nasazení, který se používá.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Příprava pro přesun virtuálního počítače z místního do Azure s diskem zobecněn
Běžnou metodu nasazení je přesunout existující virtuální počítač, na kterém běží systém SAP z místního do Azure. Tento virtuální počítač a SAP systém ve virtuálním počítači právě měli spustit v Azure pomocí stejný název hostitele a velmi pravděpodobně stejným identifikátorem SID SAP. V takovém případě by neměl být zobecněn hostovaného operačního systému virtuálního počítače pro více nasazení. Pokud je tu rozšířené místní sítě do Azure (naleznete v kapitole [mezi různými místy - nasazení jedné nebo více virtuálních počítačů SAP do Azure s požadavkem je plně integrován do místní sítě] [ planning-guide-2.2] v tomto dokumentu), pak i stejné účty domény je možné v rámci virtuálního počítače jako těch, které byly používány před místně.

Požadavky při přípravě vlastní disku virtuálního počítače Azure jsou:

* Maximální velikost 127GB pouze může být původně virtuálního pevného disku obsahující operační systém. Toto omezení získali eliminovat na konci března 2015. Virtuální pevný disk obsahující operační systém nyní může být velikost až 1TB, jako ostatní Azure Storage i hostované virtuální pevný disk.
* Musí se jednat o ve formátu pevný virtuální pevný disk. Dynamické virtuální pevné disky nebo virtuální pevné disky ve formátu VHDx se ještě nepodporují v Azure. Dynamické virtuální pevné disky budou převedeny na statický virtuálních pevných disků při nahrávání virtuální pevný disk pomocí rutiny prostředí PowerShell nebo rozhraní příkazového řádku
* Virtuální pevné disky, které jsou připojené k virtuálnímu počítači a musí být připojené znovu v Azure do virtuálního počítače potřeba mít také pevné Formát virtuálního pevného disku. Přečtěte si prosím [v tomto článku (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) a [v tomto článku (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) pro omezení velikosti datových disků. Dynamické virtuální pevné disky budou převedeny na statický virtuálních pevných disků při nahrávání virtuální pevný disk pomocí rutiny prostředí PowerShell nebo rozhraní příkazového řádku
* Přidáte, může být použit jiný místní účet s oprávněními správce, které mohou být využívána podpory společnosti Microsoft nebo které lze přiřadit jako kontext pro služeb a aplikací na spouštění v, dokud je virtuální počítač nasazen a více příslušné uživatele.
* Pro případ použití scénáři jenom pro Cloud nasazení (naleznete v kapitole [jenom pro Cloud - nasazení virtuálních počítačů do Azure bez závislosti na místní sítě zákazníka] [ planning-guide-2.1] tohoto dokumentu) v kombinaci s Tato metoda nasazení, účtů domény nemusí fungovat po disku Azure je nasazené v Azure. To platí hlavně pro účty, které se používají ke spouštění služeb jako databázového systému nebo SAP aplikací. Proto musíte pro tyto účty domény nahraďte místní účty virtuálního počítače a odstranění místních účtů domény ve virtuálním počítači. Zachování místní domény uživatele do bitové kopie virtuálního počítače se nejedná o problém při je virtuální počítač nasazen ve scénáři mezi různými místy, jak je popsáno v kapitole [mezi různými místy - nasazení jedné nebo více virtuálních počítačů SAP do Azure s požadavkem je plně integrován do místní sítě] [ planning-guide-2.2] v tomto dokumentu.
* Pokud se při spuštění systému na místě a tyto virtuální počítače jsou by měl být nasazen v čistě cloudové scénáře účty domény používaly jako databázového systému přihlášení nebo uživatelé, třeba odstranit uživatele domény. Musíte zajistit, že místní správce a jiný virtuální počítač místní uživatel je přidán jako přihlášení uživatele do databázového systému jako správci.
* Přidejte další místní účty, jak těch, které mohou být potřebné pro tento scénář konkrétní nasazení.

- - -
> ![Windows][Logo_Windows] Windows
>
> V tomto scénáři žádné generalizace (sysprep) virtuálního počítače je potřeba nahrát a nasadit virtuální počítač na platformě Azure.
> Ujistěte se, že jednotku, kterou nepoužívá D:\.
> Nastavení automatického připojení disku pro připojené disky, jak je popsáno v kapitole [nastavení automatického připojení pro připojené disky] [ planning-guide-5.5.3] v tomto dokumentu.
>
> ![Linux][Logo_Linux] Linux
>
> V tomto scénáři žádné generalizace (příkaz waagent-deprovision) virtuálního počítače je potřeba nahrát a nasadit virtuální počítač na platformě Azure.
> Ujistěte se, zda se nepoužívá/mnt nebo prostředků a že jsou všechny disky připojené přes uuid. Pro disk operačního systému ověřte, že položka zaváděcí program pro spouštění také změna připojení na základě uuid.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Příprava pro nasazení virtuálního počítače s bitovou kopii zákaznické pro SAP
Soubory virtuálního pevného disku, které obsahují zobecněný operačního systému jsou uloženy v kontejnery v účtech úložiště Azure nebo jako spravované diskové Image. Můžete nasadit nový virtuální počítač z takových bitové kopie odkazující na bitovou kopii virtuálního pevného disku nebo Disk spravované jako zdroj v souborech šablony nasazení, jak je popsáno v kapitole [scénář 2: nasazení virtuálního počítače s vlastní image pro SAP] [ deployment-guide-3.3] z [Průvodce nasazením][deployment-guide].

Požadavky při přípravě vlastní Image virtuálního počítače Azure jsou:

* Maximální velikost 127GB pouze může být původně virtuálního pevného disku obsahující operační systém. Toto omezení získali eliminovat na konci března 2015. Virtuální pevný disk obsahující operační systém nyní může být velikost až 1TB, jako ostatní Azure Storage i hostované virtuální pevný disk.
* Musí se jednat o ve formátu pevný virtuální pevný disk. Dynamické virtuální pevné disky nebo virtuální pevné disky ve formátu VHDx se ještě nepodporují v Azure. Dynamické virtuální pevné disky budou převedeny na statický virtuálních pevných disků při nahrávání virtuální pevný disk pomocí rutiny prostředí PowerShell nebo rozhraní příkazového řádku
* Virtuální pevné disky, které jsou připojené k virtuálnímu počítači a musí být připojené znovu v Azure do virtuálního počítače potřeba mít také pevné Formát virtuálního pevného disku. Přečtěte si prosím [v tomto článku (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) a [v tomto článku (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) pro omezení velikosti datových disků. Dynamické virtuální pevné disky budou převedeny na statický virtuálních pevných disků při nahrávání virtuální pevný disk pomocí rutiny prostředí PowerShell nebo rozhraní příkazového řádku
* Vzhledem k tomu, že všichni uživatelé domény registrovaný jako uživatelům ve virtuálním počítači nebude existovat ve scénáři jenom pro Cloud (naleznete v kapitole [jenom pro Cloud - nasazení virtuálních počítačů do Azure bez závislosti na místní sítě zákazníka] [ planning-guide-2.1] tohoto dokumentu), služby pomocí takové domény účty nemusí fungovat po bitovou kopii je nasazené v Azure. To platí hlavně pro účty, které se používají ke spouštění služeb jako databázového systému nebo SAP aplikací. Proto musíte pro tyto účty domény nahraďte místní účty virtuálního počítače a odstranění místních účtů domény ve virtuálním počítači. Zachování místní domény uživatele do bitové kopie virtuálního počítače nemusí být problém, když je virtuální počítač nasazen ve scénáři mezi různými místy, jak je popsáno v kapitole [mezi různými místy - nasazení jedné nebo více virtuálních počítačů SAP do Azure s požadavkem je plně integrován do místní sítě] [ planning-guide-2.2] v tomto dokumentu.
* Přidáte, může být použit jiný místní účet s oprávněními správce, které mohou být využívána podpory společnosti Microsoft v vyšetřování problém nebo které lze přiřadit jako kontext pro služeb a aplikací na spouštění v, dokud je virtuální počítač nasazen a více příslušné uživatele.
* V nasazení jenom pro Cloud a kde účty domény používaly jako databázového systému přihlášení nebo uživatelé při spuštění systému na místě měli byste odstranit uživatele domény. Musíte zajistit, že místní správce a jiný uživatel místní počítač přidán jako přihlášení nebo uživatel databázového systému jako správci.
* Přidejte další místní účty, jak těch, které mohou být potřebné pro tento scénář konkrétní nasazení.
* Pokud bitová kopie obsahuje instalace SAP NetWeaver a přejmenování názvu hostitele z původní název v okamžiku nasazení Azure je pravděpodobné, že doporučuje se nejnovější verze SAP softwaru zřizování Manager DVD zkopírovat do šablony. To vám umožní snadno použít funkci SAP zadané přejmenování přizpůsobit změněné název hostitele nebo změnit SID systému SAP v nasazené bitové kopie virtuálního počítače, jakmile se spustí novou kopií.

- - -
> ![Windows][Logo_Windows] Windows
>
> Ujistěte se, že jednotku D:\ není použít automatického připojení disku sady pro připojené disky, jak je popsáno v kapitole [nastavení automatického připojení pro připojené disky] [ planning-guide-5.5.3] v tomto dokumentu.
>
> ![Linux][Logo_Linux] Linux
>
> Ujistěte se, zda se nepoužívá/mnt nebo prostředků a že jsou všechny disky připojené přes uuid. Pro disk operačního systému zkontrolujte, zda že položka zaváděcí program pro spouštění také odráží připojení na základě uuid.
>
>

- - -
* SAP grafickým uživatelským rozhraním (pro správu a instalační program pro účely) může být předem nainstalován v takové šablonu.
* Další potřebné ke spuštění virtuálních počítačů, které jsou úspěšně ve scénářích mezi různými místy software se dá nainstalovat, dokud tento software může spolupracovat s přejmenování virtuálního počítače.

Jestliže virtuální počítač je připravená dostatečně obecné a nakonec nezávislé na účty či uživatelů ve scénáři nasazenou Azure není k dispozici, je provedena poslední krok přípravy generalizací takové image.

##### <a name="generalizing-a-vm"></a>Generalizací virtuálního počítače
- - -
> ![Windows][Logo_Windows] Windows
>
> Posledním krokem je k přihlášení k virtuálnímu počítači pomocí účtu správce. Otevřete příkazové okno Windows jako *správce*. Přejděte do %windir%\windows\system32\sysprep a provést sysprep.exe.
> Zobrazí se okno malé. Je důležité zkontrolovat **generalizace** možnost (výchozí hodnota je vypnuta) a změňte možnost vypnutí z výchozí hodnoty, restartování' 'vypnutí'. Tento postup předpokládá, že procesu nástroje sysprep je spustit místně v hostovaného operačního systému virtuálního počítače.
> Pokud chcete provést postup v případě virtuálních počítačů už běží v Azure, postupujte podle kroků popsaných v [v tomto článku](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Jak zachytit virtuální počítač s Linuxem chcete použít jako šablony Resource Manageru][capture-image-linux-step-2-create-vm-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Přenos virtuálních počítačů a virtuálních pevných disků mezi místním nasazením a Azure
Vzhledem k tomu, že nahrání Image virtuálních počítačů a disků do Azure není možné prostřednictvím portálu Azure, budete muset použít rutiny prostředí Azure PowerShell nebo rozhraní příkazového řádku. Další možností je použití nástroje 'AzCopy'. Nástroj můžete zkopírovat virtuální pevné disky mezi místními a Azure (v obou směrech). Je taky zkopírovat virtuální pevné disky mezi oblastmi Azure. Přečtěte si [této dokumentace] [ storage-use-azcopy] pro stažení a použití nástroje AzCopy.

Třetí možnost může být použití různých nástrojů orientovaných na grafickém uživatelském rozhraní třetích stran. Ale ujistěte, že tyto nástroje jsou podpora objekty BLOB stránky Azure. Pro naše účely potřebujeme používat úložiště objektů Blob stránky Azure (rozdíly jsou popsány zde: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Také nástroje poskytované subsystémem pro Azure jsou velmi efektivní v kompresi virtuálních počítačů a virtuálních pevných disků, které je třeba k odeslání. To je důležité, protože této efektivity v komprese snižuje čas odeslání (který se liší přesto podle nahrávání odkaz k Internetu z místní zařízením a cílem nasazení Azure oblast). Je správného předpoklad, že odesílání virtuálního počítače nebo virtuálního pevného disku z Evropského umístění na základě USA Azure dat v centrech bude trvat déle než stejné virtuální počítače nebo virtuální pevné disky se nahrávají na Evropského Azure datová centra.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Nahrání virtuálního pevného disku z místního do Azure
Nahrát na existující virtuální počítač nebo virtuální pevný disk z místní sítě virtuálních počítačů nebo virtuální pevný disk musí splňovat požadavky uvedené v kapitole [přípravy pro přesun virtuálního počítače z místního do Azure s diskem zobecněn] [ planning-guide-5.2.1] tohoto dokumentu.

Virtuální počítač nemusí být zobecněn a ve stavu a tvar, který se má po vypnutí na straně místní je možné uložit. Totéž platí pro další virtuální pevné disky, které neobsahují žádný operační systém.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Nahrání virtuálního pevného disku a jejich zpřístupnění diskem Azure
V tomto případě chceme nahrát VHD, s nebo bez operační systém v něm a připojte k virtuálnímu počítači jako datový disk nebo ho používat jako disk s operačním systémem. Jedná se o vícekrokový proces

**Powershell**

* Přihlaste se k vašemu předplatnému s *Login-AzureRmAccount*
* Nastavte předplatné váš kontext s *Set-AzureRmContext* a parametr ID předplatného nebo Název_předplatného - najdete v části <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Nahrání virtuálního pevného disku s *přidat AzureRmVhd* účet úložiště Azure - najdete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Volitelné) Vytvoření spravované disku z virtuálního pevného disku s *New-AzureRmDisk* -najdete v části <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk>
* Nastavte disku operačního systému nová konfigurace virtuálního počítače na virtuální pevný disk nebo spravované Disk s *Set-AzureRmVMOSDisk* -najdete v části <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
* Vytvoření nového virtuálního počítače z konfigurace virtuálního počítače s *New-AzureRmVM* -najdete v části <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>
* Přidat datový disk do nového virtuálního počítače s *přidat AzureRmVMDataDisk* -najdete v části <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk>

**Azure CLI 2.0**

* Přihlaste se k vašemu předplatnému s *az přihlášení*
* Vyberte předplatné s *nastaven účet az--předplatného `<subscription name or id`>*
* Nahrání virtuálního pevného disku s *az úložiště objektů blob nahrávání* -najdete v části [použití Azure CLI s Azure Storage][storage-azure-cli]
* (Volitelné) Vytvoření spravované disku z virtuálního pevného disku s *vytvoření disku az* -najdete v části https://docs.microsoft.com/cli/azure/disk#az_disk_create
* Vytvoření nového virtuálního počítače zadání nahraný virtuální pevný disk nebo spravované Disk jako disk operačního systému s *vytvořit virtuální počítač az* a parametr *– připojit disk operačního systému*
* Přidat datový disk do nového virtuálního počítače s *připojit disk virtuálního počítače az* a parametr *– nový*

**Šablona**

* Nahrání virtuálního pevného disku pomocí prostředí Powershell nebo Azure CLI
* (Volitelné) Vytvoření spravované disku z virtuálního pevného disku pomocí prostředí Powershell, rozhraní příkazového řádku Azure nebo portálu Azure
* Nasaďte virtuální počítač pomocí šablony JSON odkazující na virtuální pevný disk, jak je znázorněno v [této šablony JSON příklad](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json) nebo pomocí spravovaných disků, jak je znázorněno v [této šablony JSON příklad](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-2-tier-user-disk-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Nasazení Image virtuálního počítače
Nahrát na existující virtuální počítač nebo virtuální pevný disk z místní sítě chcete-li použít bitovou kopii virtuálního počítače Azure virtuálních počítačů nebo virtuální pevný disk budou muset splnit požadavky uvedené v kapitole [přípravy pro nasazení virtuálního počítače s bitovou kopii zákaznické pro SAP] [ planning-guide-5.2.2] tohoto dokumentu.

* Použít *sysprep* v systému Windows nebo *příkaz waagent-deprovision* v systému Linux ke generalizaci virtuálního počítače – viz [technické informace o nástroji Sysprep](https://technet.microsoft.com/library/cc766049.aspx) pro systém Windows nebo [jak zachytit virtuální počítač s Linuxem chcete použít jako šablony Resource Manageru] [ capture-image-linux-step-2-create-vm-image] pro Linux
* Přihlaste se k vašemu předplatnému s *Login-AzureRmAccount*
* Nastavte předplatné váš kontext s *Set-AzureRmContext* a parametr ID předplatného nebo Název_předplatného - najdete v části <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Nahrání virtuálního pevného disku s *přidat AzureRmVhd* účet úložiště Azure - najdete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Volitelné) Vytvoření bitové kopie disku spravované z virtuálního pevného disku s *New-AzureRmImage* -najdete v části <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage>
* Nastavit nové konfigurace virtuálního počítače na disku operačního systému
  * Virtuální pevný disk s *Set AzureRmVMOSDisk - SourceImageUri - CreateOption fromImage* -najdete v části <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
  * Spravované bitové kopie disku *Set-AzureRmVMSourceImage* -najdete v části <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage>
* Vytvoření nového virtuálního počítače z konfigurace virtuálního počítače s *New-AzureRmVM* -najdete v části <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>

**Azure CLI 2.0**

* Použít *sysprep* v systému Windows nebo *příkaz waagent-deprovision* v systému Linux ke generalizaci virtuálního počítače – viz [technické informace o nástroji Sysprep](https://technet.microsoft.com/library/cc766049.aspx) pro systém Windows nebo [jak zachytit virtuální počítač s Linuxem chcete použít jako šablony Resource Manageru] [ capture-image-linux-step-2-create-vm-image] pro Linux
* Přihlaste se k vašemu předplatnému s *az přihlášení*
* Vyberte předplatné s *nastaven účet az--předplatného `<subscription name or id`>*
* Nahrání virtuálního pevného disku s *az úložiště objektů blob nahrávání* -najdete v části [použití Azure CLI s Azure Storage][storage-azure-cli]
* (Volitelné) Vytvoření bitové kopie disku spravované z virtuálního pevného disku s *vytvoření bitové kopie az* -najdete v části https://docs.microsoft.com/cli/azure/image#az_image_create
* Vytvoření nového virtuálního počítače zadání nahraný virtuální pevný disk nebo spravovat bitové kopie disku jako disk operačního systému s *vytvořit virtuální počítač az* a parametr *– bitové kopie*

**Šablona**

* Použít *sysprep* v systému Windows nebo *příkaz waagent-deprovision* v systému Linux ke generalizaci virtuálního počítače – viz [technické informace o nástroji Sysprep](https://technet.microsoft.com/library/cc766049.aspx) pro systém Windows nebo [jak zachytit virtuální počítač s Linuxem chcete použít jako šablony Resource Manageru] [ capture-image-linux-step-2-create-vm-image] pro Linux
* Nahrání virtuálního pevného disku pomocí prostředí Powershell nebo Azure CLI
* (Volitelné) Vytvoření bitové kopie disku spravované z virtuálního pevného disku pomocí prostředí Powershell, rozhraní příkazového řádku Azure nebo portálu Azure
* Nasaďte virtuální počítač pomocí šablony JSON odkazující na bitovou kopii virtuálního pevného disku, jak je znázorněno v [této šablony JSON příklad](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-2-tier-user-image/azuredeploy.json) nebo pomocí bitové kopie disku spravovat, jak je znázorněno v [této šablony JSON příklad](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Při stahování virtuální pevné disky nebo spravovaných disků na místní
Azure infrastruktura jako služba není jednosměrný ulici, kde pouze bude možné nahrát virtuální pevné disky a SAP systémy. Můžete přesunout SAP systémy z Azure zpět do na místní světě.

Během doby stahování nemůže být virtuální pevné disky nebo disky spravované aktivní. I když stahování disky, které jsou připojené k virtuálním počítačům, musí virtuální počítač vypnout a navrácena. Pokud chcete stáhnout obsah databáze který pak by měla být používána k nastavení nového systému místní a pokud je přijatelné, že během doby stahování a instalace nového systému, aby systém v Azure může být stále provozní, se můžete vyhnout dlouho výpadek komprimované databáze zálohování na disk, jenom stažení tohoto disku místo také stahování základní virtuální počítač operačního systému.

#### <a name="powershell"></a>PowerShell

  * Stahování se spravovaným diskem  
  Musíte nejprve získat přístup k základní objekt blob spravované disku. Pak můžete zkopírovat základní objekt blob do nového účtu úložiště a stáhnout objekt blob z tohoto účtu úložiště.

  ```powershell
  $access = Grant-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzureRmStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzureStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzureStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzureStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzureRmVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

  * Stahování virtuální pevný disk  
  Po zastavení systému SAP a virtuální počítač je vypnutý, můžete použít rutinu prostředí PowerShell AzureRmVhd uložit na místní cíl pro stažení disky VHD na světě místní. Aby bylo možné provést, potřebujete adresu URL virtuálního pevného disku, které můžete najít v 'úložiště oddíl' z portálu Azure (třeba k účtu úložiště a kontejneru úložiště, kde byl vytvořen virtuální pevný disk) a potřebujete vědět, kde má být virtuální pevný disk zkopírována.

  Pak můžete použít příkaz definováním jednoduše parametr SourceUri jako adresu URL VHD, který chcete stáhnout a LocalFilePath jako fyzické umístění virtuálního pevného disku (včetně jeho název). Příkaz může vypadat podobně jako:

  ```powerhell
  Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Další podrobnosti rutiny AzureRmVhd uložit, zkontrolujte, zde <https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvhd>.

#### <a name="cli-20"></a>CLI 2.0
  * Stahování se spravovaným diskem  
  Musíte nejprve získat přístup k základní objekt blob spravované disku. Pak můžete zkopírovat základní objekt blob do nového účtu úložiště a stáhnout objekt blob z tohoto účtu úložiště.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

  * Stahování virtuální pevný disk   
  Po zastavení systému SAP a virtuální počítač je vypnutý, můžete použít příkaz příkazového řádku Azure CLI _úložiště azure blob stažení_ na místní cíl, který má stažení virtuální pevný disk disky zpět na místní world. Aby bylo možné provést, je nutné název a kontejneru virtuálního pevného disku, které můžete najít v 'části úložiště, portálu Azure (třeba k účtu úložiště a kontejneru úložiště, kde byl vytvořen virtuální pevný disk) a je potřeba vědět, kde má být virtuální pevný disk copi ED k.

  Pak můžete použít příkaz tak, že jednoduše definujete parametry objektu blob a kontejner virtuálního pevného disku stahování a cíl jako fyzické cílové umístění virtuálního pevného disku (včetně jeho název). Příkaz může vypadat podobně jako:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Přenášení virtuální počítače a disky v rámci Azure
#### <a name="copying-sap-systems-within-azure"></a>Kopírování SAP systémy v rámci Azure
Systému SAP nebo i vyhrazený server databázového systému podpora SAP aplikační vrstva bude pravděpodobně obsahovat několik disků, které budou obsahovat buď operačního systému se binární soubory nebo soubory protokolu a data z databáze SAP. Funkci Azure kopírování disků ani Azure funkce ukládání disků na místní disk má synchronizační mechanismus, který by snímku více disků synchronně. Proto stav disků zkopírovaný nebo uložené i v případě, že těch, které jsou připojené proti stejného virtuálního počítače by lišit. To znamená, že v případě konkrétní toho, že logfile(s) obsažené v různých discích a různých dat databázi v end by být nekonzistentní.

**Uzavření: Aby bylo možné kopírovat nebo uložení disků, které jsou součástí konfigurace aplikace SAP systému musíte zastavit v systému SAP a také muset vypnout nasazených virtuálních počítačů. Pak můžete zkopírovat nebo stáhnout sadu disků, aby buď vytvořit kopii systému SAP v Azure nebo místní.**

Datové disky mohou být uloženy jako soubory virtuálního pevného disku v účtu úložiště Azure a může být přímo připojen k virtuálnímu počítači nebo použít jako obrázek. V takovém případě virtuálního pevného disku se zkopíruje do jiného umístění před se připojit k virtuálnímu počítači. Úplný název souboru VHD v Azure musí být jedinečný v rámci Azure. Jak už bylo zmíněno dříve již, je název druh třemi částmi název, který vypadá takto:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Datových disků může být také spravovat disky. V takovém případě spravované Disk se používá k vytvoření nového disku spravované před se připojit k virtuálnímu počítači. Název disku spravované musí být jedinečný v rámci skupiny prostředků.

##### <a name="powershell"></a>PowerShell
Rutiny prostředí Azure PowerShell můžete použít ke zkopírování virtuálního pevného disku, jak je znázorněno v [v tomto článku][storage-powershell-guide-full-copy-vhd]. Pokud chcete vytvořit nový Disk, který je spravovaný, pomocí New-AzureRmDiskConfig a New-AzureRmDisk jak je znázorněno v následujícím příkladu.

```powershell
$config = New-AzureRmDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzureRmDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="cli-20"></a>CLI 2.0
Rozhraní příkazového řádku Azure můžete kopírovat virtuální pevný disk, jak je znázorněno v [v tomto článku][storage-azure-cli-copy-blobs]. Chcete-li vytvořit nový Disk, který je spravovaný, použijte *vytvoření disku az* jak je znázorněno v následujícím příkladu.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure Storage tools
* <http://storageexplorer.com/>

Profesionální edicí Průzkumníci úložiště Azure naleznete zde:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

Kopii samotného virtuálního pevného disku v rámci účtu úložiště je proces, který trvá jenom pár sekund (podobně jako hardware sítě SAN vytváření snímků s opožděné a kopii na zápis). Až budete mít kopii souboru virtuálního pevného disku můžete připojit k virtuálnímu počítači nebo ho použít jako obrázek pro připojit k virtuálním počítačům kopie virtuálního pevného disku.

##### <a name="powershell"></a>PowerShell
```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzureRmVM

# attach a copy of the managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzureRmDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzureRmDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzureRmVM
```
##### <a name="cli-20"></a>CLI 2.0
```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Kopírování disků mezi účty úložiště Azure
Tuto úlohu nelze provést na portálu Azure. Můžete použít rutiny prostředí Azure PowerShell, rozhraní příkazového řádku Azure nebo prohlížeč úložiště jiných výrobců. Rutiny prostředí PowerShell nebo rozhraní příkazového řádku můžete vytvořit a spravovat objekty BLOB, které zahrnují možnost asynchronně zkopírovat objekty BLOB mezi různými účty úložiště a v oblastech v rámci předplatného Azure.

##### <a name="powershell"></a>PowerShell
Můžete také zkopírovat virtuální pevné disky, mezi předplatnými. Další informace najdete v tématu [v tomto článku][storage-powershell-guide-full-copy-vhd].

Základní tok logiku rutiny PS vypadá takto:

* Vytvoření kontextu pro účet úložiště **zdroj** účet úložiště s *New-AzureStorageContext* -najdete v části <https://msdn.microsoft.com/library/dn806380.aspx>
* Vytvoření kontextu pro účet úložiště **cíl** účet úložiště s *New-AzureStorageContext* -najdete v části <https://msdn.microsoft.com/library/dn806380.aspx>
* Spusťte kopírování pomocí

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Zkontrolujte stav kopírování ve smyčce s

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* K virtuálnímu počítači připojte nový virtuální pevný disk, jak je popsáno výše.

Příklady najdete v tématu [v tomto článku][storage-powershell-guide-full-copy-vhd].

##### <a name="cli-20"></a>CLI 2.0
* Spusťte kopírování pomocí

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Zkontrolujte stav, pokud kopie ve smyčce s

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* K virtuálnímu počítači připojte nový virtuální pevný disk, jak je popsáno výše.

Příklady najdete v tématu [v tomto článku][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Zpracování disku
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Virtuální počítač nebo disk strukturu pro nasazení SAP
V ideálním případě by měl být zpracování struktury virtuálního počítače a přidružené disky velmi jednoduché. V místní instalace vyvinuté zákazníkům mnoho způsobů strukturování instalaci serveru.

* Jeden základní disk, který obsahuje operační systém a všechny binární soubory databázového systému a/nebo SAP. Od března 2015 tento disk může být až 1TB velikosti místo starší omezení, která je omezený na 127 GB.
* Soubor databáze SAP protokolu jeden nebo více discích, které obsahuje systému správy databáze a soubor protokolu databázového systému dočasné oblasti úložiště (Pokud databázového systému podporuje to). Pokud jsou požadavky IOPS protokolu databáze. vysoká, budete muset rozkládají víc disků, aby bylo dosaženo IOPS svazek potřebný.
* Počet disků obsahující jeden nebo dva soubory databáze z databáze SAP a i soubory dočasná data databázového systému (Pokud databázového systému podporuje to).

![Konfigurace referenčního virtuálního počítače Azure IaaS pro SAP][planning-guide-figure-1300]

[comment]: <> (Struktura Linux popisují MShermannd TODO  )

- - -
> ![Windows][Logo_Windows] Windows
>
> U mnoha zákazníků jsme viděli konfigurace, například SAP a databázového systému binární soubory nebyly nainstalovanou na jednotce c:\ nainstalovaným operačním systémem. Existují různé důvody pro to, ale když jsme se zpět do kořenového adresáře, obvykle byl, jednotky byly malé a upgrady operačního systému před 10 až 15 let potřeba další místo. Obě podmínky dní příliš často už neplatí. Dnes jednotka c:\ lze mapovat na velkého objemu disků nebo virtuálních počítačů. Chcete-li zjednodušení nasazení v jejich struktura se doporučuje použít následující vzor nasazení SAP NetWeaver systémy v Azure
>
> Stránkovací soubor operačního systému Windows musí být na jednotce D: (dočasnou disk)
>
> ![Linux][Logo_Linux] Linux
>
> Umístěte swapfile Linux pod /mnt/mnt nebo prostředků v systému Linux, jak je popsáno v [v tomto článku][virtual-machines-linux-agent-user-guide]. Odkládací soubor lze nakonfigurovat v souboru konfigurace /etc/waagent.conf agenta systému Linux. Přidat nebo změnit následující nastavení:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Pokud chcete aktivovat změny, bude nutné restartovat agenta systému Linux s

```
sudo service waagent restart
```

Přečtěte si prosím Poznámka SAP [1597355] další podrobnosti o jeho doporučená velikost

- - -
Počet disků používat pro datové soubory databázového systému a typ úložiště Azure, jsou tyto disky hostované na by měl určit požadavky na IOPS a latence vyžaduje. Přesný kvóty jsou popsané v [v tomto článku (Linux)] [ virtual-machines-sizes-linux] a [v tomto článku (Windows)][virtual-machines-sizes-windows].

Prostředí nasazení SAP za poslední 2 roky nám výukové některé lekce, které jde vyhodnotit jako:

* IOPS provoz do různých datových souborů není vždy stejná od stávajících systémů zákazníků může mít jinak velikost datové soubory představující jejich SAP databází. V důsledku ji vrátit na lépe používat konfiguraci RAID přes více disků pro soubory dat, které logické jednotky LUN carved mimo těch, které. Nebyly situacích, zejména s Azure Standard Storage, kde IOPS míra dosáhl kvóty jediný disk proti transakčního protokolu databázového systému. V takových situacích se doporučuje použít úložiště Premium Storage nebo případně agregování více standardního úložiště disků se softwaru RAID.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Osvědčené postupy z hlediska výkonu pro SQL Server v Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Konfigurace softwaru diskového pole RAID v systému Linux][virtual-machines-linux-configure-raid]
> * [Konfigurace LVM na virtuální počítač s Linuxem v Azure][virtual-machines-linux-configure-lvm]
> * [Azure tajných klíčů úložiště a vstupně-výstupních operací Linux optimalizace](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* Storage úrovně Premium se zobrazuje důležité lepší výkon, hlavně pro kritické transakce protokolu zápisy. Pro scénáře SAP, které budou poskytovat produkční, jako je třeba výkon důrazně doporučujeme používat VM-Series, které můžete využít Azure Premium Storage.

Mějte na paměti, disku, který obsahuje operační systém, a doporučujeme, binární soubory SAP a databáze (základní virtuální počítač), a není už omezený na 127GB. Velikost teď může mít až 1TB. To by měl být dostatek místa, aby všechny nezbytné souboru, včetně například SAP batch v protokolech úloh.

Další návrhy a další podrobnosti, konkrétně pro virtuální počítače databázového systému, přečtěte si [Průvodce nasazením databázového systému][dbms-guide]

#### <a name="disk-handling"></a>Zpracování disku
Ve většině scénářů musíte vytvořit další disk za účelem nasazení databázi SAP do virtuálního počítače. Už jsme mluvili o aspektech na počet disků v kapitole [virtuální počítač nebo disk strukturu pro nasazení SAP] [ planning-guide-5.5.1] tohoto dokumentu. Portál Azure umožňuje připojit a odpojit disky po základní virtuální počítač nasazen. Připojit/Odpojit po virtuálního počítače a spuštěná, i když je zastavena, může být disky. Při připojení disku, na portálu Azure nabízí připojte prázdný disk nebo stávající disk, který v tuto chvíli není připojen k jiným virtuálním Počítačem.

**Poznámka:**: disky lze připojit pouze pro jeden virtuální počítač v daném okamžiku.

![Připojit / Odpojit disky s Azure Standard Storage][planning-guide-figure-1400]

Během nasazení nového virtuálního počítače můžete rozhodnout, zda chcete spravovat disky nebo umístěte vaše disky na účtech úložiště Azure. Pokud chcete používat úložiště úrovně Premium, doporučujeme používat spravované disky.

Dále musíte rozhodnout, jestli chcete vytvořit nové a prázdný disk nebo jestli chcete vybrat existujícího disku, který se předtím nahrála a by měl být připojen virtuální počítač nyní.

**Důležité**: můžete **nesmí** pomocí ukládání do mezipaměti hostitele s Azure Standard Storage. Nechat předvoleb mezipaměti hostitele na výchozí hodnotu NONE. S Azure Premium Storage byste měli povolit ukládání do mezipaměti pro čtení vlastnosti vstupně-výstupních operací je většinou přečtení jako typický vstupně-výstupní provoz proti datových souborů databáze. Pokud se soubor protokolu transakcí databáze je doporučeno bez ukládání do mezipaměti.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Tom, jak připojit datový disk na portálu Azure][virtual-machines-linux-attach-disk-portal]
>
> Pokud jsou připojené disky, budete muset přihlásit k virtuálnímu počítači otevřete Správce disků systému Windows. Pokud automatického připojení není povolena podle doporučení v kapitole [nastavení automatického připojení pro připojené disky][planning-guide-5.5.3], je potřeba provést online a inicializované nově připojená svazku.
>
> ![Linux][Logo_Linux] Linux
>
> Pokud jsou připojené disky, budete muset přihlásit k virtuálnímu počítači a inicializace disky, jak je popsáno v [v tomto článku][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

- - -
Pokud je nový disk prázdný disk, budete muset formátovat disk také. Pro formátování, zejména pro soubory protokolu a data databázového systému platí stejná doporučení pro úplné nasazení databázového systému.

Jak již bylo uvedeno v kapitole [The koncept virtuálního počítače Microsoft Azure][planning-guide-3.2], účet úložiště Azure neposkytuje nekonečné prostředky z hlediska vstupně-výstupních operací svazek, svazek IOPS a data. Obvykle databázového systému virtuální počítače jsou nejvíce ovlivněno. Může to být nejvhodnější použít samostatný účet úložiště pro každý virtuální počítač, pokud máte několik velký objem vstupně-výstupní operace virtuálních počítačů k nasazení, aby bylo možné zůstat v rámci limitu svazku účet úložiště Azure. Jinak budete muset v tématu jak můžou vyrovnávat těchto virtuálních počítačů mezi různými účty úložiště bez nedosáhli limitu každý jeden účet úložiště. Další podrobnosti, které jsou popsané v [Průvodce nasazením databázového systému][dbms-guide]. Tato omezení měli také mít na paměti pro čistá aplikace SAP virtuálních počítačů serveru nebo ostatních virtuálních počítačů, které nakonec může vyžadovat další virtuální pevné disky. Tato omezení neplatí, pokud používáte spravované disku. Pokud plánujete používat úložiště úrovně Premium, doporučujeme používat spravované disku.

Jiné téma, které jsou důležité pro účty úložiště se, zda jsou virtuální pevné disky v účtu úložiště, získávání geograficky replikované. Geografická replikace povolená nebo zakázaná na úrovni účtu úložiště a ne na úrovni virtuálního počítače. Pokud je povoleno geografická replikace, virtuální pevné disky v rámci účtu úložiště by replikovat do jiného datového centra Azure v rámci stejné oblasti. Než se rozhodnete, že na tomto, musí si myslíte o následující omezení:

Azure geografická replikace nereplikuje IOs v chronologickém pořadí napříč více virtuálních pevných disků ve virtuálním počítači a funguje místně na každý virtuální pevný disk ve virtuálním počítači. Proto se replikují virtuální pevný disk, který představuje základní virtuální počítač a také všechny další virtuální pevné disky připojené k virtuálnímu počítači nezávisle na sobě navzájem. To znamená, že neexistuje žádná synchronizace mezi změny v různých virtuálních pevných disků. Fakt, že jsou replikovány IOs nezávisle na pořadí, ve kterém jsou zapsané znamená geografická replikace není hodnoty pro databázové servery, které mají své databáze distribuovány na více virtuálních pevných disků. Kromě databázového systému také mohou existovat další aplikace, které procesy zápisu nebo pracovat s daty v různých virtuálních pevných disků a kde je důležité udržovat pořadí změny. Pokud se požadavek, by nemělo být povoleno geografická replikace v Azure. Závisí na tom, jestli potřebujete nebo chcete geografická replikace pro sadu virtuálních počítačů, ale ne pro jinou sadu, můžete již zařadit virtuálních počítačů a jejich související virtuální pevné disky do jiné účty úložiště, které mají geografická replikace povolená nebo zakázaná.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Nastavení automatického připojení pro připojené disky
- - -
> ![Windows][Logo_Windows] Windows
>
> Pro virtuální počítače, které jsou vytvořené pomocí vlastní Image nebo disky je potřeba zkontrolovat a může být nastaven parametr automatického připojení. Nastavení tohoto parametru vám umožní virtuální počítač po restartování nebo opětovné nasazení v Azure automaticky znovu připojit připojené připojené jednotky.
> Parametr je nastaven pro Image poskytované Microsoft Azure Marketplace.
>
> Aby bylo možné nastavit automatického připojení, zkontrolujte v dokumentaci příkazového řádku spustitelného souboru diskpart.exe tady:
>
> * [Možnosti příkazového řádku DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automatického připojení](http://technet.microsoft.com/library/cc753703.aspx)
>
> Okno příkazového řádku systému Windows musí být otevřen jako správce.
>
> Pokud jsou připojené disky, budete muset přihlásit k virtuálnímu počítači otevřete Správce disků systému Windows. Pokud automatického připojení není povolena podle doporučení v kapitole [nastavení automatického připojení pro připojené disky][planning-guide-5.5.3], nově připojený svazek > je potřeba provést online a inicializované.
>
> ![Linux][Logo_Linux] Linux
>
> Je nutné inicializovat nově připojená prázdný disk, jak je popsáno v [v tomto článku][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Musíte taky přidat do /etc/fstab nové disky.
>
>

- - -
### <a name="final-deployment"></a>Poslední nasazení
Pro konečné nasazení a jaké kroky, zejména s ohledem na nasazení SAP rozšířené monitorování, naleznete [Průvodce nasazením][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Přístup k SAP systémy s operačním systémem v rámci virtuálních počítačů Azure
Pro scénáře jenom pro Cloud můžete chtít připojit k těmto systémům SAP přes grafické uživatelské rozhraní SAP veřejného Internetu. Pro tyto případy musí provést následující postupy.

Později v dokumentu se budeme zabývat další hlavní scénáře, připojují k systémům SAP v nasazeních mezi různými místy, které mají připojení site-to-site (tunelového připojení sítě VPN) nebo Azure ExpressRoute připojení mezi místní systémy a systémy Azure.

### <a name="remote-access-to-sap-systems"></a>Vzdálený přístup k SAP systémy
Pomocí Azure Resource Manageru neobsahuje žádné výchozí koncové body už jako bývalé klasického modelu. Všechny porty virtuálního počítače Azure ARM jsou otevřené, dokud:

1. Žádné skupiny zabezpečení sítě je definována pro podsíť nebo síťové rozhraní. Síťový provoz na virtuálních počítačích Azure můžou být zabezpečené pomocí takzvané "skupin zabezpečení sítě". Další informace najdete v části [co je skupina zabezpečení sítě (NSG)?][virtual-networks-nsg]
2. Pro síťové rozhraní, není definována žádná služba Vyrovnávání zatížení Azure   

Jak je popsáno v, najdete v části architektura rozdíl mezi klasického modelu a ARM [v tomto článku][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Konfigurace připojení k systému SAP a SAP grafického uživatelského rozhraní pro scénář jenom pro Cloud
Podrobnosti najdete v tomto článku, který popisuje podrobnosti k tomuto tématu: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Změna nastavení brány Firewall v rámci virtuálního počítače
Může být potřeba nakonfigurovat bránu firewall na virtuální počítače chcete povolit příchozí přenosy v systému SAP.

- - -
> ![Windows][Logo_Windows] Windows
>
> Ve výchozím nastavení je zapnutá brána Firewall systému Windows v rámci Azure nasazených virtuálních počítačů. Nyní je třeba povolit otevřít Port SAP, jinak nebude moct připojit SAP grafické uživatelské rozhraní.
> Použijte následující postup:
>
> * Otevřete ovládací panely\systém a zabezpečení\Brána Windows Firewall tak, aby **upřesňující nastavení**.
> * Nyní klikněte pravým tlačítkem na příchozí pravidla a zvolili **nové pravidlo**.
> * V následujícím Průvodci zvolili pro vytvoření nového **Port** pravidlo.
> * V dalším kroku průvodce nechte nastavení na TCP a zadejte číslo portu, který chcete otevřít. Vzhledem k tomu, že je naše ID instance SAP 00, vzali jsme 3200. Pokud je vaše instance číslo jinou instanci, musí být otevřen port, který jste definovali dříve podle počtu instancí.
> * V další části průvodce, je potřeba ponechat položku **povolit připojení** zaškrtnutí.
> * V dalším kroku průvodce budete muset definovat, zda se pravidlo vztahuje pro doménu, privátní a veřejné sítě. V případě potřeby pro vaše potřeby ho upravte. Ale připojují pomocí grafického uživatelského rozhraní SAP zvenku přes veřejnou síť, musíte mít pravidlo použije k veřejné síti.
> * V posledním kroku průvodce, název pravidla a uložte stisknutím **Dokončit**.
>
> Pravidlo začne okamžitě platit.
>
> ![Definice pravidla portu][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Linux obrázků v Azure Marketplace nepovolujte iptables brány firewall ve výchozím nastavení a připojení k systému SAP, by měly fungovat. Pokud jste povolili iptables nebo jiná brána firewall, naleznete v dokumentaci iptables nebo použité brány firewall umožňující tcp příchozí přenosy na portu 32xx (kde xx je počet systému vašeho systému SAP).
>
>

- - -
#### <a name="security-recommendations"></a>Doporučení zabezpečení
Grafické uživatelské rozhraní SAP nepřipojí k žádné z instance SAP (port 32xx), které jsou spuštěny, ale poprvé připojí přes port otevřít k procesu SAP zpráv serveru (port 36xx) okamžitě. V minulosti byl velmi stejný port používán serverem zpráva pro interní komunikaci na instancí aplikace. Aby se zabránilo místní aplikační servery nechtěně komunikaci se serverem zpráva v Azure lze změnit interní komunikační porty. Důrazně doporučujeme změnit interní komunikaci mezi zpráva serveru SAP a její instance aplikace na jiné číslo portu v systémech, které byly klonovat z místních systémů, jako je například klon vývoje pro projekt testování atd. To lze provést s výchozím parametrem profil:

> rdisp/msserv_internal
>
>

jak je uvedeno v [nastavení zabezpečení pro Server zpráv SAP ](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Koncepty čistě cloudové nasazení SAP instancí
### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Jeden virtuální počítač s SAP NetWeaver demo a školení scénář
![Systémem jednoho virtuálního počítače SAP ukázkový se stejnými názvy virtuálních počítačů, izolované ve službě Azure Cloud Services][planning-guide-figure-1700]

V tomto scénáři (naleznete v kapitole [jenom pro Cloud] [ planning-guide-2.1] tohoto dokumentu) jsme implementujete typické školení nebo ukázkový scénář systému kde obsaženy dokončení školení nebo ukázkový scénář v jeden virtuální počítač. Předpokládáme, že nasazení se provádí prostřednictvím image šablony virtuálních počítačů. Také předpokládáme, že více tyto ukázkové školení potřeba virtuální počítače nasadit s virtuálními počítači, který má stejný název.

Předpokladem je, který jste vytvořili Image virtuálního počítače, jak je popsáno v některých částech kapitoly [přípravy virtuálních počítačů s SAP pro Azure] [ planning-guide-5.2] v tomto dokumentu.

Posloupnost událostí implementovat scénář vypadá takto:

##### <a name="powershell"></a>PowerShell
* Vytvořit novou skupinu prostředků pro každých šířku školení nebo demo

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```
* Vytvořit nový účet úložiště, pokud nechcete používat spravované disky

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Vytvořte novou virtuální síť pro každý školení nebo ukázku šířku povolit využití stejný název hostitele a IP adres. Virtuální síť je chráněn skupinu zabezpečení sítě, který umožňuje pouze přenosy na portu 3389 povolit přístup ke vzdálené ploše a port 22 pro SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Vytvořit novou veřejnou IP adresu, která lze použít pro přístup k virtuálnímu počítači z Internetu

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Vytvoření nového síťového rozhraní pro virtuální počítač

```powershell
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Vytvoření virtuálního počítače. Pro scénář jenom pro Cloud bude mít každý virtuální počítač se stejným názvem. Identifikátor SID SAP instance SAP NetWeaver ve tyto virtuální počítače budou stejné také. V rámci skupiny prostředků Azure musí být jedinečný název virtuálního počítače, ale v různých skupinách prostředků Azure můžete spustit virtuální počítače se stejným názvem. Výchozí účet 'správce systému Windows nebo "kořenový" pro Linux nejsou platné. Nové uživatelské jméno správce proto musí být definován společně s heslem. Velikost virtuálního počítače taky musí být definován.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Volitelně můžete přidat další disky a potřeby obsah obnovit. Upozorňujeme, že názvy všech objektů blob (adres URL pro objekty BLOB), musí být jedinečný v rámci Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM

# Optional: Attach additional Managed Disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzureRmVM
```

##### <a name="cli"></a>Rozhraní příkazového řádku
Následující příklad kódu lze v systému Linux. Pro systém Windows, proveďte pomocí prostředí PowerShell, jak je popsáno výše nebo přizpůsobit příklad používat % rgName % místo $rgName a nastavte proměnnou prostředí pomocí příkazu Windows *nastavit*.

* Vytvořit novou skupinu prostředků pro každých šířku školení nebo demo

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Vytvoření nového účtu úložiště

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Vytvořte novou virtuální síť pro každý školení nebo ukázku šířku povolit využití stejný název hostitele a IP adres. Virtuální síť je chráněn skupinu zabezpečení sítě, který umožňuje pouze přenosy na portu 3389 povolit přístup ke vzdálené ploše a port 22 pro SSH.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Vytvořit novou veřejnou IP adresu, která lze použít pro přístup k virtuálnímu počítači z Internetu

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Vytvoření nového síťového rozhraní pro virtuální počítač

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Vytvoření virtuálního počítače. Pro scénář jenom pro Cloud bude mít každý virtuální počítač se stejným názvem. Identifikátor SID SAP instance SAP NetWeaver ve tyto virtuální počítače budou stejné také. V rámci skupiny prostředků Azure musí být jedinečný název virtuálního počítače, ale v různých skupinách prostředků Azure můžete spustit virtuální počítače se stejným názvem. Výchozí účet 'správce systému Windows nebo "kořenový" pro Linux nejsou platné. Nové uživatelské jméno správce proto musí být definován společně s heslem. Velikost virtuálního počítače taky musí být definován.

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Volitelně můžete přidat další disky a potřeby obsah obnovit. Upozorňujeme, že názvy všech objektů blob (adres URL pro objekty BLOB), musí být jedinečný v rámci Azure.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Šablona
Můžete vytvořit šablony ukázkových šablon azure rychlý start úložišti na githubu.

* [Jednoduchý virtuální počítač s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Jednoduchý virtuální počítač s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Virtuální počítač z bitové kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>Implementovat sadu virtuálních počítačů, které je třeba pro komunikaci v rámci Azure
Tento scénář jenom pro Cloud je typický scénář v nástroji školení a ukázkové účely kde software představující demo a školení scénář se šíří přes víc virtuálních počítačů. Různé komponenty nainstalované v různých virtuálních počítačů je třeba vzájemně komunikovat. Znovu v tomto scénáři nemáte žádnou místní síťová komunikace nebo mezi různými místy scénáři je potřeba.

Tento scénář je rozšíření Instalace popsané v kapitole [jeden virtuální počítač s SAP NetWeaver demo a školení scénář] [ planning-guide-7.1] tohoto dokumentu. V takovém případě víc virtuálních počítačů se zařadí do existující skupiny prostředků. V následujícím příkladu se skládá povahu školení SAP ASC nebo SCS virtuálního počítače, virtuální počítač spuštěn databázového systému a aplikačního serveru SAP instance virtuálních počítačů.

Než vytvoříte v tomto scénáři musíte vezměte v úvahu základní nastavení už se provést ve scénáři před.

#### <a name="resource-group-and-virtual-machine-naming"></a>Skupina prostředků a pojmenování virtuálního počítače
Všechny názvy skupin prostředků musí být jedinečný. Vývoj vlastní schéma pojmenování prostředků, jako například `<rg-name`>-příponu.

Název virtuálního počítače musí být jedinečný v rámci skupiny prostředků.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Nastavení sítě pro komunikaci mezi různé virtuální počítače
![Sadu virtuálních počítačů v rámci virtuální sítě Azure][planning-guide-figure-1900]

Abyste zabránili kolizím s klony ze stejné krajiny školení nebo ukázku, musíte vytvořit virtuální síť Azure pro každý na šířku. Překlad názvů DNS poskytovaný Azure nebo můžete nakonfigurovat vlastní DNS server mimo Azure (není možné dále zde popsané). V tomto scénáři jsme nekonfigurujte vlastní DNS. Pro všechny virtuální počítače uvnitř jednu virtuální síť Azure bude povolena komunikace přes názvy hostitelů.

Důvody k oddělení školení nebo ukázku krajiny tak, že virtuální sítě a ne jenom skupiny prostředků může být:

* Povahu SAP jako nastavení potřebuje vlastní AD OpenLDAP a musí být součástí jednotlivých krajina Server domény.  
* Na šířku SAP jako nastavení obsahuje součásti, které potřebujete k práci s pevné IP adresy.

Další informace o virtuálních sítí Azure a jak se definovat naleznete v [v tomto článku][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Nasazení SAP virtuálních počítačů s připojením k podnikové síti (mezi různými místy)
Spustit SAP šířku a chcete rozdělit nasazení mezi úplné obnovení vyšší kategorie servery databázového systému, místní virtualizované prostředí pro aplikaci vrstvy a menší vrstvě 2 nakonfigurovali SAP systémy a Azure IaaS. Základní předpokladem je, že je potřeba SAP systémy v rámci jednoho šířku SAP komunikaci mezi sebou a s mnoha další softwarové součásti nasazení ve společnosti, nezávisle na jejich nasazení formuláře. Taky by měl existovat žádné rozdíly zaváděné formuláře nasazení pro koncového uživatele propojení s grafickým uživatelským rozhraním SAP nebo dalších rozhraní. Tyto podmínky lze splnit, pouze když máme OpenLDAP Active Directory v místě a rozšířit do Azure systémů prostřednictvím připojení site na lokality nebo více-místě nebo privátní připojení, jako jsou Azure ExpressRoute služby DNS.

Chcete-li získat další pozadí na podrobnosti implementace SAP v Azure, doporučujeme přečíst kapitoly [koncepty Cloud-Only nasazení SAP instancí] [ planning-guide-7] tohoto dokumentu, který vysvětluje některé konstrukce základy Azure a jak tyto musí být použit s SAP aplikací v Azure.

### <a name="scenario-of-an-sap-landscape"></a>Scénář šířku SAP
Mezi různými místy scénář lze zhruba popsat jako grafického níže:

![Připojení Site-to-Site mezi místními a prostředky Azure][planning-guide-figure-2100]

Scénáře uvedené výše popisuje scénář, kde místní AD nebo OpenLDAP a DNS jsou rozšířené do Azure. Na straně místně je vyhrazený určitý rozsah IP adres podle předplatného Azure. Rozsah IP adres se přiřadí k virtuální síti Azure na straně Azure.

#### <a name="security-considerations"></a>Aspekty zabezpečení
Minimální požadavek je použití zabezpečené komunikace protokoly, jako je například protokol SSL/TLS pro přístup z prohlížeče nebo připojení VPN pro systém přístup ke službám Azure. Předpokladem je, že společností velmi jinak zpracovat připojení VPN mezi jejich podnikovou sítí a Azure. Některé společnosti mohou otevřít blankly všechny porty. Některé jiné společnosti, chcete mít velmi přesné v porty, které potřebují k otevírání, atd.

V následující tabulce typické SAP jsou uvedeny komunikační porty. V podstatě je pro otevření portu brány SAP dostatečná.

| Služba | Název portu | Příklad `<nn`> = 01 | Výchozí rozsah (min-max) | Poznámka |
| --- | --- | --- | --- | --- |
| Dispečer |sapdp`<nn>` najdete v části * |3201 |3200 - 3299 |Dispečer SAP, používá SAP grafického uživatelského rozhraní pro systém Windows a Java |
| Server zpráv |sapms`<sid`> najdete v části ** |3600 |volné sapms`<anySID`> |identifikátor SID = ID systému SAP |
| brána |sapgw`<nn`> najdete v části * |3301 |Volné |Bráně SAP, používá pro komunikaci CPIC a RFC |
| Směrovač SAP |sapdp99 |3299 |Volné |Pouze názvy služby CI (centrální instance) můžete přiřadit v /etc/services na libovolnou hodnotu po instalaci. |

*) nn = čísla Instance SAP

*) sid = ID systému SAP

Podrobnější informace o porty vyžadované pro různé SAP produkty nebo služby, na základě produktů SAP naleznete zde <http://scn.sap.com/docs/DOC-17124>.
Tento dokument je třeba v zařízení VPN, která je potřebná pro konkrétní produkty SAP a scénáře otevřít vyhrazené porty.

Další bezpečnostní opatření při nasazení virtuálních počítačů v takovém scénáři může být k vytvoření [skupinu zabezpečení sítě] [ virtual-networks-nsg] k definování pravidel přístupu.

### <a name="dealing-with-different-virtual-machine-series"></a>Práci s jinou sérii virtuálního počítače
V průběhu posledních 12 měsíců Microsoft přidat mnoho další typy virtuálních počítačů, které se liší buď v počet Vcpu, paměti nebo důležitější na hardwaru, je spuštěn na. Ne všechny tyto virtuální počítače jsou podporovány v systému SAP (viz podporované typy virtuálních počítačů v Poznámka SAP [1928533]). Některé z těchto virtuálních počítačů na jiný hostitelský hardware generace spustit. V členitost z Azure – jednotka škálování získávání nasazených generace hardwaru tyto hostitele. Mohou nastat znamená případy, kdy nelze spustit, jiné velikosti virtuálních počítačů, které jste zvolili na stejné jednotky škálování. Skupiny dostupnosti je omezené možnosti span jednotek škálování závislosti na jiný hardware.  Například pokud chcete spustit databázového systému na virtuální počítače A5 A11 a aplikační vrstvu SAP na virtuálních počítačích G-Series, můžete by se vynutilo nasazení jednoho systému SAP nebo jiné systémy SAP v rámci jiné skupiny dostupnosti.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Tisk na místní síťové tiskárny z instance SAP v Azure
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Tisk přes TCP/IP v napříč místním a externím
Nastavení místní síťové tiskárny TCP/IP na základě virtuální počítač Azure se celkové stejné jako v podnikové síti, za předpokladu, že máte tunelové propojení VPN typu Site-To-Site nebo připojením ExpressRoute vytvořeno.

- - -
> ![Windows][Logo_Windows] Windows
>
> Použijte následující postup:
>
> * Některé síťové tiskárny jsou součástí konfigurace průvodce, který usnadňuje nastavení tiskárny virtuální počítač Azure. Pokud žádný takový software Průvodce byla distribuována s tiskárnou, chcete-li vytvořit nový port tiskárny TCP/IP je manuální způsob, jak nastavit tiskárny.
> * Otevřete ovládací panely -> zařízení a tiskárny -> Přidat tiskárnu
> * Zvolte možnost Přidat tiskárnu pomocí adresy protokolu TCP/IP nebo název hostitele
> * Zadejte IP adresu tiskárny
> * Port tiskárny standardní 9100
> * V případě potřeby nainstalujte příslušný ovladač tiskárny ručně.
>
> ![Linux][Logo_Linux] Linux
>
> * pro Windows jenom postupujte jako standardní postup instalace síťové tiskárny
> * postupujte podle veřejné Linux příručky pro [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) nebo [Red Hat a Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) o tom, jak přidat tiskárnu.
>
>

- - -
![Tisk v síti][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Na hostiteli tiskárnu přes protokol SMB (sdílené tiskárny) mezi různými místy scénář
Tiskárny na hostiteli nejsou kompatibilní sítě záměrné. Ale tiskárnu založené na hostiteli může být sdílen počítače v síti, dokud je tiskárna připojených k počítači používá technologii na. Připojení k vaší podnikové sítě Site-To-Site nebo ExpressRoute a sdílet místní tiskárny. Protokol SMB pro rozhraní NetBIOS místo DNS používá jako název služby. Název hostitele rozhraní NetBIOS se může lišit od název hostitele DNS. Standardní případem je, že název hostitele rozhraní NetBIOS a název hostitele DNS jsou identické. Doména DNS nemá smysl v oboru názvů pro rozhraní NetBIOS. Podle toho název hostitele DNS a doména DNS plně kvalifikovaný název hostitele DNS se nesmí používat v oboru názvů pro rozhraní NetBIOS.

Sdílené tiskárny je identifikována jedinečný název v síti:

* Název hostitele protokolu SMB (vždycky potřeboval).
* Název sdílené složky (vždycky potřeboval).
* Název domény, pokud sdílená složka tiskárny není ve stejné doméně jako systém SAP.
* Kromě toho uživatelské jméno a heslo může být nutné přístup do sdílené složky tiskárny.

Postup:

- - -
> ![Windows][Logo_Windows] Windows
>
> Sdílet místní tiskárny.
> Ve virtuálním počítači Azure otevřete Průzkumníka Windows a zadejte název sdílené složky tiskárny.
> Průvodce instalací tiskárny vás provede procesem instalace.
>
> ![Linux][Logo_Linux] Linux
>
> Zde jsou některé příklady dokumentaci o konfiguraci síťové tiskárny v systému Linux nebo včetně kapitola ohledně tisku v systému Linux. Bude možné používat stejným způsobem jako virtuální počítač Azure Linux tak dlouho, dokud virtuální počítač je součástí sítě VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL nebo Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>Tiskárny USB (přesměrování tiskárny)
V Azure schopnost služby Vzdálená plocha uživatelům poskytnout přístup ke svým zařízením místní tiskárnu ve vzdálené relaci není k dispozici.

- - -
> ![Windows][Logo_Windows] Windows
>
> Další informace o tisk se systémem Windows naleznete zde: <http://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integrace SAP Azure systémů do oprava a systému pro přenos (TMS) v mezi různými místy
SAP změn a přenosu systému (TMS) musí být nakonfigurován pro export a import žádost o přenos mezi systémy v povahu. Předpokládáme, že instance vývoj systému SAP (vývoj) nacházejí v Azure, zatímco kvality zajištění kvality a produktivitu systémy (PRD) jsou místní. Kromě toho předpokládáme, že existuje adresář centrální přenosu.

##### <a name="configuring-the-transport-domain"></a>Konfigurace domény přenosu
Nakonfigurujte doménu přenosu systému, který jste určili jako řadič domény přenosu, jak je popsáno v [konfigurací řadiče domény přenos](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Systémový uživatel, bude vytvořen TMSADM a požadovaný cíl RFC se budou generovat. Tato připojení RFC pomocí transakce SM59 může zkontrolovat. Rozlišení názvů hostitelů musí být povolena napříč doménu přenosu.

Postup:

* V tomto scénáři jsme se rozhodli, že v místním systému QAS bude CTS řadič domény. Volání transakce moduly STM. Zobrazí se dialogové okno TMS. Zobrazí se dialogové okno Konfigurace domény přenosu. (Toto dialogové okno se zobrazí pouze pokud jste ještě nenakonfigurovali přenosu domény.)
* Ujistěte se, že se oprávnění automaticky vytvořený uživatel TMSADM (SM59 -> ABAP připojení -> TMSADM@E61.DOMAIN_E61 -> Podrobnosti o -> Utilities(M) -> autorizace Test). Úvodní obrazovka transakce moduly STM by měl zobrazit, že tento systém SAP teď funguje jako řadič domény přenosu jak je vidět tady:

![Úvodní obrazovka transakce moduly STM na řadiči domény][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Včetně SAP systémy v doméně přenosu
Pořadí včetně systému SAP v doméně přenosu vypadá takto:

* V systému Vývojářů v Azure přejděte do systému pro přenos (klient 000) a volání transakce moduly STM. Vyberte další konfiguraci z dialogového okna a pokračujte zahrnují systému v doméně. Zadejte řadič domény jako cílový hostitel ([včetně SAP systémy v doméně přenosu](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Systém čeká nyní mají být zahrnuty v doméně přenosu.
* Z bezpečnostních důvodů budete muset poté přejděte zpět na řadiči domény, aby žádost potvrdit. Zvolte Přehled systému a schválit čekání systému. Potvrďte, řádku a konfigurace budou distribuována.

Tento systém SAP nyní obsahuje nezbytné informace o všechny ostatní systémy SAP v doméně přenosu. Ve stejnou dobu data adresu nového systému SAP se odesílají na všechny ostatní systémy SAP a je v systému SAP zadali v profilu přenosu programu řízení přenosu. Zkontrolujte, zda dokumenty RFC a přístup k adresáři přenosu domény fungovat.

Pokračovat v konfiguraci vašeho systému přenosu obvyklým, jak je popsáno v dokumentaci k [změn a systému pro přenos](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Postup:

* Ujistěte se, zda že je správně nakonfigurována vaše moduly STM místně.
* Ujistěte se, že název hostitele řadiče domény, přenos se dají vyřešit virtuálního počítače na karet visa Azure a naopak.
* Volání transakce moduly STM -> Další konfigurace -> zahrnují systému v doméně.
* Zkontrolujte připojení v na místním TMS systému.
* Nakonfigurujte směrování přenosu, skupiny a vrstvy jako obvykle.

V připojených site-to-site mezi různými místy scénáře, latenci mezi místními a Azure stále může být výrazně. Pokud jsme postupujte podle pořadí převod objekty prostřednictvím vývoj a testování systémů do produkčního prostředí nebo myslíte o použití přenosy nebo podpůrných balíčků na jiné systémy, zjistíte, že, závisí na umístění adresáře centrální přenosu, některé z systémů se setkají vysokou latencí čtení nebo zápis dat v adresáři centrální přenosu. Situaci je podobná SAP šířku konfigurace, kde se šíří různými systémy prostřednictvím různých datových střediscích s dlouhou vzdálenost mezi datovými centry.

Chcete-li vyřešit tyto latence a mají systémy fungovat Rychlé čtení nebo zápisu do nebo z adresáře přenosu, můžete nastavit dvěma doménami přenosu moduly STM (jeden pro místní a jeden systémech v Azure a propojit domén přenosu. Zkontrolujte prosím tuto dokumentaci, která vysvětluje principy za tento koncept v SAP TMS: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Postup:

* Nastavení domény přenosu v každém umístění (místní a Azure) pomocí transakce moduly STM <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Odkaz domén s odkazem domény a potvrďte propojení mezi těmito dvěma doménami.
  <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuují konfiguraci propojené systému.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC provoz mezi instancemi SAP, které jsou umístěné v Azure a místními (mezi různými místy)
RFC provoz mezi systémy, které jsou místně a v Azure potřebuje pracovat. Nastavit připojení transakci volání SM59 ve zdrojovém systému potřebujete-li definovat připojení k RFC směrem do cílového systému. Konfigurace je podobná standardní nastavení připojení k RFC.

Předpokládáme, že ve scénáři, mezi různými místy, virtuální počítače, které spuštění SAP systémy, které potřebují ke komunikaci mezi sebou jsou ve stejné doméně. Nastavení připojení k RFC mezi systémy SAP proto neliší od kroky instalace a vstupy ve scénářích místně.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Přístupu k místním fileshares z instance SAP umístěná v Azure nebo naopak
Instance SAP umístěný v Azure je nutné přístup ke sdíleným složkám, které jsou v rámci podnikové místní. Kromě toho místní instance SAP muset přístup ke sdíleným složkám, které jsou umístěny v Azure. Chcete-li povolit sdílené složky, musíte nakonfigurovat oprávnění a sdílení možnosti v místním systému. Ujistěte se, že otevřete porty pro připojení VPN nebo ExpressRoute mezi Azure a vaším datacentrem.

## <a name="supportability"></a>Možnosti podpory
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure monitorování řešení pro SAP
Chcete-li povolit monitorování zvláště důležité systémy SAP v Azure SAP monitorování nástroje SAPOSCOL nebo Agent hostitele SAP získat data z hostitele virtuálního počítače služby Azure prostřednictvím rozšíření monitorování Azure pro SAP. Vzhledem k tomu, že požadavky pomocí SAP měla velmi specifické pro aplikace SAP, Microsoft rozhodla obecně implementovat požadovanou funkčnost do Azure, ale ponechte zákazníci nasazení komponenty potřebné monitorování a konfigurací pro své virtuální počítače spuštěné v Azure. Nasazení a životního cyklu správy monitorování součástí však bude možné většinou automatizovat, Azure.

#### <a name="solution-design"></a>Návrh řešení
Řešení vyvinuté tak, aby povolit SAP monitorování je založen na architektuře agenta virtuálního počítače Azure a rámce pro rozšíření. Rada agenta virtuálního počítače Azure a rozšíření rámce je povolit instalaci softwaru aplikace k dispozici v galerii rozšíření virtuálního počítače Azure v rámci virtuálního počítače. Princip myšlenkou tento koncept je umožnit (v takových případech rozšíření monitorování Azure pro SAP), nasazení speciální funkce do virtuálního počítače a konfigurace takový software v době nasazení.

'Azure agenta virtuálního počítače, umožňující zpracování konkrétní rozšíření virtuálního počítače Azure v rámci virtuálního počítače je vsunout do virtuálních počítačů Windows ve výchozím nastavení při vytváření virtuálního počítače na portálu Azure. V případě SUSE, Red Hat nebo Oracle Linux agent virtuálního počítače je již součástí bitové kopie Azure Marketplace. V případě, že jeden by nahrát virtuální počítač s Linuxem z místního do Azure agenta virtuálního počítače musí být nainstalován ručně.

Základní stavební bloky řešení monitorování v Azure pro SAP vypadá jako tento:

![Součástí rozšíření Microsoft Azure][planning-guide-figure-2400]

Jak je vidět ve výše uvedeném diagramu bloku, je jednou ze součástí sady řešení monitorování pro SAP hostitelem Image virtuálního počítače Azure a Azure Galerie rozšíření, která je globálně replikované úložiště, který je spravován nástrojem Azure Operations. Je zodpovědností týmem společné SAP/MS pracující na Azure implementace SAP pro práci s Azure operace publikování nové verze rozšíření monitorování Azure pro SAP.

Když nasadíte nový virtuální počítač s Windows, Agent virtuálního počítače Azure se automaticky přidá do virtuálního počítače. Funkce tohoto agenta je ke koordinaci načítání a konfiguraci rozšíření Azure pro sledování systému SAP NetWeaver. Pro virtuální počítače s Linuxem agenta virtuálního počítače Azure je již součástí bitové kopie operačního systému Azure Marketplace.

Je však stále je třeba provést zákazník krok. Jedná se o povolování a konfiguraci kolekce výkonu. Proces souvisejí s konfigurací je automatizováno pomocí skriptu prostředí PowerShell nebo rozhraní příkazového řádku příkaz. Skript prostředí PowerShell můžete stáhnout na webu Microsoft Script Center Azure, jak je popsáno v [Průvodce nasazením][deployment-guide].

Přehled architektury řešení Azure monitorování pro SAP vypadá takto:

![Řešení monitorování pro SAP NetWeaver Azure][planning-guide-figure-2500]

**Přesný postup a podrobný postup pomocí těchto rutin prostředí PowerShell nebo rozhraní příkazového řádku příkaz během nasazení, postupujte podle pokynů v [Průvodce nasazením][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integrace Azure nachází instance SAP do SAProuter
Instance SAP běžící v Azure musí být také dostupný z SAProuter.

![Připojení k síti SAP-směrovač][planning-guide-figure-2600]

SAProuter umožňuje komunikaci TCP/IP mezi zúčastněné systémy, pokud neexistuje žádné přímé připojení IP. To poskytuje výhodu, že žádné připojení klient server mezi partnery komunikace je nutné na úrovni sítě. SAProuter naslouchá na portu 3299 ve výchozím nastavení.
Chcete-li připojení instance SAP přes SAProuter dejte název SAProuter řetězec a hostitele s pokusy o připojení.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java
Dosavadní fokus dokument byl SAP NetWeaver obecně nebo SAP NetWeaver ABAP zásobníku. V této části malé jsou uvedeny konkrétní důležité informace týkající se zásobníku SAP Java. Jedním z nejdůležitějších aplikací Java SAP NetWeaver výhradně na základě je SAP podnikovém portálu. Další SAP NetWeaver na základě aplikací, jako SAP platformy a správce řešení SAP použít SAP NetWeaver ABAP i zásobníky Java. Proto určitě je potřeba vzít v úvahu zvláštní aspekty související se zásobníkem SAP NetWeaver Java také.

### <a name="sap-enterprise-portal"></a>SAP podnikového portálu
Instalační program portálu SAP v virtuální počítač Azure se neliší od na místní instalace, pokud nasazujete ve scénářích mezi různými místy. Vzhledem k tomu, že DNS se provádí na místě, nastavení portu jednotlivých instancí lze provést jako nakonfigurované na místě. Doporučení a omezení popsaná v tomto dokumentu, pokud platí pro aplikaci jako SAP podnikový portál nebo zásobníku SAP NetWeaver Java obecně.

![Portál zveřejněné SAP][planning-guide-figure-2700]

Scénář speciální nasazení podle někteří zákazníci je přímé expozice podnikový portál SAP k Internetu, zatímco hostitel virtuálního počítače je připojený k podnikové síti prostřednictvím tunelu site-to-site VPN nebo ExpressRoute. Pro tento případ budete muset Ujistěte se, že určité porty jsou otevřené a není blokován bránou firewall nebo skupiny zabezpečení. Stejné mechanismy by musela být použita, pokud se chcete připojit k instanci SAP Java z místního ve scénáři jenom pro Cloud.

Počáteční portál identifikátor URI je http (s):`<Portalserver`>: 5XX00/irj, kde je port tvořen 50000 plus (Systemnumber?? 100). Výchozí portálu URI SAP systém 00 `<dns name`>.`<azure region` >.Cloudapp.azure.com:PublicPort/irj. Další informace, podívejte se na <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Konfigurace koncového bodu][planning-guide-figure-2800]

Pokud chcete upravit adresu URL nebo porty SAP podnikového portálu, zkontrolujte Tato dokumentace:

* [Změnit adresu URL portálu](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Změňte výchozí čísla portů, čísla portů portálu](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Vysoké dostupnosti (HA) a obnovení po havárii (DR) pro SAP NetWeaver spuštěna ve virtuálních počítačích Azure
### <a name="definition-of-terminologies"></a>Definice terminologie jsou
Termín **vysokou dostupnost (HA)** obvykle souvisí s sada technologií, které minimalizují přerušení IT díky kontinuity podnikových procesů IT služeb prostřednictvím redundantní, odolný proti chybám nebo převzetí služeb při selhání chráněné komponenty uvnitř **stejné** datového centra. V našem případě v rámci jedné oblasti Azure.

**Zotavení po havárii (DR)** také cílí minimalizovat narušení služeb IT a jejich obnovení ale napříč **různých** datových center, které jsou obvykle umístěné stovky kilometrů rychle. V našem případě obvykle mezi různých oblastech Azure v rámci stejné geopolitické oblasti nebo jako zavedených vy jako zákazník.

### <a name="overview-of-high-availability"></a>Přehled vysokou dostupnost
Jsme můžete oddělit diskuzi o SAP vysoké dostupnosti v Azure do dvou částí:

* **Vysoká dostupnost infrastruktury Azure**, například HA výpočetní (VM), sítě, úložiště atd. a jeho výhody pro zvýšení dostupnosti aplikací SAP.
* **Vysoká dostupnost aplikace SAP**, například HA systému SAP softwarové součásti:
  * SAP aplikační servery
  * Instance SAP ASC nebo SCS
  * Databázového serveru

a jak je možné kombinovat s infrastrukturu Azure HA.

SAP vysoké dostupnosti v Azure má některé rozdíly ve srovnání s SAP vysoké dostupnosti v místním fyzickém nebo virtuálním prostředí. Následující dokument z SAP popisuje standardní konfigurace s vysokou dostupností SAP ve virtualizovaném prostředí v systému Windows: <http://scn.sap.com/docs/DOC-44415>. Neexistuje žádné integrované sapinst SAP-HA konfigurace pro Linux jako existuje pro systém Windows. O SAP HA místní pro Linux najít další informace: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Vysoká dostupnost infrastruktury Azure
Není aktuálně single-VM SLA 99,9 %. Chcete-li získat představu, jak dostupnost jeden virtuální počítač může vypadat jako můžete jednoduše vytvořit produkt různé dostupné Azure SLA: <https://azure.microsoft.com/support/legal/sla/>.

Základ pro výpočet je 30 dní, měsíčně nebo 43 200 minut. Proto hodnotu 0,05 % výpadek odpovídá 21,6 minut. Dostupnost různé služby bude obvyklým způsobem, vynásobte následujícím způsobem:

(Služba dostupnosti č. 1/100) * (služba dostupnosti č. 2/100) * (služba dostupnosti č. 3/100) 

Například:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 nebo celkové dostupnosti 99.75 %.

#### <a name="virtual-machine-vm-high-availability"></a>Vysoká dostupnost virtuálního počítače (VM)
Existují dva typy událostí platformy Azure, které můžou ovlivnit dostupnost vašich virtuálních počítačů: plánované údržby a neplánovaná Údržba.

* Události plánované údržby jsou pravidelné aktualizace provedené microsoftem základní platformu Azure ke zlepšení celkové spolehlivosti, výkonu a zabezpečení infrastruktury platformy, které virtuální počítače spustit na.
* Neplánovaná Údržba události dojít, když hardware nebo fyzické infrastruktuře základní virtuálního počítače došlo k chybě nějakým způsobem. To může zahrnovat selhání místní sítě, selhání místního disku nebo další selhání na úrovni racku. Když se taková selhání detekuje, platformu Azure automaticky migrovat virtuální počítač z není v pořádku fyzický server, který je hostitelem virtuálního počítače v pořádku fyzický server. Takové události se vyskytují jen vzácně, ale také můžou způsobit restartování vašeho virtuálního počítače.

Další podrobnosti naleznete v této dokumentaci: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redundance úložiště Azure
Data ve vašem účtu úložiště Microsoft Azure se vždycky replikují, aby zajistila stálost a vysoká dostupnost, splňuje smlouvy SLA pro úložiště Azure i při krátkodobém výpadku hardwaru.

Vzhledem k tomu, že Azure Storage je ve výchozím nastavení uchovávání tři bitové kopie dat, RAID5 nebo RAID1 na více disků Azure nejsou potřebné.

Další podrobnosti najdete v tomto článku: <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Využívá infrastrukturu Azure restartování virtuálního počítače lze dosáhnout vyšší dostupnosti aplikace SAP
Pokud se rozhodnete nepoužívat funkce jako je Windows Server Failover Clustering (WSFC) nebo kardiostimulátor v systému Linux (momentálně podporována pouze pro SLES 12 a vyšší), restartovat virtuální počítač Azure je použít k ochraně SAP systém proti plánovaných a neplánovaných výpadků infrastruktury Azure fyzického serveru a celkové základní platformy Azure.

> [!NOTE]
> Je důležité zmínit, že restartování virtuálního počítače Azure především chrání virtuální počítače a ne aplikace. Virtuální počítač restartovat nenabízí vysokou dostupnost pro aplikace SAP, ale nabízí systém určitá úroveň dostupnost infrastruktury a proto nepřímo vyšší dostupnosti systémů SAP. Je zde také žádné SLA pro dobu, kterou bude trvat restartovat virtuální počítač po plánovaném nebo neplánovaném hostitele výpadku. Tato metoda vysoké dostupnosti proto není vhodný pro důležité součásti systému SAP jako (A) SCS nebo databázového systému.
>
>

Jiný element důležité infrastruktury pro zajištění vysoké dostupnosti je úložiště. Smlouvy SLA pro úložiště Azure je třeba 99,9 % dostupnost. Pokud nasadí do jeden účet Azure Storage, potenciální Azure Storage nedostupnosti způsobí, že nejsou dostupné všechny virtuální počítače, které jsou umístěny v daném účtu úložiště Azure a spuštěna v rámci těchto virtuálních počítačů také všechny součásti SAP všech virtuálních počítačů s jeho disky.  

Místo uvedení všechny virtuální počítače do jedné jeden účet úložiště Azure, můžete použít také vyhrazeného úložiště účtů pro každý virtuální počítač a tímto způsobem zvýšit celkový dostupnosti virtuálních počítačů a SAP aplikace s použitím více nezávislých účtů úložiště Azure.

Disky systému Azure spravované jsou automaticky umístěny v doméně selhání virtuálního počítače, které jsou připojené k. Zadáte-li dva virtuální počítače ve skupině dostupnosti nastavit a spravovat disky používat, platformu se postará o distribuci disky spravované do různých domén selhání také. Pokud plánujete používat úložiště úrovně Premium, důrazně doporučujeme také pomocí správy disků.

Ukázková architektura systému SAP NetWeaver, který používá infrastrukturu Azure HA a úložiště účtů může vypadat například takto:

![Využívá infrastrukturu Azure HA lze dosáhnout vyšší dostupnosti aplikace SAP][planning-guide-figure-2900]

Ukázková architektura systému SAP NetWeaver používá infrastrukturu Azure HA a spravované disků může vypadat například takto:

![Využívá infrastrukturu Azure HA lze dosáhnout vyšší dostupnosti aplikace SAP][planning-guide-figure-2901]

Pro důležité součásti SAP jsme dosáhnout následující, pokud:

* Vysoká dostupnost SAP aplikační servery (AS)

  Instance serveru SAP aplikace jsou redundantní komponenty. Každý SAP jako instance je nasazen na svůj vlastní virtuální počítač, který běží v jiné Azure selhání a upgradu domény (najdete v kapitolách [domén selhání] [ planning-guide-3.2.1] a [upgradu domény][planning-guide-3.2.2]). To je zajištěno pomocí sad dostupnosti Azure (naleznete v kapitole [skupiny dostupnosti Azure][planning-guide-3.2.3]). Potenciální nedostupnosti plánovaném nebo neplánovaném selhání Azure nebo doména Upgrade způsobí nedostupnosti omezenému počtu virtuálních počítačů s jejich SAP AS instancí.

  Každý SAP jako instance je umístěn v svůj vlastní účet služby Azure Storage – potenciální nedostupnosti jeden účet úložiště Azure způsobí, že nejsou dostupné jenom jeden virtuální počítač s jeho SAP AS instance. Však Upozorňujeme, že je maximální počet účtů úložiště Azure v rámci jednoho předplatného Azure. Aby se zajistilo automatické spuštění (A) instance SCS po restartování virtuálního počítače, ujistěte se, že chcete nastavit parametr automatické spuštění v instanci (A) SCS spustit profil popsané v kapitole [pomocí automatické spuštění pro instance SAP][planning-guide-11.5].
  Přečtěte si také kapitoly [vysoká dostupnost pro SAP aplikační servery] [ planning-guide-11.4.1] další podrobnosti.

  I když používáte spravované disky, tyto disky jsou také uloženy v účtu úložiště Azure a může být k dispozici v případě výpadku úložiště.

* *Vyšší* instance SCS dostupnosti SAP (A)

  Zde jsme využívat Azure restartujte počítač k ochraně virtuálního počítače s nainstalovanou instanci SCS SAP (A). V případě plánované, nebo neplánované výpadky Azure servery, virtuální počítače se restartuje na jiném serveru k dispozici. Jak už bylo zmíněno dříve, restartovat virtuální počítač Azure především chrání virtuální počítače a ne aplikace, u této instance SCS případ (A). Prostřednictvím restartování virtuálního počítače je budete spojit nepřímo vyšší dostupnosti instance SCS SAP (A). K zajištění automatického spuštění instance (A) SCS po restartování virtuálního počítače, nezapomeňte nastavit automatické spuštění parametr v profilu spuštění instance (A) SCS popsané v kapitole [pomocí automatické spuštění pro instance SAP][planning-guide-11.5]. To znamená SCS (A) instance jako jeden bod z selhání (SPOF) spuštěné v jeden virtuální počítač bude určujícího faktor pro dostupnost celou šířku SAP.

* *Vyšší* dostupnost serveru databázového systému

  Zde podobný případ použití instance SCS SAP (A), můžeme využívat Azure restartujte počítač k ochraně virtuálního počítače s nainstalovaným softwarem databázového systému a jsme dosáhnout vyšší dostupnosti softwaru databázového systému prostřednictvím restartovat počítač.
  Databázového systému spuštěné v jeden virtuální počítač je také SPOF a je určujícího faktor pro dostupnost celou šířku SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>SAP vysoké dostupnosti aplikace na Azure IaaS
K dosažení úplné SAP systém vysoké dostupnosti, je potřeba chránit všechny důležité součásti systému SAP, příklad redundantní SAP aplikační servery a jedinečný součásti (například jediné místo poruchy) jako instance SCS SAP (A) a databázového systému.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Vysoká dostupnost pro SAP aplikační servery
Pro servery nebo dialogové okno instance aplikace SAP není potřeba myslet řešení konkrétní vysokou dostupnost. Redundance a tím jednoduše dosáhnout vysoké dostupnosti s dostatek z nich v různých virtuálních počítačů. Se musí všechny umístit do stejné Azure skupina dostupnosti předejdete, virtuální počítače mohou být aktualizovány v průběhu během plánované údržby. výpadků. Základní funkce, která je založena na jiné upgradu a domén selhání v rámci jednotky škálování Azure již byla zavedena v kapitole [upgradu domény][planning-guide-3.2.2]. Azure skupiny dostupnosti prezentovaly v kapitole [skupiny dostupnosti Azure] [ planning-guide-3.2.3] tohoto dokumentu.

Neexistuje žádné nekonečné počet selhání a upgradu domén, které je možné podle skupiny dostupnosti Azure, v rámci jednotky škálování služby Azure. To znamená, že uvedení počet virtuálních počítačů do jedné skupiny dostupnosti, dřív nebo později více než jeden virtuální počítač bude mít ve stejném selhání nebo upgradu domény.

Nasazení několik instancí SAP aplikačních serverů v jejich vyhrazených virtuálních počítačích a za předpokladu, že My pět upgradu domény, na konci ukáže na následujícím obrázku. Maximální skutečný počet domén selhání a aktualizace v rámci skupiny dostupnosti může v budoucnu změnit:

![HA SAP aplikačních serverů v Azure][planning-guide-figure-3000]

Další podrobnosti naleznete v této dokumentaci: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>Vysoká dostupnost pro instance SCS SAP (A) v systému Windows
Windows Server Failover Cluster (WSFC) se často používané řešení ochrany instance SCS SAP (A). Také je integrovaný do sapinst v podobě "HA instalace". V tuto chvíli není schopen poskytnout funkce pro nastavení požadovaná clusteru převzetí služeb při selhání systému Windows Server stejným způsobem jako dokončení místní infrastruktury Azure.

Od ledna 2016 platformou Azure cloud s operačním systémem Windows nenabízí možnost použití sdíleného svazku clusteru na disku sdílena mezi dva virtuální počítače Azure.

Platným řešením, když je využití softwaru 3. stran, které poskytuje sdíleného svazku pomocí replikace synchronní a transparentní disku, který lze integrovat do služby WSFC. Tento přístup vyplývá, že pouze aktivním uzlu clusteru je moci jednu kopii disku v bodě v čase. Konfigurace je od ledna 2016 tento HA podporována k ochraně instanci SCS SAP (A) na Windows hostovaný operační systém na virtuálních počítačích Azure v kombinaci s 3rd softwarem s DataKeeper.

Řešení s DataKeeper poskytuje prostředek sdíleného disku clusteru na clustery převzetí služeb při selhání systému Windows tak, že:

* Další Azure virtuální pevný disk připojený ke každému z virtuálních počítačů (VM), které jsou v konfiguraci clusteru se systémem Windows
* Spuštěná na obou uzlech virtuálních počítačů s DataKeeper Cluster Edition
* S s DataKeeper Cluster Edition nakonfigurovaný tak, že synchronně zrcadlí obsah další virtuální pevný disk připojený svazek ze zdrojové virtuální počítače na další virtuální pevný disk připojený svazek cílovém virtuálním počítači.
* S DataKeeper je poskytuje abstrakci zdrojové a cílové místní svazky a jejich obchodní úpravu clusteru převzetí služeb při selhání systému Windows jako jeden sdílený disk.

Můžete najít všechny podrobnosti o tom, jak nainstalovat s s DataKeeper a SAP v clusteru s podporou převzetí služeb při selhání systému Windows [Clustering SAP ASC instanci pomocí clusteru převzetí služeb při selhání systému Windows Server v Azure s DataKeeper s] [ ha-guide-classic] dokumentu white paper.

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Vysoká dostupnost pro instance SCS SAP (A) v systému Linux
Od prosince 2015 je zde také žádný ekvivalent sdíleného disku služby WSFC pro virtuální počítače s Linuxem v Azure. Alternativní řešení pomocí softwaru 3rdstrany jako s pro systém Windows ještě se neověřuje SAP systémem Linux v Azure.

#### <a name="high-availability-for-the-sap-database-instance"></a>Vysoká dostupnost pro instanci databáze SAP
Typické SAP databázového systému HA instalace je založena na dva databázového systému virtuální počítače, kde je funkce vysoké dostupnosti databázového systému používanou k replikaci dat od aktivní instance databázového systému do druhého virtuálního počítače do pasivní instance databázového systému.

Funkci obnovení po havárii s vysokou dostupností a pro databázového systému obecně také konkrétní databázového systému jsou popsané v [Průvodce nasazením databázového systému][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Vysoká dostupnost začátku do konce pro dokončení SAP systému
Následují dva příklady architektury dokončení SAP NetWeaver HA v Azure – jeden pro Windows a jeden pro Linux.

Nespravované jen disky: koncepty, jak je popsáno níže pravděpodobně došlo k narušení trochu při nasazování mnoha systémů SAP a počet nasazených virtuálních počítačů jsou překračuje maximální limit účtů úložiště na jedno předplatné. V takových případech nutné virtuální pevné disky virtuálních počítačů a nelze jej zkombinovat v rámci jednoho účtu úložiště. Obvykle lze provést kombinací virtuální pevné disky SAP aplikační vrstvu virtuální počítače různých systémů SAP.  Také jsme kombinaci různých virtuální pevné disky různé databázového systému virtuální počítače různých systémů SAP v jednom účtu úložiště Azure. Tím zachování IOPS omezení účtech úložiště Azure v paměti (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] HA v systému Windows
![Architektura HA aplikace SAP NetWeaver s SQL serverem v Azure IaaS][planning-guide-figure-3200]

Následující Azure konstrukce se používají pro systém SAP NetWeaver k minimalizaci vlivu podle problémů s infrastrukturou a hostování opravy:

* Celý systém je nasazen na Azure (požadováno – databázového systému vrstvy, (A) SCS instanci a dokončení aplikační vrstvu muset spustit ve stejném umístění).
* Celý systém běží v rámci jednoho předplatného Azure (povinné).
* Celý systém běží v rámci jedné virtuální sítě Azure (povinné).
* Oddělení virtuální počítače jednoho systému SAP do tří skupiny dostupnosti je možné, i když všechny virtuální počítače, které patří do stejné virtuální síti.
* Všechny virtuální počítače spuštěné instance databázového systému SAP systémů jsou v jedné skupině dostupnosti. Předpokládáme, že existuje více než jeden virtuální počítač spuštěné instance databázového systému na systém od nativní vysokou dostupnost databázového systému, které se používají funkce, jako je SQL Server AlwaysOn nebo Oracle Data Guard.
* Všechny virtuální počítače spuštěné instance databázového systému použít vlastní účet úložiště. Databázového systému souborů protokolu a data se replikují z jednoho účtu úložiště na jiný účet úložiště pomocí funkcí databázového systému vysoké dostupnosti, které se synchronizují data. Nedostupnosti jeden účet úložiště způsobí, že nedostupnosti jednoho uzlu clusteru serveru SQL Windows, ale ne celou službu systému SQL Server.
* Všechny virtuální počítače spuštěné instance (A) SCS jednoho systému SAP se nacházejí v jedné skupině dostupnosti. Windows Server Failover Cluster (WSFC) je nakonfigurován v rámci těchto virtuálních počítačů k ochraně (A) SCS instance.
* Všechny virtuální počítače spuštěné instance (A) SCS použít vlastní účet úložiště. (A) SCS instance soubory a složku globální SAP se replikují z jednoho účtu úložiště na jiný účet úložiště DataKeeper s replikací. Nedostupnosti jeden účet úložiště může způsobit nedostupnost jednoho (A) uzlu clusteru systému Windows SCS, ale ne celé (A) SCS služby.
* VŠECHNY virtuální počítače představující vrstvu serveru SAP aplikace jsou ve třetí dostupnosti nastavit.
* VŠECHNY virtuální počítače spuštěné SAP aplikační servery pomocí účtu úložiště. Nedostupnosti jeden SAP aplikačního serveru, kde nadále spouštět další SAP AS způsobí, že nedostupnosti jeden účet úložiště.

Následující obrázek ukazuje povahu stejné pomocí spravovaných disků.

![Architektura HA aplikace SAP NetWeaver s SQL serverem v Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] HA v systému Linux
Architektura pro SAP HA v systému Linux v Azure je v podstatě stejný jako u Windows jak bylo popsáno výše. Odkazovat na Poznámka SAP [1928533] seznam řešení s vysokou dostupností podporované.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Pomocí automatické spuštění pro instance SAP
SAP nabízí funkce pro spustit instance SAP ihned po spuštění operačního systému v rámci virtuálního počítače. Přesný postup byly popsané v článku znalostní báze SAP [1909114]. Však není SAP doporučujeme, aby se použilo nastavení už, protože neexistuje žádný ovládací prvek v pořadí restartování instance, za předpokladu, že více než jeden virtuální počítač nebyl vliv nebo více instancí spustili na virtuální počítač. Za předpokladu, že Azure Typický scénář v virtuálního počítače a jeden virtuální počítač nakonec získávání restartování v případě jedné instance serveru aplikace SAP, automatické spuštění není skutečně kritické a lze je povolit přidáním tento parametr:

    Autostart = 1

Do profilu spustit instance SAP ABAP nebo Java.

> [!NOTE]
> Některé downfalls také může mít parametr automatické spuštění. Podrobněji parametr aktivuje spuštění SAP ABAP nebo Java instance při spuštění související služby Windows nebo Linuxem instance. Který určitě je případ, kdy se spustí operační systém. Ale restartování služby SAP jsou také běžné věcí, kterou funkce správy životního cyklu softwaru SAP jako součet nebo jiné aktualizace nebo aktualizace. Tyto funkce nejsou byla očekávána instance na všechny automaticky restartovat. Proto by mělo být zakázáno automatické spuštění parametr před spuštěním takových úloh. Parametr automatické spuštění také nepoužívejte pro instance SAP, které jsou Clusterované, jako jsou ASC/SCS/CI.
>
>

Zjistit další informace o automatické spuštění pro SAP instance tady:

* [Spuštění a zastavení SAP společně s vaší spuštění a zastavení serveru systému Unix](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Spuštění a zastavení SAP NetWeaver správy agentů](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Postup povolení automatického spuštění HANA databáze](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Větší systémy SAP vrstvy 3
Aspekty vysoké dostupnosti 3vrstvé SAP konfigurace neobdrželo již popsané v předchozích částech. Ale co o může být nasazený systémů, kde jsou požadavky na server databázového systému moc velká, aby ji v Azure, ale aplikační vrstvu SAP do Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Umístění konfigurace SAP vrstvy 3
Není možné rozdělit aplikační vrstvě samostatně nebo aplikaci a databázového systému vrstvy mezi místními a Azure. Systému SAP je buď úplně nasadit místně nebo v Azure. Také se nepodporuje z aplikačních serverů, spusťte místní a jiná v Azure. Výchozí bod diskuse se. Můžeme také nejsou podporovány tak, aby měl komponenty databázového systému systému SAP a SAP vrstva aplikačního serveru, který je nasazen ve dvou různých oblastech Azure. Například databázového systému v západní USA a SAP aplikační vrstvu střed USA. Důvodem nejsou podporovány tyto konfigurace je latence citlivosti SAP NetWeaver architektury.

Ale v průběhu poslední rok center partnery vyvinuté společné umístění dat do oblasti Azure. Tyto společné umístění často se v velmi těsné blízkosti fyzické dat Azure centrech v rámci oblast Azure. Čekací doba, která je menší než 2 MS může způsobit krátkou vzdálenost a připojení prostředků ve společném umístění prostřednictvím ExpressRoute do Azure. V takových případech k vyhledání vrstvě databázového systému (včetně úložiště SAN nebo NAS) ve společném umístění a SAP aplikační vrstvu v Azure je možné. Od prosince 2015 nemáme všechna nasazení jako je například. Ale různých zákazníků, jejichž nasazení aplikace bez SAP již používáte takové přístupy.

### <a name="offline-backup-of-sap-systems"></a>Offline systémy zálohování SAP
Závisí na konfiguraci SAP existuje vybrali (vrstvy 2 nebo 3 úrovně) může být potřeba zálohovat. Obsah virtuální počítač plus vytvořit zálohu databáze. Očekává se, že zálohy související databázového systému provádět pomocí metod databáze. Podrobný popis různých databází, najdete v [databázového systému průvodce][dbms-guide]. Na druhé straně SAP data můžete zálohovat offline způsobem (včetně k databázi obsahu) jak je popsáno v této části nebo online jak je popsáno v následující části.

Zálohování offline by vyžadovaly v podstatě vypnutí virtuálního počítače prostřednictvím portálu Azure a kopírovat základní disk virtuálního počítače a všechny připojené disky na virtuální počítač. To by zachovávají bod čas Image virtuálního počítače a jeho přidružené disk. Doporučujeme zkopírovat zálohy do jiný účet úložiště Azure. Proto postup popsaný v kapitole [kopírování disků mezi účty úložiště Azure] [ planning-guide-5.4.2] by použití tohoto dokumentu.
Kromě toho ukončení pomocí portálu Azure, jeden také to provést pomocí prostředí Powershell nebo rozhraní příkazového řádku podle postupu popsaného tady: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Obnovení tohoto stavu by obsahovat odstranění základní virtuální počítač, jakož i původní disky virtuálního počítače základní a připojené disky, kopírování uložené disků zpět do původní účet úložiště nebo prostředek skupiny pro spravované disky a potom znovu nasazovat systému.
Tento článek ukazuje příklad, jak skript tento proces v prostředí Powershell: <http://www.westerndevs.com/azure-snapshots/>

Zkontrolujte, zda nainstalovat novou licenci SAP, protože obnovování zálohování virtuálních počítačů, jak je popsáno výše vytvoří nový klíč hardwaru.

### <a name="online-backup-of-an-sap-system"></a>Online zálohování systému SAP
Zálohování databázového systému se provádí pomocí metody specifické databázového systému, jak je popsáno v [databázového systému průvodce][dbms-guide].

Ostatní virtuální počítače v systému SAP lze zálohovat pomocí funkce zálohování virtuálních počítačů Azure. Zálohování virtuálního počítače Azure získali zavedená v rané fázi 2015 a mezitím je standardní metoda zálohování dokončení virtuálního počítače v Azure. Zálohování Azure ukládá zálohování v Azure a umožňuje obnovení virtuálního počítače znovu.

> [!NOTE]
> Od prosince 2015 pomocí zálohování virtuálních počítačů by neměly být jedinečné ID virtuálního počítače, který se používá pro SAP licencování. To znamená, že obnovení ze zálohy virtuálního počítače vyžaduje instalaci nový licenční klíč SAP jako obnovený virtuální počítač se považuje za nového virtuálního počítače a není to náhrada bývalé ta, která byla uložena.
>
> ![Windows][Logo_Windows] Windows
>
> Teoreticky, virtuálních počítačů, které spuštění databáze lze zálohovat konzistentním způsobem také pokud databázového systému systém podporuje systému Windows VSS (služby Stínová kopie svazku <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) jako, například SQL Server neexistuje.
> Upozorňujeme však, založený na zálohování virtuálního počítače Azure, které v daném okamžiku obnoví databází nejsou možné. Proto doporučujeme provést zálohování databází s funkcemi databázového systému, aniž byste museli spoléhat na zálohování virtuálních počítačů Azure.
>
> Abyste se seznámili s zálohování virtuálních počítačů Azure, spusťte zde: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Další možnosti se mají použít kombinaci aplikace Microsoft Data Protection Manager nainstalovat do virtuálního počítače Azure a Azure Backup k zálohování a obnovení databáze. Další informace naleznete zde: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Neexistuje žádný ekvivalent VSS systému Windows v systému Linux. Pouze konzistentními soubory zálohy jsou proto možná, ale není konzistentní s aplikací zálohování. Zálohování databázového systému SAP by mělo být provedeno pomocí funkce databázového systému. Systém souborů, které zahrnují data související s SAP lze uložit, například pomocí vkládání popsaný tady: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure jako web zotavení po Havárii pro produkční prostředí SAP krajiny
Rozšíření pro různé součásti kolem technologie Hyper-V, System Center a Azure od Mid 2014, povolit používání Azure jako lokalita zotavení po Havárii pro virtuální počítače spuštěné místní založené na technologii Hyper-V.

Blog s podrobnostmi o tom, jak nasadit toto řešení je tady popisujeme: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Souhrn
Klíčové body vysoké dostupnosti pro SAP systémy v Azure jsou:

* V tuto chvíli SAP jediný bod selhání nelze zabezpečit přesně stejně jako je možné ji provést v místním nasazení. Důvodem je, že sdíleného disku ještě nemůže být sestaven clustery v Azure bez použití softwaru 3. stran.
* Pro vrstvu databázového systému, budete muset použít funkce databázového systému, který není závislý na sdílený disk clusteru technologie. Podrobnosti jsou dokumentovány v článku [databázového systému průvodce][dbms-guide].
* Chcete-li minimalizovat dopad problémy v rámci domén selhání v Azure údržba infrastruktury nebo hostitele, byste měli používat skupiny dostupnosti Azure:
  * Doporučujeme mít jednu sadu dostupnosti pro SAP aplikační vrstvu.
  * Doporučuje se mít samostatnou sadu dostupnosti pro vrstvu databázového systému SAP.
  * NENÍ doporučujeme použít stejnou sadu dostupnosti pro virtuální počítače různých systémů SAP.
  * Doporučujeme použít prémiové disky spravované.
* Pro účely zálohování vrstvy databázového systému SAP, zkontrolujte, zda [databázového systému průvodce][dbms-guide].
* Zálohování instance SAP dialogové okno má moc smysl vzhledem k tomu, že je obvykle rychlejší znovu nasadit jednoduchý dialogu instancí.
* Zálohování virtuálních počítačů, které obsahuje globální adresář systému SAP a s ním všechny profily různých instancí dává smysl a opakuje zálohování nebo, například funkce vkládání v systému Linux. Vzhledem k tomu, že jsou rozdíly mezi Windows Server 2008 (R2) a Windows Server 2012 (R2), které usnadňují zálohování pomocí novější systému Windows Server verze, doporučujeme spustit jako Windows hostovaný operační systém Windows Server 2012 (R2).
