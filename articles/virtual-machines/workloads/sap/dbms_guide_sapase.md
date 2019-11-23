---
title: Nasazení SAP pomocného mechanismu pro Azure Virtual Machines DBMS pro úlohy SAP | Microsoft Docs
description: Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP
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
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51e97089b1de88ccf9f45b1a0f429abc0cfac9f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101328"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]: https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]: https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]: https://launchpad.support.sap.com/#/notes/1558958
[1585981]: https://launchpad.support.sap.com/#/notes/1585981
[1588316]: https://launchpad.support.sap.com/#/notes/1588316
[1590719]: https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]: https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]: https://launchpad.support.sap.com/#/notes/1619967
[1750510]: https://launchpad.support.sap.com/#/notes/1750510
[1752266]: https://launchpad.support.sap.com/#/notes/1752266
[1757924]: https://launchpad.support.sap.com/#/notes/1757924
[1757928]: https://launchpad.support.sap.com/#/notes/1757928
[1758182]: https://launchpad.support.sap.com/#/notes/1758182
[1758496]: https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]: https://launchpad.support.sap.com/#/notes/1814258
[1882376]: https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]: https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]: https://launchpad.support.sap.com/#/notes/1941500
[1956005]: https://launchpad.support.sap.com/#/notes/1956005
[1973241]: https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]: https://launchpad.support.sap.com/#/notes/2121797
[2134316]: https://launchpad.support.sap.com/#/notes/2134316
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
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
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



