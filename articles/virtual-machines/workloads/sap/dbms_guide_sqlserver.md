---
title: Nasazení SQL serveru Azure Virtual Machines DBMS pro úlohy SAP | Dokumentace Microsoftu
description: Nasazení SQL serveru Azure Virtual Machines DBMS pro úlohy SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58ae26a2daf2a65eaf56672c9e75147bd71e489a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330728"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Nasazení SQL serveru Azure Virtual Machines DBMS pro SAP NetWeaver

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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
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




Tento dokument popisuje několik různých oblastí, které je třeba zvážit při nasazování serveru SQL pro úlohy SAP v Azure IaaS. Podmínkou pro tento dokument, by měl mít čtení dokumentu [aspekty pro nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](dbms_guide_general.md) a také další příručky v [úloh SAP v dokumentaci Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 



> [!IMPORTANT]
> Rámec tohoto dokumentu je verze Windows na serveru SQL Server. SAP nepodporuje verzi systému SQL Server s žádným z softwaru SAP Linuxu. Dokument není diskuze o Microsoft Azure SQL Database, což je platforma jako nabídka služeb platformy Microsoft Azure. Informace v tomto dokumentu jsou o spuštění produktu SQL Server, jako je znám pro místní nasazení v Azure Virtual Machines, využití infrastruktury jako schopnosti služeb Azure. Možnosti databáze a funkcí mezi tyto dvě nabídky se liší a by neměl promíchala mezi sebou. Viz také: <https://azure.microsoft.com/services/sql-database/>
> 
>

Obecně platí měli byste zvážit použití nejnovější serveru SQL Server verze spouštění úloh SAP v Azure IaaS. Nejnovější verze systému SQL Server nabízejí lepší integraci do služby Azure a funkce. Nebo obsahují změny, které optimalizaci operací v infrastruktuře Azure IaaS.

Doporučujeme zkontrolovat [to] [ virtual-machines-sql-server-infrastructure-services] dokumentaci, než budete pokračovat.

V následujících částech jsou části částí dokumentace v části výše uvedený odkaz agregovat a uvedené. Specifika týkající se SAP jsou také uvedené a některé koncepty, které jsou popsány podrobněji. Důrazně ale doporučujeme projít dokumentaci nad první před čtením dokumentaci specifickou pro SQL Server.

V IaaS konkrétní informace, které byste měli znát před pokračováním je některé SQL Server:

* **Podpora verzí SQL**: Pro zákazníky, SAP, SQL Server 2008 R2 a vyšší je podporovaná ve virtuálních počítačích Microsoft Azure. Starší verze nepodporují. Kontrola této Obecné [prohlášení o odborné pomoci](https://support.microsoft.com/kb/956893) další podrobnosti. Obecně platí SQL Server 2008 je společnost Microsoft podporuje také. Ale protože významné funkce pro SAP, která byla zavedena v systému SQL Server 2008 R2, SQL Server 2008 R2 je minimální verzi pro SAP. Obecně platí měli byste zvážit použití nejnovější serveru SQL Server verze spouštění úloh SAP v Azure IaaS. Nejnovější verze systému SQL Server nabízejí lepší integraci do služby Azure a funkce. Nebo obsahují změny, které optimalizaci operací v infrastruktuře Azure IaaS. Proto je omezen na SQL Server 2016 a SQL serverem 2017 papíru.
* **Výkon SQL**: Microsoft Azure a porovnání jiné nabídky veřejného cloudu virtualizace, ale jednotlivé výsledky provedení hostované virtuální počítače se můžou lišit. Podívejte se článek [osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Pomocí Imagí z webu Azure Marketplace**: Nejrychlejší způsob, jak nasadit nový virtuální počítač Microsoft Azure je chcete použít některou image z Azure Marketplace. Existují imagí v Tržišti Azure Marketplace, které obsahují nejnovější verze systému SQL Server. Obrázky, kde je již nainstalován SQL Server nelze použít pro aplikace SAP NetWeaver okamžitě. Důvodem je, že výchozí kolace systému SQL Server je nainstalovaný v rámci těchto imagí a nejsou vyžadované systémy SAP NetWeaver kolace. Chcete-li použít tyto bitové kopie, zkontrolujte kroky popsané v kapitole [pomocí bitové kopie systému SQL Server z webu Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Doporučení pro virtuální počítač nebo virtuální pevný disk strukturu pro nasazení SAP související SQL serveru
V souladu s obecný popis spustitelné soubory systému SQL Server nachází nebo nainstalovat do systémové jednotce disk s operačním systémem Virtuálního počítače (jednotka C:\).  Většina systémových databází systému SQL Server nejsou obvykle využít na vysoké úrovni podle úloh SAP NetWeaver. V důsledku systémové databáze systému SQL Server (hlavní server, databázi msdb a modelu) mohou zůstat na jednotce C:\. Výjimka by měla být databáze tempdb, který v případě úloh SAP mohou vyžadovat větší objem dat nebo objem vstupně-výstupní operace. Vstupně-výstupní úlohy, které by neměly použije na virtuální pevný disk operačního systému. Pro tyto systémy by měl provést následující kroky:


* S řešením SAP všechny certifikované typy virtuálních počítačů (viz poznámka SAP [1928533]), s výjimkou virtuálních počítačů řady A-Series, tempdb dat a souborů protokolu můžete umístit na jednotku D:\ netrvalé. 
* Doporučujeme však použít několik souborů dat databáze tempdb. Mějte na paměti, že svazky D:\ jednotky se liší podle typu virtuálního počítače. Přesné velikosti disku D:\ různých virtuálních počítačů najdete v článku [velikosti pro Windows virtual machines v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Tyto konfigurace povolit tempdb využívat více místa, než je schopna poskytovat systémové jednotce. Dočasné jednotce D:\ také nabízí lepší vstupně-výstupní latence a propustnosti (s výjimkou virtuálních počítačů řady A-Series). Aby bylo možné zjistit správné tempdb velikost, můžete zkontrolovat velikosti databáze tempdb na stávající systémy. 

>[!NOTE]
> v případě, že datové soubory databáze tempdb a soubor protokolu se umístí do složky na jednotku D:\, kterou jste vytvořili, musíte zajistit, že složka existuje po restartování virtuálního počítače. Vzhledem k tomu, že jednotce D:\ je inicializován čerstvě po restartování virtuálního počítače všechny struktury souborů a adresářů jsou dojde k vymazání. Možnost znovu vytvořit struktury konečné adresáře na jednotku D:\, před počáteční službu systému SQL Server je popsána v [v tomto článku](http://www.sqlserver.co.uk/index.php/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Konfigurace virtuálního počítače, který spouští SQL Server s databázi SAP a umístění dat databáze tempdb a databázi tempdb souboru protokolu na jednotku D:\ vypadat nějak takto:

![Diagram jednoduchého konfigurace disků virtuálního počítače pro SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Předchozí diagram zobrazuje jednoduchém případě. Jak obcházena v článku [aspekty pro nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](dbms_guide_general.md), number, a velikost disků Premium Storage je závislá z různých faktorech. Obecně doporučujeme ale:

- Použití prostorů úložiště a vytvoří jednu nebo několik svazků, které obsahují datové soubory SQL serveru. Důvod pro vytvoření této konfigurace je, že v reálném životě jsou mnoho databází SAP s soubory různé velikosti databáze s jinou vstupně-výstupní úlohy.
- Použití prostorů úložiště slouží k poskytování dostatek vstupně-výstupních operací a pro soubor protokolu transakcí serveru SQL Server. Potenciální vstupně-výstupních operací úloh je často vodicí čáry použité k určení velikosti svazku protokolu transakcí a není možné svazku objemu transakcí serveru SQL Server
- Použijte D:\drive pro databázi tempdb, za předpokladu, výkon je dostatečné. Celkové zatížení, je omezen výkonu tmepdb se nachází na jednotce D:\ potřebujete zvažte přesunutí databáze tempdb na samostatné disky Premium Storage podle doporučení v [v tomto článku](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Speciální pro virtuální počítače řady M-Series
Pro virtuální počítače Azure řady M-Series lze snížit latenci zápisu do protokolu transakcí faktorů, které jsou ve srovnání s výkonem Azure Premium Storage, při použití akcelerátor zápisu Azure. Proto byste měli nasadit akcelerátor zápisu Azure pro virtuální pevné disky, které tvoří svazek protokolu transakcí serveru SQL Server. Podrobnosti najdete v dokumentu [akcelerátorem zápisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>Formátování disků
Velikost bloku systému souborů NTFS pro disky obsahující soubory protokolu a data systému SQL Server pro SQL Server, třeba 64KB. Není nutné k formátování na jednotku D:\. Tato jednotka se dodává předem formátovaný.

Pokud chcete mít jistotu, že obnovení nebo vytváření databází není inicializace datové soubory eliminací obsah souborů, by měl Ujistěte se, že uživatel kontext, ve kterém je spuštěna služba SQL Server v má určitá oprávnění. Uživatelé ve skupině Windows správce obvykle mají tato oprávnění. Pokud službu systému SQL Server je spuštěn v kontextu uživatele Windows uživatel není správcem, budete muset přiřadit tomuto uživateli uživatelské právo **provádět úlohy údržby svazku**.  Podrobnosti najdete v tomto článku znalostní báze Microsoft: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Dopad komprese databáze
V konfiguraci, kde šířku pásma vstupně-výstupní operace se může stát omezujícím faktorem každá míra, což snižuje vstupně-výstupních operací vám může pomoci roztáhnout úlohy, jež možné spouštět ve scénáři IaaS, jako je Azure. Proto pokud ještě není Hotovo, použití komprese stránku serveru SQL se doporučuje od SAPU a Microsoftu před nahráním existující databázi SAP do Azure.

Doporučení a provádí kompresi databáze před nahráním do Azure dostane mimo dva důvody:

* Množství dat k odeslání je menší.
* Doba trvání spuštění komprese je kratší, za předpokladu, že jeden můžete používat silnější hardware s více procesory nebo větší šířku pásma vstupně-výstupních operací nebo méně vstupně-výstupní latence místní.
* Menší velikosti databáze může vést k nižší náklady pro přidělení disku

Komprese databáze funguje i na Azure Virtual Machines, stejně jako místní. Pro další podrobnosti o tom, jak komprimovat existujících databází systému SQL Server SAP NetWeaver, zkontrolujte článek [SAP vylepšené komprese nástroj MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 a novější – ukládání soubory databáze přímo v Azure Blob Storage
SQL Server 2014 a novějších verzích open možnost k ukládání databázových souborů přímo na Store objektů Blob v Azure bez "obálku" virtuálního pevného disku je obcházet. Tento typ nasazení zejména s pomocí úložiště Azure úrovně Standard nebo menší typy virtuálních počítačů umožňuje scénáře, ve kterém lze překonat limity vstupně-výstupních operací, které by se vynucuje omezený počet disků, které je možné připojit na několik menších typy virtuálních počítačů. Tento způsob nasazení funguje pro uživatelské databáze, ale ne pro systémové databáze systému SQL Server. Funguje i pro data a soubory protokolu serveru SQL Server. Pokud chcete nasadit databázi SQL serveru SAP díky tomu namísto "zabalení" do virtuálních pevných disků, mějte na paměti:

* Účet úložiště používá musí být ve stejné oblasti Azure, protože ten, který se používá k nasazení virtuálního počítače SQL Server běží v.
* Důležité informace týkající se distribuce virtuální pevné disky výše uvedených přes různé účty úložiště Azure použít pro tuto metodu i nasazení. Znamená, že počet vstupně-výstupní operace proti limity účtu úložiště Azure.
* Místo účetní proti podíl vstupně-výstupní operace úložiště Virtuálního počítače, provoz proti představující systému SQL Server soubory protokolu a data objektů BLOB služby storage se s tím při výběru do šířky pásma sítě Virtuálního počítače z konkrétní typ virtuálního počítače. Síť a úložiště šířky pásma určitého typu virtuálních počítačů, projděte si článek [velikosti pro Windows virtual machines v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* V důsledku doručením (push) vstup a výstup souborů přes kvótu sítě, jsou většinou ztroskotání kvóta úložiště a s ním používat celkovou šířku pásma virtuálního počítače pouze částečně.
* Vstupně-výstupních operací a vstupně-výstupní propustnost výkonnostní cíle, které Azure Premium Storage nabízí pro jiný disk velikosti už neplatí. I když jsou objekty BLOB, který jste vytvořili jsou uložená na Azure Premium Storage. Cíle, které jsou popsané v článku [vysoce výkonné úložiště úrovně Premium a spravovaným diskům pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage#scalability-and-performance-targets). V důsledku umístění datové soubory SQL serveru a souborů protokolu přímo na objektech BLOB, které jsou uložené na Azure Premium Storage, výkonové charakteristiky se může lišit v porovnání s virtuální pevné disky na Azure Premium Storage.
* Na základě použití mezipaměti u hostitele jako k dispozici pro disky Azure Premium Storage není k dispozici, při umísťování datové soubory SQL serveru přímo na objektech BLOB Azure.
* Na virtuální počítače řady M-Series nelze použít akcelerátor zápisu Azure pro podporu hranicí milisekund zápisů ve službě soubor protokolu transakcí serveru SQL Server. 

Podrobnosti o této funkci najdete v článku [datové soubory SQL serveru v Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Doporučení pro produkční systémy se vyhnout této konfigurace a místo toho zvolte umístění dat SQL serveru a souborů protokolů v Azure Premium Storage virtuální pevné disky místo přímo na objektech BLOB Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014 Buffer Pool Extension
SQL Server 2014 zavedli novou funkci, která se nazývá [rozšíření fondu vyrovnávací paměti](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Tato funkce rozšíření fondu vyrovnávací paměti systému SQL Server, který je uložen v paměti s mezipamětí druhé úrovně, která je založená na místní jednotky SSD serveru nebo na virtuálním počítači. Rozšíření fondu vyrovnávací paměti povolí, uchovávání větší pracovní sadu dat "v paměti". Ve srovnání s přístupem k Azure Standard Storage přístup do rozšíření fondu vyrovnávací paměti, který je uložený na místní SSD disků virtuálního počítače Azure je řada faktorů rychleji. Rozšíření fondu vyrovnávací paměti do mezipaměti Azure Premium Storage pro čtení, porovnání podle doporučení pro datové soubory SQL serveru, jsou pro rozšíření fondu vyrovnávací paměti neočekávají žádné významné výhody. Důvodem je, že oba mezipamětí (rozšíření fondu vyrovnávací paměti SQL serveru a mezipaměť pro čtení úložiště úrovně Premium) budou používat místní disky výpočetního uzlu Azure.

Zkušenosti získané do té doby se rozšíření fondu vyrovnávací paměti SQL serveru s úlohami SAP je a stále neumožňuje vymazat doporučení na, jestli se má použít ve všech případech. Ideálním případem je, že pracovní sady, které vyžaduje aplikace SAP zapadá do hlavní paměti. Díky Azure mezitím nabídky virtuálních počítačů, které jsou součástí až na 4 TB paměti by měl být dosažitelný zachovat pracovní sady v paměti. Proto využití rozšíření fondu vyrovnávací paměti je omezená na některých výjimečných případech a by neměl být hlavní fáze technické případ.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Informace o zálohování nebo obnovení pro SQL Server
Při nasazování serveru SQL Server do Azure, musí být přezkoumána zálohování metodologii. I když v systému není produkční systém, SAP databázi hostované na serveru SQL Server je nutné zálohovat pravidelně. Vzhledem k tomu, že Azure Storage uchovává tři Image, je nyní méně důležité možnosti kompenzační selhání úložiště z hlediska zálohování. Priorita důvod k zachování správný plán zálohování a obnovení je další, která se může kompenzovat chyby logické nebo ruční tím, že poskytuje bod v možnosti v době obnovení. Cílem je tak buď pomocí zálohování a obnovení databáze zpět do určitého bodu v čase nebo pomocí zálohování v Azure pro jiný systém tak, že zkopírujete existující databázi. 

Pokud se chcete podívat na jiném SQL serveru možnosti zálohování v Azure najdete v článku [zálohování a obnovení pro SQL Server ve službě Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). Tento článek popisuje několik různých možností.

### <a name="manual-backups"></a>Ruční zálohy
Máte několik možností pro "Ruční" zálohování podle:

1. Provádění běžných záloh systému SQL Server na přímé disky Azure připojené. Tato metoda má výhodu máte zálohy, které jsou k dispozici rychle pro aktualizace systému a Vybudujte nový systémů jako kopie stávající systémy SAP
2.  SQL Server 2012 CU4 a vyšší můžete zálohovat databáze na adresu URL služby Azure storage.
3.  Zálohy snímků souborů pro databázové soubory v úložišti objektů Blob v Azure. Tato metoda funguje jenom v případě systému SQL Server soubory protokolu a data jsou umístěné v úložišti objektů blob v Azure

Prvním způsobem je dobře známé a použité v mnoha případech na místní světě. Nicméně můžete ponechá s úlohou řešení delší období zálohy. Vzhledem k tomu, že nechcete zachovat zálohy po dobu 30 nebo více dní v místně připojené služby Azure Storage, budete mít nutnost použít služby zálohování Azure nebo jiného nástroje třetích stran zálohování nebo obnovení, který zahrnuje správy přístupu a uchovávání informací pro své zálohy. Nebo můžete vytvořit velké souborový server v Azure přes prostory úložiště Windows.

Druhý způsob je blíže popsáno v článku [zálohování SQL serveru na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Dvě různé verze systému SQL Server mají různé varianty v této funkce. Proto byste měli zkontrolovat si v dokumentaci pro konkrétní vrácení verze systému SQL Server. Důležité si uvědomit, že v tomto článku jsou uvedené mnoho omezení. Buď máte možnost provést zálohování pro:

- Jeden Azure jednostránkové objektu blob, který pak omezuje zálohování velikost až 1 000 GB. Tím také omezuje propustnost, které můžete dosáhnout.
- Více (až 64) Azure objekty BLOB bloku, které umožňují teoretické zálohování velikost 12 TB. Testy s databázemi zákazníka zjistí, že může být menší než jeho teoretické omezení maximální velikosti zálohy. V tomto případě jsou zodpovědného za správu zálohování a uchovávání záloh a o přístup.


### <a name="automated-backup-for-sql-server"></a>Automatizovaná záloha pro SQL Server
Automatizované zálohování poskytuje automatické zálohování služby pro edice SQL Server Standard a Enterprise spuštěná ve virtuálním počítači s Windows v Azure. Tato služba je poskytovaná [rozšíření agenta SQL Server IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), které se automaticky instaluje do Image virtuálních počítačů Windows serveru SQL na webu Azure Portal. Pokud nasadíte Image operačního systému s nainstalovaným SQL serverem, budete muset nainstalovat rozšíření virtuálního počítače zvlášť. Nezbytné kroky popsané v tomto [článku](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Další podrobnosti o možnostech této metody můžete najít v těchto článcích:

- SQL Server 2014: [Automatizovaná záloha pro SQL Server 2014 Virtual Machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Automatizované zálohování v2 pro Azure Virtual Machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Podívat se na dokumentaci, uvidíte, že vylepšené funkce s novější verzí systému SQL Server. Některé další podrobnosti o systému SQL Server v tomto článku se vydávají automatizovaných záloh [spravované zálohování SQL serveru do Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). Omezení velikosti teoretické zálohování je 12 TB.  Automatizované zálohování může být vhodné metody pro zálohování velikosti až 12 TB. Vzhledem k tomu víc objektů blob se zapisují do paralelně, můžete očekávat propustnost větší než 100 MB/s. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Azure Backup pro virtuální počítače s SQL serverem
Tato nová metoda záloh systému SQL Server nabízí od června 2018 jako verze public preview služby Azure Backup. Metoda k zálohování serveru SQL Server je stejný, jako jsou pomocí jiných nástrojů třetích stran, konkrétně rozhraní SQL Server VSS/infrastruktury virtuálních klientských počítačů na zálohování datového proudu do cílového umístění. V tomto případě cílového umístění je trezor služby Azure Recovery Services.

Více než podrobný popis této metody zálohování, přidává řadu výhod centrální záložní konfigurace, monitorování, a je k dispozici správu [tady](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Řešení zálohování jiného výrobce
Poměrně počtu zákazníků SAP nebyla žádná možnost začít znovu a zavést kompletní řešení zálohování nové části jejich prostředí SAP spuštěným v Azure. V důsledku toho existující řešení zálohování museli použít a rozšíří do Azure. Rozšíření stávajících řešení pro zálohování do Azure, obvykle pracovat i s většinou hlavních dodavatelů v tomto prostoru. 


## <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Pomocí bitové kopie systému SQL Server z webu Microsoft Azure Marketplace
Společnost Microsoft nabízí virtuální počítače na webu Azure Marketplace, které již obsahují verze systému SQL Server. Pro SAP zákazníci, kteří požadují licence pro SQL Server a Windows pomocí těchto bitových kopií může být příležitost k pokrytí potřebu licence zapínáním virtuální počítače s SQL Server již nainstalován. Chcete-li použít tyto bitové kopie pro SAP, potřeba provést následující aspekty:

* Než zkušební verze systému SQL Server získat vyšší náklady než "Pouze Windows" virtuálních počítačů nasazených z Azure Marketplace. Najdete v těchto článcích pro porovnání cen: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> a <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Můžete použít jenom verze systému SQL Server, které SAP podporuje.
* Kolace instance systému SQL Server, který je nainstalovaný na virtuálních počítačích, na webu Azure Marketplace nabízí není kolace SAP NetWeaver vyžaduje instanci systému SQL Server spustit. I když s pokyny uvedené v následující části můžete změnit kolaci.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Změna kolace systému SQL Server z virtuálního počítače s Microsoft Windows a SQL serverem
Vzhledem k tomu, že Image SQL serveru na webu Azure Marketplace nejsou nastaveny pro používání kolace, který je požadován aplikací SAP NetWeaver, je potřeba změnit okamžitě po nasazení. Jakmile byl nasazen virtuální počítač a správce může přihlásit k nasazenému virtuálnímu počítači, lze pro SQL Server provést tato změna kolace pomocí následujících kroků:

* Jako správce otevřete okno příkazového řádku Windows.
* Změňte adresář na C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Spusťte příkaz: Setup.exe/quiet /ACTION = InstanceName REBUILDDATABASE = MSSQLSERVER /SQLSYSADMINACCOUNTS =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> je účet, který byl definován jako účet správce při nasazování virtuálního počítače poprvé v galerii.

Proces zabere jenom pár minut. Pokud chcete mít jistotu, zda se na krok skončila s správný výsledek, proveďte následující kroky:

* Otevřete SQL Server Management Studio.
* Otevře se okno dotazu.
* Spusťte příkaz sp_helpsort v hlavní databázi systému SQL Server.

Požadovaný výsledek by měl vypadat:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Pokud je jiný výsledek, zastavit nasazení SAP a zjistěte, proč instalačního příkazu nefunguje podle očekávání. Nasazení aplikací SAP NetWeaver na instanci systému SQL Server s jinou neurčují SQL serveru než je uvedeno výše je **není** podporována.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server vysokou dostupnost pro SAP v Azure
Použití SQL serveru v nasazeních Azure IaaS pro SAP, máte několik různých možností přidat k nasazení DBMS vrstvu s vysokou dostupností. Jak je popsáno v [aspekty pro nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](dbms_guide_general.md) již, Azure nabízí různé doby provozu smlouvy o úrovni služeb pro jeden virtuální počítač a pár virtuální počítače nasazené ve skupině dostupnosti Azure. Předpokladem je, jednotce je směrem k doby provozu smlouva SLA pro vaše nasazení v produkčním prostředí, které vyžaduje nasazení ve skupinách dostupnosti Azure. V takovém případě musíte nasadit nejméně dva virtuální počítače v takové skupině dostupnosti. Jeden virtuální počítač se spustí aktivní instanci serveru SQL Server. Jiný virtuální počítač se spustí pasivní Instance

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>SQL Server Clustering pomocí souborového serveru se Škálováním Windows
S Windows serverem 2016 společnost Microsoft zavedla [prostory úložiště – přímé](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Podle přímé nasazení prostorů úložiště, SQL Server FCI clustering se podporuje. Podrobnosti najdete v článku [konfigurace převzetí služeb při selhání clusteru Instance systému SQL Server na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). Řešení vyžaduje vypořádat s virtuální IP adresou z prostředků clusteru služby Azure load balancer. Soubory databáze systému SQL Server se ukládají v prostorech úložiště. Jedná se tedy vzhledem k tomu, že by vyžadoval vytvořit prostory úložiště Windows založené na Azure Premium Storage. Protože toto řešení má byla podporována pro není příliš dlouho, nejsou žádné známé SAP zákazníkům, kteří používají toto řešení v produkčních scénářích SAP.  

### <a name="sql-server-log-shipping"></a>Přesouvání protokolu serveru SQL Server
Jednou z metod vysoké dostupnosti (HA) je přesouvání protokolu SQL serveru. Pokud virtuální počítače součástí konfigurace HA překlad názvů funguje, neexistuje žádný problém a nastavení v Azure se neliší od všechna nastavení, která se provádí na místě. S ohledem na nastavení přesouvání protokolu a zásady kolem přesouvání protokolu. Podrobnosti o přesouvání protokolu SQL serveru najdete v článku [o přesouvání protokolu (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

Funkce přesouvání protokolu serveru SQL Server používal téměř v Azure zajistit vysokou dostupnost v rámci jedné oblasti Azure. Ale v následujících situacích zákazníci SAP používali přesouvání protokolu úspěšné ve spojení s Azure:

- Scénáře zotavení po havárii z jedné oblasti Azure do jiné oblasti Azure
- Konfigurace zotavení po havárii z místního do oblasti Azure
- Scénáře selhání vyjmutí z místního do Azure. V takových případech se přesouvání protokolu používá k synchronizaci nové nasazení DBMS v Azure s probíhající produkční systém místní. Při vyjmutí přes produkčního prostředí se vypne a je k jistotu, že je teď zálohy protokolu transakce poslední a nejnovější přenášet pro nasazení DBMS v Azure. Pak je nasazení DBMS v Azure otevře pro produkční prostředí.  



### <a name="database-mirroring"></a>Zrcadlení databáze
Zrcadlení databáze, protože SAP (viz poznámka SAP [965908]) spoléhá na definování partnerem převzetí služeb při selhání v připojovacím řetězci SAP. Případech, mezi různými místy předpokládáme, že dva virtuální počítače ve stejné doméně a že instance systému SQL Server kontextu dva uživatele jsou spuštěny v uživatel domény a máte dostatečná oprávnění v dvě instance systému SQL Server používané. Proto se mezi typické místní instalace/konfigurace liší nastavení zrcadlení databáze v Azure.

Od čistě cloudových nasazení nejjednodušší způsob je má jiné nastavení domény v Azure a mají tyto virtuální počítače DBMS (a ideálně vyhrazených virtuálních počítačích SAP) v rámci jedné domény.

Pokud domény není možné, jeden také používat certifikáty pro databázi zrcadlení koncové body podle postupu popsaného tady: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Kurz k nastavení zrcadlení databáze v Azure najdete tady: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>Always On systému SQL Server
Always On se podporuje pro SAP v místním (viz poznámka SAP [1772688]), se podporuje v kombinaci s řešením SAP v Azure. Existují některé speciální aspekty nasazování SQL serveru naslouchací proces skupiny dostupnosti (nesmí být zaměňovány dostupnosti Azure) od Azure v tomto okamžiku nepovoluje vytvoření objektu AD/DNS je možné místní. Proto některé jinou instalační kroky jsou nezbytné k překonání konkrétní chování Azure.

Některé důležité informace o použití naslouchací proces skupiny dostupnosti jsou:

* Použití naslouchací proces skupiny dostupnosti je možné pouze v systémech Windows Server 2012 nebo vyšší jako hostovaný operační systém virtuálního počítače. Systém Windows Server 2012 je třeba Ujistěte se, že se použije tato oprava: <https://support.microsoft.com/kb/2854082> 
* Pro Windows Server 2008 R2, tato oprava neexistuje a Always On by bylo potřeba použít stejným způsobem jako zrcadlení databáze tak, že zadáte partnerem převzetí služeb při selhání v řetězci připojení (provádí přes SAP default.pfl parametr databází/mss/server – viz poznámka SAP [965908]).
* Při použití naslouchací proces skupiny dostupnosti, virtuální počítače databáze musí být připojen k vyhrazené nástroje pro vyrovnávání zatížení. Předejdete tak, že Azure je přiřazení nové IP adresy v případech, kdy oba virtuální počítače mimochodem se vypnout, jeden by měl přiřadit statické IP adresy pro síťová rozhraní z těchto virtuálních počítačů v konfiguraci Always On (definování statická IP adresa je popsané v [to] [ virtual-networks-reserved-private-ip] článek)
* Existují speciální kroků potřebných při vytváření konfigurace clusteru služby WSFC, kdy cluster potřebuje speciální IP adresu přiřadit, protože Azure s její aktuální funkce by přiřadit název clusteru stejné IP adresy jako uzel clusteru je vytvořen na. To znamená, že ruční krok je nutné provést přiřadit jinou IP adresu clusteru.
* Naslouchací proces skupiny dostupnosti bude v Azure vytvořit pomocí koncových bodů protokolu TCP/IP, které jsou přiřazeny k virtuálním počítačům s primární a sekundární repliky skupiny dostupnosti.
* Může být potřeba zabezpečené tyto koncové body pomocí seznamů ACL.

Podrobnou dokumentaci k nasazení Always On s využitím SQL serveru na virtuálních počítačích Azure se uvádí jako:

- [Představení SQL serveru skupiny dostupnosti AlwaysOn na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Konfigurace skupiny dostupnosti AlwaysOn na virtuálních počítačích, které jsou v různých oblastech Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Konfigurace nástroje pro vyrovnávání zatížení pro skupinu dostupnosti AlwaysOn v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Při konfiguraci nástroje pro vyrovnávání zatížení Azure pro virtuální IP adresu naslouchacího procesu skupiny dostupnosti, ujistěte se, že je nakonfigurovaný DirectServerReturn. konfigurování této možnosti se sníží sítě latence doby odezvy mezi aplikační vrstvě SAP a vrstvu DBMS. 

SQL serveru Always On je nejběžnější používané vysokou dostupnost a funkce pro zotavení po havárii používá pro nasazení úloh SAP v Azure. Většina zákazníků používá AlwaysOn pro vysokou dostupnost v rámci jedné oblasti Azure. Pokud je nasazení aplikace s omezeným přístupem k jenom dva uzly, máte dvě možnosti pro připojení:

- Pomocí naslouchacího procesu skupiny dostupnosti. Pro naslouchací proces skupiny dostupnosti jsou nutné k nasazení služby Azure load balancer. To je obvykle výchozí metoda nasazení. Aplikace SAP bude nakonfigurován připojení proti naslouchacím procesem skupiny dostupnosti a není jeden uzel
- Pomocí parametrů připojení zrcadlení databáze SQL serveru. V takovém případě musíte nakonfigurovat připojení aplikace SAP způsobem, kdy jsou pojmenované oba názvy uzlů. Přesné údaje o takové konfigurace na straně SAP jsou uvedené v Poznámka SAP [#965908](https://launchpad.support.sap.com/#/notes/965908). Když použijete tuto možnost, budete mít není potřeba konfigurace naslouchacího procesu skupiny dostupnosti. A s ním žádné Azure služba Vyrovnávání zatížení pro zajištění vysoké dostupnosti systému SQL Server. V důsledku toho je nižší latence sítě mezi aplikační vrstvě SAP a vrstvu DBMS, protože není směrovat příchozí provoz na instanci systému SQL Server prostřednictvím služby Azure load balancer. Ale možná Vzpomínáte, tato možnost funguje jenom v případě omezení vaší skupiny dostupnosti rozložit dvě instance. 

Mnoho zákazníků se využívá funkce SQL serveru Always On pro fungování obnovy po havárii další mezi oblastmi Azure. Několik zákazníků také použít možnost provádět zálohování sekundární repliky. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server transparentní šifrování dat
Existuje několik zákazníci, kteří používají SQL Server [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) při nasazení systému SQL Server jejich SAP databáze v Azure. Funkce pro transparentní šifrování dat SQL Server se plně podporuje SAP (viz poznámka SAP [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Použití systému SQL Server transparentní šifrování dat
V případech, kde provádíte heterogenní migraci z jiného systému DBMS, běží v místním Windows/SQL Server provozovaný v Azure měli byste vytvořit prázdnou cílovou databázi v systému SQL Server předem. Jako další krok by použít funkce transparentní šifrování dat SQL serveru. Přestože stále běží vaše produkční systém místní. Z důvodů, proč chcete provést v tomto pořadí je, že proces šifrování prázdná databáze může chvíli trvat poměrně. Import procesy SAP by potom importovat data do šifrovaného databázového během fáze výpadek. Nároky na import do šifrovaného databázového má způsobem nižší vliv času než šifrování databáze po fázi exportu v seznamu fází doby. Negativní dojde, pokud při pokusu o použití transparentní šifrování dat s SAP úlohu spuštěnou na databázi. Doporučení je proto zpracuje nasazení transparentní šifrování dat jako aktivita, kterou je potřeba provést bez úloh SAP v konkrétní databázi.

V případech, kde můžete přesunout databáze SQL serveru SAP z místního do Azure doporučujeme, na které infrastruktury můžete získat šifrování použít nejrychlejší testování. To mít na paměti tyto skutečnosti:

- Nejde definovat, kolik vlákna umožňují použít šifrování dat v databázi. Počet vláken je majorly závisí na počtu diskové svazky, které jsou rozloženy na soubory protokolu a data systému SQL Server. Znamená to zřetelný svazky (písmena jednotek), další vlákna budou zapojení paralelně provádět šifrování. Taková konfigurace trochu rozporu s starší návrh konfigurace disku na vytváření jednu nebo menší počet prostory úložiště pro soubory databáze serveru SQL Server na virtuálních počítačích Azure. Konfigurace s malý počet svazků by mohlo dojít k malý počet vláken v šifrování. Jedno vlákno šifrování je pro čtení rozsahů o velikosti 64KB, šifruje je a pak zapsat záznam do souboru protokolu transakcí, o tom, že se rozsah zašifroval. V důsledku zatížení transakční protokol je střední.
- Ve starších verzích systému SQL Server komprese záloh nedosáhli efektivitu už při šifrování databáze SQL serveru. Toto chování může vyvíjet na problém. Pokud váš plán proběhlo k šifrování vašeho serveru SQL Server database místní a potom zkopírujte zálohy do Azure za účelem obnovení databáze v Azure. Komprese záloh systému SQL Server se obvykle dosahuje kompresního poměru faktoru 4.
- SQL Server s SQL serverem 2016 zavádí nové funkce, které umožňuje komprese šifrovaným databázím a efektivním způsobem. Zobrazit [tento blogy](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) některé podrobnosti.
 
Považuje šifrování TDE bez na malé úlohy SAP pouze aplikace, měli byste otestovat ve vaší konkrétní konfiguraci na to, zda je lepší použít transparentní šifrování dat na vašich SAP databáze v místním nebo k tomu v Azure. V Azure jistě máte větší flexibilitu z hlediska over-pass-the zřizování infrastruktury a zmenšit infrastruktury po transparentní šifrování dat je teď.

### <a name="using-azure-key-vault"></a>Použití Azure Key Vault
Azure nabízí službu [služby Key Vault](https://azure.microsoft.com/services/key-vault/) pro ukládání šifrovacích klíčů. SQL Server na druhé straně nabízejí konektor využívat jako úložiště pro certifikáty transparentní šifrování dat služby Azure Key Vault.

Další podrobnosti o použití Azure Key Vault pro transparentní šifrování dat SQL Server jsou uvedeny jako:

- [Rozšiřitelná Správa klíčů s využitím Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [SQL Server TDE rozšiřitelná Správa klíčů s využitím Azure Key Vault – pokynů k instalaci](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [SQL Server konektoru údržby a řešení potíží](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Další dotazy zákazníků týkající se SQL Server transparentní šifrování dat – transparentní šifrování dat a službou Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Pomocí SQL serveru transparentní šifrování dat, zejména s Azure key Vault, se doporučuje použít nejnovější opravy SQL Server 2014, SQL Server 2016 a SQL serverem 2017. Důvodem je, že na základě názorů zákazníků, optimalizací a oprav je teď použitý pro kód. Jako příklad, zkontrolujte [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Obecné systém SQL Server pro SAP v Azure souhrn
Existuje mnoho doporučení v tomto průvodci a doporučujeme že si přečíst je více než jednou před plánování nasazení vašeho řešení Azure. Obecně platí ale musíte splnit nejvyšší obecné DBMS na doporučení týkající se Azure:

1. Použijte nejnovější vydání systému DBMS, jako je SQL Server 2017, obsahující většinu výhody v Azure. 
2. Pečlivě naplánujte prostředí systému SAP v Azure pro data souboru rozložení a omezení Azure:
   * Není má příliš mnoho disků, ale dostatek Ujistěte se, že můžete oslovit požadované vstupně-výstupních operací.
   * Pokud nechcete používat službu Managed Disks, mějte na paměti, že vstupně-výstupních operací se také omezené na jeden účet úložiště Azure a účty úložiště jsou omezené v každém předplatném Azure ([podrobnosti][azure-subscription-service-limits]). 
   * Pouze stripe mezi disky. Pokud potřebujete, abyste dosáhli vyšší propustnosti.
3. Software nemá nikdy instalovat nebo umístěte všechny soubory, které vyžadují trvalost na jednotku D:\, jako je mimo trvalé a nic na této jednotce dojde ke ztrátě při restartování Windows.
4. Nepoužívejte ukládání do mezipaměti na disku pro úložiště Azure úrovně Standard.
5. Nepoužívejte geograficky replikovaného Azure standardní účty Azure Storage.  Místně redundantní použijte pro úlohy DBMS.
6. Pomocí řešení od dodavatele DBMS HA/DR k replikaci dat z databáze.
7. Vždy použít překlad, nespoléhejte na IP adresách.
8. Díky transparentní šifrování dat SQL serveru, použijte nejnovější opravy serveru SQL Server.
9. Používejte nejvyšší komprese databáze, je to možné. Což je stránka komprese pro SQL Server.
10. Buďte opatrní, pomocí Image SQL serveru z Azure Marketplace. Pokud používáte SQL Server, jeden, musíte změnit kolací instance před instalací jakékoli systému SAP NetWeaver na něm.
11. Instalace a konfigurace monitorování SAP hostitele pro Azure, jak je popsáno v [Průvodce nasazením][deployment-guide].