---
title: Azure Virtual Machines, plánování a implementace SAP Netweaveru | Dokumentace Microsoftu
description: Azure Virtual Machines, plánování a implementace SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b1f937a71a0ff5b8030c922073dc463af3c8430
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44349258"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure Virtual Machines, plánování a implementace SAP NetWeaver
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
[resource-groups-networking]:../../../networking/networking-overview.md
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
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
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
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
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

Microsoft Azure umožňuje společnostem získat výpočetních a úložných kapacit minimální pracovat bez zdlouhavé zajišťování cykly. Služba Azure virtuální počítač umožňuje společnostem nasazení klasického aplikací, jako je SAP NetWeaver na základě aplikací do Azure a rozšířit jejich spolehlivost a dostupnost bez nutnosti další prostředky k dispozici místně. Služby virtuálního počítače Azure podporuje také připojení mezi místními sítěmi, které umožňuje společnostem aktivně integrace virtuálních počítačů Azure do místních domén, jejich privátních Cloudů a jejich prostředí systému SAP.
Tento dokument white paper popisuje základní informace o virtuálním počítači Microsoft Azure a poskytuje návod, jak aspekty plánování a implementace pro instalacím systému SAP NetWeaver v Azure a jako takové musí být dokument k načtení před zahájením skutečné nasazení SAP NetWeaver v Azure.
Dokument doplňuje dokumentaci k instalaci SAP a SAP poznámky, které představují primární zdroje pro instalaci a nasazení softwaru SAP v dané platformy.

## <a name="summary"></a>Souhrn
Cloud Computing je často používaný termín, který získává čím dál větší význam v rámci oboru IT, od malých firem až po velké a nadnárodní společnosti.

Microsoft Azure je platforma cloudových služeb od Microsoftu, která nabízí široké spektrum nových možností. Zákazníci mají nyní možnost rychle zřizovat a rušit přístup aplikací jako službu v cloudu, takže už nejsou omezeni technickými nebo rozpočtovými limity. Namísto investování času a prostředků do hardwarové infrastruktury, společností můžou zaměřit na aplikace, obchodní procesy a jeho výhody pro zákazníky a uživatele.

Pomocí služeb Microsoft Azure pro virtuální počítače nabízí společnost Microsoft komplexní platformu infrastruktury jako služby (IaaS). Služba Azure Virtual Machines teď v rámci IaaS podporuje aplikace využívající SAP NetWeaver. Tento dokument White Paper popisuje postup plánování a implementace aplikací využívajících SAP NetWeaver v rámci Microsoft Azure jako platformu volbu.

Samotný dokument se zaměřuje na dva hlavní aspekty:

* První část popisuje dva modely nasazení podporované aplikací využívajících SAP NetWeaver v Azure. Popisuje taky obecné zpracování Azure s nasazeními SAP v úvahu.
* Druhá část Podrobnosti implementace dvě různé scénáře popsané v první části.

Další zdroje naleznete v kapitole [prostředky] [ planning-guide-1.2] v tomto dokumentu.

### <a name="definitions-upfront"></a>Definice předem
V celém dokumentu používáme následující termíny:

* IaaS: Infrastruktura jako služba
* PaaS: Platforma jako služba
* SaaS: Software jako služba
* Součást SAP: jednotlivé SAP aplikaci jako je třeba ECC, BW, správci řešení nebo podnikovém portálu.  Komponenty SAP může být založen na tradičních technologií ABAP a Java nebo jiných NetWeaver na základě aplikace, například pro obchodní objekty.
* Prostředí SAP: jedna nebo více součástí SAP logicky seskupeny provádět obchodní funkce jako je vývoj, QAS, školení, zotavení po Havárii nebo produkčního prostředí.
* Prostředí SAP: Tento termín se vztahuje na celý majetek SAP v zákazníka IT na šířku. Prostředí SAP zahrnuje všechny produkčního prostředí a neprodukční prostředí.
* Systém SAP: Kombinace DBMS vrstvy a aplikační vrstvu služby, například vývojový systém SAP ERP, SAP BW testovacího systému, SAP CRM produkční systém atd. V nasazení Azure není možné rozdělit tyto dvě vrstvy mezi místními a Azure. Znamená, že je buď systém SAP nasazené v místním nebo je nasazené v Azure. Však můžete nasadit různé systémy prostředí SAP do Azure nebo místně. Můžete například nasazení SAP CRM vývoj a testování v Azure, ale SAP CRM produkční systém místní systémy.
* Nasazení jenom cloudu: nasazení, kde předplatné Azure, které není připojené přes site-to-site nebo připojením ExpressRoute do místní síťové infrastruktury. Dokumentace v běžných Azure a tyto druhy nasazení jsou také popsány jako "Čistě cloudových" nasazení. Virtuální počítače nasazené pomocí této metody jsou přístupné prostřednictvím Internetu a veřejnou IP adresu a/nebo veřejný název DNS přiřadit k virtuálním počítačům v Azure. Pro Microsoft Windows, v místním Active Directory (AD) a DNS se rozšíří do Azure v těchto typech nasazení. Proto se virtuální počítače nejsou součástí skupiny v místní službě Active Directory. Totéž platí pro implementace systému Linux, například pomocí OpenLDAP + protokolu Kerberos.

> [!NOTE]
> Výhradně cloudový nasazení v tomto dokumentu je definován tak kompletní prostředí SAP běží výhradně v Azure bez rozšíření služby Active Directory / OpenLDAP nebo překladu názvů z místní do veřejného cloudu. Výhradně cloudový konfigurace se nepodporují konfigurace, kdy moduly SAP STM nebo jiných místních prostředků je potřeba použít mezi systémy SAP hostované na Azure a prostředky, které se nacházejí v místním nebo produkční systémy SAP.
>
>

* Mezi různými místy: Popisuje scénář, ve které jsou nasazené virtuální počítače s předplatným Azure, který má site-to-site a multi-Site, připojení ExpressRoute mezi místní nápomocni a Azure. Dokumentace v běžných Azure a tyto druhy nasazení jsou také popsány jako scénáře mezi různými místy. Důvod pro připojení je k rozšíření místních domén, v místním Active Directory/OpenLDAP a místní DNS do Azure. V místním prostředí je rozšířit na prostředky předplatného Azure. S touto příponou, virtuální počítače můžou být součástí místní domény. Domény uživatele z místní domény můžete přistupovat k serverům a spuštěním služby na těchto virtuálních počítačů (například systému DBMS služby). Komunikace a název řešení mezi virtuální počítače nasazené v místním a nasazených virtuálních počítačích Azure není možné. Toto je nejběžnější a skoro výhradně případ nasazení SAP prostředky do Azure. Další informace najdete v tématu [to] [ vpn-gateway-cross-premises-options] článku a [to][vpn-gateway-site-to-site-create].

> [!NOTE]
> Nasazení mezi místními systémy SAP, kde Azure Virtual Machines systémy SAP jsou členy domény služby v místním jsou podporovány pro produkční systémy SAP. Konfigurace mezi různými místy jsou podporovány pro nasazení částí nebo dokončení prostředí SAP do Azure. Dokonce i kompletní prostředí SAP běžící v Azure vyžaduje s tyto virtuální počítače se zapojil místní domény a reklamy/OpenLDAP. V předchozí verze dokumentace, už jsme mluvili o IT hybridních scénářích, kde výraz *hybridní* spočívají ve skutečnosti, že existuje připojení mezi místními sítěmi mezi místními a Azure. Kromě toho skutečnost, že virtuální počítače v Azure jsou součástí v místní službě Active Directory / OpenLDAP.
>
>

Některé dokumentaci Microsoftu popisuje scénáře, mezi různými místy trochu jinak, především pro DBMS HA konfigurace. V případě SAP související dokumenty scénáře mezi různými místy právě boils s tím, že site-to-site nebo připojení k privátní (ExpressRoute) a skutečnosti, že se prostředí SAP distribuuje mezi místními a Azure.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Prostředky
Pro téma nasazení SAP v Azure k dispozici jsou následující další příručky:

* [Azure Virtual Machines, plánování a implementace SAP Netweaveru (tohoto dokumentu)][planning-guide]
* [Nasazení virtuálních počítačů pro SAP NetWeaver v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP NetWeaver][dbms-guide]

