---
title: Nasazení DBMS virtuálních počítačů SQL Server Azure pro úlohy SAP | Dokumenty společnosti Microsoft
description: Nasazení DBMS v počítačích Azure Virtual Machines s SQL Serverem pro úlohy SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a0fbed1f4dd62b2d75d39f475d2fe124c55a2b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645799"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Nasazení DBMS virtuálních počítačů SQL Server Azure pro SAP NetWeaver

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
[azure-ps]:/powershell/azureps-cmdlets-docs
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
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
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
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
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




Tento dokument pokrývá několik různých oblastí, které je třeba zvážit při nasazování úloh SQL Server pro SAP v Azure IaaS. Jako podmínku pro tento dokument byste si měli přečíst dokument [Aspekty nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](dbms_guide_general.md) a další příručky v [pracovním vytížení SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)v dokumentaci Azure . 



> [!IMPORTANT]
> Rozsah tohoto dokumentu je verze systému Windows na serveru SQL Server. SAP nepodporuje linuxovou verzi SQL Serveru s žádným softwarem SAP. Dokument nepopisuje Microsoft Azure SQL Database, což je nabídka platformy jako služby platformy Microsoft Azure. Diskuse v tomto článku se o spuštění produktu SQL Server, protože je známý pro místní nasazení ve virtuálních počítačích Azure, využití infrastruktury jako služby schopnosti Azure. Databázové schopnosti a funkce mezi těmito dvěma nabídkami se liší a neměly by být vzájemně směšovány. Viz také:<https://azure.microsoft.com/services/sql-database/>
> 
>

Obecně byste měli zvážit použití nejnovějších verzí SERVERU SQL Server ke spuštění úlohy SAP v Azure IaaS. Nejnovější verze SQL Serveru nabízejí lepší integraci do některých služeb a funkcí Azure. Nebo mají změny, které optimalizují operace v infrastruktuře Azure IaaS.

Před pokračováním doporučujeme [tuto][virtual-machines-sql-server-infrastructure-services] dokumentaci zkontrolovat.

V následujících oddílech jsou agregovány a uvedeny části dokumentace pod výše uvedeným odkazem. Specifika kolem SAP jsou také zmíněny a některé koncepty jsou popsány podrobněji. Důrazně se však doporučuje pracovat prostřednictvím dokumentace výše před první před čtením sql server specifické dokumentace.

V aplikaci IaaS jsou některé informace specifické pro SQL Server, které byste měli vědět, než budete pokračovat:

* **Podpora verzí SQL:** Pro zákazníky SAP je sql server 2008 R2 a vyšší podporován ve virtuálním počítači Microsoft Azure. Dřívější edice nejsou podporovány. Další podrobnosti naleznete v tomto [obecném prohlášení](https://support.microsoft.com/kb/956893) o podpoře. Obecně platí, že SQL Server 2008 je podporován také společností Microsoft. Nicméně vzhledem k významné funkce pro SAP, který byl představen s SQL Server 2008 R2, SQL Server 2008 R2 je minimální verze pro SAP. Obecně byste měli zvážit použití nejnovějších verzí SERVERU SQL Server ke spuštění úlohy SAP v Azure IaaS. Nejnovější verze SQL Serveru nabízejí lepší integraci do některých služeb a funkcí Azure. Nebo mají změny, které optimalizují operace v infrastruktuře Azure IaaS. Papír je proto omezen na SQL Server 2016 a SQL Server 2017.
* **Sql Performance**: Virtuální počítače hostované v Microsoft Azure fungují dobře ve srovnání s jinými nabídkami virtualizace veřejného cloudu, ale jednotlivé výsledky se mohou lišit. Podívejte se na článek [Doporučené postupy výkonu pro SQL Server ve virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Použití ibi z Azure Marketplace**: Nejrychlejší způsob, jak nasadit nový virtuální počítač Microsoft Azure, je použít image z Azure Marketplace. Existují image na Azure Marketplace, které obsahují nejnovější verze SQL Serveru. Bitové kopie, na kterých je sql server již nainstalován, nelze okamžitě použít pro aplikace SAP NetWeaver. Důvodem je výchozí sql server řazení je nainstalován v rámci těchto bitových kopií a nikoli řazení vyžaduje systémy SAP NetWeaver. Chcete-li tyto bitové kopie používat, zkontrolujte kroky zdokumentované v kapitole [Použití bitové kopie serveru SQL Server z webu Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Doporučení pro strukturu virtuálního počítače a virtuálního pevného disku pro nasazení SQL Serveru související se systémem SAP
V souladu s obecným popisem by měly být spustitelné soubory serveru SQL Server umístěny nebo\)nainstalovány do systémové jednotky disku s os virtuálním počítačem (jednotka C: .  Většina systémových databází serveru SQL Server obvykle není využívána na vysoké úrovni zatížením SAP NetWeaver. V důsledku toho systémové databáze SQL Server (master, msdb a model) může zůstat na C:\ také disk. Výjimkou by měl být tempdb, který v případě úloh SAP může vyžadovat buď vyšší objem dat nebo vstupně-v.I operace svazku. Vstupně-založené na prodeji, které by neměly být použity pro virtuální pevný disk operačního systému. U těchto systémů by měly být provedeny následující kroky:


* Se všemi typy virtuálních počítačů certifikovaných SAP (viz SAP Note [1928533]), s výjimkou virtuálních počítačů řady A, dat tempdb a souborů protokolu lze umístit na netrvalé D:\ Jednotky. 
* Nicméně, je doporučeno používat více datových souborů tempdb. Uvědomte si D:\ objemy jednotek se liší v závislosti na typu virtuálního počítače. Pro přesné velikosti D:\ jednotky různých virtuálních počítačů, podívejte se na článek [Velikosti pro virtuální počítače s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Tyto konfigurace umožňují tempdb spotřebovávat více místa, než je schopen poskytnout systémová jednotka. Netrvalé D:\ jednotka také nabízí lepší latenci vstupně-výstupních služeb a propustnost (s výjimkou virtuálních zařízení řady A). Chcete-li určit správnou velikost tempdb, můžete zkontrolovat velikosti tempdb ve stávajících systémech. 

>[!NOTE]
> v případě, že umístíte datové soubory tempdb a soubor protokolu do složky na D:\ jednotka, kterou jste vytvořili, musíte se ujistit, že složka existuje po restartování virtuálního počítače. Vzhledem k tomu, D:\ jednotka je čerstvě inicializována po restartování virtuálního počítače všechny struktury souborů a adresářů jsou vymazány. Možnost znovu vytvořit případné adresářové struktury na D:\ před zahájením služby SQL Server je dokumentováno v [tomto článku](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Konfigurace virtuálního počítače, který spouští SQL Server s databází SAP a kde jsou umístěny data tempdb a soubor protokolu tempdb na D:\ disk bude vypadat takto:

![Diagram jednoduché konfigurace disku virtuálního počítače pro SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Výše uvedený diagram zobrazuje jednoduchý případ. Jak se vyhnul v článku [Důležité informace pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md), počet a velikost disků úložiště Premium závisí z různých faktorů. Obecně však doporučujeme:

- Použití prostorů úložiště k vytvoření jednoho nebo malého počtu svazků, které obsahují datové soubory serveru SQL Server. Důvodem této konfigurace je, že v reálném životě existuje mnoho databází SAP s různými velikostmi databázových souborů s různými vstupně-v. úlohy.
- Použití prostorů úložiště k zadání dostatečného množství viops a pro soubor protokolu transakcí serveru SQL Server. Potenciální úloha VOPS je často hlavní linií pro velikost svazku transakčního protokolu a nikoli potenciálního objemu svazku transakcí serveru SQL Server.
- Použijte D:\drive pro tempdb tak dlouho, dokud výkon je dost dobrý. Pokud je celkový pracovní vytížení omezen výkon tmepdb se nachází na D:\ jednotka, kterou budete muset zvážit přesunout tempdb na samostatné disky úložiště Premium, jak je doporučeno v [tomto článku](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Speciální pro virtuální měna řady M
Pro virtuální počítač Azure řady M může být latence zápisu do transakčního protokolu snížena o faktory ve srovnání s výkonem úložiště Azure Premium při použití akcelerátoru zápisu Azure. Proto byste měli nasadit Azure Write Accelerator pro virtuální pevné disky, které tvoří svazek pro transakční protokol serveru SQL Server. Podrobnosti si můžete přečíst v dokumentu [Akcelerátor zápisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>Formátování disků
Pro SQL Server by velikost bloku NTFS pro disky obsahující data serveru SQL Server a soubory protokolu měla být 64 KB. Není třeba formátovat d:\ Jednotky. Tato jednotka je dodávána předformátovaná.

Chcete-li se ujistit, že obnovení nebo vytváření databází není inicializace datových souborů vynulováním obsahu souborů, měli byste se ujistit, že kontext uživatele sql server služba je spuštěna v má určité oprávnění. Obvykle uživatelé ve skupině Správce systému Windows mají tato oprávnění. Pokud je služba SQL Server spuštěna v uživatelském kontextu uživatele, který není správcem systému Windows, je třeba tomuto uživateli přiřadit uživatelské právo **Provádět úlohy hromadné údržby**.  Podrobnosti naleznete v tomto článku znalostní báze Microsoft Knowledge Base:<https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Dopad komprese databáze
V konfiguracích, kde vstupně-v., šířka pásma se může stát omezujícím faktorem, může každé opatření, které snižuje vstupně-maily, pomoci roztáhnout úlohu, kterou lze spustit ve scénáři IaaS, jako je Azure. Proto pokud ještě není hotovo, použití komprese SQL Server PAGE doporučuje SAP i Microsoft před nahráním existující databáze SAP do Azure.

Doporučení provést kompresi databáze před odesláním do Azure je dáno ze dvou důvodů:

* Množství dat, která mají být odeslána, je nižší.
* Doba trvání spuštění komprese je kratší za předpokladu, že lze použít silnější hardware s více procesory nebo vyšší vstupně-výstupní šířku pásma nebo menší latence vstupně-výstupních prostor v místním prostředí.
* Menší velikosti databáze mohou vést k nižším nákladům na přidělení disku.

Komprese databáze funguje stejně dobře ve virtuálních počítačích Azure stejně jako v místním prostředí. Další podrobnosti o komprimování existujících databází SAP NetWeaver SQL Server naleznete v článku [Vylepšený kompresní nástroj SAP MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 a novější – ukládání databázových souborů přímo do úložiště objektů blob Azure
SQL Server 2014 a novější verze otevřít možnost ukládání databázových souborů přímo na Azure Blob Store bez 'obálky' virtuálního pevného disku kolem nich. Zejména s použitím standardního úložiště Azure nebo menších typů virtuálních počítačů tento typ nasazení umožňuje scénáře, kde můžete překonat limity viops, které by byly vynuceny omezeným počtem disků, které lze připojit k některým menším typům virtuálních počítačů. Tento způsob nasazení funguje pro uživatelské databáze však není pro systémové databáze SQL Server. Funguje také pro datové a log soubory SQL Server. Pokud chcete nasadit databázi SAP SQL Server tímto způsobem namísto "zabalení" do virtuálních discích, mějte na paměti:

* Použitý účet úložiště musí být ve stejné oblasti Azure jako ten, který se používá k nasazení VM SQL Server běží v.
* Důležité informace uvedené dříve týkající se distribuce virtuálních disků přes různé účty úložiště Azure platí i pro tuto metodu nasazení. Znamená, že vstupně-v., které jsou vstupně-nosné, se započítávají do limitů účtu úložiště Azure.
* Místo účtování proti kvótě vstupně-vstupně-vstupně-videa virtuálního zařízení se bude přenos proti objektům BLOB úložiště představujícím data serveru SQL Server a soubory protokolu započítávat do šířky pásma sítě virtuálního zařízení konkrétního typu virtuálního zařízení. Šířka pásma sítě a úložiště určitého typu virtuálního počítače nahlédněte do článku [Velikosti virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* V důsledku odesílání vstupně-out souboru prostřednictvím kvóty sítě, jste podložení kvóty úložiště většinou a s tím používat celkovou šířku pásma virtuálního zařízení pouze částečně.
* Cíle výkonu vstupně-up a vstupně-výkon, které má Azure Premium Storage pro různé velikosti disků, už se nepoužívají. I v případě, že objekty BLOB, které jste vytvořili, jsou umístěny ve službě Azure Premium Storage. Cíle jsou zdokumentovány v článku [Vysoce výkonné úložiště Premium storage a spravované disky pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). V důsledku umístění datových souborů sql serveru a souborů protokolu přímo na objekty BLOB, které jsou uložené ve službě Azure Premium Storage, se výkonové charakteristiky mohou lišit ve srovnání s virtuálními servery v Azure Storage.
* Ukládání do mezipaměti založené na hostiteli, jak je k dispozici pro disky úložiště Azure Premium, není dostupné při umísťování datových souborů SQL Serveru přímo na objekty BLOB Azure.
* Na virtuálních počítačích řady M nelze akcelerátor zápisu Azure použít k podpoře zápisů podmilisekund proti souboru protokolu transakcí serveru SQL Server. 

Podrobnosti o této funkci naleznete v článku [datové soubory serveru SQL Server v Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Doporučení pro produkční systémy je vyhnout se této konfiguraci a spíše zvolit umístění dat sql serveru a souborů protokolu v Virtuálních řadičích zabezpečení úložiště Azure Namísto přímo na objektech BLOB Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>Rozšíření fondu vyrovnávací paměti serveru SQL Server 2014
SQL Server 2014 představil novou funkci, která se nazývá [rozšíření fondu vyrovnávací paměti](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Tato funkce rozšiřuje fond vyrovnávacích pamětí serveru SQL Server, který je uložen v paměti s mezipamětí druhé úrovně, která je zálohována místními ssd disky serveru nebo virtuálního soudu. Rozšíření fondu vyrovnávací paměti umožňuje zachovat větší pracovní sadu dat "v paměti". Ve srovnání s přístupem k Azure Standard Storage je přístup do rozšíření fondu vyrovnávacích pamětí, který se ukládá na místních ssd znamená virtuální počítač Azure, je mnoho faktorů rychlejší. Porovnání rozšíření fondu vyrovnávací paměti na mezipaměť pro čtení úložiště Azure Premium, jak je doporučeno pro datové soubory serveru SQL Server, se pro rozšíření fondu vyrovnávací paměti neočekávají žádné významné výhody. Důvodem je, že obě mezipaměti (rozšíření fondu vyrovnávací paměti SQL Server a cache pro čtení úložiště Premium) používají místní disky výpočetního uzlu Azure.

Zkušenosti získané v mezidobí s SQL Server rozšíření fondu vyrovnávací paměti s úlohou SAP je smíšené a stále neumožňuje jasná doporučení, zda jej použít ve všech případech. Ideální případ je, že pracovní sada, kterou aplikace SAP vyžaduje, se vejde do hlavní paměti. Díky Tomu, že Azure mezitím nabízí virtuální počítače, které jsou dodávány s až 4 TB paměti, by mělo být dosažitelné, aby pracovní sada byla v paměti. Proto použití rozšíření fondu vyrovnávací paměti je omezena na některé vzácné případy a by neměl být hlavního proudu případu.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Důležité informace o zálohování a obnovení pro sql server
Při nasazování SQL Serveru do Azure je nutné zkontrolovat metodiku zálohování. I v případě, že systém není produkční systém, databáze SAP hostované SQL Server musí být zálohovány pravidelně. Vzhledem k tomu, že Azure Storage udržuje tři bitové kopie, zálohování je teď méně důležité z hlediska kompenzace selhání úložiště. Prioritou pro udržování správné zálohy a plánu obnovení je více, že můžete kompenzovat logické/ruční chyby poskytnutím možnosti obnovení bodu v čase. Takže cílem je buď použít zálohy k obnovení databáze zpět do určitého bodu v čase nebo použít zálohy v Azure k osivu jiného systému zkopírováním existující databáze. 

Chcete-li se podívat na různé možnosti zálohování serveru SQL Server v Azure, přečtěte si článek [Zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). Článek pokrývá několik různých možností.

### <a name="manual-backups"></a>Ruční zálohování
Máte několik možností, jak provádět "ruční" zálohování:

1. Provádění konvenčních záloh SQL Serveru na přímo připojených diskech Azure. Tato metoda má tu výhodu, že máte zálohy k dispozici rychle pro obnovení systému a vybudovat nové systémy jako kopie stávajících systémů SAP
2.  SQL Server 2012 CU4 a vyšší můžete zálohovat databáze na adresu URL úložiště Azure.
3.  Zálohy snímků souborů pro databázové soubory v úložišti objektů blob Azure. Tato metoda funguje jenom v případě, že data a soubory protokolu serveru SQL Server jsou umístěny v úložišti objektů blob Azure

První metoda je dobře známá a používá se v mnoha případech i v místním světě. Nicméně, to vás nechá s úkolem vyřešit dlouhodobější umístění zálohování. Vzhledem k tomu, že nechcete uchovávat zálohy po dobu 30 nebo více dní v místně připojeném úložišti Azure Storage, musíte buď použít služby Zálohování Azure nebo jiný nástroj pro zálohování a obnovení od jiného výrobce, který zahrnuje správu přístupu a uchovávání informací pro vaše zálohy. Nebo vytvoříte velký souborový server v Azure pomocí úložných prostorů Windows.

Druhá metoda je popsána blíže v článku [ZÁLOHOVÁNÍ serveru SQL Server na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Různé verze SQL Server mají některé varianty v této funkci. Proto byste měli rezervovat dokumentaci pro konkrétní kontrolu verze serveru SQL Server. Je důležité si uvědomit, že tento článek uvádí mnoho omezení. Máte buď možnost provést zálohování proti:

- Jeden objekt blob stránky Azure, který pak omezí velikost zálohy na 1000 GB. To také omezuje propustnost, kterou můžete dosáhnout.
- Více (až 64) objektů BLOB bloku Azure, které umožňují teoretickou velikost zálohování 12 TB. Testy s databázemi zákazníků však odhalily, že maximální velikost zálohy může být menší než jeho teoretický limit. V takovém případě jste zodpovědní za správu uchovávání záloh a přístup o zálohy také.


### <a name="automated-backup-for-sql-server"></a>Automatizovaná záloha pro SQL Server
Automatické zálohování poskytuje službu automatického zálohování pro edice SQL Server Standard a Enterprise spuštěnou ve virtuálním počítači se systémem Windows v Azure. Tuto službu poskytuje [rozšíření agenta SQL Server IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), které se automaticky instaluje na ibi virtuálních strojů SQL Server Windows na webu Azure Portal. Pokud nasadíte vlastní image operačního systému s nainstalovaným SQL Serverem, musíte nainstalovat rozšíření virtuálních zařízení samostatně. Nezbytné kroky jsou popsány v tomto [článku](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Další podrobnosti o možnostech této metody naleznete v těchto článcích:

- SQL Server 2014: [Automatické zálohování pro virtuální počítače SQL Serveru 2014 (Správce prostředků)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Automatické zálohování v2 pro virtuální počítače Azure (Správce prostředků)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Při pohledu do dokumentace, uvidíte, že funkce s novější verze SQL Server lepší. Některé další podrobnosti o automatickém zálohování serveru SQL Server jsou vydány v článku [SQL Server Managed Backup to Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). Teoretický limit velikosti zálohy je 12 TB.  Automatické zálohování může být dobrou metodou pro velikosti zálohování až 12 TB. Vzhledem k tomu, že více objektů BLOB jsou zapsány paralelně, můžete očekávat propustnost větší než 100 MB/s. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Zálohování Azure pro virtuální počítače SQL Serveru
Tato nová metoda zálohování SQL Serveru je nabízena od června 2018 jako veřejná předběžná verze služeb Azure Backup. Metoda zálohování SQL Serveru je stejná jako ostatní nástroje třetích stran, konkrétně rozhraní SQL Server VSS/VDI pro streamování záloh do cílového umístění. V tomto případě je cílovým umístěním trezor služby Azure Recovery Service.

Více než podrobný popis této metody zálohování, který přidává řadu výhod konfigurace centrální ho zálohování, monitorování a správy je k dispozici [zde](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Řešení zálohování jiných výrobců
Pro celou řadu zákazníků SAP nebylo možné začít znovu a zavést kompletní nová řešení zálohování pro část jejich prostředí SAP, která běžela v Azure. V důsledku toho bylo potřeba použít a rozšířit existující řešení zálohování do Azure. Rozšíření stávajících řešení zálohování do Azure obvykle fungovalo dobře s většinou hlavních dodavatelů v tomto prostoru. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Použití bitové kopie serveru SQL Server mimo tržiště Microsoft Azure Marketplace
Microsoft nabízí virtuální počítače na Azure Marketplace, které už obsahují verze SQL Serveru. Pro zákazníky SAP, kteří vyžadují licence pro SQL Server a Windows, použití těchto bitových kopií může být příležitost í tísním potřebou licencí tak, že se virtuální ms s sql serverem už nainstaluje. Aby bylo možné použít tyto obrázky pro SAP, je třeba provést následující úvahy:

* Verze SQL Server bez vyhodnocení získat vyšší náklady než "Windows pouze" virtuální počítač nasazený z Azure Marketplace. Viz tyto články porovnat <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>ceny: a . 
* Můžete použít pouze SQL Server verze, které jsou podporovány SAP.
* Řazení instance SQL Serveru, která je nainstalovaná ve virtuálních počítačích nabízených na webu Azure Marketplace, není řazení SAP NetWeaver vyžaduje spuštění instance SERVERU SQL Server. Můžete změnit řazení i když s pokyny v následující části.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Změna řazení sql serveru virtuálního ms systému Microsoft Windows/SQL Server
Vzhledem k tomu, že image SQL Serveru na Azure Marketplace nejsou nastaveny na řazení, což je vyžadováno aplikacemi SAP NetWeaver, je třeba je změnit ihned po nasazení. Pro SQL Server tuto změnu řazení lze provést pomocí následujících kroků, jakmile je virtuální modul nasazen a správce se může přihlásit k nasazenému virtuálnímu virtuálnímu mnásledujícím virtuálnímu jemu:

* Otevřete příkazové okno systému Windows jako správce.
* Změňte adresář na C:\Program Files\Microsoft SQL Server\110\Setup Boottrap\SQLServer2012.
* Spusťte příkaz Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> je účet, který byl definován jako účet správce při prvním nasazení virtuálního počítačů prostřednictvím galerie.

Proces by měl trvat jen několik minut. Chcete-li se ujistit, zda krok skončil se správným výsledkem, proveďte následující kroky:

* Otevřete SQL Server Management Studio.
* Otevřete okno dotazu.
* Spusťte příkaz sp_helpsort v hlavní databázi serveru SQL Server.

Požadovaný výsledek by měl vypadat takto:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Pokud je výsledek jiný, STOP nasazení SAP a zjistěte, proč příkaz nastavení nefunguje podle očekávání. Nasazení aplikací SAP NetWeaver na instanci SERVERU SQL Server s různými kódovými stránkami serveru SQL Server, než je uvedeno **výše, není** podporováno.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Vysoká dostupnost SQL Serveru pro SAP v Azure
Pomocí SQL Serveru v nasazení Azure IaaS pro SAP máte několik různých možností, jak přidat k nasazení vrstvy DBMS vysoce dostupné. Jak je popsáno v [úvahách o nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](dbms_guide_general.md) již, Azure poskytuje různé služby SLA pro dostupnost jednoho virtuálního počítače a dvojice virtuálních počítačů nasazených v sadě dostupnosti Azure. Předpokladem je, že jedete směrem k up-time SLA pro vaše produkční nasazení, která vyžaduje nasazení v Azure Dostupnost Sady. V takovém případě je potřeba nasadit minimálně dva virtuální chody v takové sadě dostupnosti. Jeden virtuální virtuální modul spustí aktivní instanci serveru SQL Server. Druhý virtuální virtuální virtuální ms spustí pasivní instanci

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Clustering serveru SQL Server pomocí souborového serveru se škálovatcími velikostmi systému Windows
Se systémem Windows Server 2016 společnost Microsoft zavedla [službu Storage Spaces Direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Na základě přímého nasazení prostorů úložiště je podporováno clustering FCI serveru SQL Server. Podrobnosti najdete v článku [Konfigurace instance clusteru s podporou převzetí služeb při selhání serveru SQL Server na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). Řešení vyžaduje nástroj pro vyrovnávání zatížení Azure také pro řešení virtuální IP adresy prostředků clusteru. Databázové soubory serveru SQL Server jsou uloženy v prostorech úložiště. Proto je dané, že budete muset vybudovat prostory úložiště Windows založené na Azure Premium Storage. Vzhledem k tomu, že toto řešení bylo podporováno již příliš dlouho, neexistují žádné známé zákazníky SAP, kteří používají toto řešení ve scénářích výroby SAP.  

### <a name="sql-server-log-shipping"></a>Přesouvání protokolu serveru SQL Server
Jednou z metod vysoké dostupnosti (HA) je sql server přesouvání protokolu. Pokud virtuální počítače účastnící se konfigurace HA mají pracovní překlad názvů, není žádný problém a nastavení v Azure se neliší od žádné nastavení, které se provádí místně. S ohledem na nastavení Log Shipping a zásady kolem log shipping. Podrobnosti o přesouvání protokolu serveru SQL Server lze nalézt v článku [o přesouvání protokolu (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

Funkce přesouvání protokolu SQL Serveru se v Azure téměř nepoužívala k dosažení vysoké dostupnosti v rámci jedné oblasti Azure. V následujících scénářích však zákazníci SAP používali přesouvání protokolu úspěšné ve spojení s Azure:

- Scénáře zotavení po havárii z jedné oblasti Azure do jiné oblasti Azure
- Konfigurace zotavení po havárii z místního prostředí do oblasti Azure
- Přeškrtnout scénáře z místního do Azure. V těchto případech přesouvání protokolu se používá k synchronizaci nové nasazení DBMS v Azure s probíhající produkční systém v místním prostředí. V době přetečení je výroba ukončena a je zajištěno, že poslední a nejnovější zálohy transakčních protokolů byly přeneseny do nasazení Azure DBMS. Pak se otevře nasazení Azure DBMS pro produkční prostředí.  



### <a name="database-mirroring"></a>Zrcadlení databáze
Zrcadlení databáze podporované SAP (viz SAP Poznámka [965908]) spoléhá na definování partnera s podporou převzetí služeb při selhání v připojovacím řetězci SAP. Pro případy mezi místními, předpokládáme, že dva virtuální počítače jsou ve stejné doméně a že kontext uživatele dvě instance SQL Server jsou spuštěny pod uživatelem domény také a mají dostatečná oprávnění ve dvou instancích SQL Server účastní. Proto nastavení zrcadlení databáze v Azure se neliší mezi typické místní nastavení nebo konfigurace.

Od nasazení pouze v cloudu je nejjednodušší mít v Azure jiné nastavení domény, aby tyto virtuální počítače DBMS (a ideálně vyhrazené virtuální počítače SAP) byly v rámci jedné domény.

Pokud doména není možná, můžete také použít certifikáty pro koncové body zrcadlení databáze, jak je popsáno zde:<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Kurz nastavení zrcadlení databáze v Azure najdete zde:<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>AlwaysOn SQL Serveru
Jako vždy on je podporovánpro SAP místně (viz SAP Poznámka [1772688]), je podporována v kombinaci s SAP v Azure. Existují některé zvláštní aspekty týkající se nasazení naslouchací proces skupiny dostupnosti serveru SQL Server (nelze zaměňovat se sadou dostupnosti Azure) protože Azure v tomto okamžiku neumožňuje vytvoření objektu AD/DNS, jak je to možné místně. Proto některé různé kroky instalace jsou nezbytné k překonání konkrétní chování Azure.

Některé aspekty pomocí naslouchací proces skupiny dostupnosti jsou:

* Použití naslouchací proces skupiny dostupnosti je možné pouze s Windows Server 2012 nebo vyšší jako hostovaný operační systém virtuálního soudu. V systému Windows Server 2012 se musíte ujistit, že je tato oprava použita:<https://support.microsoft.com/kb/2854082> 
* Pro systém Windows Server 2008 R2 tato oprava neexistuje a vždy zapnuto by bylo nutné použít stejným způsobem jako zrcadlení databáze zadáním partnera s podporou převzetí služeb při selhání v řetězci připojení (provedeno prostřednictvím parametru SAP default.pfl dbs/mss/server - viz SAP Note [965908]).
* Při použití naslouchací proces skupiny dostupnosti musí být virtuální virtuální moduly připojeny k vyhrazenému vyvykladaču zatížení. Aby se zabránilo tomu, že Azure přiřazuje nové IP adresy v případech, kdy jsou oba virtuální počítače mimochodem vypnuty, měli byste přiřadit statické IP adresy síťovým rozhraním těchto virtuálních počítačů v konfiguraci Always On (definování statické IP adresy je popsáno v [tomto][virtual-networks-reserved-private-ip] článku)
* Při vytváření konfigurace clusteru WSFC, kde cluster potřebuje přiřazenou speciální IP adresu, jsou vyžadovány speciální kroky, protože Azure s jeho aktuální funkcí by přiřadil názvu clusteru stejnou adresu IP jako uzel, na kterém je cluster vytvořen. To znamená, že je nutné provést ruční krok, aby bylo možné clusteru přiřadit jinou adresu IP.
* Naslouchací proces skupiny dostupnosti se vytvoří v Azure s koncovými body TCP/IP, které jsou přiřazeny k virtuálním počítačům, na kterých běží primární a sekundární repliky skupiny Dostupnost.
* Je možné, že je potřeba zabezpečit tyto koncové body pomocí aklů.

Podrobná dokumentace k nasazení always on s SQL Server v seznamech virtuálních počítačích Azure, jako jsou:

- [Představujeme skupiny dostupnosti SQL Server always on na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Nakonfigurujte skupinu dostupnosti vždy na virtuálních počítačích Azure v různých oblastech](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Konfigurace provyrovnávání zatížení pro skupinu dostupnosti always on v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Pokud konfigurujete nástroj pro vyrovnávání zatížení Azure pro virtuální IP adresu naslouchací proces skupiny dostupnosti, ujistěte se, že je nakonfigurovaný DirectServerReturn. konfigurace této možnosti sníží latenci síťové odezvy mezi aplikační vrstvou SAP a vrstvou DBMS. 

SQL Server Always On je nejběžnější používaná funkce vysoké dostupnosti a zotavení po havárii používaná v nasazení úloh Azure pro SAP. Většina zákazníků používá always on pro vysokou dostupnost v rámci jedné oblasti Azure. Pokud je nasazení omezeno pouze na dva uzly, máte dvě možnosti připojení:

- Pomocí naslouchací proces skupiny dostupnosti. S naslouchací proces skupiny dostupnosti, musíte nasadit azure vyrovnávání zatížení. Obvykle se jedná o výchozí metodu nasazení. Aplikace SAP by byly nakonfigurovány tak, aby se připojovaly k naslouchací proces skupiny dostupnosti a nikoli k jednomu uzlu.
- Použití parametrů připojení sql server zrcadlení databáze. V takovém případě je třeba nakonfigurovat připojení aplikací SAP způsobem, kde jsou pojmenovány oba názvy uzlů. Přesné podrobnosti o takové konfiguraci na straně SAP jsou popsány v SAP Note [#965908](https://launchpad.support.sap.com/#/notes/965908). Pomocí této možnosti byste nemuseli konfigurovat naslouchací proces skupiny dostupnosti. A s tím žádný Azure nástroje pro vyrovnávání zatížení pro SQL Server vysokou dostupnost. V důsledku toho je latence sítě mezi aplikační vrstvou SAP a vrstvou DBMS nižší, protože příchozí provoz na instanci serveru SQL Server není směrován prostřednictvím nástroje pro vyrovnávání zatížení Azure. Ale odvolání, tato možnost funguje pouze v případě, že omezíte skupinu dostupnosti na dvě instance. 

Poměrně málo zákazníků využívá funkci SQL Server Always On pro další funkce zotavení po havárii mezi oblastmi Azure. Několik zákazníků také využívá možnost provádět zálohy ze sekundární repliky. 

## <a name="sql-server-transparent-data-encryption"></a>Transparentní šifrování dat serveru SQL Server
Existuje řada zákazníků, kteří používají SQL Server [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) při nasazování jejich DATABÁZE SAP SQL Server v Azure. Funkce SQL Server TDE je plně podporována SAP (viz SAP Note [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Použití tde serveru SQL Server
V případech, kdy provedete heterogenní migraci z jiného systému DBMS, který běží místně, do systému Windows/SQL Server spuštěný v Azure, měli byste předem vytvořit prázdnou cílovou databázi na serveru SQL Server. Jako další krok byste použít SQL Server TDE funkce. I když stále běží váš produkční systém v místním prostředí. Důvod, který chcete provést v tomto pořadí je, že proces šifrování prázdné databáze může trvat poměrně dlouho. Procesy importu SAP by pak importovat data do šifrované databáze během fáze prostojů. Režie importu do šifrované databáze má mnohem nižší časový dopad než šifrování databáze po fázi exportu v časové fázi mezičasy. Negativní prostředí, kde se při pokusu o použití TDE s úlohou SAP spuštěné v horní části databáze. Proto doporučení je považován za nasazení TDE jako aktivitu, která je třeba provést bez úlohy SAP v konkrétní databázi.

V případech, kdy přesunete databáze SAP SQL Server z místního do Azure, doporučujeme testování, na které infrastruktuře můžete získat šifrování použít nejrychleji. Za tímto skutečností mějte na paměti tyto skutečnosti:

- Nelze definovat, kolik podprocesů se používá k použití šifrování dat v databázi. Počet podprocesů je převážně závislý na počtu diskových svazků, přes které jsou distribuována data serveru SQL Server a soubory protokolu. Znamená, že čím zřetelnější svazky (písmena jednotek), tím více vláken bude paralelně zapojeno k provedení šifrování. Taková konfigurace je trochu v rozporu s dřívějšínávrh konfigurace disku na vytváření jednoho nebo menší počet prostorů úložiště pro soubory databáze SQL Server ve virtuálních počítačích Azure. Konfigurace s malým počtem svazků by vedla k malému počtu vláken provádějících šifrování. Jedno vlákno šifrování je čtení 64KB rozsahy, šifruje a pak zapsat záznam do souboru protokolu transakcí, říká, že rozsah dostal zašifrována. V důsledku toho je zatížení transakční protokol je mírné.
- Ve starších verzích serveru SQL Server se komprese záloh již nezefektivňovala při šifrování databáze serveru SQL Server. Toto chování může vyvinout do problému, když váš plán byl šifrovat databázi SQL Server místně a pak zkopírovat zálohu do Azure k obnovení databáze v Azure. Komprese zálohování sql serveru obvykle dosahuje kompresního poměru faktoru 4.
- S SQL Server 2016, SQL Server představil nové funkce, které umožňuje kompresi šifrované databáze i efektivním způsobem. Viz [tento blogy](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) pro některé podrobnosti.
 
Při práci s aplikací šifrování TDE pouze s ne až malou úlohou SAP byste měli v konkrétní konfiguraci otestovat, jestli je lepší použít TDE v místní databázi SAP nebo v Azure. V Azure máte určitě větší flexibilitu, pokud jde o infrastrukturu nadměrného zřizování a zmenšit infrastrukturu po použití TDE.

### <a name="using-azure-key-vault"></a>Použití trezoru klíčů Azure
Azure nabízí službu [trezoru klíčů](https://azure.microsoft.com/services/key-vault/) pro ukládání šifrovacích klíčů. SQL Server na druhé straně nabízejí konektor pro využití Azure Key Vault jako úložiště pro certifikáty TDE.

Další podrobnosti pro použití Azure Key Vault pro SQL Server TDE seznamy, jako jsou:

- [Rozšiřitelná správa klíčů pomocí trezoru klíčů Azure (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [Sql Server TDE Rozšiřitelná správa klíčů pomocí trezoru klíčů Azure - kroky nastavení](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [Řešení potíží s implementačním & údržby konektoru SQL Server](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Další otázky od zákazníků o sql server transparentní šifrování dat – TDE + Trezor klíčů Azure](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Pomocí SQL Server TDE, zejména s Azure key Vault, se doporučuje používat nejnovější opravy SQL Server 2014, SQL Server 2016 a SQL Server 2017. Důvodem je, že na základě zpětné vazby od zákazníků, optimalizace a opravy dostal použít na kód. Jako příklad můžete například zkontrolovat [#4058175 KBA](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Obecný SQL Server pro SAP v souhrnu Azure
V této příručce je mnoho doporučení a doporučujeme, abyste si ji před plánováním nasazení Azure přečetli více než jednou. Obecně však nezapomeňte dodržovat nejvyšší obecné DBMS na doporučení specifická pro Azure:

1. Použijte nejnovější verzi DBMS, jako je SQL Server 2017, která má v Azure nejvíce výhod. 
2. Pečlivě naplánujte prostředí systému SAP v Azure, abyste vyvážili rozložení datových souborů a omezení Azure:
   * Nemáte příliš mnoho disků, ale mají dost, aby zajistily, že můžete dosáhnout požadované viops.
   * Pokud spravované disky nepoužíváte, nezapomeňte, že viopy jsou také omezené na účet úložiště Azure a že účty úložiště jsou omezené v rámci každého předplatného Azure ([další podrobnosti).][azure-resource-manager/management/azure-subscription-service-limits] 
   * Prokládejte se na příčce disků pouze v případě, že potřebujete dosáhnout vyšší propustnosti.
3. Nikdy neinstalujte software ani nevkládejte žádné soubory, které vyžadují trvalost na d:\ disk, jak to je non-trvalé a nic na této jednotce je ztracena při restartu systému Windows.
4. Nepoužívejte ukládání disku do mezipaměti pro Azure Standard Storage.
5. Nepoužívejte azure geograficky replikované účty Azure Standard Storage.  Použití místně redundantní pro úlohy DBMS.
6. K replikaci databázových dat použijte řešení HA/DR dodavatele DBMS.
7. Vždy používejte překlad názvů, nespoléhejte se na IP adresy.
8. Pomocí SQL Server TDE, použít nejnovější opravy SQL Server.
9. Použijte nejvyšší možnou kompresi databáze. Což je komprese stránky pro SQL Server.
10. Buďte opatrní při používání ibi sql serveru z Azure Marketplace. Pokud používáte SQL Server jeden, musíte změnit shromažďování instancí před instalací jakéhokoli systému SAP NetWeaver na něj.
11. Nainstalujte a nakonfigurujte monitorování hostitele SAP pro Azure, jak je popsáno v [Průvodci nasazením][deployment-guide].
