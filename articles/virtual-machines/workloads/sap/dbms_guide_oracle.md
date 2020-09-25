---
title: Nasazení Oracle Azure Virtual Machines DBMS pro úlohy SAP | Microsoft Docs
description: Nasazení DBMS v počítačích Azure Virtual Machines s Oraclem pro úlohy SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, Oracle, ochrana dat
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d83c4ffe4e60ef2896e16b97e1ec34d71a022b9b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279004"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Nasazení Azure Virtual Machines DBMS pro úlohy SAP

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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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


Tento dokument popisuje několik různých oblastí, které je potřeba vzít v úvahu při nasazení Oracle Database pro úlohy SAP v Azure IaaS. Před čtením tohoto dokumentu doporučujeme přečíst si [informace o nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md). Doporučujeme také přečíst si další příručky v [dokumentaci ke službě SAP v dokumentaci k Azure](./get-started.md). 

Informace o verzích Oracle a odpovídajících verzích operačních systémů, které jsou podporované pro provoz SAP v Oracle v Azure, najdete v poznámce SAP [2039619].

Obecné informace o používání SAP Business Suite v Oracle najdete v [SAP v Oracle](https://www.sap.com/community/topic/oracle.html).
Oracle podporuje software Oracle pro spouštění na Microsoft Azure. Další informace o obecné podpoře pro Windows Hyper-V a Azure najdete v [nejčastějších dotazech k Oracle a Microsoft Azure](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Poznámky SAP relevantní pro Oracle, SAP a Azure 

Následující poznámky SAP souvisejí s SAP v Azure.

| Číslo poznámky | Nadpis |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: podporované produkty a typy virtuálních počítačů Azure |
| [2015553] |SAP v Microsoft Azure: požadavky na podporu |
| [1999351] |Řešení potíží s vylepšeným monitorováním Azure pro SAP |
| [2178632] |Klíčové metriky monitorování pro SAP v Microsoft Azure |
| [2191498] |SAP v systému Linux s Azure: rozšířené monitorování |
| [2039619] |Aplikace SAP na Microsoft Azure s využitím databáze Oracle: podporované produkty a verze |
| [2243692] |Virtuální počítač se systémem Linux na Microsoft Azure (IaaS): problémy s licencí SAP |
| [2069760] |Instalace a upgrade systému Oracle Linux 7. x SAP |
| [1597355] |Doporučení pro zaměněné místo pro Linux |
| [2171857] |Oracle Database 12c-podpora systému souborů na platformě Linux |
| [1114181] |Oracle Database 11g-podpora systému souborů na platformě Linux |

Přesné konfigurace a funkce podporované Oracle a SAP v Azure jsou popsané v části SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619).

Windows a Oracle Linux jsou jediné operační systémy, které podporují Oracle a SAP v Azure. Široce používané distribuce SLES a RHEL Linux se nepodporují pro nasazování komponent Oracle v Azure. Komponenty Oracle zahrnují klienta Oracle Database, který aplikace SAP používá pro připojení k systému Oracle DBMS. 

Výjimky, v závislosti na službě SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619), jsou komponenty SAP, které nepoužívají klienta Oracle Database. Takovými součástmi SAP jsou samostatné služby pro zařazování do fronty SAP, server zpráv, fronty pro replikaci, Dispatch a SAP Gateway.  

I když v Oracle Linux spouštíte své instance aplikace Oracle DBMS a SAP, můžete provozovat služby SAP Central Services na SLES nebo RHEL a chránit je pomocí clusteru s podporou Pacemaker. Pacemaker jako rozhraní s vysokou dostupností se na Oracle Linux nepodporuje.

## <a name="specifics-for-oracle-database-on-windows"></a>Specifické pro Oracle Database ve Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Pokyny pro konfiguraci Oracle pro instalace SAP ve virtuálních počítačích Azure ve Windows

