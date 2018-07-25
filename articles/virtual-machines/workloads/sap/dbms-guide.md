---
title: Nasazení DBMS virtuálních počítačů pro SAP NetWeaver v Azure | Dokumentace Microsoftu
description: Nasazení Azure Virtual Machines DBMS pro SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
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
ms.openlocfilehash: 2caa9a5137edd4e012adf704c01dc5c470e1bb51
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972440"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Nasazení Azure Virtual Machines DBMS pro SAP NetWeaver
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Tato příručka je součástí sady dokumentace na implementaci a nasazení softwaru SAP v Microsoft Azure. Před čtením této příručky, přečtěte si [implementaci Průvodce plánováním a][planning-guide]. Tento dokument popisuje nasazení různých systémů správy relační databáze (RDBMS) a související produkty v kombinaci s řešením SAP v Microsoft Azure Virtual Machines (VM) pomocí Azure infrastruktury jako služby (IaaS) funkce.

Dokument doplňuje dokumentaci k instalaci SAP a SAP poznámky, které představují primární zdroje pro instalaci a nasazení softwaru SAP v dané platformy.

## <a name="general-considerations"></a>Obecné aspekty
V této kapitole vydávají aspekty provozování systémů souvisejících s SAP DBMS na virtuálních počítačích Azure. Existuje několik odkazů na konkrétní systémy DBMS v této kapitole. Místo toho konkrétní systémy DBMS jsou zpracovány v tomto dokumentu po této kapitole.

### <a name="definitions-upfront"></a>Definice předem
V celém dokumentu používáme následující termíny:

* IaaS: Infrastruktura jako služba.
* PaaS: Platforma jako služba.
* SaaS: Software jako služba.
* Součást SAP: jednotlivé SAP aplikaci jako je třeba ECC, BW, správci řešení nebo podnikovém portálu.  Komponenty SAP může být založen na tradičních technologií ABAP a Java nebo jiných NetWeaver na základě aplikace, například pro obchodní objekty.
* Prostředí SAP: jedna nebo více součástí SAP logicky seskupeny provádět obchodní funkce jako je vývoj, QAS, školení, zotavení po Havárii nebo produkčního prostředí.
* Prostředí SAP: To se vztahuje na celý SAP prostředky v zákazníka IT na šířku. Prostředí SAP zahrnuje všechny produkčního prostředí a neprodukční prostředí.
* Systém SAP: Kombinace DBMS vrstvy a aplikační vrstvu služby, například vývojový systém SAP ERP, SAP BW testovacího systému, SAP CRM produkční systém atd. V nasazení Azure není možné rozdělit tyto dvě vrstvy mezi místními a Azure. To znamená, že je buď systém SAP nasazené v místním nebo je nasazené v Azure. Však můžete nasadit různé systémy prostředí SAP v Azure nebo místně. Můžete například nasazení SAP CRM vývoj a testování v Azure, ale SAP CRM produkční systém místní systémy.
* Nasazení jenom cloudu: nasazení, kde předplatné Azure, které není připojené přes site-to-site nebo připojením ExpressRoute do místní síťové infrastruktury. V běžných dokumentace ke službě Azure těchto typů nasazení jsou také popsány jako "Čistě cloudových" nasazení. Virtuální počítače nasazené pomocí této metody jsou přístupné přes Internet a veřejné internetové koncové body přiřazené k virtuálním počítačům v Azure. V místním Active Directory (AD) a DNS se rozšíří do Azure v těchto typech nasazení. Proto se virtuální počítače nejsou součástí skupiny v místní službě Active Directory. Poznámka: Čistě cloudových nasazení v tomto dokumentu jsou definovány jako kompletní prostředí SAP, které jsou spuštěny výhradně v Azure bez rozšíření služby Active Directory nebo překlad místních do veřejného cloudu. Výhradně cloudový konfigurace se nepodporují konfigurace, kdy moduly SAP STM nebo jiných místních prostředků je potřeba použít mezi systémy SAP hostované na Azure a prostředky, které se nacházejí v místním nebo produkční systémy SAP.
* Mezi různými místy: Popisuje scénář, ve které jsou nasazené virtuální počítače s předplatným Azure, který má site-to-site a multi-Site, připojení ExpressRoute mezi místní nápomocni a Azure. Dokumentace v běžných Azure a tyto druhy nasazení jsou také popsány jako scénáře mezi různými místy. Důvod pro připojení je rozšířit místní domény, v místní službě Active Directory a DNS v místním prostředí do Azure. V místním prostředí je rozšířit na prostředky předplatného Azure. S touto příponou, virtuální počítače můžou být součástí místní domény. Domény uživatele z místní domény můžete přistupovat k serverům a spuštěním služby na těchto virtuálních počítačů (například systému DBMS služby). Místně nasadili komunikace a název řešení mezi virtuálními počítači a je možné virtuální počítače nasazené v Azure. Očekáváme, že toto nejběžnější scénáře pro nasazení prostředků SAP v Azure. Další informace najdete v tématu [v tomto článku] [ vpn-gateway-cross-premises-options] a [v tomto článku][vpn-gateway-site-to-site-create].

> [!NOTE]
> Nasazení mezi místními systémy SAP, kde Azure Virtual Machines systémy SAP jsou členy domény služby v místním jsou podporovány pro produkční systémy SAP. Konfigurace mezi různými místy jsou podporovány pro nasazení částí nebo dokončení prostředí SAP do Azure. I spuštěné kompletní prostředí SAP v Azure vyžaduje, mají tyto virtuální počítače se zapojil místní domény a reklamy. V předchozí verze dokumentace, už jsme mluvili o IT hybridních scénářích, kde výraz *hybridní* spočívají ve skutečnosti, že existuje připojení mezi místními sítěmi mezi místními a Azure. V tomto případě *hybridní* také znamená, že virtuální počítače v Azure jsou součástí v místní službě Active Directory.
> 
> 

Některé dokumentaci Microsoftu popisuje scénáře, mezi různými místy trochu jinak, především pro DBMS HA konfigurace. V případě dokumenty týkající se SAP se scénáři varu mezi různými místy dolů s site-to-site nebo privátního připojení (ExpressRoute) a na skutečnost, že SAP šířku distribuuje mezi místními a Azure.

### <a name="resources"></a>Zdroje a prostředky
Následující pokyny jsou k dispozici pro nasazení SAP v Azure:

* [Azure Virtual Machines, plánování a implementace SAP NetWeaver][planning-guide]
* [Nasazení virtuálních počítačů pro SAP NetWeaver v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP NetWeaver (tohoto dokumentu)][dbms-guide]

Následující poznámky SAP jsou související s řešením SAP v Azure:

| Poznámka: číslo | Titul |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: podporované produkty a virtuálních počítačů Azure typy |
| [2015553] |SAP v Microsoft Azure: požadavky pro podporu |
| [1999351] |Řešení potíží s rozšířené monitorování Azure pro SAP |
| [2178632] |Klíč monitorování metrik pro SAP v Microsoft Azure |
| [1409604] |Virtualizace ve Windows: rozšířené monitorování |
| [2191498] |SAP v Linuxu se službou Azure: rozšířené monitorování |
| [2039619] |Aplikace SAP v Microsoft Azure s využitím databáze Oracle: podporované produkty a verze |
| [2233094] |DB6: Aplikace SAP v Azure s využitím IBM DB2 pro Linux, UNIX a Windows – Další informace |
| [2243692] |Linux v Microsoft Azure (IaaS) virtuálních počítačů: problémy licence SAP |
| [1984787] |Operačního systému SUSE LINUX Enterprise Server 12: Poznámky k instalaci |
| [2002167] |Red Hat Enterprise Linux 7.x: instalace a Upgrade |
| [2069760] |Oracle Linux 7.x SAP instalace a Upgrade |
| [1597355] |Doporučení odkládacího prostoru pro Linux |
| [2171857] |Oracle Database 12c – podpora systému souborů v Linuxu |
| [1114181] |Databáze Oracle 11g - podpora systému souborů v Linuxu |


Přečíst také [oznámení změny stavu Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) , která obsahuje všechny poznámky SAP pro Linux.

Měli byste mít praktické znalosti o Architektura Microsoft Azure a jak jsou nasazené a provozované Microsoft Azure Virtual Machines. Další informace najdete <https://azure.microsoft.com/documentation/>

> [!NOTE]
> Nepovedlo se **není** diskuze o platformě Microsoft Azure jako služba (PaaS) nabídky platformy Microsoft Azure. Tento dokument je spuštěn systém správy databáze (DBMS) v Microsoft Azure Virtual Machines (IaaS), jako byste spustili systém DBMS v místním prostředí. Možnosti databáze a funkcí mezi tyto dvě nabídky se velmi liší a by neměl promíchala mezi sebou. Viz také: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Vzhledem k tomu, že budeme se zabývat IaaS, obecně Windows, Linux a DBMS instalace a konfigurace jsou v podstatě stejné jako libovolný virtuální počítač nebo úplné systému počítač, nainstalujete místní. Existují však některé architektury a systém správy implementace rozhodnutí, která se liší, při použití IaaS. Účelem tohoto dokumentu je vysvětlit konkrétní architektury a rozdíly systému správy, které vám musí být připravené při použití IaaS.

Obecně platí celkový oblasti rozdílem, že tento dokument popisuje, jsou:

* Plánování správné rozložení disku virtuálního počítače a systémy SAP tak, aby zkontrolovali, že máte správná data soubor rozložení a dosáhnout dostatek vstupně-výstupních operací pro vaši úlohu.
* Důležité informace o sítích při použití IaaS.
* Konkrétní databázové funkce, které chcete použít k optimalizaci rozložení.
* Aspekty zálohování a obnovení v IaaS.
* Využívá různé druhy Image pro nasazení.
* Zajištění vysoké dostupnosti v Azure IaaS.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktura nasazení relační databázový systém
Pokud chcete postupovat podle této kapitole, je potřeba pochopit, co se zobrazí v [to] [ deployment-guide-3] kapitoly [Průvodce nasazením][deployment-guide]. Poznatky o různých řadu virtuálních počítačů a jejich rozdílů a rozdíly Azure na úrovni Standard a Premium Storage by měl porozuměl jsem jim a známé před čtením této kapitole.

Až do března 2015 byly omezený na 127 GB. velikost disků, které obsahují operační systém. Toto omezení je teď v březnu 2015 zrušeno vs. (pro další informace o kontrolu <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). Odtud na disk obsahující operační systém může mít stejnou velikost jako ostatní disku. Nicméně jsme stále raději strukturu nasazení, kde jsou oddělené od databázové soubory operačního systému, DBMS a konečný výsledek SAP binární soubory. Proto Očekáváme, že ve službě Azure Virtual Machines se systémy SAP mít základní virtuální počítač (nebo disk) nainstalovány s operačním systémem, spustitelné soubory systému správy databáze a SAP spustitelné soubory. Soubory protokolu a data DBMS jsou uložené ve službě Azure Storage (Standard nebo Premium Storage) v samostatných disků a připojit k původní bitové kopie operačního systému Azure virtuální počítač jako logické disky. 

Jsou závislé na využití Azure na úrovni Standard nebo Premium Storage (například s použitím řady DS nebo GS-series virtuálních počítačů) existuje další kvóty v Azure, které jsou popsány [tady (Linux)] [ virtual-machines-sizes-linux] a [ Tady (Windows)][virtual-machines-sizes-windows]. Při plánování rozložení disku, je potřeba najít nejlepší poměr kvóty pro následující položky:

* Počet datových souborů.
* Počet disků, které obsahují soubory.
* Kvóty vstupně-výstupních operací z jednoho disku.
* Propustnost dat na disk.
* Počet dalších datových disků je to možné, velikosti virtuálních počítačů.
* Celkovou propustnost úložiště může poskytnout virtuálního počítače.

Azure vynucuje kvóty vstupně-výstupních operací na datový disk. Tyto kvóty se liší pro disky hostované na Azure Storage úrovně Standard a Premium Storage. Latence vstupu/výstupu se také velmi liší mezi dvěma typy úložiště se službou Premium Storage poskytuje lepší latence vstupu/výstupu faktorů. Každý z různých typů virtuálních počítačů má omezený počet datových disků, které budete moct připojit. Dalším omezením je skutečnost, že pouze určité typy virtuálních počítačů můžete využít Azure Premium Storage. To znamená, že rozhodnutí pro určitého typu virtuálních počítačů nemusí pouze bude týkat procesoru a paměti požadavky, ale také podle IOPS, latenci a disk požadavkům na propustnost, které jsou obvykle škálovat počet disků nebo typ disků Premium Storage. Zejména s Premium Storage velikosti disku také může být určený počet IOPS a propustnost, kterou je potřeba se dá dosáhnout každého disku.

Skutečnost, že celková míra vstupně-výstupních operací, počet disků připojit, a velikost virtuálního počítače jsou všechny spojených dohromady, může způsobit, že Azure konfigurace je jiná než jeho nasazení v místním systému SAP. Limity vstupně-výstupních operací na logickou jednotku se obvykle dají konfigurovat v místních nasazeních. Zatímco pomocí služby Azure Storage jsou tyto limity pevný nebo jako Storage úrovně Premium závisí na typu disku. Proto se v místním nasazení vidíme zákaznických konfigurací databázové servery, které používají řadu různých svazcích pro speciální spustitelné soubory, jako je SAP a DBMS nebo speciální svazky pro dočasné databáze nebo tabulky mezery. Pokud místnímu systému je přesunuta do Azure, může vést k plýtvání potenciální šířku pásma vstupně-výstupních operací podle plýtvání na disk pro spustitelné soubory nebo databáze, které nebudete provádět žádné nebo není velké množství vstupně-výstupních operací. Ve virtuálních počítačích Azure, proto doporučujeme, spustitelné soubory DBMS a SAP Pokud je to možné nainstalovat na disk s operačním systémem.

Umístění souborů databáze a soubory protokolu a typ úložiště Azure, musí být definován tak, že vstupně-výstupních operací, latence a požadavky na propustnost. Pokud chcete mít dostatek vstupně-výstupních operací pro protokol transakcí, mohlo by být vynuceno využívat více disků pro soubor protokolu transakcí nebo použít větší disk Storage úrovně Premium. V takovém případě jeden by sestavení softwaru diskového pole RAID (pro příklad Windows úložiště fondu pro Windows nebo MDADM a LVM (Správce logických svazků) pro Linux) s disky, které obsahují transakčního protokolu.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Jednotce D:\ ve Virtuálním počítači Azure je jednotka netrvalé, která je založená na některé místní disky na Azure výpočetním uzlu. Protože je dočasný, to znamená, že dojde ke ztrátě všechny změny provedené v obsahu na jednotku D:\, po restartování virtuálního počítače. "Všechny změny" představuje uložené soubory, adresáře vytvořené, nainstalované aplikace atd.
> 
> ![Linux][Logo_Linux] Linux
> 
> Virtuální počítače Azure s Linuxem automaticky připojit jednotku na /mnt/resource, který je jednotka netrvalé se opírá o místní disky na Azure výpočetním uzlu. Protože je dočasný, to znamená, že všechny změny obsahu v /mnt/resource ztraceny po restartování virtuálního počítače. Všechny změny představuje soubory uložené, vytvoření adresáře, nainstalované aplikace atd.
> 
> 

- - -
Závisí na Azure řadu virtuálních počítačů, místní disky na výpočetním uzlu zobrazit různé výkonu, které lze označit jako:

* A0 – A7: Velmi omezená výkonu. Nejde použít pro všechno, co je nad rámec stránkovací soubor windows
* Řady a8-A11: Velmi dobré výkonové charakteristiky se některé vstupně-výstupních operací 10 tisíc a > propustnost 1GB/s
* Řady D-Series: Velmi dobré výkonové charakteristiky se některé vstupně-výstupních operací 10 tisíc a > propustnost 1GB/s
* Řady DS-Series: Velmi dobré výkonové charakteristiky se některé vstupně-výstupních operací 10 tisíc a > propustnost 1GB/s
* Řada G-Series: Velmi dobré výkonové charakteristiky se některé vstupně-výstupních operací 10 tisíc a > propustnost 1GB/s
* Řady GS-Series: Velmi dobré výkonové charakteristiky se některé vstupně-výstupních operací 10 tisíc a > propustnost 1GB/s

Výše uvedené příkazy se má použít pro typy virtuálních počítačů, které jsou certifikované s řešením SAP. Řadu virtuálních počítačů s vynikající IOPS a propustnost nárok využívat některé funkce DBMS, jako jsou databáze tempdb nebo místa na dočasnou tabulku.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Ukládání do mezipaměti pro virtuální počítače a datové disky
Když vytvoříme datových disků prostřednictvím portálu nebo můžeme připojit nahrané disky pro virtuální počítače, abychom si mohli vybrat, zda jsou ukládány do mezipaměti vstupně-výstupní provoz mezi virtuálním počítači a tyto disky umístěné ve službě Azure storage. Azure Standard a Premium Storage pro tento typ mezipaměti použít dvě různé technologie. V obou případech by samotný mezipaměti disku vztahuje na stejné jednotky využívané dočasný disk (D:\ na Windows) nebo v Linuxu /mnt/resource virtuálního počítače.

Pro službu Azure Standard Storage jsou typy možných mezipaměti:

* Neexistující ukládání do mezipaměti
* Přečtěte si ukládání do mezipaměti
* Čtení a zápis do mezipaměti

Zajistí konzistentní vzhledem k aplikacím a deterministický výkonu byste měli nastavit ukládání do mezipaměti v Azure Storage úrovně Standard pro všechny disky obsahující **DBMS souvisejících datových souborů, souborů protokolu a místa pro tabulku 'NONE'**. Ukládání do mezipaměti virtuálním počítači může zůstat výchozí.

Pro službu Azure Premium Storage existují tyto možnosti ukládání do mezipaměti:

* Neexistující ukládání do mezipaměti
* Přečtěte si ukládání do mezipaměti

