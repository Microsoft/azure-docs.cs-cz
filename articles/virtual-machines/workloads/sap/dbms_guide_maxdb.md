---
title: Nasazení SAP MaxDB, liveCache a Content serveru na virtuálních počítačích Azure | Microsoft Docs
description: Nasazení SAP MaxDB, liveCache a Content serveru v Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e15b592aaa780ea80b6931c6d44fbf99e2f3a730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673065"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>Nasazení SAP MaxDB, liveCache a Content serveru na virtuálních počítačích Azure

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
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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




Tento dokument popisuje několik různých oblastí, které je potřeba vzít v úvahu při nasazování MaxDB, liveCache a serveru obsahu v Azure IaaS. Jako předběžnou podmínkou tohoto dokumentu byste měli mít přehled o dokumentech [pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md) a také další Příručky k úloze [SAP v dokumentaci k Azure](./get-started.md). 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Specifické pro nasazení SAP MaxDB ve Windows
### <a name="sap-maxdb-version-support-on-azure"></a>Podpora verze SAP MaxDB v Azure
SAP v současné době podporuje SAP MaxDB verze 7,9 nebo vyšší pro použití s produkty SAP NetWeaver v Azure. Všechny aktualizace pro SAP MaxDB Server nebo ovladače JDBC a ODBC, které se mají použít s produkty SAP NetWeaver, jsou k dispozici výhradně prostřednictvím služby SAP Service Marketplace na adrese <https://support.sap.com/swdc> .
Obecné informace o spuštění SAP NetWeaver na SAP MaxDB najdete na adrese <https://www.sap.com/community/topic/maxdb.html> .

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Podporované verze systému Microsoft Windows a typy virtuálních počítačů Azure pro SAP MaxDB DBMS
Podporované verze Microsoft Windows pro SAP MaxDB DBMS v Azure najdete v těchto tématech:

* [Tabulka dostupnosti produktů SAP (PAM)][sap-pam]
* Poznámky SAP [1928533]