V tomto dokumentu popisuje několik různých oblastí, které je potřeba vzít v úvahu při nasazování pomocného mechanismu SAP v Azure IaaS. Jako předběžnou podmínkou tohoto dokumentu byste měli mít přehled o dokumentech [pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md) a další příručky v [dokumentaci ke službě SAP v dokumentaci k Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-to-sap-ase-on-windows"></a>Specifická pro SAP pomocného programu SAP ve Windows
Počínaje Microsoft Azure můžete stávající aplikace SAP pomocného mechanismu migrovat do Azure Virtual Machines. Protokol SAP pomocného programu na virtuálním počítači Azure umožňuje snížit celkové náklady na vlastnictví nasazení, správy a údržby aplikací v rozlehlých sítích, a to tak, že tyto aplikace jednoduše migrujete do Microsoft Azure. S pomocným mechanismem SAP na virtuálním počítači Azure můžou správci a vývojáři dál používat stejné nástroje pro vývoj a správu, které jsou k dispozici místně.

SLA pro Azure Virtual Machines, najdete tady: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Microsoft Azure nabízí spoustu různých typů virtuálních počítačů, které vám umožní spustit nejmenší systémy SAP a využít až velké systémy SAP a možnost krajiny s tisíci uživateli. V tématu SAP Note [1928533]se poskytuje SAP – počty SAP různých SKU virtuálních počítačů certifikovaných pro SAP.

Příkazy a doporučení týkající se použití Azure Storage, nasazení virtuálních počítačů SAP nebo monitorování SAP provedeného v souvislosti s [nasazením Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md) se vztahují i na nasazení služby SAP.

### <a name="sap-ase-version-support"></a>Podpora verze pomocného mechanismu protokolu SAP
SAP aktuálně podporuje SAP pomocného programu SAP verze 16,0 pro použití s produkty SAP Business Suite. Všechny aktualizace serveru SAP pomocného programu nebo ovladačů JDBC a ODBC, které se mají použít s produkty SAP Business Suite, se poskytují výhradně prostřednictvím tržiště služby SAP na adrese: <https://support.sap.com/swdc>.

Nestahujte aktualizace serveru SAP pomocného mechanismu nebo pro ovladače JDBC a ODBC přímo z webů Sybase. Podrobné informace o opravách, které se podporují pro použití s produkty SAP v místním prostředí a v Azure Virtual Machines najdete v následujících komentářích ke SAP:

* [1590719]
* [1973241]

Obecné informace o spuštění SAP Business Suite na SAP pomocném mechanismu služby najdete v [SCN](https://www.sap.com/community/topic/ase.html) .

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Pokyny pro konfiguraci SAP pomocného mechanismu služby pro instalaci SAP pomocného mechanismu pro SAP na virtuálních počítačích Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura nasazení SAP pomocného programu SAP
Spustitelné soubory SAP pomocného programu by se měly nacházet nebo instalovat na systémovou jednotku disku s operačním systémem virtuálního počítače (jednotka c:\). Většinou v databázích systému a nástrojů SAP pomocného programu SAP nedochází k velkým úlohám. Proto mohou databáze systému a nástrojů (hlavní, model, saptools, sybmgmtdb, sybsystemdb) zůstat v C:\ disky. 

Výjimkou může být dočasná databáze, která v případě některých funkcí SAP ERP a všech ČERNOBÍLých úloh může vyžadovat buď větší objem dat, nebo vstupně-výstupní operace. Svazky nebo IOPS, které nemůže poskytnout disk s operačním systémem virtuálního počítače (jednotka C:\).

V závislosti na verzi SAPInst/SWPM, která se používá k instalaci, by konfigurace instance SAP pomocného programu mohla vypadat takto:

* Jedna databáze s pomocným mechanismem SAP, která se vytvoří při instalaci SAP pomocného mechanismu
* Služba pro vytváření databáze SAP s pomocným mechanismem SAP vytvořená instalací SAP pomocného mechanismu a dalších saptempdb vytvořených instalační rutinou SAP
* Služba pro vytváření databáze SAP s pomocným mechanismem SAP vytvořená instalací SAP pomocného objektu SAP a další databází tempdb vytvořená ručně (například podle poznámky SAP [1752266]) pro splnění specifických požadavků databáze ERP/šířky

Z důvodů výkonu pro konkrétní ERP nebo všechny ČERNOBÍLé úlohy je možné, že je vhodné ukládat do úložiště tempdb i na jiné jednotce než C:\. zařízení tempdb. Pokud neexistuje žádná další databáze tempdb, doporučujeme ji vytvořit (Poznámka k SAP – [1752266]).

U takových systémů je potřeba provést následující kroky pro vytvořenou databázi tempdb navíc:

* Přesunout první zařízení tempdb do prvního zařízení databáze SAP
* Přidání zařízení tempdb do každého z virtuálních pevných disků, které obsahují zařízení databáze SAP

Tato konfigurace umožňuje databázi tempdb spotřebovat více místa, než může systémová jednotka poskytnout. V takovém případě se může u stávajících systémů, které běží místně, kontrolovat velikosti zařízení tempdb. Nebo taková konfigurace umožňuje čísla IOPS v databázi tempdb, která se nedá poskytnout se systémovou jednotkou. Systémy, které jsou spuštěné místně, se dají použít k monitorování vstupně-výstupních úloh v databázi tempdb.

Nikdy neumísťujte žádná zařízení SAP pomocného mechanismu na D:\ disk virtuálního počítače. V případě systému SAP pomocného mechanismu se tato doporučení vztahují také na databázi tempdb, i když jsou objekty uchovávané v databázi tempdb pouze dočasné.

V případě nasazení dat a souborů protokolu transakcí se jedná o příkazy a návrhy provedené v rámci [nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md). V případě systému Windows doporučujeme nasazení prostorů úložiště systému Windows použít k sestavení prokládaných sad s dostatečnými IOPS, propustností a objemy.  

#### <a name="impact-of-database-compression"></a>Dopad komprimace databáze
V konfiguracích, kde se může I/O šířka pásma stát omezením, by každá míra, která omezuje IOPS, mohla přispět k roztažení úlohy, která může běžet ve scénáři IaaS, jako je Azure. Proto se před nahráním existující databáze SAP do Azure doporučuje použít kompresi SAP pomocného mechanismu.

Doporučení použít kompresi před nahráním do Azure je z několika důvodů:

* Množství dat, která se mají nahrát do Azure, je nižší.
* Doba trvání spuštění komprese je kratší, předpokládá se, že jedna může používat silnější hardware s více procesory nebo vyšší propustnost vstupně-výstupních operací nebo méně v/v místní latenci.
* Menší velikosti databází můžou vést k menšímu množství nákladů na přidělení disku.

Data-a při práci v CLOUDu, která je hostovaná v Azure, Virtual Machines v místním prostředí. Další informace o tom, jak zjistit, jestli se komprese už používá v existující databázi pomocného mechanismu služby SAP, najdete v článku SAP Note [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Monitorování instancí databáze pomocí DBACockpit
Pro systémy SAP, které používají program SAP pomocného mechanismu přístupu jako databázová platforma, je DBACockpit přístupný jako okna integrovaných prohlížečů v transakčních DBACockpit nebo jako WebDynpro. Kompletní funkce pro monitorování a správu databáze jsou však k dispozici pouze v WebDynpro implementaci DBACockpit.

Stejně jako u místních systémů je potřeba pro povolení všech funkcí SAP NetWeaver používaných WebDynpro implementací DBACockpit použít několik kroků. Postupujte podle poznámky [1245200] pro SAP, abyste povolili použití webdynpros a vygenerovali požadované. Pokud budete postupovat podle pokynů uvedených v předchozích poznámkách, můžete také nakonfigurovat nástroj Internet Communication Manager (ICM) spolu s porty, které se mají použít pro připojení HTTP a HTTPS. Výchozí nastavení pro protokol HTTP vypadá takto:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a odkazy vygenerované v transakční DBACockpit vypadají podobně jako:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V závislosti na tom, jak je virtuální počítač Azure hostující systém SAP připojený k vaší službě AD a DNS, je potřeba zajistit, aby ICM používala plně kvalifikovaný název hostitele, který se dá vyřešit v počítači, ze kterého se DBACockpit otevírá. V části SAP Note [773830] se naučíte, jak ICM Určuje plně kvalifikovaný název hostitele na základě parametrů profilu a v případě potřeby explicitně nastaví parametry icm/host_name_full.

Pokud jste virtuální počítač nasadili ve scénáři jenom pro cloud bez propojení mezi místními sítěmi a Azure, musíte definovat veřejnou IP adresu a domainlabel. Formát veřejného názvu DNS virtuálního počítače vypadá takto:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Další podrobnosti týkající se názvu DNS najdete [tady][virtual-machines-azurerm-versus-azuresm].

Nastavení parametru profilu SAP ICM/host_name_full na název DNS virtuálního počítače Azure odkaz může vypadat podobně jako:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

V takovém případě je třeba zajistit následující:

* Přidat příchozí pravidla do skupiny zabezpečení sítě v Azure Portal pro porty TCP/IP používané ke komunikaci s nástrojem ICM
* Přidání příchozích pravidel do konfigurace brány Windows Firewall pro porty TCP/IP používané ke komunikaci s nástrojem ICM

Pro automatizované importy dostupných oprav se doporučuje pravidelně uplatňovat poznámku SAP ke shromažďování oprav, které platí pro vaši verzi SAP:

* [1558958]
* [1619967]
* [1882376]

Další informace o řídicím panelu DBA pro SAP pomocného mechanismu služby najdete v následujících komentářích ke službě SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Předpoklady pro zálohování a obnovení pro SAP pomocného programu SAP
Při nasazování pomocného mechanismu SAP do Azure je nutné zkontrolovat metodologii zálohování. I v případě neprodukčních systémů je třeba databáze SAP pravidelně zálohovat. Vzhledem k tomu, že Azure Storage udržuje tři image, může být zálohování méně důležité v souvislosti s kompenzačním selháním úložiště. Hlavní důvod pro zachování správného plánu zálohování a obnovení je větší, než můžete kompenzovat logické a ruční chyby tím, že zajistíte možnosti obnovení k určitému bodu v čase. Cílem je, aby se databáze obnovila k určitému časovému bodu v čase nebo aby používala zálohy v Azure k osazení jiného systému zkopírováním stávající databáze. 

Zálohování a obnovení databáze v Azure funguje stejným způsobem jako v místním prostředí. Viz poznámky SAP:

* [1588316]
* [1585981]

Podrobnosti o vytváření konfigurací výpisu paměti a plánování záloh. V závislosti na vaší strategii a potřebách můžete konfigurovat výpisy databáze a protokolu na disk na jednom z existujících disků nebo přidat další disk pro zálohování. Chcete-li snížit nebezpečí ztráty dat v případě chyby, doporučujeme použít disk, na kterém nejsou umístěny žádné soubory databáze.

Kromě komprese dat a LOB nabízí protokol SAP pomocného programu také kompresi zálohy. Chcete-li využívat méně místa v databázi a výpisy stavu protokolu, doporučuje se použít kompresi zálohy. Další informace najdete v tématu SAP Note [1588316]. Komprimace zálohy je také zásadní pro omezení množství dat, která se mají přenést, pokud chcete stáhnout zálohy nebo virtuální pevné disky obsahující výpisy záloh z virtuálního počítače Azure do místního prostředí.

Nepoužívat jednotku D:\ jako cíl výpisu databáze nebo protokolu.

#### <a name="performance-considerations-for-backupsrestores"></a>Požadavky na výkon pro zálohování a obnovení
Stejně jako v holých nasazeních se výkon zálohování a obnovení závisí na tom, kolik svazků je možné paralelně číst a jakou propustnost těchto svazků může být. Mějte na paměti, že komprese záloh spotřebovává prostředky procesoru. Tato spotřeba procesoru komprimace zálohy může hrát významnou roli na virtuálních počítačích s malým počtem PROCESORových vláken. Proto můžete předpokládat:

* Čím méně je počet disků, který se používá k uložení databázových zařízení, tím menší je celková propustnost při čtení.
* Menší počet PROCESORových vláken ve virtuálním počítači, což je závažnější z vlivu komprimace zálohy
* Čím méně cílů (prokládané adresáře, disky), do kterého se má zapsat záloha, tím menší je propustnost.

Pokud chcete zvýšit počet cílů, na které se má zapisovat, můžete použít dvě možnosti, které se dají v závislosti na vašich potřebách použít nebo kombinovat:

* Prokládání cílového svazku zálohování přes několik připojených disků pro zlepšení propustnosti IOPS na tomto prokládaném svazku
* Vytvoření konfigurace výpisu paměti na úrovni služby SAP pro čtení, která používá více než jeden cílový adresář k zápisu výpisu paměti

Prokládání diskového svazku na více připojených discích je popsáno v tématu [požadavky na nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md). Další informace o použití více adresářů v konfiguraci výpisu stavu systému SAP naleznete v dokumentaci k uložené proceduře sp_config_dump, která se používá k vytvoření konfigurace výpisu paměti v [Sybase InfoCenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Zotavení po havárii s virtuálními počítači Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikace dat pomocí replikačního serveru SAP Sybase
Pomocí serveru SAP Sybase pro replikaci (SRS) poskytuje SAP pomocné řešení pro úsporný režim pro asynchronní přenos databázových transakcí do vzdáleného umístění. 

Instalace a operace služby SRS funguje i v případě, že je virtuální počítač hostovaný ve službách Azure Virtual Machine v místním prostředí.

SAP pomocného HADR nevyžaduje interní Load Balancer Azure a nemá žádné závislosti na clusteringu na úrovni operačního systému. Funguje na virtuálních počítačích Azure s Windows a Linux. Podrobnosti o SAP pomocného programu HADR najdete v [příručce SAP POmocného uživatele hadr](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Specifická pro SAP pomocného mechanismu pro Linux
Počínaje Microsoft Azure můžete svoje stávající aplikace SAP pomocného mechanismu migrace snadno migrovat do Azure Virtual Machines. SAP pomocného programu na virtuálním počítači umožňuje snížit celkové náklady na vlastnictví nasazení, správy a údržby aplikací v podnikové šířce tím, že tyto aplikace jednoduše migruje na Microsoft Azure. S pomocným mechanismem SAP na virtuálním počítači Azure můžou správci a vývojáři dál používat stejné nástroje pro vývoj a správu, které jsou k dispozici místně.

Pro nasazení virtuálních počítačů Azure je důležité znát oficiální SLA, který najdete tady: <https://azure.microsoft.com/support/legal/sla>

Informace o velikosti SAP a seznam SKU certifikovaných virtuálních počítačů SAP jsou k dispozici v tématu SAP Note [1928533]. Další dokumenty SAP o velikosti pro virtuální počítače Azure najdete tady <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> a tady <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Příkazy a doporučení týkající se použití Azure Storage, nasazení virtuálních počítačů SAP nebo monitorování SAP se vztahují na nasazení SAP pomocného programu SAP ve spojení s aplikacemi SAP, jak uvádíme v prvních čtyřech kapitolách tohoto dokumentu.

Následující dvě poznámky SAP obsahují obecné informace o službě pomocného mechanismu pro Linux a pomocného mechanismu řízení v cloudu:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Podpora verze pomocného mechanismu protokolu SAP
SAP aktuálně podporuje SAP pomocného programu SAP verze 16,0 pro použití s produkty SAP Business Suite. Všechny aktualizace serveru SAP pomocného programu nebo ovladačů JDBC a ODBC, které se mají použít s produkty SAP Business Suite, se poskytují výhradně prostřednictvím tržiště služby SAP na adrese: <https://support.sap.com/swdc>.

Jako u místních instalací nestahujte aktualizace serveru SAP pomocného mechanismu nebo pro ovladače JDBC a ODBC přímo z webů Sybase. Podrobné informace o opravách, které se podporují pro použití s produkty SAP Business Suite v místním prostředí a v Azure Virtual Machines najdete v následujících komentářích ke SAP:

* [1590719]
* [1973241]

Obecné informace o spuštění SAP Business Suite na SAP pomocném mechanismu služby najdete v [SCN](https://www.sap.com/community/topic/ase.html) .

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Pokyny pro konfiguraci SAP pomocného mechanismu služby pro instalaci SAP pomocného mechanismu pro SAP na virtuálních počítačích Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktura nasazení SAP pomocného programu SAP
Spustitelné soubory SAP pomocného programu by se měly nacházet nebo instalovat do kořenového systému souborů virtuálního počítače (/Sybase). Většinou v databázích systému a nástrojů SAP pomocného programu SAP nedochází k velkým úlohám. Proto mohou být databáze systému a nástrojů (hlavní, model, saptools, sybmgmtdb, sybsystemdb) uloženy v kořenovém systému souborů. 

Výjimkou může být dočasná databáze, která v případě některých funkcí SAP ERP a všech ČERNOBÍLých úloh může vyžadovat buď větší objem dat, nebo vstupně-výstupní operace. Svazky nebo IOPS, které nemůže disk s operačním systémem virtuálního počítače poskytnout 

V závislosti na verzi SAPInst/SWPM, která se používá k instalaci systému, může databáze obsahovat:

* Při instalaci SAP pomocného mechanismu softwaru se vytvořila jedna databáze s přísystémem SAP
* Služba pro vytváření databáze SAP s pomocným mechanismem SAP vytvořená instalací SAP pomocného mechanismu a dalších saptempdb vytvořených instalační rutinou SAP
* Služba pro vytváření databáze SAP s pomocným mechanismem SAP vytvořená instalací SAP pomocného objektu SAP a další databází tempdb vytvořená ručně (například podle poznámky SAP [1752266]) pro splnění specifických požadavků databáze ERP/šířky

Z důvodů výkonu pro konkrétní ERP nebo všechny ČERNOBÍLé úlohy může být vhodné ukládat do samostatného systému souborů databázi tempdb (pomocí SWPM nebo ručně), která může být reprezentována s jedním datovým diskem Azure nebo se systémem Linux RAID pokrývá m více datové disky Azure. Pokud neexistuje žádná další databáze tempdb, doporučujeme ji vytvořit (Poznámka k SAP – [1752266]).

U takových systémů je potřeba provést následující kroky pro vytvořenou databázi tempdb navíc:

* Přesunout první adresář v databázi tempdb do prvního systému souborů databáze SAP
* Přidejte adresáře tempdb do každého disku obsahujícího systém souborů databáze SAP.

Tato konfigurace umožňuje databázi tempdb spotřebovat více místa, než může systémová jednotka poskytnout. V takovém případě se může u stávajících systémů, které běží místně, kontrolovat velikosti zařízení tempdb. Nebo taková konfigurace umožňuje čísla IOPS v databázi tempdb, která se nedá poskytnout se systémovou jednotkou. Systémy, které jsou spuštěné místně, se dají použít k monitorování vstupně-výstupních úloh v databázi tempdb.

Nikdy neumísťujte žádné adresáře SAP pomocného programu do/mnt nebo/mnt/Resource virtuálního počítače. V případě systému SAP pomocného mechanismu se tato doporučení vztahují také na databázi tempdb, i když jsou objekty uchovávané v databázi tempdb pouze dočasné. Vzhledem k tomu, že/mnt nebo/mnt/Resource je výchozí dočasné místo pro virtuální počítače Azure, které není trvalé. Další podrobnosti o dočasném prostoru virtuálního počítače Azure najdete v [tomto článku][virtual-machines-linux-how-to-attach-disk] .

V případě nasazení dat a souborů protokolu transakcí se jedná o příkazy a návrhy provedené v rámci [nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md). V případě nasazení se systémem Linux se doporučuje použití LVM nebo MDADM k vytváření prokládaných sad s dostatečným IOPS, propustností a svazkem. 

#### <a name="impact-of-database-compression"></a>Dopad komprimace databáze
V konfiguracích, kde se může I/O šířka pásma stát omezením, by každá míra, která omezuje IOPS, mohla přispět k roztažení úlohy, která může běžet ve scénáři IaaS, jako je Azure. Proto se před nahráním existující databáze SAP do Azure doporučuje použít kompresi SAP pomocného mechanismu.

Doporučení použít kompresi před nahráním do Azure je z několika důvodů:

* Množství dat, která se mají nahrát do Azure, je nižší.
* Doba trvání spuštění komprese je kratší, předpokládá se, že jedna může používat silnější hardware s více procesory nebo vyšší propustnost vstupně-výstupních operací nebo méně v/v místní latenci.
* Menší velikosti databází můžou vést k menšímu množství nákladů na přidělení disku.

Data-a při práci v CLOUDu, která je hostovaná v Azure, Virtual Machines v místním prostředí. Další informace o tom, jak zjistit, jestli se komprese už používá v existující databázi pomocného mechanismu služby SAP, najdete v článku SAP Note [1750510]. Další informace o kompresi databáze najdete v tématu SAP Note [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Monitorování instancí databáze pomocí DBACockpit
Pro systémy SAP, které používají program SAP pomocného mechanismu přístupu jako databázová platforma, je DBACockpit přístupný jako okna integrovaných prohlížečů v transakčních DBACockpit nebo jako WebDynpro. Kompletní funkce pro monitorování a správu databáze jsou však k dispozici pouze v WebDynpro implementaci DBACockpit.

Stejně jako u místních systémů je potřeba pro povolení všech funkcí SAP NetWeaver používaných WebDynpro implementací DBACockpit použít několik kroků. Postupujte podle poznámky [1245200] pro SAP, abyste povolili použití webdynpros a vygenerovali požadované. Pokud budete postupovat podle pokynů uvedených v předchozích poznámkách, můžete také nakonfigurovat nástroj Internet Communication Manager (ICM) spolu s porty, které se mají použít pro připojení HTTP a HTTPS. Výchozí nastavení pro protokol HTTP vypadá takto:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a odkazy vygenerované v transakční DBACockpit budou vypadat podobně jako toto:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V závislosti na tom, jak je virtuální počítač Azure hostující systém SAP připojený k vaší službě AD a DNS, je potřeba zajistit, aby ICM používala plně kvalifikovaný název hostitele, který se dá vyřešit v počítači, ze kterého se DBACockpit otevírá. V tématu SAP Note [773830] můžete pochopit, jak ICM Určuje plně kvalifikovaný název hostitele v závislosti na parametrech profilu a v případě potřeby explicitně nastavily icm/host_name_full.

Pokud jste virtuální počítač nasadili ve scénáři jenom pro cloud bez propojení mezi místními sítěmi a Azure, musíte definovat veřejnou IP adresu a domainlabel. Formát veřejného názvu DNS virtuálního počítače vypadá takto:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Další podrobnosti týkající se názvu DNS najdete [tady][virtual-machines-azurerm-versus-azuresm].

Nastavení parametru profilu SAP ICM/host_name_full na název DNS virtuálního počítače Azure odkaz může vypadat podobně jako:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

V takovém případě je třeba zajistit následující:

* Přidat příchozí pravidla do skupiny zabezpečení sítě v Azure Portal pro porty TCP/IP používané ke komunikaci s nástrojem ICM
* Přidání příchozích pravidel do konfigurace brány Windows Firewall pro porty TCP/IP používané ke komunikaci s nástrojem ICM

Pro automatizované importy dostupných oprav se doporučuje pravidelně uplatňovat poznámku SAP ke shromažďování oprav, které platí pro vaši verzi SAP:

* [1558958]
* [1619967]
* [1882376]

Další informace o řídicím panelu DBA pro SAP pomocného mechanismu služby najdete v následujících komentářích ke službě SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Předpoklady pro zálohování a obnovení pro SAP pomocného programu SAP
Při nasazování pomocného mechanismu SAP do Azure je nutné zkontrolovat metodologii zálohování. I v případě neprodukčních systémů je třeba databáze SAP pravidelně zálohovat. Vzhledem k tomu, že Azure Storage udržuje tři image, může být zálohování méně důležité v souvislosti s kompenzačním selháním úložiště. Hlavní důvod pro zachování správného plánu zálohování a obnovení je větší, než můžete kompenzovat logické a ruční chyby tím, že zajistíte možnosti obnovení k určitému bodu v čase. Cílem je, aby se databáze obnovila k určitému časovému bodu v čase nebo aby používala zálohy v Azure k osazení jiného systému zkopírováním stávající databáze. 

Zálohování a obnovení databáze v Azure funguje stejným způsobem jako v místním prostředí. Viz poznámky SAP:

* [1588316]
* [1585981]

Podrobnosti o vytváření konfigurací výpisu paměti a plánování záloh. V závislosti na vaší strategii a potřebách můžete nakonfigurovat výpisy databáze a protokolu na disk na jeden z existujících disků nebo přidat další disk pro zálohování. Pokud chcete snížit nebezpečí ztráty dat v případě chyby, doporučujeme použít disk, na kterém není umístěný žádný adresář nebo soubor databáze.

Kromě komprese dat a LOB nabízí protokol SAP pomocného programu také kompresi zálohy. Chcete-li využívat méně místa v databázi a výpisy stavu protokolu, doporučuje se použít kompresi zálohy. Další informace najdete v tématu SAP Note [1588316]. Komprimace zálohy je také zásadní pro omezení množství dat, která se mají přenést, pokud chcete stáhnout zálohy nebo virtuální pevné disky obsahující výpisy záloh z virtuálního počítače Azure do místního prostředí.

Nepoužívejte dočasné místo pro virtuální počítače Azure/mnt ani/mnt/Resource jako cíl výpisu databáze nebo protokolu.

#### <a name="performance-considerations-for-backupsrestores"></a>Požadavky na výkon pro zálohování a obnovení
Stejně jako v holých nasazeních se výkon zálohování a obnovení závisí na tom, kolik svazků je možné paralelně číst a jakou propustnost těchto svazků může být. Mějte na paměti, že komprese záloh spotřebovává prostředky procesoru. Tato spotřeba procesoru komprimace zálohy může hrát významnou roli na virtuálních počítačích s malým počtem PROCESORových vláken.  Proto můžete předpokládat:

* Čím méně je počet disků, který se používá k uložení databázových zařízení, tím menší je celková propustnost při čtení.
* Menší počet PROCESORových vláken ve virtuálním počítači, což je závažnější z vlivu komprimace zálohy
* Méně cílů (RAID software pro Linux, disky), do kterých se zapisuje záloha, čím menší propustnost

Pokud chcete zvýšit počet cílů, na které se má zapisovat, můžete použít dvě možnosti, které se dají v závislosti na vašich potřebách použít nebo kombinovat:

* Prokládání cílového svazku zálohování přes několik připojených disků pro zlepšení propustnosti IOPS na tomto prokládaném svazku
* Vytvoření konfigurace výpisu paměti na úrovni služby SAP pro čtení, která používá více než jeden cílový adresář k zápisu výpisu paměti

Prokládání diskového svazku na více připojených discích je popsáno v tématu [požadavky na nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md). Další informace o použití více adresářů v konfiguraci výpisu stavu systému SAP naleznete v dokumentaci k uložené proceduře sp_config_dump, která se používá k vytvoření konfigurace výpisu paměti v [Sybase InfoCenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Zotavení po havárii s virtuálními počítači Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replikace dat pomocí replikačního serveru SAP Sybase
Pomocí serveru SAP Sybase pro replikaci (SRS) poskytuje SAP pomocné řešení pro úsporný režim pro asynchronní přenos databázových transakcí do vzdáleného umístění. 

Instalace a operace služby SRS funguje i v případě, že je virtuální počítač hostovaný ve službách Azure Virtual Machine v místním prostředí.

Podporuje se HADR pomocného serveru přes SAP pro replikaci. K pokusu o takovou konfiguraci se důrazně doporučuje použít SAP pomocného mechanismu 16,03. Podrobnější pokyny k instalaci takových konfigurací najdete podrobněji v tomto [blogu](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).