V souladu s ruční instalací SAP by se soubory související s Oracle neměli instalovat nebo umístit na disk s operačním systémem virtuálního počítače (jednotka c:). Virtuální počítače s různou velikostí můžou podporovat proměnlivý počet připojených disků. Menší typy virtuálních počítačů můžou podporovat menší počet připojených disků. 

Pokud máte menší virtuální počítače a zaznamenali jste limit počtu disků, které můžete připojit k virtuálnímu počítači, můžete nainstalovat nebo najít Oracle Home, Stage,, `saptrace` `saparch` , `sapbackup` , `sapcheck` nebo `sapreorg` na disk s operačním systémem. Tyto části komponent Oracle DBMS nejsou příliš náročné na vstupně-výstupní operace a vstupně-výstupní propustnost. To znamená, že disk s operačním systémem může zpracovávat požadavky na vstupně-výstupní operace. Výchozí velikost disku s operačním systémem by měla být 127 GB. 

Soubory protokolu Oracle Database a znovu je třeba uložit na samostatné datové disky. Pro dočasný tabulkový prostor Oracle existuje výjimka. `Tempfiles` dá se vytvořit v D:/ (netrvalá jednotka). Netrvalý D:\ jednotka také nabízí lepší latenci I/O a propustnost (s výjimkou virtuálních počítačů řady A-Series). 

Chcete-li určit správné množství místa pro `tempfiles` , můžete kontrolovat velikosti `tempfiles` v existujících systémech.

### <a name="storage-configuration"></a>Konfigurace úložiště
Podporovaná je jenom jedna instance Oracle, která používá disky formátované systémem souborů NTFS. Všechny soubory databáze musí být uložené v systému souborů NTFS na Managed Disks (doporučeno) nebo na VHD. Tyto disky jsou připojené k virtuálnímu počítači Azure a jsou založené na [Azure Page BLOB Storage](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) nebo [Azure Managed disks](../../managed-disks-overview.md). 

V článku [Azure Storage typy pro úlohy SAP](./planning-guide-storage.md) získáte další podrobnosti o specifických typech úložiště bloku Azure vhodných pro úlohy DBMS.

Důrazně doporučujeme použít [Azure Managed disks](../../managed-disks-overview.md). Důrazně doporučujeme pro nasazení Oracle Database použít službu [Azure Premium Storage nebo Azure Ultra disk](../../disks-types.md) .

Síťové jednotky nebo vzdálené sdílené složky jako souborové služby Azure nejsou podporované pro Oracle Database soubory. Další informace naleznete v tématech:

- [Představujeme službu Microsoft Azure File](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Nastavení trvalých připojení k Microsoft Azure Files](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)


Pokud používáte disky založené na Azure Page Storage nebo Managed Disks, příkazy v části [požadavky na nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md) se vztahují i na nasazení s Oracle Database.

Kvóty pro propustnost vstupně-výstupních operací pro disky Azure existují. Tento koncept je vysvětlen v tématu [týkajícím se nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md). Přesná kvóta závisí na typu virtuálního počítače, který používáte. Seznam typů virtuálních počítačů s jejich kvótami najdete ve [velikosti pro virtuální počítače s Windows v Azure][virtual-machines-sizes-windows].

Pokud chcete identifikovat podporované typy virtuálních počítačů Azure, přečtěte si článek SAP Note [1928533].

Minimální konfigurace je následující: 

| Součást | Disk | Ukládání do mezipaměti | Fond úložiště |
| --- | ---| --- | --- |
| \oracle \<SID> \origlogaA & mirrlogB | Disk úrovně Premium nebo Ultra | Žádné | Není potřeba |
| \oracle \<SID> \origlogaB & mirrlogA | Disk úrovně Premium nebo Ultra | Žádné | Není potřeba |
| \oracle \<SID> \sapdata1... n | Disk úrovně Premium nebo Ultra | Jen pro čtení | Dá se použít pro Premium |
| \oracle \<SID> \oraarch | Standard | Žádné | Není potřeba |
| Oracle Home, `saptrace` ,... | Disk s operačním systémem (Premium) | | Není potřeba |