> [!IMPORTANT]
> Všude, kde je možné odkaz na instalační příručce odkazující SAP použít (referenční dokumentace InstGuide-01, naleznete v tématu <http://service.sap.com/instguides>). Při rozhodování o požadavky a proces instalace, průvodců instalací SAPU NetWeaver by měl vždy být pečlivě si přečtěte, tento dokument obsahuje pouze konkrétní úlohy pro systémy SAP NetWeaver, které jsou nainstalované v virtuálního počítače Microsoft Azure.
>
>

Následující poznámky SAP se vztahují k tématu SAP v Azure:

| Poznámka: číslo | Titul |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: podporované produkty a velikosti |
| [2015553] |SAP v Microsoft Azure: požadavky pro podporu |
| [1999351] |Řešení potíží s rozšířené monitorování Azure pro SAP |
| [2178632] |Klíč monitorování metrik pro SAP v Microsoft Azure |
| [1409604] |Virtualizace ve Windows: rozšířené monitorování |
| [2191498] |SAP v Linuxu se službou Azure: rozšířené monitorování |
| [2243692] |Linux v Microsoft Azure (IaaS) virtuálních počítačů: problémy licence SAP |
| [1984787] |Operačního systému SUSE LINUX Enterprise Server 12: Poznámky k instalaci |
| [2002167] |Red Hat Enterprise Linux 7.x: instalace a Upgrade |
| [2069760] |Oracle Linux 7.x SAP instalace a Upgrade |
| [1597355] |Doporučení odkládacího prostoru pro Linux |

Přečíst také [oznámení změny stavu Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) , která obsahuje všechny poznámky SAP pro Linux.

Obecné výchozí omezení a omezení maximální předplatných Azure, najdete v [v tomto článku][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Možné scénáře
SAP se často používá jako jeden z nejdůležitější aplikace v rámci podniků. Architektura a operace těchto aplikací je ve většině případů komplexní a zajištění, že splňujete požadavky na dostupnost a výkon je důležité.

Podniky mají tedy přemýšlet pečlivě o tom, které aplikace se může spouštět ve veřejném cloudovém prostředí, nezávisle na zvolenou zprostředkovatele.

Typy možných systému pro nasazení SAP NetWeaver na základě aplikace v rámci veřejného cloudu, prostředí jsou uvedeny níže:

1. Střední produkční systémy
2. Vývoj systémů
3. Testování systémy
4. Systémy prototypu
5. Učení / ukázku systémy

Pokud chcete úspěšně nasadit systémy SAP do Azure IaaS nebo IaaS obecně platí, je důležité porozumět významných rozdílů mezi nabídky tradiční outsourcers nebo hostitelé a nabídky IaaS. Zatímco tradiční hostitele nebo pro subdodavatele přizpůsobí infrastruktury (typ sítě, úložiště a server) pro úlohy, které zákazník chce hostovat, je místo toho odpovědnosti zákazníka k výběru správné úloh pro nasazení IaaS.

Jako první krok potřeba zákazníků, zkontrolujte následující položky:

* SAP podporované typy virtuálních počítačů Azure
* SAP v Azure nepodporuje produktů a verzí
* Podporované operační systémy a DBMS aktualizací pro konkrétní verze SAP v Azure
* Propustnost protokoly SAP poskytuje různé skladové jednotky Azure

Odpovědi na tyto otázky najdete v poznámce SAP [1928533].

Jako druhý krok Azure omezení prostředků a šířka pásma musí být porovnáno více vlastního prostředku spotřebovávat místní systémy. Proto zákazníci musí být znáte různé možnosti Azure s řešením SAP v oblasti podporované typy:

* Prostředky procesoru a paměti různých typů virtuálních počítačů a
* Šířku pásma vstupně-výstupních operací různých typů virtuálních počítačů a
* Možnosti síťového různých typů virtuálních počítačů.

Většina těchto dat můžete najít [tady (Linux)] [ virtual-machines-sizes-linux] a [tady (Windows)][virtual-machines-sizes-windows].

Uvědomte si, že výše uvedené v na odkaz výše jsou horní meze. Neznamená to, který limity pro některý ze zdrojů, například vstupně-výstupních operací může zadat za všech okolností. Výjimky i když jsou prostředky procesoru a paměti zvolený typ virtuálního počítače. Pro SAP podporuje typy virtuálních počítačů prostředky procesoru a paměti jsou vyhrazené a jako takový je k dispozici v libovolném bodě v čase pro použití ve virtuálním počítači.

Platforma Microsoft Azure stejně jako jiné platformy IaaS je platforma pro více tenantů. V důsledku úložiště, sítě a další prostředky se sdílejí mezi tenanty. Inteligentní logiky omezení a kvóty se dá zabránit jednoho tenanta vliv na výkon jiného tenanta (hlučného souseda) závažný způsobem. Když logiky v Azure se pokusí zajistit odchylky v šířky pásma došlo malé, vysoce sdílené platformy mají tendenci zavést větší odchylky dostupnost prostředků nebo šířka pásma než mnoho zákazníků se používají k v jejich místní nasazení. V důsledku toho se mohou vyskytnout různé úrovně šířky pásma týkající se sítěmi nebo úložištěm vstupně-výstupních operací (na svazku stejně jako čekací doba) z minut na minutu. Pravděpodobnost, že systém SAP v Azure se může setkat s větší odchylky než v místním systému je potřeba vzít v úvahu.

Posledním krokem je vyhodnotit požadavky na dostupnost. Může se stát, že základní infrastrukturu Azure je potřeba aktualizovat a vyžaduje hostitelích spuštěných virtuálních počítačů, třeba restartovat. V těchto případech byste se vypínají a restartují i virtuálních počítačů spuštěných na těchto hostitelích. Načasování těchto údržba se provádí během pracovní doby. jiné než core pro konkrétní oblasti, ale je poměrně široké okno potenciální několik hodin, během které dojde k restartování. Existují různé technologie v rámci platformy Azure, který se dá zmírnit některé nebo všechny vlivu těchto aktualizací. Budoucí vylepšení platformy Azure, DBMS a SAP aplikace jsou navržená tak, chcete-li minimalizovat dopad těchto restartování.

Pokud chcete úspěšně nasadit systém SAP do Azure, místní SAP systémy operační systém, databáze a aplikací SAP musí být uvedena na matice podpory SAP Azure, přizpůsobit v prostředcích Azure může zajistit infrastrukturu a které můžete pracovat u nabídek dostupnosti smlouvy o úrovni služeb Microsoft Azure. Tyto systémy jsou jsme uvedli, musíte se rozhodnout na jednom z následujících scénářů dvě nasazení.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Výhradně cloudový – nasazení virtuálních počítačů do Azure bez závislosti na místní síť zákazníka
![Jeden virtuální počítač s SAP firma ukázkovou nebo trénovacího scénáře, které jsou nasazené v Azure][planning-guide-figure-100]

Tento scénář je typický pro školení nebo systémy ukázku, ve kterém jsou nainstalovány všechny součásti softwaru mimo systém SAP a SAP v rámci jednoho virtuálního počítače. Produkční systémy SAP nejsou podporovány v tomto scénáři nasazení. Obecně platí tento scénář splňuje následující požadavky:

* Vlastních virtuálních počítačů jsou přístupné přes veřejnou síť. Není nutné přímého síťového připojení pro aplikace běžící v rámci virtuálních počítačů k místní síti společnosti vlastnící ukázky nebo školení obsah nebo zákazníka.
* V případě představující školení nebo ukázka scénář několik virtuálních počítačů síťové komunikace a název řešení musí fungovat mezi virtuálními počítači. Ale komunikace mezi sadu virtuálních počítačů musí být izolované tak, aby několik sad virtuálních počítačů můžete nasadit souběžně bez rušení.  
* Připojení k Internetu je vyžadován pro koncového uživatele na vzdálené přihlášení do na virtuální počítače hostované v Azure. V závislosti na hostovaný operační systém, Terminálové služby/RDS nebo VNC/ssh slouží k přístupu k virtuálnímu počítači splnění úkolů školení nebo provádět ukázky. Pokud SAP porty, jako je například 3200 3300 & 3600 může také být vystaveny instance aplikace SAP je přístupný z desktopu připojené žádné Internet.
* Systémy SAP (a představují VM(s)) samostatný scénář v Azure, který pouze veřejné připojení k Internetu vyžaduje pro přístup koncových uživatelů a nevyžaduje připojení k jiným virtuálním počítačům v Azure.
* SAPGUI prohlížeč nainstalovat a spustit přímo ve virtuálním počítači.
* Je vyžadována rychlé obnovení virtuálního počítače do původního stavu a znovu nové nasazení tohoto původního stavu.
* V případě ukázky a školení scénáře, které jsou realizované víc virtuálních počítačů, službu Active Directory / service OpenLDAP a/nebo DNS je vyžadován pro každou sadu virtuálních počítačů.

![Skupiny Virtuálních počítačů reprezentovat jeden firma ukázkovou nebo trénovací scénář v cloudové službě Azure][planning-guide-figure-200]

Je důležité mít na paměti, které virtuální počítače v jednotlivých sad je nutné nasadit paralelně, kde názvy virtuálních počítačů v každé sadě jsou stejné.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Mezi různými místy – nasazení jednoho nebo několika virtuálních počítačích SAP do Azure s požadavkem je plně integrován do místní sítě
![Virtuální privátní sítě s připojením Site-To-Site (mezi různými místy)][planning-guide-figure-300]

Tento scénář je mezi různými místy scénář s mnoha možných nasazení vzory. Může být popsány jako spuštění některých částí systému SAP na šířku na místě a šířku ostatní části systému SAP v Azure. Všechny aspekty skutečnost, že součást SAP komponenty běží na Azure by měl být transparentní pro koncové uživatele. Proto moduly SAP přenosu oprava systému (STM), RFC komunikace, tisk, zabezpečení, (jako je jednotné přihlašování), atd. bez problému fungují pro systémy SAP běžící v Azure. Ale mezi různými místy scénář také popisuje scénář, kde běží kompletní prostředí SAP v Azure zákazníka domény a DNS rozšíří do Azure.

> [!NOTE]
> Toto je scénář nasazení, který není podporován pro spouštění produktivní systémů SAP.
>
>

Čtení [v tomto článku] [ vpn-gateway-create-site-to-site-rm-powershell] Další informace o tom, jak připojit místní sítě k Microsoft Azure

> [!IMPORTANT]
> Pokud se myslí scénáře různých lokalit mezi Azure a místních zákaznických nasazení, uvažujeme členitosti celé systémů SAP. Scénáře, které jsou *nepodporuje* pro více míst scénáře jsou:
>
> * Spouštění různých vrstev aplikací SAP v různých metod nasazení. Například spuštěných DBMS vrstvy místní, ale aplikační vrstvě SAP na virtuálních počítačích nasadit jako virtuální počítače Azure nebo naopak.
> * Některé součásti vrstvu SAP v Azure a některá místní. Příklad rozdělení instance aplikační vrstvě SAP mezi místní a virtuální počítače Azure.
> * Distribuce virtuální počítače spuštěné instance SAP jednoho systému nad několika oblastmi Azure se nepodporuje.
>
> Důvod pro tato omezení je požadavkem pro vysoce výkonné sítě s nízkou latencí v rámci jednoho systému SAP, zejména mezi instancemi aplikace a systém DBMS vrstvy systému SAP.
>
>

### <a name="supported-os-and-database-releases"></a>Podporované operační systém a verze databáze
* Serverového softwaru Microsoftu pro služby virtuálního počítače Azure je uvedené v tomto článku nepodporuje: <http://support.microsoft.com/kb/2721672>.
* Podporované operační systém verze, vydání systému databáze podporován služby virtuálního počítače Azure společně s softwaru SAP jsou dokumentovány v článku Poznámka SAP [1928533].
* Aplikace SAP a vydání podporována v služby virtuálního počítače Azure jsou popsané v Poznámka SAP [1928533].
* Ke spuštění jako virtuální počítače hostované v Azure pro scénáře SAP jsou podporovány pouze 64bitové kopie. V důsledku toho jsou podporovány pouze 64bitové aplikace SAP a databází.

## <a name="microsoft-azure-virtual-machine-services"></a>Služby virtuálního počítače Microsoft Azure
Platforma Microsoft Azure se internetovém měřítku platforma pro cloudové služby hostované a provozované v datových centrech společnosti Microsoft. Platforma obsahuje služeb virtuálního počítače Microsoft Azure (infrastruktury jako služby nebo IaaS) a sadu bohatá platforma jako služba (PaaS) možnosti.

Platforma Azure omezuje potřebu přímých technologie a nákupech infrastruktury. Zjednodušuje údržbu a provozování aplikací tím, že poskytuje výpočetní prostředky na vyžádání a úložiště pro hostování, škálování a Správa webové aplikace a propojených aplikací. Správa infrastruktury je automatizované díky platformě, která je navržená pro zajištění vysoké dostupnosti a dynamické škálování tak, aby odpovídaly potřebám využití s možností cenového modelu s průběžnými platbami.

![Umístění služby virtuálního počítače Microsoft Azure][planning-guide-figure-400]

Pomocí služby virtuálního počítače Azure Microsoft je povolíte nasazení vlastních serverových imagí do Azure jako instance IaaS (viz obrázek 4). Virtuální počítače v Azure jsou založené na Hyper-V virtuální pevné disky (VHD) a je možné spouštět různé operační systémy jako hostovaný operační systém.

Z hlediska provozní do služby Azure Virtual Machines nabízí podobné možnosti jako virtuální počítače nasazené v místním prostředí. Má ale důležité výhodou je, není nutné s nákupem, spravovat a správou infrastruktury. Vývojáři a správci mají plnou kontrolu nad bitovou kopii operačního systému v rámci těchto virtuálních počítačů. Správci můžou přihlásit vzdáleně těchto virtuálních počítačů k provádění údržby a řešení potíží s úkoly, stejně jako úkoly nasazení softwaru. Ve vztahu nasazení jsou pouze omezení velikosti a funkce virtuálních počítačů Azure. Tyto velikosti nemusí být v pořádku detailní v konfiguraci může udělat v místním prostředí. Existuje široký výběr typy virtuálních počítačů, které představují kombinaci:

* Počet virtuálních procesorů
* Memory (Paměť)
* Počet virtuálních pevných disků, které je možné připojit
* Šířky pásma sítě a úložiště

Velikost a omezení různých velikostí různé virtuální počítače nabízí, můžete vidět v tabulce v [(Linux) v tomto článku] [ virtual-machines-sizes-linux] a [v tomto článku (Windows)] [virtual-machines-sizes-windows].

Ne všechny řadu různých virtuálních počítačů může nabízet v jednotlivých oblastech Azure (pro další kapitola najdete v oblasti Azure). Také mějte na paměti, že ne všechny virtuální počítače nebo virtuálního počítače-Series jsou certifikované pro SAP.

> [!IMPORTANT]
> Pro použití aplikací využívajících SAP NetWeaver, pouze podmnožinu typy virtuálních počítačů a konfigurace uvedené v Poznámka SAP [1928533] jsou podporovány.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Oblasti Azure
Nasazení virtuálních počítačů do tzv *oblastí Azure*. Určitá oblast Azure může být jeden nebo více datových center, které se nacházejí v těsné blízkosti. Pro většinu geopolitické oblasti na světě společnost Microsoft má aspoň dvě oblasti Azure. Například v Evropě existuje určitá oblast Azure z *Severní Evropa* a jeden z *západní Evropa*. Tyto dvě oblasti Azure v rámci geopolitické oblasti jsou odděleny natolik závažné, vzdálenost tak, aby přírodních nebo technických jiného problému ovlivňujícího nemají vliv na obě oblasti Azure ve stejné geopolitické oblasti. Od Microsoftu je neustálé vytváření nové oblasti Azure v různých oblastech geopolitických globálně, počet těchto oblastí postupného růstu a od prosince 2015 dosaženo počtu 20 oblastech Azure jsme oznámili již i další oblasti. Zákazníky můžete nasadit systémy SAP do všech těchto oblastí, včetně dvou oblastí Azure v Číně. Aktuální aktuální informace o oblastech Azure najdete v tomto webu: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Koncept virtuálního počítače Microsoft Azure
Microsoft Azure nabízí infrastruktura jako služba (IaaS) řešení do hostitele virtuálních počítačů s podobné funkce jako v místním řešení virtualizace. Budete moct vytvořit virtuální počítače z webu Azure portal, Powershellu nebo rozhraní příkazového řádku, které také umožňují nasazení a možnosti správy.

Azure Resource Manager umožňuje zřizovat aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Použít stejnou šablonu k opakovanému nasazení aplikace během každé fáze životního cyklu aplikace.

Další informace o použití šablon Resource Manageru najdete tady:

* [Nasadit a spravovat virtuální počítače pomocí šablony Azure Resource Manager a rozhraní příkazového řádku Azure] [../../linux/create-ssh-secured-vm-from-template.md]
* [Správa virtuálních počítačů pomocí Azure Resource Manageru a Powershellu][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Další zajímavé funkcí je schopnost vytvářet Image z virtuálních počítačů, které vám umožní připravit určité úložiště, ze kterých budete moct rychle nasazovat instance virtuálních počítačů, které splňují vaše požadavky.

Další informace o vytváření bitové kopie z virtuálních počítačů najdete v [(Linux) v tomto článku] [ virtual-machines-linux-capture-image-resource-manager] a [v tomto článku (Windows)] [ virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domény selhání
Domény selhání představují fyzické jednotky selhání úzce souvisí s fyzické infrastruktury, které jsou obsaženy v datových centrech, a zatímco fyzické okna nebo stojan lze považovat za doménu selhání, neexistuje žádné přímé mapování 1: 1 mezi těmito dvěma.

Když nasadíte několik virtuálních počítačů jako součást jednoho systému SAP v služby virtuálního počítače Microsoft Azure, můžete ovlivnit kontroler prostředků infrastruktury Azure k nasazení aplikace do různých domén selhání, a tím splňuje požadavky Microsoft Azure SLA. Rozšiřování domén selhání v jednotce škálování Azure (kolekce stovky výpočetních uzlů a uzly úložiště a sítě) nebo přiřazení virtuální počítače, které konkrétní doména selhání je však něco nad tím, které nemají přímou kontrolu. Aby bylo možné směrovat kontroler prostředků infrastruktury Azure k nasazení sady virtuálních počítačů přes různých domén selhání, musíte přiřadit skupině dostupnosti Azure virtuálních počítačích v době nasazení. Další informace o skupinách dostupnosti Azure naleznete v kapitole [skupin dostupnosti Azure] [ planning-guide-3.2.3] v tomto dokumentu.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Upgradovací domény
Upgradovací domény představují logické jednotky, která vám pomůže určit, jak se virtuální počítač v rámci systému SAP, který se skládá z instance SAP běžící v několika virtuálních počítačů, aktualizuje. Pokud dojde k upgradu, Microsoft Azure prochází proces aktualizace těchto Upgradovacích doménách jeden po druhém. Tím, že rozprostírá virtuálních počítačů v době nasazení v různých doménách upgradovat, lze ochranu vašeho systému SAP částečně před potenciální výpadek. Pokud chcete vynutit Azure k nasazení virtuálních počítačů rozdělené do jiných upgradu domén systému SAP, je nutné nastavit konkrétní atribut v době nasazení každého virtuálního počítače. Podobně jako u domén selhání, jednotka škálování Azure je rozdělen do více domén upgradu. Aby bylo možné směrovat kontroler prostředků infrastruktury Azure k nasazení sady virtuálních počítačů přes různé domény upgradu, musíte přiřadit skupině dostupnosti Azure virtuálních počítačích v době nasazení. Další informace o skupinách dostupnosti Azure naleznete v kapitole [skupin dostupnosti Azure] [ planning-guide-3.2.3] níže.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Skupinu dostupnosti Azure
Azure Virtual Machines v rámci jedné sady dostupnosti Azure rozdělené podle kontroler prostředků infrastruktury Azure v různých chybových nebo upgradovat doménách. Účelem distribuce v různých chybových nebo upgradu domény je zabránit probíhalo vypínání v případě infrastruktury údržbě nebo selhání v rámci jedné domény selhání všech virtuálních počítačích systému SAP. Ve výchozím nastavení virtuální počítače nejsou součástí skupiny dostupnosti. Účast virtuálního počítače ve skupině dostupnosti je definován v době nasazení nebo později tak, že změna konfigurace a opětovné nasazení virtuálního počítače.

Vysvětlení konceptu skupin dostupnosti Azure a způsob, jakým skupiny dostupnosti se týkají selhání a upgradu domény, přečtěte si [v tomto článku][virtual-machines-manage-availability]

K definování skupiny dostupnosti pro Azure Resource Manageru pomocí šablony json, naleznete v tématu [specifikace rozhraní rest api](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) a vyhledejte položku "dostupnosti".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Úložiště: Microsoft Azure Storage a datové disky
Microsoft Azure Virtual Machines využívat různé typy úložišť. Při implementaci SAP na virtuálních počítačů služby Azure, je důležité znát rozdíly mezi těmito dvěma hlavní typy úložiště:

* Dočasný, ale volatilních úložiště.
* Trvalé úložiště.

Virtuální počítače Azure nabízejí dočasné disky po nasazení virtuálního počítače. V případě restartování virtuálního počítače vymaže veškerý obsah na těchto jednotkách. Jedná se tedy vzhledem k tomu, že datové soubory a soubory protokolů a znovu databází za žádných okolností musíte umístit na těchto jednotkách netrvalé. Můžou nastat výjimky pro některé databáze, kde tyto disky netrvalé může být vhodný pro databázi tempdb a tabulkové temp prostory. Ale Vyhněte se použití těchto jednotek pro virtuální počítače řady A-Series, od těchto jednotek netrvalé mají omezenou propustnost s rodiny virtuálních počítačů. Další podrobnosti najdete v článku [pochopení dočasné jednotky ve službě Windows Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

- - -
> ![Windows][Logo_Windows] Windows
> 
> Jednotce D:\ ve Virtuálním počítači Azure je jednotka netrvalé, která je založená na některé místní disky na Azure výpočetním uzlu. Protože je dočasný, to znamená, že dojde ke ztrátě všechny změny provedené v obsahu na jednotku D:\, po restartování virtuálního počítače. "Všechny změny", jako jsou soubory uložené, vytvoření adresáře aplikace nainstalované, atd.
> 
> ![Linux][Logo_Linux] Linux
> 
> Virtuální počítače Azure s Linuxem automaticky připojit jednotku na /mnt/resource, který je jednotka netrvalé se opírá o místní disky na Azure výpočetním uzlu. Protože je dočasný, to znamená, že všechny změny obsahu v /mnt/resource ztraceny po restartování virtuálního počítače. Všechny změny, jako jsou soubory uložené, vytvoření adresáře aplikace nainstalované, atd.
> 
> 

- - -

Microsoft Azure Storage poskytuje trvalého úložiště a typickou úroveň ochrany a redundance na úložiště SAN. Disky založené na Azure Storage jsou virtuální pevný disk (VHD) umístěný ve službě Azure Storage. Místní Disk operačního systému (Windows C:\, Linux/dev/sda1) je uložen v úložišti Azure a další svazky nebo disky připojené k virtuálnímu počítači získat uložené, příliš.

Je možné nahrát existujícího virtuálního pevného disku v místním nebo vytvoření prázdných z v rámci Azure a připojení do nasazené virtuální počítače.

Po vytvoření a nahrání virtuálního pevného disku do služby Azure Storage, je možné připojit a připojte do stávající virtuální počítač a zkopírovat existující virtuální pevný disk (odpojeny).

Tyto virtuální pevné disky jsou trvalé, dat a souvisejícím adresářům změny jsou bezpečné při restartování a znovu vytvořit instanci virtuálního počítače. I v případě, že se odstraní instanci, těmto virtuálním pevným diskům zůstal v bezpečí a můžete znovu nasadit nebo v případě disky bez operačního systému je možné připojit k jiným virtuálním počítačům.

Další informace o službě Azure Storage najdete tady:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Storage úrovně Standard
Azure storage úrovně Standard je typ úložiště k dispozici při vydání Azure IaaS. Došlo k vstupně-výstupních operací kvóty vynucena na jeden disk. Latenci nebyla ve stejné třídě, jako obvykle nasazeni systémů SAP vyšší kategorie zařízení SAN nebo NAS hostované místně. Nicméně dokázaly dostatečná pro mnoho stovky Azure Standard Storage systémů SAP vyhrazené mezitím nasazené v Azure.

Disky, které jsou uložené na standardních účtech úložiště Azure se účtují podle skutečné data uložená, objemu transakcí úložiště, přenosy odchozích dat a zvolené možnosti redundance. Mnoho disky se dají vytvořit na maximální 1TB, velikost, ale tak dlouho, dokud plánované aktualizace mezipaměti zůstávají prázdné se neúčtují žádné poplatky. Když potom vyplněním jeden virtuální pevný disk s 100GB, bude vám účtována pro ukládání 100GB a ne pro nominální velikost, kterou vytvořil virtuální pevný disk s.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Premium Storage
Azure Premium Storage je teď zavedená s cílem poskytnout:

* Lepší latenci vstupu/výstupu.
* Vyšší propustnost.
* Méně proměnlivé latence vstupu a výstupu.

Pro tento účel byly zavedeny mnoho změn jsou dvě nejvýznamnější:

* Použití disků SSD v uzlech služby Azure Storage
* Novou mezipaměť zajišťovanou místní jednotkou SSD výpočetního uzlu Azure pro čtení

V opačném úložiště úrovně Standard, ve kterém funkce nezměnil závisí na velikosti disku (nebo virtuální pevný disk), Premium Storage aktuálně má tři kategorie jiný disk, které se zobrazují v tomto článku: <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Uvidíte, že jsou závislé na kategorii velikost disků vstupně-výstupních operací/disku a disk propustnost/disk

Základní náklady v případě služby Premium Storage není svazek skutečná data uložená v těchto disků, ale velikost kategorie takové disku, nezávisle na množství dat, která je uložena v rámci disku.

Můžete také vytvořit disky v Premium Storage, která nejsou mapování přímo do kategorií velikost je znázorněno. To může být případ, zejména v případě, že kopírování disků ze Standard Storage do Premium Storage. V takových případech se provádí mapování na další největší možnost disku služby Premium Storage.

Většina rodiny virtuálních počítačů Azure certified s řešením SAP budou moct pracovat s Premium Storage a nebo směs mezi Azure standard a Premium Storage.

Pokud jsou rezervace součástí virtuálních počítačů řady DS-series v [(Linux) v tomto článku] [ virtual-machines-sizes-linux] a [v tomto článku (Windows)][virtual-machines-sizes-windows], pamatujte, že existují omezení objem dat na disky Premium Storage na členitosti úroveň virtuálního počítače. Jiné řady DS nebo virtuální počítače řady GS-series také mít různá omezení související s počet datových disků, které je možné připojit. Tato omezení jsou popsané v článku také uvedených výše. Ale v podstatě znamená, že pokud, například připojíte 32 x P30 disků na jeden virtuální počítač DS14 není získáte 32 x maximální propustnost disků P30. Místo toho maximální propustnost na úrovni virtuálního počítače, jak je uvedeno v následujícím článku omezuje propustnost dat.

Další informace o Premium Storage najdete tady: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>


#### <a name="azure-storage-accounts"></a>Účty služby Azure Storage
Při nasazování služeb nebo virtuálních počítačů v Azure, nasazení virtuálních pevných disků a Imagí virtuálních počítačů mohou být uspořádány v jednotkách nazývaných účtech úložiště Azure. Při plánování nasazení služby Azure, budete muset pečlivě zvažte omezení Azure. Na jedné straně je omezený počet účtů úložiště, jedno předplatné Azure. I když každý účet úložiště Azure může obsahovat velký počet souborů virtuálního pevného disku, je pevný limit na celkový počet IOPS na účet úložiště. Nasazení stovky virtuálních počítačů SAP pomocí vytváření významné vstupně-výstupní operace volání systémy DBMS, se doporučuje pro distribuci vysoké vstupně-výstupních operací DBMS VMs mezi více účtů úložiště Azure. Nechcete překračovat aktuální limit účtech úložiště Azure na jedno předplatné musí věnovat pozornost. Vzhledem k tomu, že úložiště je důležitou součástí nasazení databáze pro systém SAP, tento koncept je podrobněji popsána podrobněji už odkazované [nasazení DBMS][dbms-guide].

Další informace o účtech Azure Storage najdete v [v tomto článku][storage-scalability-targets]. Přečtení tohoto článku, je dobré si uvědomit, že existují rozdíly v omezení mezi účty Azure Standard Storage a účty služby Premium Storage. Hlavními rozdíly jsou objem dat, která mohou být uloženy v rámci účtu úložiště. Ve standardním úložišti svazek je větší než díky službě Premium Storage velikost. Na druhé straně standardní účet úložiště je vážně v omezená vstupně-výstupních operací (Zobrazit sloupec **celková míra žádosti**), že účet služby Azure Premium Storage nemá žádné takové omezení. Pojednává o podrobnosti a výsledky z těchto rozdílů diskuse o nasazení systémů SAP, hlavně servery DBMS.

V rámci účtu úložiště máte možnost vytvořit různé kontejnery za účelem uspořádání a zařazení různých virtuálních pevných disků. Tyto kontejnery se používají k například samostatné virtuální pevné disky různých virtuálních počítačů. Nejsou k dispozici žádný vliv na výkon pomocí právě jeden kontejner nebo více kontejnerů pod jeden účet úložiště Azure.

Název virtuálního pevného disku v rámci Azure probíhá následující pojmenování připojení, které je potřeba zadat jedinečný název pro virtuální pevný disk v rámci Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Výše uvedené řetězec musí jednoznačně identifikovat virtuální pevný disk, který je uložený ve službě Azure Storage.

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Spravované disky
Spravované disky jsou nového typu prostředku v Azure Resource Manageru, který je možné použít místo virtuální pevné disky, které se ukládají v účtech úložiště Azure. Spravované disky automaticky zarovnají dostupnosti daného virtuálního počítače, které jsou připojeny k a proto zvýšit dostupnost virtuálního počítače a služby, které jsou spuštěné na virtuálním počítači. Další informace najdete v článku [přehledovém článku](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Doporučujeme používat se spravovanými disky, protože toto zjednodušení nasazení a správu virtuálních počítačů.
SAP aktuálně podporuje jenom Managed Disks úrovně Premium. Další informace najdete v článku Poznámka SAP [1928533].


#### <a name="microsoft-azure-storage-resiliency"></a>Odolnost proti chybám služby Microsoft Azure Storage
Microsoft Azure Storage ukládá základní virtuální pevný disk (s operačním systémem) a připojených disků nebo objekty BLOB na alespoň tři samostatné uzly úložiště. Tato skutečnost se nazývá místní redundantní úložiště (LRS). LRS se výchozí nastavení pro všechny typy úložišť v Azure. 

Několika způsoby další redundance, všechny popsané v článku [replikace Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Od Azure Premium Storage, což je doporučený typ úložiště pro virtuální počítače DBMS a disky, které ukládají soubory databáze a protokolu/znovu, je k dispozici pouze metody LRS. V důsledku toho musíte nakonfigurovat databázi metody, jako je SQL serveru Always On, Oracle Data Guard nebo systémové replikace HANA povolíte replikaci dat databáze do jiné oblasti Azure nebo jiné zóny dostupnosti Azure.


> [!NOTE]
> Pro nasazení DBMS využití geograficky redundantní úložiště jako dostupné úložiště Azure úrovně Standard se nedoporučuje, protože to ovlivňuje výkon a nastavení nejsou respektovat pořadí zápisu v různých virtuálních pevných disků, které jsou připojeny k virtuálnímu počítači. Skutečnost, že není v různých virtuálních pevných disků příslušných pořadí zápisu nese vysoké riziko ukládaly do nekonzistentní databáze na straně cíle replikace, pokud soubory databáze a protokolu/znovu jsou rozděleny mezi více virtuálních pevných disků (jako většinou případ) na zdrojovém virtuálním počítači na straně.


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Síťových služeb Microsoft Azure
Microsoft Azure poskytuje síťovou infrastrukturu, která umožňuje mapování všechny scénáře, které chceme, aby mohli využívat pomocí softwaru SAP. Možnosti jsou:

* Přístup z vnějšku, přímo k virtuálním počítačům přes Windows Terminálové služby nebo ssh/VNC
* Přístup ke službám a určité porty používané aplikacemi v rámci virtuálních počítačů
* Interní komunikace a překlad mezi skupiny virtuální počítače nasazené jako virtuální počítače Azure
* Připojení mezi místními sítěmi mezi místní sítí zákazníka a síť Azure
* Pro různé oblasti Azure nebo datové centrum připojení mezi weby Azure

Další informace najdete tady: <https://azure.microsoft.com/documentation/services/virtual-network/>

Existují různé možnosti konfigurace název a IP řešení v Azure. V tomto dokumentu čistě cloudové scénáře využívají výchozí použití Azure DNS (na rozdíl od definování vlastní službu DNS). Je také nová služba Azure DNS, který je možné použít místo nastavení serveru DNS. Další informace najdete v [v tomto článku] [ virtual-networks-manage-dns-in-vnet] a na [na této stránce](https://azure.microsoft.com/services/dns/).

Pro scénáře, mezi různými místy, jsme závislí vychází ze skutečnosti, které místní AD/OpenLDAP/DNS bylo rozšířeno přes VPN nebo privátní připojení k Azure. U určitých scénářů, jak je zde uvedeno, může být nutné mít repliku AD/OpenLDAP nainstalované v Azure.

Protože sítě a překladu je důležitou součástí nasazení databáze pro systém SAP, tento koncept je podrobněji popsána podrobněji [nasazení DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Virtuální sítě Azure
Sestavením službě Azure Virtual Network můžete definovat rozsah adres privátní IP adresy přidělené funkčnost Azure DHCP. Ve scénářích mezi různými místy rozsah IP adres definované je pořád ještě přidělená pomocí protokolu DHCP v Azure. Překlad názvů domény však probíhá místní (za předpokladu, že virtuální počítače jsou součástí doménu v místním) a proto může překládat adresy nad rámec různých cloudových služeb Azure.

Každému virtuálnímu počítači v Azure musí být připojené k virtuální síti.

Další podrobnosti najdete v [v tomto článku] [ resource-groups-networking] a na [na této stránce](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Ve výchozím nastavení po nasazení virtuálního počítače nejde změnit konfiguraci virtuální sítě. Nastavení protokolu TCP/IP musí být ponechány na server Azure DHCP. Výchozí chování je přiřazení dynamické IP adresy.
>
>

Adresa MAC virtuální síťové karty může například změnit po změny velikosti a Windows nebo Linuxem hostovaného operačního systému vybere nové síťové karty a automaticky přiřadit adresy IP a DNS v tomto případě používá protokol DHCP.

##### <a name="static-ip-assignment"></a>Přiřazení statické IP adresy
Je možné přiřadit pevné nebo vyhrazené IP adresy k virtuálním počítačům ve virtuální síti Azure. Provoz virtuálních počítačů ve virtuální síti Azure otevře skvělou možnost využívat tuto funkci, pokud potřebná nebo vyžaduje pro několik scénářů. Přiřazení IP adresy zůstává v platnosti v průběhu existence virtuálního počítače, nezávisle na tom, jestli je virtuální počítač spuštění nebo vypnutí. V důsledku toho je nutné vzít celkový počet virtuálních počítačů (spuštěných a zastavených virtuálních počítačů) v úvahu při definování rozsahu IP adres pro virtuální síť. IP adresa zůstane přiřazená, až do odstranění virtuálního počítače a jeho síťové rozhraní, nebo dokud se IP adresa získá Zrušit znovu přiřadí. Další informace najdete v článku [v tomto článku][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Byste měli přiřadit statické IP adresy pomocí Azure znamená, že na jednotlivé virtuální síťové adaptéry. Pro virtuální síťový adaptér by neměl přiřadit statické IP adresy v rámci hostovaného operačního systému. Některé služby Azure, jako je služba Azure Backup závisí na skutečnost, který na alespoň primárního virtuálního síťového adaptéru je nastavena, DHCP, ne statické IP adresy. Viz také dokument [Poradce při potížích s Azure se zálohováním virtuálních počítačů](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Pokud je potřeba přiřadit více statických IP adres k virtuálnímu počítači, musíte přiřadit víc karet Vnic k virtuálnímu počítači.
>
>

##### <a name="multiple-nics-per-vm"></a>Několik síťových rozhraní na virtuálním počítači
Můžete definovat více virtuální síťové karty (vNIC) pro virtuální počítač Azure. Možnost mít více virtuální síťové adaptéry, které lze spustit k nastavení síťového provozu oddělení, kde například klientský provoz směrován přes jeden virtuální síťový adaptér a back-endu provoz směrován přes druhý virtuální síťový adaptér. Závisí na typu virtuálního počítače existuje narazíte na různých omezení namapoval na počet virtuální síťové adaptéry. Najdete přesné informace, funkce a omezení najdete v těchto článcích:

* [Vytvoření virtuálního počítače s Windows s několika síťovými kartami][virtual-networks-multiple-nics-windows]
* [Vytvoření virtuálního počítače s Linuxem s několika síťovými kartami][virtual-networks-multiple-nics-linux]
* [Nasazení s více přenosovými síťové karty virtuálního počítače pomocí šablony][virtual-network-deploy-multinic-arm-template]
* [Nasazení virtuálních počítačů síťovou kartu s více pomocí Powershellu][virtual-network-deploy-multinic-arm-ps]
* [Nasazení virtuálních počítačů síťovou kartu s více přenosovými pomocí rozhraní příkazového řádku Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Připojení Site-to-Site
Mezi různými místy je virtuálních počítačů Azure a místní propojené pomocí připojení VPN transparentní a trvalé. Očekává se stane Nejběžnější vzor nasazení SAP v Azure. Předpokladem je, že provozních postupů a procesů pomocí instance SAP v Azure by měl fungovat transparentně. Tato znamená, že byste měli tisk mimo tyto systémy, jakož i použití SAP přenosu Management System (TMS) k přenosu se změní z vývojového systému v Azure na testovací systém, který je místně nasadili. Další dokumentaci kolem site-to-site najdete v [v tomto článku][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Zařízení tunelového propojení sítě VPN
Chcete-li vytvořit připojení site-to-site (v místním datovém centru k datovému centru Azure), musíte buď získat a nakonfigurovat zařízení VPN nebo použít směrování a vzdálený přístup (RRAS) zavedenou jako softwarová součást v systému Windows Server 2012.

* [Vytvoření virtuální sítě s připojením VPN site-to-site pomocí Powershellu][vpn-gateway-create-site-to-site-rm-powershell]
* [Informace o zařízeních VPN pro připojení typu Site-to-Site VPN Gateway][vpn-gateway-about-vpn-devices]
* [Nejčastější dotazy k branám VPN][vpn-gateway-vpn-faq]

![Připojení Site-to-site mezi místními a Azure][planning-guide-figure-600]

Obrázek nahoře ukazuje, že dva odběry služeb Azure máte IP adresu podrozsahů vyhrazené pro použití ve virtuálních sítích v Azure. Přes síť VPN se navazuje připojení z místní sítě do Azure.

#### <a name="point-to-site-vpn"></a>Point-to-Site VPN
Point-to-site VPN vyžaduje každý klientský počítač připojit pomocí vlastní sítě VPN do Azure. Pro scénáře SAP uvažujeme, připojení point-to-site není praktické. Proto žádné další odkazy jsou uvedeny pro připojení k síti VPN point-to-site.

Další informace najdete tady
* [Konfigurace připojení typu Point-to-Site k virtuální síti pomocí webu Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurace připojení typu Point-to-Site k virtuální síti pomocí PowerShellu](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>VPN Multi-Site
Azure také v současné době nabízí možnost vytvořit připojení VPN více lokalit pro jedno předplatné Azure. V rámci jednoho předplatného dříve omezovala na jednoho připojení sítě VPN site-to-site. Toto omezení ztraceny s připojeními VPN typu Multi-Site pro jedno předplatné. Díky tomu je možné využívat více než jedné oblasti Azure pro konkrétní předplatné prostřednictvím konfigurace mezi různými místy.

Další dokumentaci najdete v tématu [v tomto článku][vpn-gateway-create-site-to-site-rm-powershell]



#### <a name="vnet-to-vnet-connection"></a>Připojení mezi virtuálními sítěmi
Pomocí sítě VPN typu Multi-Site, musíte nakonfigurovat samostatnou virtuální síť Azure v jednotlivých oblastech. Ale často máte požadavek, který by měl softwarové komponenty v různých oblastech komunikovat mezi sebou. V ideálním případě tato komunikace by neměl směrovat z jedné oblasti Azure do místního a z něj do jiné oblasti Azure. Pro místní Azure nabízí možnost pro konfiguraci připojení z jedné virtuální sítě Azure v jedné oblasti s jinou virtuální sítí Azure hostované v jiné oblasti. Tato funkce se nazývá připojení VNet-to-VNet. Další podrobnosti o této funkci najdete tady: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Soukromé připojení k Azure ExpressRoute
Microsoft Azure ExpressRoute umožňuje vytvářet privátní připojení mezi datacentry Azure a v místní infrastruktuře buď zákazníka nebo ve společném umístění prostředí. ExpressRoute nabízí různé MPLS poskytovatelé VPN (přepínáním paketů) nebo jiných poskytovatelů síťových služeb. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Připojení ExpressRoute nabízejí vyšší úroveň zabezpečení a spolehlivější prostřednictvím více paralelních okruhů, vyšší rychlost a nižší latenci než Typická připojení přes Internet.

Najdete další informace o Azure ExpressRoute a nabídky tady:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Expressroute umožňuje více předplatných Azure prostřednictvím jednoho okruhu ExpressRoute, jak je uvedeno zde

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Vynucené tunelování v případě mezi různými místy
Pro virtuální počítače připojení k místním doménám prostřednictvím site-to-site, point-to-site nebo ExpressRoute budete muset Ujistěte se, že jsou získávání pro všechny uživatele v těchto virtuálních počítačů a nasadili nastavení internetového proxy serveru. Ve výchozím nastavení se software na tyto virtuální počítače nebo uživatele, kteří používají prohlížeč pro přístup k Internetu nedařilo přes proxy server společnosti, ale by připojit přímo prostřednictvím Azure k Internetu. Ale i nastavení serveru proxy není 100 % řešení ke směrování provozu přes proxy server společnosti, protože jde o odpovědnosti softwaru a služeb ke kontrole pro proxy server. Pokud software spuštěný na virtuálním počítači, který teď nebo správce provádí úpravy nastavení, provoz do Internetu mohou být zkrácen znovu přímo prostřednictvím Azure k Internetu.

Pokud se chcete vyhnout takové přímé připojení k Internetu, můžete nakonfigurovat vynucené tunelování s připojením site-to-site mezi místními a Azure. Podrobný popis funkce vynuceného tunelování se publikuje sem <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Ve zákazníkům inzeruje výchozí trasu prostřednictvím relací vytvoření partnerského vztahu protokolu BGP ExpressRoute je povolené vynucené tunelování s ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Přehled sítě Azure
Tato kapitola obsahuje mnoho důležitých bodů o sítě Azure. Tady je přehled hlavních bodů:

* Virtuální sítě Azure umožňují vložit struktury sítě do nasazení vašeho řešení Azure. Virtuální sítě můžou být izolované proti sobě nebo s použitím skupin zabezpečení sítě provoz mezi virtuálními sítěmi je možné řídit.
* Virtuální sítě Azure můžete využít k přiřazení rozsahy IP adres k virtuálním počítačům nebo přiřadit pevné IP adresy k virtuálním počítačům
* Nastavit připojení Site-To-Site nebo Point-To-Site je potřeba nejprve vytvořit virtuální síť Azure
* Po nasazení virtuálního počítače se už nedají změnit virtuální síť přiřazené k virtuálnímu počítači

### <a name="quotas-in-azure-virtual-machine-services"></a>Kvóty služby virtuálních počítačů Azure
Musíme být jasné, o tom, že úložiště a síťové infrastruktury jsou sdílena mezi virtuální počítače spuštěné v infrastruktuře Azure celou řadu služeb. A stejně jako u zákazníka vlastních datových center, bylo potřeba zřizovat z některé z prostředků infrastruktury v míře. Platforma Microsoft Azure používá disku, využití procesoru, sítě a další kvóty pro omezení využití prostředků a zachování konzistentní vzhledem k aplikacím a deterministický výkonu.  Různé typy virtuálních počítačů (A5, A6 atd.) mají různé kvóty pro počet disků, procesoru, paměti RAM a síti.

> [!NOTE]
> Jsou prostředky procesoru a paměti SAP podporuje typy virtuálních počítačů v hostitelských uzlech předběžně přidělit. To znamená, že nasadíte virtuální počítač a prostředky v hostiteli k dispozici v souladu s definicemi typu virtuálního počítače.
>
>

Při plánování a změny velikosti SAP na řešení Azure, musí být zvážen kvóty pro jednotlivé velikosti virtuálního počítače. Kvóty virtuálních počítačů jsou popsány [tady (Linux)] [ virtual-machines-sizes-linux] a [tady (Windows)][virtual-machines-sizes-windows].

Kvót popsané představují teoretický maximální hodnoty.  Limit vstupně-výstupních operací na disk lze dosáhnout pomocí malé IOs (8kb), ale pravděpodobně nemusí dosáhnout s Iosem velký (1 Mb).  Limit vstupně-výstupních operací je vynucena na členitosti jeden disk.

Jako hrubý rozhodovací strom se rozhodnout, zda systém SAP zapadá do služby virtuálního počítače Azure a jeho funkce nebo určuje, zda je potřeba nakonfigurovat jinak k nasazení systému na Azure stávající systém je možné rozhodovacího stromu níže:

![Rozhodovací strom rozhodnout možnost nasazení SAP v Azure][planning-guide-figure-700]

**Krok 1**: nejdůležitější informace je začít s protokoly SAP požadavkem pro daný systém SAP. Požadavky na protokoly SAP muset být rozdělen do částí DBMS a části aplikace SAP i v případě, že systém SAP je už nasazená místně v konfiguraci vrstvy 2. Stávajících systémů můžete určit nebo odhadují podle existující srovnávací testy SAP přístupové body, které často souvisí hardware používá. Výsledky najdete tady: <https://sap.com/about/benchmark.html>.
Pro nově nasazené systémy SAP by měl prošli velikosti cvičení, které by měl určit požadavky na protokoly SAP systému.
Viz také tento blog a přiložený dokument k určení velikosti SAP v Azure: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Krok 2**: stávajících systémů, se měří množství vstupně-výstupní operace a vstupně-výstupních operací za sekundu na serveru DBMS. Pro nově plánované systémy cvičení nastavení velikosti pro nový systém také měl dát hrubý nápady požadavků na vstupně-výstupních operací na straně DBMS. Pokud nejste jisti, časem budete muset provést testování konceptu.

**Krok 3**: porovnání požadavek na protokoly SAP DBMS server s přístupové body můžou poskytovat různé typy virtuálních počítačů Azure. Informace na protokoly SAP různých typů virtuálních počítačů Azure najdete v poznámce SAP [1928533]. Fokus by měl být na virtuálním počítači DBMS nejprve protože databázové vrstvě je vrstvě v systému SAP NetWeaver, který není horizontální navýšení kapacity ve většině nasazení. Naproti tomu SAP aplikační vrstvu lze škálovat. Pokud žádná z SAP nepodporuje typy virtuálních počítačů Azure můžete poskytovat požadované přístupové body, nelze spustit úlohy plánovaný systém SAP v Azure. Můžete buď budete muset nasadit v systému on-premises nebo budete muset změnit svazek zatížení systému.

**Krok 4**: jak je uvedeno [tady (Linux)] [ virtual-machines-sizes-linux] a [tady (Windows)][virtual-machines-sizes-windows], Azure vynucuje kvóty vstupně-výstupních operací na disk nezávislé, ať už používáte Storage úrovně Standard nebo Premium Storage. Závisí na typu virtuálního počítače, počet datových disků, které je možné připojit se liší. V důsledku toho můžete vypočítat maximální počet vstupně-výstupních operací, které lze dosáhnout pomocí různých typech virtuálních počítačů. Závislé na rozložení souboru databáze, vám může prokládanou disky se jeden svazek v hostovaném operačním systému. Nicméně pokud aktuální objem vstupně-výstupních operací nasazený systém SAP překročí počítané omezení největší typ virtuálního počítače Azure, a pokud neexistuje žádné možnost kompenzovat s větší pamětí, zatížení systému SAP může mít dopad na vážně. V takových případech mohli dostat do bodu, kde by neměl nasazení systému na Azure.

**Krok 5**: zejména systémů SAP, které jsou místně nasadit v konfiguracích vrstvy 2, se pravděpodobnost, že systém může být potřeba nakonfigurovat v Azure v konfiguraci vrstvy 3. V tomto kroku je potřeba zkontrolovat, zda je součásti v aplikační vrstvě SAP, které nelze škálovat a které nevejde do prostředky procesoru a paměti, které nabízejí různé typy virtuálních počítačů Azure. Pokud takové součásti skutečně existuje, systém SAP a její úlohy nelze nasadit v Azure. Ale pokud součásti aplikace SAP můžete škálovat do několika virtuálních počítačů Azure, v systému je možné nasadit do Azure.

**Krok 6**: Pokud DBMS a SAP součásti vrstvy aplikace může běžet na virtuálních počítačích Azure, musí být definován s ohledem na konfiguraci:

* Počet virtuálních počítačů Azure
* Typy virtuálních počítačů pro jednotlivé komponenty
* Počet virtuálních pevných disků ve virtuálním počítači databázového systému zajistit dostatek vstupně-výstupních operací

## <a name="managing-azure-assets"></a>Správa prostředků Azure
### <a name="azure-portal"></a>portál Azure
Na webu Azure portal je jednou tři rozhraní pro správu nasazení virtuálního počítače Azure. Základní úlohy správy, jako je nasazení virtuálních počítačů z imagí, lze provést prostřednictvím webu Azure portal. Kromě toho vytváření účtů úložiště, virtuální sítě a dalšími komponentami Azure, jsou také úlohy, které může také zpracovávat na webu Azure portal. Funkce, jako jsou nahrávání virtuálních pevných disků do Azure z místního nebo kopírování virtuálního pevného disku v rámci Azure, ale jsou úlohy, které vyžadují nástroje třetích stran nebo správa pomocí Powershellu nebo rozhraní příkazového řádku.

![Portál Microsoft Azure – Přehled virtuálních počítačů][planning-guide-figure-800]

[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

Správu a konfiguraci úlohy pro instanci virtuálního počítače je možné z webu Azure portal.

Kromě restartování a vypínání virtuálního počítače můžete také připojení, odpojení a datových disků pro instance virtuálních počítačů, zachytit instanci pro přípravu image, vytvořte a nakonfigurujte velikost instance virtuálního počítače.

Na webu Azure portal poskytuje základní funkce pro nasazení a konfigurace virtuálních počítačů a mnoha dalšími službami Azure. Ale ne všechny dostupné funkce se bude vztahovat na webu Azure portal. Na webu Azure Portal není možné provádět úkoly, jako je:

* Ukládání virtuálních pevných disků do Azure
* Kopírování virtuálních počítačů

[comment]: <> (MShermannd TODO, co o automatizaci služby pro virtuální počítače SAP? )
[comment]: <> (MSSedusch nasazení více virtuálních počítačů os mezitím možná)
[comment]: <> (Jakýkoli typ automatizace týkající se nasazení MSSedusch také není možné pomocí webu Azure portal. Úlohy, jako je nasazení více virtuálních počítačů není možné prostřednictvím webu Azure portal.)

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Správa prostřednictvím rutin Powershellu pro Microsoft Azure
Prostředí Windows PowerShell je výkonný a rozšiřitelný rámec, který má byla široce přijat zákazníky nasazující větší počet systémů v Azure. Po instalaci rutin Powershellu na počítači, přenosných počítačů nebo vyhrazené správy stanice dá se spouštět rutiny Powershellu vzdáleně.

Proces umožňující místní desktop nebo přenosný počítač pro použití rutin Powershellu pro Azure a jak tyto konfigurace pro využití pomocí předplatných Azure je popsán v [v tomto článku][powershell-install-configure].

Podrobnější pokyny k instalaci, aktualizaci a konfigurace Azure Powershellu rutin najdete také v [této kapitole Průvodce nasazením][deployment-guide-4.1].

Zkušenosti zatím bylo, prostředí PowerShell (PS) je určitě výkonnější nástroje pro nasazení virtuálních počítačů a vytvořit vlastní kroky v nasazení virtuálních počítačů. Všem zákazníkům běžící instance SAP v Azure používají rutiny PS doplnit úlohy správy, proveďte na webu Azure Portal nebo dokonce i pomocí rutiny PS výhradně pro správu jejich nasazení v Azure. Protože rutiny týkající se Azure sdílejí stejné zásady vytváření názvů jako více než 2000 rutiny související s Windows, je snadný úkol pro správce Windows využívat tyto rutiny.

Podívejte se na příklad tady: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd TODO popisují při testování nového příkazu rozhraní příkazového řádku )
Nasazení rozšíření Azure Monitoring for SAP (viz kapitola [řešení pro monitorování Azure pro SAP] [ planning-guide-9.1] v tomto dokumentu) je možné pouze prostřednictvím Powershellu nebo rozhraní příkazového řádku. Proto je nutné vytvořit a nakonfigurovat prostředí PowerShell nebo rozhraní příkazového řádku pro nasazování nebo správu systém SAP NetWeaver v Azure.  

Jak Azure poskytuje další funkce, nové rutiny PS se chystáte přidat, který vyžaduje aktualizace rutin. Proto je vhodné kontrolovat web Azure, stáhněte si alespoň jednou měsíc <https://azure.microsoft.com/downloads/> pro novou verzi rutin. Nová verze se nainstaluje na starší verzi.

Obecnější seznam souvisejících s Azure PowerShell příkazy najdete tady: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Správa prostřednictvím příkazů rozhraní příkazového řádku Microsoft Azure
Pro zákazníky, kteří používají Linux a chcete spravovat Azure nemusí být prostředky Powershellu možnost. Společnost Microsoft nabízí rozhraní příkazového řádku Azure jako alternativu.
Rozhraní příkazového řádku Azure nabízí sadu open source příkazů pro různé platformy pro práci s platformou Azure. Rozhraní příkazového řádku Azure poskytuje většinu funkcí najít na webu Azure Portal.

Informace o instalaci, konfiguraci a jak používat rozhraní příkazového řádku najdete v tématu příkazy provádět úkoly v Azure

* [Instalace Azure CLI][xplat-cli]
* [Nasadit a spravovat virtuální počítače pomocí šablony Azure Resource Manager a rozhraní příkazového řádku Azure] [../../linux/create-ssh-secured-vm-from-template.md]
* [Použití Azure CLI pro Mac, Linux a Windows pomocí Azure Resource Manageru][xplat-cli-azure-resource-manager]

Také přečíst kapitolu [rozhraní příkazového řádku Azure pro virtuální počítače s Linuxem] [ deployment-guide-4.5.2] v [Průvodce nasazením] [ planning-guide] o tom, jak pomocí Azure CLI k nasazení, monitorování Azure Rozšíření pro SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Různé způsoby, jak nasadit virtuální počítače pro SAP v Azure
V této kapitole zjistíte různé způsoby, jak nasadit virtuální počítač v Azure. Postupy provést další přípravu, stejně jako zpracování virtuální pevné disky a virtuální počítače v Azure jsou zahrnuté v této kapitole.

### <a name="deployment-of-vms-for-sap"></a>Nasazení virtuálních počítačů pro SAP
Microsoft Azure nabízí několik způsobů, jak nasadit virtuální počítače a přidružené disky. Proto je důležité porozumět rozdílům od přípravy virtuálních počítačů se mohou lišit v závislosti na metodě nasazení. Obecně platí jsme se podívejte na následující scénáře:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Přesun virtuálního počítače z místního Azure s-zobecněný disk
Máte v plánu přesouvat místní konkrétního systému SAP do Azure. To můžete udělat tak, že nahrajete virtuální pevný disk, který obsahuje operační systém, SAP binární soubory a binární soubory DBMS a virtuální pevné disky se soubory protokolu a data DBMS na Azure. Rozdíl od [scénář #2 níže][planning-guide-5.1.2], zachovat název hostitele, SAP SID a SAP uživatelské účty ve virtuálním počítači Azure, jak byla nakonfigurována v místním prostředí. Proto zobecňuje bitovou kopii není nutné. Najdete v kapitolách [přípravu pro přesun virtuálního počítače z místního Azure s diskem zobecněný] [ planning-guide-5.2.1] tohoto dokumentu pro místní přípravné kroky a nahrávání-zobecněný virtuální počítače nebo virtuální pevné disky do Azure. Přečíst kapitolu [scénář 3: přesunutí virtuálního počítače pomocí virtuálního pevného disku není zobecněný Azure s řešením SAP v místním] [ deployment-guide-3.4] v [Průvodce nasazením] [ deployment-guide] pro Podrobné kroky nasazení těchto image v Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Nasazení virtuálního počítače s bitovou kopií určitého zákazníka
Z důvodu požadavků na oprav konkrétní verze operačního systému nebo DBMS zadaný imagí v Tržišti Azure Marketplace, nemusí odpovídat vašim potřebám. Proto můžete potřebovat k vytvoření virtuálního počítače pomocí vlastní privátní image operačního systému/DBMS virtuálního počítače, který po nasazení potom několikrát. Příprava duplikace privátní image, musí být považovány za následující položky:

- - -
> ![Windows][Logo_Windows] Windows
>
> Další podrobnosti najdete tady: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> nastavení Windows (např. Windows SID nebo název hostitele) musí být na místní virtuální počítač pomocí příkazu sysprep abstrahovaná/zobecněný.
>
>
> ![Linux][Logo_Linux] Linux
>
> Postupujte podle kroků popsaných v těchto článcích [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd], nebo [Oracle Linux] [ virtual-machines-linux-create-upload-vhd-oracle], chcete-li připravit virtuální pevný disk nahrát do Azure.
>
>

- - -
Pokud jste již nainstalovali SAP obsah ve vaší místní virtuální počítač (zejména pro systémy vrstvy 2), můžete upravit nastavení systému SAP po nasazení virtuálního počítače Azure prostřednictvím instance přejmenovat postupu podporovány službou SAP Software zřizování správce (SAP Mějte na paměti [1619720]). Najdete v kapitolách [přípravy pro nasazení virtuálního počítače pomocí bitové kopie zákaznického pro SAP] [ planning-guide-5.2.2] a [nahrání VHD z místního úložiště do Azure] [ planning-guide-5.3.2]tohoto dokumentu pro místní přípravné kroky a nahrání generalizovaného virtuálního počítače do Azure. Přečíst kapitolu [scénář 2: nasazení virtuálního počítače s použitím vlastní image pro SAP] [ deployment-guide-3.3] v [Průvodce nasazením] [ deployment-guide] podrobný postup nasazení Tyto image v Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Nasazení virtuálního počítače mimo Azure Marketplace
Chcete používat Microsoft nebo třetích stran, pokud image virtuálního počítače z Azure Marketplace k nasazení virtuálního počítače. Po nasazení virtuálního počítače v Azure, můžete postupovat podle stejné pokyny a nástroje k instalaci softwaru SAP a/nebo DBMS uvnitř virtuálního počítače, jako byste to udělali v místním prostředí. Podrobný popis nasazení, naleznete v kapitole [scénář 1: nasazení virtuálního počítače mimo Azure Marketplace pro SAP] [ deployment-guide-3.2] v [Průvodce nasazením] [ deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Příprava virtuálních počítačů s řešením SAP pro Azure
Před nahráním virtuálních počítačů do Azure, musíte zajistit, aby že virtuální počítače a virtuální pevné disky splnit určité požadavky. Existují malé rozdíly v závislosti na metodě nasazení, který se používá.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Příprava pro přesun virtuálního počítače z místního Azure s-zobecněný disk
Běžnou metodu nasazení je přesunout existující virtuální počítač, který používá systém SAP do Azure z místního. Tento virtuální počítač a systém SAP ve virtuálním počítači, stačí by měl spustit v Azure pomocí stejného názvu hostitele a pravděpodobně na stejný identifikátor SID SAP. V takovém případě by neměla zobecnit pro více nasazení hostovaného operačního systému virtuálního počítače. Pokud v místní síti je teď rozšíří do Azure (viz kapitola [mezi různými místy – nasazení jednoho nebo několika virtuálních počítačích SAP do Azure s požadavkem je plně integrován do místní sítě] [ planning-guide-2.2] v tomto dokumentu), pak i stejné účty domény je možné v rámci virtuálního počítače jako ty byly použity před místní.

Požadavky při přípravě vlastní disku virtuálního počítače Azure jsou:

* Původně virtuálního pevného disku obsahující operační systém může mít maximální velikost 127GB jsou pouze. Toto omezení je teď odstranili na konci března 2015. Virtuální pevný disk obsahující operační systém nyní může být velikost až 1TB, jako jakékoli jiné služby Azure Storage i hostované virtuální pevný disk.
* Musí být v pevném formátu virtuálního pevného disku. Dynamické virtuální pevné disky nebo virtuální pevné disky ve formátu VHDx zatím nepodporují v Azure. Dynamických virtuálních pevných disků budou převedeny na statické virtuální pevné disky, když nahrajete virtuální pevný disk pomocí rutiny prostředí PowerShell nebo rozhraní příkazového řádku
* Virtuálních pevných disků, které jsou připojené k virtuálnímu počítači a by měla být připojená znovu v Azure pro virtuální počítač nemusí být v také pevném formátu virtuálního pevného disku. Čtení [(Linux) v tomto článku](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) a [v tomto článku (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) pro omezení velikosti datových disků. Dynamických virtuálních pevných disků budou převedeny na statické virtuální pevné disky, když nahrajete virtuální pevný disk pomocí rutiny prostředí PowerShell nebo rozhraní příkazového řádku
* Přidejte další místní účet s oprávněními správce, které se dají použít tak, že podpory společnosti Microsoft nebo které je možné přiřadit jako kontext pro služby a aplikace, které poběží, dokud je nasazen virtuální počítač a vhodnější uživatelů je možné použít.
* Pro případ použití scénář cloudového nasazení (naleznete v kapitole [výhradně Cloudový – nasazení virtuálních počítačů do Azure bez závislosti na své lokální síti zákazníka] [ planning-guide-2.1] tohoto dokumentu) v kombinace pomocí této metody nasazení, účtů domény nemusí fungovat po nasazení Azure Disk v Azure. To platí zejména pro účty, které se používají ke spouštění služeb, jako jsou aplikace DBMS nebo SAP. Proto budete muset nahraďte tyto účty domény místní účty virtuálního počítače a odstranit místních účtů domény ve virtuálním počítači. Zachování místní domény uživatele v image virtuálního počítače není problém, když je virtuální počítač nasazený ve scénáři mezi různými místy, jak je popsáno v kapitole [mezi různými místy – nasazení jednoho nebo několika virtuálních počítačích SAP do Azure s požadavkem je plně integrováno v místní síti] [ planning-guide-2.2] v tomto dokumentu.
* Je-li doménové účty byly použity jako DBMS přihlášení nebo uživatele při spouštění systému v místním a tyto virtuální počítače mají být nasazeny v čistě cloudové scénáře, třeba odstranit uživatele domény. Potřebujete, abyste měli jistotu, že místní správce a jiného uživatele místní virtuální počítač se přidá jako přihlášení nebo uživatele do databázového systému jako správce.
* Přidáte další místní účty, protože ty mohou být potřebné pro scénáře nasazení.

- - -
> ![Windows][Logo_Windows] Windows
>
> V tomto scénáři žádné generalizace (sysprep) virtuálního počítače je potřeba nahrát a nasadit virtuální počítač v Azure.
> Ujistěte se, že tuto jednotku D:\ se nepoužívá.
> Nastavení automatického připojení disku pro připojené disky, jak je popsáno v kapitole [nastavení automatického připojení pro připojené disky] [ planning-guide-5.5.3] v tomto dokumentu.
>
> ![Linux][Logo_Linux] Linux
>
> V tomto scénáři žádné generalizace (waagent-zrušení zřízení) virtuálního počítače je potřeba nahrát a nasadit virtuální počítač v Azure.
> Ujistěte se, že se nepoužívá/mnt/prostředků a že jsou všechny disky připojené přes uuid. Pro disk s operačním systémem Ujistěte se, že položka spouštěcí zavaděč také odráží připojení na základě identifikátoru uuid.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Příprava pro nasazení virtuálního počítače pomocí bitové kopie zákaznického pro SAP
Soubory virtuálního pevného disku, které obsahují zobecněný operačního systému jsou uloženy v kontejnery v účtech úložiště Azure nebo jako Image spravovaného disku. Nasadíte nový virtuální počítač z takových bitové kopie pomocí odkazu na image virtuálního pevného disku nebo spravovaného disku jako zdroj v souborech šablony nasazení, jak je popsáno v kapitole [scénář 2: nasazení virtuálního počítače s použitím vlastní image pro SAP] [ deployment-guide-3.3]z [Průvodce nasazením][deployment-guide].

Požadavky při přípravě vlastní Image virtuálního počítače Azure jsou:

* Původně virtuálního pevného disku obsahující operační systém může mít maximální velikost 127GB jsou pouze. Toto omezení je teď odstranili na konci března 2015. Virtuální pevný disk obsahující operační systém nyní může být velikost až 1TB, jako jakékoli jiné služby Azure Storage i hostované virtuální pevný disk.
* Musí být v pevném formátu virtuálního pevného disku. Dynamické virtuální pevné disky nebo virtuální pevné disky ve formátu VHDx zatím nepodporují v Azure. Dynamických virtuálních pevných disků budou převedeny na statické virtuální pevné disky, když nahrajete virtuální pevný disk pomocí rutiny prostředí PowerShell nebo rozhraní příkazového řádku
* Virtuálních pevných disků, které jsou připojené k virtuálnímu počítači a by měla být připojená znovu v Azure pro virtuální počítač nemusí být v také pevném formátu virtuálního pevného disku. Přečtěte si prosím [(Linux) v tomto článku](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) a [v tomto článku (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows) pro omezení velikosti datových disků. Dynamických virtuálních pevných disků budou převedeny na statické virtuální pevné disky, když nahrajete virtuální pevný disk pomocí rutiny prostředí PowerShell nebo rozhraní příkazového řádku
* Vzhledem k tomu, že všichni uživatelé domény zaregistrovaný jako uživatelům ve virtuálním počítači nesmí existovat v čistě cloudové scénáře (viz kapitola [výhradně Cloudový – nasazení virtuálních počítačů do Azure bez závislosti na své lokální síti zákazníka] [ planning-guide-2.1] tohoto dokumentu), služby, používání takové domény účty nemusí fungovat po nasazení Image v Azure. To platí zejména pro účty, které se používají ke spuštění služeb, jako je SAP DBMS aplikací. Proto budete muset nahraďte tyto účty domény místní účty virtuálního počítače a odstranit místních účtů domény ve virtuálním počítači. Zachování místní domény uživatele v image virtuálního počítače nemusí být problém, když virtuální počítač je nasazený ve scénáři mezi různými místy, jak je popsáno v kapitole [mezi různými místy – nasazení jednoho nebo několika virtuálních počítačích SAP do Azure s požadavkem je plně integrovaná v místní síti] [ planning-guide-2.2] v tomto dokumentu.
* Přidáte jiný místní účet s oprávněními správce, které je možné použít podpory společnosti Microsoft v šetření problém nebo které je možné přiřadit jako kontext pro služby a aplikace, které poběží, dokud je nasazen virtuální počítač a více příslušným uživatelům je možné použít.
* V čistě cloudových nasazení a kde doménové účty byly použity jako DBMS přihlášení nebo uživatele při spuštění systému v místním je potřeba odstranit uživatele domény. Potřebujete, abyste měli jistotu, že místní správce a jiného uživatele místní virtuální počítač se přidá jako přihlášení a uživatele DBMS jako správce.
* Přidáte další místní účty, protože ty mohou být potřebné pro scénáře nasazení.
* Pokud image obsahuje k instalaci aplikace SAP NetWeaver a přejmenovat název hostitele z původní název Přejme během nasazení Azure je pravděpodobné, že doporučuje se kopírovat nejnovější verze DVD pro správce zřizování softwaru SAP do šablony. To vám umožní snadno použít funkci přejmenování SAP poskytuje můžete přizpůsobit změněné název hostitele nebo změnit identifikátor SID systému SAP v rámci do nasazené bitové kopie virtuálního počítače, co nejdříve spustit novou kopii.

- - -
> ![Windows][Logo_Windows] Windows
>
> Ujistěte se, že jednotku D:\ není použít automatického připojení disku sady pro připojené disky, jak je popsáno v kapitole [nastavení automatického připojení pro připojené disky] [ planning-guide-5.5.3] v tomto dokumentu.
>
> ![Linux][Logo_Linux] Linux
>
> Ujistěte se, že se nepoužívá/mnt/prostředků a že jsou všechny disky připojené přes uuid. Pro disk s operačním systémem zkontrolujte, zda že položka spouštěcí zavaděč také odráží připojení na základě identifikátoru uuid.
>
>

- - -
* Grafické uživatelské rozhraní SAP (pro správce a instalační program účely) můžete předem nainstalované v těchto šablon.
* Další software, které jsou potřebné k úspěšnému spuštění virtuálních počítačů v různých scénářích nainstalujete tak dlouho, dokud tento software může spolupracovat s přejmenování virtuálního počítače.

Pokud virtuální počítač připravený dostatečně obecný a nakonec nezávisle na účtů/uživatele není k dispozici ve scénáři nasazenou Azure, se provádí poslední krok přípravy zobecňuje tyto image.

##### <a name="generalizing-a-vm"></a>Zobecňuje se virtuální počítač
- - -
> ![Windows][Logo_Windows] Windows
>
> Posledním krokem je pro přihlášení k virtuálnímu počítači pomocí účtu správce. Otevřete příkazové okno Windows jako *správce*. Přejděte na %windir%\windows\system32\sysprep a spustit sysprep.exe.
> Malé okno se zobrazí. Je důležité zkontrolovat **generalizace** možnost (výchozí hodnota je zaškrtnuté políčko) a změňte možnost vypnutí z výchozí hodnoty "Restart", "vypnout". Tento postup předpokládá, že procesu sysprep je spuštěné místně ve hostovaného operačního systému virtuálního počítače.
> Pokud chcete provést postup v případě virtuálních počítačů už běží v Azure, postupujte podle kroků popsaných v [v tomto článku](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Jak zachytit virtuální počítač s Linuxem, který se použije jako šablonu Resource Manageru][capture-image-linux-step-2-create-vm-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Převod virtuálních počítačů a virtuálních pevných disků mezi on-premises do Azure
Protože nahrávání imagí virtuálních počítačů a disků do Azure není možné prostřednictvím webu Azure portal, budete muset použít rutiny prostředí Azure PowerShell nebo rozhraní příkazového řádku. Další možností je použití nástroje "AzCopy". Nástroj můžete zkopírovat virtuální pevné disky mezi místními a Azure (v obou směrech). Je taky zkopírovat virtuální pevné disky mezi oblastmi Azure. Obraťte se prosím [této dokumentace] [ storage-use-azcopy] ke stažení a použití nástroje AzCopy.

Je třetí možnost použití různých orientované grafické uživatelské rozhraní nástroje třetích stran. Ale ujistěte se, že tyto nástroje jsou podporuje objekty BLOB stránky Azure. Pro naše účely, musíme použít úložiště objektů Blob stránky Azure (rozdíly jsou popsány zde: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Také nástroje poskytované systémem Azure jsou efektivní při kompresi virtuální počítače a virtuální pevné disky, které budete muset nahrát. To je důležité, protože této efektivity komprese snižuje čas odeslání (které se liší i přesto v závislosti na nahrávacího propojení k Internetu z místních zařízení a cílové oblasti Azure nasazení). Je veletrh předpokladu, že odesílání virtuálního počítače nebo virtuální pevný disk z Evropského umístění státech Azure dat centra bude trvat déle než nahrávání stejné virtuální počítače nebo virtuální pevné disky do Evropského Azure datová centra.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Odeslání VHD z místního úložiště do Azure
Nahrání virtuálního pevného disku nebo existujícího virtuálního počítače z místní sítě odpovídající virtuální počítač nebo virtuální pevný disk musí splňovat požadavky uvedené v kapitole [přípravu pro přesun virtuálního počítače z místního Azure s diskem zobecněný] [ planning-guide-5.2.1]tohoto dokumentu.

Virtuální počítač nemusí být zobecněn a se dají nahrát, do stavu a obrazec, který se má po vypnutí na straně místní. Totéž platí pro další virtuální pevné disky, které neobsahují slovo libovolný operační systém.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Nahrání virtuálního pevného disku a jeho Disk s Azure
V tomto případě chceme nahrání virtuálního pevného disku, s nebo bez operačního systému a připojit k virtuálnímu počítači jako datového disku nebo ho použít jako disk s operačním systémem. Toto je vícefázový proces

**Powershell**

* Přihlaste se k předplatnému pomocí *Connect-AzureRmAccount*
* Nastavení předplatného kontext s *Set-AzureRmContext* a parametr ID předplatného nebo SubscriptionName - naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Nahrání virtuálního pevného disku s *Add-AzureRmVhd* do účtu služby Azure Storage – naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Volitelné) Vytvoření spravovaného disku z virtuálního pevného disku s *New-AzureRmDisk* -naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk>
* Nastavte disk s operačním systémem novou konfiguraci virtuálního počítače na virtuální pevný disk nebo spravovaný Disk s *Set-AzureRmVMOSDisk* -naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
* Vytvořit nový virtuální počítač z konfigurace virtuálního počítače s *New-AzureRmVM* -naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>
* Přidání datového disku k novému virtuálnímu počítači přes *Add-AzureRmVMDataDisk* -naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk>

**Azure CLI 2.0**

* Přihlaste se k předplatnému pomocí *az login*
* Vyberte své předplatné s *set účet az--předplatného `<subscription name or id`>*
* Nahrání virtuálního pevného disku s *az storage blob nahrávání* – viz [pomocí Azure CLI s Azure Storage][storage-azure-cli]
* (Volitelné) Vytvoření spravovaného disku z virtuálního pevného disku s *az disk vytvořit* -naleznete v tématu https://docs.microsoft.com/cli/azure/disk#az_disk_create
* Vytvořit nový virtuální počítač zadáním nahraný virtuální pevný disk nebo spravovaný Disk jako disk s operačním systémem *az vm vytvořit* a parametr *– připojení disku operačního systému*
* Přidání datového disku k novému virtuálnímu počítači přes *az vm disk attach* a parametr *– nové*

**Šablona**

* Nahrání virtuálního pevného disku pomocí Powershellu nebo Azure CLI
* (Volitelné) Vytvoření spravovaného disku z VHD pomocí Powershellu, rozhraní příkazového řádku Azure nebo na webu Azure portal
* Nasazení virtuálního počítače pomocí šablony JSON odkazující na virtuální pevný disk, jak je znázorněno v [Tato ukázková šablona JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) nebo pomocí Managed Disks, jak je znázorněno v [Tato ukázková šablona JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Nasazení Image virtuálního počítače
K nahrání virtuálního pevného disku nebo existujícího virtuálního počítače z místní sítě, aby bylo možné ho použít jako image virtuálního počítače Azure virtuální počítač nebo virtuální pevný disk musí splňovat požadavky uvedené v kapitole [přípravy pro nasazení virtuálního počítače pomocí bitové kopie zákaznického pro SAP] [ planning-guide-5.2.2] tohoto dokumentu.

* Použití *sysprep* na Windows nebo *waagent-zrušení zřízení* v Linuxu generalizace virtuálního počítače – viz [technické informace o nástroji Sysprep](https://technet.microsoft.com/library/cc766049.aspx) pro Windows nebo [zachycení Virtuální počítač Linux použít jako šablonu Resource Manageru] [ capture-image-linux-step-2-create-vm-image] pro Linux
* Přihlaste se k předplatnému pomocí *Connect-AzureRmAccount*
* Nastavení předplatného kontext s *Set-AzureRmContext* a parametr ID předplatného nebo SubscriptionName - naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Nahrání virtuálního pevného disku s *Add-AzureRmVhd* do účtu služby Azure Storage – naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Volitelné) Vytvoření Image spravovaného disku z virtuálního pevného disku s *New-AzureRmImage* -naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage>
* Nastavit novou konfiguraci virtuálního počítače na disku operačního systému
  * Virtuální pevný disk pomocí *Set-AzureRmVMOSDisk - SourceImageUri - CreateOption fromImage* -naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
  * Spravované Image disku *Set-AzureRmVMSourceImage* -naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage>
* Vytvořit nový virtuální počítač z konfigurace virtuálního počítače s *New-AzureRmVM* -naleznete v tématu <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>

**Azure CLI 2.0**

* Použití *sysprep* na Windows nebo *waagent-zrušení zřízení* v Linuxu generalizace virtuálního počítače – viz [technické informace o nástroji Sysprep](https://technet.microsoft.com/library/cc766049.aspx) pro Windows nebo [zachycení Virtuální počítač Linux použít jako šablonu Resource Manageru] [ capture-image-linux-step-2-create-vm-image] pro Linux
* Přihlaste se k předplatnému pomocí *az login*
* Vyberte své předplatné s *set účet az--předplatného `<subscription name or id`>*
* Nahrání virtuálního pevného disku s *az storage blob nahrávání* – viz [pomocí Azure CLI s Azure Storage][storage-azure-cli]
* (Volitelné) Vytvoření Image spravovaného disku z virtuálního pevného disku s *az image vytvořit* -naleznete v tématu https://docs.microsoft.com/cli/azure/image#az_image_create
* Vytvořit nový virtuální počítač zadáním nahraných virtuálního pevného disku nebo Image spravovaného disku jako disku s operačním systémem s *az vm vytvořit* a parametr *– obrázek*

**Šablona**

* Použití *sysprep* na Windows nebo *waagent-zrušení zřízení* v Linuxu generalizace virtuálního počítače – viz [technické informace o nástroji Sysprep](https://technet.microsoft.com/library/cc766049.aspx) pro Windows nebo [zachycení Virtuální počítač Linux použít jako šablonu Resource Manageru] [ capture-image-linux-step-2-create-vm-image] pro Linux
* Nahrání virtuálního pevného disku pomocí Powershellu nebo Azure CLI
* (Volitelné) Vytvoření Image spravovaného disku ze souboru VHD pomocí Powershellu, rozhraní příkazového řádku Azure nebo na webu Azure portal
* Nasazení virtuálního počítače pomocí šablony JSON odkazující na image virtuálního pevného disku, jak je znázorněno v [Tato ukázková šablona JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) nebo pomocí Image spravovaného disku, jak je znázorněno v [Tato ukázková šablona JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Stahování virtuální pevné disky nebo spravované disky na on-premises
Infrastruktura jako služba Azure není jednosměrné ulici, kde pouze nebudou moct nahrát virtuální pevné disky a SAP systémy. Můžete přesunout SAP systémy z Azure zpět do místního prostředí také.

Během doby stahování nemůže být aktivních virtuálních pevných disků na Managed Disks. I v případě stahování disky, které jsou připojené k virtuálním počítačům, virtuální počítač je potřeba vypnout a uvolnit. Pokud chcete stáhnout obsah databáze, který potom slouží k nastavení nového systému místní a pokud je to přijatelné být funkční, která během doby stahování a instalace nového systému, který se můžete stále systému v Azure , může dlouho výpadky provedením zálohy komprimované databáze na disk a stáhnout pouze tento disk místo také stažení virtuálního počítače pro základní operační systém.

#### <a name="powershell"></a>PowerShell

  * Stahování spravovaného disku  
  Nejdřív je potřeba získat přístup k podkladové blob spravovaného disku. Pak můžete zkopírovat základní objekt blob na nový účet úložiště a stáhnout objekt blob z tohoto účtu úložiště.

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

  * Stažení virtuálního pevného disku  
  Po zastavení systému SAP a virtuální počítač je vypnutý, můžete použít rutinu Powershellu Save-AzureRmVhd na cíli v místním stáhnout virtuální pevný disk disky zpátky do místního prostředí. Aby bylo možné provést, budete potřebovat adresu URL virtuálního pevného disku, které můžete vyhledat v funkcí "storage část" na webu Azure portal (třeba přejděte do účtu úložiště a kontejner úložiště, kde byl vytvořen virtuální pevný disk) a je potřeba vědět, kam zkopírovat virtuální pevný disk.

  Pak můžete využít příkaz tak, že definujete parametr SourceUri jako adresu URL virtuálního pevného disku ke stažení a LocalFilePath jako fyzické umístění virtuálního pevného disku (včetně názvu). Tento příkaz může vypadat třeba:

  ```powerhell
  Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Další podrobnosti o rutina Save-AzureRmVhd tady <https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvhd>.

#### <a name="cli-20"></a>CLI 2.0
  * Stahování spravovaného disku  
  Nejdřív je potřeba získat přístup k podkladové blob spravovaného disku. Pak můžete zkopírovat základní objekt blob na nový účet úložiště a stáhnout objekt blob z tohoto účtu úložiště.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

  * Stažení virtuálního pevného disku   
  Po zastavení systému SAP a virtuální počítač je vypnutý, můžete pomocí příkazu Azure CLI _služby azure storage blob download_ na místní cíl, který chcete stáhnout virtuální pevný disk disky zpátky do místního prostředí. Aby bylo možné provést, je třeba název a kontejner virtuálního pevného disku, který najdete v části úložiště na webu Azure Portal (třeba přejděte do účtu úložiště a kontejner úložiště, kde byl vytvořen virtuální pevný disk) a potřebujete vědět, kde virtuální pevný disk by měl být aplikace FxCop IE k.

  Pak můžete využít příkaz tak, že definujete parametry objektů blob a kontejnerů virtuálního pevného disku pro stahování a cílové jako fyzické cílové umístění virtuálního pevného disku (včetně názvu). Tento příkaz může vypadat třeba:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Převod virtuálních počítačů a disků v Azure
#### <a name="copying-sap-systems-within-azure"></a>Kopírování systémů SAP v Azure
Systém SAP nebo dokonce vyhrazený server DBMS podpůrné aplikační vrstvě SAP bude pravděpodobně skládat z několika disků, které budou obsahovat buď operačního systému se binární soubory nebo soubory protokolu a data z databáze SAP. Funkce Azure kopírování disků ani Azure funkce pro ukládání disků na místní disk nemá synchronizační mechanismus, který snímky několik disků s konzistentním způsobem. Proto stav zkopírovaný nebo uložené disků i v případě, že ty jsou připojené na stejném virtuálním počítači by lišit. To znamená, že v konkrétní případ s různými daty a logfile(s) obsažené v různých discích, databáze v konečném by byly nekonzistentní.

**Uzavření: Aby bylo možné kopírovat nebo uložit disky, které jsou součástí konfigurace systému SAP budete muset zastavit systému SAP a také muset vypnout virtuální počítač nasazený. Teprve pak můžete zkopírovat nebo stáhnout sadu disků buď vytvořit kopii systému SAP v Azure nebo místně.**

Datové disky mohou být uloženy jako soubory VHD v účtu úložiště Azure může být přímo připojená k virtuálnímu počítači a použít jako obrázek. V tomto případě virtuální pevný disk je zkopírován do jiného umístění než právě připojené k virtuálnímu počítači. Celý název souboru VHD v Azure musí být jedinečný v rámci Azure. Jak už bylo zmíněno dříve již, název je druh trojdílného názvu, bude vypadat takto:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Datové disky můžou být také Managed Disks. V takovém případě spravovaného disku se používá k vytvoření nového spravovaného disku před přímého připojení k virtuálnímu počítači. Název spravovaného disku musí být jedinečný v rámci skupiny prostředků.

##### <a name="powershell"></a>PowerShell
Rutiny prostředí Azure PowerShell můžete použít ke kopírování virtuálního pevného disku, jak je znázorněno v [v tomto článku][storage-powershell-guide-full-copy-vhd]. K vytvoření nového spravovaného disku, pomocí New-AzureRmDiskConfig a New-AzureRmDisk, jak je znázorněno v následujícím příkladu.

```powershell
$config = New-AzureRmDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzureRmDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="cli-20"></a>CLI 2.0
Rozhraní příkazového řádku Azure můžete použít ke zkopírování virtuálního pevného disku, jak je znázorněno v [v tomto článku][storage-azure-cli-copy-blobs]. Chcete-li vytvořit nového spravovaného disku, použijte *az disk vytvořit* jak je znázorněno v následujícím příkladu.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Nástroje pro Azure Storage
* <http://storageexplorer.com/>

Profesionální edicí průzkumníky úložiště Azure najdete tady:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

Kopírování samotného virtuálního pevného disku v rámci účtu úložiště je proces, který trvá jenom pár sekund (podobně jako na hardware sítě SAN vytváření snímků pomocí opožděné a kopii při zápisu). Až budete mít kopii souboru virtuálního pevného disku, můžete připojit k virtuálnímu počítači nebo použít jako bitové kopie virtuální pevný disk připojit k virtuálním počítačům.

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

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Kopírování disků mezi účty Azure Storage
Tuto úlohu nelze provést na portálu Azure portal. Můžete použít rutiny Azure Powershellu, rozhraní příkazového řádku Azure nebo prohlížeče třetích stran úložiště. Rutiny prostředí PowerShell nebo příkazů rozhraní příkazového řádku můžete vytvářet a spravovat objekty BLOB, mezi které patří schopnost asynchronně kopírovat objekty BLOB mezi účty úložiště a oblastmi v rámci předplatného Azure.

##### <a name="powershell"></a>PowerShell
Můžete také zkopírovat virtuální pevné disky mezi předplatnými. Další informace najdete v článku [v tomto článku][storage-powershell-guide-full-copy-vhd].

Základní tok logiky rutiny PS vypadá takto:

* Vytvořte kontext účtu úložiště pro **zdroj** účtu úložiště pomocí *New-AzureStorageContext* -naleznete v tématu <https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext>
* Vytvořte kontext účtu úložiště pro **cílové** účtu úložiště pomocí *New-AzureStorageContext* -naleznete v tématu <https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext>
* Začít kopírovat s

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Zkontrolujte stav kopírování ve smyčce s

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* K virtuálnímu počítači připojte nový virtuální pevný disk, jak je popsáno výše.

Příklady naleznete v části [v tomto článku][storage-powershell-guide-full-copy-vhd].

##### <a name="cli-20"></a>CLI 2.0
* Začít kopírovat s

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Zkontrolovat stav, pokud je stále ve smyčce se kopie

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* K virtuálnímu počítači připojte nový virtuální pevný disk, jak je popsáno výše.

Příklady naleznete v části [v tomto článku][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Zpracování disku
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Virtuální počítač nebo disk strukturu pro nasazení SAP
Zpracování struktury virtuálního počítače a přidružené disky v ideálním případě by měl být jednoduché. V místní instalace vyvinuté zákazníci mnoho způsobů strukturování instalace serveru.

* Jeden základní disk, který obsahuje operační systém a všechny binární soubory DBMS a/nebo SAP. Od března 2015 může být tento disk velikosti místo starší omezení, které omezený na 127 GB až 1TB.
* Disky jeden nebo více, které obsahuje DBMS soubor protokolu databáze SAP a soubor protokolu o DBMS dočasného úložiště (Pokud je systém DBMS podporuje). Pokud jsou vysoké požadavky na vstupně-výstupních operací protokolu databáze, budete muset prokládanou více disků, abyste dosáhli potřebný objem vstupně-výstupních operací.
* Počet disků (pokud správce databáze podporuje to) obsahující jeden nebo dva soubory databáze z databáze SAP a také soubory dočasná data DBMS.

![Odkaz na konfiguraci virtuálního počítače Azure IaaS pro SAP][planning-guide-figure-1300]


- - -
> ![Windows][Logo_Windows] Windows
>
> U mnoha zákazníků jsme viděli konfigurace, například SAP a DBMS binární soubory nebyly nainstalovanou na jednotce c:\, kam se nainstaloval operační systém. Existují různé důvody pro to, ale když přejdeme zpět do kořenového adresáře, obvykle byla, že byly malé jednotky a upgrady operačního systému nutné další místo 10 až 15 lety. Obě podmínky těchto dnů příliš často už neplatí. Dnes jednotky c:\ lze mapovat na velké objemy disků nebo virtuálních počítačů. Aby bylo možné zjednodušení nasazení v jejich strukturu, doporučuje se odpovídat následujícímu vzoru nasazení systémů SAP NetWeaver v Azure
>
> Stránkovací soubor operačního systému Windows musí být na jednotce D: (dočasné disk)
>
> ![Linux][Logo_Linux] Linux
>
> Umístit Linux stránkovacího souboru v rámci /mnt/mnt/prostředků v Linuxu, jak je popsáno v [v tomto článku][virtual-machines-linux-agent-user-guide]. Odkládací soubor lze nastavit v konfiguračním souboru /etc/waagent.conf agenta pro Linux. Přidat nebo změnit následující nastavení:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Pokud chcete aktivovat změny, bude nutné restartovat agenta pro Linux s

```
sudo service waagent restart
```

Přečtěte si prosím Poznámka SAP [1597355] podrobné informace o velikosti doporučené odkládacího souboru

- - -
Počet disků pro DBMS datové soubory a typ jsou tyto disky hostované na Azure Storage byste měli určit podle požadavků na vstupně-výstupních operací a latence vyžaduje. Přesné kvóty jsou popsané v [(Linux) v tomto článku] [ virtual-machines-sizes-linux] a [v tomto článku (Windows)][virtual-machines-sizes-windows].

Prostředí pro nasazení SAP za poslední dva roky nám museli některé lekcí, které lze shrnout jako:

* Provoz vstupně-výstupních operací na různé datové soubory není vždy stejný od stávajících zákazníků systémů může mít jinak velikost datových souborů představující jejich databáze SAP. V důsledku se ukázalo lepší používat konfiguraci RAID přes víc disků k umístit datové soubory, které logické jednotky LUN carved mimo těch. Došlo k situacím, zejména s Azure Standard Storage, kde objemem vstupně-výstupních operací dosažení kvóty jednoho disku pro protokol transakcí DBMS. V takových případech se doporučuje použití služby Premium Storage nebo případně agregaci více úložiště úrovně Standard prokládanou disků pomocí softwaru.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Konfigurace softwarového pole RAID v Linuxu][virtual-machines-linux-configure-raid]
> * [Konfigurace LVM na virtuální počítač s Linuxem v Azure][virtual-machines-linux-configure-lvm]
> * [Tajné klíče Azure Storage a optimalizace Linux vstupně-výstupních operací](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* Premium Storage je zobrazují se významné lepší výkon, hlavně pro zápis protokolů kritické transakce. Pro SAP scénáře, které se očekává poskytování výkonu se podobají produkčním důrazně doporučujeme použít virtuální počítač řady, která můžete využít Azure Premium Storage.

Mějte na paměti, disku, který obsahuje operační systém, a doporučujeme, binární soubory SAP a databázi (základní virtuální počítač), už není omezený na 127GB. Velikost nyní může mít až 1TB. To by měl být dostatek místa pro zachovat všechny nezbytné souboru, včetně například SAP protokoly úloh služby batch.

Pro další návrhy a další podrobnosti, konkrétně pro virtuální počítače DBMS, najdete [nasazení DBMS][dbms-guide]

#### <a name="disk-handling"></a>Zpracování disku
Ve většině případů je potřeba vytvořit další disky za účelem nasazení SAP databáze k virtuálnímu počítači. Už jsme mluvili o aspektech na počet disků v kapitole [struktura/disku virtuálního počítače pro nasazení SAP] [ planning-guide-5.5.1] tohoto dokumentu. Na webu Azure portal umožňuje připojení a odpojení disků po nasazení základního virtuálního počítače. Připojit/odpojit, když virtuální počítač je zapnutý a běží i když je zastavena, může být disky. Při připojování disku, na webu Azure portal nabízí připojte prázdný disk nebo stávající disk, který v tomto okamžiku není připojený k jinému virtuálnímu počítači.

**Poznámka:**: disky můžete připojit pouze k jednomu virtuálnímu počítači v daném okamžiku.

![Připojení / odpojení disků s Azure Storage úrovně Standard][planning-guide-figure-1400]

Během nasazení nového virtuálního počítače můžete rozhodnout, jestli chcete používat službu Managed Disks nebo umístěte disky v účtech úložiště Azure. Pokud chcete používat Premium Storage, doporučujeme použít Managed Disks.

Dále musíte rozhodnout, jestli chcete vytvořit nový a prázdný disk nebo jestli chcete vybrat stávající disk, který byl dříve odeslán a by měl být připojen k virtuálnímu počítači teď.

**Důležité**: můžete **neměňte** chcete používat ukládání do mezipaměti hostitele s Azure Storage úrovně Standard. Preference mezipaměti hostitele by měl ponechte výchozí hodnotu NONE. S Azure Storage úrovně Premium byste měli povolit ukládání do mezipaměti pro čtení je-li vlastnost vstupně-výstupních operací je většinou pro čtení, stejně jako typický vstupně-výstupní operace proti datové soubory databáze. V případě soubor protokolu transakcí databáze se doporučuje neexistující ukládání do mezipaměti.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Postup připojení datového disku na webu Azure Portal][virtual-machines-linux-attach-disk-portal]
>
> Pokud jsou připojené disky, budete muset přihlásit k virtuálnímu počítači otevřete Správce disků Windows. Není-li automatického připojení podle doporučení v kapitole [nastavení automatického připojení pro připojené disky][planning-guide-5.5.3], nově připojený svazek je třeba provést online a inicializované.
>
> ![Linux][Logo_Linux] Linux
>
> Pokud jsou připojené disky, musíte se přihlásit k virtuálnímu počítači a inicializovat disky, jak je popsáno v [v tomto článku][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

- - -
Pokud nový disk se prázdný disk, budete muset také a disk naformátujte. Pro formátování, zejména pro DBMS dat a souborů protokolu stejné doporučení pro nasazení DBMS úplné obnovení systému použije.

Jak již bylo uvedeno v kapitole [The koncept virtuálního počítače Microsoft Azure][planning-guide-3.2], účet služby Azure Storage neposkytuje nekonečné prostředky v rámci svazku, vstupně-výstupních operací a objem dat vstupně-výstupních operací. Obvykle DBMS virtuální počítače jsou největší vliv. Může být nejvhodnější použít samostatný účet úložiště pro každý virtuální počítač, pokud máte několik vysoké objemy vstupně-výstupní operace virtuálních počítačů určených k nasazení, abyste mohli zůstat v rámci limitu objemu účet služby Azure Storage. V opačném případě budete muset naleznete v tématu Jak vyrovnávat tyto virtuální počítače mezi různými účty úložiště bez dosažení limitu každý jeden účet úložiště. Další podrobnosti jsou popsány v [nasazení DBMS][dbms-guide]. Tato omezení je třeba také zachovat v paměti pro virtuální počítače serverem nebo jiným virtuálním počítačům, které mohou vyžadovat další virtuální pevné disky čistě aplikace SAP. Tato omezení neplatí, pokud použijete spravovaný Disk. Pokud chcete používat Premium Storage, doporučujeme použít Managed Disks.

Jiné téma, jež jsou relevantní pro účty úložiště je virtuálních pevných disků v účtu úložiště, jestli se zobrazuje, geograficky replikovaný. Geografická replikace povolit nebo zakázat na úrovni účtu úložiště a ne na úrovni virtuálního počítače. Pokud je povolené geografickou replikaci, virtuálních pevných disků v účtu úložiště, by replikují do jiného datového centra Azure v rámci stejné oblasti. Než se rozhodnete na to, byste uvažovat o následující omezení:

Azure geografické replikace funguje místně na každý virtuální pevný disk ve virtuálním počítači a nereplikuje napříč více virtuálních pevných disků ve virtuálním počítači s Iosem v chronologickém pořadí. Proto se replikují virtuální pevný disk, který představuje základní virtuální počítač, stejně jako jakékoli další virtuální pevné disky připojené k virtuálnímu počítači nezávisle na sobě navzájem. To znamená, že není žádná synchronizace mezi změnami v různých virtuálních pevných disků. Fakt, že bez ohledu na jejich pořadí, ve kterém jsou zapsány znamená, že se replikují IOs geografická replikace není hodnota pro databázové servery, které mají své databáze distribuovat napříč více virtuálních pevných disků. Kromě databázového systému může existovat i další aplikace, kde procesy zápisu nebo manipulaci s daty v různých virtuálních pevných disků a kde je důležité udržovat pořadí změny. Pokud se jedná o požadavek, by se neměla povolovat geografické replikace do Azure. Závisí na tom, jestli potřebujete nebo chcete geografickou replikaci pro sadu virtuálních počítačů, ale ne pro jiné sady, lze již zařadit virtuálních počítačů a jejich související virtuální pevné disky do různých účtů úložiště, které mají geografická replikace povolená nebo zakázaná.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Nastavení automatického připojení pro připojené disky
- - -
> ![Windows][Logo_Windows] Windows
>
> Pro virtuální počítače, které jsou vytvořené z vlastních Imagí nebo disky, je potřeba zkontrolovat a případně nastavte parametr automatického připojení. Nastavení tohoto parametru vám umožní virtuální počítač po restartování nebo opětovné nasazení v Azure a připojené/připojených jednotek automaticky znovu připojit.
> Parametr je nastaven pro bitových kopií poskytnutých microsoftem na webu Azure Marketplace.
>
> Aby bylo možné nastavit automatického připojení, přečtěte si dokumentaci k příkazového řádku spustitelného souboru diskpart.exe tady:
>
> * [Možnosti příkazového řádku DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automatického připojení](http://technet.microsoft.com/library/cc753703.aspx)
>
> V okně příkazového řádku Windows by měla být otevřené s oprávněními správce.
>
> Pokud jsou připojené disky, budete muset přihlásit k virtuálnímu počítači otevřete Správce disků Windows. Není-li automatického připojení podle doporučení v kapitole [nastavení automatického připojení pro připojené disky][planning-guide-5.5.3], nově připojený svazek > je třeba provést online a inicializované.
>
> ![Linux][Logo_Linux] Linux
>
> Je nutné inicializovat nově připojené prázdný disk, jak je popsáno v [v tomto článku][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Musíte také přidat nové disky, které /etc/fstab.
>
>

- - -
### <a name="final-deployment"></a>Poslední nasazení
Poslední nasazení a přesné kroky, zejména s ohledem na nasazení SAP rozšířené monitorování, najdete [Průvodce nasazením][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Přístup k SAP systémy s operačním systémem v rámci virtuálních počítačů Azure
Pro výhradně Cloudovou scénáře můžete chtít připojit k těmto systémům SAP přes veřejný internet pomocí grafického uživatelského rozhraní SAP. Pro tyto případy je potřeba použít následující postupy.

Později v dokumentu se budeme zabývat další hlavní scénáře, připojování k systémům SAP v nasazení, mezi různými místy, která mají připojení site-to-site (tunel VPN) nebo Azure ExpressRoute připojení mezi místními systémy a systémy pro Azure.

### <a name="remote-access-to-sap-systems"></a>Vzdálený přístup k systémům SAP
Pomocí Azure Resource Manageru nejsou výchozí koncové body už stejně jako v původní klasického modelu. Všechny porty virtuální počítač Azure Resource Manageru jsou otevřené tak dlouho, dokud:

1. Pro podsíť nebo síťové rozhraní není definována žádná skupina zabezpečení sítě. Síťový provoz do virtuálních počítačů Azure můžete zabezpečit prostřednictvím takzvané "skupiny zabezpečení sítě". Další informace najdete v tématu [co je skupina zabezpečení sítě (NSG)?][virtual-networks-nsg]
2. Žádná služba Vyrovnávání zatížení Azure je definována pro síťové rozhraní   

Zobrazit architektury rozdíl mezi modelu classic a ARM, jak je popsáno v [v tomto článku][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Konfigurace připojení k systému SAP a SAP grafického uživatelského rozhraní pro čistě cloudové scénáře
Podrobnosti najdete v článku, který popisuje podrobnosti k tomuto tématu: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Změna nastavení brány Firewall na virtuálním počítači
Může být potřeba nakonfigurovat bránu firewall na virtuálních počítačích povolit příchozí přenosy do systému SAP.

- - -
> ![Windows][Logo_Windows] Windows
>
> Bránu Windows Firewall v rámci virtuálního počítače Azure nasazené je ve výchozím nastavení zapnuté. Teď je potřeba povolit SAP Port, který chcete otevřít, jinak nebudou moci připojit grafickým uživatelským rozhraním SAP.
> Použijte následující postup:
>
> * Otevřete ovládací panely\systém a zabezpečení\Brána Windows Firewall na **upřesňující nastavení**.
> * Teď klikněte pravým tlačítkem na příchozí pravidla a zvolili **nové pravidlo**.
> * V následujícím Průvodci se rozhodli vytvořit nový **Port** pravidlo.
> * V dalším kroku v Průvodci nechte nastavení na TCP a zadejte číslo portu, který chcete otevřít. Protože je naše ID instance SAP 00, nám trvalo 3200. Pokud vaše instance má jinou instanci číslo, by měla otevřít port, který jste definovali dříve podle čísla instance.
> * V další části Průvodce je potřeba ponechat položku **povolit připojení** zaškrtnuto.
> * V dalším kroku průvodce budete muset definovat, jestli se pravidlo vztahuje k doméně, privátní a veřejné sítě. V případě potřeby pro vaše potřeby ho upravte. Ale připojení s grafickým uživatelským rozhraním SAP z vnějšku přes veřejnou síť, musí být pravidlo použito ve veřejné síti.
> * V posledním kroku průvodce název pravidla a uložte stisknutím kombinace kláves **Dokončit**.
>
> Pravidlo začne platit okamžitě.
>
> ![Definice pravidla portů][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Linuxové Image v Tržišti Azure Marketplace nepovolujte iptables brány firewall ve výchozím nastavení a připojení k systému SAP by mělo fungovat. Pokud jste povolili iptables nebo jinou bránu firewall, najdete v dokumentaci iptables nebo použité brány firewall pro povolení provozu příchozího portu tcp na portu 32xx (kde xx je počet systému systému SAP).
>
>

- - -
#### <a name="security-recommendations"></a>Doporučení zabezpečení
Grafické uživatelské rozhraní SAP nepřipojuje k některému z instance SAP (port 32xx), které jsou spuštěné, ale nejdřív se připojuje přes port otevřen k procesu serveru zpráv SAP (port 36xx) okamžitě. V minulosti použil stejný port serveru zpráv pro interní komunikaci s instancí aplikace. Aby se zabránilo aplikačních serverů s místními od nedopatřením komunikaci se serverem zprávy v Azure, je možné změnit interní komunikační porty. Důrazně doporučujeme změnit interní komunikace mezi server zpráv SAP a její instance aplikace na jiné číslo portu v systémech, které byly naklonovali z místních systémů, jako je klonování vývoje pro projekt testování atd. To můžete udělat s výchozím parametrem profilu:

> rdisp/msserv_internal
>
>

jak je uvedeno v [nastavení zabezpečení pro Server zpráv SAP ](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Koncepty cloudového nasazení instancí SAP
### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Jeden virtuální počítač se SAP Netweaverem ukázku/trénovací scénář
![Systémy jeden virtuální počítač SAP ukázka se stejnými názvy virtuálních počítačů, izolovaný v Azure Cloud Services][planning-guide-figure-1700]

V tomto scénáři (viz kapitola [výhradně Cloudový] [ planning-guide-2.1] tohoto dokumentu) jsme implementujete scénář systému typické školení/ukázku ve kterém je obsažen kompletní školení/ukázkové scénáře v jednom virtuálním počítači. Předpokládáme, že nasazení se provádí prostřednictvím image šablony virtuálních počítačů. Také předpokládáme této více tyto ukázky a školení virtuální počítače vyžadují k nasazení s virtuálními počítači, který má stejný název.

Předpokladem je, že jste vytvořili Image virtuálního počítače, jak je popsáno v některé části kapitoly [Příprava virtuálních počítačů s řešením SAP pro Azure] [ planning-guide-5.2] v tomto dokumentu.

Posloupnost událostí k implementaci scénáře vypadá takto:

##### <a name="powershell"></a>PowerShell
* Vytvořit novou skupinu prostředků pro každý školení/ukázku na šířku

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```
* Vytvořit nový účet úložiště, pokud nechcete používat službu Managed Disks

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Vytvořte novou virtuální síť pro každou školení/ukázku na šířku umožňující použití stejného názvu hostitele a IP adres. Virtuální síť je chráněný skupinou zabezpečení sítě, která umožňuje pouze provoz na portu 3389 pro povolení přístupu ke vzdálené ploše a port 22 pro SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Vytvoření nové veřejné IP adresy, který slouží pro přístup k virtuálnímu počítači z Internetu

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Vytvořit nové síťové rozhraní virtuálního počítače

```powershell
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Vytvoří virtuální počítač. Pro výhradně Cloudovou scénář bude mít každý virtuální počítač se stejným názvem. Identifikátor SID SAP instancí SAP NetWeaver v těchto virtuálních počítačů budou stejné i. V rámci skupiny prostředků Azure musí být jedinečný název virtuálního počítače, ale v různých skupinách prostředků Azure můžete spouštět virtuální počítače se stejným názvem. Výchozí účet "Administrator" Windows nebo "root" pro Linux nejsou platné. Nové uživatelské jméno správce proto musí být definován společně heslem. Velikost virtuálního počítače taky musí být definován.

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

* Volitelně můžete přidat další disky a nezbytné obsah obnovit. Všechny názvy objektů blob (adresy URL do objektů BLOB) musí být jedinečný v rámci Azure.

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
Následující příklad kódu je možné v Linuxu. Pro Windows, buď pomocí prostředí PowerShell, jak je popsáno výše, nebo úprava příkladu rgName % nahrazujícím $rgName a nastavte proměnnou prostředí pomocí příkazu Windows *nastavit*.

* Vytvořit novou skupinu prostředků pro každý školení/ukázku na šířku

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Vytvoření nového účtu úložiště

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Vytvořte novou virtuální síť pro každou školení/ukázku na šířku umožňující použití stejného názvu hostitele a IP adres. Virtuální síť je chráněný skupinou zabezpečení sítě, která umožňuje pouze provoz na portu 3389 pro povolení přístupu ke vzdálené ploše a port 22 pro SSH.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Vytvoření nové veřejné IP adresy, který slouží pro přístup k virtuálnímu počítači z Internetu

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Vytvořit nové síťové rozhraní virtuálního počítače

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Vytvoří virtuální počítač. Pro výhradně Cloudovou scénář bude mít každý virtuální počítač se stejným názvem. Identifikátor SID SAP instancí SAP NetWeaver v těchto virtuálních počítačů budou stejné i. V rámci skupiny prostředků Azure musí být jedinečný název virtuálního počítače, ale v různých skupinách prostředků Azure můžete spouštět virtuální počítače se stejným názvem. Výchozí účet "Administrator" Windows nebo "root" pro Linux nejsou platné. Nové uživatelské jméno správce proto musí být definován společně heslem. Velikost virtuálního počítače taky musí být definován.

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

* Volitelně můžete přidat další disky a nezbytné obsah obnovit. Všechny názvy objektů blob (adresy URL do objektů BLOB) musí být jedinečný v rámci Azure.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Šablona
Ukázkové šablony můžete použít v úložišti šablon azure quickstart na githubu.

* [Jednoduchý virtuální počítač s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Jednoduché Windows virtuální počítač](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Virtuální počítač z bitové kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementovat sadu virtuálních počítačů, které komunikují v rámci Azure
Tento scénář jenom cloudu je typický scénář pro trénování a ukázka účely software představující ukázky a školení, kde scénář je rozdělené do několika virtuálních počítačů. Různé součásti nainstalované v jiných virtuálních počítačů se muset vzájemně komunikovat. Znovu v tomto scénáři nemáte žádnou místní síťovou komunikaci nebo mezi různými místy scénáři je potřeba.

Tento scénář je rozšířením instalace je popsáno v kapitole [jeden virtuální počítač se SAP Netweaverem ukázku/trénovací scénář] [ planning-guide-7.1] tohoto dokumentu. V tomto případě další virtuální počítače se přidají do existující skupiny prostředků. V následujícím příkladu školení na šířku se skládá z SAP ASCS/SCS Virtuálním počítači, virtuálního počítače s systémem DBMS a aplikační Server SAP instance virtuálního počítače.

Před sestavením tohoto scénáře musíte přemýšlet o základní nastavení, které jsou už ve scénáři před uplatnit.

#### <a name="resource-group-and-virtual-machine-naming"></a>Pojmenování virtuálního počítače a skupiny prostředků
Všechny názvy skupin prostředků musí být jedinečný. Vyvíjejte vlastní schéma pojmenování prostředků, jako například `<rg-name`>-příponu.

Název virtuálního počítače musí být jedinečný v rámci skupiny prostředků.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Nastavení sítě pro komunikaci mezi jiných virtuálních počítačů.
![Sada virtuálních počítačů v rámci virtuální sítě Azure][planning-guide-figure-1900]

Abyste zabránili kolizím s klony ze stejné prostředí školení/ukázku, budete muset vytvořit virtuální síť Azure pro každé prostředí. Překlad názvů DNS, poskytneme vám Azure nebo můžete vytvořit svůj vlastní server DNS mimo Azure (nechcete být dále zde popsané). V tomto scénáři jsme nenakonfigurujete vlastní DNS. Pro všechny virtuální počítače v rámci jedné virtuální sítě Azure se povolí komunikaci s použitím názvy hostitelů.

Důvody k oddělení školení nebo ukázkové prostředí tak, že virtuální sítě a ne jenom skupiny prostředků může být:

* Prostředí SAP dle nastavení potřebuje svůj vlastní AD/OpenLDAP a Server domény musí být součástí každého krajina.  
* Prostředí SAP dle nastavení obsahuje součásti, které potřebujete pro práci s pevné IP adresy.

Další podrobnosti o Azure Virtual Networks a tom, jak definovat je nacházely v [v tomto článku][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Nasazení SAP virtuálních počítačů s připojením k podnikové síti (mezi různými místy)
Spuštění prostředí SAP a dělit nasazení mezi úplné obnovení pro servery se špičkovou DBMS, místních virtualizovaných prostředí pro aplikace a nižší úroveň 2 konfiguraci systémů SAP a Azure IaaS. Základní předpokladem je, že potřebují komunikovat mezi sebou a s mnoha dalšími součástmi softwaru nasazené ve společnosti, nezávisle na jejich nasazení formuláře systémů SAP v rámci jedné prostředí SAP. Také by měl existovat žádné rozdíly zavedené ve formuláři nasazení pro propojení s grafickým uživatelským rozhraním SAP nebo jiných rozhraní koncového uživatele. Tyto podmínky mohou být splněny, pouze když máme v místním Active Directory/OpenLDAP a rozšířená tak, aby systémy pro Azure prostřednictvím připojení site na site/více-místě nebo privátní připojení, jako jsou Azure ExpressRoute služby DNS.

Pokud chcete získat další informace o podrobnosti implementace SAP v Azure, doporučujeme přečíst kapitolu [Cloud-Only koncepty nasazení instancí SAP] [ planning-guide-7] tohoto dokumentu, který vysvětluje některé konstrukce základy Azure a jak tyto mají být použity s aplikacemi SAP v Azure.

### <a name="scenario-of-an-sap-landscape"></a>Scénář prostředí SAP
Scénář mezi různými místy mohou zhruba popsány jako ve níže grafiky:

![Připojení Site-to-Site mezi místními a prostředky Azure][planning-guide-figure-2100]

Scénáře uvedené výše popisuje scénář kde místní AD/OpenLDAP a DNS se rozšíří do Azure. Na straně místní je vyhrazený určitý rozsah IP adres na jedno předplatné Azure. Rozsah IP adres se přiřadí ke službě Azure Virtual Network na straně Azure.

#### <a name="security-considerations"></a>Aspekty zabezpečení
Požadavek na minimální je použití zabezpečený komunikační protokoly, například SSL/TLS pro přístup z prohlížeče nebo připojení VPN pro systém přístup ke službám Azure. Předpokladem je, že společnosti zpracovat připojení VPN mezi jejich podnikovou sítí a Azure odlišně. Některé společnosti můžou otevřít blankly všechny porty. Některé společnosti může být vhodné abychom byli přesní v porty, které potřebují k otevření atd.

V typické SAP v následující tabulce jsou uvedeny portů pro komunikaci. V podstatě stačí otevřít port brány SAP.

| Služba | Název portu | Příklad `<nn`> = 01 | Výchozí rozsah (maximálních) | Poznámka |
| --- | --- | --- | --- | --- |
| Dispečer |sapdp`<nn>` najdete v článku * |3201 |3200 - 3299 |Dispečer SAP, SAP grafického uživatelského rozhraní pro Windows a Java |
| Server zpráv |sapms`<sid`> Zobrazit ** |3600 |bezplatné sapms`<anySID`> |identifikátor SID = ID systému SAP |
| brána |sapgw`<nn`> Zobrazit * |3301 |free |Brány SAP, použít pro komunikaci CPIC a v dokumentu RFC |
| Směrovač SAP |sapdp99 |3299 |free |Pouze názvy položek konfigurace (centrální instance) služby lze přiřadit v /etc/services na libovolnou hodnotu po dokončení instalace. |

*) nn = číslo Instance SAP

*) sid = ID systému SAP

Podrobnější informace o tom porty vyžadované pro různé produkty SAP nebo služby v jednotlivých produktů SAP najdete tady <http://scn.sap.com/docs/DOC-17124>.
S tímto dokumentem byste měli moct otevřít vyhrazené porty v zařízení VPN pro konkrétní produktů SAP a scénářů.

Další bezpečnostní opatření při nasazování virtuálních počítačů v takové situaci může být k vytvoření [skupinu zabezpečení sítě] [ virtual-networks-nsg] definovat pravidla přístupu.

### <a name="dealing-with-different-virtual-machine-series"></a>Práce s různými řadami virtuálních počítačů
Společnost Microsoft přidala mnoho dalších typů virtuálních počítačů, které se liší v počtu virtuálních procesorů, paměť nebo důležitější na hardwaru, je spuštěn na. Ne všechny tyto virtuální počítače jsou podporované s řešením SAP (viz podporované typy virtuálních počítačů v Poznámka SAP [1928533]). Některé z těchto virtuálních počítačů spustit na jiného hostitele generacemi hardwaru. Tyto generace hardwarových hostitele získávání nasazených v členitosti ze Azure – jednotka škálování. Mohou nastat případy, kdy nelze spustit různé typy virtuálních počítačů, kterou jste zvolili na stejné jednotce škálování. Skupina dostupnosti má zobrazení omezenou schopnost span jednotek škálování na základě různých hardwaru.  Například pokud spustíte vrstvě SAP DBMS na virtuálním počítači E64s_v3, což je ve skupině dostupnosti společně s virtuální počítač s instancí sekundární DBMS v konfiguraci vysokou DOSTUPNOSTÍ, nelze jednoduše zastavte a restartujte sekundárního virtuálního počítače jako virtuální počítač řady M-Series, protože můžete chtít upg radovat virtuálního počítače. Důvodem je, že virtuální počítače řady M-Series a virtuální počítače Ev3-Series běží na jiném hardwaru a s ním v různých jednotek škálování. Je třeba vytvořit novou sadu dostupnosti, odstranit bez odstranění úložiště sekundárního virtuálního počítače Ev3-Series, a znovu nasadit virtuální počítač jako virtuální počítač řady M-Series do nové sady dostupnosti.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Tisk na místní síťové tiskárny z instance SAP v Azure
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Tisk přes protokol TCP/IP ve scénáři mezi různými místy
Nastavení místní síťové tiskárny TCP/IP, na základě ve Virtuálním počítači Azure se celkově stejně jako v podnikové síti, za předpokladu, že že máte tunel VPN typu Site-To-Site nebo ExpressRoute připojení.

- - -
> ![Windows][Logo_Windows] Windows
>
> Použijte následující postup:
>
> * Průvodce konfigurací, který umožňuje snadno nastavit tiskárny na Virtuálním počítači Azure jsou dostupné některé síťové tiskárny. Pokud žádný takový software Průvodce byl distribuován s tiskárnou, chcete-li vytvořit nový port tiskárny TCP/IP je manuální způsob, jak nastavit.
> * Otevřete ovládací panely -> zařízení a tiskárny -> Přidat tiskárnu
> * Zvolte možnost přidat tiskárny TCP/IP adres nebo název hostitele
> * Zadejte IP adresu tiskárny
> * Port tiskárny standardní 9100
> * V případě potřeby nainstalujte ručně příslušný ovladač tiskárny.
>
> ![Linux][Logo_Linux] Linux
>
> * pro Windows postupujte stejně jako standardní postup instalace síťové tiskárny
> * postupujte podle veřejné Linux vodítka pro [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) nebo [Red Hat a Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) o tom, jak přidat tiskárnu.
>
>

- - -
![Tisk v síti][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Na základě hostitele tiskárny přes protokol SMB (sdílené tiskárny) ve scénáři mezi různými místy
Tiskárny založené na hostiteli nejsou kompatibilní síťové záměrné. Ale tiskárny založené na hostiteli je možné sdílet mezi počítači v síti za předpokladu, tiskárny, kterou je připojený k určitému počítači spouštět. Připojení vaší podnikové sítě Site-To-Site nebo ExpressRoute a sdílet místní tiskárny. Protokol SMB používá jako název služby NetBIOS místo DNS. Název hostitele rozhraní NetBIOS se může lišit od názvu hostitele DNS. Standardní případ je, že název hostitele rozhraní NetBIOS a název hostitele DNS jsou identické. DNS domény nedává smysl v oboru názvu rozhraní NetBIOS. Odpovídajícím způsobem skládající se z názvu hostitele DNS a doména DNS plně kvalifikovaný název hostitele DNS se nesmí používat v oboru názvu rozhraní NetBIOS.

Sdílené tiskárny je identifikován jedinečný název v síti:

* Název hostitele SMB (vždycky potřeboval).
* Název sdílené složky (vždycky potřeboval).
* Název domény, pokud sdílení tiskáren není ve stejné doméně jako systém SAP.
* Kromě toho uživatelské jméno a heslo může být nutné přístup ke sdílené tiskárny.

Postup:

- - -
> ![Windows][Logo_Windows] Windows
>
> Sdílejte vaše místní tiskárnu.
> Ve virtuálním počítači Azure otevřete v aplikaci Windows Explorer a zadejte název sdílené složky tiskárny.
> Průvodce instalací tiskárny vás provede procesem instalace.
>
> ![Linux][Logo_Linux] Linux
>
> Tady je několik příkladů dokumentaci ke konfiguraci síťových tiskáren v Linuxu nebo včetně kapitola ohledně tisku v systému Linux. Ve virtuálním počítači Azure s Linuxem bude fungovat stejným způsobem jako virtuální počítač je součástí sítě VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL nebo Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>Tiskárny USB (přesměrování tiskárny)
V Azure není k dispozici možnost služby Vzdálená plocha můžete poskytnout uživatelům přístup ke svým zařízením místní tiskárnu ve vzdálené relaci.

- - -
> ![Windows][Logo_Windows] Windows
>
> Další podrobnosti o tisk s Windows najdete tady: <http://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integrace SAP do opravy a systému pro přenos (TMS) v mezi místními systémy pro Azure
Změna SAP a přenosu systému (TMS) je potřeba nakonfigurovat pro export a import žádost o přenos mezi systémy ve světě. Předpokládáme, že vývoj instance systému SAP (vývoj) jsou umístěné v Azure, zatímco kontroly kvality (dotazů a odpovědí) a produktivní systémy (PRD) jsou v místním. Kromě toho předpokládáme, že je adresář centrálního přenosu.

##### <a name="configuring-the-transport-domain"></a>Konfigurace domény přenosu
Konfigurace domény přenosu systému jste uvedli jako řadič domény přenosu, jak je popsáno v [konfigurací řadiče domény přenosu](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Vygeneruje se uživatel systému, vytvoří se TMSADM a požadované cílové RFC. Můžete vyhledat tato připojení RFC pomocí SM59 transakce. Překlad názvů hostitelů musí být povolena napříč doménou přenosu.

Postup:

* V tomto scénáři jsme se rozhodli, že bude systém QAS místní řadič domény CTS. Volání transakcí moduly STM. Zobrazí se dialogové okno TMS. Zobrazí se dialogové okno Konfigurace domény přenosu. (Toto dialogové okno se zobrazí pouze pokud jste ještě nenakonfigurovali přenosu domény.)
* Ujistěte se, že je automaticky vytvořený uživatel TMSADM oprávnění (SM59 -> ABAP připojení -> TMSADM@E61.DOMAIN_E61 -> Podrobnosti -> Utilities(M) -> Test autorizace). Úvodní obrazovka transakce moduly STM byste měli vidět, že tento systém SAP nyní funguje jako řadič domény přenosu jak je znázorněno zde:

![Úvodní obrazovka transakce moduly STM na řadiči domény][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Včetně systémů SAP v doméně přenosu
Posloupnost včetně systému SAP v doméně přenosu by měl vypadat takto:

* VÝVOJ systému v Azure přejděte k systému pro přenos (klient 000) a volání transakcí moduly STM. Vyberte další konfiguraci z dialogových oken a pokračujte zahrnují systému v doméně. Zadejte řadič domény jako cílový hostitel ([včetně systémů SAP v doméně přenosu](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Systém čeká nyní mají být zahrnuty v doméně přenosu.
* Z bezpečnostních důvodů budete muset pak přejděte zpět do řadiče domény Potvrďte vaši žádost. Zvolte Přehled systému a schválit čekání systému. Potvrďte výzvu a konfigurace budou distribuována.

Tento systém SAP nyní obsahuje nezbytné informace o všech jiných systémů SAP v doméně přenosu. Ve stejnou dobu adresu nového systému SAP se odešlou do všech dalších systémů SAP a systém SAP je zadán v profilu přenos řízení programu přenosu. Zkontrolujte, zda dokumenty RFC a přístup k adresáři přenosu domény fungovat.

Pokračovat v konfiguraci vašeho systému přenosu jako obvykle, jak je popsáno v dokumentaci k [změn a systému pro přenos](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Postup:

* Ujistěte se, že vaše moduly STM v místním prostředí je správně nakonfigurovaný.
* Zajistěte, aby že vaše na virtuálním počítači Azure a naopak visa dají vyřešit název hostitele řadiče domény přenosu.
* Volání transakcí moduly STM -> ostatní konfigurace -> zahrnout systém v doméně.
* Ověřte připojení TMS systému na místě.
* Konfigurace směrování přenosu, skupiny a vrstvy jako obvykle.

V site-to-site připojení mezi různými místy scénáře, latence mezi místními a Azure stále může být významné. Pokud budeme postupovat podle pořadí přenos objektů do vývojových a testovacích systémů do produkčního prostředí nebo přemýšlení o použití přenosy nebo podporu balíčky do různých systémů, je dobré si uvědomit, závisí na umístění adresáře centrální přenosu Některé systémy dojde k vysoké latenci čtení nebo zápis dat v adresáři centrální přenosu. Situace je podobný konfigurace SAP na šířku, kde se šíří různými systémy prostřednictvím různých datových střediscích podstatné vzdálenost mezi datovými centry.

Pokud chcete obejít tato čekací doba a mít systémy pracovat rychle čtení a zápis do nebo z adresáře přenosu, můžete nastavit dvěma doménami moduly STM přenosu (jeden pro místní a jeden u systémů v Azure a propojte přenos domén. Zkontrolujte, zda tato dokumentace, která vysvětluje principy za tento koncept v SAP TMS: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Postup:

* Nastavení domény přenosu na jednotlivých umístěních (místní a Azure) pomocí transakce moduly STM <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Odkaz domény s odkazem na domény a zkontrolujte propojení mezi těmito dvěma doménami.
  <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuce konfigurace do propojené systému.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC provozu mezi instance SAP v Azure a místní (mezi různými místy)
RFC provoz mezi systémy, které jsou místně i v Azure je potřeba pracovat. Nastavit volání transakcí SM59 připojení v systému zdrojového kde je třeba definovat spojení RFC na cílovém systému. Konfigurace je podobná standardní nastavení připojení k dokumentu RFC.

Předpokládáme, který v případě mezi různými místy, virtuálních počítačů, které jsou spuštění SAP systémy, které potřebují komunikovat mezi sebou ve stejné doméně. Proto nastavení RFC připojení mezi systémy SAP není se liší od pokynů k instalaci a vstupy v místních scénářích.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Přístup k místním fileshares z instance SAP v Azure nebo naopak
Instance SAP v Azure, třeba přístup ke sdíleným složkám, které jsou v rámci podnikové místním prostředí. Kromě toho místní instance SAP potřeba přístup ke sdíleným složkám, které jsou umístěné v Azure. Pokud chcete povolit sdílené složky, musíte nakonfigurovat oprávnění a možnosti sdílení v místním systému. Ujistěte se, že otevřete porty pro připojení VPN nebo ExpressRoute mezi Azure a vaším datovým centrem.

## <a name="supportability"></a>Možnosti podpory
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure řešení pro monitorování pro SAP
Chcete-li povolit monitorování zásadně důležitých systémů SAP v Azure SAP monitorovacích nástrojů SAPOSCOL nebo Agent hostitele SAP získání dat vypnout hostitele služby virtuálního počítače Azure prostřednictvím rozšíření monitorování Azure pro SAP. Protože požadavky SAP byly specifické pro aplikace SAP, Microsoft se rozhodli obecně implementovat požadované funkce do Azure, ta ale nebude zákazníci mohli nasazení komponenty potřebné monitorování a konfigurací pro jejich virtuální Virtuální počítače běžící v Azure. Nasazení a správa životního cyklu monitorování součástí však bude možné většinou automatizovat, Azure.

#### <a name="solution-design"></a>Návrh řešení
Řešení vyvinutý povolení monitorování SAP vychází z architektury agenta virtuálního počítače Azure a platformy pro rozšíření. Cílem architektury agenta virtuálního počítače Azure a rozšíření je povolit instalaci aplikací softwaru k dispozici v galerii rozšíření virtuálního počítače Azure v rámci virtuálního počítače. Hlavní myšlenkou tento koncept je umožnit (v případech, jako je rozšíření Azure Monitoring for SAP), nasazení speciální funkce do virtuálního počítače a konfigurací softwaru v době nasazení.

"Azure agenta virtuálního počítače", která umožňuje zpracování konkrétní rozšíření virtuálního počítače Azure v rámci virtuálního počítače se vloží do virtuálních počítačů Windows ve výchozím nastavení při vytváření virtuálního počítače na webu Azure Portal. V případě SUSE, Red Hat nebo Oracle Linux agent virtuálního počítače je již součástí image Azure Marketplace. V případě, že jeden by virtuální počítač s Linuxem v místním do Azure nahrát agenta virtuálního počítače musí ručně doinstalovat.

Základní stavební bloky řešení monitorování v Azure pro SAP vypadá takto:

![Součástí rozšíření Microsoft Azure][planning-guide-figure-2400]

Jak je uvedeno ve výše uvedeném diagramu bloku, jednu část řešení pro monitorování pro SAP hostována v Imagi virtuálního počítače Azure a Galerie rozšíření Azure, který je globálně replikovaného úložiště, který je spravovaný nástrojem operace Azure. Je odpovědností společné SAP/MS tým pracující na Azure implementace SAP pro práci s Azure operace publikování nové verze rozšíření Azure Monitoring pro SAP.

Když nasadíte nový virtuální počítač s Windows, Agent virtuálního počítače Azure se automaticky přidá do virtuálního počítače. Funkce Tento agent je ke koordinaci konfigurace rozšíření Azure pro monitorování systémů SAP NetWeaver a načítání. Pro virtuální počítače s Linuxem Agent virtuálního počítače Azure je již součástí image operačního systému Azure Marketplace.

Je však krok, kterou je stále potřeba provést příslušného zákazníka. Jedná se o povolení a konfigurace shromažďování výkonu. Skript prostředí PowerShell nebo rozhraní příkazového řádku je automatizovaný proces související s konfigurací. Skript prostředí PowerShell můžete stáhnout na webu Microsoft Script Center Azure, jak je popsáno v [Průvodce nasazením][deployment-guide].

Architektury řešení Azure monitorování pro SAP vypadá takto:

![Řešení pro monitorování pro SAP NetWeaver Azure][planning-guide-figure-2500]

**Přesný postup a podrobné kroky pro použití těchto rutin prostředí PowerShell nebo rozhraní příkazového řádku během nasazení, postupujte podle pokynů uvedených v [Průvodce nasazením][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integrace Azure nachází instance SAP do SAProuter
Instance SAP v Azure musí být také přístupná SAProuter.

![Připojení k síti směrovače SAP][planning-guide-figure-2600]

SAProuter umožňuje komunikaci protokolu TCP/IP mezi zúčastněné systémy, pokud neexistuje žádné přímé připojení IP. To poskytuje výhodu, že žádné začátku do konce připojení mezi partnery komunikace je nezbytné na úrovni sítě. SAProuter naslouchá na portu 3299 ve výchozím nastavení.
Připojení přes SAProuter instance SAP, budete muset poskytnout SAProuter řetězec a název hostitele s pokusy o připojení.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver jako Java
Zatím zaměření dokumentu byla SAP NetWeaver obecně nebo SAP NetWeaver ABAP zásobníku. V této malé části jsou uvedené důležité informace k sadě SAP Java. Jednou z nejdůležitějších aplikací výhradně založených na Javě SAP NetWeaver je SAP Enterprise Portal. Další SAP NetWeaver na základě aplikací, jako správce řešení SAP a SAP PI používat SAP NetWeaver ABAP i zásobníky Javy. Proto určitě je potřeba vzít v úvahu konkrétní aspekty související s do SAP NetWeaver Java zásobníku.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal
Instalační program portálu SAP ve virtuálním počítači Azure se neliší od na místní instalace, pokud nasazujete ve scénářích mezi různými místy. Protože DNS se provádí v místním, nastavení portu jednotlivých instancí lze provést jako nakonfigurovaný v místním. Doporučení a omezeními popsanými v tomto dokumentu, pokud platí pro aplikace, jako je SAP Enterprise Portal nebo zásobníku SAP NetWeaver Java obecně.

![Portál vystavené SAP][planning-guide-figure-2700]

Scénář speciální nasazení některé zákazníky je přímé expozice SAP Enterprise Portal k Internetu na hostitele virtuálního počítače připojeny k podnikové síti pomocí tunelového připojení sítě VPN site-to-site nebo ExpressRoute. Pro scénář budete muset ověřit, že určité porty jsou otevřené a není blokován bránou firewall nebo skupiny zabezpečení. Stejný mechanismus by bylo potřeba nainstalovat, pokud se chcete připojit k instanci SAP Java z místního v čistě cloudové scénáře.

Portálu počáteční identifikátor URI je http (s):`<Portalserver`>: 5XX00/irj kde port, který je tvořen přidáním 50000 plus (Systemnumber?? 100). Je výchozí portálu URI SAP systém 00 `<dns name`>.`<azure region` >.Cloudapp.azure.com:PublicPort/irj. Další podrobnosti, podíváme se na <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Konfigurace koncového bodu][planning-guide-figure-2800]

Pokud chcete upravit adresu URL a/nebo porty webu Enterprise Portal SAP, zkontrolujte Tato dokumentace:

* [Změna adresy URL portálu](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Změnit výchozí čísla portů, čísla portů pro portál](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Vysoká dostupnost (HA) a zotavení po havárii (DR) pro SAP NetWeaver provozovaný v Azure Virtual Machines
### <a name="definition-of-terminologies"></a>Definice terminologie
Termín **vysoké dostupnosti (HA)** souvisí obecně k sadě technologií, které minimalizuje přerušení služeb IT tím, že poskytuje kontinuitu služeb IT prostřednictvím redundantní, odolné proti chybám, nebo převzetí služeb při selhání chráněné komponenty uvnitř **stejné** datového centra. V našem případě v rámci jedné oblasti Azure.

**Zotavení po havárii (DR)** také cílí na minimalizovat narušení služeb IT a jejich obnovení ale napříč **různých** datových center, které jsou obvykle umístěné stovky kilometrů daleko. V našem případě obvykle mezi různými oblastmi Azure ve stejné geopolitické oblasti nebo jako zavedené vy jako zákazník.

### <a name="overview-of-high-availability"></a>Přehled vysoké dostupnosti
Jsme můžete oddělit diskuze o vysoké dostupnosti SAP v Azure do dvou částí:

* **Vysoká dostupnost infrastruktury Azure**, například HA výpočetní prostředky (virtuální počítače), sítě, úložiště atd. a jeho výhody pro zvýšení dostupnosti aplikací SAP.
* **Vysoká dostupnost pro aplikace SAP**, třeba ověření stavu systému SAP softwarových komponent:
  * Aplikační servery SAP
  * Instanci SAP ASCS/SCS
  * Databázového serveru

a jak mohou být kombinovány s vysokou dostupnost infrastruktury Azure.

Zajištění vysoké dostupnosti SAP v Azure má několik rozdílů ve srovnání s vysokou dostupností pro SAP v místním fyzickém nebo virtuálním prostředí. Následující dokument od SAPU. popisuje standardní konfigurace vysoké dostupnosti SAP ve virtualizovaných prostředích ve Windows: <http://scn.sap.com/docs/DOC-44415>. Není žádná integrované sapinst SAP-HA konfigurace pro Linux jako existuje pro Windows. O SAP HA on-premises pro Linux najdete další informace tady: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Vysoká dostupnost infrastruktury Azure
Je právě jeden virtuální počítač SLA 99,9 %. Kde získáte představu, jak může vypadat dostupnost jednoho virtuálního počítače, můžete vytvořit produktu různými smlouvami SLA k dispozici Azure: <https://azure.microsoft.com/support/legal/sla/>.

Základ pro výpočet je 30 dnů za měsíc nebo 43 200 minut. Proto 0,05 % výpadek odpovídá 21,6 minut. Jako obvykle dostupnosti různé služby bude násobit následujícím způsobem:

(Služba dostupnosti #1/100) * (dostupnosti služby #2/100) * (dostupnosti služby #3/100) 

Například:

(99,95/100) * (99,9/100) * (99,9/100) = 0.9975 nebo celkovou dostupnost 99.75 %.

#### <a name="virtual-machine-vm-high-availability"></a>Vysoká dostupnost virtuálního počítače (VM)
Existují dva typy událostí platformy Azure, které můžou ovlivnit dostupnost vašich virtuálních počítačů: plánovaná údržba a neplánovaná Údržba.

* Události plánované údržby jsou pravidelné aktualizace prováděné microsoftem základní platformy Azure ke zlepšení celkové spolehlivosti, výkonu a zabezpečení infrastruktury platformy, které běží vaše virtuální počítače na.
* K neplánovaným událostem údržby dojít, když hardwaru nebo virtuálního počítače základní fyzické infrastruktury došlo k chybě nějakým způsobem. To může zahrnovat selhání místní sítě, selhání místního disku nebo další selhání na úrovni racku. Když se takové selhání detekuje, Platforma Azure automaticky migrovat váš virtuální počítač z není v pořádku fyzického serveru, který hostuje vašeho virtuálního počítače na fyzickém serveru v dobrém stavu. Takové události se vyskytují jen vzácně, ale také můžou způsobit restartování vašeho virtuálního počítače.

Další podrobnosti najdete v této dokumentaci: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redundance úložiště Azure
Data ve vašem účtu úložiště Microsoft Azure se vždy replikují, aby zajistila stálost a vysoká dostupnost splňující podmínky smlouvy SLA pro Azure Storage dokonce i v případě krátkodobého selhání hardwaru.

Vzhledem k tomu, že Azure Storage je ve výchozím nastavení uchovávání tři Image dat, RAID5 nebo RAID1 napříč více disků v Azure, je nezbytné.

Další podrobnosti najdete v tomto článku: <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Využívání infrastruktury Azure VM Restart k dosažení vysoké dostupnosti aplikací SAP
Pokud se rozhodnete nepoužívat funkce, jako například Windows Server Failover Clustering (WSFC) nebo Pacemaker na Linuxu (momentálně se podporuje jenom pro SLES 12 a vyšší), restartujte virtuální počítač Azure se využívá k ochraně systému SAP proti plánovaných a neplánovaných výpadků Azure fyzický server infrastruktury a celkové základní platformy Azure.

> [!NOTE]
> Je důležité zmínit, že restartování virtuálního počítače Azure primárně chrání virtuální počítače a ne aplikace. Restartujte virtuální počítač se nebude poskytovat vysokou dostupnost pro aplikace SAP, ale nabízí určitou úroveň dostupnosti infrastruktury a proto nepřímo vyšší dostupnost systému SAP. Pro čas, kdy bude trvat restartování virtuálního počítače po hostitele plánovaného nebo neplánovaného výpadku také neexistuje žádná smlouva SLA. Tato metoda vysokou dostupnost, proto není vhodný pro důležité součásti systému SAP jako (A) SCS nebo DBMS.
>
>

Jiný element důležité infrastruktury pro zajištění vysoké dostupnosti je úložiště. SLA pro Azure Storage je třeba 99,9 % dostupnost. Pokud nasadíte všechny virtuální počítače s jeho disky do jednoho účtu Azure Storage, potenciální nedostupnost způsobí nedostupnost všech virtuálních počítačů, které jsou umístěné v tomto účtu úložiště Azure a také všechny SAP součástí, které běží v rámci těchto virtuálních počítačů služby Azure Storage.  

Namísto vložení hodnoty všech virtuálních počítačů do jednoho jednoho účtu úložiště Azure, můžete použít také vyhrazeného úložiště účtů pro každý virtuální počítač a tímto způsobem zvýšíte celkovou dostupnost virtuálních počítačů a SAP aplikace s použitím více nezávislých účtů úložiště Azure.

Služba Azure Managed Disks se automaticky umístí v virtuálního počítače, které jsou připojeny k doméně selhání. Umístíte-li nastavit dva virtuální počítače ve skupině dostupnosti a používat spravované disky, platformu se postará o distribuci spravovaných disků do různých domén selhání také. Pokud chcete používat Premium Storage, důrazně doporučujeme také pomocí správy disků.

Ukázková architektura systému SAP NetWeaver, které používá infrastrukturu Azure vysokou dostupnost a úložiště účtů by mohl vypadat takto:

![Využívání infrastruktury Azure HA k dosažení vysoké dostupnosti aplikace SAP][planning-guide-figure-2900]

Ukázková architektura systému SAP NetWeaver, které používá infrastrukturu Azure HA a Managed Disks může vypadat například takto:

![Využívání infrastruktury Azure HA k dosažení vysoké dostupnosti aplikace SAP][planning-guide-figure-2901]

Pro kritické komponenty SAP dosáhli jsme následující zatím:

* Vysoká dostupnost aplikační servery SAP (AS)

  Instance serveru aplikace SAP jsou redundantní komponenty. Každý SAP, protože instance je nasazen na vlastním virtuálním počítači, na kterém běží v jiné poruchy Azure a upgradovat doménu (najdete v kapitolách [domén selhání] [ planning-guide-3.2.1] a [upgradu domény] [ planning-guide-3.2.2]). To je, že jsou splněné pomocí skupin dostupnosti Azure (viz kapitola [skupin dostupnosti Azure][planning-guide-3.2.3]). Potenciální plánované nebo neplánované nedostupnosti Azure selhání nebo upgradu domény způsobí nedostupnost omezenému počtu virtuálních počítačů s jejich SAP jako instance.

  Každý SAP, protože instance je umístěn v svůj vlastní účet služby Azure Storage – potenciální nedostupnost jeden účet úložiště Azure způsobí nedostupnost jenom jeden virtuální počítač s jeho SAP jako instance. Nezapomínejte, že platí omezení účtů úložiště Azure v rámci jednoho předplatného Azure. Aby se zajistilo automatické spuštění instanci (A) SCS po restartování virtuálního počítače, ujistěte se, že chcete nastavit parametr automatické spouštění v instanci (A) SCS spuštění profilu je popsáno v kapitole [pomocí automatické spuštění pro instance SAP][planning-guide-11.5].
  Také přečtěte si kapitolu [vysoká dostupnost pro aplikační servery SAP] [ planning-guide-11.4.1] další podrobnosti.

  I když můžete použít Managed Disks, tyto disky jsou také uloženy v účtu služby Azure Storage a může být k dispozici v událost kvůli výpadku úložiště.

* *Vyšší* instanci dostupnosti SAP (A) SCS

  Tady můžeme využívat Azure restartovat virtuální počítač ochranu virtuálního počítače s nainstalovanou instanci SAP (A) SCS. V případě plánované nebo neplánované výpadky Azure přeruší, virtuální počítače se restartuje na jiný server k dispozici. Jak už bylo zmíněno dříve, restartujte virtuální počítač Azure primárně chrání virtuální počítače a ne aplikace, v takovém případě (A) SCS. Prostřednictvím virtuální počítač restartovat budeme nepřímo vyšší dostupnost SAP (A) SCS instance. Po restartování virtuálního počítače – pomáhat zajistit automatické spuštění instanci (A) SCS, nezapomeňte nastavit automatické spuštění parametru v profilu spuštění instanci (A) SCS je popsáno v kapitole [pomocí automatické spuštění pro instance SAP][planning-guide-11.5]. To znamená, že (A) SCS instance jako jednoho bodu o selhání (SPOF) spuštěná v jednom virtuálním počítači bude faktor určující dostupnost celé prostředí SAP.

* *Vyšší* dostupnost serveru DBMS

  Tady podobně jako případ použití instance SAP (A) SCS, můžeme využít Azure restartovat virtuální počítač ochranu virtuálního počítače s nainstalovaným softwarem DBMS a jsme dosáhli vysoké dostupnosti DBMS softwaru prostřednictvím restartování virtuálního počítače.
  Systém DBMS spuštěné v jednom virtuálním počítači je také SPOF a je faktor určující dostupnost celé prostředí SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Vysoké dostupnosti aplikace SAP v Azure IaaS
K dosažení úplné vysoké dostupnosti systému SAP, musíme chránit všechny důležité součásti systému SAP, například redundantní aplikační servery SAP a jedinečný součásti (například jeden bod selhání), jako jsou SAP (A) SCS instance a DBMS.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Vysoká dostupnost pro aplikační servery SAP
Pro SAP instance aplikace, které servery pro/dialog není potřeba uvažovat o konkrétní vysokou dostupnost řešení. Vysoké dostupnosti se dosahuje pomocí redundance a tím současně s dostatečně z nich různé virtuální počítače. Musí všechny být umístěné ve stejné Azure skupině dostupnosti, aby, že virtuální počítače mohou být aktualizovány ve stejnou dobu, během plánované údržby výpadků. Základní funkce, který je založen na jiný Upgrade a domén selhání v rámci jednotce škálování Azure byl již zaveden v kapitole [upgradu domény][planning-guide-3.2.2]. Skupiny dostupnosti Azure prezentovaly v kapitole [skupin dostupnosti Azure] [ planning-guide-3.2.3] tohoto dokumentu.

Neexistuje žádné neomezený počet selhání a upgradu domény, kterého ve skupině dostupnosti Azure v rámci jednotky škálování Azure. To znamená, že uvedení počet virtuálních počítačů do jedné skupiny dostupnosti, dříve či později více než jeden virtuální počítač končí ve stejném selhání nebo upgradu domény.

Nasazení několika instancí SAP aplikačních serverů v jejich vyhrazených virtuálních počítačích a za předpokladu, že nám dává pět upgradu domény, na konci splatit na následujícím obrázku. Maximální skutečný počet selhání a aktualizačními doménami v rámci skupiny dostupnosti může být v budoucnu změnit:

![HA z aplikační servery SAP v Azure][planning-guide-figure-3000]

Další podrobnosti najdete v této dokumentaci: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Vysoká dostupnost pro centrální služby SAP v Azure
Architektura pro vysokou dostupnost centrální služby SAP v Azure, najdete v článku [architektura pro vysokou dostupnost a scénáře pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) jako položka informace. Tento článek odkazuje na podrobnější popisy pro konkrétní operační systémy.

#### <a name="high-availability-for-the-sap-database-instance"></a>Vysoká dostupnost pro instanci databáze SAP
Typické SAP DBMS HA instalace je založena na dva virtuální počítače DBMS kde DBMS funkce vysoké dostupnosti se používá k replikaci dat od aktivní instance DBMS na druhý virtuální počítač do pasivní instance DBMS.

Vysoká dostupnost a obnovení funkce pro DBMS obecně stejně jako konkrétní systém DBMS jsou popsány v [nasazení DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Vysoké dostupnosti systému SAP kompletní začátku do konce
Tady jsou dva příklady kompletní SAP NetWeaver HA architekturu v Azure – jeden pro Windows a jeden pro Linux.

Nespravované disky jenom: koncepty, jak je vysvětleno níže možná bude nutné u něho ohrožena bezpečnost o něco, když nasadíte mnoho systémů SAP a počet nasazených virtuálních počítačů k překročení maximálního počtu účtů úložiště na jedno předplatné. V takových případech třeba virtuální pevné disky virtuálních počítačů a nelze jej zkombinovat v rámci jednoho účtu úložiště. Obvykle by tak učiníte kombinací aplikační vrstvě SAP virtuální pevné disky virtuálních počítačů různých systémů SAP.  Můžeme také kombinovat různé virtuální pevné disky různých DBMS virtuálních počítačů různých systémů SAP v jednom účtu úložiště Azure. Tím dodržujte při tom na limity IOPS účtů úložiště Azure (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] HA ve Windows
![Architektura aplikací vysokou dostupnost pro SAP NetWeaver s SQL serverem v Azure IaaS][planning-guide-figure-3200]

Následující konstrukce Azure se používají pro systém SAP NetWeaver k minimalizaci vlivu podle problémů s infrastrukturou a hostování, opravy chyb:

* Celý systém je nasazený v Azure (požadováno – DBMS, (A) SCS instance a aplikace dokončena a vrstva potřeba spustit ve stejném umístění).
* Celý systém běží v rámci jednoho předplatného Azure (povinné).
* Celý systém běží v rámci jedné virtuální sítě Azure (povinné).
* I přes všechny virtuální počítače, které patří do stejné virtuální síti je možné oddělit virtuální počítače z jednoho systému SAP do tří skupin dostupnosti.
* Každá vrstva (například systému DBMS, ASCS, aplikační servery) musíte použít vyhrazenou skupinu dostupnosti.
* Všechny virtuální počítače spuštěné instance jednoho systému SAP DBMS jsou v jedné skupiny dostupnosti. Předpokládáme, že je více než jeden virtuální počítač s instancí DBMS na systému od nativní vysoké dostupnosti DBMS, které se používají funkce, jako je SQL Server AlwaysOn nebo Oracle Data Guard.
* Všechny virtuální počítače se systémem DBMS instance použít svůj vlastní účet úložiště. Systém DBMS data a soubory protokolů se replikují z jednoho účtu úložiště do jiného účtu úložiště pomocí funkcí DBMS vysoké dostupnosti, které se synchronizují data. Nedostupnost jeden účet úložiště způsobí nedostupnost jednoho uzlu clusteru SQL Windows, ale ne celou službu systému SQL Server.
* Všechny virtuální počítače spuštěné instanci (A) SCS jednoho systému SAP jsou v jedné skupiny dostupnosti. Windows Server Failover Cluster (WSFC) je nakonfigurován v rámci těchto virtuálních počítačů k ochraně (A) SCS instance.
* Všechny virtuální počítače spuštěné instance (A) SCS použít svůj vlastní účet úložiště. (A) SCS instance soubory a složky, globální SAP se replikují z jednoho účtu úložiště do jiného účtu úložiště pomocí replikace SIOS DataKeeper. Způsobí, že nedostupnosti jednoho účtu úložiště nedostupnosti jednoho uzlu clusteru (A) SCS Windows, ale ne celý (A) SCS služby.
* VŠECHNY virtuální počítače představující aplikačního serveru SAP jsou ve třetí skupinu dostupnosti.
* VŠECHNY virtuální počítače spuštěné aplikační servery SAP použít svůj vlastní účet úložiště. Nedostupnost jeden účet úložiště způsobí nedostupnost jeden aplikační server SAP, kde ostatní aplikační servery SAP dál běžet.

Na následujícím obrázku znázorněno stejnou šířku využití služby Managed Disks.

![Architektura aplikací vysokou dostupnost pro SAP NetWeaver s SQL serverem v Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] HA v Linuxu
Architektura SAP HA v Linuxu v Azure je v podstatě stejný jako u Windows jak je popsáno výše. Přečtěte si poznámky SAP [1928533] seznam řešení s vysokou dostupností podporované.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Pomocí automatické spuštění pro instance SAP
SAP nabízí funkci, která instance SAP spustit ihned po spuštění operačního systému ve virtuálním počítači. Byly přesné kroky popsané v článku znalostní báze SAP [1909114]. Však není SAP doporučující použití nastavení už, protože neexistuje žádný ovládací prvek v pořadí restartování instance, za předpokladu, že více než jeden virtuální počítač je teď vliv nebo na virtuální počítač běžel více instancí. Za předpokladu, že Typický scénář Azure jedné instance serveru aplikace SAP v virtuálního počítače a velikost písmen jednoho nakonec získávání restartování virtuálního počítače, automatické spuštění není kritické a dá se povolit tak, že přidáte tento parametr:

    Autostart = 1

Do profilu spuštění instance SAP ABAP a Java.

> [!NOTE]
> Automatické spuštění parametr může mít také některé downfalls. Podrobněji se aktivuje parametr při spuštění související služba Windows/Linux instance spuštění SAP ABAP nebo Java instance. Určitě je to tento případ při spuštění operačního systému. Ale restartování služby SAP jsou také běžné věcí, kterou funkce Správa životního cyklu softwaru SAP, jako jsou součet nebo jiné aktualizace nebo upgradu. Tyto funkce nejsou očekávána instance automaticky restartovat vůbec. Proto by mělo být zakázáno automatické spuštění parametru před spuštěním těchto úloh. Parametr Autostart neměli použít také pro instance SAP, které jsou v clusteru, jako jsou ASC/SCS/CI.
>
>

Zobrazit další informace o automatické spuštění pro SAP instance tady:

* [Spuštění/zastavení SAP spolu s vaší spuštění/zastavení serveru systému Unix](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Spuštění a zastavení agenti pro správu SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Jak povolit automatické spuštění z databáze HANA](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Větší systémy SAP vrstvy 3
Aspekty vysoké dostupnosti konfigurace SAP vrstvy 3 je teď již popsané v předchozí části. Ale co může být nasazený systémů, ve kterém jsou požadavky na systém DBMS server příliš velký, aby byl v Azure, ale aplikační vrstvě SAP do Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Umístění konfigurace SAP vrstvy 3
Není možné rozdělit na úrovni aplikace samostatně, nebo aplikace a systém DBMS vrstvy mezi místními a Azure. Systém SAP je buď zcela nasazená místně nebo v Azure. Také není možné z aplikačních serverů v Azure spustit místně a nějaké jiné. Který je výchozím bodem diskuse. Můžeme také nejsou podporovány mít DBMS součásti systému SAP a SAP aplikační vrstvu serveru nasazené ve dvou různých oblastech Azure. Například systému DBMS v oblastech západní USA a SAP aplikační vrstvy v oblasti střed USA. Z důvodů nejsou podporovány tyto konfigurace je latence citlivosti architektura SAP NetWeaver.

Ale v průběhu poslední rok center vyvinutá partnery společné umístění dat do oblastí Azure. Tyto společné umístění jsou často v těsné blízkosti fyzické Azure data Center v rámci oblasti Azure. Čekací doba, která je menší než 2 MS může způsobit krátké vzdálenosti a připojení prostředků ve společném umístění prostřednictvím ExpressRoute do Azure. V takovém případě má být vyhledán ve společném umístění a SAP DBMS vrstvy (včetně úložiště SAN nebo NAS) vrstvy aplikace v Azure je možné. [Velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Systémy v režimu offline zálohování SAP
Závisí na zvolené (úrovně 2 nebo 3 vrstvy) existuje konfiguraci SAP může být potřeba zálohovat. Obsah samotných virtuálních počítačů a vytvořit zálohu databáze. Zálohy DBMS související se očekává, že provádí pomocí metody databáze. Podrobný popis různých databázích, najdete v [DBMS průvodce][dbms-guide]. Na druhé straně SAP data lze zálohovat offline způsobem (včetně obsahu databáze také) jak je popsáno v této části nebo online jak je popsáno v další části.

Zálohování offline by vyžadovaly v podstatě vypnutí virtuálního počítače na webu Azure portal a zkopírovat základní disk virtuálního počítače a všechny připojené disky na virtuálním počítači. To by zachovat bodu v čase image virtuálního počítače a jeho přidružený disk. Se doporučuje zkopírovat zálohy do jiného účtu úložiště Azure. Proto postup najdete v kapitole [kopírování disků mezi účty Azure Storage] [ planning-guide-5.4.2] tohoto dokumentu se vztahují.
Kromě vypnutí počítače pomocí webu Azure portal, jeden to také udělat prostřednictvím Powershellu nebo CLI podle postupu popsaného tady: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Obnovit tento stav by obsahovat odstranění základní virtuální počítač, stejně jako původní disky základní virtuální počítač a připojené disky uložené disky kopírování zpátky do původní skupiny prostředků nebo účtu úložiště za spravované disky a pak znovu nasadit v systému.
Tento článek ukazuje příklad, jak skriptu tento proces v prostředí Powershell: <http://www.westerndevs.com/azure-snapshots/>

Zkontrolujte prosím, že k instalaci nové licence SAP od obnovení zálohování virtuálních počítačů, jak je popsáno výše se vytvoří nový klíč hardwaru.

### <a name="online-backup-of-an-sap-system"></a>Online zálohování systému SAP
Zálohování systému DBMS se provádí pomocí metody specifické pro systém DBMS jak je popsáno v [DBMS průvodce][dbms-guide].

Ostatní virtuální počítače v rámci systému SAP můžete zálohovat pomocí funkce pro zálohování virtuálních počítačů Azure. Azure se zálohováním virtuálních počítačů je standardní metoda k zálohování celého virtuálního počítače v Azure. Azure Backup ukládat zálohy do Azure a umožňuje obnovení virtuálního počítače znovu.

> [!NOTE]
> Od prosince 2015 pomocí zálohování virtuálních počítačů neudržuje jedinečné ID virtuálního počítače, který se používá pro SAP licencování. To znamená, že obnovení ze zálohy virtuálního počítače vyžaduje instalaci nového klíče licence SAP jako obnovený virtuální počítač se považuje za nový virtuální počítač a nenahrazuje bývalé tu, která byla uložena.
>
> ![Windows][Logo_Windows] Windows
>
> Teoreticky, virtuální počítače, které je možné zálohovat spuštění databáze konzistentním způsobem, pokud systém DBMS podporuje Windows VSS (služby Stínová kopie svazku <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>), například SQL Server neexistuje.
> Nicméně mějte na paměti, která podle záloh virtuálních počítačů Azure, které v daném okamžiku obnovení databází nejsou možné. Proto doporučujeme provést zálohování databází s funkcemi DBMS, aniž byste museli spoléhat na zálohování virtuálních počítačů Azure.
>
> K seznámení s Azure se zálohováním virtuálních počítačů Začněte zde: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Další možností je použít kombinaci aplikace Microsoft Data Protection Manager nainstalovat do virtuálního počítače Azure a Azure Backup k zálohování a obnovení databází. Další informace najdete tady: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Neexistuje žádný ekvivalent k Windows stínové kopie svazku v systému Linux. Proto pouze konzistentní zálohování jsou zálohy je to možné, ale není konzistentní s aplikací. Zálohování SAP DBMS by mělo být provedeno pomocí funkce DBMS. Systém souborů, které zahrnují data související s SAP můžete uložit, například pomocí cíl jak je popsáno zde: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure jako lokalitu pro zotavení po Havárii pro produkční prostředí SAP
Rozšíření pro různé součásti Hyper-V, System Center a Azure od Mid 2014, povolit používání Azure jako lokalitu pro zotavení po Havárii pro virtuální počítače spuštěné místně založené na technologii Hyper-V.

Blog s podrobnostmi o tom, jak nasadit toto řešení je zdokumentované tady: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Souhrn
Klíčové body vysoké dostupnosti systémů SAP v Azure jsou:

* V tomto okamžiku SAP jediný bod selhání nelze zabezpečit úplně stejně, jak to provést v případě místních nasazení. Důvodem je to tento Disk sdílené clustery ještě nedají sestavit v Azure bez použití softwaru 3. stran.
* Pro vrstvu DBMS, budete muset použít DBMS funkce, které se nespoléhá na sdíleném disku clusteru technologie. Podrobnosti jsou dokumentovány v článku [DBMS průvodce][dbms-guide].
* Chcete-li minimalizovat dopad problémy v rámci domén selhání v Azure údržbu infrastruktury nebo hostitele, by měl použít skupiny dostupnosti Azure:
  * Doporučujeme mít jednu skupinu dostupnosti pro aplikační vrstvě SAP.
  * Doporučuje se mít samostatnou skupinu dostupnosti pro vrstvu k SAP DBMS.
  * NENÍ doporučuje použít stejné skupině dostupnosti pro virtuální počítače různých systémů SAP.
  * Doporučuje se použít Managed Disks úrovně Premium.
* Pro účely zálohování vrstvě SAP DBMS, zkontrolujte, [DBMS průvodce][dbms-guide].
* Zálohování SAP dialogu instance nemá příliš smysl vzhledem k tomu, že je obvykle rychlejší k opětovnému nasazení instancí jednoduché dialogového okna.
* Zálohování virtuálního počítače, který obsahuje globálního adresáře systému SAP a s ním všechny profily různé instance dává smysl a by se měla provést pomocí služby Windows Backup nebo, například cílová v Linuxu. Vzhledem k tomu, že existují rozdíly v systému Windows Server 2008 (R2) a Windows Server 2012 (R2), která usnadňují zálohování pomocí novější systému Windows Server verze, doporučujeme, abyste jako Windows hostovaný operační systém s Windows serverem 2012 (R2).

##<a name="next-steps"></a>Další postup
V článcích:

- [Nasazení virtuálních počítačů pro SAP NetWeaver v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Důležité informace týkající se nasazení Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Konfigurace infrastruktury SAP HANA a operací v Azure] (https://docs.microsoft.com/
- Azure/virtuální – počítače/úlohy/sap/hana-vm-operations)
