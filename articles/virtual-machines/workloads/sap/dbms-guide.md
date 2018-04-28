---
title: Nasazení virtuálních počítačů databázového systému Azure pro SAP NetWeaver | Microsoft Docs
description: Nasazení virtuálních počítačů databázového systému Azure pro SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5654dac7-4204-4387-b312-3d8b2898eb3a
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/26/2018
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f8e8f1bc6f43c78e500e198a222fd29051138505
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Nasazení virtuálních počítačů databázového systému Azure pro SAP NetWeaver
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Tato příručka je součástí v dokumentaci na implementaci a nasazení SAP softwaru v Microsoft Azure. Před čtením této příručky, přečtěte si [plánování a implementace průvodce][planning-guide]. Tento dokument popisuje nasazení různých systémů správy relační databáze (RDBMS) a souvisejících produktů v kombinaci s SAP na Microsoft Azure virtuální počítače (VM) pomocí infrastruktury Azure jako možnosti služby (IaaS).

Dokumentu doplňuje SAP instalace dokumentace a poznámky k SAP, které představují primární prostředky pro instalace a nasazení SAP softwaru na dané platformy.

## <a name="general-considerations"></a>Obecné aspekty
V této kapitole vydávají aspektů s databázového systému SAP související systémy ve virtuálních počítačích Azure. Existuje několik odkazy na konkrétní systémy databázového systému v této kapitole. Místo toho konkrétní systémy databázového systému jsou zpracovávány v tomto dokumentu po této kapitoly.

### <a name="definitions-upfront"></a>Definice předem
V dokumentu můžeme použít následující podmínky:

* IaaS: Infrastruktura jako služba.
* PaaS: Platforma jako služba.
* SaaS: Software jako služba.
* Součást SAP: jednotlivých SAP aplikace například ECC, BW, správce řešení nebo podnikovém portálu.  SAP součástí může být založen na tradičních technologií ABAP nebo Java nebo jiných NetWeaver na základě aplikaci, například obchodních objektů.
* Prostředí SAP: jeden nebo více součástí SAP logicky seskupeny provést obchodní funkce jako je například vývoj, QAS, školení, zotavení po Havárii nebo produkční.
* SAP na šířku: Vztahuje se k celé SAP prostředků v zákazníka na šířku IT. Na šířku SAP zahrnuje všechny produkční a mimo provozní prostředí.
* Systém SAP: Kombinace databázového systému vrstvu a aplikační vrstvu služby, například SAP ERP vývojového systému SAP BW testovací systém, produkční systému SAP CRM, atd. V nasazeních Azure není možné rozdělit tyto dvě vrstvy mezi místními a Azure. To znamená, že systému SAP buď je nasazena místně nebo je nasazené v Azure. Můžete však nasadit různých systémech SAP šířku v Azure nebo místní. Můžete například nasazení SAP CRM vývoj a testování systémy v Azure, ale SAP CRM produkční systému místní.
* Nasazení jenom cloudu: nasazení, kde není připojen k předplatnému Azure prostřednictvím site-to-site nebo připojením ExpressRoute na místní infrastrukturu sítě. Společné dokumentace k Azure tyto typy nasazení jsou také popsány jako "Jen cloudu" nasazení. Virtuální počítače nasazené pomocí této metody jsou přístupné přes Internet a veřejné koncové body Internetu přiřazené k virtuálním počítačům v Azure. Místní služby Active Directory (AD) a DNS není rozšířené k Azure v těchto typů nasazení. Proto virtuální počítače nejsou součástí místní služby Active Directory. Poznámka: Jenom pro Cloud nasazení v tomto dokumentu jsou definovány jako dokončení krajiny SAP, které jsou spuštěny v Azure bez rozšíření služby Active Directory nebo překlad výhradně z místního do veřejného cloudu. Jenom pro cloud konfigurace nejsou podporovány pro produkční systémy SAP nebo konfigurace, kdy moduly STM SAP nebo jiných místních prostředků je potřeba použít mezi systémy SAP hostované v Azure a prostředky, které se nacházejí na místní.
* Mezi různými místy: Popisuje scénář, kde jsou nasazené virtuální počítače k předplatnému Azure, který má site-to-site, více lokalit nebo připojení ExpressRoute mezi místní datových centrech a Azure. Dokumentace k společné Azure, tyto typy nasazení jsou také popsány jako mezi různými místy scénáře. Z důvodu pro připojení je rozšířit místní domény, místní služby Active Directory a DNS místně do Azure. Na šířku místní je rozšířeno na Azure prostředky předplatného. S touto příponou, virtuálních počítačů může být součástí místní domény. Uživatelé domény místní domény, můžete přístup k serverům a službu lze spouštět na těchto virtuálních počítačů (např. služby databázového systému). Komunikace a název rozlišení mezi virtuálními počítači nasazen místní a virtuální počítače nasazené v Azure je možné. Očekáváme, že to být nejběžnější scénáře pro nasazení SAP prostředky v Azure. Další informace najdete v tématu [v tomto článku] [ vpn-gateway-cross-premises-options] a [v tomto článku][vpn-gateway-site-to-site-create].

> [!NOTE]
> Mezi různými místy nasazení SAP systémy, kde Azure Virtual Machines s SAP systémy jsou členy místní domény jsou podporovány pro produkční systémy SAP. Konfigurace mezi různými místy jsou podporovány pro nasazení částí nebo dokončení krajiny SAP do Azure. I spuštění povahu dokončení SAP v Azure vyžaduje, že tyto virtuální počítače byly součástí místní domény a služby Active Directory. V předchozí verze dokumentace, už jsme mluvili o IT hybridní scénáře, kde termín *hybridní* je integrován do skutečnost, že je připojení mezi různými místy mezi místními a Azure. V takovém případě *hybridní* také znamená, že virtuální počítače v Azure jsou součástí služby Active Directory v místě.
> 
> 

Některé dokumentaci Microsoft popisuje scénáře mezi různými místy trochu jinak, zejména pro konfigurace HA databázového systému. V případě SAP související dokumenty scénář varu mezi různými místy dolů s site-to-site ani privátní připojení (ExpressRoute) a na skutečnost, že SAP šířku rozděluje mezi místními a Azure.

### <a name="resources"></a>Zdroje a prostředky
Následující příručky jsou k dispozici pro nasazení SAP na Azure:

* [Azure virtuálních počítačů, plánování a implementace pro SAP NetWeaver][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP NetWeaver][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP NetWeaver (Tento dokument)][dbms-guide]

Následující poznámky k SAP souvisí s SAP v Azure:

| Poznámka: číslo | Nadpis |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: typy podporovaných produktů a virtuální počítač Azure |
| [2015553] |SAP na platformě Microsoft Azure: podporovat požadavky |
| [1999351] |Řešení potíží s rozšířenou Azure monitorování pro SAP |
| [2178632] |Klíč monitorování metriky pro SAP na platformě Microsoft Azure |
| [1409604] |Virtualizace v systému Windows: rozšířené monitorování |
| [2191498] |SAP v systému Linux s Azure: rozšířené monitorování |
| [2039619] |SAP aplikací v Microsoft Azure pomocí databáze Oracle: podporované produkty a verze |
| [2233094] |DB6: Aplikace SAP v Azure pomocí IBM DB2 pro Linux, UNIX a Windows - Další informace |
| [2243692] |Linux na Microsoft Azure (IaaS) virtuálních počítačů: problémy licence SAP |
| [1984787] |Systému SUSE LINUX Enterprise Server 12: Poznámky k instalaci |
| [2002167] |Red Hat Enterprise Linux 7.x: instalace a Upgrade |
| [2069760] |Oracle Linux 7.x SAP instalace a Upgrade |
| [1597355] |Doporučení odkládacího prostoru pro Linux |
| [2171857] |Oracle Database 12c – podpora systému souborů v systému Linux |
| [1114181] |Databáze Oracle 11g – podpora systému souborů v systému Linux |


Přečíst také [oznámení změny stavu Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) obsahující všechny SAP poznámky pro Linux.

Měli byste mít praktické znalosti o Architektura Microsoft Azure a jak jsou nasadit a provozovat virtuální počítače Microsoft Azure. Můžete najít další informace <https://azure.microsoft.com/documentation/>

> [!NOTE]
> Snažíme se **není** hovoříte o platforma Microsoft Azure jako služba (PaaS) nabídky na platformě Microsoft Azure. Tento dokument je o spuštění systému správy databáze (databázového systému) v Microsoft Azure Virtual Machines (IaaS), jako databázového systému by byl spuštěn v místním prostředí. Možnosti databáze a funkce mezi tyto dvě nabídky se velmi liší a nesmí promíchala mezi sebou. Viz také: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Vzhledem k tomu, že budeme se zabývat IaaS, obecně se instalace systému Windows, Linux a databázového systému a konfigurace jsou v podstatě stejné jako jakékoli virtuálního počítače nebo úplné systému počítače, které by nainstalujete místně. Existují však některé architektura a systém správy implementace rozhodnutí, která se liší, při použití IaaS. Účelem tohoto dokumentu je popisují konkrétní architektury a rozdíly systému správy, které musí být přípravy na při použití IaaS.

Celkové oblasti rozdílem, že tento dokument popisuje jsou obecně:

* Plánování správné rozložení virtuální počítač nebo disk systémů SAP do, zda že máte správná data souboru rozložení a můžete dosáhnout dostatek IOPS pro úlohy.
* Požadavky sítě při použití IaaS.
* Funkce konkrétní databáze má použít k optimalizaci rozložení databáze.
* Aspekty zálohování a obnovení v IaaS.
* Využívá různé typy Image pro nasazení.
* Vysoké dostupnosti v Azure IaaS.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktura RDBMS nasazení
Aby bylo možné postupovat podle této kapitoly, je potřeba pochopit, co se zobrazí v [to] [ deployment-guide-3] kapitoly [Průvodce nasazením][deployment-guide]. Informace o různých virtuálních počítačů-Series a jejich rozdíly a rozdíly Azure Standard a Premium úložiště by měl rozumím jim a známé před čtením této kapitoly.

Dokud března 2015 byly omezený na 127 GB velikost disků, které obsahují operačního systému. Toto omezení získali odvolat v března 2015 (Další informace o kontrole <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). Odtud na discích obsahující operační systém může mít stejnou velikost jako jakýkoli jiný disk. Nicméně nám stále přednost struktura nasazení, kde jsou oddělené od databázové soubory operačního systému, databázového systému a případný SAP binární soubory. Proto Očekáváme, že SAP systémy s operačním systémem v Azure Virtual Machines základní virtuálního počítače (nebo disk) nainstalované s operačním systémem, spustitelné soubory systému správy databáze a SAP spustitelné soubory. Soubory protokolu a data databázového systému jsou uložené ve službě Azure Storage (Standard nebo Premium Storage) v různých discích a připojit k původní image Azure operačního systému virtuálního počítače jako logické disky. 

Závisí na využívání Azure Standard nebo Premium Storage (například pomocí služby DS-series nebo GS-series virtuálních počítačů) existuje jsou ostatní kvóty v Azure, které jsou popsány [zde (Linux)] [ virtual-machines-sizes-linux] a [zde (Windows)][virtual-machines-sizes-windows]. Při plánování vaší rozložení disku, budete muset najít nejlepší rovnováhu mezi kvót pro následující položky:

* Počet datových souborů.
* Počet disků, které obsahují soubory.
* Kvóty IOPS jednoho disku.
* Propustnost dat na disk.
* Počet dalších datových disků na jeden velikost virtuálního počítače.
* Celkovou propustnost úložiště může poskytovat virtuálního počítače.

Azure vynucuje kvóty IOPS podle datový disk. Tyto kvóty se liší pro disky hostované na úložiště Azure úrovně Standard a Premium Storage. Latence vstupně-výstupních operací se také velmi liší mezi těmito dvěma typy úložiště Storage úrovně Premium doručování faktory lepší latence vstupně-výstupní operace. Každý jiný typ virtuálního počítače je omezený počet datových disků, které budete moci připojit. Další omezení je, že pouze určité typy virtuálních počítačů můžete využít Azure Premium Storage. To znamená, že rozhodnutí pro určitý typ virtuálního počítače nemusí pouze bude týkat procesoru a paměti požadavky, ale také podle IOPS, latenci a disku propustnost požadavky, které jsou obvykle škálovat počet disků nebo typ disky úložiště Premium. Zejména s Storage úrovně Premium velikost disku také může být závisí na počtu IOPS a propustnosti, kterou je potřeba dosáhnout každého disku.

Skutečnost, že celkovou rychlost IOPS, počet disků připojit, a velikost virtuálního počítače, jsou všechny svázané společně, může způsobit konfigurace systému SAP jiné než její místní nasazení aplikace Azure. Omezení IOPS na logické jednotky jsou obvykle konfigurovatelná v místním nasazení. Vzhledem k tomu s Azure Storage jsou tyto limity pevný nebo jako úložiště Premium závisí na typu disku. Proto s místní nasazení vidíte zákazníka konfigurace databázové servery, které používají pro speciální spustitelné soubory jako SAP a databázového systému nebo speciální svazky pro dočasné databáze nebo tabulka prostory mnoho různých svazcích. Pokud v místním systému je přesunuta do Azure, může vést k plýtvání potenciální IOPS šířky pásma pomocí plýtvání disk pro spustitelné soubory nebo databáze, které neprovádějte žádné nebo není spoustu IOPS. Proto ve virtuálních počítačích Azure doporučujeme, aby spustitelné soubory databázového systému a SAP Pokud je to možné nainstalovat na disk operačního systému.

Umístění souborů databáze a soubory protokolu a typ úložiště Azure používají, nesmí být definován IOPS, latence a propustnosti požadavky. Chcete-li mít dostatek IOPS pro protokol transakcí, mohlo by být vynuceno využívat více disků pro soubor protokolu transakcí nebo použijte větší disk úložiště Premium. V takovém případě by jeden vytvořit softwaru diskového pole RAID (pro příklad Windows úložiště fondu pro Windows nebo MDADM a LVM (Správce logických svazku) pro Linux) s disky, které obsahují transakčního protokolu.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Jednotka D:\ virtuální počítač Azure je jednotka netrvalý, který je zálohovaný díky některé místní disky na Azure výpočetním uzlu. Protože se jedná, netrvalý, to znamená, že všechny změny obsahu na jednotku D:\ bude ztracena, jakmile je virtuální počítač restartovat. "Jakékoliv změny" jsme znamená uložené soubory, adresáře vytvořené, nainstalované aplikace, atd.
> 
> ![Linux][Logo_Linux] Linux
> 
> Virtuální počítače Azure s Linuxem jednotku v /mnt/resource, který se nachází na dočasné jednotce založenou na místní disky na Azure výpočetním uzlu automaticky připojit. Protože se jedná, netrvalý, to znamená, že všechny změny na obsah v /mnt/resource byly ztraceny, když je virtuální počítač restartovat. Jakékoliv změny jsme znamená soubory uložené, adresáře vytvořené, nainstalované aplikace, atd.
> 
> 

- - -
Závisí na virtuální počítač Azure-series, místní disky na výpočetním uzlu zobrazit různé výkonu, které můžou být zařazené do kategorie jako:

* A0 A7: Velmi omezená výkonu. Nejde použít pro všechno, co je nad rámec stránkovací soubor windows
* A8-A11: Velmi dobré výkonové charakteristiky se některé deset tisíc IOPS a > propustnost 1GB/s
* D-Series: Velmi dobré výkonové charakteristiky se některé deset tisíc IOPS a > propustnost 1GB/s
* DS-Series: Velmi dobré výkonové charakteristiky se některé deset tisíc IOPS a > propustnost 1GB/s
* G-Series: Velmi dobré výkonové charakteristiky se některé deset tisíc IOPS a > propustnost 1GB/s
* GS-Series: Velmi dobré výkonové charakteristiky se některé deset tisíc IOPS a > propustnost 1GB/s

Výše uvedené příkazy se má použít pro typy virtuálních počítačů, které jsou certifikované s SAP. Virtuální počítač series s vynikající IOPS a propustnost dosáhlo nároku na využívání některé funkce databázového systému, jako jsou databáze tempdb nebo místa na dočasné tabulky.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Ukládání do mezipaměti pro virtuální počítače a datové disky
Při vytváření datových disků prostřednictvím portálu nebo pokud jsme připojit nahrané disky pro virtuální počítače, jsme můžete vybrat, jestli jsou do mezipaměti vstupně-výstupní provoz mezi virtuálního počítače a těchto disků umístěný v úložišti Azure. Azure Standard a Premium Storage použít dvě různé technologie pro tento typ mezipaměti. V obou případech by mezipaměti samotné disku založenou na stejné jednotky využívané dočasným diskovým (D:\ v systému Windows) nebo /mnt/resource v systému Linux virtuálního počítače.

Pro Azure Standard Storage mezipaměti možné typy jsou:

* Žádné ukládání do mezipaměti
* Mezipaměti pro čtení
* Čtení a ukládání do mezipaměti

Chcete-li získat konzistentní a deterministický výkonu, byste měli nastavit ukládání do mezipaměti na Azure Standard Storage pro všechny disky obsahující **data související s databázového systému souborů, souborů protokolu a tabulky místa pro 'NONE'**. Ukládání do mezipaměti virtuálního počítače může zůstat s výchozím.

Pro Storage úrovně Premium existují tyto možnosti ukládání do mezipaměti:

* Žádné ukládání do mezipaměti
* Mezipaměti pro čtení