Výběr disků pro hostování online protokolů opětovného provedení by měl být řízen požadavky IOPS. Je možné uložit všechny sapdata1... n (tabulkové prostory) na jednom jednom připojeném disku, pokud velikost, IOPS a propustnost splňují požadavky. 

Konfigurace výkonu je následující:

| Součást | Disk | Ukládání do mezipaměti | Fond úložiště |
| --- | ---| --- | --- |
| \oracle \<SID> \origlogaA | Disk úrovně Premium nebo Ultra | Žádné | Dá se použít pro Premium  |
| \oracle \<SID> \origlogaB | Disk úrovně Premium nebo Ultra | Žádné | Dá se použít pro Premium |
| \oracle \<SID> \mirrlogAB | Disk úrovně Premium nebo Ultra | Žádné | Dá se použít pro Premium |
| \oracle \<SID> \mirrlogBA | Disk úrovně Premium nebo Ultra | Žádné | Dá se použít pro Premium |
| \oracle \<SID> \sapdata1... n | Disk úrovně Premium nebo Ultra | Jen pro čtení | Doporučeno pro Premium  |
| \oracle\SID\sapdata (n + 1) * | Disk úrovně Premium nebo Ultra | Žádné | Dá se použít pro Premium |
| \oracle \<SID> \oraarch * | Disk úrovně Premium nebo Ultra | Žádné | Není potřeba |
| Oracle Home, `saptrace` ,... | Disk s operačním systémem (Premium) | Není potřeba |

* (n + 1): hostování systému, dočasné a ZPĚTná prostoru. Vzor vstupně-výstupních prostorů systému a zpětných prostorů se liší od ostatních tabulkových prostorů hostujících data aplikací. Nejedná se o nejlepší možnost pro výkon systému a vrácení tabulkových prostorů.

* oraarch: fond úložiště není potřebný z hlediska výkonu v zobrazení. Dá se použít k získání více místa.

Pokud se v případě Azure Premium Storage vyžaduje víc IOPS, doporučujeme použít fondy úložiště Windows (dostupné jenom ve Windows Serveru 2012 a novějším) a vytvořit tak velké logické zařízení přes víc připojených disků. Tento přístup zjednodušuje správu místa na disku a pomáhá zabránit tomu, abyste se vyhnuli ruční distribuci souborů mezi více připojených disků.


#### <a name="write-accelerator"></a>Akcelerátor zápisu
U virtuálních počítačů Azure M-Series je latence psaní do online protokolů možné snížit o faktorech v porovnání s Azure Premium Storage. Povolte Azure Akcelerátor zápisu pro disky (VHD) založené na Premium Storage Azure, které se používají pro soubory protokolu online opětovného provedení. Další informace najdete v tématu [akcelerátor zápisu](../../how-to-enable-write-accelerator.md). Nebo použijte Azure Ultra disk pro svazek protokolu online opětovného přihlášení.


### <a name="backuprestore"></a>Zálohování a obnovení
V případě funkcí zálohování a obnovení jsou podporované nástroje SAP BR * pro Oracle stejným způsobem jako u standardních operačních systémů Windows Server. Oracle Recovery Manager (RMAN) se podporuje taky pro zálohování na disk a obnovení z disku.

Pomocí Azure Backup můžete také spustit zálohování virtuálních počítačů konzistentní s aplikací. Článek [plánování infrastruktury zálohování virtuálních počítačů v Azure](../../../backup/backup-azure-vms-introduction.md) vysvětluje, jak Azure Backup využívá funkci Windows VSS ke spouštění záloh konzistentních vzhledem k aplikacím. Verze systému Oracle DBMS podporované v Azure pomocí SAP můžou využívat funkci stínové kopie svazku (VSS). Další informace najdete v dokumentaci Oracle [základní koncepty zálohování a obnovení databáze pomocí služby VSS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701).