Doporučení pro službu Azure Premium Storage je možné využít **čtení ukládání do mezipaměti pro datové soubory** z databáze SAP a vyberte možnost **neexistující ukládání do mezipaměti pro disky souborů protokolu**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>Softwarového pole RAID
Které jsou už bylo uvedeno výše, je potřeba vyvážení počtu vstupně-výstupních operací, které jsou potřeba pro soubory databáze do počtu disků, které můžete nakonfigurovat a poskytuje maximální IOPS virtuálního počítače Azure na disku nebo typ disku služby Premium Storage. Nejjednodušší způsob, jak řešit zatížení vstupně-výstupních operací za disky je sestavení softwarového pole RAID prostřednictvím různých discích. Pak umístíte počet datových souborů k SAP DBMS na jednotky LUN carved mimo softwarového pole RAID. Závislé na požadavcích, že kdy je vhodné zvážit použití Storage úrovně Premium i od dva ze tří různých discích Premium Storage poskytuje vyšší kvótu vstupně-výstupních operací než disky založené na úložiště úrovně Standard. Kromě velkou lepší latenci vstupně-výstupních operací poskytovaný Azure Premium Storage. 

Totéž platí pro transakční protokol z různých systémů DBMS. S mnoha jejich přidávání více souborů Tlog nepomůže od systémů DBMS zapisovat do jednoho ze souborů najednou pouze. Pokud než jeden disk Storage úrovně Standard na základě může poskytovat jsou potřeba vyšší míra vstupně-výstupních operací, můžete prokládanou přes několik disků Storage úrovně Standard, nebo můžete použít větší typ disku služby Premium Storage, který nad vyšší rychlosti vstupně-výstupních operací také poskytuje faktorů nižší latencí pro zápis I / Operační systém do transakčního protokolu.

Situace došlo v nasazení Azure, které by upřednostnit pomocí softwarového pole RAID jsou:

* Transakční protokol/znovu protokol vyžadují další vstupně-výstupních operací, než Azure poskytuje pro jeden disk. Jak je uvedeno výše to můžete vyřešit vytvořením logickou jednotku prostřednictvím více disků pomocí softwarového pole RAID.
* Nerovnoměrné vstupně-výstupních operací úloh distribuci přes různé datové soubory databáze SAP. V takových případech jeden může docházet k dosažení této kvóty spíše často jeden datový soubor. Zatímco ostatní datové soubory nejsou ještě přibližujete kvóty vstupně-výstupních operací na jeden disk. V takových případech je nejjednodušší řešení k vytvoření jedné logické jednotce přes několik disků pomocí softwarového pole RAID. 
* Nevíte, co přesně vstupně-výstupní úlohy na datovém souboru je a pouze zhruba vědět, co je celkové zatížení vstupně-výstupních operací pro správce databáze. Nejsnáze provést je vytvořit jednu logickou jednotku s pomocí softwarového pole RAID. Součet hodnot kvóty více disků za tuto logickou jednotku byste pak splnění známé rychlosti vstupně-výstupních operací.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Doporučujeme použít prostory úložiště Windows, pokud spouštíte na systému Windows Server 2012 nebo vyšší. To je mnohem efektivnější než Windows prokládáním starší verze Windows. Můžete potřebovat k vytvoření fondů úložišť systému Windows a prostory úložiště pomocí příkazů Powershellu při použití jako operační systém Windows Server 2012. Příkazy prostředí PowerShell můžete najít zde <https://technet.microsoft.com/library/jj851254.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> K vytvoření softwarového pole RAID v Linuxu jsou podporovány pouze MDADM a LVM (Správce logických svazků). Další informace najdete v následujících článcích:
> 
> * [Konfigurace softwaru diskového pole RAID v systému Linux][virtual-machines-linux-configure-raid] (pro MDADM)
> * [Konfigurace LVM na virtuální počítač s Linuxem v Azure][virtual-machines-linux-configure-lvm]
> 
> 

- - -
Důležité informace týkající se využití řad virtuálních počítačů, které jsou obvykle pracovat se službou Azure Premium Storage jsou:

* Požadavky pro latence vstupu/výstupu, která jsou blízko co poskytovat zařízení sítě SAN nebo NAS.
* Poptávka po faktory lepší vstupně-výstupních operací latenci, než může poskytovat Azure Standard Storage.
* Zadejte vyšší IOPS na virtuální počítač, než co lze dosáhnout pomocí více disků Storage úrovně Standard na určitém virtuálním počítači.

Protože základní služby Azure Storage replikuje každého disku do alespoň tři uzly úložiště a jednoduché RAID 0 prokládáním lze použít. Není nutné provádět RAID5 nebo RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage
Microsoft Azure Storage ukládá základní virtuální počítač (s operačním systémem) a disků nebo objekty BLOB na alespoň tři samostatné uzly úložiště. Při vytváření účtu úložiště nebo spravovaný disk, existuje možnost ochrany, jak je znázorněno zde:

![Geografická replikace povolena pro účet služby Azure Storage][dbms-guide-figure-100]

Místní účet replikace Azure Storage (místně redundantní) poskytuje úrovně ochrany proti ztrátě dat kvůli chybě infrastruktury, který může dovolit někteří zákazníci nasadit. Jak je uvedeno výše, že existují čtyři různé možnosti s pátá se varianta některý z prvních tří. Hledání je blíže jsme lze rozlišit:

* **Premium místně redundantní úložiště (LRS)**: Azure Premium Storage poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače provozované můžu intenzivních vstupně-výstupních operací. Existují tři repliky dat v rámci stejného datového centra Azure z oblasti Azure. Tyto kopie jsou v různých chybových nebo upgradovat doménách (naleznete v části koncepty [to] [ planning-guide-3.2] kapitolu v [Planning Guide][planning-guide]). V případě repliky data odcházející z služby z důvodu selhání uzlu úložiště nebo selhání disku je automaticky generovat novou repliku.
* **Místně redundantní úložiště (LRS)**: V tomto případě existují tři repliky dat v rámci stejného datového centra Azure z oblasti Azure. Tyto kopie jsou v různých chybových nebo upgradovat doménách (naleznete v části koncepty [to] [ planning-guide-3.2] kapitolu v [Planning Guide][planning-guide]). V případě repliky data odcházející z služby z důvodu selhání uzlu úložiště nebo selhání disku je automaticky generovat novou repliku. 
* **Geograficky redundantní úložiště (GRS)**: V tomto případě je asynchronní replikaci, která se předají další tři repliky dat v jiné oblasti Azure, která je ve většině případů, ve stejné zeměpisné oblasti (jako je Severní Evropa a západní Evropa ). V důsledku tři další repliky, aby byly v součtu šesti replik. Varianta této je doplněk, pokud data v oblasti geograficky replikované Azure je možné pro další účely (Read-Access Geo-redundantní).
* **Zóna redundantní úložiště (ZRS)**: V tomto případě zůstanou tří replik dat ve stejné oblasti Azure. Jak je vysvětleno v [to] [ planning-guide-3.1] kapitoly [Planning Guide] [ planning-guide] určitá oblast Azure může být několik datových center v těsné blízkosti. V případě LRS by repliky distribuovat napříč různých datových centrech, díky kterému jedné oblasti Azure.

Další informace najdete [tady][storage-redundancy].

> [!NOTE]
> Pro nasazení DBMS se nedoporučuje použití geograficky redundantní úložiště
> 
> Azure Storage geografická replikace je asynchronní. Replikace jednotlivých disků připojených do jednoho virtuálního počítače nejsou synchronizované v kroku zámku. Není proto vhodný replikace databázového systému souborů, které jsou distribuovány prostřednictvím různých discích nebo nasadit proti softwaru diskového pole RAID založené na několik disků. Systém DBMS software vyžaduje přesně synchronizaci trvalý disk úložiště v různých logických jednotek a základní disky nebo disky. Systém DBMS software používá různé mechanismy pro aktivity pořadí vstupně-výstupní operace zápisu a DBMS hlásí, že pokud tyto lišit i za několik milisekund, než je poškozený úložný prostor na disku cílem replikace. Proto pokud jeden chce, aby se konfigurace databáze s roztažená na několik disků geograficky replikované databáze, takové replikace je potřeba provést s prostředky databáze a funkce. Jeden neměli byste tedy spoléhat na Azure Storage geografické replikace k provedení této úlohy. 
> 
> Problém je nejjednodušší vysvětlují s příklad systémem. Předpokládejme, že máte systém SAP nahráli do Azure, který má osm disky obsahující data souborů systém DBMS plus jeden disk obsahující soubor protokolu transakcí. Jedna z těchto devíti disky mají data zapsaná do nich v konzistentní způsob podle DBMS, zda nepsalo se data do soubory protokolů dat nebo transakcí.
> 
> V, aby se správně geografickou replikaci dat a udržovat konzistentní databáze image, obsah všech devět disků by mohl být geograficky replikované v přesném pořadí vstupně-výstupních operací, které byly spuštěny před devět různých discích. Geografická replikace služby Azure Storage však neumožňuje deklarovat závislosti mezi disky. To znamená, že Microsoft Azure Storage georeplikace neví o tom, že obsah těchto devíti různých discích se vztahují k sobě navzájem a, že změny jsou konzistentní pouze v případě, že replikace v pořadí, vstupně-výstupní operace se stalo ve všech devět disky.
> 
> Kromě se, že geograficky replikovaného imagí ve scénáři se neposkytuje bitové kopie konzistentní databáze a také je snížení výkonu, který se zobrazuje s geograficky redundantní úložiště, který může vážně vysokou pravděpodobnost dopad na výkon. Stručně řečeno nepoužívejte tento typ redundance úložiště pro úlohy typu DBMS.
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mapování virtuálních pevných disků do účtu úložiště služby virtuálních počítačů Azure
Tato kapitola platí jenom pro účty Azure Storage. Pokud máte v plánu používat službu Managed Disks, omezení uvedených v této kapitole se nevztahují. Další informace o službě Managed Disks najdete v kapitole [Managed Disks] [ dbms-guide-managed-disks] tohoto průvodce.

Účet úložiště Azure se pouze administrativní konstrukce, ale také předmětem omezení. Zatímco omezení se liší v tom, jestli mluvíme o účtu služby Azure Standard Storage nebo účtu služby Azure Premium Storage. Přesné možnosti a omezení jsou uvedeny [zde][storage-scalability-targets]

Proto pro Azure Storage úrovně Standard je důležité si uvědomit, je omezený na vstupně-výstupních operací na účet úložiště (řádek, který obsahuje **celková míra žádosti** v [článku][storage-scalability-targets]). Kromě toho je počáteční omezený na 100 účtů úložiště na předplatné Azure (k červenci 2015). Proto se doporučuje pro počet IOPS virtuálních počítačů mezi více účtů úložiště při použití Azure Standard Storage. Zatímco jeden virtuální počítač v ideálním případě pokud je to možné používá jeden účet úložiště. Proto pokud mluvíme o nasazení databázového systému, kde každý virtuální pevný disk, který je hostitelem úložiště Azure úrovně Standard může dosáhnout své maximální kvóty, měli byste pouze nasadit 30 – 40 virtuálních pevných disků na účet úložiště Azure, která používá Azure Standard Storage. Na druhé straně Pokud využít Azure Premium Storage a chcete ukládat rozsáhlé databázové svazky, budete asi bez problémů z hlediska vstupně-výstupních operací. Ale účtu služby Azure Premium Storage je v objem dat způsobem více omezující než standardní účet úložiště Azure. V důsledku toho můžete nasadit jenom omezený počet virtuálních pevných disků v účtu služby Azure Premium Storage před dosažení limitu dat svazku. Na konci představit jako "Virtuální síť SAN", který má omezené možnosti v vstupně-výstupních operací a/nebo kapacity účtu úložiště Azure. V důsledku toho úkol zůstane, stejně jako v případě místních nasazení, pokud chcete definovat rozložení virtuálních pevných disků z různých systémů SAP prostřednictvím různých "imaginární zařízení sítě SAN" nebo účty úložiště Azure.

Pro službu Azure Standard Storage se nedoporučuje prezentovat úložiště z různých účtů úložiště na jeden virtuální počítač, pokud je to možné.

Pokud používáte DS nebo GS-series virtuálních počítačů Azure, je možné připojit virtuální pevné disky mimo standardní účty úložiště Azure a účty služby Premium Storage. Případy použití, jako je zápisu zálohy do úložiště úrovně Standard založená na virtuálních pevných disků a s DBMS data a soubory protokolu na Premium Storage přijdou do paměti, kde může využít heterogenní úložiště. 

Na základě zákaznických nasazení a testování přibližně 30 až 40 virtuálních pevných discích obsahující datové soubory databáze a soubory protokolů je možné zřídit na jedné Azure standardní účet úložiště se přijatelný výkon. Jak už bylo zmíněno dříve, omezení účtu služby Azure Premium Storage je pravděpodobně datové kapacity, který může obsahovat a ne vstupně-výstupních operací.

Jako SAN zařízení místní, sdílení vyžaduje monitorování aby nakonec slabých míst v účtu služby Azure Storage. Rozšíření Azure Monitoring pro SAP a webu Azure portal nabízí nástroje, které lze použít k detekci zaneprázdněný účty úložiště Azure, které může poskytovat neoptimální vstupně-výstupním výkonem.  Pokud se v této situaci detekuje, se doporučuje zaneprázdněné virtuální počítače přesunout do jiného účtu úložiště Azure. Odkazovat [Průvodce nasazením] [ deployment-guide] podrobnosti o tom, jak aktivovat SAP hostitelským možnosti monitorování.