Doporučení pro Storage úrovně Premium je možné využít **číst ukládání do mezipaměti pro datové soubory** databázi SAP a pokusit **bez ukládání do mezipaměti pro disky soubory protokolu**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>Softwaru diskového pole RAID
Jak již uvedené výše, musíte vyvážit počet IOPS potřebné pro soubory databáze mezi počet disků, které můžete konfigurovat, a maximální IOPS virtuálního počítače Azure poskytuje na disku nebo disk typu Premium Storage. Nejjednodušší způsob, jak řešit zatížení IOPS přes disků je sestavení softwaru diskového pole RAID přes různé disky. Pak umístíte počet datových souborů databázového systému SAP na jednotky LUN carved mimo softwaru diskového pole RAID. Závislé na požadavky, které že můžete vzít v úvahu využití úložiště Premium také od dvě ze tří různých disky úložiště Premium poskytují vyšší kvóty IOPS než disky podle standardního úložiště. Kromě toho významné lepší latence vstupně-výstupních operací poskytované Azure Premium Storage. 

Totéž platí i pro transakční protokol různých systémů databázového systému. S mnohými z nich přidat další soubory protokolu nepomůže vzhledem k tomu, že systémy databázového systému zapisovat do jednoho ze souborů vždy pouze. Pokud jsou potřeba vyšší rychlosti IOPS než jednu standardní úložiště založené může poskytnout disk, můžete rozkládají přes víc disků Standard Storage nebo můžete použít větší typ disku Storage úrovně Premium, který kromě vyšší rychlosti IOPS také nabízí faktory menší latence pro zápis vstupně-výstupních operací do transakčního protokolu.

Situace došlo v Azure nasazení, které by upřednostnit pomocí softwaru diskového pole RAID jsou:

* Transakční protokol/opakování protokol vyžadují více procesorů než Azure poskytuje pro jeden disk. Jak je uvedeno nahoře to bude možné vyřešit podle budovy logické jednotce LUN přes víc disků pomocí softwaru diskového pole RAID.
* Nevyrovnaná vstupně-výstupní úlohy distribuce přes různé datové soubory databázi SAP. V takových případech jeden prostředí stiskne kvótu místo často jeden datový soubor. Zatímco brzy bude dosaženo kvóty IOPS jednoho disku se nezobrazují i jiné datové soubory. V takových případech Nejsnazším řešením je k vytvoření jedné logické jednotce přes víc disků pomocí softwaru diskového pole RAID. 
* Nevíte, co přesně vstupně-výstupní úlohy za datový soubor a pouze zhruba věděli, jakou celkové zatížení IOPS proti databázového systému. Je nejjednodušší udělat k vytvoření jedné logické jednotce pomocí softwaru diskového pole RAID. Součet kvót více disků za tuto logickou jednotku by pak splnění známé rychlost IOPS.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Doporučujeme použít prostory úložiště ve Windows, pokud používáte v systému Windows Server 2012 nebo vyšší. Je efektivnější než proložení Windows starších verzí systému Windows. Musíte může vytvořit fondy úložiště systému Windows a prostory úložiště pomocí příkazů prostředí PowerShell, při použití systému Windows Server 2012 jako operační systém. Příkazy prostředí PowerShell naleznete zde <https://technet.microsoft.com/library/jj851254.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> K vytvoření softwaru diskového pole RAID v systému Linux jsou podporovány pouze MDADM a LVM (Správce logických svazek). Další informace najdete v následujících článcích:
> 
> * [Konfigurace softwaru diskového pole RAID v systému Linux][virtual-machines-linux-configure-raid] (pro MDADM)
> * [Konfigurace LVM na virtuální počítač s Linuxem v Azure][virtual-machines-linux-configure-lvm]
> 
> 

- - -
Důležité informace pro využívání virtuálních počítačů series, které mohou pracovat s Azure Premium Storage obvykle jsou:

* Požadavky pro latenci vstupně-výstupních operací, které již brzy bude dosaženo co poskytovat zařízení sítě SAN nebo NAS.
* Vyžádání pro faktory lepší vstupně-výstupních operací latenci než může poskytnout Azure Standard Storage.
* Vyšší IOPS na virtuálních počítačů než co bylo možné dosáhnout s více disky standardní úložiště pro určitý typ virtuálního počítače.

Vzhledem k tomu, že základní úložiště Azure replikuje každého disku, do alespoň tři uzly úložiště jednoduché RAID 0 proložení lze použít. Není nutné implementovat RAID5 nebo RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage
Microsoft Azure Storage ukládá základní virtuální počítač (s operačním systémem) a disky nebo objekty BLOB na alespoň tři uzly samostatné úložiště. Při vytváření účtu úložiště nebo spravovaných disků, při volbě ochrany, jak je vidět tady:

![Geografická replikace povolena pro účet úložiště Azure][dbms-guide-figure-100]

Místní účet replikace Azure Storage (místně redundantní) poskytuje úrovně ochrany proti ztrátě dat v důsledku selhání infrastruktury, které několik zákazníků může dovolit nasazení. Jako v příkladu nahoře, že se s pátá se varianta jeden ze tří první čtyři různé možnosti. Vyhledávání blíže v nich jsme možné rozlišit:

* **Premium místně redundantní úložiště (LRS)**: Azure Premium Storage nabízí podporu vysoce výkonné, nízkou latencí disku pro virtuální počítače spuštěné I náročnými úlohy. Existují tři repliky dat v rámci stejné datové centrum Azure z oblasti Azure. Kopie jsou v různých selhání a upgradu domén (koncepty, najdete v tématu [to] [ planning-guide-3.2] kapitoly v [Planning Guide][planning-guide]). V případě replika dat přejdete mimo provoz z důvodu selhání uzlu úložiště nebo selhání disku se automaticky vytvoří novou repliku.
* **Místně redundantní úložiště (LRS)**: V tomto případě existují tři repliky dat v rámci stejné datové centrum Azure z oblasti Azure. Kopie jsou v různých selhání a upgradu domén (koncepty, najdete v tématu [to] [ planning-guide-3.2] kapitoly v [Planning Guide][planning-guide]). V případě replika dat přejdete mimo provoz z důvodu selhání uzlu úložiště nebo selhání disku se automaticky vytvoří novou repliku. 
* **Geograficky redundantní úložiště (GRS)**: V tomto případě je asynchronní replikaci, která kanály další tři repliky dat v jiné oblasti Azure, která je ve většině případů ve stejné zeměpisné oblasti (např. Severní Evropa a západní Evropa ). Výsledkem tři další repliky, aby byly v součet šesti repliky. Varianta to je doplněk, kde dat v oblasti geograficky replikované Azure lze pro čtení účely (Read-Access Geo-redundantní).
* **Zónu redundantní úložiště (ZRS)**: V tomto případě tři repliky data zůstat ve stejné oblasti Azure. Jak je popsáno v [to] [ planning-guide-3.1] kapitoly [Planning Guide] [ planning-guide] oblast Azure může být počet datových centrech v těsné blízkosti. V případě LRS by repliky distribuovány prostřednictvím různých datových centrech, který jedné oblasti Azure.

Další informace naleznete [sem][storage-redundancy].

> [!NOTE]
> Pro nasazení databázového systému se nedoporučuje využití geograficky redundantní úložiště
> 
> Azure geografická replikace úložiště je asynchronní. Replikace jednotlivé disky připojené k jedné virtuálnímu počítači nejsou synchronizovány v kroku zámku. Proto není vhodný pro replikaci databázového systému souborů, které jsou distribuovány na jiné disky nebo nasadit proti softwaru založené na více disků RAID. Software databázového systému vyžaduje, aby trvalé úložný prostor na disku se synchronizuje přesněji v různých logických jednotek a základní disky nebo disky. Software databázového systému používá různé mechanismy pro aktivity pořadí vstupně-výstupní operace zápisu a databázového systému hlásí, že úložiště na disku, který je cílem replikace je poškozená, pokud tyto i liší v závislosti na několik milisekund. Proto pokud jeden chce konfigurace databáze s databází na více disků geograficky replikované roztažen tak, takové replikace se musí provést s prostředky databáze a funkce. Jeden neměli spoléhat na Azure úložiště geografická replikace k provedení této úlohy. 
> 
> Problém je nejjednodušší vysvětlit, třeba systém. Předpokládejme, že máte systému SAP nahraje do Azure, který má osm disky obsahující datové soubory databázového systému plus jeden disk obsahující soubor protokolu transakcí. Každé z nich tyto devět disků mít data zapsaná do je konzistentní metoda podle databázového systému, zda data probíhá zápis do souborů protokolu dat nebo transakcí.
> 
> V, aby se správně geograficky replikovat data a zachovat bitovou kopii databáze konzistentní obsah všech devět disků by mohl být geograficky replikované v uvedeném pořadí vstupně-výstupních operací měla spustit pro devět různých disků. Geografická replikace Azure Storage, ale neumožňuje deklarovat závislosti mezi disky. To znamená, že geografická replikace úložiště Microsoft Azure neví o tom, že obsah v těchto devět různých disků se vztahují k sobě navzájem, a že změny dat jsou konzistentní pouze v případě, že replikace v pořadí vstupně-výstupních operací se stalo napříč všemi devět disky.
> 
> Kromě toho se vysoké, že geograficky replikované bitové kopie v tomto scénáři neposkytují image konzistentní databáze, také je snížení výkonu, který se zobrazí s geograficky redundantní úložiště, které může vážně šance dopad na výkon. V souhrnu nepoužívejte tento typ redundance úložiště pro úlohy typu databázového systému.
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mapování virtuálních pevných disků do účtů úložiště služby virtuální počítač Azure
Tato kapitola platí jenom pro účty úložiště Azure. Pokud máte v úmyslu použít spravované disky, platí omezení výše v této kapitole se nevztahují. Další informace o discích spravovaných, najdete v kapitole [spravované disky] [ dbms-guide-managed-disks] tohoto průvodce.

Účet úložiště Azure je pouze pro správu konstrukce, ale také předmětem omezení. Zatímco se omezeních lišit na tom, jestli mluvíme o standardní účet úložiště Azure nebo prémiový účet úložiště Azure. Jsou uvedeny přesný možnostmi a omezeními [sem][storage-scalability-targets]

Proto pro Azure Standard Storage je důležité si uvědomit, je omezen na IOPS na účet úložiště (řádek obsahující **celkový počet požadavků** v [článek][storage-scalability-targets]). Kromě toho existuje počáteční maximální 100 účtů úložiště za předplatné Azure (k červenci 2015). Proto se doporučuje vyvážit IOPS z virtuálních počítačů mezi více účtů úložiště při použití Azure Standard Storage. Zatímco jeden virtuální počítač v ideálním případě pokud je to možné používá jeden účet úložiště. Takže pokud mluvíme o databázového systému nasazení, kde může každý virtuální pevný disk, který je hostován na Azure Standard Storage dosažení limitu kvóty, měli byste pouze nasadit 30-40 virtuální pevné disky na účet úložiště Azure, která používá Azure Standard Storage. Na druhé straně Pokud využít Azure Premium Storage a chcete ukládat velké databáze svazků, je dobře z hlediska IOPS. Ale prémiový účet úložiště Azure je ve svazku data způsobem více omezující než standardní účet úložiště Azure. V důsledku toho lze nasadit pouze omezený počet virtuálních pevných disků v rámci účtu Azure Premium Storage před stiskne limit dat svazek. Na konci představit jako "virtuální síť SAN, která má omezené možnosti ve IOPS a kapacity účtu úložiště Azure. Úkol zůstane v důsledku toho jako místní nasazení, můžete definovat rozložení virtuálních pevných disků různých systémů SAP přes různé 'pomyslná zařízení sítě SAN, nebo účty úložiště Azure.

Pro Azure Standard Storage není doporučeno prezentace úložišť jiným účtům úložiště pro jeden virtuální počítač, pokud je to možné.

Pokud používáte DS nebo GS-series virtuálních počítačů Azure, je možné připojit virtuální pevné disky mimo standardní účty úložiště Azure a účty úložiště Premium. Případy použití, jako je zápis do standardního úložiště záloh zálohovaný virtuální pevné disky, které mají databázového systému data a soubory protokolu na Storage úrovně Premium se k rozhodnutí, kde může využít takové heterogenní úložiště. 

Na základě zákaznických nasazení a testování přibližně 30 až 40 virtuální pevné disky obsahující data soubory databáze a soubory protokolu se dá zřídit v jednom Azure standardní účet úložiště s přijatelný výkon. Jak už bylo zmíněno dříve, bude nejspíš kapacity dat, kterou lze nainstalovat a není IOPS omezení prémiový účet úložiště Azure.

Jako síť SAN zařízení místní, sdílení vyžaduje některá monitorování s cílem nakonec detekovat kritická místa na účet úložiště Azure. Rozšíření monitorování Azure pro SAP a portálu Azure jsou nástroje, které lze použít k detekci zaneprázdněn účtech úložiště Azure, který může být doručování zhoršené výkon vstupně-výstupní operace.  Pokud se v této situaci detekuje, doporučujeme přesunout zaneprázdněn virtuálních počítačů na jiný účet úložiště Azure. Odkazovat [Průvodce nasazením] [ deployment-guide] podrobnosti o tom, jak aktivovat SAP hostitelským možnosti monitorování.