### <a name="high-availability"></a>Vysoká dostupnost
Oracle data Guard je podporováno pro účely vysoké dostupnosti a zotavení po havárii. Aby bylo možné zajistit automatické převzetí služeb při selhání v ochraně dat, je potřeba použít rychlé spuštění převzetí služeb při selhání (FSFA). Pozorovatel (FSFA) aktivuje převzetí služeb při selhání. Pokud FSFA nepoužíváte, můžete použít jenom ruční konfiguraci převzetí služeb při selhání.

Další informace o zotavení po havárii pro databáze Oracle v Azure najdete v tématu [zotavení po havárii pro databázi Oracle Database 12c v prostředí Azure](../oracle/oracle-disaster-recovery.md).

### <a name="accelerated-networking"></a>Urychlení sítě
Pro nasazení Oracle ve Windows důrazně doporučujeme zrychlit sítě, jak je popsáno v tématu [urychlené síťové služby Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Zvažte také doporučení, která jsou k [disVirtual Machines v části požadavky na nasazení Azure DBMS pro úlohy SAP](dbms_guide_general.md). 
### <a name="other"></a>Další
[Důležité informace týkající se nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md) popisují další důležité koncepty týkající se nasazení virtuálních počítačů s Oracle Database, včetně skupin dostupnosti Azure a monitorování SAP.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Specifické pro Oracle Database Oracle Linux
Oracle podporuje software Oracle pro spouštění na Microsoft Azure s Oracle Linux jako hostovaný operační systém. Další informace o obecné podpoře pro Windows Hyper-V a Azure najdete v [nejčastějších dotazech k Azure a Oracle](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

Podporuje se taky konkrétní scénář aplikací SAP využívajících databáze Oracle. Podrobnosti jsou popsány v následující části dokumentu.

### <a name="oracle-version-support"></a>Podpora verzí Oracle
Informace o tom, které verze Oracle a odpovídající verze operačního systému se podporují pro provozování SAP v Oracle v Azure Virtual Machines, najdete v článku SAP Note [2039619].

Obecné informace o používání SAP Business Suite na Oracle najdete na [stránce komunity SAP na Oracle](https://www.sap.com/community/topic/oracle.html).

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Pokyny pro konfiguraci Oracle pro instalace SAP na virtuálních počítačích Azure v systému Linux

V souladu s instalačními příručkami SAP by se soubory související s Oracle neměly instalovat nebo umístit do systémových ovladačů pro spouštěcí disk virtuálního počítače. Různé velikosti virtuálních počítačů podporují proměnlivý počet připojených disků. Menší typy virtuálních počítačů můžou podporovat menší počet připojených disků. 

V takovém případě doporučujeme nainstalovat nebo najít Oracle Home, Stage,, `saptrace` `saparch` , `sapbackup` ,, `sapcheck` nebo `sapreorg` na spouštěcí disk. Tyto části komponent Oracle DBMS nejsou náročné na vstupně-výstupní operace a vstupně-výstupní propustnost. To znamená, že disk s operačním systémem může zpracovávat požadavky na vstupně-výstupní operace. Výchozí velikost disku s operačním systémem je 30 GB. Spouštěcí disk můžete rozšířit pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Po rozbalení spouštěcího disku můžete přidat další oddíl pro binární soubory Oracle.


### <a name="storage-configuration"></a>Konfigurace úložiště

Systémy souborů ext4, XFS a Oracle ASM jsou podporovány pro Oracle Database soubory v Azure. Všechny soubory databáze musí být uložené v těchto systémech souborů na základě VHD nebo Managed Disks. Tyto disky jsou připojené k virtuálnímu počítači Azure a jsou založené na [Azure Page BLOB Storage](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) nebo [Azure Managed disks](../../managed-disks-overview.md).

Pro Oracle Linux jádra UEK se vyžaduje minimálně UEK verze 4, která podporuje [Azure Premium SSD](../../premium-storage-performance.md#disk-caching).

Zarezervujte článek [Azure Storage typy pro úlohy SAP](./planning-guide-storage.md) , abyste získali další podrobnosti o specifických typech úložiště bloku Azure vhodných pro úlohy DBMS.

Používání [Azure Managed disks](../../managed-disks-overview.md)se důrazně doporučuje. Pro nasazení Oracle Database se důrazně doporučuje používat [Azure Premium SSD](../../disks-types.md) .

Síťové jednotky nebo vzdálené sdílené složky jako souborové služby Azure nejsou podporované pro Oracle Database soubory. Další informace najdete v následujících článcích: 

- [Představujeme službu Microsoft Azure File](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Nastavení trvalých připojení k Microsoft Azure Files](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)

Pokud používáte disky založené na Azure Page Storage nebo Managed Disks, příkazy v části [požadavky na nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md) se vztahují i na nasazení s Oracle Database.

Kvóty pro propustnost vstupně-výstupních operací pro disky Azure existují. Tento koncept je vysvětlen v tématu [týkajícím se nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md). Přesná kvóta závisí na použitém typu virtuálního počítače. Seznam typů virtuálních počítačů s jejich kvótami najdete v tématu [velikosti pro virtuální počítače Linux v Azure][virtual-machines-sizes-linux].

Pokud chcete identifikovat podporované typy virtuálních počítačů Azure, přečtěte si článek SAP Note [1928533].

Minimální konfigurace:

| Součást | Disk | Ukládání do mezipaměti | Odstranění |
| --- | ---| --- | --- |
| /Oracle/ \<SID> /origlogaA & mirrlogB | Disk úrovně Premium nebo Ultra | Žádné | Není potřeba |
| /Oracle/ \<SID> /origlogaB & mirrlogA | Disk úrovně Premium nebo Ultra | Žádné | Není potřeba |
| /Oracle/ \<SID> /sapdata1... n | Disk úrovně Premium nebo Ultra | Jen pro čtení | Dá se použít pro Premium |
| /Oracle/ \<SID> /oraarch | Standard | Žádné | Není potřeba |
| Oracle Home, `saptrace` ,... | Disk s operačním systémem (Premium) | | Není potřeba |

* Odstranění: LVM Stripe nebo MDADM using RAID0

Výběr disku pro hostování protokolů opětovného provedení online v Oracle by měl být řízený požadavky IOPS. Je možné uložit všechny sapdata1... n (tabulkové prostory) na jednom připojeném disku, pokud svazek, IOPS a propustnost splňují požadavky. 

Konfigurace výkonu:

| Součást | Disk | Ukládání do mezipaměti | Odstranění |
| --- | ---| --- | --- |
| /Oracle/ \<SID> /origlogaA | Disk úrovně Premium nebo Ultra | Žádné | Dá se použít pro Premium  |
| /Oracle/ \<SID> /origlogaB | Disk úrovně Premium nebo Ultra | Žádné | Dá se použít pro Premium |
| /Oracle/ \<SID> /mirrlogAB | Disk úrovně Premium nebo Ultra | Žádné | Dá se použít pro Premium |
| /Oracle/ \<SID> /mirrlogBA | Disk úrovně Premium nebo Ultra | Žádné | Dá se použít pro Premium |
| /Oracle/ \<SID> /sapdata1... n | Disk úrovně Premium nebo Ultra | Jen pro čtení | Doporučeno pro Premium  |
| /Oracle/ \<SID> /sapdata (n + 1) * | Disk úrovně Premium nebo Ultra | Žádné | Dá se použít pro Premium |
| /Oracle/ \<SID> /oraarch * | Disk úrovně Premium nebo Ultra | Žádné | Není potřeba |
| Oracle Home, `saptrace` ,... | Disk s operačním systémem (Premium) | Není potřeba |

* Odstranění: LVM Stripe nebo MDADM using RAID0

* (n + 1): hostující prostředí, dočasné a odvolatelné prostory: vzor vstupně-výstupních prostorů systému a zpětných prostorů se liší od ostatních tabulkových prostorů hostujících data aplikací. Nejedná se o nejlepší možnost pro výkon systému a vrácení tabulkových prostorů.

* oraarch: fond úložiště není potřebný z hlediska výkonu v zobrazení.


Pokud se při použití služby Azure Premium Storage vyžaduje víc IOPS, doporučujeme pomocí LVM (Správce logických svazků) nebo MDADM vytvořit jeden velký logický svazek na několika připojených discích. Další informace najdete v tématu [týkajícím se nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md) týkající se pokynů a ukazatelů, jak využít LVM nebo MDADM. Tento přístup zjednodušuje správu místa na disku a pomáhá zabránit tomu, abyste se vyhnuli ruční distribuci souborů mezi více připojených disků.


#### <a name="write-accelerator"></a>Akcelerátor zápisu
Pro virtuální počítače Azure M-Series se při použití Azure Akcelerátor zápisu dá latence psaní do online protokolů při opakovaném používání služby Azure Premium Storage snížit o faktory. Povolte Azure Akcelerátor zápisu pro disky (VHD) založené na Premium Storage Azure, které se používají pro soubory protokolu online opětovného provedení. Další informace najdete v tématu [akcelerátor zápisu](../../how-to-enable-write-accelerator.md). Nebo použijte Azure Ultra disk pro svazek protokolu online opětovného přihlášení.


### <a name="backuprestore"></a>Zálohování a obnovení
V případě funkcí zálohování a obnovení jsou podporované nástroje SAP BR * pro Oracle stejným způsobem jako na holém počítači a Hyper-V. Oracle Recovery Manager (RMAN) se podporuje taky pro zálohování na disk a obnovení z disku.

Další informace o tom, jak můžete pomocí Azure Backup a Recovery Services zálohovat a obnovovat databáze Oracle, najdete v tématu [zálohování a obnovení databáze Oracle Database 12c na virtuálním počítači Azure Linux](../oracle/oracle-backup-recovery.md).

### <a name="high-availability"></a>Vysoká dostupnost
Oracle data Guard je podporováno pro účely vysoké dostupnosti a zotavení po havárii. Chcete-li dosáhnout automatického převzetí služeb při selhání v ochraně dat, je nutné použít příkaz rychle spustit převzetí služeb při selhání (FSFA). Funkce pozorovatele (FSFA) aktivuje převzetí služeb při selhání. Pokud FSFA nepoužíváte, můžete použít jenom ruční konfiguraci převzetí služeb při selhání. Další informace najdete v tématu [implementace Oracle data Guard na virtuálním počítači Azure Linux](../oracle/configure-oracle-dataguard.md).


Aspekty zotavení po havárii pro databáze Oracle v Azure jsou uvedené v článku [zotavení po havárii pro databázi Oracle Database 12c v prostředí Azure](../oracle/oracle-disaster-recovery.md).

### <a name="accelerated-networking"></a>Urychlení sítě
Podpora pro rychlejší síťové sítě Azure v Oracle Linux je k dispozici s Oracle Linux 7 Update 5 (Oracle Linux 7,5). Pokud nemůžete upgradovat na nejnovější verzi Oracle Linux 7,5, může to být alternativní řešení s využitím jádra RHCK (RedHat Compatible kernel) místo jádra Oracle UEK. 

Použití jádra RHEL v rámci Oracle Linux je podporované v souladu s poznámkou SAP [#1565179](https://launchpad.support.sap.com/#/notes/1565179). Pro akcelerované síťové služby Azure musí být minimální verze jádra RHCKL 3.10.0-862.13.1. el7. Pokud používáte jádro UEK v Oracle Linux ve spojení s [akcelerovanými síťovými sítěmi Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/), musíte použít jádro Oracle uek verze 5.

Pokud nasazujete virtuální počítače z image, která není založená na Azure Marketplace, musíte do virtuálního počítače zkopírovat další konfigurační soubory spuštěním následujícího kódu: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


## <a name="next-steps"></a>Další kroky
Přečtěte si článek 

- [Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md)
 