Jiný článek shrnuje osvědčené postupy Azure Standard Storage a účty Azure Standard Storage najdete tady <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>Spravované disky
Spravované disky jsou nového typu prostředku v Azure Resource Manageru, který je možné použít místo virtuální pevné disky, které se ukládají v účtech úložiště Azure. Spravované disky automaticky zarovnají dostupnosti daného virtuálního počítače, které jsou připojeny k a proto zvýšit dostupnost virtuálního počítače a služby, které jsou spuštěné na virtuálním počítači. Další informace najdete v článku [přehledovém článku](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

SAP aktuálně podporuje jenom Managed Disks úrovně Premium. Poznámka SAP čtení [1928533] další podrobnosti.

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Přesun nasazení DBMS virtuální počítače z Azure Standard Storage do služby Azure Premium Storage
Dojít poměrně některé scénáře, kdy jako zákazník chcete přesunout nasazený virtuální počítač z Azure Standard Storage do služby Azure Premium Storage. Pokud vaše disky se ukládají v účtech úložiště Azure, to není možné bez dat fyzickým přesunutím. K dosažení cíle několika způsoby:

* Všechny virtuální pevné disky, základní virtuální pevný disk i virtuální pevné disky data může kopírovat do nového účtu služby Azure Premium Storage. Často vyberete počet virtuálních pevných disků v Azure Storage úrovně Standard není kvůli skutečnost, že jste museli datový svazek. Ale potřeba daný počet virtuálních pevných disků kvůli IOPS. Teď, když přejdete na Azure Premium Storage je může přejít do toho, jak méně virtuálních pevných disků k dosažení stejného propustnost vstupně-výstupních operací. Vzhledem k tomu, že ve službě Azure Standard Storage platíte za používaná data a nikoli velikost disku nominální počet virtuálních pevných disků není důležitá z hlediska nákladů. Však se službou Azure Premium Storage by platíte za nominální velikosti. Většina zákazníků, proto se pokuste mějte počet virtuálních pevných disků Azure Storage úrovně Premium na číslo, které jsou potřebné k dosažení propustnost vstupně-výstupních operací nezbytné. Ano většina zákazníků rozhodnout, proti způsob použití jednoduchých 1:1 kopírování.
* Pokud ještě není připojený, připojit jeden virtuální pevný disk obsahující zálohu databáze v databázi SAP. Po dokončení zálohování odpojte všechny virtuální pevné disky, včetně virtuální pevný disk obsahující zálohu a zkopírujte základní virtuální pevný disk a virtuální pevný disk s zálohování do účtu služby Azure Premium Storage. By pak nasaďte virtuální počítač založený na základní virtuální pevný disk a připojit virtuální pevný disk se zálohou. Teď vytvoříte další prázdné disky Storage úrovně Premium pro virtuální počítač, který slouží k obnovení databáze do. Předpokladem je, že systém DBMS umožňuje změnit cesty k souborům protokolu a data jako součást procesu obnovení.
* Další možností je varianta předchozího procesu, kde zkopírovat zálohu virtuálního pevného disku do služby Azure Premium Storage a připojit na virtuálním počítači, který nově nasazení a instalaci.
* Čtvrtá možnost byste zvolili po potřebují změnit počet datových souborů databáze. V takovém případě by provádět jako kopie homogenní systému SAP pomocí exportu/importu. PUT ty exportovat soubory do virtuálního pevného disku, který se zkopíruje do účtu služby Azure Premium Storage a připojení k virtuálnímu počítači, který používáte ke spouštění procesů import. Zákazníci využívat tuto možnost, hlavně tehdy, když chtějí snížit počet datových souborů.

Pokud používáte spravované disky, můžete migrovat do služby Premium Storage podle:

1. Zrušit přidělení virtuálního počítače
1. V případě potřeby změnit velikost virtuálního počítače na hodnotu, která podporuje Storage úrovně Premium (například DS nebo GS)
1. Změnit typ účtu spravovaného disku úrovně Premium (SSD)
1. Změnit ukládání do mezipaměti datových disků podle doporučení v kapitole [ukládání do mezipaměti pro virtuální počítače a datového disku][dbms-guide-2.1]
1. Spustit virtuální počítač

### <a name="deployment-of-vms-for-sap-in-azure"></a>Nasazení virtuálních počítačů pro SAP v Azure
Microsoft Azure nabízí několik způsobů, jak nasadit virtuální počítače a přidružené disky. Tím je důležité porozumět rozdílům od přípravy virtuálních počítačů se mohou lišit závisí na způsobu nasazení. Obecně platí podíváme do scénáře popsané v následujících kapitolách.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Nasazení virtuálního počítače na webu Azure Marketplace
Chcete společnosti Microsoft nebo třetích stran image z Azure Marketplace k dispozici pro nasazení virtuálního počítače. Po nasazení virtuálního počítače v Azure, můžete postupovat podle stejné pokyny a nástroje k instalaci softwaru SAP uvnitř virtuálního počítače, jako byste to udělali v místním prostředí. Pro instalaci softwaru SAP uvnitř virtuálního počítače Azure, SAPU a Microsoftu doporučujeme nahrávání a uložte instalační médium nástroje SAP v disky nebo k vytvoření virtuálního počítače Azure funguje jako "souborový server", který obsahuje všechny nezbytné SAP instalačního média.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Nasazení virtuálního počítače s generalizovanou imagí specifické pro zákazníka
Z důvodu opravy specifické požadavky týkající se vaší verze operačního systému nebo DBMS zadaný imagí v Tržišti Azure Marketplace, nemusí odpovídat vašim potřebám. Proto můžete potřebovat k vytvoření virtuálního počítače pomocí vlastní 'private' image operačního systému/DBMS virtuálního počítače, který po nasazení potom několikrát. Příprava duplikace "privátní" image, operačním systémem musí generalizovat na místní virtuální počítač. Odkazovat [Průvodce nasazením] [ deployment-guide] podrobnosti o tom, jak generalizace virtuálního počítače.

Pokud jste již nainstalovali SAP obsah ve vaší místní virtuální počítač (zejména pro systémy vrstvy 2), můžete upravit nastavení systému SAP po nasazení virtuálního počítače Azure prostřednictvím instance přejmenovat postupu podporovány službou SAP Software zřizování správce (SAP Mějte na paměti [1619720]). Jinak můžete nainstalovat SAP software až po nasazení virtuálního počítače Azure.

Databáze obsahu používaný aplikací SAP, můžete buď vygenerovat obsah čerstvě pomocí instalace SAP nebo svůj obsah můžete importovat do Azure pomocí virtuálního pevného disku při zálohování databáze DBMS nebo s využitím možnosti Správce databáze pro zálohování přímo do  Microsoft Azure Storage. V takovém případě může také připravit virtuální pevné disky s DBMS protokolu a data souborů místní a pak tyto disky importu do Azure. Ale přenos dat DBMS, který je načítán do Azure z místního bude fungovat přes disky VHD, které je potřeba se připravit místní.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Přesun virtuálního počítače z místního Azure s-zobecněný disk
Plánujete přesunout konkrétního systému SAP v místním do Azure (výtah a posunout). To můžete udělat tak, že nahrajete disk, který obsahuje operační systém, SAP binární soubory a konečný výsledek DBMS binární soubory a disky se soubory protokolu a data DBMS na Azure. V opačném scénář #2 výše, uchováváte název hostitele, identifikátor SID SAP a SAP uživatelské účty ve virtuálním počítači Azure byly nakonfigurované v místním prostředí. Proto zobecňuje bitovou kopii není nutné. Tento případ většinou platí pro scénáře míst, kde je součástí prostředí SAP spustit místně a části v Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Vysoká dostupnost a zotavení po havárii pomocí virtuálních počítačů Azure
Azure nabízí následující vysokou dostupnost (HA) a zotavení po havárii (DR) funkcí, které platí pro různé součásti, které můžeme použít pro nasazení produktu a SAP DBMS

### <a name="vms-deployed-on-azure-nodes"></a>Virtuální počítače nasazené na uzly Azure
Platforma Azure se nebude poskytovat funkce, jako je migrace za provozu pro nasazené virtuální počítače. Znamená to, že jestli v clusteru serveru, na kterém je nasazený virtuální počítač je nutné údržby, virtuální počítač je potřeba získat zastavit, restartovat. Údržba v Azure se provádí pomocí tak volá upgradu domén v rámci clusterů serverů. Pouze jeden upgradu domény postupně zachovaný. Během restartování je zaznamenat přerušení služby a virtuální počítač je vypnutý, provádění údržby virtuální počítač restartoval. Většina dodavatelů DBMS ale poskytuje vysokou dostupnost a zotavení po havárii funkcí, které rychle restartuje služby DBMS v jiném uzlu, pokud není k dispozici primárního uzlu. Na platformě Azure a nabízí funkce pro distribuci virtuálních počítačů, úložiště a dalších služeb Azure napříč doménami Upgrade k zajištění, že plánované údržby nebo infrastrukturu selhání postihlo pouze malou podmnožinu virtuální počítače nebo služby.  S pečlivé plánování, je možné dosáhnout dostupnosti úrovně srovnatelný s místní infrastruktury.

Microsoft Azure skupiny dostupnosti je logické seskupení virtuálních počítačů nebo služeb, které zajišťuje virtuálním počítačům a dalším službám nejsou distribuovány do různých chybových nebo upgradu domén v rámci clusteru tak, že by existovat pouze jeden uzel vypnutí v kterémkoli bodě v čase (čtení [tento (Linux)] [ virtual-machines-manage-availability-linux] nebo [tento (Windows)] [ virtual-machines-manage-availability-windows] , kde najdete další podrobnosti).

To je potřeba nakonfigurovat podle účelu při zavádění virtuální počítače, jak je vidět tady:

![Definice skupiny dostupnosti pro konfigurace DBMS HA][dbms-guide-figure-200]

Pokud chcete vytvořit vysoce dostupné konfigurace nasazení DBMS (nezávislé na jednotlivé DBMS HA funkce používá), bude nutné DBMS virtuálních počítačů:

* Přidání virtuálních počítačů do stejné virtuální síti Azure (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* Virtuální počítače v konfiguraci s vysokou DOSTUPNOSTÍ by měla být ve stejné podsíti. Překlad mezi různé podsítě není možné v čistě cloudových nasazení řešení funguje pouze IP. Nasazení mezi různými místy pomocí site-to-site nebo připojením ExpressRoute, síť s alespoň jednu podsíť je už navázalo. Překlad názvů lze provést podle místní zásady a síťové infrastruktury služby AD. 



#### <a name="ip-addresses"></a>IP adresy
Důrazně doporučujeme nastavit u konfigurací s vysokou DOSTUPNOSTÍ virtuálních počítačů odolných způsobem. Spoléhání se na IP adresy, které řešení partnerů vysokou dostupnost v rámci konfigurace s vysokou DOSTUPNOSTÍ není spolehlivé v Azure, pokud se používají statické IP adresy. V Azure existují dva "Vypnout" koncepty:

* Vypnutí prostřednictvím webu Azure portal nebo rutiny prostředí Azure PowerShell Stop-AzureRmVM: V tomto případě získá vypnutí virtuálního počítače a zrušit přidělení. Váš účet Azure se účtuje už pro tento virtuální počítač tak, aby byly, které se vám účtovat jenom poplatky za využívání úložiště. Pokud nebyla statické privátní IP adresu síťového rozhraní, ale IP adresa se uvolní a není zaručeno, že síťové rozhraní získá starou IP adresu přiřadit znovu po restartu virtuálního počítače. Vypnutí dolů na webu Azure portal nebo pomocí volání Stop-AzureRmVM automaticky způsobí uvolnění. Pokud nechcete uvolnění použít počítač Stop-AzureRmVM - StayProvisioned 
* Pokud vypnete virtuální počítač na úroveň operačního systému, získá virtuální počítač vypne a není zrušit přidělení. Ale v takovém případě účtu Azure se stále účtuje pro virtuální počítač bez ohledu na skutečnost, že se jedná o vypnutí. V takovém případě se přiřazení IP adresy do zastaveného virtuálního počítače zůstane beze změny. Vypnutí virtuálního počítače z v rámci nenutí automaticky vyhovělo.

Dokonce i pro scénáře, mezi různými místy ve výchozím nastavení vypnutí a vyhovělo znamená, že deaktivace přiřazení IP adresy z virtuálního počítače, i v případě, že místní zásady v nastavení protokolu DHCP se liší. 

* Výjimka je jeden přiřadí statické IP adresy k síťovému rozhraní, jako to popisuje téma [tady][virtual-networks-reserved-private-ip].
* V takovém případě zůstane pevnou IP adresu, tak dlouho, dokud se neodstraní síťové rozhraní.

> [!IMPORTANT]
> Aby bylo možné zachovat celé nasazení jednoduché a snadno spravovatelné, zrušte zaškrtnutí doporučujeme nastavit partnerství DBMS HA nebo konfigurace zotavení po Havárii v rámci Azure způsobem, který je funkční překlad mezi různé virtuální počítače používané virtuálními počítači.
> 
> 

## <a name="deployment-of-host-monitoring"></a>Nasazení hostitele monitorování
SAP vyžaduje produktivní použití aplikace SAP v Azure Virtual Machines, možnost využívat hostitele data monitorování z fyzických hostitelů, které jsou spuštěné virtuální počítače Azure. Vyžaduje se konkrétní úroveň opravy agenta hostitele SAP, která umožňuje tato funkce v SAPOSCOL a Agent hostitele SAP. Úroveň oprav přesně je popsána v Poznámka SAP [1409604].

Podrobné informace o nasazení komponenty, které doručují data hostitele SAPOSCOL a Agent hostitele SAP a správa životního cyklu těchto komponent, najdete [Průvodce nasazením][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Podrobnosti k systému Microsoft SQL Server
### <a name="sql-server-iaas"></a>SQL Server IaaS
Zahájení práce s Microsoft Azure, můžete snadno migrovat stávající aplikací SQL serveru založeným na platformě Windows Server na virtuálních počítačích Azure. SQL Server na virtuálním počítači umožňuje snížit celkové náklady na vlastnictví nasazení, správu a údržbu podnikových kontejnerových nástrojů aplikací snadno migrace těchto aplikací do Microsoft Azure. SQL Server ve virtuálním počítači Azure správci a vývojáři stále pomocí stejné vývoj a nástroje pro správu, které jsou k dispozici místně. 

> [!IMPORTANT]
> Jsme nejsou diskuze o Microsoft Azure SQL Database, což je platforma jako nabídka služeb platformy Microsoft Azure. Informace v tomto dokumentu jsou o spuštění produktu SQL Server, jako je znám pro místní nasazení v Azure Virtual Machines, využití infrastruktury jako schopnosti služeb Azure. Možnosti databáze a funkcí mezi tyto dvě nabídky se liší a by neměl promíchala mezi sebou. Viz také: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Doporučujeme zkontrolovat [to] [ virtual-machines-sql-server-infrastructure-services] dokumentaci, než budete pokračovat.

V následujících částech jsou kusů částí dokumentace v části výše uvedený odkaz agregovat a uvedené. Specifika týkající se SAP jsou také uvedené a některé koncepty, které jsou popsány podrobněji. Důrazně ale doporučujeme projít dokumentaci nad první před čtením dokumentaci specifickou pro SQL Server.

V IaaS konkrétní informace, které byste měli znát před pokračováním je některé SQL Server:

* **SLA pro Virtual Machines**: je smlouva SLA pro virtuální počítače běžící v Azure, který najdete tady: <https://azure.microsoft.com/support/legal/sla/>  
* **Podpora verzí SQL**: pro zákazníky, SAP, podporujeme SQL Server 2008 R2 a vyšší ve virtuálních počítačích Microsoft Azure. Starší verze nepodporují. Kontrola této Obecné [prohlášení o odborné pomoci](https://support.microsoft.com/kb/956893) další podrobnosti. Všimněte si, že obecně systému SQL Server 2008 společnost Microsoft podporuje také. Ale protože významné funkce pro SAP, která byla zavedena v systému SQL Server 2008 R2, SQL Server 2008 R2 je minimální verzi pro SAP. Mějte na paměti, že systém SQL Server 2012 a 2014 je teď rozšířené pomocí hlubší integrace do scénáře IaaS (např. zálohování přímo s úložištěm Azure). Proto Omezujeme tento dokument a SQL Server 2012, 2014 s jeho nejnovější úroveň opravy pro Azure.
* **Podpora funkce SQL**: funkce nejvíce systému SQL Server jsou podporovány na virtuálních počítačích Microsoft Azure s několika výjimkami. **SQL Server převzetí služeb při selhání pomocí sdílených disků není podporováno vytváření clusterů**.  Distribuovaných technologií, jako je zrcadlení databáze skupiny dostupnosti AlwaysOn, replikace, přesouvání protokolu služby Service Broker jsou podporovány a v rámci jedné oblasti Azure. Technologie AlwaysOn systému SQL Server také je podporována mezi různými oblastmi Azure, jak je popsáno zde: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Zkontrolujte [prohlášení o odborné pomoci](https://support.microsoft.com/kb/956893) další podrobnosti. Příklad toho, jak nasadit konfigurace funkce AlwaysOn se zobrazí v [to] [ virtual-machines-workload-template-sql-alwayson] článku. Také, podívejte se na osvědčené postupy popsané [zde][virtual-machines-sql-server-infrastructure-services] 
* **Výkon SQL**: jsme si jisti, že Microsoft Azure hostované virtuální počítače fungují velmi dobře ve srovnání s další nabídky veřejného cloudu virtualizace, ale jednotlivé výsledky se mohou lišit. Podívejte se na [to] [ virtual-machines-sql-server-performance-best-practices] článku.
* **Pomocí Imagí z webu Azure Marketplace**: nejrychlejší způsob, jak nasadit nový virtuální počítač Microsoft Azure je chcete použít některou image z Azure Marketplace. Existují Image v Tržišti Azure Marketplace, které obsahují systém SQL Server. Obrázky, kde je již nainstalován SQL Server nelze použít pro aplikace SAP NetWeaver okamžitě. Důvodem je, že výchozí kolace systému SQL Server je nainstalovaný v rámci těchto imagí a nejsou vyžadované systémy SAP NetWeaver kolace. Chcete-li použít tyto bitové kopie, zkontrolujte kroky popsané v kapitole [pomocí bitové kopie systému SQL Server z webu Microsoft Azure Marketplace][dbms-guide-5.6]. 
* Podívejte se na [podrobnosti o cenách](https://azure.microsoft.com/pricing/) Další informace. [Průvodce licencováním SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) a [SQL Server 2014 – Průvodce licencemi](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) jsou také důležité prostředků.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Pokyny pro konfigurace systému SQL Server pro instalace SAP související systému SQL Server na virtuálních počítačích Azure
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Doporučení pro virtuální počítač nebo virtuální pevný disk strukturu pro nasazení SAP související SQL serveru
V souladu s obecný popis spustitelné soubory systému SQL Server nachází nebo nainstalovat do systémové jednotce disk s operačním systémem Virtuálního počítače (jednotka C:\).  Většina systémových databází systému SQL Server nejsou obvykle využít na vysoké úrovni podle úloh SAP NetWeaver. Proto systémové databáze systému SQL Server (hlavní server, databázi msdb a modelu) mohou zůstat na jednotce C:\. Výjimky může být databáze tempdb, který v případě některých SAP ERP a všechny sady funkcí BW mohou vyžadovat větší objem dat nebo vstupně-výstupní operace svazek, který nelze umístit do původního virtuálního počítače. Pro tyto systémy by měl provést následující kroky:

* Přesune soubory dat primární databáze tempdb na stejné logické jednotce jako soubory primární data z databáze SAP.
* Přidáte všechny soubory databáze tempdb další data pro každý z jiné logické jednotky obsahující soubor dat o uživatelskou databázi SAP.
* Přidáte soubor protokolu databáze tempdb na logickou jednotku, která obsahuje soubor protokolu databáze uživatele.
* **Výhradně pro typy virtuálních počítačů, které používají místní jednotky SSD** na výpočetním uzlu tempdb dat a protokolů může být soubory umístěny na jednotku D:\. Nicméně může se doporučuje použít několik souborů dat databáze tempdb. Mějte na paměti, že svazky D:\ jednotky se liší podle typu virtuálního počítače.

Tyto konfigurace povolit tempdb využívat více místa, než je schopna poskytovat systémové jednotce. Aby bylo možné určit velikost správnou databázi tempdb, jeden můžete zkontrolovat velikosti databáze tempdb na existující systémy, které běží místně. Kromě toho by taková konfigurace povolit čísla vstupně-výstupních operací s databází tempdb, který nemůže být součástí systémové jednotky. Systémy, které jsou spuštěné místně znovu, lze použít pro sledování úloh vstupně-výstupních operací s databází tempdb tak, aby odvozujete čísla vstupně-výstupních operací, které byste měli vidět pro vaši databázi tempdb.

Konfigurace virtuálního počítače, který spouští SQL Server s databázi SAP a umístění dat databáze tempdb a databázi tempdb souboru protokolu na jednotku D:\ vypadat nějak takto:

![Odkaz na konfiguraci virtuálního počítače Azure IaaS pro SAP][dbms-guide-figure-300]

Mějte na paměti, že jednotce D:\ má různé velikosti závisí na typu virtuálního počítače. Závisí na požadavek na velikost databáze tempdb je může vynutit pár tempdb dat a souborů protokolu se SAP databáze dat a souborů protokolu v případech, kdy jednotku D:\ je příliš malá.

#### <a name="formatting-the-disks"></a>Formátování disků
Pro SQL Server systému souborů NTFS blokovat velikosti disků, obsahující data systému SQL Server a soubory protokolů by měl být 64 kB. Není nutné k formátování na jednotku D:\. Tato jednotka se dodává předem formátovaný.

Pokud chcete mít jistotu, že obnovení nebo vytváření databází není inicializace datové soubory eliminací obsah souborů, jeden měli ujistit, že uživatel kontext, ve kterém je spuštěna služba SQL Server v určitá oprávnění. Uživatelé ve skupině Windows správce obvykle mají tato oprávnění. Pokud službu systému SQL Server je spuštěn v kontextu uživatele Windows uživatel není správcem, budete muset přiřadit tomuto uživateli uživatelské právo **provádět úlohy údržby svazku**.  Podrobnosti najdete v tomto článku znalostní báze Microsoft: <https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>Dopad komprese databáze
V konfiguraci, kde šířku pásma vstupně-výstupní operace se může stát omezujícím faktorem každá míra, což snižuje vstupně-výstupních operací vám může pomoci roztáhnout úlohy, jež možné spouštět ve scénáři IaaS, jako je Azure. Proto pokud ještě není Hotovo, použití komprese stránku serveru SQL se doporučuje od SAPU a Microsoftu před nahráním existující databázi SAP do Azure.

Doporučení a provádí kompresi databáze před nahráním do Azure dostane mimo dva důvody:

* Množství dat k odeslání je menší.
* Doba trvání spuštění komprese je kratší, za předpokladu, že jeden můžete používat silnější hardware s více procesory nebo větší šířku pásma vstupně-výstupních operací nebo méně vstupně-výstupní latence místní.
* Menší velikosti databáze může vést k nižší náklady pro přidělení disku

Komprese databáze funguje i na Azure Virtual Machines, stejně jako místní. Další podrobnosti o tom, aby se komprimoval existující databáze SQL serveru SAP najdete tady: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 – ukládání databázových souborů přímo v Azure Blob Storage
SQL Server 2014 se otevře možnost ukládat databázové soubory přímo na Store objektů Blob v Azure bez "obálku" virtuálního pevného disku je obcházet. Zejména s pomocí úložiště Azure úrovně Standard nebo menší typy virtuálních počítačů to umožňuje scénáře, ve kterém lze překonat limity vstupně-výstupních operací, které by se vynucuje omezený počet disků, které je možné připojit na několik menších typy virtuálních počítačů. Tento postup funguje pro uživatelské databáze, ale ne pro systémové databáze systému SQL Server. Funguje i pro data a soubory protokolu serveru SQL Server. Pokud chcete nasadit databázi SQL serveru SAP díky tomu namísto "zabalení" do virtuálních pevných disků, mějte na následující:

* Účet úložiště používá musí být ve stejné oblasti Azure, protože ten, který se používá k nasazení virtuálního počítače SQL Server běží v.
* Důležité informace týkající se distribuce virtuální pevné disky výše uvedených přes různé účty úložiště Azure použít pro tuto metodu i nasazení. Znamená, že počet vstupně-výstupní operace proti limity účtu úložiště Azure.

[comment]: <> (MSSedusch TODO, ale tímto dojde k použití šířky pásma sítě a nikoli úložiště šířky pásma, že?)

Tady jsou uvedené podrobnosti o tomto typu nasazení: <https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

Pokud chcete uložit datové soubory SQL Server přímo na Azure Premium Storage, musíte mít minimální verzi opravy SQL Server 2014, které jsou zde uvedeny: <https://support.microsoft.com/kb/3063054>. Ukládání souborů dat serveru SQL Server na Azure Standard Storage pracuje s vydaná verze systému SQL Server 2014. Ale velmi stejné opravy obsahovat další řadu oprav, které spolehlivost přímého použití úložiště objektů Blob v Azure pro datové soubory SQL serveru a zálohování. Proto doporučujeme použít tyto opravy obecně.

### <a name="sql-server-2014-buffer-pool-extension"></a>Rozšíření fondu vyrovnávací paměti SQL serveru 2014
SQL Server 2014 zavedli novou funkci, která se nazývá rozšíření fondu vyrovnávací paměti. Tato funkce rozšíření fondu vyrovnávací paměti systému SQL Server, který je uložen v paměti s druhou úroveň mezipaměti, která je založená na místní jednotky SSD serveru nebo na virtuálním počítači. To umožňuje zajistit větší pracovní sadu dat "v paměti". Ve srovnání s přístupem k Azure Standard Storage přístup do rozšíření fondu vyrovnávací paměti, který je uložený na místní SSD disků virtuálního počítače Azure je řada faktorů rychleji.  Využití místní jednotce D:\ typy virtuálních počítačů, které mají vynikající IOPS a propustnost a proto může být velmi rozumný způsob, jak snížit zatížení vstupně-výstupních operací využívající službu Azure Storage a výrazně zkrátit doba odezvy dotazů. To platí zejména v případě, že bez použití služby Premium Storage. V případě služby Premium Storage a využití mezipaměti pro čtení úrovně Premium Azure na výpočetním uzlu se podle doporučení pro datové soubory neočekávají žádné významné rozdíly. Důvodem je, že oba mezipamětí (rozšíření fondu vyrovnávací paměti SQL serveru a mezipaměť pro čtení úrovně Premium Storage) budou používat místní disky výpočetních uzlech.
Další podrobnosti o této funkci najdete v této dokumentaci: <https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Informace o zálohování nebo obnovení pro SQL Server
Při nasazování serveru SQL Server do Azure backup metodologie musí zkontrolovat. I když v systému není systémem produktivní, SAP databázi hostované na serveru SQL Server je nutné zálohovat pravidelně. Vzhledem k tomu, že Azure Storage uchovává tři Image, je nyní méně důležité možnosti kompenzační selhání úložiště z hlediska zálohování. Priorita důvod k zachování správný plán zálohování a obnovení je další, která se může kompenzovat chyby logické nebo ruční tím, že poskytuje bod v možnosti v době obnovení. Cílem je tak buď pomocí zálohování a obnovení databáze zpět do určitého bodu v čase nebo pomocí zálohování v Azure pro jiný systém tak, že zkopírujete existující databázi. Například může přenesete z konfigurace SAP úrovně 2 a instalační program systému 3vrstvé stejného systému tak, že obnovíte zálohu.

Existují tři různé způsoby, jak zálohování SQL serveru do služby Azure Storage:

1. SQL Server 2012 CU4 a vyšší můžete nativně zálohování databází na adresu URL. To je podrobně popsán v blogu [nové funkce v vylepšení zálohování a obnovení SQL serveru 2014 – část 5 –](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx). Naleznete v kapitole [SQL Server 2012 SP1 CU4 nebo novější][dbms-guide-5.5.1].
2. Verze systému SQL Server před SQL 2012 CU4 můžete použít funkce přesměrování pro zálohování virtuálního pevného disku a v podstatě přechod datového proudu pro zápis k umístění služby Azure Storage, který byl nakonfigurován. Naleznete v kapitole [SQL Server 2012 SP1 CU3 a dřívějších verzích][dbms-guide-5.5.2].
3. Finální metoda je k provádění běžných zálohování serveru SQL k příkazu disk na disk zařízení. To je stejný jako vzor místní nasazení a není podrobně popsány v tomto dokumentu.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 nebo novější
Tato funkce umožňuje přímo zálohování do Azure BLOB storage. Bez této metody musíte zálohovat na jiné disky, které bude využívat kapacitu disků a IOPS. Spočívá v podstatě to:

 ![Zálohování SQL serveru 2012 do Microsoft Azure Storage BLOB][dbms-guide-figure-400]

V tomto případě výhodou je, že jeden nemusí trávit disky k ukládání záloh systému SQL Server na. Je proto nutné menšího počtu disků, které jsou přidělené a celou šířku pásma disku, vstupně-výstupních operací se dá použít pro data a soubory protokolu. Všimněte si, že maximální velikost zálohy je omezená maximální velikostí 1 TB, jak je uvedeno v části **omezení** v tomto článku: <https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations>. Pokud velikost zálohování bez ohledu na použít kompresi zálohování serveru SQL Server by být delší než 1 TB velikosti, funkce popsané v kapitole [SQL Server 2012 SP1 CU3 a dřívějších verzích] [ dbms-guide-5.5.2] v tomto dokumentu musí být použít.

[Související dokumentaci](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure) popisující obnovení databáze ze zálohy na Azure Store objektů Blob doporučujeme nechcete obnovit přímo z úložiště objektů BLOB v Azure, pokud je záloha > 25 GB. Doporučení v tomto článku je založeno na důležité informace o výkonu a ne z důvodu omezení funkčnosti. Proto mohou použít různé podmínky pro případ od případu.

Najdete v dokumentaci k fungování tohoto typu zálohování nastavení a využít [to](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) kurz

Příklad pořadí kroků může číst [tady](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Automatizace zálohování, je nejvyšší důležitosti, abyste měli jistotu, že jsou objekty BLOB pro každou zálohu pojmenované jinak. V opačném případě jsou přepsány a je porušený řetězec obnovení.

V pořadí nechcete kombinovat nahoru věci mezi tři různé typy zálohování je vhodné vytvořit různé kontejnery pod účet úložiště používané pro zálohy. Kontejnery může být virtuální počítač pouze nebo z virtuálního počítače a typ zálohování. Schéma může vypadat:

 ![Pomocí zálohování SQL serveru 2012 na Microsoft Azure Storage BLOB - různé kontejnery v rámci samostatný účet úložiště][dbms-guide-figure-500]

V předchozím příkladu by nebyla provedena zálohy do stejného účtu úložiště, ve které jsou nasazené virtuální počítače. Bude nový účet úložiště pro zálohy. V rámci účtů úložiště by různé kontejnery vytvořené pomocí matici typ zálohování a název virtuálního počítače. Tato segmentace usnadňuje správa záloh různých virtuálních počítačů.

Objekty BLOB, jeden přímo zapíše zálohy, nejsou přidání počet data na disky virtuálního počítače. Proto může jeden maximalizovat maximální počet datových disků připojit z konkrétní skladovou Položku virtuálního počítače pro data a transakce souboru protokolu a spustit zálohování pro kontejner úložiště. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 a dřívějších verzích
Prvním krokem je nutné provést, abyste dosáhli zálohování přímo s úložištěm Azure může být soubor MSI, který je propojený s [to](https://www.microsoft.com/download/details.aspx?id=40740) KBA článku.

Stahování x64 instalační soubor a v dokumentaci. Soubor, nainstaluje program s názvem: **zálohování systému Microsoft SQL Server pro nástroj Microsoft Azure**. Přečtěte si důkladně dokumentaci produktu.  Nástroj funguje v podstatě následujícím způsobem:

* Na straně serveru SQL, je definován umístění na disku pro zálohování SQL serveru (nepoužívejte jednotce D:\ jako umístění).
* Nástroj umožňuje definovat pravidla, které je možné použít k řízení různých typech zálohování pro různé kontejnery služby Azure Storage.
* Jakmile pravidla jsou na místě, přesměruje nástroj streamu zápisu zálohy na jeden virtuální pevné disky nebo disky pro umístění služby Azure Storage, která byla dříve definovaná.
* Nástroj opustí soubor malé velikosti několik KB na virtuálního pevného disku nebo disku, který byl definován pro SQL Server zálohování. **Tento soubor by měla zůstat na umístění úložiště, protože je třeba, aby znovu obnovit ze služby Azure Storage.**
  * Pokud jste ztratili souboru zástupné procedury (například prostřednictvím ztráty úložná média, který obsahoval soubor zástupné procedury) a jste vybrali možnost zálohování do účtu úložiště Microsoft Azure, může obnovit souboru zástupné procedury prostřednictvím služby Microsoft Azure Storage stažením z kontejneru úložiště, ve kterém byl umístěn. Soubor zástupné procedury umístíte do složky na místním počítači, ve kterém je tento nástroj konfigurovaný k detekci a nahrát do kontejneru s stejné šifrovací heslo, pokud šifrování byl použit s původní pravidlo. 

To znamená, že schéma, jak je popsáno výše pro novější verze systému SQL Server můžete umístit na místě i verze systému SQL Server, které nejsou povolení přímé adresa umístění služby Azure Storage.

Tato metoda by neměla používat s novější verze systému SQL Server, které záložní nahoru nativně nepodporují využívající službu Azure Storage. Výjimky jsou, kde jsou omezení nativního zálohování do Azure blokování nativní spuštění zálohování do Azure.

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>Další možnosti pro zálohování databází systému SQL Server
Další možnosti pro zálohování databází, je další datové disky připojit k virtuálnímu počítači, který použijete k ukládání záloh na. V takovém případě je třeba, abyste měli jistotu, že disky nejsou spuštěné úplné. Pokud je to tento případ, musíte k odpojení disků a proto do speak "" ho archivovat a nahraďte ji metodou nového prázdného disku. Pokud přejdete dolů této cesty, budete chtít ponechat tyto virtuální pevné disky v samostatných účtech úložiště Azure od těch, které virtuální pevné disky se soubory databáze.

Druhá možnost je použít velký virtuální počítač, který může mít mnoho disky připojené, například D14 s 32VHDs. Pomocí prostorů úložiště můžete vytvářet flexibilní prostředí, ve kterém může vytvoření sdílené složky, které jsou pak použít jako cíle zálohování pro různé servery DBMS.

Máte některé osvědčené postupy popsané [tady](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) také. 

#### <a name="performance-considerations-for-backupsrestores"></a>Otázky výkonu při zálohování/obnovení
Výkon zálohování a obnovení je stejně jako v nasazení na holé počítače, závisí na tom, kolik svazky může číst paralelně a propustnost tyto svazky, které je možné. Kromě toho může spotřeby procesoru používá komprese záloh přehrát významnou roli na virtuálních počítačích s až osm vlákna CPU. Proto můžete předpokládat:

* Méně počet disků používá k ukládání dat souborů, čím menší celkovou propustnost čtení.
* Čím menší že počet procesorů, vlákna ve virtuálním počítači více závažnost dopadu komprese záloh.
* Menším počtem cílů (objekty BLOB, virtuální pevné disky nebo disky), zapisovat zálohy do, menší než propustnost.
* Čím menší velikost virtuálního počítače, čím menší propustnost kvóta úložiště zápis a čtení ze služby Azure Storage. Bez ohledu na tom, jestli jsou zálohy přímo uložené na objektů Blob v Azure nebo určuje, zda jsou uloženy v virtuální pevné disky, které jsou znovu uložené v objektech BLOB Azure.

Při použití Microsoft Azure Storage BLOB jako cíl zálohování v novějších verzích, můžete jsou omezeny na určit pouze jeden cíl adresy URL pro každé konkrétní zálohování.

Ale při použití ve starších vydaných verzích "Microsoft SQL Server zálohování pro nástroj Microsoft Azure", můžete definovat více než jeden cílový soubor. S více než jeden cíl zálohování můžete škálovat a propustnost zálohování je vyšší. Výsledkem by pak více souborů také v účtu Azure Storage. Při testování, pomocí více cílů souboru můžete jednoznačně dosáhnout propustnosti můžete dosáhnout pomocí zálohování rozšíření implementované v SQL Server 2012 SP1 CU4 na. Můžete také nejsou blokovány bránou limit 1 TB, stejně jako v nativní zálohování do Azure.

Mějte však na paměti, propustnost je také závislý na umístění účtu úložiště Azure, můžete použít pro zálohování. Najděte účet úložiště v jiné oblasti než virtuální počítače jsou spuštěné v může být nápad. Například spusťte konfigurace virtuálního počítače v západní Evropě, ale umístění účtu úložiště, který používáte k zálohování proti v oblastech severní Evropa. Který jistě má dopad na propustnost zálohování a není pravděpodobně vygeneruje propustnost 150MB/s, jak to vypadá v případech, kde účet cílového úložiště a virtuální počítače běží ve stejném datacentru místní.

#### <a name="managing-backup-blobs"></a>Správa objekty BLOB zálohy
Neexistuje požadavek na správu zálohy sami. Předpokladem je, že velký počet objektů BLOB jsou vytvořeny pomocí provádí zálohy protokolu transakce časté, správa těchto objektů BLOB snadno můžete přetížit na webu Azure portal. Proto je recommendable využívat Azure storage Exploreru. Existuje několik dobré z nich k dispozici, což pomůže ke správě účtu služby Azure storage

* Microsoft Visual Studio se sadou SDK Azure nainstalovaný (<https://azure.microsoft.com/downloads/>)
* Microsoft Azure Storage Explorer (<https://azure.microsoft.com/downloads/>)
* Nástroje třetích stran

Podrobnější diskuzi o zálohování a SAP v Azure, najdete [příručce zálohování SAP](sap-hana-backup-guide.md) Další informace.

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Pomocí bitové kopie systému SQL Server z webu Microsoft Azure Marketplace
Společnost Microsoft nabízí virtuální počítače na webu Azure Marketplace, které již obsahují verze systému SQL Server. Pro SAP zákazníci, kteří požadují licence pro SQL Server a Windows může se jednat příležitost k v podstatě pokrytí potřebu licence zapínáním virtuální počítače s SQL Server již nainstalován. Chcete-li použít tyto bitové kopie pro SAP, potřeba provést následující aspekty:

* Než zkušební verze systému SQL Server získat vyšší náklady než "Pouze Windows" virtuálních počítačů nasazených z Azure Marketplace. Najdete v těchto článcích pro porovnání cen: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> a <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Můžete použít jenom verze systému SQL Server, které jsou podporovány SAP, jako je SQL Server 2012.
* Kolace instance systému SQL Server, který je nainstalovaný na virtuálních počítačích, na webu Azure Marketplace nabízí není kolace SAP NetWeaver vyžaduje instanci systému SQL Server spustit. I když s pokyny uvedené v následující části můžete změnit kolaci.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Změna kolace systému SQL Server z virtuálního počítače s Microsoft Windows a SQL serverem
Vzhledem k tomu, že Image SQL serveru na webu Azure Marketplace nejsou nastaveny pro používání kolace, který je požadován aplikací SAP NetWeaver, je potřeba změnit okamžitě po nasazení. Pro SQL Server 2012 to lze provést pomocí následujících kroků Jakmile byl nasazen virtuální počítač a správce může přihlásit k nasazenému virtuálnímu počítači:

* Jako správce otevřete okno příkazového řádku Windows.
* Změňte adresář na C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Spusťte příkaz: Setup.exe/quiet nezobrazí /ACTION = InstanceName REBUILDDATABASE = MSSQLSERVER /SQLSYSADMINACCOUNTS =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> je účet, který byl definován jako účet správce při nasazování virtuálního počítače poprvé v galerii.

Proces zabere jenom pár minut. Pokud chcete mít jistotu, zda se na krok skončila s správný výsledek, proveďte následující kroky:

* Otevřete SQL Server Management Studio.
* Otevře se okno dotazu.
* Spusťte příkaz sp_helpsort v hlavní databázi systému SQL Server.

Požadovaný výsledek by měl vypadat:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Pokud není výsledek, zastavit nasazení SAP a zjistěte, proč instalačního příkazu nefunguje podle očekávání. Nasazení aplikací SAP NetWeaver na instanci systému SQL Server s jinou neurčují SQL serveru než je uvedeno výše je **není** podporována.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server vysokou dostupnost pro SAP v Azure
Jak už bylo zmíněno dříve v tomto dokumentu, nedochází k vytvoření sdíleného úložiště, což je nezbytné pro využití nejstarší funkci vysoké dostupnosti systému SQL Server. Tato funkce by nainstalovat dvě nebo víc instancí systému SQL Server v Windows Server Failover Cluster (WSFC) pomocí sdíleného disku pro uživatelské databáze (a nakonec tempdb). Toto je metoda standardní vysoké dostupnosti dlouhou dobu, což je také podporováno SAP. Protože služba Azure nepodporuje sdílené úložiště, nelze realizovat konfigurace systému SQL Server s vysokou dostupností s konfigurací sdíleného disku clusteru. Mnoho dalších metod vysoké dostupnosti jsou však stále možné a jsou popsané v následujících částech.

#### <a name="sql-server-log-shipping"></a>Přesouvání protokolu serveru SQL Server
Jednou z metod vysoké dostupnosti (HA) je přesouvání protokolu SQL serveru. Pokud virtuální počítače součástí konfigurace HA překlad názvů funguje, neexistuje žádný problém a nastavení v Azure se neliší od všechna nastavení, která se provádí na místě. Není doporučeno spoléhají na překlad IP pouze. S ohledem na nastavení přesouvání protokolu a zásady kolem přesouvání protokolu, v této dokumentaci:

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

Abyste dosáhli vysoké dostupnosti, třeba nasazení virtuálních počítačů, které jsou v rámci těchto přesouvání protokolu konfigurace nacházet ve stejné sadě dostupnosti Azure.

#### <a name="database-mirroring"></a>Zrcadlení databáze
Zrcadlení databáze, protože SAP (viz poznámka SAP [965908]) spoléhá na definování partnerem převzetí služeb při selhání v připojovacím řetězci SAP. Případech, mezi různými místy předpokládáme, že dva virtuální počítače ve stejné doméně a že instance systému SQL Server kontextu dva uživatele jsou spuštěny v uživatel domény a máte dostatečná oprávnění v dvě instance systému SQL Server používané. Proto se mezi typické místní instalace/konfigurace liší nastavení zrcadlení databáze v Azure.

Od čistě cloudových nasazení nejjednodušší způsob je má jiné nastavení domény v Azure a mají tyto virtuální počítače DBMS (a ideálně vyhrazených virtuálních počítačích SAP) v rámci jedné domény.

Pokud domény není možné, jeden také používat certifikáty pro databázi zrcadlení koncové body podle postupu popsaného tady: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Kurz k nastavení zrcadlení databáze v Azure najdete tady: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>Always On systému SQL Server
Always On se podporuje pro SAP v místním (viz poznámka SAP [1772688]), je možné použít v kombinaci s řešením SAP v Azure. Fakt, že zatím nejste schopni vytvořit sdílené disky v Azure, neznamená, že jeden nejde vytvořit konfiguraci služby vždy na Windows Server Failover Cluster (WSFC) mezi různé virtuální počítače. Pouze znamená, že nemáte možnost používat sdílený disk jako kvora v konfiguraci clusteru. Proto můžete sestavit konfiguraci služby vždy na služby WSFC v Azure a možnost kvora typ, který využívá sdílený disk. Prostředí Azure těchto virtuálních počítačů nasazených v by měl vyřešit, virtuální počítače podle názvu a virtuální počítače by měl být ve stejné doméně. To platí pro pouze Azure a nasazení mezi různými místy. Existují některé speciální aspekty nasazování SQL serveru naslouchací proces skupiny dostupnosti (nesmí být zaměňovány dostupnosti Azure) od Azure v tomto okamžiku není povoleno pro vytvoření objektu AD/DNS je možné místní. Proto některé jinou instalační kroky jsou nezbytné k překonání konkrétní chování Azure.

Některé důležité informace o použití naslouchací proces skupiny dostupnosti jsou:

* Použití naslouchací proces skupiny dostupnosti je možné pouze v systémech Windows Server 2012 nebo vyšší jako hostovaný operační systém virtuálního počítače. Systém Windows Server 2012 je třeba Ujistěte se, že se použije tato oprava: <https://support.microsoft.com/kb/2854082> 
* Pro Windows Server 2008 R2, tato oprava neexistuje a Always On by bylo potřeba použít stejným způsobem jako zrcadlení databáze tak, že zadáte partnerem převzetí služeb při selhání v řetězci připojení (provádí přes SAP default.pfl parametr databází/mss/server – viz poznámka SAP [965908]).
* Při použití naslouchací proces skupiny dostupnosti, virtuální počítače databáze musí být připojen k vyhrazené nástroje pro vyrovnávání zatížení. Překlad názvů v čistě cloudových nasazení by buď vyžadují systému SAP (aplikační servery, DBMS server a server (A) SCS) všechny virtuální počítače jsou ve stejné virtuální síti nebo by vyžadovaly z aplikační vrstvě SAP údržby etc\host souboru v pořadí Chcete-li získat názvy virtuálních počítačů přeložit virtuální počítače SQL serveru. Předejdete tak, že Azure je přiřazení nové IP adresy v případech, kdy oba virtuální počítače jsou mimochodem vypnutí, jeden by měl přiřadit statické IP adresy pro síťová rozhraní z těchto virtuálních počítačů v konfiguraci Always On (definování statická IP adresa je popsané v [to] [ virtual-networks-reserved-private-ip] článek)

[comment]: <> (Staré blogy)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Existují speciální kroků potřebných při vytváření konfigurace clusteru služby WSFC, kdy cluster potřebuje speciální IP adresu přiřadit, protože Azure s její aktuální funkce by přiřadit název clusteru stejné IP adresy jako uzel clusteru je vytvořen na. To znamená, že ruční krok je nutné provést přiřadit jinou IP adresu clusteru.
* Naslouchací proces skupiny dostupnosti bude v Azure vytvořit pomocí koncových bodů protokolu TCP/IP, které jsou přiřazeny k virtuálním počítačům s primární a sekundární repliky skupiny dostupnosti.
* Může být potřeba zabezpečené tyto koncové body pomocí seznamů ACL.

[comment]: <> (Blog staré TODO)
[comment]: <> (Podrobný postup a životní potřeby instalace konfigurace funkce AlwaysOn v Azure jsou nejlépe došlo při procházení k dispozici návod [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (Předkonfigurované instalace technologie AlwaysOn prostřednictvím Galerie Azure <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (Vytváří se naslouchací proces skupiny dostupnosti se nejlépe popisuje kurz [this][virtual-machines-windows-classic-ps-sql-int-listener])
[comment]: <> (Zabezpečení koncových bodů sítě pomocí seznamů ACL je podrobně popsaný nejlépe tady:)
[comment]: <> (* <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

Je možné nasadit v různých oblastech Azure i SQL Server vždy skupinu dostupnosti. Tato funkce využívá připojení k Azure VNet-to-Vnet ([podrobnosti][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Blog staré TODO)
[comment]: <> (Nastavení skupin dostupnosti AlwaysOn systému SQL Server v takové situaci je popsaný tady: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Přehled služby vysoké dostupnosti pro SQL Server v Azure
Vzhledem k tomu, že Azure Storage je chránit obsah, je jeden méně důvod se trvat na hot standby image. To znamená, že váš scénář vysoké dostupnosti je potřeba pouze ochranu proti následujících případech:

* Nedostupnost virtuálního počítače jako celek z důvodu údržby v clusteru serveru v Azure nebo z jiných důvodů
* Softwarových problémů v instanci systému SQL Server
* Ochrana před ruční chyb, ve kterém získá data odstraněna a je potřeba obnovení bodu v čase

Prohlížení odpovídající technologií jeden můžete tvrdí, že prvních dvou případů se dá pokrýt komponentami zrcadlení nebo Always On, že třetí případ pouze se dá pokrýt komponentami přesouvání protokolu.

Je potřeba vyrovnávat složitější nastavení vždy v porovnání s zrcadlení databáze, s výhodami Always On. Například je možné uvést tyto výhody:

* Čitelné sekundární repliky.
* Zálohy ze sekundárních replik.
* Lepší škálovatelnost.
* Více než jeden sekundární repliky.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Obecné systém SQL Server pro SAP v Azure souhrn
Existuje mnoho doporučení v tomto průvodci a doporučujeme že si přečíst je více než jednou před plánování nasazení vašeho řešení Azure. Obecně platí ale musíte splnit nejvyšší deset obecné DBMS na Azure konkrétní body:

[comment]: <> (2.3 vyšší výkon než nabízí? Než jeden virtuální pevný disk?)
1. Použijte nejnovější vydání systému DBMS, jako je SQL Server 2014, která má nejvíce výhod v Azure. SQL serveru je SQL Server 2012 SP1 CU4, který zahrnuje funkci zálohování mimo Azure Storage. V kombinaci s řešením SAP doporučujeme však použít minimálně SQL Server 2014 SP1 CU1 nebo SQL Server 2012 SP2 a nejnovější CU.
2. Pečlivě naplánujte prostředí systému SAP v Azure pro data souboru rozložení a omezení Azure:
   * Není má příliš mnoho disků, ale dostatek Ujistěte se, že můžete oslovit požadované vstupně-výstupních operací.
   * Pokud nechcete používat službu Managed Disks, mějte na paměti, že vstupně-výstupních operací se také omezené na jeden účet úložiště Azure a účty úložiště jsou omezené v každém předplatném Azure ([podrobnosti][azure-subscription-service-limits]). 
   * Pouze stripe mezi disky. Pokud potřebujete, abyste dosáhli vyšší propustnosti.
3. Software nemá nikdy instalovat nebo umístěte všechny soubory, které vyžadují trvalost na jednotku D:\, jako je mimo trvalé a nic na této jednotce dojde ke ztrátě při restartování Windows.
4. Nepoužívejte ukládání do mezipaměti na disku pro úložiště Azure úrovně Standard.
5. Nepoužívejte Azure účty geograficky replikovaného úložiště.  Místně redundantní použijte pro úlohy DBMS.
6. Pomocí řešení od dodavatele DBMS HA/DR k replikaci dat z databáze.
7. Vždy použít překlad, nespoléhejte na IP adresách.
8. Používejte nejvyšší komprese databáze, je to možné. Což je stránka komprese pro SQL Server.
9. Buďte opatrní, pomocí Image SQL serveru z Azure Marketplace. Pokud používáte SQL Server, jeden, musíte změnit kolací instance před instalací jakékoli systému SAP NetWeaver na něm.
10. Instalace a konfigurace monitorování SAP hostitele pro Azure, jak je popsáno v [Průvodce nasazením][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>Podrobnosti k SAP ASE na Windows
Zahájení práce s Microsoft Azure, můžete snadno migrovat stávající aplikace SAP ASE k Azure Virtual Machines. SAP ASE v rámci virtuálního počítače umožňuje snížit celkové náklady na vlastnictví nasazení, správu a údržbu podnikových kontejnerových nástrojů aplikací snadno migrace těchto aplikací do Microsoft Azure. Se SAP ASE ve virtuální počítač Azure správci a vývojáři můžou využívat stejné vývoj a nástroje pro správu, které jsou k dispozici místně.

Existuje smlouvu SLA pro Azure Virtual Machines, které najdete tady: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Jsme si jisti, že Microsoft Azure hostované virtuální počítače s rozhodovacím i jiné nabídky veřejného cloudu virtualizace, ale jednotlivé výsledky se mohou lišit. Certifikace SAPU velikosti protokoly SAP množství různých SAP použít skladové položky virtuálních počítačů je k dispozici v samostatných Poznámka SAP [1928533].

Příkazy a doporučení týkající se použití Azure Storage, nasazení virtuálních počítačů o SAP nebo SAP monitorování platí pro nasazení SAP ASE ve spojení s aplikacemi SAP, jak je uvedeno v průběhu prvních čtyř kapitol tohoto dokumentu.

### <a name="sap-ase-version-support"></a>Podpora SAP ASE verzí
SAP aktuálně podporuje verze 16.0 pro použití se službou SAP Business Suite produkty SAP ASE. Všechny aktualizace pro server zpráv SAP ASE nebo JDBC a ovladače ODBC pro použití s produkty SAP Business Suite jsou k dispozici pouze prostřednictvím SAP Service Marketplace v: <https://support.sap.com/swdc>.

Jako u zařízení v místním nestahovat aktualizací serveru SAP ASE nebo ovladače JDBC a ODBC přímo z webů Sybase. Podrobné informace o opravy, které jsou podporovány pro použití se službou SAP Business Suite produkty místních a následující poznámky SAP na virtuálních počítačích Azure najdete v článku:

* [1590719]
* [1973241]

Obecné informace o spouštění SAP Business Suite v SAP ASE najdete v [oznámení změny stavu](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Pokyny pro konfigurace SAP ASE pro SAP související SAP ASE instalace na virtuálních počítačích Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura nasazení SAP ASE
V souladu s obecný popis SAP ASE spustitelné soubory umístěné nebo nainstalovat do systémové jednotce disk s operačním systémem Virtuálního počítače (jednotka c:\). Obvykle většina databází systému a nástroje SAP ASE nepoužívá složitých úloh SAP NetWeaver. Proto systému a nástroje pro databáze (master, model, saptools, sybmgmtdb, sybsystemdb) mohou zůstat na jednotce C:\. 

Výjimky může být dočasné databáze obsahující všechny pracovní tabulky a dočasné tabulky vytvořené SAP ASE, který v případě některých SAP ERP a všechny sady funkcí BW mohou vyžadovat větší objem dat nebo vstupně-výstupní operace svazek, který nelze umístit do operačního systému původního virtuálního počítače disk (jednotku c:\).

V závislosti na verzi SAPInst/SWPM použitý k instalaci v systému může obsahovat databáze:

* Jeden tempdb SAP ASE, který se vytvoří při instalaci SAP ASE
* SAP ASE tempdb vytvořené instalace SAP ASE a další saptempdb vytvořené SAP instalačního programu
* SAP ASE tempdb vytvořené instalace SAP ASE a další databáze tempdb, který byl vytvořen ručně (například následující poznámka SAP [1752266]) podle požadavků konkrétního tempdb ERP/BW

V případě všech úloh BW nebo konkrétní ERP má smysl, pokud jde o výkonu, aby zařízení tempdb kromě vytvořené databáze tempdb (SWPM nebo ručně) na jiném disku než C:\. Pokud neexistuje žádné další databáze tempdb, doporučujeme vytvořit jeden (Poznámka SAP [1752266]).

Pro tyto systémy by měl pro kromě vytvořenou databázi tempdb provést následující kroky:

* Pozice prvního zařízení tempdb jako první zařízení databázi SAP
* Přidat zařízení databáze tempdb na všech virtuálních pevných discích obsahující zařízení databázi SAP

Tato konfigurace umožňuje tempdb buď využívat více místa, než je schopna poskytovat systémové jednotce. Jako referenci jeden můžete zkontrolovat zařízení velikosti databáze tempdb na existující systémy, které běží místně. Nebo by taková konfigurace povolit čísla vstupně-výstupních operací s databází tempdb, který nemůže být součástí systémové jednotky. Znovu systémy, které jsou spuštěné místně slouží k monitorování vstupně-výstupní úlohy proti databázi tempdb.

Nikdy žádným zařízením SAP ASE umístěny na jednotce D:\ virtuálního počítače. To platí také pro databázi tempdb, i když jsou jenom dočasné objekty v databázi tempdb.

#### <a name="impact-of-database-compression"></a>Dopad komprese databáze
V konfiguraci, kde šířku pásma vstupně-výstupní operace se může stát omezujícím faktorem každá míra, což snižuje vstupně-výstupních operací vám může pomoci roztáhnout úlohy, jež možné spouštět ve scénáři IaaS, jako je Azure. Doporučujeme proto se ujistěte, že SAP ASE komprese před nahráním existující databázi SAP do Azure.

Doporučení a provádí kompresi před nahráním do Azure, pokud není již implementována dostane z několika důvodů:

* Množství dat k odeslání do Azure je nižší
* Doba trvání spuštění komprese je kratší, za předpokladu, že jeden můžete používat silnější hardware s více procesory nebo větší šířku pásma vstupně-výstupních operací nebo méně vstupně-výstupní latence v místním
* Menší velikosti databáze může vést k nižší náklady pro přidělení disku

Komprese dat a obchodní fungují v hostovaných ve službě Azure Virtual Machines, stejně jako místní virtuální počítač. Další podrobnosti o tom, jak zkontrolovat, jestli komprese je již v použít stávající databázi SAP ASE, zkontrolujte poznámky SAP [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Pomocí DBACockpit k monitorování instancí databáze
Pro systémy SAP, které používají jako databáze platformy SAP ASE, je dostupné jako windows embedded prohlížeče v transakci DBACockpit nebo jako Webdynpro DBACockpit. Ale je k dispozici v implementaci Webdynpro DBACockpit pouze úplné funkce pro monitorování a správu databáze.

Jako s místními systémy několik kroků je potřeba povolit všechny funkce SAP NetWeaver používané Webdynpro provádění DBACockpit. Postupujte podle Poznámka SAP [1245200] povolit spotřebu webdynpros a generovat ty povinné. Podle pokynů ve výše uvedené poznámky, můžete také nakonfigurovat Správce internetové komunikace (icm) spolu s porty, které chcete používat pro připojení http a https. Výchozí nastavení pro protokol http vypadá takto:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a odkazy generované v transakci DBACockpit vypadá nějak takto:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V závislosti na jestli a jakým způsobem je virtuální počítač Azure, který je hostitelem systému SAP připojené přes site-to-site, více webů nebo ExpressRoute (mezi různými místy nasazení) potřebujete, abyste měli jistotu, že ICM používá plně kvalifikovaný název hostitele, který lze převést na počítači kde Pokoušíte se otevřít DBACockpit z. Viz poznámka SAP [773830] pochopit, jak ICM Určuje plně kvalifikovaný název v závislosti na profil parametry a nastavení parametru icm/host_name_full explicitně v případě potřeby.

Pokud jste nasadili virtuální počítač v čistě cloudové scénáře bez připojení mezi místními sítěmi mezi místními a Azure, musíte definovat veřejnou IP adresu a domainlabel. Formát veřejný název DNS virtuálního počítače vypadá takto:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Další podrobnosti týkající se názvu DNS najdete [tady][virtual-machines-azurerm-versus-azuresm].

Nastavení parametru profilu SAP icm/host_name_full jako název DNS virtuálního počítače Azure na odkaz může vypadat podobně jako:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V tomto případě musíte nezapomeňte:

* Přidat příchozí pravidlo na skupinu zabezpečení sítě na webu Azure Portal pro portů TCP/IP, které slouží ke komunikaci s ICM
* Přidat příchozí pravidla konfigurace brány Windows Firewall pro porty TCP/IP používaný ke komunikaci s ICM

Pro automatické importu všech oprav, které jsou k dispozici doporučuje se pravidelně použít kolekci opravy Poznámka SAP pro vaši verzi SAP:

* [1558958]
* [1619967]
* [1882376]

Další informace o DBA řídicí panel pro SAP ASE najdete v následující poznámky SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Důležité informace o zálohování nebo obnovení pro SAP ASE
Při nasazování služby ASE SAP do Azure, musí být přezkoumána zálohování metodologii. I když v systému není systémem produktivní, SAP databázi hostované na SAP ASE je nutné zálohovat pravidelně. Vzhledem k tomu, že Azure Storage uchovává tři Image, je nyní méně důležité možnosti kompenzační selhání úložiště z hlediska zálohování. Hlavním důvodem pro udržení správného plánu zálohování a obnovení je další, která se může kompenzovat chyby logické nebo ruční tím, že poskytuje bod v možnosti v době obnovení. Cílem je tak buď pomocí zálohování a obnovení databáze zpět do určitého bodu v čase nebo pomocí zálohování v Azure pro jiný systém tak, že zkopírujete existující databázi. Například může přenesete z konfigurace SAP úrovně 2 a instalační program systému 3vrstvé stejného systému tak, že obnovíte zálohu.

Zálohování a obnovení databáze v Azure funguje stejným způsobem jako v místním prostředí. Naleznete v poznámkách:

* [1588316]
* [1585981]

Podrobné informace o vytváření konfigurace výpisu stavu systému a plánování zálohování. V závislosti na vaší strategie a požadavky, které můžete konfigurovat databáze a protokolu výpisy stavu systému na disku na jeden z disků existující, nebo přidejte další disk pro zálohování. Ke snížení nebezpečí ztrátě dat v případě chyby, se doporučuje používat disk, kde je umístěné zařízení žádná databáze.

Kromě dat a obchodní komprese SAP ASE také nabízí komprese záloh. Tak, aby obsadily méně místa s výpisy databáze a protokolu se doporučuje použít komprese záloh. Další informace viz poznámka SAP [1588316]. Komprese zálohy je také důležité ke snížení objemu dat, které se mají přenést, pokud plánujete stažení zálohy nebo virtuálních pevných discích obsahující zálohování výpisů paměti z virtuálního počítače Azure do místní.

Nepoužívejte jako databázi nebo protokolu s výpisem paměti cílové jednotce D:\.

#### <a name="performance-considerations-for-backupsrestores"></a>Otázky výkonu při zálohování/obnovení
Výkon zálohování a obnovení je stejně jako v nasazení na holé počítače, závisí na tom, kolik svazky může číst paralelně a propustnost tyto svazky, které je možné. Kromě toho může spotřeby procesoru používá komprese záloh přehrát významnou roli na virtuálních počítačích s až osm vlákna CPU. Proto můžete předpokládat jeden:

* Méně počet disků používá k ukládání zařízení databáze, čím menší celkovou propustnost čtení
* Čím menší že počet procesorů, vlákna ve virtuálním počítači více závažnost dopadu komprese záloh
* Menším počtem cílů (Stripe adresáře, discích) k zápisu zálohy do, menší než propustnost

Chcete-li zvýšit počet cílů k zápisu do, že existují dvě možnosti, které je možné použít/kombinovat podle svých potřeb:

* Prokládání cílový svazek přes více připojené disky za účelem zlepšení propustnosti vstupně-výstupních operací na tomto svazku prokládané
* Vytvoření konfigurace výpisu stavu systému na úrovni SAP ASE, který používá více než jeden cílový adresář pro zápis s výpisem paměti pro

Prokládání svazku přes více připojené disky byl popsáno dříve v tomto průvodci. Další informace o používání více adresářů v konfiguraci s výpisem paměti SAP ASE, naleznete v dokumentaci na sp_config_dump uloženou proceduru, který slouží k vytvoření konfigurace výpisu stavu systému na [informační Sybase středisko](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Zotavení po havárii pomocí virtuálních počítačů Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikace dat se serverem replikace SAP Sybase
Díky SAP ASE SAP Sybase replikace serveru (SRS) poskytuje záložním pohotovostním režimu řešení asynchronně přenášet databázové transakce na vzdálené umístění. 

Instalace a používání služby SRS funguje i funkčně v hostované služby virtuálního počítače Azure, stejně jako místní virtuální počítač.

SAP ASE HADR nevyžaduje Azure Internal Load Balancer a nemá žádné závislosti na úrovni clusterů operačního systému a funguje na Windows Azure a virtuální počítače s Linuxem. Najdete podrobnosti o SAP ASE HADR [SAP ASE HADR uživatelské příručky](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Podrobnosti k SAP ASE v Linuxu
Zahájení práce s Microsoft Azure, můžete snadno migrovat stávající aplikace SAP ASE k Azure Virtual Machines. SAP ASE v rámci virtuálního počítače umožňuje snížit celkové náklady na vlastnictví nasazení, správu a údržbu podnikových kontejnerových nástrojů aplikací snadno migrace těchto aplikací do Microsoft Azure. Se SAP ASE ve virtuální počítač Azure správci a vývojáři můžou využívat stejné vývoj a nástroje pro správu, které jsou k dispozici místně.

Pro nasazení virtuálních počítačů Azure je důležité znát oficiální smlouvy SLA, které najdete tady: <https://azure.microsoft.com/support/legal/sla>

Informace o určení velikosti SAP a seznam skladových položek virtuálních počítačů s certifikací SAP je k dispozici v Poznámka SAP [1928533]. Další velikosti dokumentů pro Azure Virtual machines najdete tady SAP <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> a zde <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Příkazy a doporučení týkající se použití Azure Storage, nasazení virtuálních počítačů o SAP nebo SAP monitorování platí pro nasazení SAP ASE ve spojení s aplikacemi SAP, jak je uvedeno v průběhu prvních čtyř kapitol tohoto dokumentu.

Následující dva poznámky SAP zahrnout obecné informace o službě ASE v Linuxu a služba ASE v cloudu:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Podpora SAP ASE verzí
SAP aktuálně podporuje verze 16.0 pro použití se službou SAP Business Suite produkty SAP ASE. Všechny aktualizace pro server zpráv SAP ASE nebo JDBC a ovladače ODBC pro použití s produkty SAP Business Suite jsou k dispozici pouze prostřednictvím SAP Service Marketplace v: <https://support.sap.com/swdc>.

Jako u zařízení v místním nestahovat aktualizací serveru SAP ASE nebo ovladače JDBC a ODBC přímo z webů Sybase. Podrobné informace o opravy, které jsou podporovány pro použití se službou SAP Business Suite produkty místních a následující poznámky SAP na virtuálních počítačích Azure najdete v článku:

* [1590719]
* [1973241]

Obecné informace o spouštění SAP Business Suite v SAP ASE najdete v [oznámení změny stavu](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Pokyny pro konfigurace SAP ASE pro SAP související SAP ASE instalace na virtuálních počítačích Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura nasazení SAP ASE
V souladu s obecný popis SAP ASE spustitelné soubory by měly být umístěné nebo nainstalovat do systému souborů kořenového virtuálního počítače (/sybase). Většina databází systému a nástroje SAP ASE nejsou obvykle využít náročné úlohy SAP NetWeaver. Proto může zůstat systému a nástroje pro databáze (master, model, saptools, sybmgmtdb, sybsystemdb) v systému souborů kořenové také. 

Výjimky může být dočasné databáze obsahující všechny pracovní tabulky a dočasné tabulky vytvořené SAP ASE, který v případě některých SAP ERP a všechny sady funkcí BW mohou vyžadovat větší objem dat nebo vstupně-výstupních operací, svazek, který se nevejde do operačního systému původního virtuálního počítače disk.

V závislosti na verzi SAPInst/SWPM použitý k instalaci v systému může obsahovat databáze:

* Jeden tempdb SAP ASE, který se vytvoří při instalaci SAP ASE
* SAP ASE tempdb vytvořené instalace SAP ASE a další saptempdb vytvořené SAP instalačního programu
* SAP ASE tempdb vytvořené instalace SAP ASE a další databáze tempdb, který byl vytvořen ručně (například následující poznámka SAP [1752266]) podle požadavků konkrétního tempdb ERP/BW

V případě všech úloh BW nebo konkrétní ERP má smysl, ve vztahu výkonu, udržovat zařízení tempdb kromě vytvořené databáze tempdb (SWPM nebo ručně) v systému samostatného souboru, který může být reprezentován jednoho Azure datového disku nebo spannin RAID v Linuxu g více datové disky Azure. Pokud neexistuje žádné další databáze tempdb, doporučujeme vytvořit jeden (Poznámka SAP [1752266]).

Pro tyto systémy by měl pro kromě vytvořenou databázi tempdb provést následující kroky:

* Přesun první adresář databáze tempdb na první systém souborů databáze SAP
* Přidat adresáře databáze tempdb na všech discích obsahující systému souborů databáze SAP

Tato konfigurace umožňuje tempdb buď využívat více místa, než je schopna poskytovat systémové jednotce. Jako referenci jeden můžete zkontrolovat velikosti adresář databáze tempdb na existující systémy, které běží místně. Nebo by taková konfigurace povolit čísla vstupně-výstupních operací s databází tempdb, který nemůže být součástí systémové jednotky. Znovu systémy, které jsou spuštěné místně slouží k monitorování vstupně-výstupní úlohy proti databázi tempdb.

Nikdy umístěny všechny adresáře SAP ASE na /mnt nebo /mnt/resource virtuálního počítače. To platí také pro databázi tempdb, i v případě, že objekty v databázi tempdb jsou pouze dočasné, protože /mnt nebo /mnt/resource je výchozí virtuální počítač Azure temp prostor, který není trvalý. Další podrobnosti o dočasného prostoru virtuálních počítačů Azure najdete v [v tomto článku][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Dopad komprese databáze
V konfiguraci, kde šířku pásma vstupně-výstupní operace se může stát omezujícím faktorem každá míra, což snižuje vstupně-výstupních operací vám může pomoci roztáhnout úlohy, jež možné spouštět ve scénáři IaaS, jako je Azure. Doporučujeme proto se ujistěte, že SAP ASE komprese před nahráním existující databázi SAP do Azure.

Doporučení a provádí kompresi před nahráním do Azure, pokud není již implementována dostane z několika důvodů:

* Množství dat k odeslání do Azure je nižší
* Doba trvání spuštění komprese je kratší, za předpokladu, že jeden můžete používat silnější hardware s více procesory nebo větší šířku pásma vstupně-výstupních operací nebo méně vstupně-výstupní latence v místním
* Menší velikosti databáze může vést k nižší náklady pro přidělení disku

Komprese dat a obchodní fungují v hostovaných ve službě Azure Virtual Machines, stejně jako místní virtuální počítač. Další podrobnosti o tom, jak zkontrolovat, jestli komprese je již v použít stávající databázi SAP ASE, zkontrolujte poznámky SAP [1750510]. Další informace týkající se komprese databáze, viz poznámka SAP [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Pomocí DBACockpit k monitorování instancí databáze
Pro systémy SAP, které používají jako databáze platformy SAP ASE, je dostupné jako windows embedded prohlížeče v transakci DBACockpit nebo jako Webdynpro DBACockpit. Ale je k dispozici v implementaci Webdynpro DBACockpit pouze úplné funkce pro monitorování a správu databáze.

Jako s místními systémy několik kroků je potřeba povolit všechny funkce SAP NetWeaver používané Webdynpro provádění DBACockpit. Postupujte podle Poznámka SAP [1245200] povolit spotřebu webdynpros a generovat ty povinné. Podle pokynů ve výše uvedené poznámky, můžete také nakonfigurovat Správce internetové komunikace (icm) spolu s porty, které chcete používat pro připojení http a https. Výchozí nastavení pro protokol http vypadá takto:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a odkazy generované v transakci DBACockpit bude vypadat nějak takto:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V závislosti na jestli a jakým způsobem je virtuální počítač Azure, který je hostitelem systému SAP připojené přes site-to-site, více webů nebo ExpressRoute (mezi různými místy nasazení) potřebujete, abyste měli jistotu, že ICM používá plně kvalifikovaný název hostitele, který lze převést na počítači kde Pokoušíte se otevřít DBACockpit z. Viz poznámka SAP [773830] pochopit, jak ICM Určuje plně kvalifikovaný název v závislosti na profil parametry a nastavení parametru icm/host_name_full explicitně v případě potřeby.

Pokud jste nasadili virtuální počítač v čistě cloudové scénáře bez připojení mezi místními sítěmi mezi místními a Azure, musíte definovat veřejnou IP adresu a domainlabel. Formát veřejný název DNS virtuálního počítače vypadá takto:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Další podrobnosti týkající se názvu DNS najdete [tady][virtual-machines-azurerm-versus-azuresm].

Nastavení parametru profilu SAP icm/host_name_full jako název DNS virtuálního počítače Azure na odkaz může vypadat podobně jako:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V tomto případě musíte nezapomeňte:

* Přidat příchozí pravidlo na skupinu zabezpečení sítě na webu Azure Portal pro portů TCP/IP, které slouží ke komunikaci s ICM
* Přidat příchozí pravidla konfigurace brány Windows Firewall pro porty TCP/IP používaný ke komunikaci s ICM

Pro automatické importu všech oprav, které jsou k dispozici doporučuje se pravidelně použít kolekci opravy Poznámka SAP pro vaši verzi SAP:

* [1558958]
* [1619967]
* [1882376]

Další informace o DBA řídicí panel pro SAP ASE najdete v následující poznámky SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Důležité informace o zálohování nebo obnovení pro SAP ASE
Při nasazování do Azure backup metodologie musí být přezkoumána SAP ASE. I když v systému není systémem produktivní, SAP databázi hostované na SAP ASE je nutné zálohovat pravidelně. Vzhledem k tomu, že Azure Storage uchovává tři Image, je nyní méně důležité možnosti kompenzační selhání úložiště z hlediska zálohování. Hlavním důvodem pro udržení správného plánu zálohování a obnovení je další, která se může kompenzovat chyby logické nebo ruční tím, že poskytuje bod v možnosti v době obnovení. Cílem je tak buď pomocí zálohování a obnovení databáze zpět do určitého bodu v čase nebo pomocí zálohování v Azure pro jiný systém tak, že zkopírujete existující databázi. Například může přenesete z konfigurace SAP úrovně 2 a instalační program systému 3vrstvé stejného systému tak, že obnovíte zálohu.

Zálohování a obnovení databáze v Azure funguje stejným způsobem jako v místním prostředí. Naleznete v poznámkách:

* [1588316]
* [1585981]

Podrobné informace o vytváření konfigurace výpisu stavu systému a plánování zálohování. V závislosti na vaší strategie a požadavky, které můžete konfigurovat databáze a protokolu výpisy stavu systému na disku na jeden z disků existující, nebo přidejte další disk pro zálohování. Ke snížení nebezpečí ztrátě dat v případě chyby, se doporučuje používat disk, kde se nachází žádné adresářů a souborů databáze.

Kromě dat a obchodní komprese SAP ASE také nabízí komprese záloh. Tak, aby obsadily méně místa s výpisy databáze a protokolu se doporučuje použít komprese záloh. Další informace viz poznámka SAP [1588316]. Komprese zálohy je také důležité ke snížení objemu dat, které se mají přenést, pokud plánujete stažení zálohy nebo virtuálních pevných discích obsahující zálohování výpisů paměti z virtuálního počítače Azure do místní.

Nepoužívejte /mnt dočasného prostoru virtuálních počítačů Azure nebo /mnt/resource jako cílové databázi nebo protokolu s výpisem paměti.

#### <a name="performance-considerations-for-backupsrestores"></a>Otázky výkonu při zálohování/obnovení
Výkon zálohování a obnovení je stejně jako v nasazení na holé počítače, závisí na tom, kolik svazky může číst paralelně a propustnost tyto svazky, které je možné. Kromě toho může spotřeby procesoru používá komprese záloh přehrát významnou roli na virtuálních počítačích s až osm vlákna CPU. Proto můžete předpokládat jeden:

* Méně počet disků používá k ukládání zařízení databáze, čím menší celkovou propustnost čtení
* Čím menší že počet procesorů, vlákna ve virtuálním počítači více závažnost dopadu komprese záloh
* Menším počtem cílů (Linux softwarového pole RAID, discích) k zápisu zálohy do, menší než propustnost

Chcete-li zvýšit počet cílů k zápisu do, že existují dvě možnosti, které je možné použít/kombinovat podle svých potřeb:

* Prokládání cílový svazek přes více připojené disky za účelem zlepšení propustnosti vstupně-výstupních operací na tomto svazku prokládané
* Vytvoření konfigurace výpisu stavu systému na úrovni SAP ASE, který používá více než jeden cílový adresář pro zápis s výpisem paměti pro

Prokládání svazku přes více připojené disky byl popsáno dříve v tomto průvodci. Další informace o používání více adresářů v konfiguraci s výpisem paměti SAP ASE, naleznete v dokumentaci na sp_config_dump uloženou proceduru, který slouží k vytvoření konfigurace výpisu stavu systému na [informační Sybase středisko](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Zotavení po havárii pomocí virtuálních počítačů Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikace dat se serverem replikace SAP Sybase
Díky SAP ASE SAP Sybase replikace serveru (SRS) poskytuje záložním pohotovostním režimu řešení asynchronně přenášet databázové transakce na vzdálené umístění. 

Instalace a používání služby SRS funguje i funkčně v hostované služby virtuálního počítače Azure, stejně jako místní virtuální počítač.

Služba ASE HADR prostřednictvím serveru SAP replikace není podporována v daném okamžiku. Mohou být otestovány a vydání pro platformy Microsoft Azure v budoucnu.

## <a name="specifics-to-oracle-database-on-windows"></a>Podrobnosti k databázi Oracle na Windows
Oracle software je podporován společností Oracle běží v Microsoft Windows Hyper-V a Azure. Podrobnosti o obecné podpoře Windows Hyper-V a Azure zkontrolujte: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Následující obecné podpory se také podporuje konkrétní scénář využití databází Oracle aplikací SAP. Podrobnosti jsou uvedeny v této části dokumentu.

### <a name="oracle-version-support"></a>Podpora verze Oracle
Verze Oracle a odpovídající verze operačního systému, které jsou podporovány pro spuštění SAP v Oracle v Azure Virtual Machines najdete v poznámce SAP [2039619].

Obecné informace o spouštění SAP Business Suite v Oracle najdete v 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny pro konfigurace Oracle pro instalace SAP na virtuálních počítačích Azure
#### <a name="storage-configuration"></a>Konfigurace úložiště
Pouze jednu instanci Oracle pomocí disků formátovaných systémem NTFS je podporována. Všechny soubory databáze musí být uložen v systému souborů NTFS na základě virtuálních pevných disků nebo Managed Disks. Tyto disky jsou připojené k virtuálnímu počítači Azure, jsou založeny na úložiště objektů BLOB stránky Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) nebo Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Jakýkoli druh síťové jednotky nebo vzdálených sdílených složkách, jako je Azure souborových služeb:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

jsou **není** nepodporuje pro soubory databáze Oracle!

Použití disků na základě úložiště objektů BLOB stránky Azure nebo příkazy v tomto dokumentu v kapitole Managed Disks [ukládání do mezipaměti pro virtuální počítače a datové disky] [ dbms-guide-2.1] a [Microsoft Azure Storage] [ dbms-guide-2.3] platí pro nasazení s databázi Oracle.

Jak jsme vysvětlili výše v části Obecné v dokumentu, kvóty na propustnost vstupně-výstupních operací pro disky Azure neexistuje. Přesné kvóty se v závislosti na typu virtuálního počítače používají. Seznam typů virtuálních počítačů s jejich kvóty najdete [tady (Linux)] [ virtual-machines-sizes-linux] a [tady (Windows)][virtual-machines-sizes-windows].

Identifikujte typy podporovaných virtuálních počítačů Azure, najdete v tématu Poznámka SAP [1928533].

Tak dlouho, dokud aktuální kvóta IOPS na disk splňuje požadavky, je možné uložit všechny soubory databáze na jednom jeden připojeného disku. 

Pokud potřebujete další vstupně-výstupních operací, vytvořte jedno velké logické zařízení přes více připojené disky doporučuje se použít okno fondy úložiště (pouze k dispozici ve Windows serveru 2012 a vyšší) nebo Windows prokládání pro Windows 2008 R2 (viz kapitola také [ Software RAID] [ dbms-guide-2.2] tohoto dokumentu). Tento přístup zjednodušuje režie správy spravovat místo na disku a zabraňuje úsilí ručně distribuci souborů na několik připojených disků.

#### <a name="backup--restore"></a>Backup / obnovení
Pro zálohování a obnově funkci SAP BR * nástroje pro Oracle jsou podporovány stejným způsobem jako na standardní operační systémy Windows Server a Hyper-V. Oracle Recovery Manager (RMAN) je také podporována pro zálohování na disk a obnovení z disku.

#### <a name="high-availability"></a>Vysoká dostupnost
Oracle Data Guard se podporuje pro vysokou dostupnost a zotavení po havárii. Podrobnosti najdete v [to] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentaci.

#### <a name="other"></a>Ostatní
Všechny ostatní obecné oblasti jako je monitorování dostupnosti Azure a SAP použít, jak je popsáno v první tři kapitol tohoto dokumentu pro nasazení virtuálních počítačů s Oracle Database i.

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Podrobnosti k databázi Oracle v Oracle Linuxu
Oracle software je podporován společností Oracle běží v Microsoft Windows Hyper-V a Azure. Podrobnosti o obecné podpoře Windows Hyper-V a Azure zkontrolujte: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Následující obecné podpory se také podporuje konkrétní scénář využití databází Oracle aplikací SAP. Podrobnosti jsou uvedeny v této části dokumentu.

### <a name="oracle-version-support"></a>Podpora verze Oracle
Verze Oracle a odpovídající verze operačního systému, které jsou podporovány pro spuštění SAP v Oracle v Azure Virtual Machines najdete v poznámce SAP [2039619].

Obecné informace o spouštění SAP Business Suite v Oracle najdete v 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny pro konfigurace Oracle pro instalace SAP na virtuálních počítačích Azure
#### <a name="storage-configuration"></a>Konfigurace úložiště
Je podporována pouze jedna instance Oracle pomocí ext3, ext4 a xfs ve formátu disky. Všechny soubory databáze musí být uložen na těmto systémům souborů na základě virtuálních pevných disků nebo Managed Disks. Tyto disky jsou připojené k virtuálnímu počítači Azure, jsou založeny na úložiště objektů BLOB stránky Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) nebo Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Jakýkoli druh síťové jednotky nebo vzdálených sdílených složkách, jako je Azure souborových služeb:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

jsou **není** nepodporuje pro soubory databáze Oracle!

Použití disků na základě úložiště objektů BLOB stránky Azure nebo příkazy v tomto dokumentu v kapitole Managed Disks [ukládání do mezipaměti pro virtuální počítače a datové disky] [ dbms-guide-2.1] a [Microsoft Azure Storage] [ dbms-guide-2.3] platí pro nasazení s databázi Oracle.

Jak jsme vysvětlili výše v části Obecné v dokumentu, kvóty na propustnost vstupně-výstupních operací pro disky Azure neexistuje. Přesné kvóty se v závislosti na typu virtuálního počítače používají. Seznam typů virtuálních počítačů s jejich kvóty najdete [tady (Linux)] [ virtual-machines-sizes-linux] a [tady (Windows)][virtual-machines-sizes-windows].

Identifikujte typy podporovaných virtuálních počítačů Azure, najdete v tématu Poznámka SAP [1928533]

Tak dlouho, dokud aktuální kvóta IOPS na disk splňuje požadavky, je možné uložit všechny soubory databáze na jednom jeden připojeného disku. 

Pokud potřebujete další vstupně-výstupních operací, se doporučuje použít LVM (Správce logických svazků) nebo MDADM vytvoření jedné velké logické přes několik připojených disků. Také naleznete v kapitole [softwaru diskového pole RAID] [ dbms-guide-2.2] tohoto dokumentu. Tento přístup zjednodušuje režie správy spravovat místo na disku a zabraňuje úsilí ručně distribuci souborů na několik připojených disků.

#### <a name="backup--restore"></a>Backup / obnovení
Pro zálohování a obnově funkci SAP BR * nástroje pro Oracle jsou podporovány stejným způsobem jako na holé počítače a Hyper-V. Oracle Recovery Manager (RMAN) je také podporována pro zálohování na disk a obnovení z disku.

#### <a name="high-availability"></a>Vysoká dostupnost
Oracle Data Guard se podporuje pro vysokou dostupnost a zotavení po havárii. Podrobnosti najdete v [to] [ virtual-machines-windows-classic-configure-oracle-data-guard] dokumentaci.

#### <a name="other"></a>Ostatní
Všechny ostatní obecné oblasti jako je monitorování dostupnosti Azure a SAP použít, jak je popsáno v první tři kapitol tohoto dokumentu pro nasazení virtuálních počítačů s Oracle Database i.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Podrobné informace o databázi SAP MaxDB na Windows
### <a name="sap-maxdb-version-support"></a>Podpora verzí MaxDB SAP
SAP aktuálně podporuje SAP MaxDB verze 7.9 pro použití s produktů založených na systému SAP NetWeaver v Azure. Všechny aktualizace serveru SAP MaxDB nebo JDBC a ovladače ODBC pro použití s produktů založených na systému SAP NetWeaver jsou k dispozici pouze prostřednictvím SAP Service Marketplace na <https://support.sap.com/swdc>.
Obecné informace o používání systému SAP NetWeaver na SAP MaxDB najdete v <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Podporované typy verze Microsoft Windows a virtuálních počítačů Azure pro SAP MaxDB DBMS
Podporovaná verze Microsoft Windows MaxDB SAP DBMS v Azure najdete v tématu:

* [Přehled dostupnosti SAP produktu (PAM)][sap-pam]
* Poznámka SAP [1928533]

Důrazně doporučujeme používat nejnovější verzi operačního systému Microsoft Windows, což je Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>K dispozici SAP MaxDB dokumentace
Aktualizovaný seznam SAP MaxDB dokumentaci najdete v následujících SAP Note [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny pro konfigurace MaxDB SAP pro instalace SAP na virtuálních počítačích Azure
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Konfigurace úložiště
Osvědčené postupy Azure storage pro SAP MaxDB podle obecná doporučení uvedených v kapitole [strukturu nasazení relační databázový systém][dbms-guide-2].

> [!IMPORTANT]
> Stejně jako ostatní databáze má SAP MaxDB také dat a souborů protokolu. V terminologii SAP MaxDB je správný výraz "svazek" (ne "file"). Existují třeba SAP MaxDB datových svazků a svazky s protokoly. Nezaměňujte je s svazků disku operačního systému. 
> 
> 

Stručně řečeno budete muset:

* Pokud používáte účty Azure Storage, nastavte účet úložiště Azure, který obsahuje SAP MaxDB data a svazky protokolů (například soubory) na **místní redundantní úložiště (LRS)** podle zadání v kapitole [Microsoft Azure Storage][dbms-guide-2.3].
* Samostatné cesty vstupně-výstupních operací pro SAP MaxDB datové svazky (tj. soubory) v cestě vstupně-výstupních operací pro svazky s protokoly (například soubory). To znamená, že SAP MaxDB datové svazky (tj. soubory) bylo nutné instalovat na jedné logické jednotky a svazky s protokoly SAP MaxDB (například soubory) bylo nutné instalovat na jiné logické jednotce.
* Nastavte správný typ ukládání do mezipaměti u každého disku, v závislosti na tom, jestli budete používat pro SAP MaxDB dat či protokolu svazky (tj. soubory) a ať už používáte Azure na úrovni Standard nebo Premium služby Azure Storage, jak je popsáno v kapitole [ukládání do mezipaměti pro virtuální počítače a datové disky] [dbms-guide-2.1].
* Tak dlouho, dokud aktuální kvóta IOPS na disk splňuje požadavky, je možné ukládat všechny datové svazky na jednom připojeném disku a také ukládat všechny svazky protokolu databáze na jiného jeden připojený disk.
* Pokud se vyžadují další vstupně-výstupních operací a/nebo místa, se doporučuje použít Microsoft okno fondy úložiště (pouze k dispozici v systému Microsoft Windows Server 2012 a vyšší) nebo prokládání Windows společnosti Microsoft pro Microsoft Windows 2008 R2 vytvořte jedno velké logické zařízení přes více připojené disky. Také naleznete v kapitole [softwaru diskového pole RAID] [ dbms-guide-2.2] tohoto dokumentu. Tento přístup zjednodušuje režie správy spravovat místo na disku a zabraňuje úsilí ručně distribuce souborů na několik připojených disků.
* Nejvyšší požadavky na vstupně-výstupních operací můžete použít Azure Premium Storage, která je k dispozici na virtuální počítače řady GS-series a DS-series.

![Odkaz na konfiguraci virtuálního počítače Azure IaaS pro SAP MaxDB DBMS][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Zálohování a obnovení
Při nasazování SAP MaxDB do Azure, musíte zkontrolovat zálohování metodologii. I když v systému není systémem produktivní, SAP databázi hostované na SAP MaxDB nutné zálohovat pravidelně. Od služby Azure Storage uchovává tři Image, je nyní méně důležité z hlediska ochranu systému proti selhání úložiště a další důležité provozní databázi nebo správu selhání zálohování. Hlavním důvodem pro zajištění řádného zálohování a plánu obnovení je tak, aby se může kompenzovat chyby logické nebo ruční tím, že poskytuje funkce pro obnovení bodu v čase. Cílem je tak buď pomocí zálohování a obnovení databáze do určitého bodu v čase nebo pomocí záloh v Azure pro jiný systém tak, že zkopírujete existující databázi. Například může přenesete z konfigurace SAP úrovně 2 a instalační program systému 3vrstvé stejného systému tak, že obnovíte zálohu.

Zálohování a obnovení databáze v Azure funguje stejným způsobem jako pro místní systémy, takže můžete použít standardní nástroje SAP MaxDB zálohování a obnovení, které jsou popsány v jednom z dokumenty SAP MaxDB dokumentaci uvedené v Poznámka SAP [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Otázky výkonu při zálohování a obnovení
Výkon zálohování a obnovení je stejně jako v nasazení na holé počítače, závisí na tom, kolik svazky lze číst v paralelních aplikací a propustnosti tyto svazky. Kromě toho můžete spotřeby procesoru používá komprese záloh přehrát významnou roli na virtuálních počítačích s až osm vlákna CPU. Proto můžete předpokládat jeden:

* Méně počet disků používá k ukládání zařízení databáze, nižší celkové propustnost čtení
* Čím menší že počet procesorů, vlákna ve virtuálním počítači více závažnost dopadu komprese záloh
* Menším počtem cílů (Stripe adresáře, discích) k zápisu zálohy do nižší propustnost

Pokud chcete zvýšit počet cílů pro zápis do, existují dvě možnosti, které používáte, pravděpodobně v kombinaci, v závislosti na potřebách:

* Vyhrazení oddělených svazků pro zálohování
* Prokládání cílový svazek přes více připojené disky za účelem zlepšení propustnosti vstupně-výstupních operací na tomto svazku prokládané disku
* S zařízení samostatný vyhrazený logického disku:
  * SAP MaxDB záložní svazky (tj. soubory)
  * SAP MaxDB datové svazky (tj. soubory)
  * SAP svazky s protokoly MaxDB (například soubory)

Prokládání svazku přes více připojené disky popsané dříve v kapitole [softwaru diskového pole RAID] [ dbms-guide-2.2] tohoto dokumentu. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Ostatní
Všechny ostatní obecné oblasti jako je například monitorování dostupnosti Azure a SAP také použít, jak je popsáno v první tři kapitol tohoto dokumentu pro nasazení virtuálních počítačů s databází SAP MaxDB.
Další nastavení specifická pro SAP MaxDB je transparentní pro virtuální počítače Azure a jsou popsané v různých dokumenty, které jsou uvedené v Poznámka SAP [767598] a v těchto poznámkách:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Podrobné informace o SAP liveCache na Windows
### <a name="sap-livecache-version-support"></a>SAP liveCache podpora verzí
Minimální verze SAP liveCache podporované ve službě Azure Virtual Machines je **25 SP 10.0 LC/LCAPPS SAP** včetně **liveCache 7.9.08.31** a **LCA sestavení 25**, kterou jsme vydali pro **EhP 2 pro SAP SCM 7.0** a vyšší.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Podporované typy verze Microsoft Windows a virtuálních počítačů Azure pro SAP liveCache DBMS
Podporovaná verze Microsoft Windows liveCache SAP v Azure najdete v tématu:

* [Přehled dostupnosti SAP produktu (PAM)][sap-pam]
* Poznámka SAP [1928533]

Důrazně doporučujeme používat nejnovější verzi operačního systému Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache pokyny ke konfiguraci pro instalace SAP na virtuálních počítačích Azure
#### <a name="recommended-azure-vm-types"></a>Doporučené typy virtuálních počítačů Azure
Jak SAP liveCache je aplikace, která provádí výpočty velký, velikost a rychlost paměti RAM a procesoru má hlavní vliv na výkon SAPU liveCache. 

Pro SAP podporuje typy virtuálních počítačů Azure (Poznámka SAP [1928533]), všechny virtuální prostředkům procesoru přiděleným k virtuálnímu počítači se zálohují na vyhrazených prostředcích fyzického procesoru hypervisoru. Žádné předimenzování (a tedy žádné soutěže pro prostředky procesoru) se provádí.

Podobně pro SAP podporuje všechny typy s instancí virtuálních počítačů Azure, paměť virtuálního počítače je 100 % mapované na fyzické paměti – například předimenzování (útoky over-pass-the závazek), se nepoužívá.

Z pohledu důrazně doporučujeme používat nový typ řady D-series nebo virtuálního počítače Azure řady DS-series (v kombinaci s Azure Storage úrovně Premium), mají 60 % rychlejšími procesory než řada A-series. Pro nejvyšší zatížení paměti RAM a využití procesoru můžete použít G-series a GS-series (v kombinaci s Azure Storage úrovně Premium) virtuálních počítačů s nejnovější Intel?? Xeon?? řady E5 v3, které mají dvakrát paměti a čtyři vícekrát úložiště jednotky SSD (Solid-State Drive) D/DS-series.

#### <a name="storage-configuration"></a>Konfigurace úložiště
Jako SAP liveCache je založena na technologii SAP MaxDB, Azure storage osvědčených postupů doporučení uvedené výše pro SAP MaxDB v kapitole [konfiguraci úložiště] [ dbms-guide-8.4.1] platí také pro SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Vyhrazený virtuální počítač Azure pro liveCache
Stejně jako SAP liveCache intenzivně využívá výpočetní výkon, produktivitu využití důrazně doporučujeme k nasazení na vyhrazený virtuální počítač Azure. 

![Vyhrazená pro zajištění liveCache pro případ produktivní použití virtuálních počítačů Azure][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Zálohování a obnovení
zálohování a obnovení, včetně důležité informace o výkonu, jsou popsány již v příslušné kapitolách SAP MaxDB [zálohování a obnovení] [ dbms-guide-8.4.2] a [otázky výkonu při zálohování a obnovit][dbms-guide-8.4.3]. 

#### <a name="other"></a>Ostatní
Další obecné oblasti už jsou popsány v příslušné MaxDB SAP [to] [ dbms-guide-8.4.4] kapitoly. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Podrobné informace o obsahu serveru SAP na Windows
Server obsahu SAP je součástí samostatné, na serveru k ukládání obsahu, jako jsou elektronických dokumentů v různých formátech. Server obsahu SAP poskytuje vývoje technologie a má být použité mezi aplikacemi pro všechny aplikace SAP. Je nainstalovaný v samostatném systému. Typické obsah je školicích materiálů a dokumentace ke službě z datového skladu znalostní báze nebo technické výkresy pocházející z mySAP PLM systém správy dokumentů. 

### <a name="sap-content-server-version-support"></a>Podpora verze obsahu serveru SAP
SAP v současné době podporuje:

* **Server obsahu SAP** s verzí **6.50 (a vyšší)**
* **SAP MaxDB verze 7.9**
* **Microsoft IIS (Internet Information Server) verze 8.0 (a vyšší)**

Důrazně doporučujeme používat nejnovější verzi obsahu serveru SAP, které jsou v době psaní tohoto dokumentu je **6.50 SP4**a nejnovější verzi **Microsoft IIS 8.5**. 

Zkontrolujte podporované verze obsahu serveru SAP a Microsoft IIS [SAP produktu dostupnost matice (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Podporované typy Microsoft Windows a virtuálních počítačů Azure pro SAP serveru obsahu
Podporovaná verze Windows serveru obsahu SAP v Azure, najdete v tématu:

* [Přehled dostupnosti SAP produktu (PAM)][sap-pam]
* Poznámka SAP [1928533]

Důrazně doporučujeme používat nejnovější verzi systému Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny ke konfiguraci serveru obsahu SAP pro instalace SAP na virtuálních počítačích Azure
#### <a name="storage-configuration"></a>Konfigurace úložiště
Při konfiguraci serveru SAP obsahu k ukládání souborů v databázi SAP MaxDB, všechny služby Azure storage osvědčené postupy pro doporučení pro SAP MaxDB podle kapitoly [konfiguraci úložiště] [ dbms-guide-8.4.1] je taky platná pro scénář obsahu serveru SAP. 

Při konfiguraci serveru SAP obsahu k ukládání souborů v systému souborů, doporučujeme použít vyhrazený logické jednotky. Použití prostorů úložiště systému Windows umožňuje také zvýšit velikost logického disku a propustnost vstupně-výstupních operací, jak je popsáno v kapitole [softwaru diskového pole RAID][dbms-guide-2.2]. 

#### <a name="sap-content-server-location"></a>Umístění obsahu serveru SAP
Server SAP obsahu musí být nasazená ve stejné oblasti Azure a virtuální síť Azure, ve kterém je nasazená systému SAP. Můžete se rozhodnout, jestli chcete nasadit komponenty serveru obsahu SAP na vyhrazený virtuální počítač Azure nebo na jednom virtuálním počítači, ve kterém je spuštěný systém SAP libovolně. 

![Vyhrazených virtuálních počítačích Azure k obsahu serveru SAP][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>Umístění mezipaměti serveru SAP
Server SAP mezipaměti je dalších serverových součástí a zajistit tak přístup k místně (v mezipaměti) dokumenty. Server SAP mezipaměti ukládá do mezipaměti dokumenty obsahu serveru SAP. Toto je optimalizace síťový provoz, pokud se dokumenty muset načíst více než jednou z různých míst. Obecně platí, že Server SAP mezipaměti musí být fyzicky blízko klienta, který má přístup k mezipaměti serveru SAP. 

Tady máte dvě možnosti:

1. **Klient je back-end systém SAP** Pokud systém SAP back-end je nakonfigurovaná pro přístup k obsahu serveru SAP, SAP systému je klient. Jako systém SAP a SAP serveru obsahu jsou nasazené ve stejné oblasti Azure, ve stejném datacentru Azure, jsou fyzicky blízko u sebe. Proto není nutné mít vyhrazený Server mezipaměti SAP. Klienti SAP uživatelského rozhraní (SAP GUI nebo webový prohlížeč) přímo přistupovat do systému SAP a systém SAP načte dokumenty ze serveru obsahu SAP.
2. **Klient je v místním webovém prohlížeči** The Server obsahu SAP dá přistupovat přímo ve webovém prohlížeči. V tomto případě je webový prohlížeč v místním klientovi obsahu serveru SAP. Místním datovým centrem a datacentra Azure jsou umístěny v různých fyzických umístění (ideálně blízko u sebe). Vašeho místního datového centra je připojení k Azure přes Azure VPN typu Site-to-Site nebo ExpressRoute. Přestože obě možnosti nabízí zabezpečené připojení k síti VPN do Azure, připojení sítě site-to-site se nebude poskytovat SLA síťové šířky pásma a latencí mezi místním datovým centrem a datového centra Azure. Ke zrychlení přístupu k dokumentům, proveďte jednu z následujících akcí:
   1. Nainstalovat místní Server SAP mezipaměti, Zavřít v místním webovém prohlížeči (možnost [to] [ dbms-guide-900-sap-cache-server-on-premises] obrázek)
   2. Konfigurace Azure ExpressRoute, které nabízí nízkou latenci a vysokorychlostní vyhrazené síťové připojení mezi místním datovým centrem a datového centra Azure.

![Možnost instalace místním serverem SAP mezipaměti][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Backup / obnovení
Při konfiguraci serveru SAP obsahu k ukládání souborů v databázi SAP MaxDB, zálohování a obnovení postup a výkonové požadavky jsou již je popsáno v kapitole SAP MaxDB [zálohování a obnovení] [ dbms-guide-8.4.2]a kapitoly [otázky výkonu při zálohování a obnovení][dbms-guide-8.4.3]. 

Při konfiguraci serveru SAP obsahu k ukládání souborů v systému souborů, jednou z možností je provést ruční zálohování a obnovení struktury celý soubor kde jsou umístěny v dokumentech. Podobně jako SAP MaxDB zálohování a obnovení, se doporučuje mít vyhrazený svazek pro zálohování účel. 

#### <a name="other"></a>Ostatní
Další nastavení SAP obsahu specifickou pro Server je transparentní pro virtuální počítače Azure a jsou popsané v různých dokumenty a SAP poznámky:

* <https://service.sap.com/contentserver> 
* Poznámka SAP [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Podrobné informace o k IBM DB2 LUW na Windows
S Microsoft Azure můžete snadno migrovat stávající aplikace SAP systémem IBM DB2 pro Linux, UNIX a Windows (LUW) na virtuálních počítačích Azure. S řešením SAP v IBM DB2 pro LUW správci a vývojáři můžou využívat stejné vývoj a nástroje pro správu, které jsou k dispozici místně.
Obecné informace o spouštění SAP Business Suite v IBM DB2 pro LUW najdete v SAP komunity sítě (oznámení změny stavu) za <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Další informace a aktualizacích SAP v DB2 pro LUW v Azure, viz poznámka SAP [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 pro Linux, UNIX a podpoře verzí Windows
SAP v IBM DB2 pro LUW na služby virtuálního počítače Microsoft Azure se podporuje od verze DB2 10.5.

Informace o podporovaných produktů SAP a typy virtuálních počítačů Azure najdete Poznámka SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 pro Linux, UNIX a pokyny ke konfiguraci Windows pro instalace SAP na virtuálních počítačích Azure
#### <a name="storage-configuration"></a>Konfigurace úložiště
Všechny soubory databáze musí být uložen v systému souborů NTFS podle přímo připojených disků. Tyto disky jsou připojené k virtuálnímu počítači Azure a je založena na úložiště objektů BLOB stránky Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) nebo Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Jakýkoli druh síťové jednotky nebo vzdálených sdílených složkách, jako jsou následující služby Azure file **není** nepodporuje pro soubory databáze: 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Pokud použijete disky založené na úložiště objektů BLOB stránky Azure nebo Managed Disks, příkazy provedené v tomto dokumentu v kapitole [strukturu nasazení relační databázový systém] [ dbms-guide-2] platí také pro nasazení s IBM DB2 pro LUW Databáze. 

Jak jsme vysvětlili výše v části Obecné v dokumentu, existují kvóty na propustnost vstupně-výstupních operací pro disky. Přesné kvóty, závisí na typu virtuálního počítače používá. Seznam typů virtuálních počítačů s jejich kvóty najdete [tady (Linux)] [ virtual-machines-sizes-linux] a [tady (Windows)][virtual-machines-sizes-windows].

Aktuální kvóta IOPS na disku je dostatek, je možné ukládat všechny soubory databáze na jednom jeden připojeného disku. 

Výkon důležité informace také najdete kapitoly "Data zabezpečení a důležité informace o výkonu pro databázi adresáře" v průvodců instalací SAPU.

Alternativně můžete použít fondy úložišť systému Windows (pouze k dispozici ve Windows serveru 2012 a vyšší) nebo prokládání Windows pro Windows 2008 R2, jako je popsáno v kapitole [softwaru diskového pole RAID] [ dbms-guide-2.2] tohoto dokumentu Vytvořte jedno velké logické zařízení přes několik disků.
Pro disky obsahující cesty úložiště DB2 sapdata a saptmp adresářů je nutné zadat velikost sektoru fyzického disku 512 kB. Pokud používáte fondy úložišť systému Windows, je třeba vytvořit fondy úložiště ručně pomocí rozhraní příkazového řádku pomocí parametru `-LogicalSectorSizeDefault`. Další informace najdete v tématu <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

#### <a name="backuprestore"></a>Zálohování a obnovení
Funkce zálohování a obnovení pro IBM DB2 pro LUW se podporuje stejně jako na standardní operační systémy Windows Server a Hyper-V.

Musí se ujistěte, že máte strategie zálohování platnou databázi na místě. 

Stejně jako v nasazení na holé počítače výkon zálohování a obnovení závisí na počtu svazků lze číst souběžně a propustnost tyto svazky, které je možné. Kromě toho může spotřeby procesoru používá komprese záloh přehrát významnou roli na virtuálních počítačích s až osm vlákna CPU. Proto můžete předpokládat jeden:

* Méně počet disků používá k ukládání zařízení databáze, čím menší celkovou propustnost čtení
* Čím menší že počet procesorů, vlákna ve virtuálním počítači více závažnost dopadu komprese záloh
* Menším počtem cílů (Stripe adresáře, discích) k zápisu zálohy do nižší propustnost

Pokud chcete zvýšit počet cílů pro zápis do, může být dvě možnosti použít/kombinovat podle svých potřeb:

* Za účelem zlepšení propustnosti vstupně-výstupních operací na tomto svazku prokládané prokládání cílový svazek přes několik disků
* Použití více než jeden cílový adresář pro psaní zálohování

#### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii
Clusteru MSCS (Microsoft Server) se nepodporuje.

DB2. podporuje se vysoká dostupnost, zotavení po havárii (HADR). Pokud virtuální počítače v konfiguraci s vysokou DOSTUPNOSTÍ překlad názvů funguje, instalační program v Azure se neliší od všechna nastavení, která se provádí na místě. Není doporučeno spoléhají na překlad IP pouze.

Nepoužívejte geografické replikace pro účty úložiště, které ukládají na discích databáze. Další informace najdete v kapitole [Microsoft Azure Storage] [ dbms-guide-2.3] a kapitoly [vysokou dostupnost a zotavení po havárii pomocí virtuálních počítačů Azure] [ dbms-guide-3].

#### <a name="other"></a>Ostatní
Všechny ostatní obecné oblasti jako je monitorování dostupnosti Azure a SAP použít, jak je popsáno v první tři kapitol tohoto dokumentu pro nasazení virtuálních počítačů s IBM DB2 pro LUW také. 

Také najdete v kapitole [obecné systém SQL Server pro SAP v Azure Souhrn][dbms-guide-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Podrobné informace o k IBM DB2 LUW v Linuxu
S Microsoft Azure můžete snadno migrovat stávající aplikace SAP systémem IBM DB2 pro Linux, UNIX a Windows (LUW) na virtuálních počítačích Azure. S řešením SAP v IBM DB2 pro LUW správci a vývojáři můžou využívat stejné vývoj a nástroje pro správu, které jsou k dispozici místně. Obecné informace o spouštění SAP Business Suite v IBM DB2 pro LUW najdete v SAP komunity sítě (oznámení změny stavu) za <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Další informace a aktualizacích SAP v DB2 pro LUW v Azure, viz poznámka SAP [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 pro Linux, UNIX a podpoře verzí Windows
SAP v IBM DB2 pro LUW na služby virtuálního počítače Microsoft Azure se podporuje od verze DB2 10.5.

Informace o podporovaných produktů SAP a typy virtuálních počítačů Azure najdete Poznámka SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 pro Linux, UNIX a pokyny ke konfiguraci Windows pro instalace SAP na virtuálních počítačích Azure
#### <a name="storage-configuration"></a>Konfigurace úložiště
Všechny soubory databáze musí být uložen v systému souborů podle přímo připojených disků. Tyto disky jsou připojené k virtuálnímu počítači Azure a je založena na úložiště objektů BLOB stránky Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) nebo Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Jakýkoli druh síťové jednotky nebo vzdálených sdílených složkách, jako jsou následující služby Azure file **není** nepodporuje pro soubory databáze:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Pokud používáte disky založené na úložiště objektů BLOB stránky Azure, příkazy v tomto dokumentu v kapitole [strukturu nasazení relační databázový systém] [ dbms-guide-2] platí také pro nasazení s IBM DB2 LUW databáze.

Jak jsme vysvětlili výše v části Obecné v dokumentu, existují kvóty na propustnost vstupně-výstupních operací pro disky. Přesné kvóty, závisí na typu virtuálního počítače používá. Seznam typů virtuálních počítačů s jejich kvóty najdete [tady (Linux)] [ virtual-machines-sizes-linux] a [tady (Windows)][virtual-machines-sizes-windows].

Aktuální kvóta IOPS na disku je dostatek, je možné ukládat všechny soubory databáze na jednom jednoho disku.

Výkon důležité informace také najdete kapitoly "Data zabezpečení a důležité informace o výkonu pro databázi adresáře" v průvodců instalací SAPU.

Alternativně můžete použít LVM (Správce logických svazků) nebo MDADM jak je popsáno v kapitole [softwaru diskového pole RAID] [ dbms-guide-2.2] tohoto dokumentu vytvořte jedno velké logické zařízení přes několik disků.
Pro disky obsahující cesty úložiště DB2 sapdata a saptmp adresářů je nutné zadat velikost sektoru fyzického disku 512 kB.

#### <a name="backuprestore"></a>Zálohování a obnovení
Funkce zálohování a obnovení pro IBM DB2 pro LUW je stejným způsobem jako standardní Linux instalace v místním podporována.

Musí se ujistěte, že máte strategie zálohování platnou databázi na místě.

Stejně jako v nasazení na holé počítače výkon zálohování a obnovení závisí na počtu svazků lze číst souběžně a propustnost tyto svazky, které je možné. Kromě toho může spotřeby procesoru používá komprese záloh přehrát významnou roli na virtuálních počítačích s až osm vlákna CPU. Proto můžete předpokládat jeden:

* Méně počet disků používá k ukládání zařízení databáze, čím menší celkovou propustnost čtení
* Čím menší že počet procesorů, vlákna ve virtuálním počítači více závažnost dopadu komprese záloh
* Menším počtem cílů (Stripe adresáře, discích) k zápisu zálohy do nižší propustnost

Pokud chcete zvýšit počet cílů pro zápis do, může být dvě možnosti použít/kombinovat podle svých potřeb:

* Za účelem zlepšení propustnosti vstupně-výstupních operací na tomto svazku prokládané prokládání cílový svazek přes několik disků
* Použití více než jeden cílový adresář pro psaní zálohování

#### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii
DB2. podporuje se vysoká dostupnost, zotavení po havárii (HADR). Pokud virtuální počítače v konfiguraci s vysokou DOSTUPNOSTÍ překlad názvů funguje, instalační program v Azure se neliší od všechna nastavení, která se provádí na místě. Není doporučeno spoléhají na překlad IP pouze.

Nepoužívejte geografické replikace pro účty úložiště, které ukládají na discích databáze. Další informace najdete v kapitole [Microsoft Azure Storage] [ dbms-guide-2.3] a kapitoly [vysokou dostupnost a zotavení po havárii pomocí virtuálních počítačů Azure] [ dbms-guide-3].

#### <a name="other"></a>Ostatní
Další obecné témata zahrnují třeba monitorování dostupnosti Azure a SAP použít, jak je popsáno v první tři kapitol tohoto dokumentu pro nasazení virtuálních počítačů s IBM DB2 pro LUW také.

Také najdete v kapitole [obecné systém SQL Server pro SAP v Azure Souhrn][dbms-guide-5.8].