Důrazně doporučujeme použít nejnovější verzi operačního systému Microsoft Windows, což je Microsoft Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Dostupná dokumentace SAP MaxDB pro MaxDB
Aktualizovaný seznam dokumentace SAP MaxDB najdete v následující dokumentaci ke SAP [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny pro konfiguraci SAP MaxDB pro instalace SAP na virtuálních počítačích Azure
#### <a name="storage-configuration"></a><a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Konfigurace úložiště
Osvědčené postupy pro Azure Storage pro SAP MaxDB se řídí obecnými doporučeními uvedenými v kapitole [Struktura úložiště virtuálního počítače pro nasazení RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).

> [!IMPORTANT]
> Podobně jako u jiných databází má SAP MaxDB také soubory dat a protokolů. V terminologii SAP MaxDB je však správný výraz "svazek" (nikoli "soubor"). K dispozici jsou například datové svazky SAP MaxDB a svazky protokolů. Nepleťte si je s diskovými svazky operačního systému. 
> 
> 

V krátkém případě musíte:

* Pokud používáte účty Azure Storage, nastavte účet úložiště Azure, který obsahuje data a svazky protokolů SAP MaxDB (soubory dat a protokolů) do **místního redundantního úložiště (LRS)** , jak je uvedeno v části [požadavky na nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md).
* Oddělte cestu v/v pro datové svazky SAP MaxDB (datové soubory) z cesty v/v pro svazky protokolů (soubory protokolu). Znamená to, že je potřeba nainstalovat datové svazky SAP MaxDB (datové soubory) na jednu logickou jednotku a svazky protokolu SAP MaxDB (soubory protokolu) na jinou logickou jednotku.
* Nastavte vhodný typ ukládání do mezipaměti pro každý disk v závislosti na tom, jestli ho používáte pro MaxDB dat nebo svazky protokolů (data a soubory protokolu SAP), a jestli používáte Azure Standard nebo Azure Premium Storage, jak je popsáno v tématu [požadavky na azure Virtual Machines DBMS nasazení pro úlohy SAP](dbms_guide_general.md).
* Pokud má aktuální kvóta IOPS na disk splňovat požadavky, je možné uložit všechny datové svazky na jednom připojeném disku a také uložit všechny svazky protokolů databáze na jiný jeden připojený disk.
* Pokud se vyžaduje více vstupně-výstupních operací a/nebo mezer, doporučuje se používat fondy úložišť Microsoft Window (dostupné jenom v Microsoft Windows Serveru 2012 a novějších) k vytvoření jednoho velkého logického zařízení přes více připojených disků. Další podrobnosti najdete v tématu [informace o nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md). Tento přístup zjednodušuje správu a umožňuje spravovat místo na disku a vyhnout se úsilí o ruční distribuci souborů mezi více připojenými disky.
* pro nasazení MaxDB se důrazně doporučuje použít Azure Premium Storage. 

![Referenční konfigurace virtuálního počítače Azure s IaaS pro SAP MaxDB DBMS](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="backup-and-restore"></a><a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Zálohování a obnovení
Při nasazování SAP MaxDB do Azure je nutné zkontrolovat metodologii zálohování. I v případě, že systém není produktivní systém, musí být databáze SAP hostovaná SAP MaxDB pravidelně zálohována. Vzhledem k tomu, že Azure Storage udržuje tři image, zálohování je teď méně důležité z hlediska ochrany vašeho systému proti selhání úložiště a důležitějším provozním nebo administrativním selháním. Hlavním důvodem pro zachování správného plánu zálohování a obnovení je, že můžete kompenzovat logické nebo ruční chyby tím, že zajistíte možnosti obnovení k určitému bodu v čase. Cílem je, aby se v tomto časovém okamžiku obnovila databáze k určitému bodu v čase nebo používala zálohy v Azure k osazení jiného systému zkopírováním existující databáze. 

Zálohování a obnovení databáze v Azure funguje stejným způsobem jako u místních systémů, takže můžete používat standardní nástroje SAP MaxDB Backup/Restore, které jsou popsané v některém z dokumentů dokumentace SAP MaxDB uvedených v tématu SAP Note [767598]. 

#### <a name="performance-considerations-for-backup-and-restore"></a><a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Požadavky na výkon pro zálohování a obnovení
Stejně jako u nasazení v holém prostředí závisí výkon zálohování a obnovení na tom, kolik svazků je možné paralelně číst a propustnost těchto svazků. Proto může jeden předpokládat:

* Čím méně je počet disků použitých k uložení databázových zařízení, tím nižší je celková propustnost čtení.
* Čím méně cílů (prokládané adresáře, disky) k zápisu zálohy do, tím nižší propustnost

Pokud chcete zvýšit počet cílů, na které se má zapisovat, existují dvě možnosti, které můžete v závislosti na vašich potřebách použít, možná v kombinaci:

* Vyhradování samostatných svazků pro zálohování
* Prokládání cílového svazku zálohování přes několik připojených disků za účelem zlepšení propustnosti IOPS na tomto svazku prokládaného disku
* Samostatná vyhrazená zařízení logického disku pro:
  * Svazky pro zálohování SAP MaxDB (tj. soubory)
  * MaxDB datové svazky SAP (tj. soubory)
  * Svazky protokolu SAP MaxDB (tj. soubory)

Prokládání svazků na více připojených discích bylo zmíněno dříve v tématu [požadavky na nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md). 

#### <a name="other-considerations"></a><a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Další důležité informace
Všechny ostatní obecné oblasti, jako jsou například skupiny dostupnosti Azure nebo monitorování SAP, platí také jak je popsáno v tématu [požadavky pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md).  pro nasazení virtuálních počítačů s databází SAP MaxDB.
Další nastavení pro SAP MaxDB jsou pro virtuální počítače Azure transparentní a jsou popsána v různých dokumentech uvedených v tématu SAP Note [767598] a v těchto poznámkách SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Specifické pro nasazení SAP liveCache ve Windows
### <a name="sap-livecache-version-support"></a>Podpora verze SAP liveCache
Minimální verze SAP liveCache podporovaná ve službě Azure Virtual Machines je **SAP LC/LCAPPS 10,0 SP 25** včetně **LiveCache 7.9.08.31** a **LCA-Build 25**, vydané pro **EHP 2 pro SAP SCM 7,0** a novější verze.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Podporované verze systému Microsoft Windows a typy virtuálních počítačů Azure pro SAP liveCache DBMS
Podporované verze Microsoft Windows pro SAP liveCache v Azure najdete v těchto tématech:

* [Tabulka dostupnosti produktů SAP (PAM)][sap-pam]
* Poznámky SAP [1928533]

Důrazně doporučujeme použít nejnovější verzi operačního systému Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny pro konfiguraci SAP liveCache pro instalace SAP na virtuálních počítačích Azure
#### <a name="recommended-azure-vm-types-for-livecache"></a>Doporučené typy virtuálních počítačů Azure pro liveCache
Jako SAP liveCache je aplikace, která provádí velké výpočty, množství a rychlost paměti RAM a CPU mají zásadní vliv na výkon SAP liveCache. 

Pro typy virtuálních počítačů Azure podporované SAP (SAP Note [1928533]) se všechny prostředky virtuálního procesoru přidělené k tomuto virtuálnímu počítači zálohují pomocí vyhrazených prostředků fyzického procesoru hypervisoru. Žádná nadvýšení (a tudíž ani žádná konkurence pro prostředky CPU) se neprovádí.

Podobně platí, že u všech typů instancí virtuálních počítačů Azure podporovaných systémem SAP je paměť virtuálního počítače 100% namapovaná na fyzické převzetí paměti (Provisioning), například v případě, že se nepoužívá.

V této perspektivě se důrazně doporučuje použít nejnovější virtuální počítače s Dv2, Dv3, Ev3 a M-Series. Volba různých typů virtuálních počítačů závisí na paměti, kterou potřebujete pro liveCache a prostředky procesoru, které potřebujete. Stejně jako u všech ostatních nasazení systému DBMS je vhodné využít Azure Premium Storage pro kritické svazky s výkonem.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Konfigurace úložiště pro liveCache v Azure
Jelikož SAP liveCache vychází z technologie SAP MaxDB, platí pro SAP liveCache všechna doporučení osvědčených postupů pro Azure Storage, která jsou uvedená pro SAP MaxDB popsaná v tomto dokumentu. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Vyhrazený virtuální počítač Azure pro liveCache scénář
Vzhledem k tomu, že SAP liveCache intenzivně využívá výpočetní výkon, se důrazně doporučuje nasadit na vyhrazený virtuální počítač Azure. 

![Vyhrazený virtuální počítač Azure pro liveCache pro případ produktivního použití](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Zálohování a obnovení pro liveCache v Azure
zálohování a obnovení, včetně požadavků na výkon, jsou již popsány v příslušných kapitolách SAP MaxDB v tomto dokumentu. 

#### <a name="other-considerations"></a>Další důležité informace
Všechny ostatní obecné oblasti jsou již popsány v příslušné kapitole SAP MaxDB. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Specifické pro nasazení serveru obsahu SAP ve Windows v Azure
Server obsahu SAP je samostatná součást založená na serveru pro ukládání obsahu, jako jsou například elektronické dokumenty v různých formátech. Server obsahu SAP poskytuje vývoj technologií a je možné ho používat pro jakékoli aplikace SAP. Instaluje se do samostatného systému. Typický obsah je školicí materiál a dokumentace ze služby Knowledge Warehouse nebo technických kreseb pocházejících ze systému správy dokumentů mySAP PLM. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Podpora verze serveru SAP Content Server pro virtuální počítače Azure
SAP aktuálně podporuje:

* **Server obsahu SAP** s verzí **6,50 (a vyšší)**
* **SAP MaxDB verze 7,9**
* **Microsoft IIS (Internet Information Server) verze 8,0 (a vyšší)**

Důrazně doporučujeme použít nejnovější verzi serveru SAP Content a nejnovější verzi **služby Microsoft IIS**. 

Podívejte se na nejnovější podporované verze serveru SAP Content a služby Microsoft IIS v tabulce [dostupnosti produktů SAP (pam)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Podporované typy virtuálních počítačů Microsoft Windows a Azure pro server obsahu SAP
Pokud chcete zjistit podporovanou verzi Windows pro server obsahu SAP v Azure, přečtěte si téma:

* [Tabulka dostupnosti produktů SAP (PAM)][sap-pam]
* Poznámky SAP [1928533]

Důrazně doporučujeme použít nejnovější verzi systému Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Pokyny pro konfiguraci serveru SAP pro instalaci SAP na virtuálních počítačích Azure
#### <a name="storage-configuration-for-content-server-in-azure"></a>Konfigurace úložiště pro server obsahu v Azure
Pokud nakonfigurujete server obsahu SAP pro ukládání souborů v databázi SAP MaxDB, platí také pro scénář serveru SAP Content Server všechna doporučení pro osvědčené postupy pro službu Azure Storage uvedená pro SAP MaxDB v tomto dokumentu. 

Pokud nakonfigurujete server obsahu SAP pro ukládání souborů v systému souborů, doporučuje se použít vyhrazenou logickou jednotku. Použití prostorů úložiště Windows vám umožní taky zvýšit propustnost logických disků a IOPS, jak je popsáno v tématu [požadavky pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md). 

#### <a name="sap-content-server-location"></a>Umístění serveru obsahu SAP
Server obsahu SAP musí být nasazený ve stejné oblasti Azure a virtuální síti Azure, kde je nasazený systém SAP. Můžete se rozhodnout, jestli chcete nasadit součásti serveru SAP Content Server na vyhrazeném virtuálním počítači Azure nebo na stejném virtuálním počítači, kde je spuštěný systém SAP. 

![Vyhrazený virtuální počítač Azure pro server obsahu SAP](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>Umístění serveru mezipaměti SAP
Server mezipaměti SAP je další komponentou založenou na serveru, která poskytuje přístup k dokumentům (v mezipaměti) místně. Server mezipaměti SAP ukládá do mezipaměti dokumenty serveru SAP Content. To umožňuje optimalizovat síťový provoz, pokud se dokumenty musí z různých umístění načíst víckrát než jednou. Obecným pravidlem je, že server mezipaměti SAP musí být fyzicky blízko klientovi, který přistupuje k serveru mezipaměti SAP. 

Tady máte dvě možnosti:

1. **Klient je back-end systém SAP** . Pokud je systém back-end SAP nakonfigurovaný pro přístup k serveru obsahu SAP, je systém SAP klientem. Jak systém SAP, tak i SAP Content Server jsou nasazené ve stejné oblasti Azure, ve stejném datovém centru Azure, jsou fyzicky blízko sebe. Proto není nutné mít vyhrazený server mezipaměti SAP. Klienti uživatelského rozhraní SAP (grafické rozhraní SAP nebo webový prohlížeč) přistupují k systému SAP přímo a systém SAP načte dokumenty ze serveru obsahu SAP.
2. **Klient je místní webový prohlížeč** . Server obsahu SAP lze nakonfigurovat tak, aby k němu měl přímý odkaz webový prohlížeč. V tomto případě je webový prohlížeč, který běží v místním prostředí, klientem serveru SAP Content. Místní datové centrum a datové centrum Azure jsou umístěné v různých fyzických umístěních (v ideálním případě blízko sebe). Vaše místní datacentrum je připojené k Azure přes Azure Site-to-Site VPN nebo ExpressRoute. I když obě možnosti nabízí zabezpečené připojení k síti VPN k Azure, síťové připojení typu Site-to-site nenabízí smlouvu SLA sítě a latenci mezi místním datacentrem a datacenterm Azure. Chcete-li zrychlit přístup k dokumentům, můžete provést jednu z následujících akcí:
   1. Místní instalace serveru mezipaměti SAP, blízko místního webového prohlížeče (možnost na obrázku níže)
   2. Nakonfigurujte Azure ExpressRoute, který nabízí vysokorychlostní síťové připojení s vysokou latencí a nízkou latencí mezi místním datacentrem a datacenterm Azure.

![Možnost instalace místního serveru mezipaměti SAP](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Zálohování a obnovení
Pokud nakonfigurujete server obsahu SAP tak, aby ukládal soubory do databáze SAP MaxDB, postupy zálohování a obnovení a požadavky na výkon jsou již popsány v kapitolách SAP MaxDB tohoto dokumentu. 

Pokud nakonfigurujete server obsahu SAP pro ukládání souborů v systému souborů, jednou z možností je provést ruční zálohování nebo obnovení celé struktury souborů, kde jsou umístěny dokumenty. Podobně jako u služby SAP MaxDB Backup/Restore se doporučuje mít vyhrazený diskový svazek pro účely zálohování. 

#### <a name="other"></a>Jiné
Další nastavení specifická pro obsah SAP serveru jsou pro virtuální počítače Azure transparentní a jsou popsána v tématu různé dokumenty a poznámky SAP:

* <https://service.sap.com/contentserver> 
* Poznámky SAP [1619726]  
