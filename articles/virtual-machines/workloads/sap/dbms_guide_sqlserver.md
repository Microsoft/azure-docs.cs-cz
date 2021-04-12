---
title: SQL Server nasazení Azure Virtual Machines DBMS pro úlohy SAP | Microsoft Docs
description: Nasazení DBMS v počítačích Azure Virtual Machines s SQL Serverem pro úlohy SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: Azure, SQL Server, SAP, AlwaysOn
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/08/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05a0aeb43b13dc4db28ca8c56fc668756a2a4510
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258720"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server nasazení Azure Virtual Machines DBMS pro SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

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

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
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




Tento dokument popisuje několik různých oblastí, které je potřeba vzít v úvahu při nasazování SQL Server pro úlohy SAP v Azure IaaS. Jako předběžnou podmínkou tohoto dokumentu byste měli mít přehled o dokumentech [pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_general.md) a další příručky v [dokumentaci ke službě SAP v dokumentaci k Azure](./get-started.md). 



> [!IMPORTANT]
> Rozsah tohoto dokumentu je verze Windows na SQL Server. SAP nepodporuje SQL Server verzi pro Linux pomocí žádného softwaru SAP. Dokument se nezabývá Microsoft Azure SQL Database, což je platforma jako nabídka služby Microsoft Azure platformy. Diskuze v tomto dokumentu se týká spuštění SQL Serverho produktu, který je známý pro místní nasazení v Azure Virtual Machines a využití infrastruktury jako služby pro Azure. Možnosti databáze a funkce mezi těmito dvěma nabídkami se liší a neměly by se vzájemně kombinovat. Viz také: <https://azure.microsoft.com/services/sql-database/>
> 
>

Obecně platí, že byste měli zvážit použití nejnovějších verzí SQL Server ke spuštění úlohy SAP v Azure IaaS. Nejnovější verze SQL Server nabízejí lepší integraci do některých služeb a funkcí Azure. Nebo máte změny, které optimalizují operace v infrastruktuře Azure IaaS.

Než budete pokračovat, doporučujeme, abyste si přečtěte článek [co je SQL Server v Azure Virtual Machines (Windows)] [ https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview ].

V následujících částech jsou části dokumentace pod výše uvedeným odkazem shrnuty a zmíněny. Jsou zde uvedeny konkrétní informace o SAP a některé koncepce jsou podrobněji popsány. Důrazně doporučujeme před načtením dokumentace týkající se SQL Server použít výše uvedenou dokumentaci.

V IaaS konkrétní informace, které byste měli znát, byste měli před pokračováním vymezit nějaké SQL Server:

* **Podpora verzí SQL**: pro zákazníky SAP se v Microsoft Azure virtuálním počítači podporuje SQL Server 2008 R2 a vyšší verze. Starší edice se nepodporují. Další podrobnosti najdete v tomto [příkazu obecné podpory](https://support.microsoft.com/kb/956893) . Obecně platí, že společnost Microsoft také podporuje SQL Server 2008. Vzhledem k významným funkcím pro SAP, které byly představeny s SQL Server 2008 R2, SQL Server 2008 R2 je minimální verze pro SAP. Obecně platí, že byste měli zvážit použití nejnovějších verzí SQL Server ke spuštění úlohy SAP v Azure IaaS. Nejnovější verze SQL Server nabízejí lepší integraci do některých služeb a funkcí Azure. Nebo máte změny, které optimalizují operace v infrastruktuře Azure IaaS. Proto je dokument omezen na SQL Server 2016 a SQL Server 2017.
* **Výkon SQL**: Microsoft Azure hostované Virtual Machines dobře spolupracuje s jinými nabídkami virtualizace veřejného cloudu, ale jednotlivé výsledky se můžou lišit. Projděte si článek [osvědčené postupy pro výkon SQL Server v Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).
* **Použití imagí z Azure Marketplace**: nejrychlejší způsob, jak nasadit nový virtuální počítač s Microsoft Azure, je použití image z Azure Marketplace. V Azure Marketplace jsou bitové kopie, které obsahují nejnovější verze SQL Server. Image, kde už SQL Server nainstalované, nejde hned použít pro aplikace SAP NetWeaver. Důvodem je výchozí kolace SQL Server je v těchto obrázcích nainstalovaná, a ne kolace, kterou vyžaduje systém SAP NetWeaver. Chcete-li tyto obrázky použít, Projděte si postup popsaný v části [použití SQL Server obrázku z Microsoft Azure Marketplace][dbms-guide-5.6]. 
*  **Podpora více instancí SQL Server v rámci jednoho virtuálního počítače Azure**: Tato metoda nasazení je podporovaná. Je však třeba mít na paměti omezení prostředků, zejména kolem šířky pásma sítě a úložiště používaného typu virtuálního počítače. Podrobné informace jsou k dispozici v článku [velikosti pro virtuální počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/sizes). Tato omezení kvóty můžou zabránit implementaci stejné architektury s více instancemi, protože můžete implementovat místní. Od konfigurace a rušení sdílení prostředků dostupných v rámci jednoho virtuálního počítače je potřeba vzít v úvahu stejné informace jako u místních.
*  **Více databází SAP v jedné instanci s jednou SQL Server v jednom virtuálním počítači**: výše jsou podporované konfigurace, jako jsou tyto. Informace o více databázích SAP sdílejících sdílené prostředky jedné instance SQL Server jsou stejné jako u místních nasazení. Další zachovat další omezení, jako je třeba počet disků, které se můžou připojit k určitému typu virtuálního počítače. Nebo omezení kvóty sítě a úložiště pro konkrétní typy virtuálních počítačů jako podrobné [velikosti pro virtuální počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/sizes). 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Doporučení ke struktuře virtuálních počítačů/VHD pro nasazení SQL Server souvisejících s SAP
V souladu s obecným popisem, operačním systémem, SQL Server spustitelnými soubory a v případě systémů SAP 2 vrstev by se měly spustitelné soubory SAP umístit nebo nainstalovat samostatné disky Azure. Většinou se většina SQL Server systémových databází nevyužívá na vysoké úrovni úloh SAP NetWeaver. Systémové databáze SQL Server (Master, msdb a model) ale musí být spolu s ostatními adresáři SQL Server na samostatném disku Azure. SQL Server tempdb by měl být buď umístěný na D:\u nonperisisted jednotku nebo na samostatném disku.


* U všech typů virtuálních počítačů certifikovaných pomocí SAP (viz poznámku SAP – Poznámka [1928533]), s výjimkou virtuálních počítačů řady a-Series, dat tempdb a souborů protokolů, se dají umístit do netrvalého D:\u. disky. 
* Pro starší verze SQL Server, kde SQL Server nainstaluje databázi tempdb s jedním datovým souborem ve výchozím nastavení se doporučuje použít více datových souborů databáze tempdb. Buďte D:\ svazky jednotek se liší v závislosti na typu virtuálního počítače. Pro přesné velikosti D:\ na jednotce různých virtuálních počítačů se podívejte na [velikost článků pro virtuální počítače s Windows v Azure](../../sizes.md).

Tyto konfigurace umožňují, aby databáze tempdb využila více místa a důležitější větší počet IOPS a šířka pásma úložiště, než je možné poskytnout systémovou jednotku. Netrvalý D:\ jednotka také nabízí lepší latenci I/O a propustnost (s výjimkou virtuálních počítačů řady A-Series). Chcete-li určit správnou velikost databáze tempdb, můžete zjistit velikosti databáze tempdb ve stávajících systémech. 

>[!NOTE]
> v případě, že datové soubory a soubor protokolu tempdb umístíte do složky na D:\ disk, který jste vytvořili, musíte zajistit, aby složka existovala po restartování virtuálního počítače. Od D:\ jednotka je nově inicializovaná po restartování virtuálního počítače. všechny struktury souborů a adresářů se vymažou. Možnost znovu vytvořit konečné adresářové struktury na D:\ v [tomto článku](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)je popsána jednotka před začátkem SQL Server služby.

Konfigurace virtuálního počítače, která běží SQL Server s databází SAP a kde jsou umístěné data tempdb a protokol tempdb na D:\ jednotka by vypadala takto:

![Diagram konfigurace jednoduchého disku virtuálního počítače pro SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

V diagramu výše se zobrazí jednoduchý případ. Eluded se v článku aspekty týkající se [nasazení azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md), typu úložiště Azure, počtu a velikosti disků závisí na různých faktorech. Obecně doporučujeme:

- Použití jednoho velkého svazku, který obsahuje SQL Server datových souborů. Důvodem pro tuto konfiguraci je to, že v reálném čase existuje mnoho databází SAP s různými velikostmi databázových souborů s různou vstupně-výstupní úlohou.
- Pokud je výkon dostatečně dobrý, použijte D:\drive pro databázi tempdb. Pokud se v databázi tempdb na D:\ omezí výkon celkového zatížení jednotka možná budete muset zvážit přesunutí databáze tempdb do samostatného úložiště Azure Premium Storage nebo Ultra diskových disků podle doporučení v [tomto článku](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).


### <a name="special-for-m-series-vms"></a>Speciální pro virtuální počítače řady M-Series
Pro virtuální počítače Azure řady M-Series se při použití Azure Akcelerátor zápisu můžou latence zápisu do transakčního protokolu snížit podle faktorů v porovnání s výkonem Azure Premium Storage. Proto byste měli nasadit Azure Akcelerátor zápisu pro virtuální pevné disky, které tvoří svazek pro SQL Server protokol transakcí. Podrobnosti lze přečíst v dokumentu [akcelerátor zápisu](../../how-to-enable-write-accelerator.md).
  

### <a name="formatting-the-disks"></a>Formátování disků
Pro SQL Server je velikost bloku NTFS pro disky obsahující SQL Server data a soubory protokolu 64 KB. Není nutné naformátovat D:\ disky. Tato jednotka je předem naformátovaná.

Aby se zajistilo, že obnovení nebo vytváření databází neinicializuje datové soubory tak, že se obsah souborů neinicializuje, měli byste se ujistit, že kontext uživatele, ke kterému je služba SQL Server spuštěná, má určité oprávnění. Tato oprávnění mají obvykle uživatelé ve skupině správce systému Windows. Pokud je služba SQL Server spuštěna v uživatelském kontextu uživatele, který není správcem systému Windows, je třeba přiřadit tohoto uživatele k **provádění úloh údržby svazku**.  Podívejte se na podrobnosti v tomto článku znalostní báze Microsoft Knowledge Base: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Dopad komprimace databáze
V konfiguracích, kde se může I/O šířka pásma stát omezením, by každá míra, která omezuje IOPS, mohla přispět k roztažení úlohy, která může běžet ve scénáři IaaS, jako je Azure. Pokud se to ještě neudělalo, doporučujeme, abyste před nahráním existující databáze SAP do Azure doporučili použití SQL Server komprese stránky od společnosti SAP i Microsoftu.

Doporučení k provedení komprese databáze před odesláním do Azure je podané dvěma důvody:

* Množství dat, která se mají nahrát, je nižší.
* Doba trvání spuštění komprese je kratší za předpokladu, že jeden může používat silnější hardware s více procesory nebo vyšší šířkou pásma I/O nebo menší latence vstupu/výstupu v místním prostředí.
* Menší velikosti databází můžou vést k menšímu množství nákladů na přidělení disku.

Komprese databáze funguje i v Virtual Machines Azure jako v místním prostředí. Další podrobnosti o tom, jak komprimovat existující databáze SAP NetWeaver SQL Server, najdete v článku [Vylepšený nástroj SAP Compression Tool MSSCOMPRESS](/archive/blogs/saponsqlserver/improved-sap-compression-tool-msscompress). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 a novější – ukládání souborů databáze přímo do Azure Blob Storage
SQL Server 2014 a novější verze si otevřou možnost ukládat soubory databáze přímo do úložiště objektů BLOB v Azure, aniž by to mělo obálku VHD. Zvláště při použití standardních Azure Storage nebo menších typů virtuálních počítačů tento typ nasazení umožňuje scénáře, ve kterých můžete překonat omezení IOPS, které by se vynutily omezeným počtem disků, které se dají připojit k některým menším typům virtuálních počítačů. Tento způsob nasazení funguje pro uživatelské databáze, ale ne pro systémové databáze SQL Server. Funguje taky pro data a soubory protokolů SQL Server. Pokud chcete databázi SQL Server SAP nasadit tímto způsobem namísto "zabalení" na VHD, pamatujte na to, že:

* Použitý účet úložiště musí být ve stejné oblasti Azure jako ta, která se používá k nasazení virtuálního počítače, SQL Server běží v.
* V této metodě nasazení se vztahují i na výše uvedené požadavky týkající se distribuce VHD na různé Azure Storage účty. Znamená počet vstupně-výstupních operací na základě limitů Azure Storageho účtu.
* Místo monitorování propustnosti vstupně-výstupních operací úložiště virtuálního počítače bude přenos dat objektů BLOB úložiště, které představují SQL Server data a soubory protokolů, zaúčtován do šířky pásma sítě určitého typu virtuálního počítače. Šířku pásma sítě a úložiště pro určitý typ virtuálního počítače najdete v článku [velikosti virtuálních počítačů s Windows v Azure](../../sizes.md).
* V důsledku nahrávání vstupně-výstupních operací se soubory přes kvótu sítě vytváříte hlavně kvótu úložiště, která využívá celkovou šířku pásma virtuálního počítače jenom částečně.
* Cíle vstupně-výstupních operací za sekundu a vstupně-výstupní propustnosti, které Azure Premium Storage pro různé velikosti disků, už neplatí. I v případě, že se objekty blob, které jste vytvořili, nacházejí v Premium Storage Azure. Cíle jsou popsány v článku o [vysoce výkonném Premium Storage a spravovaných discích pro virtuální počítače](../../disks-types.md#premium-ssd). V důsledku umístění SQL Server datových souborů a souborů protokolu přímo do objektů BLOB uložených v Azure Premium Storage se výkonnostní charakteristiky můžou v porovnání s virtuálními pevnými disky v Azure Premium Storage lišit.
* Ukládání do mezipaměti, které je k dispozici pro Azure Premium Storage disky, není k dispozici při umísťování SQL Server datových souborů přímo do objektů blob Azure.
* V případě virtuálních počítačů řady M-Series nejde Azure Akcelerátor zápisu použít pro podporu zápisů za běhu do SQL Server souboru protokolu transakcí. 

Podrobnosti o této funkci najdete v článku [SQL Server datových souborech v Microsoft Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)

Doporučením pro produkční systémy se vyhnete této konfiguraci a místo toho, abyste zvolili umístění SQL Server dat a souborů protokolů v Azure Premium Storage VHD místo přímo na objektech blob Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>Rozšíření fondu vyrovnávací paměti SQL Server 2014
SQL Server 2014 zavedlo novou funkci, která se nazývá [rozšíření fondu vyrovnávací paměti](/sql/database-engine/configure-windows/buffer-pool-extension). Tato funkce rozšiřuje fond vyrovnávací paměti SQL Server, který je uložený v paměti s mezipamětí druhé úrovně, která je založená na místní SSD serveru nebo virtuálního počítače. Rozšíření fondu vyrovnávací paměti umožňuje udržet větší pracovní sadu dat v paměti. V porovnání s přístupem ke službě Azure Storage úrovně Standard je přístup k rozšíření fondu vyrovnávací paměti, který je uložený v místní SSD virtuálního počítače Azure, mnohem rychlejší. Porovnání rozšíření fondu vyrovnávací paměti do Azure Premium Storage mezipaměti pro čtení, jak se doporučuje pro datové soubory SQL Server, ale u rozšíření fondu vyrovnávací paměti se neočekávají žádné významné výhody. Důvodem je, že ukládání do mezipaměti (SQL Server rozšíření fondu vyrovnávací paměti a Premium Storage mezipaměti pro čtení) používá místní disky výpočetního uzlu Azure.

Prostředí získaná v mezidobí s SQL Server rozšíření fondu vyrovnávací paměti s úlohou SAP je smíšená a stále neumožňují jasná doporučení týkající se toho, zda je lze použít ve všech případech. Ideálním případem je, že pracovní sada, kterou aplikace SAP vyžaduje, se zahodí do hlavní paměti. Azure mezitím nabízí virtuálním počítačům, které jsou s až 4 TB paměti, takže by měla být dosažitelná, aby fungovala v paměti. Proto je použití rozšíření fondu vyrovnávací paměti omezené na některé vzácné případy a nemělo by se jednat o běžný případ.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Předpoklady zálohování a obnovení pro SQL Server
Při nasazování SQL Server do Azure je nutné zkontrolovat metodologii zálohování. I v případě, že systém není produkční systém, databáze SAP hostovaná v SQL Server musí být pravidelně zálohována. Vzhledem k tomu, že Azure Storage udržuje tři image, zálohování je teď méně důležité v souvislosti s kompenzačním selháním úložiště. Důvod priority pro zachování správného plánu zálohování a obnovení je větší, než můžete kompenzovat logické a ruční chyby tím, že zajistíte možnosti obnovení k určitému bodu v čase. Cílem je, aby se databáze obnovila k určitému časovému bodu v čase nebo aby používala zálohy v Azure k osazení jiného systému zkopírováním stávající databáze. 

Pokud se chcete podívat na různé možnosti zálohování SQL Server v Azure, přečtěte si článek [zálohování a obnovení pro SQL Server v azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). Článek obsahuje několik různých možností.

### <a name="manual-backups"></a>Ruční zálohování
Máte několik možností, jak provést ruční zálohování pomocí:

1. Provádění konvenčních záloh SQL Server na přímo připojených discích Azure. Tato metoda má výhodu, že máte k dispozici rychlé zálohování pro systém obnovení a sestavování nových systémů jako kopií stávajících systémů SAP.
2.  SQL Server 2012 CU4 a novější může zálohovat databáze na adresu URL služby Azure Storage.
3.  File-Snapshot zálohy pro soubory databáze v Azure Blob Storage. Tato metoda funguje jenom v případě, že vaše SQL Server data a soubory protokolu jsou umístěné v úložišti objektů BLOB v Azure.

První metoda je dobře známá a používá se v mnoha případech i v místním světě. Ale ponechá vám úlohu pro řešení delšího umístění zálohy. Vzhledem k tomu, že nechcete uchovávat zálohy na 30 nebo více dní v místně připojené Azure Storage, je nutné buď použít služby Azure Backup nebo jiný nástroj pro zálohování a obnovení třetí strany, který zahrnuje správu přístupu a uchovávání informací pro vaše zálohy. Nebo můžete vytvořit rozsáhlý souborový server v Azure pomocí prostorů úložiště Windows.

Druhá metoda je popsána blíž v článku [SQL Server zálohování na adresu URL](../../../azure-sql/virtual-machines/windows/backup-restore.md). Různé verze SQL Server mají v této funkci nějaké odchylky. Proto byste měli rezervovat dokumentaci pro konkrétní SQL Server vydání. Je důležité si uvědomit, že v tomto článku jsou uvedena celá řada omezení. Máte možnost provést zálohování proti:

- Jeden objekt blob stránky Azure, který pak omezuje velikost zálohy na 1000 GB. Toto omezení také omezuje propustnost, kterou můžete dosáhnout.
- Vícenásobné (až 64) objekty blob bloku Azure, které umožňují teoretickou velikost zálohování na 12 TB. Testy se zákaznickými databázemi ale ukázaly, že maximální velikost zálohy může být menší než teoretická mez. V takovém případě zodpovídáte za správu uchovávání záloh a také přístupu k zálohám.


### <a name="automated-backup-for-sql-server"></a>Automatizovaná záloha pro SQL Server
Automatizované zálohování poskytuje službu Automatické zálohování pro edice SQL Server Standard a Enterprise běžící na virtuálním počítači s Windows v Azure. Tuto službu poskytuje [rozšíření agenta SQL Server IaaS](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md), které se automaticky instaluje do SQL Server imagí virtuálních počítačů s Windows v Azure Portal. Pokud nasadíte vlastní image operačních systémů pomocí SQL Server nainstalovány, je nutné nainstalovat rozšíření virtuálního počítače samostatně. Postup je popsaný v tomto [článku](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md).

Další podrobnosti o možnostech této metody najdete v těchto článcích:

- SQL Server 2014: [automatizované zálohování pro SQL Server 2014 Virtual Machines (Správce prostředků)](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)
- SQL Server 2016/2017: [automatizované zálohování v2 pro Azure Virtual Machines (Správce prostředků)](../../../azure-sql/virtual-machines/windows/automated-backup.md)

Podívejte se do dokumentace a podívejte se, že funkce s novějšími verzemi SQL Server vylepšila. Další podrobnosti o SQL Server automatizované zálohy jsou vydávány v článku [SQL Server spravované zálohování na Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure). Teoretické omezení velikosti zálohování je 12 TB.  Automatizované zálohování může být dobrou metodou pro velikosti zálohování až na 12 TB. Vzhledem k tomu, že je do paralelního zápisu více objektů blob, můžete očekávat propustnost větší než 100 MB/s. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Azure Backup pro virtuální počítače s SQL Server
Tato nová metoda SQL Server zálohování se nabízí jako verze Public Preview od června 2018 po Azure Backup Services. Metoda pro zálohování SQL Server je stejná jako u jiných nástrojů třetích stran, a to pomocí SQL Server rozhraní VSS/VDI streamování záloh do cílového umístění. V tomto případě je cílovým umístěním trezor služby Azure Recovery Services.

K dispozici je více než podrobný popis této metody zálohování, který přináší spoustu výhod centrálních záložních konfigurací, monitorování a správy, které jsou k dispozici [zde](../../../backup/backup-azure-sql-database.md). 


### <a name="third-party-backup-solutions"></a>Řešení zálohování třetích stran
Pro naprosto celou řadu zákazníků SAP neexistovala možnost začít znovu a zavádět kompletní nová řešení zálohování pro součást SAP na platformě, která byla spuštěna v Azure. V důsledku toho se existující řešení zálohování potřebují použít a rozšířit do Azure. Rozšíření stávajících řešení pro zálohování do Azure se obvykle dobře osvědčilo s většinou hlavních dodavatelů v tomto prostoru. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Použití bitové kopie SQL Server mimo Microsoft Azure Marketplace
Microsoft nabízí virtuální počítače ve Azure Marketplace, které už obsahují verze SQL Server. Pro zákazníky SAP, kteří vyžadují licence pro SQL Server a Windows, může být použití těchto imagí příležitostí k povýšení potřeby licencí, a to vyvoláním virtuálních počítačů s SQL Server již nainstalovanými. Aby bylo možné používat takové obrázky pro SAP, je třeba provést následující:

* Verze SQL Server bez vyhodnocení získají vyšší náklady než virtuální počítač, který je jenom Windows nasazený z Azure Marketplace. V těchto článcích můžete porovnat ceny: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> a <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/> . 
* Můžete použít jenom SQL Server verze, které SAP podporuje.
* Kolace instance SQL Server, která je nainstalovaná ve virtuálních počítačích nabízených v Azure Marketplace, není kolace SAP NetWeaver vyžaduje spuštění instance SQL Server. Kolaci můžete změnit, i když s pokyny v následující části.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Změna kolace SQL Server pro virtuální počítač se systémem Windows/SQL Server
Vzhledem k tomu, že SQL Server imagí v Azure Marketplace nejsou nastavené tak, aby používaly kolaci, kterou vyžadují aplikace SAP NetWeaver, je nutné ji hned po nasazení změnit. V případě SQL Server můžete tuto změnu kolace provést pomocí následujících kroků, jakmile se virtuální počítač nasadí, a správce se může přihlásit k nasazenému virtuálnímu počítači:

* Otevřete okno příkazového řádku systému Windows jako správce.
* Změňte adresář na C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Spusťte příkaz: Setup.exe/QUIET za akci = REBUILDDATABASE/INSTANCENAME = MSSQLSERVER/SQLSYSADMINACCOUNTS = `<local_admin_account_name`>/SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> je účet, který byl definován jako účet správce při prvním nasazení virtuálního počítače pomocí galerie.

Proces by měl trvat jen několik minut. Chcete-li se ujistit, zda byl krok dokončen se správným výsledkem, proveďte následující kroky:

* Otevřete sadu SQL Server Management Studio.
* Otevřete okno dotazu.
* Spusťte sp_helpsort příkazu v hlavní databázi SQL Server.

Požadovaný výsledek by měl vypadat takto:

```output
Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data
```

Pokud je výsledek jiný, ukončete nasazení SAP a zjistěte, proč příkaz setup nefungoval podle očekávání. Nasazení aplikací SAP NetWeaver do instance SQL Server s jinou znakovou stránkou SQL Server, než je uvedená výše, **není** podporováno.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server High-Availability pro SAP v Azure
Pomocí SQL Server v nasazeních Azure IaaS pro SAP máte několik různých možností, jak přidat k nasazení vrstvy DBMS vysoce dostupné. Jak už bylo popsáno v tématu [požadavky na nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md) , poskytuje Azure pro jeden virtuální počítač různou SLA a dvojici virtuálních počítačů nasazených v sadě dostupnosti Azure. Předpokladem je, že budete mít k dispozici aktuální smlouvu SLA pro nasazení v produkčním prostředí, která vyžadují nasazení v Azure Availability Sets. V takovém případě musíte nasadit minimálně dva virtuální počítače v takové skupině dostupnosti. V jednom virtuálním počítači se spustí instance Active SQL Server. Druhý virtuální počítač spustí pasivní instanci.

### <a name="sql-server-clustering-using-windows-scale-out-file-server-or-azure-shared-disk"></a>SQL Server clusteringu pomocí souborového serveru se škálováním na více systémů nebo sdíleného disku Azure
V systému Windows Server 2016 společnost Microsoft představila [prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/storage-spaces-direct-overview). V závislosti na nasazení Prostory úložiště s přímým přístupem je podpora clusteringu s SQL Server FCI obecně podporovaná. Azure také nabízí [sdílené disky Azure](../../disks-shared-enable.md?tabs=azure-cli) , které se dají použít pro clustering Windows. Pro úlohy SAP nepodporujeme tyto možnosti HA. 

### <a name="sql-server-log-shipping"></a>Přenos protokolu SQL Server
Jednou z metod vysoké dostupnosti (HA) je SQL Server přesouvání protokolu. Pokud virtuální počítače, které se podílejí na konfiguraci vysoké dostupnosti, mají překlad IP adres, není k dispozici žádný problém. Nastavení v Azure se neliší od nastavení, které se provádí v místním prostředí souvisejícím s nastavením přesouvání protokolu, a principy přenosu protokolu. Podrobnosti o přenosu protokolu SQL Server najdete v článku [o přesouvání protokolu (SQL Server)](/sql/database-engine/log-shipping/about-log-shipping-sql-server).

Funkce přenosu protokolu SQL Server se v Azure použila k dosažení vysoké dostupnosti v rámci jedné oblasti Azure. V následujících scénářích se ale zákazníci se systémem SAP používali při přesouvání protokolu ve spojení s Azure:

- Scénáře zotavení po havárii z jedné oblasti Azure do jiné oblasti Azure
- Konfigurace zotavení po havárii z místního prostředí do oblasti Azure
- Scénáře vyjmutí z místního prostředí do Azure. V těchto případech se přenos do protokolu používá k synchronizaci nového nasazení DBMS v Azure s probíhajícím provozním systémem v místním prostředí. V době rozmístění je výroba vypnutá a je zajištěná, že se poslední a poslední zálohy protokolu transakcí přenesly do nasazení Azure DBMS. Pak se nasazení Azure DBMS otevře v produkčním prostředí.  



### <a name="database-mirroring"></a>Zrcadlení databáze
Zrcadlení databáze podle podpor SAP (viz SAP Note [965908]) spoléhá na definování partnerského serveru pro převzetí služeb při selhání v připojovacím řetězci SAP. U různých případů předpokládáme, že tyto dva virtuální počítače jsou ve stejné doméně a že SQL Server kontext uživatele jsou spuštěné v rámci uživatele domény a mají dostatečná oprávnění pro tyto dvě instance SQL Server. Proto se nastavení zrcadlení databáze v Azure neliší mezi typickou konfigurací místního nastavení nebo konfigurace.

Od nasazení Cloud-Only nejjednodušší způsob, jak mít v Azure další nastavení domény, aby tyto virtuální počítače s DBMS (a ideálním vyhrazeným virtuálním počítačům SAP) byly v jedné doméně.

Pokud není doména možná, může použít taky certifikáty pro koncové body zrcadlení databáze, jak je popsáno zde: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Kurz nastavení zrcadlení databáze v Azure najdete tady: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>AlwaysOn SQL Serveru
Stejně jako je podpora pro SAP místně podporovaná (viz článek SAP Note [1772688]), podporuje se v kombinaci s SAP v Azure. K nasazení naslouchacího procesu skupiny dostupnosti SQL Server (nemusíte se zaměňovat se skupinou dostupnosti Azure) dochází k nějakým zvláštním hlediskům, protože Azure v tuto chvíli neumožňuje vytvoření objektu AD/DNS, protože je možné ho použít místně. Proto je nutné provést některé jiné kroky instalace pro překonání konkrétního chování Azure.

Některé okolnosti použití naslouchacího procesu skupiny dostupnosti:

* Pomocí naslouchacího procesu skupiny dostupnosti je možné používat jenom Windows Server 2012 nebo novější jako hostovaný operační systém virtuálního počítače. V případě systému Windows Server 2012 je nutné se ujistit, že je tato oprava použita: <https://support.microsoft.com/kb/2854082> 
* V systému Windows Server 2008 R2 Tato oprava neexistuje a je možné ji vždy použít stejným způsobem jako zrcadlení databáze tím, že v řetězci připojení zadáte partnerský server pro převzetí služeb při selhání (provede se pomocí výchozího parametru SAP. pfl databáze/MSS/Server – viz SAP Note [965908]).
* Při použití naslouchacího procesu skupiny dostupnosti musí být virtuální počítače databáze připojené k vyhrazené Load Balancer. Aby se zabránilo tomu, že Azure přiřadí nové IP adresy v případech, kdy se oba virtuální počítače vypínají, má jedna z nich přiřadit statické IP adresy k síťovým rozhraním těchto virtuálních počítačů v konfiguraci služby Always On (definování statické IP adresy je popsané v [tomto][virtual-networks-reserved-private-ip] článku).
* Při sestavování konfigurace clusteru služby WSFC, kde cluster potřebuje speciální přiřazenou IP adresu, se vyžadují speciální kroky, protože Azure s jeho aktuální funkcí přiřadí stejný název clusteru jako uzel, ve kterém je cluster vytvořený. Toto chování znamená, že je potřeba provést ruční krok, který clusteru přiřadí jinou IP adresu.
* Naslouchací proces skupiny dostupnosti se vytvoří v Azure s koncovými body TCP/IP, které jsou přiřazené k virtuálním počítačům s primární a sekundární replikou skupiny dostupnosti.
* Je možné, že tyto koncové body budete muset zabezpečit pomocí seznamů ACL.

Podrobná dokumentace k nasazení Always On s SQL Server v seznamech virtuálních počítačů Azure:

- [Představujeme SQL Server skupiny dostupnosti Always On na virtuálních počítačích Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
- [Nakonfigurujte skupinu dostupnosti Always On na virtuálních počítačích Azure v různých oblastech](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
- [Nakonfigurujte nástroj pro vyrovnávání zatížení pro skupinu dostupnosti Always On v Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).

>[!NOTE]
> Pokud konfigurujete Nástroj pro vyrovnávání zatížení Azure pro virtuální IP adresu naslouchacího procesu skupiny dostupnosti, ujistěte se, že je DirectServerReturn nakonfigurovaný. konfigurace této možnosti omezí latenci odezvy sítě mezi aplikační vrstvou SAP a vrstvou DBMS. 

V případě nasazení úloh SAP v Azure je funkce Always On SQL Server k dispozici nejčastěji používané funkci vysoké dostupnosti a zotavení po havárii. Většina zákazníků používá pro vysokou dostupnost v rámci jedné oblasti Azure vždycky zapnuté. Pokud je nasazení omezené jenom na dva uzly, máte k dispozici dvě možnosti připojení:

- Pomocí naslouchacího procesu skupiny dostupnosti. Pomocí naslouchacího procesu skupiny dostupnosti je nutné nasadit nástroj Azure Load Balancer. Tímto způsobem je výchozí metodou nasazení. Aplikace SAP by se nakonfigurovaly tak, aby se připojovaly k naslouchacímu procesu skupiny dostupnosti, a ne k jednomu uzlu.
- Použití parametrů připojení SQL Server zrcadlení databáze. V takovém případě je třeba nakonfigurovat připojení aplikací SAP tak, aby názvy obou uzlů byly pojmenovány. Přesné podrobnosti o takové konfiguraci na straně SAP jsou popsány v části SAP Note [#965908](https://launchpad.support.sap.com/#/notes/965908). Pomocí této možnosti nemusíte konfigurovat naslouchací proces skupiny dostupnosti. A s tím, že žádný nástroj pro vyrovnávání zatížení Azure pro SQL Server vysokou dostupnost. V důsledku toho je latence sítě mezi aplikační vrstvou SAP a vrstvou DBMS nižší, protože příchozí provoz do instance SQL Server není směrován prostřednictvím nástroje pro vyrovnávání zatížení Azure. Ale odvolání této možnosti funguje jenom v případě, že omezíte skupinu dostupnosti tak, aby zahrnovala dvě instance. 

Mnoho zákazníků využívá funkci SQL Server Always On pro zotavení po havárii mezi oblastmi Azure. Několik zákazníků používá také možnost provádět zálohy ze sekundární repliky. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server transparentní šifrování dat
Existuje mnoho zákazníků, kteří používají SQL Server [transparentní šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) při nasazování svých databází SAP SQL Server v Azure. Funkce SQL Server TDE je plně podporovaná pomocí SAP (viz poznámka k SAP [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Použití SQL Server TDE
V případech, kdy provedete heterogenní migraci z jiného systému DBMS, spuštěného v místním prostředí, na Windows/SQL Server běžící v Azure, byste měli svou prázdnou cílovou databázi vytvořit v SQL Server před časem. Jako další krok byste použili SQL Server funkce TDE. I když stále pracujete v místním produkčním systému. Důvod, který chcete v této sekvenci provést, je, že proces šifrování prázdné databáze může trvat poměrně. Procesy importu SAP pak naimportují data do šifrované databáze během fáze výpadku. Režie importování do šifrované databáze má za následek kratší dobu než šifrování databáze po fázi exportu v časové fázi. Při pokusu o použití TDE s úlohou SAP běžícími nad databází se provedla negativní prostředí. Proto doporučení zpracovává nasazení TDE jako aktivity, kterou je třeba provést bez úlohy SAP v konkrétní databázi.

V případech, kdy přesunete databáze SAP SQL Server z místního prostředí do Azure, doporučujeme, abyste provedli testování infrastruktury, kterou můžete použít k nejrychlejšímu šifrování. V takovém případě mějte na paměti tyto skutečnosti:

- Nelze definovat, kolik vláken se používá k aplikování šifrování dat do databáze. Počet vláken je v nejvyšší části závislý na počtu diskových svazků, při kterých se SQL Server data a soubory protokolu rozdělují. Znamená více různých svazků (písmena jednotek), další vlákna budou zacházet paralelně, aby provedla šifrování. Taková konfigurace je v konfliktu s dřívějším návrhem konfigurace disku při vytváření jednoho nebo menšího počtu prostorů úložiště pro SQL Server databázových souborů na virtuálních počítačích Azure. Konfigurace s malým počtem svazků by vedla k malému počtu vláken, která spouští šifrování. Šifrování jednoho vlákna je čteno z 64 rozsahů KB, je zašifrováno a pak zapisuje záznam do souboru protokolu transakcí a informuje o tom, zda byl rozsah zašifrován. V důsledku toho je zatížení protokolu transakcí střední.
- Ve starších verzích SQL Server se komprese záloh nedostala už při šifrování databáze SQL Server. Toto chování může vyvíjet problém, když se vašemu plánu zašifroval vaše databáze SQL Server místně, a pak zkopírujete zálohu do Azure a obnovíte databázi v Azure. Komprese SQL Server Backup obvykle dosahuje kompresního poměru v faktoru 4.
- S SQL Server 2016 SQL Server zavedeny nové funkce, které umožňují efektivně komprimovat šifrované databáze. Další podrobnosti najdete v [tomto blogu](/archive/blogs/sqlcat/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases) .
 
Zpracovává se aplikace šifrování TDE jenom v případě, že je jenom málo úloh SAP, měli byste testovat v konkrétní konfiguraci, ať už je lepší použít TDE pro místní databázi SAP nebo v Azure. V Azure máte jistě větší flexibilitu při zřizování infrastruktury a po TDE použití zmenšíte infrastrukturu.

### <a name="using-azure-key-vault"></a>Použití Azure Key Vault
Azure nabízí službu [Key Vault](https://azure.microsoft.com/services/key-vault/) pro ukládání šifrovacích klíčů. SQL Server na druhé straně nabízí konektor pro využití Azure Key Vault jako úložiště pro certifikáty TDE.

Další podrobnosti o použití Azure Key Vault pro SQL Server TDE seznamy jako:

- [Rozšiřitelná Správa klíčů pomocí Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- [SQL Server TDE rozšiřitelná Správa klíčů s použitím kroků Azure Key Vault-Setup](/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault).
- [Řešení potíží s Konektor SQL Serveru &](/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting).
- [Další otázky od zákazníků o SQL Server transparentní šifrování dat – TDE + Azure Key Vault](/archive/blogs/saponsqlserver/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault).


>[!IMPORTANT]
>Pomocí SQL Server TDE, zejména s trezorem klíčů Azure, se doporučuje použít nejnovější opravy SQL Server 2014, SQL Server 2016 a SQL Server 2017. Důvodem je, že na základě zpětné vazby od zákazníků byly pro kód aplikovány optimalizace a opravy. Podívejte se například na [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Obecné SQL Server pro SAP v Azure – souhrn
V této příručce máte spoustu doporučení a před plánováním nasazení Azure doporučujeme si ho přečíst víckrát než jednou. Obecně platí, že byste ale měli postupovat podle nejdůležitějšího obecného systému DBMS na doporučení specifických pro Azure:

1. Použijte nejnovější verzi systému DBMS, například SQL Server 2017, která má nejvíc výhod v Azure. 
2. Pečlivě Naplánujte systém SAP na šířku v Azure, abyste si vyrovnali rozložení datových souborů a omezení Azure:
   * Nemáte příliš mnoho disků, ale stačí, abyste se ujistili, že budete mít přístup k požadovaným IOPS.
   * Pokud nepoužíváte Managed Disks, pamatujte na to, že IOPS jsou taky omezené na účet Azure Storage a že účty úložiště jsou omezené v rámci každého předplatného Azure ([Další podrobnosti][azure-resource-manager/management/azure-subscription-service-limits]). 
   * Rozkládat na disky jenom v případě, že potřebujete dosáhnout vyšší propustnosti.
3. Nikdy neinstalujte software ani neumísťujte žádné soubory, které vyžadují trvalost na D:\. jednotka, protože je netrvalá a cokoli na této jednotce se při restartování Windows ztratí.
4. Nepoužívejte ukládání disku do mezipaměti pro službu Azure Storage úrovně Standard.
5. Nepoužívejte Azure geograficky replikované účty úložiště Azure úrovně Standard.  Pro úlohy DBMS použijte místně redundantní.
6. K replikaci dat databáze použijte řešení HA/DR od dodavatele DBMS.
7. Vždy používejte překlad adres IP, nespoléhejte na IP adresy.
8. Pomocí SQL Server TDE použijte nejnovější SQL Server opravy.
9. Použijte nejvyšší možnou kompresi databáze. Což je komprese stránky pro SQL Server.
10. Pomocí SQL Serverch imagí z Azure Marketplace buďte opatrní. Použijete-li SQL Server jeden, je nutné před instalací jakéhokoli systému SAP NetWeaver změnit kolaci instance.
11. Nainstalujte a nakonfigurujte monitorování hostitele SAP pro Azure, jak je popsáno v [Průvodci nasazením][deployment-guide].


## <a name="next-steps"></a>Další kroky
Přečtěte si článek 

- [Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md)