Jiný článek shrnutí osvědčené postupy v oblasti Azure Standard Storage a standardních účtech úložiště Azure naleznete zde <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>Spravované disky
Spravované disky jsou nového typu prostředku v Azure Resource Manager, který lze použít místo virtuální pevné disky, které jsou uložené v účtech úložiště Azure. Spravované disky automaticky zarovnané s skupina dostupnosti virtuálního počítače, které jsou připojené k a proto zvýšit dostupnost virtuálního počítače a služby, které jsou spuštěny na virtuálním počítači. Další informace, přečtěte si [článek s přehledem](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

SAP aktuálně podporuje jenom disky spravované Premium. Poznámka SAP čtení [1928533] další podrobnosti.

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Přesun nasazení databázového systému virtuálních počítačů z Azure Standard Storage do Azure Premium Storage
Jsme setkají poměrně některých scénářích, kde jako zákazník chcete přesunout nasazený virtuální počítač z Azure Standard Storage do Azure Premium Storage. Pokud vaše disky jsou uložené v účtech úložiště Azure, to není možné bez fyzickým přesunutím data. Chcete-li dosáhnout několika způsoby:

* Všechny virtuální pevné disky, základní virtuální pevný disk a také data virtuálních pevných disků může zkopírujte do nového účtu úložiště Azure Premium. Často zvolili počet virtuálních pevných disků v Azure Standard Storage není z důvodu fakt, že je potřeba zavést datový svazek. Ale potřebné tolika virtuálních pevných disků kvůli IOPS. Teď, když přesunete na Azure Premium Storage je může přejít způsob méně virtuálních pevných disků, abyste dosáhli stejné propustnosti IOPS. Vzhledem k tomu, že v Azure Standard Storage platíte za používaná data a ne podle velikosti nominální disku, počet virtuálních pevných disků není důležité, z hlediska nákladů. Službou Azure Premium Storage, ale by platila pro velikost nominální disku. Většinu zákazníků, proto zkuste mějte počet virtuálních pevných disků Azure Premium Storage na číslo potřebná k dosažení propustnost IOPS nezbytné. Ano většina zákazníků rozhodnout proti způsob jednoduchou 1:1 kopie.
* Pokud není dosud připojen, připojte se jeden virtuální pevný disk obsahující zálohu databáze z databáze SAP. Po dokončení zálohování odpojte všechny virtuální pevné disky, včetně virtuální pevný disk obsahující zálohu a zkopírujte základní virtuální pevný disk a virtuální pevný disk s zálohování do účtu Azure Premium Storage. By pak nasaďte virtuální počítač založen na základní virtuální pevný disk a připojit virtuální pevný disk se zálohou. Teď vytvoříte další prázdný prémiové disky úložiště pro virtuální počítač, který slouží k obnovení databáze do. Předpokladem je, že databázového systému umožňuje změnit cesty k souborům protokolu a data v rámci procesu obnovení.
* Další možností je variace bývalé procesu, kde zkopírujte zálohování virtuálního pevného disku do Azure Premium Storage a připojte ji na virtuální počítač, který nově nasazení a instalaci.
* Čtvrtý možnosti, které jste by zvolte, pokud potřebují Chcete-li změnit počet datových souborů databáze. V takovém případě můžete provést kopie homogenního systému SAP pomocí exportu/importu. PUT ty exportovat soubory do virtuálního pevného disku, který se zkopíruje do prémiový účet úložiště Azure a jeho připojení k virtuální počítač, který používáte ke spouštění procesů importu. Zákazníci využít tuto možnost, hlavně v případě, že chcete snížení počtu datových souborů.

Pokud používáte spravované disky, můžete migrovat do úložiště úrovně Premium podle:

1. Zrušit přidělení virtuálního počítače
2. V případě potřeby změnit velikost virtuálního počítače na velikost, která podporuje službu Premium Storage (například DS nebo GS)
3. Změnit typ účtu spravované disku Premium (SSD)
4. Spustit virtuální počítač

### <a name="deployment-of-vms-for-sap-in-azure"></a>Nasazení virtuálních počítačů pro SAP v Azure
Microsoft Azure nabízí několik způsobů pro nasazení virtuálních počítačů a přidruženými disky. Tím je důležité pochopit rozdíly, od přípravy virtuálních počítačů se můžou lišit závisí na způsobu nasazení. Obecně platí podíváme do popsané v následujících kapitolách scénáře.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Nasazení virtuálních počítačů z Azure Marketplace
Chcete převést Microsoft nebo třetích stran, které poskytuje bitovou kopii z Azure Marketplace pro nasazení virtuálního počítače. Po nasazení virtuálního počítače v Azure použijte stejné pokyny a nástroje pro instalaci softwaru SAP uvnitř virtuálního počítače, jako byste to udělali v místním prostředí. Pro instalaci softwaru SAP ve virtuálním počítači Azure, SAP a Microsoft doporučujeme odesílání a uložit na instalačním médiu SAP v přírůstcích u disků nebo k vytvoření virtuálního počítače Azure funguje jako "souborový server", který obsahuje všechny potřebné SAP instalačního média.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Nasazení virtuálního počítače pomocí bitové kopie zobecněný zákaznické
Z důvodu opravy specifické požadavky týkající se vaší verzí operačního systému nebo databázového systému nemusí zadané bitové kopie v Azure Marketplace podle vašich potřeb. Proto může být potřeba vytvořit virtuální počítač pomocí vlastní "privátní" image operačního systému nebo databázového systému virtuálního počítače, které mohou být nasazeny několikrát později. Pro přípravu "privátní" image pro duplikaci, musí být zobecněn operačního systému na virtuálním počítači na místě. Odkazovat [Průvodce nasazením] [ deployment-guide] podrobnosti o tom, jak generalize virtuálního počítače.

Pokud jste již nainstalovali SAP obsah v místní virtuální počítač (hlavně u systémy vrstvě 2), můžete upravit nastavení systému SAP po nasazení virtuálního počítače Azure pomocí instance přejmenovat postup nepodporuje správce zřizování softwaru SAP (Poznámka SAP [1619720]). Jinak můžete nainstalovat SAP software později po nasazení virtuálního počítače Azure.

Jako databázi obsahu používá aplikace SAP, můžete buď vygenerovat obsah čerstvě instalací SAP nebo svůj obsah můžete importovat do Azure pomocí virtuální pevný disk s zálohu databáze databázového systému nebo využití funkcí databázového systému zálohovat přímo do  Microsoft Azure Storage. V takovém případě může také připravit virtuální pevné disky s databázového systému protokolu a data souborů místní a importovat tyto disky do Azure. Ale přenos dat databázového systému, který je načítán z místního do Azure by fungovat přes virtuální pevný disk disky, které musejí být připraveny na místě.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Přesunutí virtuálního počítače z místního do Azure s diskem zobecněn
Máte v úmyslu přesunout z místní konkrétního systému SAP do Azure (navýšení a shift). Tento krok můžete provést odesláním disku, které obsahuje operačního systému, SAP binární soubory a případné databázového systému binární soubory a disky se soubory protokolu a data z databázového systému Azure. V opačném scénář #2 výše, můžete zachovat název hostitele, identifikátor SID SAP a SAP uživatelské účty ve virtuálním počítači Azure byly nakonfigurované v místním prostředí. Proto generalizací bitovou kopii není nutné. Tento případ se většinou platí pro scénáře mezi různými místy, kde je součástí povahu SAP spouštět místně a částí v Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Vysoká dostupnost a zotavení po havárii s virtuálními počítači Azure
Azure nabízí následující vysoké dostupnosti (HA) a obnovení po havárii (DR) funkce, které platí pro různé součásti, které by používáme pro nasazení SAP a databázového systému

### <a name="vms-deployed-on-azure-nodes"></a>Virtuální počítače nasazené na uzly Azure
Platforma Azure nenabízí funkce, jako je migrace za provozu pro nasazené virtuální počítače. To znamená, že pokud v clusteru serveru, na kterém je nasazený virtuální počítač je nutné údržby, virtuální počítač je potřeba získat zastavena a restartována. Údržby v Azure se provádí pomocí tak názvem upgradu domén v rámci clusterů serverů. Pouze jeden upgradu domény současně je neudržují. Při restartování je přerušení služby během virtuální počítač je vypnutý, provádění údržby a restartování virtuálního počítače. Většina dodavatelů databázového systému ale poskytovat vysokou dostupnost a zotavení po havárii funkce, které rychle restartuje služby databázového systému v jiném uzlu, pokud není k dispozici primárního uzlu. Platforma Azure nabízí funkce pro distribuci virtuálních počítačů, úložiště a dalším službám Azure napříč doménami Upgrade k zajištění, aby plánované údržby nebo infrastruktury selhání by vliv pouze malou podmnožinu virtuální počítače a služby.  S pečlivé plánování, je možné dosáhnout úrovní dostupnosti srovnatelná místní infrastruktury.

Skupiny dostupnosti Microsoft Azure jsou logická seskupení virtuálních počítačů nebo služeb, které zajišťuje virtuálním počítačům a dalším službám jsou distribuovány do různých selhání a upgradu domén v rámci clusteru tak, že by existovat pouze jedna vypnutí uzlu v kterémkoli bodě v čase (čtení [této (Linux)] [ virtual-machines-manage-availability-linux] nebo [této (Windows)] [ virtual-machines-manage-availability-windows] další podrobnosti najdete v článku).

Je nutné konfigurovat tak, že účel při zavádění virtuálních počítačů, jak je vidět tady:

![Definice sadu dostupnosti pro HA databázového systému konfigurace][dbms-guide-figure-200]

Pokud nám chcete vytvořit vysoce dostupné konfigurace databázového systému nasazení (nezávisle na jednotlivé HA databázového systému funkce používá), by třeba virtuální počítače databázového systému:

* Přidat virtuální počítače do stejné virtuální síti Azure (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* Virtuální počítače konfigurace HA by měla být ve stejné podsíti. Překlad mezi různé podsítě není možné v čistě cloudové nasazení, pouze funguje překlad IP. Pomocí site-to-site nebo připojením ExpressRoute pro nasazení mezi různými místy, síť se alespoň jednu podsíť je už vytvořené. Překlad probíhá podle místní zásady a síťové infrastruktury služby AD. 



#### <a name="ip-addresses"></a>IP adresy
Důrazně doporučujeme nastavit virtuálních počítačů pro konfigurace HA odolným způsobem. Pokud se používají statické IP adresy, není spoléhat na IP adresy, adresy HA partnerů v rámci konfigurace HA spolehlivé v Azure. V Azure existují dva koncepty "Vypnout":

* Vypnutí dolů prostřednictvím portálu Azure nebo Azure PowerShell rutinu Stop-AzureRmVM: V tomto případě virtuální počítač získá vypnutí a zrušte přiděleny. Účtu Azure je už účtovat pro tento virtuální počítač, takže se jenom poplatky, které zpoplatněná za využívání úložiště. Pokud nebyla statickou privátní IP adresa síťového rozhraní, IP adresa se neuvolní a není zaručeno, že síťové rozhraní získá starou IP adresu přiřadit znovu po restartování virtuálního počítače. Provádění vypnutí dolů prostřednictvím portálu Azure nebo voláním Stop-AzureRmVM způsobí, že deaktivace přidělení. Pokud nechcete navrácení počítače použití Stop-AzureRmVM - StayProvisioned 
* Pokud vypnete virtuální počítač z úroveň operačního systému, získá virtuální počítač vypnout a není zrušte přiřazený. Ale v takovém případě účtu Azure je stále účtovat pro virtuální počítač přes skutečnost, že se jedná o vypnutí. V takovém případě zůstává beze změn přiřazení IP adresy do zastaveného virtuálního počítače. Vypínání virtuálního počítače z v rámci nevynutí automaticky deaktivace přidělení.

I pro scénáře mezi různými místy ve výchozím nastavení vypnutí a deaktivace přidělení znamená deaktivace přiřazení IP adres z virtuálního počítače, i když místní zásady v nastavení protokolu DHCP se liší. 

* Výjimka je popsán Pokud jednu statickou IP adresu přiřadí k síťovému rozhraní jako [sem][virtual-networks-reserved-private-ip].
* V takovém případě zůstává pevná IP adresa, tak dlouho, dokud se neodstraní síťové rozhraní.

> [!IMPORTANT]
> Chcete-li zachovat celého nasazení jednoduché a spravovat, zrušte doporučuje se nastavit partnerství databázového systému HA nebo konfigurace zotavení po Havárii v rámci Azure tak, že je funkční překlad mezi různé virtuální počítače spojené virtuálních počítačů.
> 
> 

## <a name="deployment-of-host-monitoring"></a>Nasazení hostitele monitorování
Pro produktivní využití SAP aplikací v Azure Virtual Machines SAP vyžaduje schopnost získat hostitele dat monitorování od fyzických hostitelů se spuštěnými virtuálními počítači Azure. Konkrétní úroveň oprav SAP Agent hostitele není třeba umožňující tuto funkci SAPOSCOL a Agent hostitele SAP. Úroveň přesný oprav je popsána v Poznámka SAP [1409604].

Podrobnosti týkající se nasazení komponent, které doručování dat hostitele a Agent hostitele SAP SAPOSCOL a správa životního cyklu těchto součástí [Příručka pro nasazení][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Podrobnosti k systému Microsoft SQL Server
### <a name="sql-server-iaas"></a>SQL Server IaaS
Od verze Microsoft Azure, můžete snadno migrovat existující aplikace SQL Server založený na platformě Windows Server na virtuálních počítačích Azure. SQL Server ve virtuálním počítači umožňuje snížit celkové náklady na vlastnictví nasazení, správu a údržbu enterprise spektra aplikací snadno migrací těchto aplikací do Microsoft Azure. Se systémem SQL Server v virtuální počítač Azure správci a vývojáři můžete dál používat stejné vývoj a nástroje pro správu, které jsou k dispozici místně. 

> [!IMPORTANT]
> Jsme nejsou hovoříte o Microsoft Azure SQL Database, který je platforma jako nabídka služeb platformy Microsoft Azure. Informace v tomto dokumentu jsou o spuštění produktu SQL Server, protože je znám pro místní nasazení v Azure Virtual Machines, využití infrastruktury jako funkce služby Azure. Možnosti databáze a funkce mezi tyto dvě nabídky se liší a nesmí promíchala mezi sebou. Viz také: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Doporučujeme zkontrolovat [to] [ virtual-machines-sql-server-infrastructure-services] dokumentace než budete pokračovat.

V následujících částech jsou kusy části dokumentace v části výše uvedený odkaz agregovat a uvedené. Specifika kolem SAP jsou také uvedené a některé pojmy jsou popsány podrobněji. Důrazně ale doporučujeme pracovat prostřednictvím dokumentace výše první než si přečtete dokumentace specifické pro službu SQL Server.

V IaaS konkrétní informace, které byste měli vědět před pokračováním je některé systému SQL Server:

* **Virtuální počítač SLA**: je SLA pro virtuální počítače běžící v Azure, které naleznete zde: <https://azure.microsoft.com/support/legal/sla/>  
* **Podpora verzí SQL**: pro zákazníky, SAP, podporujeme SQL Server 2008 R2 a vyšší na virtuální počítač Microsoft Azure. Nejsou podporované starší verze. Zkontrolujte tato obecná [prohlášení o odborné pomoci](https://support.microsoft.com/kb/956893) další podrobnosti. Všimněte si, že obecně systému SQL Server 2008 je společnost Microsoft podporuje také. Ale kvůli důležité funkce pro SAP, která byla zavedena v systému SQL Server 2008 R2, SQL Server 2008 R2 je minimální verze pro SAP. Mějte na paměti, že SQL Server 2012 a 2014 získali rozšířené o hlubší integrace do scénář IaaS (např. zálohování přímo s Azure Storage). Proto jsme omezení tento dokument k systému SQL Server 2012 a 2014 s jeho nejnovější úroveň oprav pro Azure.
* **Podpora funkce SQL**: funkce nejvíce systému SQL Server jsou podporovány ve virtuálních počítačích Microsoft Azure na několik výjimek. **SQL Server převzetí služeb při selhání pomocí sdílených disků není možné clusterování**.  Distribuované technologie jako databáze zrcadlení, skupiny dostupnosti AlwaysOn, replikace, přesouvání protokolu a služby Service Broker jsou podporovány v jedné oblasti Azure. SQL Server AlwaysOn taky je podporovaná mezi různých oblastech Azure, jak je uvedeno zde: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Zkontrolujte [prohlášení o odborné pomoci](https://support.microsoft.com/kb/956893) další podrobnosti. Příklad pro nasazení konfigurace funkce AlwaysOn je uveden v [to] [ virtual-machines-workload-template-sql-alwayson] článku. Také, podívejte se na osvědčené postupy popsané [sem][virtual-machines-sql-server-infrastructure-services] 
* **Výkon SQL**: jsme si jisti, že Microsoft Azure hostované virtuální počítače provést velmi dobře oproti jiné nabídky virtualizace veřejného cloudu, ale jednotlivé výsledky se může lišit. Podívejte se na [to] [ virtual-machines-sql-server-performance-best-practices] článku.
* **Pomocí bitové kopie z Azure Marketplace**: nejrychlejší způsob, jak nasadit nový virtuální počítač Microsoft Azure je použít bitovou kopii z Azure Marketplace. Existují bitové kopie v Azure Marketplace, které obsahují systému SQL Server. Bitové kopie, kde je již nainstalován systém SQL Server nelze použít pro aplikace SAP NetWeaver okamžitě. Důvodem je, že výchozí kolace systému SQL Server je nainstalován v rámci těchto bitových kopií a není potřeba systémy SAP NetWeaver kolace. Chcete-li použít tyto obrázky, zkontrolujte kroků popsaných v kapitole [pomocí bitové kopie systému SQL Server z webu Microsoft Azure Marketplace][dbms-guide-5.6]. 
* Podívejte se na [podrobnosti o cenách](https://azure.microsoft.com/pricing/) Další informace. [SQL Server 2012 Licensing průvodce](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) a [SQL Server 2014 licencování průvodce](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) jsou také důležité prostředků.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Pokyny pro konfigurace systému SQL Server pro instalace související SAP systému SQL Server ve virtuálních počítačích Azure
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Doporučení pro virtuální počítač nebo virtuální pevný disk strukturu pro nasazení SAP související systému SQL Server
V souladu s obecný popis spustitelné soubory systému SQL Server nachází nebo nainstalován do systémové jednotce disk operačního systému Virtuálního počítače (jednotka C:\).  Obvykle se většina systémových databází systému SQL Server nejsou využívaných na vysoké úrovni SAP NetWeaver zatížení. Proto může na jednotce C:\ také zůstat systémové databáze systému SQL Server (hlavní, databázi msdb a modelu). Výjimka může být databáze tempdb, v případě některých ERP SAP a všechny úlohy BW, může to vyžadovat vyšší datový svazek nebo vstupně-výstupních operací operations svazek, který se nevejde do původní virtuální počítač. Pro tyto systémy je možné provádět následující kroky:

* Přesuňte soubory dat primární databáze tempdb stejné logické jednotce jako primární datové soubory databáze SAP.
* Přidáte žádné další databáze tempdb datové soubory ke každému z jiné logické jednotky obsahující soubor dat databáze SAP uživatele.
* Přidejte soubor protokolu databáze tempdb na logické jednotce, která obsahuje soubor protokolu databáze uživatele.
* **Výhradně pro typů virtuálních počítačů, které používají místní SSD** v protokolu a výpočetní uzel databáze tempdb data soubory mohou být umístěny na jednotku D:\. Nicméně může se doporučuje použít víc datových souborech databáze tempdb. Mějte na paměti, že svazky jednotce D:\ se liší podle typu virtuálního počítače.

Tyto konfigurace povolit databáze tempdb využívat více místa, než je schopný poskytnout systémové jednotce. Aby bylo možné určit velikost správné databázi tempdb, jeden zkontrolujte velikost databáze tempdb na stávajících systémů, které se spustit místní. Kromě toho by taková konfigurace umožňuje IOPS čísla proti databázi tempdb nelze zadat s systémová jednotka. Znovu systémy, které jsou místní slouží k monitorování vstupně-výstupní úlohy proti databázi tempdb tak, aby odvozujete čísla IOPS, která byste měli vidět na vaše databáze tempdb.

Konfigurace virtuálního počítače, který používá systém SQL Server s databázi SAP a umístění souboru protokolu databáze tempdb a databázi tempdb dat na jednotku D:\ by vypadat podobně jako:

![Konfigurace referenčního virtuálního počítače Azure IaaS pro SAP][dbms-guide-figure-300]

Upozorňujeme, že jednotku D:\ má různou velikost závisí na typu virtuálního počítače. Závisí na požadavek na velikost databáze tempdb vám může vynutit pár databáze tempdb data a soubory protokolu s SAP protokolu a data soubory databáze v případech, kdy jednotku D:\ je příliš malá.

#### <a name="formatting-the-disks"></a>Formátování disky
Pro SQL Server systému souborů NTFS blokovat velikost pro disky obsahující data systému SQL Server a soubory protokolů by měl být 64 kB. Není nutné k formátování jednotku D:\. Tato jednotka obsahuje předem formátovaný.

Pokud chcete mít jistotu, že obnovení nebo vytváření databází není inicializace datové soubory podle vynulování obsah souborů, jeden by se ujistěte, že má uživatelský kontext, který služba SQL Server běží v určité oprávnění. Uživatelé ve skupině pro správu služby Windows obvykle mají tato oprávnění. Pokud služba SQL Server běží v kontextu uživatele Windows uživatel není správcem, budete muset přiřadit tento uživatel uživatelské právo **provádět úlohy údržby svazku**.  Zobrazit podrobnosti v tomto článku znalostní báze Microsoft: <https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>Dopad komprese databáze
V konfiguracích, kde vstupně-výstupní šířky pásma může představovat problém každý míry, což snižuje IOPS vám může pomoci stretch úlohy, jež možné spouštět v případě pomocí IaaS, jako je například Azure. Proto pokud to ještě neudělali, použití komprese stránka serveru SQL se doporučuje SAP i Microsoft před nahráním existující databázi SAP do Azure.

Doporučení k provedení komprese databáze před nahráním do Azure je dán ze dvou důvodů:

* Množství dat k odeslání je nižší.
* Doba trvání spuštění komprese je kratší, za předpokladu, že jeden může používat silnější hardware s více procesorů nebo větší šířku pásma vstupně-výstupních operací nebo méně vstupně-výstupních operací latence místně.
* Menší velikosti databáze může vést k nižší náklady pro přidělení disku

Komprese databáze pracuje také virtuálních počítačích Azure, jako místní. Další podrobnosti o tom, jak komprimovat existující databázi SQL serveru SAP najdete tady: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 – ukládání souborů databáze přímo na Azure Blob Storage
SQL Server 2014 otevře možnost ukládat databázové soubory přímo v úložišti objektů Blob Azure bez "obálku" virtuální pevný disk je obcházet. Zejména s použitím standardního úložiště Azure nebo menší typy virtuálních počítačů to umožňuje scénáře, kde lze překonat omezení IOPS, která vynucovaly omezený počet disků, které může být připojen k některé menší typy virtuálních počítačů. Tento postup funguje pro uživatelské databáze, ale ne pro systémové databáze systému SQL Server. Funguje i pro data a soubory protokolu serveru SQL Server. Pokud chcete nasadit databázi SQL serveru SAP tímto způsobem místo 'zabalení' do virtuální pevné disky, mějte na následující:

* Použít pro účet úložiště, které musí být ve stejné oblasti Azure jako ten, který se používá k nasazení virtuálních počítačů systému SQL Server běží v.
* Aspekty, které jsou uvedené dříve týkající se distribuce virtuálních pevných disků v různých účtech úložiště Azure platí pro tuto metodu také nasazení. Znamená počet vstupně-výstupních operací do omezení účtu úložiště Azure.

[comment]: <> (MSSedusch TODO ale tímto dojde k použití šířky pásma sítě a není úložiště šířky pásma, nebude ho?)

Zde jsou uvedeny podrobnosti o tomto typu nasazení: <https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

Chcete-li uložit datové soubory SQL Server přímo na Azure Premium Storage, musíte mít minimální verzi oprava SQL Server 2014, které jsou zde uvedeny: <https://support.microsoft.com/kb/3063054>. Ukládání souborů dat systému SQL Server v Azure Standard Storage funguje s vydaná verze systému SQL Server 2014. Velmi stejné opravy však obsahují další řadu opravy, které přímého použití Azure Blob Storage pro datové soubory SQL serveru a zálohy spolehlivější. Proto doporučujeme používat tyto opravy obecně.

### <a name="sql-server-2014-buffer-pool-extension"></a>Rozšíření fondu vyrovnávací paměti systému SQL Server 2014
SQL Server 2014 zavedly novou funkci, která se nazývá rozšíření fondu vyrovnávací paměti. Tato funkce rozšíření fondu vyrovnávací paměti systému SQL Server, který je uložen v paměti s druhou úroveň mezipaměti, kterou je zajištěna místní SSD server nebo virtuální počítač. To umožňuje ponechat větší pracovní sadu dat, v paměti'. Ve srovnání s přístup k Azure Standard Storage přístup do rozšíření fondu vyrovnávací paměti, který je uložený na místní SSD virtuální počítač Azure se mnoha faktorech rychlejší.  Využití místní jednotce D:\ typy virtuálních počítačů, které mají vynikající IOPS a propustnost proto může být velmi rozumný způsob, jak snížit zatížení IOPS Azure Storage a výrazně zlepšit dobu odezvy dotazů. To platí hlavně v případě, že není použití služby Premium Storage. V případě Storage úrovně Premium a využití mezipaměti pro čtení Azure Premium na výpočetním uzlu jsou doporučené pro datové soubory, očekávané žádné významné rozdíly. Důvodem je to, jak mezipaměti (rozšíření fondu vyrovnávací paměti systému SQL Server a mezipaměti pro čtení úložiště Premium) používáte místní disky výpočetních uzlů.
Další podrobnosti o této funkci najdete v této dokumentaci: <https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Důležité informace o zálohování nebo obnovení pro SQL Server
Při nasazování systému SQL Server do Azure musí být zkontrolovány vaše zálohování metodika. I když v systému není systémem produktivní, databázi SAP, který je hostitelem SQL Server je nutné zálohovat pravidelně. Vzhledem k tomu, že Azure úložiště udržuje tři bitové kopie, je nyní méně důležité v ohledem na kompenzace havárie úložiště zálohy. Z důvodu priority pro údržbu správné plán zálohování a obnovení je větší, který můžete kompenzovat chyby logické nebo ruční tím, že poskytuje bod v možnosti v době obnovení. Cílem je proto záloh buď použijte k obnovení databázi zpět do určité míry v čase nebo k použití zálohování v Azure počáteční hodnoty jiného systému zkopírováním existující databáze. Například je může přenáší z konfigurace SAP vrstvě 2 na instalaci systému 3vrstvé stejného systému obnovení ze zálohy.

Existují tři různé způsoby, jak zálohovat SQL Server do služby Azure Storage:

1. SQL Server 2012 CU4 a vyšší můžete nativně zálohování databází na adresu URL. To je podrobně popsán v blogu [novou funkčnost systému SQL Server 2014 - část 5 – zálohování a obnovení vylepšení](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx). Naleznete v kapitole [SQL Server 2012 SP1 CU4 nebo novější][dbms-guide-5.5.1].
2. Verze systému SQL Server před SQL 2012 CU4 můžete použít funkci přesměrování zálohování na disk VHD a v podstatě přesuňte směrem umístění úložiště Azure, který byl nakonfigurován zápisu datového proudu. Naleznete v kapitole [SQL Server 2012 SP1 CU3 a starších verzích][dbms-guide-5.5.2].
3. Poslední metodu je provést konvenční zálohování serveru SQL Server, na příkaz disku na disk zařízení. Toto je stejný jako vzoru místní nasazení a není podrobněji v tomto dokumentu.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 nebo novější
Tato funkce umožňuje přímo zálohování do Azure BLOB storage. Bez této metody musíte zálohovat na jiné disky, které by využívat disku a kapacity IOPS. Cílem je v podstatě to:

 ![Pomocí SQL Server 2012 zálohování Microsoft Azure Storage BLOB][dbms-guide-figure-400]

Výhoda spočívá v tomto případě jeden nepotřebuje zatěžovat disky, které chcete ukládat zálohy systému SQL Server. Je proto nutné méně disky, které jsou přidělené a celou šířku pásma disku IOPS lze použít pro soubory protokolu a data. Všimněte si, že maximální velikost zálohy je omezená na maximálně 1 TB, jak je uvedeno v části **omezení** v tomto článku: <https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations>. Pokud velikost zálohování navzdory pomocí kompresi zálohy systému SQL Server překročí velikost 1 TB, funkce popsané v kapitole [SQL Server 2012 SP1 CU3 a starších verzích] [ dbms-guide-5.5.2] v tomto dokumentu musí být použít.

[Související dokumentaci](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure) popisující obnovení databáze ze zálohy na úložišti objektů Blob Azure doporučujeme nechcete obnovit přímo z úložiště objektů BLOB v Azure, pokud je záloha > 25 GB. Doporučení v tomto článku je založena na důležité informace o výkonu a ne z důvodu omezení funkční. Proto různých podmínkách uplatnit na případ od případu.

Dokumentaci o tom, jak je tento typ zálohy nastavit a využít lze nalézt v [to](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) kurzu

Příklad pořadí kroků lze číst [zde](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Automatizace zálohování, je nejvyšší důležité, abyste měli jistotu, že jsou objekty BLOB pro každé zálohování jiný název. V opačném případě budou přepsána a je porušený řetězec obnovení.

Aby se kombinovat až věcí mezi tři různé typy záloh se doporučuje vytvořit různé kontejnery pod účet úložiště pro zálohy. Kontejnery může být virtuální počítač pouze nebo ve virtuálních počítačů a typ zálohy. Schéma může vypadat podobně jako:

 ![Pomocí SQL Server 2012 zálohování Microsoft Azure BLOB Storage – různé kontejnery v části samostatný účet úložiště][dbms-guide-figure-500]

V předchozím příkladu by provést zálohování do stejný účet úložiště, kde jsou nasazené virtuální počítače. Bude nový účet úložiště speciálně pro zálohování. V rámci účty úložiště by různé kontejnery, které jsou vytvořené pomocí matice typu zálohování a název virtuálního počítače. Takové segmentace je snazší správa záloh různých virtuálních počítačů.

Objekty BLOB, které jeden přímo zapíše zálohy, nejsou na počet data přidávání disků virtuálního počítače. Proto může jeden maximalizovat maximální počet datových disků připojit konkrétní verze SKU virtuálních počítačů pro data a soubor protokolu transakcí a spustit zálohu na kontejner úložiště. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 a starších verzích
Prvním krokem je třeba provést pro dosažení zálohování přímo s Azure Storage bude stáhnout soubor msi, který je propojený s [to](https://www.microsoft.com/download/details.aspx?id=40740) KBA článku.

Stažení x64 instalační soubor a v dokumentaci. Nainstaluje program s názvem souboru: **zálohování systému Microsoft SQL Server pro nástroj Microsoft Azure**. Přečtěte si důkladně dokumentaci produktu.  Nástroj v podstatě funguje následujícím způsobem:

* Na straně serveru SQL Server je definována umístění na disku pro zálohování serveru SQL Server (nepoužívejte jednotku D:\ jako umístění).
* Tento nástroj umožňuje definovat pravidla, která slouží k přímé různých typech zálohování pro různé kontejnery Azure Storage.
* Jakmile pravidla jsou na místě, nástroj přesměruje zápisu datového proudu zálohy na jeden z virtuálních pevných disků nebo disků do umístění úložiště Azure, který byl dříve definován.
* Nástroj ponechá malé se zakázaným inzerováním soubor několik velikosti KB na virtuální pevný disk nebo Disk, který byl definován pro SQL Server zálohování. **Tento soubor by měl být ponecháno na umístění úložiště, vzhledem k tomu, že je třeba, aby opakujte obnovení z Azure Storage.**
  * Pokud jste ztratili se zakázaným inzerováním souboru (například prostřednictvím ztrátě úložná média, která obsahovala soubor se zakázaným inzerováním) a vybrali jste možnost zálohování na účet Microsoft Azure Storage, může obnovit soubor se zakázaným inzerováním prostřednictvím služby Microsoft Azure Storage stažením z kontejneru úložiště, ve kterém je umístěn. Soubor se zakázaným inzerováním umístíte do složky v místním počítači, kde je nástroj nakonfigurované ke zjišťování a nahrajte do kontejneru, pomocí stejného hesla šifrování, pokud šifrování byl použit s původní pravidlo. 

To znamená, že schéma, jak je popsáno výše pro novější verze systému SQL Server můžete umístit na místě také pro verze systému SQL Server, které nejsou povolení přímé adresu umístění úložiště Azure.

Tato metoda by neměl být používá s novější verze systému SQL Server, které podporují základní až nativně Azure Storage. Výjimky jsou, kde se omezeních nativního zálohování do Azure blokování nativní zálohování provádění do Azure.

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>Další možnosti pro zálohování databází systému SQL Server
Další možnosti pro zálohování databáze je pro připojení k virtuální počítač, který použijete k uložení zálohy na dalších datových disků. V takovém případě musíte Ujistěte se, že disky nejsou spuštěné úplné. Pokud je to tento případ, musíte disky odpojit a proto do speak 'archivu"jej a nahraďte ji metodou nového prázdného disku. Pokud přejdete dolů této cestě, chcete zachovat tyto virtuální pevné disky v samostatných účtech úložiště Azure od těch, virtuální pevné disky s soubory databáze.

Druhá možnost se má používat velký virtuální počítač, který může mít mnoho disky připojené, například D14 s 32VHDs. Používejte prostory úložiště flexibilní prostředí sestavení, kde je sestavení sdílené složky, které jsou pak používány jako cíle zálohování pro různé servery databázového systému.

Některé z osvědčených postupů tu popsané [zde](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) také. 

#### <a name="performance-considerations-for-backupsrestores"></a>Důležité informace o výkonu pro zálohování a obnovování
Stejně jako u nasazení úplné obnovení je závislá na tom, kolik svazky lze číst souběžně a propustnost tyto svazky, které je možné výkonu zálohování a obnovení. Kromě toho může spotřeby procesoru, který je používán kompresi zálohy přehrát významnou roli na virtuálních počítačích s maximálně 8 vláken procesoru. Proto můžete předpokládat:

* Méně počet disků, na které se používají k ukládání dat souborů, tím menší celkovou propustnost v režimu čtení.
* Menší že počet procesoru vláken ve virtuálním počítači, závažnější dopad kompresi zálohy.
* Méně cílů (objekty BLOB a virtuální pevné disky nebo disky), k zápisu zálohování, nižší úrovně propustnosti.
* Menší virtuální počítač velikost, tím menší propustnost kvótu úložiště zápis a čtení ze služby Azure Storage. Nezávisle na tom, jestli jsou zálohy přímo uložené v Azure Blob, nebo zda jsou uloženy na virtuální pevné disky, které znovu ukládají do objektů BLOB Azure.

Při použití Microsoft Azure Storage BLOB jako cíl zálohování v novějších verzích, jste s omezeným přístupem k určení pouze jeden cíl adresy URL pro každé konkrétní zálohování.

Ale při použití "Microsoft SQL Server zálohování pro nástroj Microsoft Azure" starší verze, můžete definovat více než jeden soubor cíl. S více než jeden cíl zálohování můžete škálovat a je vyšší propustnost zálohování. Výsledkem by pak více souborů také v účtu úložiště Azure. Při testování, pomocí více cílů souboru můžete výborný dosáhnout propustnosti, která můžete dosáhnout pomocí zálohování rozšíření implementované v z SQL serveru 2012 SP1 CU4 na. Můžete také nejsou blokována bránou limit 1 TB jako nativního zálohování do Azure.

Nicméně, mějte na paměti, propustnost je také závisí na umístění účtu úložiště Azure, můžete použít pro zálohování. Najít účet úložiště v jiné oblasti než virtuální počítače běží v může být představu. Například spusťte konfigurace virtuálního počítače v oblasti západní Evropa, ale put účet úložiště, který používáte k zálohování proti v severní Evropě. Který určitě má vliv na zálohování propustnost a není pravděpodobně vygenerujte propustnost 150MB/s, jak se vám zdá, že v případech, kde cílového úložiště a virtuální počítače běží ve stejném datacentru regionální možné.

#### <a name="managing-backup-blobs"></a>Správa objektů BLOB zálohy
Není potřeba spravovat zálohy sami. Vzhledem k tomu, očekává se, že mnoho objektů BLOB jsou vytvořeny tak, že spustíte zálohování časté transakčního protokolu, správu těchto objektů BLOB snadno může přetížit portálu Azure. Je proto recommendable využívají služby Azure storage Exploreru. Existuje několik dobrý ty, které jsou k dispozici, které může pomoci spravovat účet úložiště Azure

* Microsoft Visual Studio sadou Azure SDK nainstalovaný (<https://azure.microsoft.com/downloads/>)
* Microsoft Azure Storage Explorer (<https://azure.microsoft.com/downloads/>)
* Nástroje třetích stran

Podrobnější informace o zálohování a SAP v Azure, najdete [příručce zálohování SAP](sap-hana-backup-guide.md) Další informace.

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Pomocí bitové kopie systému SQL Server z webu Microsoft Azure Marketplace
Společnost Microsoft poskytuje virtuální počítače v Azure Marketplace, které již obsahují verze systému SQL Server. Pro SAP zákazníky, kteří požadují licence pro SQL Server a Windows může to být příležitost v podstatě nepokrývají potřebu licencí podle roztočený až virtuální počítače se systémem SQL Server již nainstalován. Chcete-li použít tyto obrázky pro SAP, nutné provést následující aspekty:

* Zkušební verze systému SQL Server získat vyšší náklady než "Pouze pro systém Windows" virtuální počítač nasadit z Azure Marketplace. Naleznete v článcích k porovnání ceny: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> a <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Můžete použít jenom verze systému SQL Server, které jsou podporovány produktem SAP, jako je SQL Server 2012.
* Kolace instance systému SQL Server, který je nainstalován ve virtuálních počítačích, které nabízí Azure Marketplace není kolace SAP NetWeaver vyžaduje instanci systému SQL Server spustit. Když pomocí pokynů v následující části, můžete změnit kolaci.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Změna kolace systému SQL Server systému Microsoft Windows nebo SQL Server virtuálního počítače
Vzhledem k tomu, že nejsou bitové kopie systému SQL Server v Azure Marketplace nastavení pro používání kolace, který je požadován SAP NetWeaver aplikace, musí změnit okamžitě po nasazení. Pro SQL Server 2012 lze to provést pomocí následujících kroků při nasazený virtuální počítač a správci je moci přihlásit do nasazených virtuálních počítačů:

* Otevřete okno příkazového řádku systému Windows jako správce.
* Změňte adresář na C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Spusťte příkaz: Setup.exe/quiet nezobrazí /ACTION = REBUILDDATABASE InstanceName = MSSQLSERVER /SQLSYSADMINACCOUNTS =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> je účet, který byl definován jako účet správce při nasazování virtuálního počítače poprvé v galerii.

Proces by měla pouze trvat několik minut. Chcete-li Ujistěte se, zda v kroku skončila s správný výsledek, proveďte následující kroky:

* Otevřete SQL Server Management Studio.
* Otevřete okno dotazu.
* Spustíte příkaz sp_helpsort v hlavní databázi systému SQL Server.

Požadovaný výsledek by měl vypadat podobně jako:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Pokud není výsledek, ZASTAVTE nasazení SAP a zjistěte, proč možnosti instalačního příkazu nefunguje podle očekávání. Nasazení aplikací SAP NetWeaver do instance systému SQL Server s jinou kódové stránky systému SQL Server než ten, který uvedených výše je **není** podporována.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server vysoká dostupnost pro SAP v Azure
Jak je uvedeno výše v tomto dokumentu, není možné vytvořit sdílené úložiště, který je potřebný pro použití nejstarší funkci vysoké dostupnosti systému SQL Server. Tato funkce by nainstalujte dva nebo více instancí systému SQL Server v Windows Server Failover Cluster (WSFC) pomocí sdíleného disku pro uživatelské databáze (a nakonec databáze tempdb). Toto je metoda standardní vysoké dostupnosti dlouhou dobu, kterou také podporuje SAP. Protože Azure nepodporuje sdílené úložiště, nemůže být dosaženo konfigurace systému SQL Server s vysokou dostupností s konfigurací sdíleného disku clusteru. Ale jiné metody vysoké dostupnosti jsou stále možné a jsou popsané v následujících částech.

#### <a name="sql-server-log-shipping"></a>Přesouvání protokolu systému SQL Server
Jedna z metod vysokou dostupnost (HA) je přesouvání protokolu serveru SQL. Pokud virtuální počítače účastní HA konfigurace funguje překlad, žádný problém a instalační program v Azure se neliší od žádné nastavení, která se provádí na místě. Není doporučeno spoléhají na pouze pro překlad IP. S ohledem na nastavení přesouvání protokolu a zásady kolem přesouvání protokolu, zkontrolujte Tato dokumentace:

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

K dosažení vysoké dostupnosti proveďte, jeden potřebuje k nasazení virtuálních počítačů, které jsou v rámci těchto přesouvání protokolu konfiguraci, kterou chcete být v rámci stejné Azure skupina dostupnosti.

#### <a name="database-mirroring"></a>Zrcadlení databáze
Zrcadlení databáze, podporuje SAP (viz poznámka SAP [965908]) využívá k definování partnera převzetí služeb při selhání v připojovacím řetězci SAP. V případech mezi různými místy předpokládáme, že dva virtuální počítače jsou ve stejné doméně a že instance systému SQL Server kontextu dva uživatele jsou spuštěná s pověřeními uživatele domény, který je také mít dostatečná oprávnění v zahrnutých dvě instance systému SQL Server. Mezi typické místní instalace nebo konfigurace se proto neliší nastavení zrcadlení databáze v Azure.

Od verze čistě cloudové nasazení je nejsnazší má jiné nastavení domény v Azure a mají tyto virtuální počítače databázového systému (a ideálně vyhrazených virtuálních počítačích SAP) v rámci jedné domény.

Pokud domény není možné, jednu pro databázi zrcadlení koncových bodů podle postupu popsaného tady používat taky certifikáty: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Kurz k nastavení zrcadlení databáze v Azure naleznete zde: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>Always On systému SQL Server
Always On podporuje pro SAP místní (viz poznámka SAP [1772688]), podporuje se použije v kombinaci s SAP v Azure. Fakt, že není možné vytvořit sdílené disky v Azure neznamená, že jeden nejde vytvořit konfiguraci vždy na Windows Server Failover Cluster (WSFC) mezi různé virtuální počítače. Pouze znamená, že nemáte možnost použití sdíleného disku jako kvora v konfiguraci clusteru. Proto můžete sestavit konfigurace aplikace vždy na WSFC v Azure a není vyberte typ kvora, který využívá sdílený disk. Prostředí Azure těchto virtuálních počítačů nasazených v by měl vyřešit virtuální počítače podle názvu a virtuální počítače musí být ve stejné doméně. To platí pro pouze Azure a mezi různými místy nasazení. Existují některé důležité informace týkající se nasazení SQL serveru naslouchacího procesu skupiny dostupnosti (Nezaměňovat s Azure sady dostupnosti nesmí) vzhledem k tomu, že Azure v tuto chvíli není povolena pro vytvoření objektu AD a DNS, protože je možné na místě. Proto některé jiné instalace kroky jsou nezbytné k překonání konkrétní chování Azure.

Některé aspekty pomocí naslouchací proces skupiny dostupnosti jsou:

* Pomocí naslouchací proces skupiny dostupnosti je možné pouze v systému Windows Server 2012 nebo vyšší jako hostovaný operační systém virtuálního počítače. Pro Windows Server 2012, musíte zajistit, aby se použila tato oprava: <https://support.microsoft.com/kb/2854082> 
* Pro Windows Server 2008 R2, tato oprava neexistuje a Always On by bylo potřeba použít stejným způsobem jako zrcadlení databáze zadáním partnera převzetí služeb při selhání v řetězci připojení (provést prostřednictvím SAP default.pfl parametr databáze nebo mss nebo serveru – viz poznámka SAP [965908]).
* Při použití naslouchací proces skupiny dostupnosti, je třeba se připojit k vyhrazené pro vyrovnávání zatížení databáze virtuálních počítačů. Název řešení v čistě cloudové nasazení by buď vyžadují všech virtuálních počítačů systému SAP (aplikační servery, databázového systému server a server (A) SCS) jsou ve stejné virtuální síti nebo by vyžadovaly z aplikační vrstva SAP údržby souboru etc\host mohli virtuálních počítačů serveru SQL přeložit názvy virtuálních počítačů. Aby se zabránilo, že Azure je přiřazení nové IP adresy v případech, kdy oba virtuální počítače jsou náhodně vypnutí, jeden by měl přiřadit statické IP adresy pro rozhraní sítě těchto virtuálních počítačů v konfiguraci Always On (definování statickou IP adresu je popsaný v [to] [ virtual-networks-reserved-private-ip] článku)

[comment]: <> (Původní blogy)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Požadované při vytváření konfigurace clusteru služby WSFC, kde clusteru vyžaduje speciální IP adresu přiřazen, protože Azure s jeho aktuální funkce přiřazujete název clusteru stejnou IP adresu jako uzel clusteru je vytvořen na speciální kroky. To znamená, že provedení ručního kroku je nutné provést k přiřadit jinou IP adresu clusteru.
* Naslouchací proces skupiny dostupnosti přechází se vytvoří v Azure s koncových bodů protokolu TCP/IP, které jsou přiřazeny k virtuálním počítačům s primární a sekundární repliky skupiny dostupnosti.
* Může být potřeba zabezpečit tyto koncové body pomocí seznamů řízení přístupu.

[comment]: <> (Blog staré TODO)
[comment]: <> (Podrobné kroky a životní potřeby instalace konfigurace aplikace AlwaysOn na Azure se nejlépe došlo při s návodem k dispozici kurzu [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (Předkonfigurované nastavení AlwaysOn prostřednictvím Azure galerii <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (Vytváření naslouchací proces skupiny dostupnosti se nejlépe popisuje v kurzu [this][virtual-machines-windows-classic-ps-sql-int-listener])
[comment]: <> (Zabezpečení koncových bodů sítě s seznamy ACL vysvětlení najdete nejlépe tady:)
[comment]: <> (* <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

Je možné nasadit v různých oblastech Azure a SQL Server vždy na skupině dostupnosti. Tato funkce využívá připojení k Azure VNet-to-Vnet ([podrobnosti][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Blog staré TODO)
[comment]: <> (Instalační program skupin dostupnosti AlwaysOn serveru SQL v takové situaci je zde popsán: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Souhrn na vysoké dostupnosti SQL serveru v Azure
Vzhledem k tomu, že Azure Storage je ochrana obsahu, je jeden menší důvod se trvat na bitovou kopii hot standby. To znamená, že váš scénář vysoké dostupnosti je potřeba chránit pouze proti v následujících případech:

* Nedostupnosti virtuálního počítače jako celek z důvodu údržby v clusteru serveru v Azure nebo z jiných důvodů
* Problémů se softwarem v instanci systému SQL Server
* Ochrana proti ruční chyba, kde se odstranila data a je potřeba obnovení bodu v čase

Prohlížení odpovídající technologií, pomocí kterých jeden můžete uvádějí, že první dva případy se dá pokrýt komponentami zrcadlení databáze nebo Always On, zatímco třetí případ pouze se dá pokrýt komponentami přesouvání protokolu.

Musíte vyvážit složitější nastavení vždy v porovnání s zrcadlení databáze, s výhody Always On. Tyto výhody může být uvedený jako:

* Čitelných místních replikách.
* Zálohování z sekundární repliky.
* Lepší škálovatelnost.
* Více než jednu sekundární repliky.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Obecné SQL Server pro SAP v Azure souhrnu
Existuje mnoho doporučení v tomto průvodci a doporučujeme přečtěte si ho více než jednou před plánování vašeho nasazení Azure. Obecně platí ale nezapomeňte podle top deset obecné databázového systému Azure konkrétní body:

[comment]: <> (2.3 vyšší propustnost než co? Než jeden virtuální pevný disk?)
1. Použijte nejnovější verzi databázového systému, jako je SQL Server 2014, která má většina výhod v Azure. Pro systém SQL Server to je SQL Server 2012 SP1 CU4, který zahrnuje funkci zálohování zobrazení Azure Storage. Ve spojení s SAP doporučujeme však používat minimálně SQL Server 2014 SP1 CU1 nebo SQL Server 2012 SP2 a nejnovější Cu:.
2. Pečlivě naplánujte vašeho systému SAP na šířku v Azure vyvážit Azure omezení a rozložení souboru dat:
   * Nemusíte mít příliš mnoho disků, ale dostatek zajistit, že nedostanete vaší požadované IOPS.
   * Pokud nepoužijete spravované disky, mějte na paměti, že IOPS jsou také omezené na účet úložiště Azure a omezeny účty úložiště v rámci každé předplatné Azure ([podrobnosti][azure-subscription-service-limits]). 
   * Pouze stripe na discích, pokud je potřeba dosáhnout vyšší propustnost.
3. Software nemá nikdy instalovat nebo blokovat všechny soubory, které vyžadují trvalost na jednotku D:\, jako je jiný trvalé a nic na této jednotce dojde ke ztrátě při restartování systému Windows.
4. Nepoužívejte ukládání do mezipaměti na disku pro Azure Standard Storage.
5. Nepoužívejte Azure geograficky replikované úložiště účtů.  Místně redundantní použijte pro úlohy databázového systému.
6. Použijte řešení dodavatele databázového systému HA/DR k replikaci dat databáze.
7. Vždy můžete použít překlad, nespoléhejte na IP adresy.
8. Nejvyšší komprese databáze, která je možné používejte. Stránka komprese pro SQL Server, které je.
9. Dávejte pozor, pomocí bitové kopie systému SQL Server v Azure Marketplace. Pokud používáte Server SQL, jednu, musíte změnit kompletování instance před instalací jakékoli systému SAP NetWeaver na něm.
10. Instalace a konfigurace monitorování SAP hostitele pro Azure, jak je popsáno v [Průvodce nasazením][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>Podrobné App Service Environment SAP v systému Windows
Od verze Microsoft Azure, můžete snadno migrovat existující aplikace app Service Environment SAP do Azure Virtual Machines. SAP App Service Environment ve virtuálním počítači umožňuje snížit celkové náklady na vlastnictví nasazení, správu a údržbu enterprise spektra aplikací snadno migrací těchto aplikací do Microsoft Azure. S SAP App Service Environment ve virtuální počítač Azure správci a vývojáři můžete dál používat stejné vývoj a nástroje pro správu, které jsou k dispozici místně.

Je SLA pro Azure virtuální počítače, které naleznete zde: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Jsme si jisti, že dobře s jiné nabídky virtualizace veřejného cloudu, ale jednotlivé výsledky rozhodovacím hostované virtuální počítače Microsoft Azure se může lišit. Změna velikosti protokoly SAP množství různých SAP SAP certifikované SKU virtuálního počítače je k dispozici v samostatné Poznámka SAP [1928533].

Příkazy a doporučení ohledně využití z Azure Storage, nasazení SAP prostředků virtuálních počítačů nebo monitorování SAP platí pro nasazení SAP App Service Environment ve spojení s aplikací SAP, jak je uvedeno v rámci první čtyři kapitol tohoto dokumentu.

### <a name="sap-ase-version-support"></a>Podpora verzí App Service Environment SAP
SAP aktuálně podporuje SAP App Service Environment verze 16.0 pro použití s produkty SAP Business Suite. Všechny aktualizace pro server App Service Environment SAP nebo JDBC a ovladače ODBC pro použití s produkty SAP Business Suite je zajišťována výhradně prostřednictvím Marketplace služby SAP v: <https://support.sap.com/swdc>.

Jako u instalací na místních počítačích nestahovat aktualizací pro server App Service Environment SAP, nebo ovladače JDBC a rozhraní ODBC přímo z databáze Sybase webů. Podrobné informace o opravy, které jsou podporovány pro použití s SAP Business Suite produkty místně a v Azure Virtual Machines najdete v následujících poznámkách k SAP:

* [1590719]
* [1973241]

Obecné informace o spouštění SAP Business Suite v App Service Environment SAP najdete v [oznámení změny stavu](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Pokyny pro konfigurace SAP App Service Environment pro SAP související SAP instalace App Service Environment ve virtuálních počítačích Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura nasazení SAP App Service Environment
V souladu s obecný popis App Service Environment SAP spustitelné soubory umístěné nebo nainstalován do systémové jednotce disk operačního systému Virtuálního počítače (jednotka c:\). Většina databází systému a nástroje pro SAP App Service Environment nejsou obvykle se používá pevného SAP NetWeaver zatížením. Proto může na jednotce C:\ také zůstat systému a nástroje pro databáze (master, model, saptools, sybmgmtdb, sybsystemdb). 

Výjimka může být dočasná databáze obsahující všechny pracovní tabulky a vytvořit pomocným SAP, což může vyžadovat vyšší datový svazek nebo vstupně-výstupních operací operations svazek, který se nevejde do operačního systému původní virtuální počítač v případě některých ERP SAP a všechny úlohy BW dočasných tabulek disk (jednotka c:\).

V závislosti na verzi SAPInst nebo SWPM použitý k instalaci systému může obsahovat databáze:

* Jeden tempdb SAP App Service Environment, která je vytvořena při instalaci SAP App Service Environment
* App Service Environment SAP tempdb vytvořené instalace SAP App Service Environment a další saptempdb vytvořené rutiny instalace SAP
* App Service Environment SAP tempdb vytvořené instalace SAP App Service Environment a další databáze tempdb, který byl vytvořen ručně (například následující poznámka SAP [1752266]) splnění konkrétní databázi tempdb ERP/BW

V případě konkrétní ERP nebo všechny úlohy BW má smysl, s ohledem na výkon, aby databáze tempdb zařízení, která kromě vytvořené databáze tempdb (SWPM nebo ručně) na jiné jednotce než C:\. Pokud existuje žádné další databáze tempdb, doporučuje se vytvořit (Poznámka SAP [1752266]).

Pro tyto systémy by měla pro kromě vytvořené databáze tempdb provést následující kroky:

* Přesunout první databáze tempdb zařízení jako první zařízení databázi SAP
* Přidat zařízení databáze tempdb všechny virtuální pevné disky obsahující zařízení, které databázi SAP

Tato konfigurace umožňuje databáze tempdb buď využívat více místa, než je schopný poskytnout systémové jednotce. Jako referenci jeden můžete zkontrolovat zařízení velikosti databáze tempdb na stávajících systémů, které spustit místně. Nebo taková konfigurace by povolte IOPS čísla proti databázi tempdb nelze zadat s systémové jednotce. Znovu systémy, které jsou místní slouží k monitorování vstupně-výstupní úlohy proti databázi tempdb.

Veškerá zařízení, která App Service Environment SAP nikdy umístit na jednotku D:\ virtuálního počítače. To platí také pro databázi tempdb, i když jsou pouze dočasné objekty zachovány v databázi tempdb.

#### <a name="impact-of-database-compression"></a>Dopad komprese databáze
V konfiguracích, kde vstupně-výstupní šířky pásma může představovat problém každý míry, což snižuje IOPS vám může pomoci stretch úlohy, jež možné spouštět v případě pomocí IaaS, jako je například Azure. Proto se doporučuje zajistit, že se používá komprese App Service Environment SAP před nahráním existující databázi SAP do Azure.

Doporučení k provedení komprese před nahráním do Azure, pokud již není implementována je dán z několika důvodů:

* Je menší množství dat k odeslání do Azure
* Doba trvání spuštění komprese je kratší, za předpokladu, že jeden může používat silnější hardware s více procesorů nebo větší šířku pásma vstupně-výstupních operací nebo méně vstupně-výstupních operací latence na místě
* Menší velikosti databáze může vést k nižší náklady pro přidělení disku

Komprese dat a obchodní fungovat na virtuální počítač hostovaný v Azure Virtual Machines, stejně jako místní. Pro další podrobnosti o tom, jak zkontrolovat, zda komprese je již v používat existující databázi SAP App Service Environment, zkontrolujte Poznámka SAP [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Pomocí DBACockpit k monitorování instancí databáze
Pro systémy, SAP, které používají jako platformu databázi SAP App Service Environment, je dostupné jako windows embedded prohlížeče v transakci DBACockpit nebo Webdynpro DBACockpit. Všechny funkce pro monitorování a Správa databáze je ale implementace Webdynpro DBACockpit pouze k dispozici.

Jako s místním systémům několik kroků nutné povolit všechny funkce SAP NetWeaver používá Webdynpro provádění DBACockpit. Postupujte podle Poznámka SAP [1245200] povolit použití webdynpros a generovat požadované ty. Pokud postupovat podle pokynů ve výše uvedené poznámky, můžete také nakonfigurovat Správce internetové komunikace (icm) společně s porty, které chcete používat pro připojení http a https. Výchozí nastavení pro protokol http vypadá takto:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a odkazy vygenerované v transakci DBACockpit vypadá podobně jako tento:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V závislosti na zda a jak virtuální počítač Azure, který je hostitelem systému SAP je připojená přes site-to-site, více lokalit nebo ExpressRoute (mezi různými místy nasazení), musíte zajistit, že ICM používá plně kvalifikovaný název hostitele, který lze převést na počítači kde se pokoušíte otevřít DBACockpit z. Viz poznámka SAP [773830] pochopit, jak ICM Určuje plně kvalifikovaný název v závislosti na parametry profil a sadu parametr icm/host_name_full explicitně v případě potřeby.

Pokud nasadíte virtuální počítač v čistě cloudové scénář bez připojení mezi různými místy mezi místními a Azure, budete muset definovat veřejnou IP adresu a domainlabel. Formát veřejný název DNS virtuálního počítače vypadá takto:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Můžete najít další podrobnosti týkající se názvu DNS [sem][virtual-machines-azurerm-versus-azuresm].

Nastavení parametru profil SAP icm/host_name_full jako název DNS virtuálního počítače Azure odkaz může vypadat podobně jako:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V takovém případě musíte nezapomeňte:

* Přidat příchozí pravidlo na skupinu zabezpečení sítě na portálu Azure pro porty TCP/IP, používaný ke komunikaci s ICM
* Přidat příchozí pravidla konfigurace brány Windows Firewall pro porty TCP/IP používaný ke komunikaci s ICM

Pro automatickou naimportovány všechny oprav, které jsou k dispozici se doporučuje pravidelně použít kolekci oprava Poznámka SAP pro vaši verzi SAP:

* [1558958]
* [1619967]
* [1882376]

Další informace o DBA řídící panel pro App Service Environment SAP naleznete v následující poznámky k SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Důležité informace o zálohování nebo obnovení pro SAP App Service Environment
Při nasazování App Service Environment SAP do Azure, musí vaše zálohování metodika zkontrolovat. I když v systému není systémem produktivní, databázi SAP hostované SAP App Service Environment je nutné zálohovat pravidelně. Vzhledem k tomu, že Azure úložiště udržuje tři bitové kopie, je nyní méně důležité v ohledem na kompenzace havárie úložiště zálohy. Hlavním důvodem pro údržbu správné plán zálohování a obnovení je větší, který můžete kompenzovat chyby logické nebo ruční tím, že poskytuje bod v možnosti v době obnovení. Cílem je proto záloh buď použijte k obnovení databázi zpět do určité míry v čase nebo k použití zálohování v Azure počáteční hodnoty jiného systému zkopírováním existující databáze. Například je může přenáší z konfigurace SAP vrstvě 2 na instalaci systému 3vrstvé stejného systému obnovení ze zálohy.

Zálohování a obnovení databáze v Azure funguje stejným způsobem jako místní. Naleznete v poznámkách k SAP:

* [1588316]
* [1585981]

Podrobné informace o vytváření výpis konfigurace a plánování zálohování. V závislosti na vaše požadavky, které můžete konfigurovat a strategie databáze a protokolu výpisy paměti na disk na jednom z existujících disků, nebo přidejte další disk pro zálohování. Aby se snížilo nebezpečí dojít ke ztrátě dat v případě chyby, se doporučuje použití disku, kde se nachází žádné databáze zařízení.

Kromě dat a obchodní komprese App Service Environment SAP také nabízí kompresi zálohy. Aby zabírala méně místa s výpisy databáze a protokolu se doporučuje použít kompresi zálohy. Další informace viz poznámka SAP [1588316]. Komprese zálohy je také nezbytné k snížit množství dat, které se mají přenést, pokud plánujete stáhnout zálohy nebo virtuální pevné disky obsahující zálohování výpisy z virtuálního počítače Azure k místnímu.

Nepoužívejte jednotce D:\ jako cíl výpisu databázi nebo protokolu.

#### <a name="performance-considerations-for-backupsrestores"></a>Důležité informace o výkonu pro zálohování a obnovování
Stejně jako u nasazení úplné obnovení je závislá na tom, kolik svazky lze číst souběžně a propustnost tyto svazky, které je možné výkonu zálohování a obnovení. Kromě toho může spotřeby procesoru, který je používán kompresi zálohy přehrát významnou roli na virtuálních počítačích s maximálně 8 vláken procesoru. Proto můžete předpokládat jeden:

* Méně počet disků používá k ukládání databáze zařízení, menší celkovou propustnost čtení
* Menší že počet procesoru vláken ve virtuálním počítači, závažnější dopad kompresi zálohy
* Méně cílů (Stripe adresáře, disků) k zápisu zálohování, nižší úrovně propustnosti

Pokud chcete zvýšit počet cílů k zápisu do, že existují dvě možnosti, které lze použít nebo kombinaci podle potřeby:

* Prokládání cílový svazek přes více připojené disky za účelem zlepšení propustnosti IOPS na tomto svazku prokládané
* Vytvoření výpisu konfigurace na úrovni SAP App Service Environment, který používá více než jeden cílový adresář k výpisu k zápisu

Prokládání svazek přes více připojené disky má popsané výše v této příručce. Další informace o používání více adresářů v konfiguraci výpisu SAP App Service Environment, naleznete v dokumentaci na sp_config_dump uloženou proceduru, který se používá k vytvoření výpisu konfigurace na [informační Sybase středisko](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Zotavení po havárii s virtuálními počítači Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikace dat s SAP Sybase replikace Server
S SAP SAP Sybase replikace serveru (SRS), App Service Environment poskytuje záložním pohotovostním řešení asynchronně přenášet databázové transakce na vzdálené umístění. 

Instalace a používání služby SRS funguje i funkčně ve virtuálním počítači, který je hostitelem služby virtuálního počítače Azure stejně jako místní.

SAP App Service Environment HADR nevyžaduje Azure interní pro vyrovnávání zatížení a nemá závislosti na úrovni clustering operačního systému a funguje na Azure Windows a virtuální počítače s Linuxem. Pro přečíst podrobnosti o SAP App Service Environment HADR [HADR App Service Environment SAP uživatelé průvodce](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Podrobné App Service Environment SAP v systému Linux
Od verze Microsoft Azure, můžete snadno migrovat existující aplikace app Service Environment SAP do Azure Virtual Machines. SAP App Service Environment ve virtuálním počítači umožňuje snížit celkové náklady na vlastnictví nasazení, správu a údržbu enterprise spektra aplikací snadno migrací těchto aplikací do Microsoft Azure. S SAP App Service Environment ve virtuální počítač Azure správci a vývojáři můžete dál používat stejné vývoj a nástroje pro správu, které jsou k dispozici místně.

Pro nasazení virtuálních počítačů Azure je důležité vědět oficiální SLA, které naleznete zde: <https://azure.microsoft.com/support/legal/sla>

Informace o nastavení velikosti SAP a seznam SAP certifikované SKU virtuální počítač je součástí Poznámka SAP [1928533]. Změna velikosti dokumentů pro virtuálními počítači Azure je zde uveden další SAP <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> a zde <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Příkazy a doporučení ohledně využití z Azure Storage, nasazení SAP prostředků virtuálních počítačů nebo monitorování SAP platí pro nasazení SAP App Service Environment ve spojení s aplikací SAP, jak je uvedeno v rámci první čtyři kapitol tohoto dokumentu.

Následující poznámky k dvě SAP patří obecné informace o App Service Environment na Linuxu a App Service Environment v cloudu:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Podpora verzí App Service Environment SAP
SAP aktuálně podporuje SAP App Service Environment verze 16.0 pro použití s produkty SAP Business Suite. Všechny aktualizace pro server App Service Environment SAP nebo JDBC a ovladače ODBC pro použití s produkty SAP Business Suite je zajišťována výhradně prostřednictvím Marketplace služby SAP v: <https://support.sap.com/swdc>.

Jako u instalací na místních počítačích nestahovat aktualizací pro server App Service Environment SAP, nebo ovladače JDBC a rozhraní ODBC přímo z databáze Sybase webů. Podrobné informace o opravy, které jsou podporovány pro použití s SAP Business Suite produkty místně a v Azure Virtual Machines najdete v následujících poznámkách k SAP:

* [1590719]
* [1973241]

Obecné informace o spouštění SAP Business Suite v App Service Environment SAP najdete v [oznámení změny stavu](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Pokyny pro konfigurace SAP App Service Environment pro SAP související SAP instalace App Service Environment ve virtuálních počítačích Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura nasazení SAP App Service Environment
V souladu s obecný popis by měla být App Service Environment SAP spustitelné soubory umístěné nebo nainstalován do systému souborů kořenové virtuálního počítače (/sybase). Většina databází systému a nástroje pro SAP App Service Environment nejsou obvykle využít pevného SAP NetWeaver zatížením. Proto systém a nástroje pro databáze (master, model, saptools, sybmgmtdb, sybsystemdb) může zůstat v systému souborů kořenové také. 

Výjimka může být dočasná databáze obsahující všechny pracovní tabulky a dočasných tabulek vytvořili pomocným SAP, v případě některých ERP SAP a všechny úlohy BW může to vyžadovat vyšší datový svazek nebo vstupně-výstupních operací svazek, který se nevejde do operačního systému původní virtuální počítač disk.

V závislosti na verzi SAPInst nebo SWPM použitý k instalaci systému může obsahovat databáze:

* Jeden tempdb SAP App Service Environment, která je vytvořena při instalaci SAP App Service Environment
* App Service Environment SAP tempdb vytvořené instalace SAP App Service Environment a další saptempdb vytvořené rutiny instalace SAP
* App Service Environment SAP tempdb vytvořené instalace SAP App Service Environment a další databáze tempdb, který byl vytvořen ručně (například následující poznámka SAP [1752266]) splnění konkrétní databázi tempdb ERP/BW

V případě konkrétní ERP nebo všechny úlohy BW má smysl, s ohledem na výkon, zachovat databázi tempdb zařízení, která kromě vytvořené databáze tempdb (SWPM nebo ručně) na systém samostatný soubor, který může být reprezentovaný jednoho Azure datový disk nebo Linux diskového pole RAID práci s více disky dat Azure. Pokud existuje žádné další databáze tempdb, doporučuje se vytvořit (Poznámka SAP [1752266]).

Pro tyto systémy by měla pro kromě vytvořené databáze tempdb provést následující kroky:

* Přesunout první adresář databáze tempdb na první systém souborů databáze SAP
* Databáze tempdb adresáře přidat všechny disky obsahující systému souborů databáze SAP

Tato konfigurace umožňuje databáze tempdb buď využívat více místa, než je schopný poskytnout systémové jednotce. Jako referenci jeden můžete zkontrolovat velikosti adresář databáze tempdb na stávajících systémů, které spustit místně. Nebo taková konfigurace by povolte IOPS čísla proti databázi tempdb nelze zadat s systémové jednotce. Znovu systémy, které jsou místní slouží k monitorování vstupně-výstupní úlohy proti databázi tempdb.

Nikdy uveďte všechny adresáře App Service Environment SAP do /mnt nebo /mnt/resource virtuálního počítače. To platí také pro databázi tempdb, i v případě, že jsou tyto objekty zachovány v databázi tempdb pouze dočasné protože /mnt nebo /mnt/resource je k výchozí virtuální počítač Azure dočasného prostoru, což není trvalý. Další podrobnosti o dočasnou místo na virtuálním počítači Azure najdete v [v tomto článku][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Dopad komprese databáze
V konfiguracích, kde vstupně-výstupní šířky pásma může představovat problém každý míry, což snižuje IOPS vám může pomoci stretch úlohy, jež možné spouštět v případě pomocí IaaS, jako je například Azure. Proto se doporučuje zajistit, že se používá komprese App Service Environment SAP před nahráním existující databázi SAP do Azure.

Doporučení k provedení komprese před nahráním do Azure, pokud již není implementována je dán z několika důvodů:

* Je menší množství dat k odeslání do Azure
* Doba trvání spuštění komprese je kratší, za předpokladu, že jeden může používat silnější hardware s více procesorů nebo větší šířku pásma vstupně-výstupních operací nebo méně vstupně-výstupních operací latence na místě
* Menší velikosti databáze může vést k nižší náklady pro přidělení disku

Komprese dat a obchodní fungovat na virtuální počítač hostovaný v Azure Virtual Machines, stejně jako místní. Pro další podrobnosti o tom, jak zkontrolovat, zda komprese je již v používat existující databázi SAP App Service Environment, zkontrolujte Poznámka SAP [1750510]. Další informace týkající se databáze komprese, viz poznámka SAP [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Pomocí DBACockpit k monitorování instancí databáze
Pro systémy, SAP, které používají jako platformu databázi SAP App Service Environment, je dostupné jako windows embedded prohlížeče v transakci DBACockpit nebo Webdynpro DBACockpit. Všechny funkce pro monitorování a Správa databáze je ale implementace Webdynpro DBACockpit pouze k dispozici.

Jako s místním systémům několik kroků nutné povolit všechny funkce SAP NetWeaver používá Webdynpro provádění DBACockpit. Postupujte podle Poznámka SAP [1245200] povolit použití webdynpros a generovat požadované ty. Pokud postupovat podle pokynů ve výše uvedené poznámky, můžete také nakonfigurovat Správce internetové komunikace (icm) společně s porty, které chcete používat pro připojení http a https. Výchozí nastavení pro protokol http vypadá takto:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a odkazy vygenerované v transakci DBACockpit bude vypadat podobně jako tento:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V závislosti na zda a jak virtuální počítač Azure, který je hostitelem systému SAP je připojená přes site-to-site, více lokalit nebo ExpressRoute (mezi různými místy nasazení), musíte zajistit, že ICM používá plně kvalifikovaný název hostitele, který lze převést na počítači kde se pokoušíte otevřít DBACockpit z. Viz poznámka SAP [773830] pochopit, jak ICM Určuje plně kvalifikovaný název v závislosti na parametry profil a sadu parametr icm/host_name_full explicitně v případě potřeby.

Pokud nasadíte virtuální počítač v čistě cloudové scénář bez připojení mezi různými místy mezi místními a Azure, budete muset definovat veřejnou IP adresu a domainlabel. Formát veřejný název DNS virtuálního počítače vypadá takto:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Můžete najít další podrobnosti týkající se názvu DNS [sem][virtual-machines-azurerm-versus-azuresm].

Nastavení parametru profil SAP icm/host_name_full jako název DNS virtuálního počítače Azure odkaz může vypadat podobně jako:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V takovém případě musíte nezapomeňte:

* Přidat příchozí pravidlo na skupinu zabezpečení sítě na portálu Azure pro porty TCP/IP, používaný ke komunikaci s ICM
* Přidat příchozí pravidla konfigurace brány Windows Firewall pro porty TCP/IP používaný ke komunikaci s ICM

Pro automatickou naimportovány všechny oprav, které jsou k dispozici se doporučuje pravidelně použít kolekci oprava Poznámka SAP pro vaši verzi SAP:

* [1558958]
* [1619967]
* [1882376]

Další informace o DBA řídící panel pro App Service Environment SAP naleznete v následující poznámky k SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Důležité informace o zálohování nebo obnovení pro SAP App Service Environment
Při nasazování App Service Environment SAP do Azure musí být zkontrolovány vaše zálohování metodika. I když v systému není systémem produktivní, databázi SAP hostované SAP App Service Environment je nutné zálohovat pravidelně. Vzhledem k tomu, že Azure úložiště udržuje tři bitové kopie, je nyní méně důležité v ohledem na kompenzace havárie úložiště zálohy. Hlavním důvodem pro údržbu správné plán zálohování a obnovení je větší, který můžete kompenzovat chyby logické nebo ruční tím, že poskytuje bod v možnosti v době obnovení. Cílem je proto záloh buď použijte k obnovení databázi zpět do určité míry v čase nebo k použití zálohování v Azure počáteční hodnoty jiného systému zkopírováním existující databáze. Například je může přenáší z konfigurace SAP vrstvě 2 na instalaci systému 3vrstvé stejného systému obnovení ze zálohy.

Zálohování a obnovení databáze v Azure funguje stejným způsobem jako místní. Naleznete v poznámkách k SAP:

* [1588316]
* [1585981]

Podrobné informace o vytváření výpis konfigurace a plánování zálohování. V závislosti na vaše požadavky, které můžete konfigurovat a strategie databáze a protokolu výpisy paměti na disk na jednom z existujících disků, nebo přidejte další disk pro zálohování. Pokud chcete zkrátit nebezpečí dojít ke ztrátě dat v případě chyby, se doporučuje použití disku, kde je umístěn žádný adresář nebo soubor databáze.

Kromě dat a obchodní komprese App Service Environment SAP také nabízí kompresi zálohy. Aby zabírala méně místa s výpisy databáze a protokolu se doporučuje použít kompresi zálohy. Další informace viz poznámka SAP [1588316]. Komprese zálohy je také nezbytné k snížit množství dat, které se mají přenést, pokud plánujete stáhnout zálohy nebo virtuální pevné disky obsahující zálohování výpisy z virtuálního počítače Azure k místnímu.

Virtuální počítač Azure dočasného prostoru /mnt nebo /mnt/resource nepoužívejte jako cíl výpisu databázi nebo protokolu.

#### <a name="performance-considerations-for-backupsrestores"></a>Důležité informace o výkonu pro zálohování a obnovování
Stejně jako u nasazení úplné obnovení je závislá na tom, kolik svazky lze číst souběžně a propustnost tyto svazky, které je možné výkonu zálohování a obnovení. Kromě toho může spotřeby procesoru, který je používán kompresi zálohy přehrát významnou roli na virtuálních počítačích s maximálně 8 vláken procesoru. Proto můžete předpokládat jeden:

* Méně počet disků používá k ukládání databáze zařízení, menší celkovou propustnost čtení
* Menší že počet procesoru vláken ve virtuálním počítači, závažnější dopad kompresi zálohy
* Méně cílů (Linux softwaru diskového pole RAID, disků) k zápisu zálohování, nižší úrovně propustnosti

Pokud chcete zvýšit počet cílů k zápisu do, že existují dvě možnosti, které lze použít nebo kombinaci podle potřeby:

* Prokládání cílový svazek přes více připojené disky za účelem zlepšení propustnosti IOPS na tomto svazku prokládané
* Vytvoření výpisu konfigurace na úrovni SAP App Service Environment, který používá více než jeden cílový adresář k výpisu k zápisu

Prokládání svazek přes více připojené disky má popsané výše v této příručce. Další informace o používání více adresářů v konfiguraci výpisu SAP App Service Environment, naleznete v dokumentaci na sp_config_dump uloženou proceduru, který se používá k vytvoření výpisu konfigurace na [informační Sybase středisko](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Zotavení po havárii s virtuálními počítači Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikace dat s SAP Sybase replikace Server
S SAP SAP Sybase replikace serveru (SRS), App Service Environment poskytuje záložním pohotovostním řešení asynchronně přenášet databázové transakce na vzdálené umístění. 

Instalace a používání služby SRS funguje i funkčně ve virtuálním počítači, který je hostitelem služby virtuálního počítače Azure stejně jako místní.

App Service Environment HADR prostřednictvím serveru SAP replikace není podporována v daném okamžiku. Může být testovány s a v budoucnu vydání pro platformy Microsoft Azure.

## <a name="specifics-to-oracle-database-on-windows"></a>Podrobnosti do databáze Oracle v systému Windows
Oracle softwaru podporuje Oracle pro spouštění v Microsoft Windows Hyper-V a Azure. Podrobnosti o obecné podpoře Windows Hyper-V a Azure zkontrolujte: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Následující obecné podporu je také podporována konkrétní scénář aplikací SAP, Oracle – databáze využití. Podrobnosti jsou pojmenované v této části dokumentu.

### <a name="oracle-version-support"></a>Podpora verzí Oracle
Verze Oracle a odpovídající verze operačního systému, které jsou podporovány pro SAP systémem Oracle na virtuálních počítačích Azure lze nalézt v Poznámka SAP [2039619].

Obecné informace o spuštění SAP Business Suite na Oracle naleznete v 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny pro konfigurace Oracle pro SAP instalace ve virtuálních počítačích Azure
#### <a name="storage-configuration"></a>Konfigurace úložiště
Pouze jednu instanci Oracle pomocí NTFS naformátovaný disků je podporována. Všechny soubory databáze musí být uložen v systému souborů NTFS na základě virtuálních pevných disků nebo disků spravované. Tyto disky jsou připojené k virtuálnímu počítači Azure a jsou založené na Azure úložiště objektů BLOB stránky (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) nebo spravované disky (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Jakýkoli druh síťové jednotky nebo vzdálených sdílených složkách, jako je Azure souborových služeb:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

jsou **není** podporované pro soubory databáze Oracle!

Pomocí disků na základě úložiště objektů BLOB stránky Azure nebo spravovat disky příkazy provedené v tomto dokumentu v kapitole [ukládání do mezipaměti pro virtuální počítače a datové disky] [ dbms-guide-2.1] a [Microsoft Azure Storage] [ dbms-guide-2.3] týkají nasazení s databázi Oracle.

Jak je popsáno výše v části Obecné v dokumentu, existují kvóty na propustnost IOPS pro disky systému Azure. Přesný kvóty jsou v závislosti na typu virtuálního počítače použít. Seznam typů virtuálních počítačů s jejich kvóty najdete [zde (Linux)] [ virtual-machines-sizes-linux] a [zde (Windows)][virtual-machines-sizes-windows].

Identifikovat podporované typy virtuálního počítače Azure, najdete v tématu Poznámka SAP [1928533].

Tak dlouho, dokud se aktuální kvóty IOPS na disk splňuje požadavky, je možné uložit všechny soubory databáze na jednom disku jedné připojené. 

Pokud jsou vyžadovány další IOPS, se doporučuje použít fondy úložiště okna (jenom k dispozici v systému Windows Server 2012 a vyšší) nebo Windows prokládání pro systém Windows 2008 R2 prostřednictvím více připojené disky vytvořit jedno velké logické zařízení (viz také kapitoly [ Software RAID] [ dbms-guide-2.2] tohoto dokumentu). Tento přístup zjednodušuje nároky na správu spravovat místo na disku a zabraňuje úsilí nezbytné k ručně distribuovat soubory do více připojené disky.

#### <a name="backup--restore"></a>Backup / obnovení
Pro zálohování a obnově funkce, Brazílie SAP * nástroje pro Oracle jsou podporovány stejným způsobem jako na standardní operační systémy Windows Server a Hyper-V. Správce obnovení Oracle (RMAN) je také podporována pro zálohování na disk a obnovení z disku.

#### <a name="high-availability"></a>Vysoká dostupnost
Oracle Data Guard je podporována pro vysokou dostupnost a zotavení po havárii pro účely. Podrobnosti najdete v [to] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentaci.

#### <a name="other"></a>Ostatní
Všechny ostatní obecné oblasti jako skupiny dostupnosti Azure nebo SAP monitorování platí jak je popsáno v první tři kapitol tohoto dokumentu pro nasazení virtuálních počítačů s databázi Oracle.

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Podrobnosti do databáze Oracle na Oracle Linux
Oracle softwaru podporuje Oracle pro spouštění v Microsoft Windows Hyper-V a Azure. Podrobnosti o obecné podpoře Windows Hyper-V a Azure zkontrolujte: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Následující obecné podporu je také podporována konkrétní scénář aplikací SAP, Oracle – databáze využití. Podrobnosti jsou pojmenované v této části dokumentu.

### <a name="oracle-version-support"></a>Podpora verzí Oracle
Verze Oracle a odpovídající verze operačního systému, které jsou podporovány pro SAP systémem Oracle na virtuálních počítačích Azure lze nalézt v Poznámka SAP [2039619].

Obecné informace o spuštění SAP Business Suite na Oracle naleznete v 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny pro konfigurace Oracle pro SAP instalace ve virtuálních počítačích Azure
#### <a name="storage-configuration"></a>Konfigurace úložiště
Je podporován pouze jednu instanci Oracle pomocí ext3, ext4 a xfs formátovány disky. Všechny soubory databáze musí být uložen v těchto systémech souborů na základě virtuálních pevných disků nebo disků spravované. Tyto disky jsou připojené k virtuálnímu počítači Azure a jsou založené na Azure úložiště objektů BLOB stránky (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) nebo spravované disky (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Jakýkoli druh síťové jednotky nebo vzdálených sdílených složkách, jako je Azure souborových služeb:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

jsou **není** podporované pro soubory databáze Oracle!

Pomocí disků na základě úložiště objektů BLOB stránky Azure nebo spravovat disky příkazy provedené v tomto dokumentu v kapitole [ukládání do mezipaměti pro virtuální počítače a datové disky] [ dbms-guide-2.1] a [Microsoft Azure Storage] [ dbms-guide-2.3] týkají nasazení s databázi Oracle.

Jak je popsáno výše v části Obecné v dokumentu, existují kvóty na propustnost IOPS pro disky systému Azure. Přesný kvóty jsou v závislosti na typu virtuálního počítače použít. Seznam typů virtuálních počítačů s jejich kvóty najdete [zde (Linux)] [ virtual-machines-sizes-linux] a [zde (Windows)][virtual-machines-sizes-windows].

Identifikovat podporované typy virtuálního počítače Azure, najdete v tématu Poznámka SAP [1928533]

Tak dlouho, dokud se aktuální kvóty IOPS na disk splňuje požadavky, je možné uložit všechny soubory databáze na jednom disku jedné připojené. 

Pokud jsou vyžadovány další IOPS, se doporučuje použít LVM (Správce logických svazku) nebo MDADM k vytvoření jednoho velkého objemu logické přes více připojené disky. Viz také kapitoly [softwaru diskového pole RAID] [ dbms-guide-2.2] tohoto dokumentu. Tento přístup zjednodušuje nároky na správu spravovat místo na disku a zabraňuje úsilí nezbytné k ručně distribuovat soubory do více připojené disky.

#### <a name="backup--restore"></a>Backup / obnovení
Pro zálohování a obnově funkce, Brazílie SAP * nástroje pro Oracle jsou podporovány stejným způsobem jako na holý počítač a Hyper-V. Správce obnovení Oracle (RMAN) je také podporována pro zálohování na disk a obnovení z disku.

#### <a name="high-availability"></a>Vysoká dostupnost
Oracle Data Guard je podporována pro vysokou dostupnost a zotavení po havárii pro účely. Podrobnosti najdete v [to] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentaci.

#### <a name="other"></a>Ostatní
Všechny ostatní obecné oblasti jako skupiny dostupnosti Azure nebo SAP monitorování platí jak je popsáno v první tři kapitol tohoto dokumentu pro nasazení virtuálních počítačů s databázi Oracle.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Specifika pro databázi SAP MaxDB v systému Windows
### <a name="sap-maxdb-version-support"></a>Podpora verzí MaxDB SAP
SAP aktuálně podporuje SAP MaxDB verze 7.9 pro použití s produkty na základě SAP NetWeaver v Azure. Jsou k dispozici všechny aktualizace pro SAP MaxDB server nebo JDBC a ovladače ODBC pro použití s produkty na základě SAP NetWeaver výhradně prostřednictvím Marketplace služby SAP v <https://support.sap.com/swdc>.
Obecné informace o spouštění SAP NetWeaver na SAP MaxDB lze najít na <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Podporované typy verze Microsoft Windows a virtuálních počítačů Azure pro SAP MaxDB databázového systému
Podporované verze systému Windows pro SAP MaxDB databázového systému v Azure, najdete v tématu:

* [SAP produktu dostupnosti matice (PAM)][sap-pam]
* Poznámka SAP [1928533]

Důrazně doporučujeme používat nejnovější verzi operačního systému Microsoft Windows, což je Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>K dispozici SAP MaxDB dokumentace
Aktualizovaný seznam SAP MaxDB dokumentaci můžete najít v následujících Poznámka SAP [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny pro konfigurace MaxDB SAP pro SAP instalace ve virtuálních počítačích Azure
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Konfigurace úložiště
Úložiště Azure osvědčené postupy pro SAP MaxDB podle obecná doporučení uvedená v kapitole [struktura nasazení RDBMS][dbms-guide-2].

> [!IMPORTANT]
> Stejně jako jiné databáze SAP MaxDB má také dat a souborů protokolu. V terminologii SAP MaxDB je správným termínem "svazek" (ne "soubor"). Například existují SAP MaxDB datové svazky a svazky protokolu. Nezaměňujte tato nastavení u diskové svazky operačního systému. 
> 
> 

Stručně řečeno budete muset:

* Pokud používáte účty Azure Storage, nastavit účet úložiště Azure, který obsahuje SAP MaxDB protokolu a data svazky (tj. soubory) **místní redundantní úložiště (LRS)** uvedené v kapitole [Microsoft Azure Storage][dbms-guide-2.3].
* Vstupně-výstupní cestu pro SAP MaxDB datové svazky (tj. soubory) nezávislá na vstupně-výstupní cestu pro svazky protokolu (tj. soubory). To znamená, že SAP MaxDB datové svazky (tj. soubory) musí být instalovány na jedné logické jednotce a svazky protokolu SAP MaxDB (tj. soubory) musí být instalovány na jiné logické jednotce.
* Nastavte správný typ ukládání do mezipaměti pro každý disk, v závislosti na tom, jestli ho použít pro SAP MaxDB dat či protokolu svazky (tj. soubory) a jestli použít standardní Azure nebo Azure Premium Storage, jak je popsáno v kapitole [ukládání do mezipaměti pro virtuální počítače a datové disky][dbms-guide-2.1].
* Tak dlouho, dokud se aktuální kvóty IOPS na disk splňuje požadavky, je možné uložit všechny datové svazky na jednom disku připojené a také uložení všechny svazky protokolu databáze na jiný disk jedné připojené.
* Pokud jsou vyžadovány další IOPS nebo místa, se doporučuje použít k vytvoření jedno velké logické zařízení přes více fondů úložiště okno Microsoft (pouze k dispozici v systému Microsoft Windows Server 2012 a vyšší) nebo Microsoft Windows prokládání pro Microsoft Windows 2008 R2 připojené disky. Viz také kapitoly [softwaru diskového pole RAID] [ dbms-guide-2.2] tohoto dokumentu. Tento přístup zjednodušuje nároky na správu spravovat místo na disku a zabraňuje úsilí ručně distribuci souborů mezi více připojené disky.
* Pro požadavky na nejvyšší IOPS můžete použít Azure Premium Storage, který je k dispozici na DS-series a GS-series virtuálních počítačů.

![Konfigurace referenčního virtuálního počítače Azure IaaS pro SAP MaxDB databázového systému][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Zálohování a obnovení
Při nasazování SAP MaxDB do Azure, je nutné si vaše zálohování metody. I když v systému není systémem produktivní, databázi SAP hostované SAP MaxDB je nutné zálohovat pravidelně. Vzhledem k tomu, že Azure úložiště udržuje tři bitové kopie, je nyní zálohu méně důležité z hlediska ochrany proti selhání úložiště a důležitější selhání provozní nebo správce systému. Hlavním důvodem pro údržbu správné zálohování a obnovení plán je tak, aby můžete kompenzovat chyby logické nebo ruční tím, že poskytuje možnosti obnovení bodu v čase. Cílem je proto buď pomocí zálohy obnovit databázi do určité míry v čase nebo použití zálohování v Azure počáteční hodnoty jiného systému zkopírováním existující databáze. Například je může přenáší z konfigurace SAP vrstvě 2 na instalaci systému 3vrstvé stejného systému obnovení ze zálohy.

Zálohování a obnovení databáze v Azure funguje stejným způsobem jako v případě místních systémů, abyste mohli používat standardní SAP MaxDB zálohování a obnovení nástroje, které jsou popsány v jednom z dokumentace dokumenty SAP MaxDB uvedené v Poznámka SAP [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Důležité informace o výkonu pro zálohování a obnovení
Stejně jako u úplné nasazení je závislá na tom, kolik svazky lze číst v paralelní a propustnost tyto svazky výkonu zálohování a obnovení. Kromě toho můžete spotřeby procesoru, který je používán kompresi zálohy přehrát významnou roli na virtuálních počítačích s maximálně 8 vláken procesoru. Proto můžete předpokládat jeden:

* Méně počet disků používá k ukládání zařízení databáze, čím nižší celkovou propustnost čtení
* Menší že počet procesoru vláken ve virtuálním počítači, závažnější dopad kompresi zálohy
* Méně cílů (Stripe adresáře, disků) k zápisu zálohování do dolní propustnost

Pokud chcete zvýšit počet cílů k zápisu, existují dvě možnosti, které můžete použít, pravděpodobně v kombinaci, v závislosti na vašich potřeb:

* Vyhradit samostatných svazcích pro zálohování
* Prokládání cílový svazek přes více připojené disky za účelem zlepšení propustnosti IOPS na tomto svazku prokládané disku
* S zařízení, samostatné vyhrazené logického disku:
  * SAP MaxDB záložní svazky (tj. soubory)
  * SAP MaxDB datové svazky (tj. soubory)
  * SAP MaxDB protokolu svazky (tj. soubory)

Prokládání svazek přes více připojené disky popsané dříve v kapitole [softwaru diskového pole RAID] [ dbms-guide-2.2] tohoto dokumentu. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Další
Všechny ostatní obecné oblasti jako jsou skupiny dostupnosti Azure nebo SAP monitorování platí také, jak je popsáno v první tři kapitol tohoto dokumentu pro nasazení virtuálních počítačů s databázi SAP MaxDB.
Další nastavení specifické pro SAP MaxDB jsou transparentní pro virtuální počítače Azure a jsou popsané v různé dokumenty, které jsou uvedené v Poznámka SAP [767598] a v těchto poznámkách k SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Specifika pro SAP liveCache v systému Windows
### <a name="sap-livecache-version-support"></a>SAP liveCache podpora verzí
Minimální verze SAP liveCache podporované v Azure Virtual Machines je **SAP LC/LCAPPS 10.0 SP 25** včetně **liveCache 7.9.08.31** a **LCA sestavení 25**, vydaná pro **EhP 2 pro SAP SCM 7.0** a vyšší.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Podporované typy verze Microsoft Windows a virtuálních počítačů Azure pro SAP liveCache databázového systému
Podporované verze systému Windows pro SAP liveCache v Azure, najdete v tématu:

* [SAP produktu dostupnosti matice (PAM)][sap-pam]
* Poznámka SAP [1928533]

Důrazně doporučujeme používat nejnovější verzi operačního systému Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache pokyny pro konfigurace pro SAP instalace ve virtuálních počítačích Azure
#### <a name="recommended-azure-vm-types"></a>Doporučená typy virtuálních počítačů Azure
SAP liveCache je aplikace, která provede výpočty velký, velikost a rychlost paměti RAM a procesoru má hlavní vliv na výkon liveCache SAP. 

Pro virtuální počítač Azure typy podporované systémem SAP (Poznámka SAP [1928533]), všechny virtuální prostředky procesoru přidělené k virtuálnímu počítači jsou zajišťované vyhrazené fyzické prostředky procesoru hypervisoru. Žádné předimenzování (a proto žádné soutěž o prostředky procesoru) probíhá.

Podobně pro všechny virtuální počítač Azure instance typy podporované systémem SAP, paměť virtuálního počítače je 100 %, které jsou namapované na fyzické paměti – například předimenzování (většího celkového), se nepoužije.

Z tohoto hlediska důrazně doporučujeme používat nový typ virtuálního počítače Azure DS-series (v kombinaci s Azure Premium Storage) nebo D-series, jako mají 60 % rychlejší procesory než A-series. U nejvyšší zatížení paměti RAM a procesoru můžete použít G-series a GS-series (v kombinaci s Azure Premium Storage) virtuálních počítačů s nejnovější Intel?? Xeon?? procesor E5 v3 rodiny, které mají dvakrát paměti a čtyři případech úložiště jednotky SSD (Solid-State Drive) D/DS-series.

#### <a name="storage-configuration"></a>Konfigurace úložiště
Jako SAP liveCache je založena na technologii SAP MaxDB, službu Azure storage osvědčených postupů doporučení uvedená v kapitole pro SAP MaxDB [konfigurace úložiště] [ dbms-guide-8.4.1] platí také pro SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Pro liveCache vyhrazený virtuální počítač Azure
Jako SAP liveCache intenzivně využívá výpočetní výkon, produktivitu využití důrazně doporučujeme k nasazení na vyhrazený virtuální počítač Azure. 

![Vyhrazený virtuální počítač Azure pro liveCache pro případ použití produktivitu][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Zálohování a obnovení
zálohování a obnovení, včetně faktory ovlivňující výkon, jsou již popsané v příslušné kapitolám SAP MaxDB [zálohování a obnovení] [ dbms-guide-8.4.2] a [důležité informace o výkonu pro zálohování a obnovení][dbms-guide-8.4.3]. 

#### <a name="other"></a>Ostatní
Další obecné oblasti jsou již popsané v příslušné MaxDB SAP [to] [ dbms-guide-8.4.4] kapitoly. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Specifika serveru SAP obsahu v systému Windows
Obsah serveru SAP je komponenta samostatný, na serveru k ukládání obsahu například elektronických dokumentů v různých formátech. Server obsahu SAP zajišťuje vývoj technologie a má být použít mezi aplikacemi pro všechny aplikace SAP. Je nainstalovaná v samostatném systému. Typické obsah je školicí materiály a dokumentace z skladu znalostní báze nebo technické výkresy pocházející z mySAP PLM systém správy dokumentů. 

### <a name="sap-content-server-version-support"></a>Podpora verze obsahu serveru SAP
SAP aktuálně podporuje:

* **Server obsahu SAP** s verzí **6.50 (a vyšší)**
* **SAP MaxDB verze 7.9**
* **Microsoft IIS (Internet Information Server) verze 8.0 (a vyšší)**

Důrazně doporučujeme používat nejnovější verzi obsahu serveru SAP, což v době psaní tohoto dokumentu je **6.50 SP4**a nejnovější verzi **Microsoft IIS 8.5**. 

Zkontrolujte podporované verze obsahu serveru SAP a Microsoft IIS v [SAP produktu dostupnosti matice (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Podporované typy Microsoft Windows a virtuálních počítačů Azure pro Server obsahu SAP
Podporované verze systému Windows pro SAP Server obsahu v Azure, najdete v tématu:

* [SAP produktu dostupnosti matice (PAM)][sap-pam]
* Poznámka SAP [1928533]

Důrazně doporučujeme používat nejnovější verzi systému Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny ke konfiguraci serveru obsahu SAP pro SAP instalace ve virtuálních počítačích Azure
#### <a name="storage-configuration"></a>Konfigurace úložiště
Pokud nakonfigurujete Server obsahu SAP k ukládání souborů v databázi SAP MaxDB, všechny úložiště Azure osvědčených postupů doporučení uvedená pro SAP MaxDB v kapitole [konfigurace úložiště] [ dbms-guide-8.4.1] platí také pro scénář obsahu serveru SAP. 

Pokud nakonfigurujete Server obsahu SAP k uložení souborů v systému souborů, doporučujeme použít vyhrazený logické jednotky. Použití prostorů úložiště systému Windows umožňuje také zvýšit velikost logického disku a propustnost IOPS, jak je popsáno v kapitole [softwaru diskového pole RAID][dbms-guide-2.2]. 

#### <a name="sap-content-server-location"></a>Umístění obsahu serveru SAP
Server obsahu SAP, je nutné nasadit ve stejné oblasti Azure a virtuální síť Azure, kde je nasazen v systému SAP. Můžete se mohou rozhodnout, jestli chcete nasadit Server obsahu SAP součásti na vyhrazený virtuální počítač Azure nebo na kterém je spuštěný v systému SAP stejného virtuálního počítače. 

![Vyhrazený virtuální počítač Azure pro Server obsahu SAP][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>Umístění mezipaměti serveru SAP
Serveru SAP mezipaměti je další komponentu na serveru k poskytování přístupu k místně (v mezipaměti) dokumenty. Ukládá do mezipaměti serveru SAP mezipaměti dokumenty k obsahu serveru SAP. Toto je za účelem optimalizace síťový provoz, pokud mají být načteny více než jednou z různých míst dokumenty. Obecně platí, že serveru SAP mezipaměti musí být fyzicky blízko klienta, který má přístup k serveru SAP mezipaměti. 

Zde máte dvě možnosti:

1. **Klient je systém SAP back-end** Pokud systému SAP back-end je nakonfigurovaná pro přístup k obsahu serveru SAP, že systém SAP je klienta. Při nasazování systému SAP a obsahu serveru SAP ve stejné oblasti Azure, ve stejném datovém centru Azure, jsou fyzicky blízko sebe navzájem. Je proto potřeba mít vyhrazený Server mezipaměti SAP. Klienti SAP uživatelského rozhraní (SAP GUI nebo webový prohlížeč) přímý přístup v systému SAP a v systému SAP načte dokumenty ze serveru obsahu SAP.
2. **Klient je webový prohlížeč místně** lze nastavit v obsahu serveru SAP nelze přistupovat přímo ve webovém prohlížeči. V takovém případě je webový prohlížeč s místně klienta serveru SAP obsahu. Místního datového centra a datové centrum Azure jsou umístěny v různých fyzických lokacích (v ideálním případě blízko sebe navzájem). Vaše místního datového centra je připojená k Azure přes Azure Site-to-Site VPN nebo ExpressRoute. I když obě možnosti nabízí zabezpečené připojení sítě VPN do Azure, site-to-site síťové připojení nenabízí SLA síťové šířky pásma a čekací doba mezi místního datového centra a datovém centru Azure. Pro urychlení přístupu k dokumentům, můžete provést jednu z těchto možností:
   1. Instalace serveru SAP mezipaměti místně, zavřete do místní webové prohlížeče (možnost [to] [ dbms-guide-900-sap-cache-server-on-premises] obrázek)
   2. Konfigurace Azure ExpressRoute, který nabízí vysokorychlostní a nízkou latencí vyhrazené síťové připojení mezi místního datového centra a datového centra Azure.

![Možnost instalace serveru SAP mezipaměti místně][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Backup / obnovení
Při konfiguraci serveru SAP obsahu k ukládání souborů v databázi SAP MaxDB, zálohování a obnovení postupu a výkonové požadavky jsou již popsané v kapitole SAP MaxDB [zálohování a obnovení] [ dbms-guide-8.4.2] a kapitoly [otázky výkonu při zálohování a obnovení][dbms-guide-8.4.3]. 

Při konfiguraci serveru SAP obsahu k uložení souborů v systému souborů, jednou z možností je provést ruční zálohování nebo obnovení struktury celý soubor, kde se nachází v dokumentech. Podobně jako SAP MaxDB zálohování a obnovení, se doporučuje mít vyhrazeným diskovým svazek pro zálohování účel. 

#### <a name="other"></a>Ostatní
Další nastavení SAP obsahu specifickou pro Server jsou transparentní pro virtuální počítače Azure a jsou popsané v různých dokumenty a SAP poznámky:

* <https://service.sap.com/contentserver> 
* Poznámka SAP [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Podrobné IBM DB2 pro LUW v systému Windows
S Microsoft Azure můžete snadno migrovat stávající aplikaci SAP systémem IBM DB2 pro Linux, UNIX a systému Windows (LUW) na virtuálních počítačích Azure. S SAP na IBM DB2 pro LUW správci a vývojáři můžete dál používat stejné vývoj a nástroje pro správu, které jsou k dispozici místně.
Obecné informace o spuštění na IBM DB2 SAP Business Suite, LUW naleznete v SAP komunity sítě (oznámení změny stavu) v <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Další informace a aktualizace o SAP v DB2 pro LUW v Azure, viz poznámka SAP [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 pro Linux, UNIX a podpora verzí systému Windows
SAP na IBM DB2 pro LUW na služby Microsoft Azure virtuálního počítače je podporováno od verze DB2 10.5.

Informace o podporovaných produktech SAP a typy virtuálního počítače Azure, najdete v části Poznámka SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 pro Linux, UNIX a pokyny pro konfigurace systému Windows pro SAP instalace ve virtuálních počítačích Azure
#### <a name="storage-configuration"></a>Konfigurace úložiště
Všechny soubory databáze musí být uložen v systému souborů NTFS podle přímo připojených disků. Tyto disky jsou připojené k virtuálnímu počítači Azure a jsou založené na Azure úložiště objektů BLOB stránky (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) nebo spravované disky (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Jakýkoli druh síťové jednotky nebo vzdálených sdílených složkách, jako jsou následující služby Azure file **není** podporované pro soubory databáze: 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Pokud používáte disky na základě úložiště objektů BLOB stránky Azure nebo spravovat disky, příkazy provedené v tomto dokumentu v kapitole [struktura nasazení RDBMS] [ dbms-guide-2] platí také pro nasazení s IBM DB2 LUW databáze. 

Jak je popsáno výše v části Obecné v dokumentu, existují kvóty na propustnost IOPS pro disky. Přesný kvóty závisí na typu virtuálního počítače použít. Seznam typů virtuálních počítačů s jejich kvóty najdete [zde (Linux)] [ virtual-machines-sizes-linux] a [zde (Windows)][virtual-machines-sizes-windows].

Tak dlouho, dokud stačí aktuální kvóty IOPS na disk, je možné uložit všechny soubory databáze na jednom disku jedné připojené. 

Výkon aspekty také naleznete kapitoly "Data zabezpečení a důležité informace o výkonu pro databázi adresáře" v příručkách instalace SAP.

Alternativně můžete použít fondy úložiště systému Windows (pouze k dispozici v systému Windows Server 2012 a vyšší) nebo proložení Windows pro systém Windows 2008 R2 jako popsané v kapitole [softwaru diskového pole RAID] [ dbms-guide-2.2] v tomto dokumentu vytvořte jeden velké logické zařízení přes několik disků.
Pro disky obsahující cesty úložiště DB2 pro sapdata a saptmp adresářů musíte zadat velikost sektoru fyzického disku 512 KB. Pokud používáte fondy úložišť systému Windows, je třeba vytvořit fondy úložiště ručně pomocí rozhraní příkazového řádku pomocí parametru `-LogicalSectorSizeDefault`. Další informace najdete v tématu <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

#### <a name="backuprestore"></a>Zálohování a obnovení
Funkce zálohování a obnovení pro IBM DB2 pro LUW je podporována stejným způsobem jako standardní operační systémy Windows Server a Hyper-V.

Musí se ujistěte, že máte zavedenou strategie zálohování platnou databázi. 

Jako úplné nasazení výkonu zálohování a obnovení závisí na kolik svazky lze číst paralelně a propustnost tyto svazky, které je možné. Kromě toho může spotřeby procesoru, který je používán kompresi zálohy přehrát významnou roli na virtuálních počítačích s maximálně 8 vláken procesoru. Proto můžete předpokládat jeden:

* Méně počet disků používá k ukládání databáze zařízení, menší celkovou propustnost čtení
* Menší že počet procesoru vláken ve virtuálním počítači, závažnější dopad kompresi zálohy
* Méně cílů (Stripe adresáře, disků) k zápisu zálohování do dolní propustnost

Pokud chcete zvýšit počet cílů k zápisu, může být dvě možnosti, podle potřeby použít nebo kombinaci:

* Prokládání cílový svazek přes víc disků za účelem zlepšení propustnosti IOPS na tomto svazku prokládané
* Použití více než jeden cílový adresář pro psaní zálohy

#### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii
Microsoft Cluster Server (MSCS) není podporována.

Zotavení po havárii DB2 vysokou dostupnost (HADR) je podporováno. Pokud virtuální počítače HA konfigurace funguje překlad, instalační program v Azure se neliší od žádné nastavení, která se provádí na místě. Není doporučeno spoléhají na pouze pro překlad IP.

Nepoužívejte geografická replikace pro účty úložiště, které ukládají databáze disky. Další informace najdete v části kapitoly [Microsoft Azure Storage] [ dbms-guide-2.3] a kapitoly [vysokou dostupnost a zotavení po havárii s virtuálními počítači Azure][dbms-guide-3].

#### <a name="other"></a>Ostatní
Všechny ostatní obecné oblasti jako skupiny dostupnosti Azure nebo SAP monitorování platí jak je popsáno v první tři kapitol tohoto dokumentu pro nasazení virtuálních počítačů s IBM DB2 pro LUW také. 

Se také podívat na kapitoly [obecné SQL Server pro SAP v Azure souhrnu][dbms-guide-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Podrobné IBM DB2 pro LUW v systému Linux
S Microsoft Azure můžete snadno migrovat stávající aplikaci SAP systémem IBM DB2 pro Linux, UNIX a systému Windows (LUW) na virtuálních počítačích Azure. S SAP na IBM DB2 pro LUW správci a vývojáři můžete dál používat stejné vývoj a nástroje pro správu, které jsou k dispozici místně. Obecné informace o spuštění na IBM DB2 SAP Business Suite, LUW naleznete v SAP komunity sítě (oznámení změny stavu) v <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Další informace a aktualizace o SAP v DB2 pro LUW v Azure, viz poznámka SAP [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 pro Linux, UNIX a podpora verzí systému Windows
SAP na IBM DB2 pro LUW na služby Microsoft Azure virtuálního počítače je podporováno od verze DB2 10.5.

Informace o podporovaných produktech SAP a typy virtuálního počítače Azure, najdete v části Poznámka SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 pro Linux, UNIX a pokyny pro konfigurace systému Windows pro SAP instalace ve virtuálních počítačích Azure
#### <a name="storage-configuration"></a>Konfigurace úložiště
Všechny soubory databáze musí být uložen v systému souborů podle přímo připojených disků. Tyto disky jsou připojené k virtuálnímu počítači Azure a jsou založené na Azure úložiště objektů BLOB stránky (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) nebo spravované disky (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Jakýkoli druh síťové jednotky nebo vzdálených sdílených složkách, jako jsou následující služby Azure file **není** podporované pro soubory databáze:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Pokud používáte disky založené na úložiště objektů BLOB stránky Azure, příkazy provedené v tomto dokumentu v kapitole [struktura nasazení RDBMS] [ dbms-guide-2] platí také pro nasazení s IBM DB2 LUW databáze.

Jak je popsáno výše v části Obecné v dokumentu, existují kvóty na propustnost IOPS pro disky. Přesný kvóty závisí na typu virtuálního počítače použít. Seznam typů virtuálních počítačů s jejich kvóty najdete [zde (Linux)] [ virtual-machines-sizes-linux] a [zde (Windows)][virtual-machines-sizes-windows].

Tak dlouho, dokud stačí aktuální kvóty IOPS na disk, je možné uložit všechny soubory databáze na jeden jediný disk.

Výkon aspekty také naleznete kapitoly "Data zabezpečení a důležité informace o výkonu pro databázi adresáře" v příručkách instalace SAP.

Alternativně můžete použít LVM (Správce logických svazku) nebo MDADM jak je popsáno v kapitole [softwaru diskového pole RAID] [ dbms-guide-2.2] v tomto dokumentu vytvořte jeden velké logické zařízení přes několik disků.
Pro disky obsahující cesty úložiště DB2 pro sapdata a saptmp adresářů musíte zadat velikost sektoru fyzického disku 512 KB.

#### <a name="backuprestore"></a>Zálohování a obnovení
Funkce zálohování a obnovení pro IBM DB2 pro LUW je podporována stejným způsobem jako místními standardní Linux instalace.

Musí se ujistěte, že máte zavedenou strategie zálohování platnou databázi.

Jako úplné nasazení výkonu zálohování a obnovení závisí na kolik svazky lze číst paralelně a propustnost tyto svazky, které je možné. Kromě toho může spotřeby procesoru, který je používán kompresi zálohy přehrát významnou roli na virtuálních počítačích s maximálně 8 vláken procesoru. Proto můžete předpokládat jeden:

* Méně počet disků používá k ukládání databáze zařízení, menší celkovou propustnost čtení
* Menší že počet procesoru vláken ve virtuálním počítači, závažnější dopad kompresi zálohy
* Méně cílů (Stripe adresáře, disků) k zápisu zálohování do dolní propustnost

Pokud chcete zvýšit počet cílů k zápisu, může být dvě možnosti, podle potřeby použít nebo kombinaci:

* Prokládání cílový svazek přes víc disků za účelem zlepšení propustnosti IOPS na tomto svazku prokládané
* Použití více než jeden cílový adresář pro psaní zálohy

#### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii
Zotavení po havárii DB2 vysokou dostupnost (HADR) je podporováno. Pokud virtuální počítače HA konfigurace funguje překlad, instalační program v Azure se neliší od žádné nastavení, která se provádí na místě. Není doporučeno spoléhají na pouze pro překlad IP.

Nepoužívejte geografická replikace pro účty úložiště, které ukládají databáze disky. Další informace najdete v části kapitoly [Microsoft Azure Storage] [ dbms-guide-2.3] a kapitoly [vysokou dostupnost a zotavení po havárii s virtuálními počítači Azure][dbms-guide-3].

#### <a name="other"></a>Ostatní
Jak je popsáno v první tři kapitol tohoto dokumentu pro nasazení virtuálních počítačů s IBM DB2 pro LUW také použít všechny ostatní obecné témata jako skupiny dostupnosti Azure nebo SAP monitorování.

Se také podívat na kapitoly [obecné SQL Server pro SAP v Azure souhrnu][dbms-guide-5.8].

