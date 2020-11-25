---
title: Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure
description: Průvodce vysokou dostupností pro SAP NetWeaver v Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b506ada0bc072a4174de6f884d1814a63f1f93ca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012506"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure

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
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:https://docs.microsoft.com/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:dbms_guide_general.md
[dbms-guide-2.1]:dbms_guide_general.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms_guide_general.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms_guide_general.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms_guide_general.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms_guide_general.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms_guide_general.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms_guide_general.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms_guide_general.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms_guide_general.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms_guide_general.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms_guide_general.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms_guide_general.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms_guide_general.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms_guide_general.md#642f746c-e4d4-489d-bf63-73e80177a0a8

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

[deploy-template-cli]:../../../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../../../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../../../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:../../virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:../../virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:../../virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:../../virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[sap-ha-guide]:high-availability-guide.md
[sap-ha-guide-1]:high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c
[sap-ha-guide-2]:high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-3]:high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1
[sap-ha-guide-3.1]:high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686
[sap-ha-guide-3.2]:high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860
[sap-ha-guide-4]:high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-4.1]:high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2
[sap-ha-guide-5]:high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275
[sap-ha-guide-5.1]:high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592
[sap-ha-guide-5.2]:high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd
[sap-ha-guide-6]:high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a
[sap-ha-guide-6.1]:high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341
[sap-ha-guide-6.2]:high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb
[sap-ha-guide-7]:high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa
[sap-ha-guide-7.1]:high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e
[sap-ha-guide-7.2]:high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db
[sap-ha-guide-7.2.1]:high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51
[sap-ha-guide-7.3]:high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027
[sap-ha-guide-7.4]:high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816
[sap-ha-guide-8]:high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.2]:high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310
[sap-ha-guide-8.3]:high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-ha-guide-8.4]:high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ha-guide-8.5]:high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f
[sap-ha-guide-8.6]:high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-ha-guide-8.7]:high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-ha-guide-8.8]:high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.10]:high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.2]:high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca
[sap-ha-guide-8.12.2.1]:high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855
[sap-ha-guide-8.12.2.2]:high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d
[sap-ha-guide-8.12.3]:high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-ha-guide-9.1.2]:high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0
[sap-ha-guide-9.1.3]:high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-ha-guide-9.1.4]:high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-ha-guide-9.1.5]:high-availability-guide.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-ha-guide-9.2]:high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7
[sap-ha-guide-9.3]:high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748
[sap-ha-guide-9.4]:high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-ha-guide-9.5]:high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5
[sap-ha-guide-9.6]:high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772
[sap-ha-guide-10]:high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9
[sap-ha-guide-10.1]:high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445
[sap-ha-guide-10.2]:high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916
[sap-ha-guide-10.3]:high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (Konfigurace s vysokou dostupností pro SAP multi-SID)


[sap-ha-guide-figure-1000]:media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (Matice dostupnosti produktu SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
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
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/management/overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:../../virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md
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
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


Azure Virtual Machines je řešení pro organizace, které potřebují výpočetní prostředky, úložiště a síťové prostředky, a to za minimální dobu a bez zdlouhavých cyklů zásobování. K nasazení klasických aplikací, jako je například SAP NetWeaver ABAP, Java a ABAP a Java Stack, můžete použít Azure Virtual Machines. Našíří spolehlivost a dostupnost bez dalších místních prostředků. Azure Virtual Machines podporuje připojení mezi různými místy, takže Azure Virtual Machines můžete integrovat do místních domén organizace, privátních cloudů a systému SAP na šířku.

V tomto článku se zaměříme na kroky, které můžete provést při nasazení systémů SAP s vysokou dostupností v Azure pomocí modelu nasazení Azure Resource Manager. Provede vás těmito hlavními úlohami:

* Vyhledejte správné poznámky ke službě SAP a Příručky k instalaci uvedené v části [Resources (prostředky][sap-ha-guide-2] ). Tento článek doplňuje dokumentaci k instalaci SAP a poznámky SAP, které jsou primárními prostředky, které vám pomůžou s instalací a nasazením softwaru SAP na konkrétní platformy.
* Přečtěte si o rozdílech mezi modelem nasazení Azure Resource Manager a modelem nasazení Azure Classic.
* Přečtěte si o režimech kvora clusteringu s podporou převzetí služeb při selhání ve Windows serveru, abyste mohli vybrat model, který je pro nasazení Azure nejvhodnější.
* Přečtěte si o sdílených úložištích služby Windows Server Failover Clustering ve službách Azure.
* Naučte se, jak chránit součásti s jedním bodem selhání, jako je třeba pokročilé programování obchodních aplikací (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) a systémy správy databáze (DBMS) a redundantní komponenty jako aplikační Server SAP v Azure.
* Postupujte podle podrobných příkladů instalace a konfigurace systému SAP s vysokou dostupností v clusteru clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure pomocí Azure Resource Manager.
* Seznamte se s dalšími kroky potřebnými k použití clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure, ale které nejsou potřeba v místním nasazení.

V rámci zjednodušení nasazení a konfigurace používáme v tomto článku šablony SAP pro Správce prostředků s vysokou dostupností. Šablony automatizují nasazení celé infrastruktury, kterou potřebujete pro systém SAP s vysokou dostupností. Infrastruktura také podporuje určení velikosti SAP (Application Performance Standard) SAP pro systém SAP.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Požadovaný
Než začnete, ujistěte se, že splňujete požadavky popsané v následujících částech. Nezapomeňte také zkontrolovat všechny prostředky uvedené v části [Resources (prostředky][sap-ha-guide-2] ).

V tomto článku používáme šablony Azure Resource Manager pro [NetWeaver SAP pro SAP](https://github.com/Azure/azure-quickstart-templates/). Užitečný přehled šablon najdete v tématu [šablony SAP Azure Resource Manager](/archive/blogs/saponsqlserver/azure-quickstart-templates-for-sap).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Prostředky
Tyto články zahrnují nasazení SAP v Azure:

* [Plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP NetWeaver][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP NetWeaver][dbms-guide]
* [Vysoká dostupnost Azure Virtual Machines pro SAP NetWeaver (Tato příručka)][sap-ha-guide]

> [!NOTE]
> Kdykoli je to možné, poskytneme vám odkaz na odkazovací příručku k instalaci SAP (viz [Příručky k instalaci SAP][sap-installation-guides]). V případě požadavků a informací o procesu instalace je vhodné pečlivě přečíst Příručky k instalaci SAP NetWeaver. Tento článek se zabývá pouze konkrétními úkoly pro systémy SAP NetWeaver, které můžete používat s Azure Virtual Machines.
>
>

Tyto poznámky SAP souvisejí s tématem SAP v Azure:

| Číslo poznámky | Nadpis |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: podporované produkty a velikost |
| [2015553] |SAP v Microsoft Azure: požadavky na podporu |
| [1999351] |Vylepšené monitorování Azure pro SAP |
| [2178632] |Klíčové metriky monitorování pro SAP v Microsoft Azure |
| [1999351] |Virtualizace ve Windows: rozšířené monitorování |
| [2243692] |Použití Azure SSD úrovně Premium Storage pro instanci SAP DBMS |

Přečtěte si další informace o [omezeních předplatných Azure][azure-resource-manager/management/azure-subscription-service-limits-subscription], včetně obecných výchozích omezení a maximálních omezení.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP s vysokou dostupností pomocí Azure Resource Manager vs. model nasazení Azure Classic
Modely nasazení Azure Resource Manager a Azure Classic se liší v následujících oblastech:

- Skupiny prostředků
- Závislost interního nástroje pro vyrovnávání zatížení Azure ve skupině prostředků Azure
- Podpora scénářů multi-SID pro SAP

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Skupiny prostředků
V Azure Resource Manager můžete použít skupiny prostředků ke správě všech prostředků aplikace ve vašem předplatném Azure. Integrovaný přístup, ve skupině prostředků, má všechny prostředky stejný životní cyklus. Například všechny prostředky jsou vytvořeny současně a jsou odstraněny ve stejnou dobu. Další informace o [skupinách prostředků](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Závislost interního nástroje pro vyrovnávání zatížení Azure ve skupině prostředků Azure

V modelu nasazení Azure Classic existuje závislost mezi interním nástrojem pro vyrovnávání zatížení Azure (Azure Load Balancer službou) a skupinou cloudových služeb. Každý interní nástroj pro vyrovnávání zatížení potřebuje jednu skupinu cloudových služeb.

V Azure Resource Manager nepotřebujete pro použití Azure Load Balancer skupinu prostředků Azure. Prostředí je jednodušší a flexibilnější.

### <a name="support-for-sap-multi-sid-scenarios"></a>Podpora scénářů multi-SID pro SAP

V Azure Resource Manager můžete v jednom clusteru nainstalovat několik instancí identifikátoru systému SAP (SID) ASCS/SCS. Instance s více identifikátory SID jsou možné z důvodu podpory více IP adres pro každý interní nástroj pro vyrovnávání zatížení Azure.

Pokud chcete použít model nasazení Azure Classic, postupujte podle pokynů popsaných v tématu [SAP NetWeaver v Azure: clusteringu instancí SAP ASCS/SCS pomocí clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure s využitím DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Důrazně doporučujeme použít model nasazení Azure Resource Manager pro vaše instalace SAP. Nabízí spoustu výhod, které nejsou k dispozici v modelu nasazení Classic. Přečtěte si další informace o [modelech nasazení][virtual-machines-azure-resource-manager-architecture-benefits-arm]Azure.   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Clustering s podporou převzetí služeb při selhání Windows serveru
Clustering s podporou převzetí služeb při selhání ve Windows serveru je základem instalace ASCS/SCS SAP s vysokou dostupností a DBMS ve Windows.

Cluster s podporou převzetí služeb při selhání je skupina s 1 + n nezávislými servery (uzly), které vzájemně spolupracují za účelem zvýšení dostupnosti aplikací a služeb. Pokud dojde k selhání uzlu, clustering s podporou převzetí služeb při selhání ve Windows serveru vypočítá počet selhání, ke kterým může dojít při údržbě clusteru v pořádku, aby poskytoval aplikace a služby. Pro zajištění clusteringu s podporou převzetí služeb při selhání můžete vybrat z různých režimů kvora.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Režimy kvora
Když použijete Clustering s podporou převzetí služeb při selhání Windows serveru, můžete vybrat ze čtyř režimů kvora:

* **Většina uzlů** Každý uzel clusteru může hlasovat. Cluster funguje jenom s většinou hlasů, to znamená s více než polovičními hlasy. Tuto možnost doporučujeme pro clustery, které mají nerovnoměrný počet uzlů. Například tři uzly v clusteru se sedmi uzly můžou selhat, ale cluster stále dosahuje většiny a pokračuje v běhu.  
* **Většina uzlů a disků**. Každý uzel a určený disk (určující disk) v úložišti clusteru může hlasovat, když jsou k dispozici a v komunikaci. Cluster funguje jenom ve většině hlasů, to znamená s více než polovičními hlasy. Tento režim dává smysl v prostředí clusteru s sudým počtem uzlů. Pokud je polovina uzlů a disků v režimu online, cluster zůstane v dobrém stavu.
* **Většina uzlů a sdílených souborů**. Každý uzel plus určenou sdílenou složku (určující sdílenou složku), kterou může správce vytvořit, může hlasovat bez ohledu na to, zda jsou uzly a sdílená složka k dispozici a v komunikaci. Cluster funguje jenom ve většině hlasů, to znamená s více než polovičními hlasy. Tento režim dává smysl v prostředí clusteru s sudým počtem uzlů. Je podobný režimu majoritního panelu a disku, ale používá sdílené složky s kopií clusteru místo disku s kopií clusteru. Tento režim je snadno implementován, ale pokud samotná sdílená složka není vysoce dostupná, může se stát, že se stane jediným bodem selhání.
* **Bez většiny: jenom disk**. Cluster má kvorum, pokud je k dispozici jeden uzel a v komunikaci s konkrétním diskem v úložišti clusteru. Ke clusteru se mohou připojit pouze uzly, které jsou zároveň v komunikaci s tímto diskem. Doporučujeme nepoužívat tento režim.

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Clustering s podporou převzetí služeb při selhání Windows serveru v místním prostředí
Obrázek 1 znázorňuje cluster dvou uzlů. Pokud síťové připojení mezi uzly selže a oba uzly zůstanou v provozu, disk nebo sdílená složka kvora určí, který uzel bude nadále poskytovat aplikace a služby clusteru. Uzel, který má přístup k disku nebo sdílené složce kvora, je uzel, který zajišťuje, aby služby pokračovaly.

Vzhledem k tomu, že tento příklad používá cluster se dvěma uzly, používáme režim kvora Většina uzlů a sdílených souborů. Většina uzlů a disků je zároveň platnou možností. V produkčním prostředí doporučujeme použít disk kvora. K zajištění vysoké dostupnosti můžete použít technologii sítě a systému úložiště.

![Obrázek 1: příklad konfigurace clusteringu s podporou převzetí služeb při selhání Windows serveru pro SAP ASCS/SCS v Azure][sap-ha-guide-figure-1000]

_**Obrázek 1:** Příklad konfigurace clusteringu s podporou převzetí služeb při selhání Windows serveru pro SAP ASCS/SCS v Azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Sdílené úložiště
Obrázek 1 také ukazuje cluster sdíleného úložiště se dvěma uzly. V místním clusteru sdíleného úložiště vyhledá všechny uzly v clusteru sdílené úložiště. Blokovací mechanizmus chrání data před poškozením. Všechny uzly můžou zjistit, jestli se jiný uzel nezdařil. Pokud jeden uzel neprojde, zbývající uzel převezme vlastnictví prostředků úložiště a zajistí dostupnost služeb.

> [!NOTE]
> Pro zajištění vysoké dostupnosti s některými aplikacemi DBMS, jako je SQL Server, nepotřebujete sdílené disky. SQL Server Always On replikuje DBMS data a soubory protokolu z místního disku jednoho uzlu clusteru na místní disk jiného uzlu clusteru. V takovém případě konfigurace clusteru Windows nepotřebuje sdílený disk.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Překlad sítí a názvů
Klientské počítače dosáhnou clusteru přes virtuální IP adresu a název virtuálního hostitele, který poskytuje server DNS. Místní uzly a server DNS můžou zpracovávat víc IP adres.

Při typické instalaci používáte dvě nebo víc síťových připojení:

* Vyhrazené připojení k úložišti
* Síťové připojení k internímu clusteru pro prezenční signál
* Veřejná síť, kterou klienti používají pro připojení ke clusteru

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Clustering s podporou převzetí služeb při selhání Windows serveru v Azure
V porovnání s nasazením holého nebo privátního cloudu vyžaduje Azure Virtual Machines další kroky ke konfiguraci clusteringu s podporou převzetí služeb při selhání Windows serveru. Když vytváříte sdílený disk clusteru, je potřeba nastavit několik IP adres a názvy virtuálních hostitelů pro instanci SAP ASCS/SCS.

V tomto článku se zabýváme klíčovými koncepty a dalšími kroky potřebnými k sestavení clusteru centrálních služeb SAP s vysokou dostupností v Azure. Ukážeme vám, jak nastavit nástroj třetí strany s nástrojem datakeep a jak nakonfigurovat interní nástroj pro vyrovnávání zatížení Azure. Pomocí těchto nástrojů můžete vytvořit cluster s podporou převzetí služeb při selhání s Windows s určující sdílenou složkou v Azure.

![Obrázek 2: Konfigurace clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure bez sdíleného disku][sap-ha-guide-figure-1001]

_**Obrázek 2:** Konfigurace clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure bez sdíleného disku_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Sdílený disk v Azure s využitím s datakeeping
Pro instanci SAP ASCS/SCS pro vysokou dostupnost potřebujete sdílené úložiště clusteru. Od září 2016 nenabízí Azure sdílené úložiště, které můžete použít k vytvoření sdíleného clusteru úložiště. K vytvoření zrcadleného úložiště, které simuluje sdílené úložiště clusteru, můžete použít software třetí strany s clusterem DataKeeper Edition. Řešení s poskytuje synchronní replikaci dat v reálném čase. Tímto způsobem můžete vytvořit prostředek sdíleného disku pro cluster:

1. Připojte další virtuální pevný disk Azure ke každému virtuálnímu počítači (VM) v konfiguraci clusteru Windows.
2. Spusťte rutinu s rutinou datakeep-cluster na obou uzlech virtuálních počítačů.
3. Nakonfigurujte s Clusterovou verzí s DataKeeper tak, aby zrcadlí obsah dalšího připojeného svazku VHD ze zdrojového virtuálního počítače na další svazek připojený k virtuálnímu pevnému disku v cílovém virtuálním počítači. S tím, že služba datakeeps vyabstrakce zdrojové a cílové místní svazky a pak je prezentuje clusteringu Windows Server s podporou převzetí služeb při selhání jako jeden sdílený disk.

Získejte další informace o [Datakeepu](https://us.sios.com/products/datakeeper-cluster/).

![Obrázek 3: Konfigurace clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure s využitím DataKeeper][sap-ha-guide-figure-1002]

_**Obrázek 3:** Konfigurace clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure s využitím_

> [!NOTE]
> Pro zajištění vysoké dostupnosti s některými produkty DBMS, jako je SQL Server, nepotřebujete sdílené disky. SQL Server Always On replikuje DBMS data a soubory protokolu z místního disku jednoho uzlu clusteru na místní disk jiného uzlu clusteru. V takovém případě konfigurace clusteru Windows nepotřebuje sdílený disk.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Překlad názvů v Azure
Cloudová platforma Azure nenabízí možnost konfigurace virtuálních IP adres, jako jsou například plovoucí IP adresy. K nastavení virtuální IP adresy pro dosažení prostředku clusteru v cloudu potřebujete alternativní řešení.
Azure obsahuje interní nástroj pro vyrovnávání zatížení ve službě Azure Load Balancer. S interním nástrojem pro vyrovnávání zatížení klienti dosáhnou clusteru přes virtuální IP adresu clusteru.
Interní nástroj pro vyrovnávání zatížení musíte nasadit ve skupině prostředků, která obsahuje uzly clusteru. Pak nakonfigurujte všechna nezbytná pravidla předávání portů s porty sondy interního nástroje pro vyrovnávání zatížení.
Klienti se mohou připojit prostřednictvím názvu virtuálního hostitele. Server DNS přeloží IP adresu clusteru a interní nástroj pro vyrovnávání zatížení zpracovává přesměrování portu na aktivní uzel clusteru.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Vysoká dostupnost SAP NetWeaver v infrastruktuře Azure jako služba (IaaS)
Chcete-li dosáhnout vysoké dostupnosti aplikace SAP, například pro softwarové komponenty SAP, je nutné chránit následující komponenty:

* Instance aplikačního serveru SAP
* Instance SAP ASCS/SCS
* Server DBMS

Další informace o ochraně komponent SAP ve scénářích s vysokou dostupností najdete v tématu [plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver](planning-guide.md).

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Aplikační Server SAP s vysokou dostupností
Obvykle nepotřebujete pro instance aplikačního serveru SAP a dialogových serverů SAP konkrétní řešení s vysokou dostupností. Dosáhnete vysoké dostupnosti redundancí a nakonfigurujete více instancí dialogů v různých instancích služby Azure Virtual Machines. Ve dvou instancích služby Azure Virtual Machines byste měli mít nainstalované aspoň dvě instance aplikace SAP.

![Obrázek 4: aplikační Server SAP s vysokou dostupností][sap-ha-guide-figure-2000]

_**Obrázek 4:** Aplikační Server SAP s vysokou dostupností_

Všechny virtuální počítače, které hostují instance aplikačního serveru SAP, musíte umístit do stejné skupiny dostupnosti Azure. Skupina dostupnosti Azure zajišťuje:

* Všechny virtuální počítače jsou součástí stejné upgradovací domény. Upgradovací doména například zajistí, že virtuální počítače nejsou aktualizovány ve stejnou dobu během plánovaného výpadku údržby.
* Všechny virtuální počítače jsou součástí stejné domény selhání. Doména selhání například zajišťuje nasazení virtuálních počítačů, aby nedošlo k žádnému jedinému bodu selhání, který by měl mít vliv na dostupnost všech virtuálních počítačů.

Přečtěte si další informace o tom, jak [spravovat dostupnost virtuálních počítačů][virtual-machines-manage-availability].

Vzhledem k tomu, že účet Azure Storage je potenciálním jediným bodem selhání, je důležité mít aspoň dva účty úložiště Azure, ve kterých jsou distribuované aspoň dva virtuální počítače. V ideálním případě by se disky každého virtuálního počítače, na kterém běží instance dialogového okna SAP, nasadily v jiném účtu úložiště.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Instance SAP ASCS/SCS s vysokou dostupností
Obrázek 5 je příkladem instance SAP ASCS/SCS s vysokou dostupností.

![Obrázek 5: instance SAP ASCS/SCS s vysokou dostupností][sap-ha-guide-figure-2001]

_**Obrázek 5:** Instance SAP ASCS/SCS s vysokou dostupností_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Vysoká dostupnost instance SAP ASCS/SCS s clusteringem s podporou převzetí služeb při selhání Windows serveru v Azure
V porovnání s nasazením holého nebo privátního cloudu vyžaduje Azure Virtual Machines další kroky ke konfiguraci clusteringu s podporou převzetí služeb při selhání Windows serveru. Pokud chcete vytvořit cluster s podporou převzetí služeb při selhání Windows, budete potřebovat sdílený disk clusteru, několik IP adres, několik názvů virtuálních hostitelů a interní nástroj pro vyrovnávání zatížení Azure pro clustering instance SAP ASCS/SCS. Podrobněji o tom probereme dále v článku.

![Obrázek 6: Clustering s podporou převzetí služeb při selhání ve Windows serveru pro konfiguraci SAP ASCS/SCS v Azure s využitím s datakeeping][sap-ha-guide-figure-1002]

_**Obrázek 6:** Clustering s podporou převzetí služeb při selhání ve Windows serveru pro konfiguraci SAP ASCS/SCS v Azure s využitím s využitím datakeep_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Instance DBMS s vysokou dostupností
DBMS je také jedním kontaktním bodem v systému SAP. Budete je muset chránit pomocí řešení s vysokou dostupností. Obrázek 7 ukazuje SQL Server vždy v řešení vysoké dostupnosti v Azure s clusteringem s podporou převzetí služeb při selhání Windows serveru a interním nástrojem pro vyrovnávání zatížení Azure. SQL Server Always On replikuje DBMS data a soubory protokolu pomocí vlastní replikace systému DBMS. V takovém případě nepotřebujete sdílené disky clusteru, což zjednodušuje celou instalaci.

![Obrázek 7: příklad nástroje SAP DBMS s vysokou dostupností s SQL Serverem Always On][sap-ha-guide-figure-2003]

_**Obrázek 7:** Příklad nástroje SAP DBMS s vysokou dostupností s SQL Server Always On_

Další informace o clusteringu SQL Server v Azure s využitím modelu nasazení Azure Resource Manager najdete v těchto článcích:

* [Ruční konfigurace skupiny dostupnosti Always On v Azure Virtual Machines pomocí Správce prostředků][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurace interního nástroje pro vyrovnávání zatížení Azure pro skupinu dostupnosti Always On v Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Kompletní scénáře nasazení s vysokou dostupností

### <a name="deployment-scenario-using-architectural-template-1"></a>Scénář nasazení pomocí šablony architektury 1

Obrázek 8 ukazuje příklad architektury s vysokou dostupností SAP NetWeaver v Azure pro **jeden** systém SAP. Tento scénář se nastaví takto:

- Pro instanci SAP ASCS/SCS se používá jeden vyhrazený cluster.
- Pro instanci systému DBMS se používá jeden vyhrazený cluster.
- Instance aplikačního serveru SAP se nasazují ve vlastních vyhrazených virtuálních počítačích.

![Obrázek 8: architektura architektury s vysokou dostupností SAP s vyhrazeným clusterem pro ASCS/SCS a DBMS][sap-ha-guide-figure-2004]

_**Obrázek 8:** Šablona architektury SAP pro vysokou dostupnost 1, vyhrazené clustery pro ASCS/SCS a DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scénář nasazení pomocí šablony architektury 2

Obrázek 9 ukazuje příklad architektury s vysokou dostupností SAP NetWeaver v Azure pro **jeden** systém SAP. Tento scénář se nastaví takto:

- Jeden vyhrazený cluster se používá pro instanci SAP ASCS/ **SCS i pro** systém DBMS.
- Instance aplikačního serveru SAP se nasazují ve vlastních vyhrazených virtuálních počítačích.

![Obrázek 9: architektura architektury SAP pro vysokou dostupnost 2, s vyhrazeným clusterem pro ASCS/SCS a vyhrazeným clusterem pro systémy DBMS][sap-ha-guide-figure-2005]

_**Obrázek 9:** Architektura architektury SAP pro vysokou dostupnost 2, s vyhrazeným clusterem pro ASCS/SCS a vyhrazeným clusterem pro systémy DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scénář nasazení pomocí šablony architektury 3

Obrázek 10 ukazuje příklad architektury s vysokou dostupností SAP NetWeaver v Azure pro **dva** systémy SAP s &lt; SID1 &gt; a &lt; SID2 &gt; . Tento scénář se nastaví takto:

- Jeden vyhrazený **cluster se používá pro instanci** SID1 SAP ASCS/SCS *a* instanci SAP ASCS/SCS SID2 (jeden cluster).
- Jeden vyhrazený cluster se používá pro DBMS SID1 a další vyhrazený cluster se používá pro DBMS SID2 (dva clustery).
- Instance aplikačního serveru SAP pro systém SAP SID1 mají vlastní vyhrazené virtuální počítače.
- Instance aplikačního serveru SAP pro systém SAP SID2 mají vlastní vyhrazené virtuální počítače.

![Obrázek 10: architektura architektury SAP s vysokou dostupností 3, s vyhrazeným clusterem pro různé instance ASCS/SCS][sap-ha-guide-figure-6003]

_**Obrázek 10:** Architektura architektury s vysokou dostupností SAP s vyhrazeným clusterem pro různé instance ASCS/SCS_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Příprava infrastruktury

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Příprava infrastruktury na šablonu architektury 1
Šablony Azure Resource Manager pro SAP usnadňují nasazení požadovaných prostředků.

Šablony tří vrstev v Azure Resource Manager také podporují scénáře s vysokou dostupností, například v architektuře architektury 1, která má dva clustery. Každý cluster představuje jediný bod SAP pro SAP ASCS/SCS a DBMS.

Tady je postup, kde můžete získat šablony Azure Resource Manager pro ukázkový scénář, který popisujeme v tomto článku:

* [Obrázek Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/)

Příprava infrastruktury pro architekturu šablony 1:

- V Azure Portal v okně **parametry** v poli **SYSTEMAVAILABILITY** vyberte **ha**.

  ![Obrázek 11: nastavení parametrů Azure Resource Manager pro vysokou dostupnost pro SAP][sap-ha-guide-figure-3000]

_**Obrázek 11:** Nastavení Azure Resource Manager parametrů pro vysokou dostupnost SAP_


  Šablony vytvoří:

  * **Virtuální počítače**:
    * Virtuální počítače aplikačního serveru SAP: <*SAPSystemSID*>-di-<*číslo*>
    * Virtuální počítače s clustery ASCS/SCS: <*SAPSystemSID*>-ASCS-<*číslo*>
    * Cluster DBMS: <*SAPSystemSID*>-db-<*číslo*>

  * **Síťové karty pro všechny virtuální počítače s přidruženými IP adresami**:
    * <*SAPSystemSID*>-nic-di-<*číslo*>
    * <*SAPSystemSID*>-nic-ASCS – *číslo* <>
    * <*SAPSystemSID*> – síťová karta-<*číslo*>

  * **Účty úložiště Azure**

  * **Skupiny dostupnosti** pro:
    * Virtuální počítače SAP aplikačního serveru: <*SAPSystemSID*>-avset-di
    * Virtuální počítače s clustery SAP ASCS/SCS: <*SAPSystemSID*>-avset-ASCS
    * DBMS clusterové virtuální počítače: <*SAPSystemSID*>-avset-DB

  * **Interní nástroj pro vyrovnávání zatížení Azure**:
    * Všechny porty pro instanci ASCS/SCS a IP adresu <*SAPSystemSID*>-9,1-ASCS
    * Se všemi porty pro SQL Server DBMS a IP adresou <*SAPSystemSID*>-9,1-dB

  * **Skupina zabezpečení sítě**: <*SAPSystemSID*>-NSG-ASCS-0  
    * S otevřeným externím portem protokol RDP (Remote Desktop Protocol) (RDP) pro virtuální počítač <*SAPSystemSID*>-ASCS-0

> [!NOTE]
> Všechny IP adresy síťových karet a služeb interního nástroje pro vyrovnávání zatížení Azure jsou ve výchozím nastavení **dynamické** . Změňte je na **statické** IP adresy. Popisujeme, jak postupovat později v článku.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Nasazení virtuálních počítačů s připojením k podnikové síti (mezi místními) pro použití v produkčním prostředí
V případě produkčních systémů SAP nasaďte virtuální počítače Azure s připojením k podnikové síti pomocí sítě VPN Azure Site-to-site nebo Azure ExpressRoute.

> [!NOTE]
> Můžete použít instanci Azure Virtual Network. Virtuální síť a podsíť už jsou vytvořené a připravené.
>
>

1. V Azure Portal vyberte v okně **parametry** v poli **NEWOREXISTINGSUBNET** možnost **existující**.
2. Do pole **SUBNETID** přidejte úplný řetězec připraveného Azure Network SUBNETID, kde plánujete nasazení virtuálních počítačů Azure.
3. Pokud chcete získat seznam všech podsítí sítě Azure, spusťte tento příkaz PowerShellu:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Pole **ID** zobrazuje **SUBNETID**.
4. Pokud chcete získat seznam všech hodnot **SUBNETID** , spusťte tento příkaz PowerShellu:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **SUBNETID** vypadá takto:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Nasazení pouze cloudových instancí SAP pro test a ukázku
Systém SAP s vysokou dostupností můžete nasadit do modelu nasazení pouze pro Cloud. Tento druh nasazení je vhodný hlavně pro ukázkové a testovací případy použití. Nehodí se pro případy použití v produkčním prostředí.

- V Azure Portal v okně **parametry** vyberte v poli **NEWOREXISTINGSUBNET** možnost **Nový**. Pole **SUBNETID** ponechte prázdné.

  Šablona SAP Azure Resource Manager automaticky vytvoří virtuální síť a podsíť Azure.

> [!NOTE]
> Musíte taky nasadit aspoň jeden vyhrazený virtuální počítač pro Active Directory a DNS ve stejné instanci Azure Virtual Network. Šablona nevytváří tyto virtuální počítače.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Příprava infrastruktury na šablonu architektury 2

Tuto šablonu Azure Resource Manager pro SAP můžete použít k jednoduššímu nasazení požadovaných prostředků infrastruktury pro architekturu SAP architektonické šablony 2.

Tady je místo, kde můžete získat Azure Resource Manager šablony pro tento scénář nasazení:

* [Obrázek Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Příprava infrastruktury na šablonu architektury 3

Můžete připravit infrastrukturu a nakonfigurovat SAP pro **více identifikátorů SID**. Do *existující* konfigurace clusteru můžete například přidat další instanci SAP ASCS/SCS. Další informace najdete v tématu [Konfigurace další instance SAP ASCS/SCS do existující konfigurace clusteru pro vytvoření konfigurace SAP multi-SID v Azure Resource Manager][sap-ha-multi-sid-guide].

Pokud chcete vytvořit nový cluster s více identifikátory SID, můžete použít [šablony pro rychlý Start pro více SID na GitHubu](https://github.com/Azure/azure-quickstart-templates).
Chcete-li vytvořit nový cluster s více identifikátory SID, je nutné nasadit následující tři šablony:

* [Šablona ASCS/SCS](#ASCS-SCS-template)
* [Šablona databáze](#database-template)
* [Šablona aplikačních serverů](#application-servers-template)

Následující části obsahují další podrobnosti o šablonách a parametry, které je třeba zadat v šablonách.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a> Šablona ASCS/SCS

Šablona ASCS/SCS nasadí dva virtuální počítače, které můžete použít k vytvoření clusteru s podporou převzetí služeb při selhání se systémem Windows Server, který je hostitelem více instancí ASCS/SCS.

Šablonu ASCS/SCS s více identifikátory SID nastavíte tak, že v [šabloně ASCS/SCS pro multi-SID][sap-templates-3-tier-multisid-xscs-marketplace-image]zadáte hodnoty pro následující parametry:

  - **Předpona prostředku**  Nastavte předponu prostředku, která se používá k vytvoření předpony všech prostředků, které se vytvoří během nasazení. Vzhledem k tomu, že prostředky nepatří pouze k jednomu systému SAP, předpona prostředku není identifikátor SID jednoho systému SAP.  Předpona musí mít **tři až šest znaků**.
  - **Typ zásobníku**. Vyberte typ zásobníku systému SAP. V závislosti na typu zásobníku Azure Load Balancer má jednu (jenom ABAP nebo Java) nebo dvě privátní IP adresy (ABAP + Java) na jeden systém SAP.
  -  **Typ operačního systému**. Vyberte operační systém virtuálních počítačů.
  -  **Počet systémů SAP** Vyberte počet systémů SAP, které chcete v tomto clusteru nainstalovat.
  -  **Dostupnost systému**. Vyberte **ha**.
  -  **Uživatelské jméno správce a heslo správce**. Vytvořte nového uživatele, který se dá použít k přihlášení k počítači.
  -  **Nová nebo existující podsíť**. Nastavte, zda má být vytvořena nová virtuální síť a podsíť, nebo by měla být použita existující podsíť. Pokud už máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
  -  **ID podsítě** Pokud chcete virtuální počítač nasadit do existující virtuální sítě, kde máte definovanou podsíť, ke které je potřeba přiřadit virtuální počítač, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá takto: *ID předplatného* /subscriptions/<>/resourceGroups/<*název skupiny prostředků*>/Providers/Microsoft.Network/virtualNetworks/<*virtuální síť* název>/subnets/<*název podsítě*>

Šablona nasadí jednu instanci Azure Load Balancer, která podporuje více systémů SAP.

- Instance ASCS jsou nakonfigurované pro instance číslo 00, 10, 20...
- Instance SCS jsou nakonfigurované pro číslo instance 01, 11, 21...
- Instance serveru pro replikaci ASCS ve frontě (jenom Linux) se konfigurují pro instance číslo 02, 12, 22...
- Instance SCS OLAJÍCÍCH (pouze Linux) jsou nakonfigurované pro instance číslo 03, 13, 23...

Vyrovnávání zátěže obsahuje 1 (2 pro Linux) VIP, VIP pro ASCS/SCS a 1x VIP pro OLAJÍCÍCH (jenom Linux).

Následující seznam obsahuje všechna pravidla vyrovnávání zatížení (kde x je číslo systému SAP, například 1, 2, 3...):
- Porty specifické pro systém Windows pro každý systém SAP: 445, 5985
- ASCS porty (číslo instance x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS porty (číslo instance x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS OLAJÍCÍCH porty na platformě Linux (číslo instance X2): 33x2, 5x213, 5x214, 5x216
- SCS OLAJÍCÍCH porty na platformě Linux (číslo instance X3): 33x3, 5x313, 5x314, 5x316

Nástroj pro vyrovnávání zatížení je nakonfigurován tak, aby používal následující zkušební porty (kde x je číslo systému SAP, například 1, 2, 3...):
- ASCS/SCS interního vyrovnávání zatížení – port testu paměti 620x0
- OLAJÍCÍCH interního nástroje pro vyrovnávání zatížení (jenom Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a> Šablona databáze

Šablona databáze nasadí jeden nebo dva virtuální počítače, které můžete použít k instalaci systému pro správu relačních databází (RDBMS) pro jeden systém SAP. Pokud například nasadíte šablonu ASCS/SCS pro pět systémů SAP, budete muset tuto šablonu nasadit pětkrát.

Šablonu Database multi-SID nastavíte tak, že v [šabloně Database multi-SID][sap-templates-3-tier-multisid-db-marketplace-image]zadáte hodnoty pro následující parametry:

- **ID systému SAP**. Zadejte ID systému SAP pro systém SAP, který chcete nainstalovat. ID se použije jako předpona pro nasazené prostředky.
- **Typ operačního systému**. Vyberte operační systém virtuálních počítačů.
- **DbType**. Vyberte typ databáze, kterou chcete na clusteru nainstalovat. Pokud chcete nainstalovat Microsoft SQL Server, vyberte **SQL** . Pokud plánujete instalaci SAP HANA na virtuálních počítačích, vyberte **Hana** . Ujistěte se, že jste vybrali správný typ operačního systému: vyberte **Windows** for SQL a vyberte distribuci Linux pro Hana. Azure Load Balancer, která je připojená k virtuálním počítačům, bude nakonfigurovaná tak, aby podporovala vybraný typ databáze:
  * **SQL**. Nástroj pro vyrovnávání zatížení bude vyrovnávat zatížení portu 1433. Nezapomeňte použít tento port pro instalaci služby Always On SQL Server.
  * **Hana**. Nástroj pro vyrovnávání zatížení bude vyrovnávat zatížení portů 35015 a 35017. Nezapomeňte nainstalovat SAP HANA s číslem instance **50**.
  Nástroj pro vyrovnávání zatížení bude používat port testu 62550.
- **Velikost systému SAP**. Nastavte počet SAP, který bude nový systém poskytovat. Pokud si nejste jistí, kolik SAP bude systém vyžadovat, požádejte svého partnera technologie SAP nebo systémový integrátor.
- **Dostupnost systému**. Vyberte **ha**.
- **Uživatelské jméno správce a heslo správce**. Vytvořte nového uživatele, který se dá použít k přihlášení k počítači.
- **ID podsítě** Zadejte ID podsítě, kterou jste použili během nasazování šablony ASCS/SCS, nebo ID podsítě, která byla vytvořena jako součást nasazení šablony ASCS/SCS.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a> Šablona aplikačních serverů

Šablona aplikačních serverů nasadí dva nebo víc virtuálních počítačů, které se dají použít jako instance aplikačního serveru SAP pro jeden systém SAP. Pokud například nasadíte šablonu ASCS/SCS pro pět systémů SAP, budete muset tuto šablonu nasadit pětkrát.

Šablonu multi-SID aplikačních serverů nastavíte tak, že v [šabloně multi-SID aplikačních serverů][sap-templates-3-tier-multisid-apps-marketplace-image]zadáte hodnoty pro následující parametry:

  -  **ID systému SAP**. Zadejte ID systému SAP pro systém SAP, který chcete nainstalovat. ID se použije jako předpona pro nasazené prostředky.
  -  **Typ operačního systému**. Vyberte operační systém virtuálních počítačů.
  -  **Velikost systému SAP**. Počet SAP, který bude nový systém poskytovat. Pokud si nejste jistí, kolik SAP bude systém vyžadovat, požádejte svého partnera technologie SAP nebo systémový integrátor.
  -  **Dostupnost systému**. Vyberte **ha**.
  -  **Uživatelské jméno správce a heslo správce**. Vytvořte nového uživatele, který se dá použít k přihlášení k počítači.
  -  **ID podsítě** Zadejte ID podsítě, kterou jste použili během nasazování šablony ASCS/SCS, nebo ID podsítě, která byla vytvořena jako součást nasazení šablony ASCS/SCS.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Virtuální síť Azure
V našem příkladu je adresní prostor virtuální sítě Azure 10.0.0.0/16. Existuje jedna podsíť s názvem **podsíť** s rozsahem adres 10.0.0.0/24. Všechny virtuální počítače a interní nástroje pro vyrovnávání zatížení se nasazují v této virtuální síti.

> [!IMPORTANT]
> V hostovaném operačním systému neprovádějte žádné změny nastavení sítě. Patří sem IP adresy, servery DNS a podsíť. Nakonfigurujte všechna nastavení sítě v Azure. Služba DHCP (Dynamic Host Configuration Protocol) rozšíří vaše nastavení.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> IP adresy DNS

Chcete-li nastavit požadované IP adresy DNS, proveďte následující kroky.

1. V Azure Portal v okně **servery DNS** se ujistěte, že je možnost **servery DNS** virtuální sítě nastavená na **vlastní DNS**.
2. Vyberte nastavení podle typu sítě, kterou máte. Další informace naleznete v následujících zdrojích:
   * Přidejte IP adresy místních serverů DNS.  
   Místní servery DNS můžete rozmístit do virtuálních počítačů, které běží v Azure. V takovém scénáři můžete přidat IP adresy virtuálních počítačů Azure, na kterých spouštíte službu DNS.
   * Pro nasazení izolovaná v Azure: nasaďte další virtuální počítač ve stejné instanci Virtual Network, která slouží jako server DNS. Přidejte IP adresy virtuálních počítačů Azure, které jste nastavili pro spuštění služby DNS.

   ![Obrázek 12: Konfigurace serverů DNS pro Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Obrázek 12:** Konfigurace serverů DNS pro Azure Virtual Network_

   > [!NOTE]
   > Pokud změníte IP adresy serverů DNS, budete muset virtuální počítače Azure restartovat, aby se změna projevila a rozšířila nové servery DNS.
   >
   >

V našem příkladu je služba DNS nainstalovaná a nakonfigurovaná na těchto virtuálních počítačích s Windows:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První server DNS |domcontr-0 |PR1-nic-domcontr-0 |10.0.0.10 |
| Druhý server DNS |domcontr-1 |PR1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Názvy hostitelů a statické IP adresy pro clusterované instance SAP ASCS/SCS a clusterované instance DBMS

Pro místní nasazení potřebujete tyto rezervované názvy hostitelů a IP adresy:

| Role názvu virtuálního hostitele | Název virtuálního hostitele | Virtuální statická IP adresa |
| --- | --- | --- |
| Název virtuálního hostitele SAP ASCS/SCS prvního clusteru (pro správu clusteru) |PR1-ASCS-vir |10.0.0.42 |
| Název virtuálního hostitele pro instanci SAP ASCS/SCS |PR1-ASCS-SAP |10.0.0.43 |
| SAP DBMS druhý název virtuálního hostitele clusteru (Správa clusteru) |PR1 – DBMS – vir |10.0.0.32 |

Při vytváření clusteru vytvořte názvy virtuálních hostitelů **PR1-ASCS-vir** a **PR1-DBMS-vir** a přidružené IP adresy, které spravují samotný cluster. Informace o tom, jak to provést, najdete v tématu [shromáždění uzlů clusteru v konfiguraci clusteru][sap-ha-guide-8.12.1].

Na serveru DNS můžete ručně vytvořit další dva názvy virtuálních hostitelů, **PR1-ASCS-SAP** a **PR1-DBMS-SAP** a přidružené IP adresy. Tyto prostředky používají clusterovaná instance SAP ASCS/SCS a instance clusterovaného systému DBMS. Informace o tom, jak to provést, najdete v tématu [Vytvoření virtuálního hostitele pro clusterovanou instanci SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Nastavení statických IP adres pro virtuální počítače SAP
Po nasazení virtuálních počítačů, které se mají použít v clusteru, je potřeba nastavit statické IP adresy pro všechny virtuální počítače. Provedete to v konfiguraci Azure Virtual Network, a ne v hostovaném operačním systému.

1. V Azure Portal vyberte možnost **Skupina prostředků**  >  **Síťová karta**  >  **Nastavení**  >  **IP adresa**.
2. V okně **IP adresy** vyberte v části **přiřazení** možnost **statické**. Do pole **IP adresa** zadejte IP adresu, kterou chcete použít.

   > [!NOTE]
   > Pokud změníte IP adresu síťové karty, budete muset virtuální počítače Azure restartovat, aby se změny projevily.  
   >
   >

   ![Obrázek 13: Nastavení statických IP adres pro síťovou kartu každého virtuálního počítače][sap-ha-guide-figure-3002]

   _**Obrázek 13:** Nastavte statické IP adresy pro síťovou kartu každého virtuálního počítače._

   Tento krok opakujte pro všechna síťová rozhraní, tedy pro všechny virtuální počítače, včetně virtuálních počítačů, které chcete použít pro službu Active Directory/DNS.

V našem příkladu máme tyto virtuální počítače a statické IP adresy:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První instance aplikačního serveru SAP |PR1-di-0 |PR1-nic-di-0 |10.0.0.50 |
| Druhá instance aplikačního serveru SAP |PR1-di-1 |PR1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Poslední instance aplikačního serveru SAP |PR1-di-5 |PR1-nic-di-5 |10.0.0.55 |
| První uzel clusteru pro instanci ASCS/SCS |PR1-ASCS-0 |PR1-nic-ASCS-0 |10.0.0.40 |
| Druhý uzel clusteru pro instanci ASCS/SCS |PR1-ASCS-1 |PR1-nic-ASCS-1 |10.0.0.41 |
| První uzel clusteru pro instanci systému DBMS |PR1-DB-0 |PR1-nic-DB-0 |10.0.0.30 |
| Druhý uzel clusteru pro instanci systému DBMS |PR1-DB-1 |PR1-nic-DB-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Nastavte statickou IP adresu pro interní nástroj pro vyrovnávání zatížení Azure.

Šablona SAP Azure Resource Manager vytvoří interní nástroj pro vyrovnávání zatížení Azure, který se používá pro cluster instancí SAP ASCS/SCS a cluster DBMS.

> [!IMPORTANT]
> IP adresa názvu virtuálního hostitele SAP ASCS/SCS je stejná jako IP adresa pro interní nástroj pro vyrovnávání zatížení SAP ASCS/SCS: **PR1-9,1-ASCS**.
> IP adresa virtuálního názvu systému DBMS je stejná jako IP adresa interního nástroje pro vyrovnávání zatížení systému DBMS: **PR1-9,1-DBMS**.
>
>

Nastavení statické IP adresy pro interní nástroj pro vyrovnávání zatížení Azure:

1. Počáteční nasazení nastaví IP adresu interního nástroje pro vyrovnávání zatížení na **dynamickou**. V Azure Portal v okně **IP adresy** v části **přiřazení** vyberte **statické**.
2. Nastavte IP adresu interního nástroje pro vyrovnávání zatížení **PR1-9,1-ASCS** na IP adresu názvu virtuálního hostitele instance SAP ASCS/SCS.
3. Nastavte IP adresu interního nástroje pro vyrovnávání zatížení **PR1-9,1-DBMS** na IP adresu názvu virtuálního hostitele instance systému DBMS.

   ![Obrázek 14: Nastavení statických IP adres pro interní nástroj pro vyrovnávání zatížení pro instanci SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Obrázek 14:** Nastavení statických IP adres pro interní nástroj pro vyrovnávání zatížení pro instanci SAP ASCS/SCS_

V našem příkladu máme dva interní nástroje pro vyrovnávání zatížení Azure, které mají tyto statické IP adresy:

| Role interního nástroje pro vyrovnávání zatížení Azure | Název interního nástroje pro vyrovnávání zatížení Azure | Statická IP adresa |
| --- | --- | --- |
| Interní nástroj pro vyrovnávání zatížení instance SAP ASCS/SCS |PR1-9,1-ASCS |10.0.0.43 |
| Interní nástroj pro vyrovnávání zatížení SAP DBMS |PR1-9,1 – systém DBMS |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure

Šablona SAP Azure Resource Manager vytvoří porty, které potřebujete:
* Instance ABAP ASCS s výchozí instancí Number **00**
* Instance Java SCS s výchozím číslem instance **01**

Při instalaci instance SAP ASCS/SCS je nutné použít výchozí číslo instance **00** pro instanci ABAP ASCS a výchozí číslo instance **01** pro instanci Java SCS.

Dále vytvořte požadované koncové body interního vyrovnávání zatížení pro porty SAP NetWeaver.

Chcete-li vytvořit požadované koncové body interního vyrovnávání zatížení, vytvořte nejprve tyto koncové body vyrovnávání zatížení pro porty ASCS SAP NetWeaver ABAP:

| Název pravidla služby/Vyrovnávání zatížení | Výchozí čísla portů | Konkrétní porty pro (ASCS instance s číslem instance 00) (OLAJÍCÍCH s 10) |
| --- | --- | --- |
| Server/ *lbrule3200* fronty |32<*číslo instance*> |3200 |
| Server zpráv ABAP/ *lbrule3600* |36<*číslo instance*> |3600 |
| Interní zpráva ABAP/ *lbrule3900* |39<*číslo instance*> |3900 |
| HTTP/ *Lbrule8100* serveru zpráv |81<*číslo instance*> |8100 |
| Služba SAP Start Service ASCS HTTP/ *Lbrule50013* |5<*číslo instance*>13 |50013 |
| Služba SAP Start Service ASCS HTTPS/ *Lbrule50014* |5<*číslo instance*>14 |50014 |
| Replikace do fronty/ *Lbrule50016* |5<*číslo instance*>16 |50016 |
| Služba SAP Start Service OLAJÍCÍCH HTTP *Lbrule51013* |5<*číslo instance*>13 |51013 |
| Služba SAP Start Service OLAJÍCÍCH HTTP *Lbrule51014* |5<*číslo instance*>14 |51014 |
| *Lbrule5985* pro Windows RM | |5985 |
| *Lbrule445* sdílení souborů | |445 |

_**Tabulka 1:** Čísla portů instancí ASCS SAP NetWeaver ABAP_

Pak vytvořte tyto koncové body vyrovnávání zatížení pro porty SAP NetWeaver Java SCS:

| Název pravidla služby/Vyrovnávání zatížení | Výchozí čísla portů | Konkrétní porty pro (instance SCS s číslem instance 01) (OLAJÍCÍCH s 11) |
| --- | --- | --- |
| Server/ *lbrule3201* fronty |32<*číslo instance*> |3201 |
| Server brány/ *lbrule3301* |33<*číslo instance*> |3301 |
| Server zpráv Java/ *lbrule3900* |39<*číslo instance*> |3901 |
| HTTP/ *Lbrule8101* serveru zpráv |81<*číslo instance*> |8101 |
| Služba SAP Start Service SCS HTTP/ *Lbrule50113* |5<*číslo instance*>13 |50113 |
| Služba SAP Start Service SCS HTTPS/ *Lbrule50114* |5<*číslo instance*>14 |50114 |
| Replikace do fronty/ *Lbrule50116* |5<*číslo instance*>16 |50116 |
| Služba SAP Start Service OLAJÍCÍCH HTTP *Lbrule51113* |5<*číslo instance*>13 |51113 |
| Služba SAP Start Service OLAJÍCÍCH HTTP *Lbrule51114* |5<*číslo instance*>14 |51114 |
| *Lbrule5985* pro Windows RM | |5985 |
| *Lbrule445* sdílení souborů | |445 |

_**Tabulka 2:** Čísla portů pro instance SAP NetWeaver Java SCS_

![Obrázek 15: výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure][sap-ha-guide-figure-3004]

_**Obrázek 15:** Výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure_

Nastavte IP adresu nástroje pro vyrovnávání zatížení **PR1-9,1-DBMS** na IP adresu názvu virtuálního hostitele instance systému DBMS.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure

Pokud chcete pro instance SAP ASCS nebo SCS používat odlišná čísla, musíte změnit názvy a hodnoty jejich portů z výchozích hodnot.

1. V Azure Portal vyberte **< *SID*>-disascs**  >  **pravidla vyrovnávání zatížení pro vyrovnávání** zatížení.
2. Pro všechna pravidla vyrovnávání zatížení, která patří do instance SAP ASCS nebo SCS, změňte tyto hodnoty:

   * Name
   * Port
   * Back-end port

   Například pokud chcete změnit výchozí číslo instance ASCS z 00 na 31, je nutné provést změny pro všechny porty uvedené v tabulce 1.

   Tady je příklad aktualizace pro port *lbrule3200*.

   ![Obrázek 16: Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure][sap-ha-guide-figure-3005]

   _**Obrázek 16:** Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Přidání virtuálních počítačů s Windows do domény

Po přiřazení statické IP adresy k virtuálním počítačům přidejte virtuální počítače do domény.

![Obrázek 17: Přidání virtuálního počítače k doméně][sap-ha-guide-figure-3006]

_**Obrázek 17:** Přidání virtuálního počítače do domény_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Přidání položek registru na obou uzlech clusteru instance SAP ASCS/SCS

Azure Load Balancer má interní nástroj pro vyrovnávání zatížení, který ukončí připojení v případě, že jsou připojení po nastaveném časovém intervalu nečinné (nečinný časový limit). Pracovní procesy SAP v instancích dialogů otevřou připojení k procesu zařazení do fronty SAP ihned po odeslání první žádosti o zařazení do fronty nebo vyřazení z fronty. Tato připojení obvykle zůstanou zavedena, dokud se nerestartuje pracovní proces nebo proces zařazování do fronty. Pokud je ale připojení nečinné v nastaveném časovém intervalu, ukončí interní nástroj pro vyrovnávání zatížení Azure připojení. Nejedná se o problém, protože pracovní proces SAP znovu naváže připojení k procesu zařazování, pokud již neexistuje. Tyto aktivity jsou popsány v části vývojář sleduje procesy SAP, ale v těchto trasování vytvářejí velký objem obsahu navíc. Je vhodné změnit protokol TCP/IP `KeepAliveTime` a `KeepAliveInterval` oba uzly clusteru. Zkombinujte tyto změny do parametrů protokolu TCP/IP s parametry profilu SAP, které jsou popsány dále v článku.

Chcete-li přidat položky registru na oba uzly clusteru instance SAP ASCS/SCS, přidejte tyto položky registru Windows na obou uzlech clusteru Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveTime` |
| Typ proměnné |REG_DWORD (desetinné číslo) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957549.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |

_**Tabulka 3:** Změna prvního parametru protokolu TCP/IP_

Pak přidejte tyto položky registru Windows na obou uzlech clusteru Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveInterval` |
| Typ proměnné |REG_DWORD (desetinné číslo) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957548.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |

_**Tabulka 4:** Změna druhého parametru protokolu TCP/IP_

**Chcete-li změny použít, restartujte oba uzly clusteru**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Nastavení clusteru clusterování s podporou převzetí služeb při selhání ve Windows serveru pro instanci SAP ASCS/SCS

Nastavení clusteru clusteringu Windows Server s podporou převzetí služeb při selhání pro instanci SAP ASCS/SCS zahrnuje tyto úlohy:

- Shromažďování uzlů clusteru v konfiguraci clusteru
- Konfigurace určující sdílené složky clusteru

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Shromáždění uzlů clusteru v konfiguraci clusteru

1. V Průvodci přidáním role a funkce přidejte Clustering s podporou převzetí služeb při selhání do obou uzlů clusteru.
2. Cluster s podporou převzetí služeb při selhání nastavte pomocí Správce clusteru s podporou převzetí služeb při selhání. V Správce clusteru s podporou převzetí služeb při selhání vyberte **vytvořit cluster** a pak přidejte jenom název prvního clusteru, Node a. Ještě Nepřidávat druhý uzel; druhý uzel přidáte v pozdějším kroku.

   ![Obrázek 18: Přidejte název serveru nebo virtuálního počítače prvního uzlu clusteru.][sap-ha-guide-figure-3007]

   _**Obrázek 18:** Přidejte název serveru nebo virtuálního počítače prvního uzlu clusteru._

3. Zadejte název sítě (název virtuálního hostitele) clusteru.

   ![Obrázek 19: zadejte název clusteru.][sap-ha-guide-figure-3008]

   _**Obrázek 19:** Zadejte název clusteru._

4. Po vytvoření clusteru spusťte test ověření clusteru.

   ![Obrázek 20: spuštění kontroly ověření clusteru][sap-ha-guide-figure-3009]

   _**Obrázek 20:** Spustit kontrolu ověření clusteru_

   V tomto okamžiku můžete v tomto okamžiku ignorovat všechna upozornění týkající se disků. Později přidáte určující sdílenou složku a sdílené disky s příponou. V této fázi si nemusíte dělat starosti s kvorem.

   ![Obrázek 21: nenašel se žádný disk kvora.][sap-ha-guide-figure-3010]

   _**Obrázek 21:** Nenašel se žádný disk kvora._

   ![Obrázek 22: základní prostředek clusteru potřebuje novou IP adresu.][sap-ha-guide-figure-3011]

   _**Obrázek 22:** Základní prostředek clusteru potřebuje novou IP adresu._

5. Změňte IP adresu základní Clusterové služby. Cluster se nemůže spustit, dokud nezměníte IP adresu základní Clusterové služby, protože IP adresa serveru odkazuje na jeden z uzlů virtuálních počítačů. Udělejte to na stránce **vlastností** prostředku IP základní Clusterové služby.

   Například potřebujeme přiřadit IP adresu (v našem příkladu **10.0.0.42**) pro název virtuálního hostitele clusteru **PR1-ASCS-vir**.

   ![Obrázek 23: v dialogovém okně Vlastnosti změňte IP adresu.][sap-ha-guide-figure-3012]

   _**Obrázek 23:** V dialogovém okně **vlastnosti** změňte IP adresu._

   ![Obrázek 24: přiřazení IP adresy rezervované pro cluster][sap-ha-guide-figure-3013]

   _**Obrázek 24:** Přiřaďte IP adresu rezervovanou pro cluster._

6. Přepněte název virtuálního hostitele clusteru do režimu online.

   ![Obrázek 25: služba Cluster Core je spuštěná a má správnou IP adresu.][sap-ha-guide-figure-3014]

   _**Obrázek 25:** Služba Cluster Core je spuštěná a se správnou IP adresou._

7. Přidejte druhý uzel clusteru.

   Teď, když je základní Clusterová služba v provozu, můžete přidat druhý uzel clusteru.

   ![Obrázek 26: Přidání druhého uzlu clusteru][sap-ha-guide-figure-3015]

   _**Obrázek 26:** Přidat druhý uzel clusteru_

8. Zadejte název druhého hostitele uzlu clusteru.

   ![Obrázek 27: Zadejte druhý název hostitele uzlu clusteru.][sap-ha-guide-figure-3016]

   _**Obrázek 27:** Zadejte název hostitele druhého uzlu clusteru._

   > [!IMPORTANT]
   > Ujistěte **se, že není zaškrtnuté** políčko **Přidat do clusteru veškeré oprávněné úložiště** .  
   >
   >

   ![Obrázek 28: zrušte zaškrtnutí políčka][sap-ha-guide-figure-3017]

   _**Obrázek 28:** Nezaškrtávejte **políčko**_

   Můžete ignorovat upozornění týkající se kvora a disků. Pak nastavíte kvorum a budete disk sdílet později, jak je popsáno v tématu Instalace s ASCS s tím, že [clusterový disk pro SAP/SCS pro sdílený disk clusteru][sap-ha-guide-8.12.3].

   ![Obrázek 29: ignorování upozornění na kvorum disku][sap-ha-guide-figure-3018]

   _**Obrázek 29:** Ignorovat upozornění týkající se kvora disku_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurace určující sdílené složky clusteru

Konfigurace určující sdílené složky clusteru zahrnuje tyto úlohy:

- Vytvoření sdílené složky
- Nastavení kvora určující sdílené složky v Správce clusteru s podporou převzetí služeb při selhání

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Vytvoření sdílené složky

1. Vyberte místo na disku kvora určující sdílenou složku. S parametrem datakeeped je tato možnost podporována.

   V příkladech v tomto článku je určující sdílená složka ve službě Active Directory nebo DNS serveru, který běží v Azure. Určující sdílená složka se nazývá **domcontr-0**. Vzhledem k tomu, že jste nakonfigurovali připojení VPN k Azure (přes VPN typu Site-to-site nebo Azure ExpressRoute), služba Active Directory/DNS je místní a není vhodná pro spuštění určující sdílené složky.

   > [!NOTE]
   > Pokud služba Active Directory/DNS běží jenom místně, nekonfigurujte určující sdílenou složku v operačním systému Windows Active Directory/DNS, který je místně spuštěný. Latence sítě mezi uzly clusteru běžícími v Azure a službou Active Directory/DNS v místním prostředí může být moc velká a způsobit problémy s připojením. Nezapomeňte nakonfigurovat určující sdílenou složku na virtuálním počítači Azure, na kterém běží blízko uzlu clusteru.  
   >
   >

   Jednotka kvora potřebuje alespoň 1 024 MB volného místa. Pro jednotku kvora doporučujeme 2 048 MB volného místa.

2. Přidejte objekt názvu clusteru.

   ![Obrázek 30: přiřazení oprávnění ke sdílené složce pro objekt názvu clusteru][sap-ha-guide-figure-3019]

   _**Obrázek 30:** Přiřaďte oprávnění ke sdílené složce pro objekt názvu clusteru._

   Ujistěte se, že oprávnění zahrnují autoritu pro změnu dat ve sdílené složce pro objekt názvu clusteru (v našem příkladu **PR1-ASCS-vir $**).

3. Pokud chcete do seznamu přidat objekt názvu clusteru, vyberte **Přidat**. Změňte filtr tak, aby kontroloval objekty počítače (kromě těch, které jsou znázorněny na obrázku 31).

   ![Obrázek 31: Změna typů objektů na zahrnutí počítačů][sap-ha-guide-figure-3020]

   _**Obrázek 31:** Změna typů objektů na zahrnutí počítačů_

   ![Obrázek 32: zaškrtnutí políčka počítače][sap-ha-guide-figure-3021]

   _**Obrázek 32:** Zaškrtněte políčko **počítače** ._

4. Zadejte objekt názvu clusteru, jak je znázorněno na obrázku 31. Vzhledem k tomu, že tento záznam již byl vytvořen, můžete změnit oprávnění, jak je znázorněno na obrázku 30.

5. Vyberte kartu **zabezpečení** sdílené složky a pak nastavte podrobnější oprávnění pro objekt názvu clusteru.

   ![Obrázek 33: nastavení atributů zabezpečení pro objekt názvu clusteru v kvoru sdílení souborů][sap-ha-guide-figure-3022]

   _**Obrázek 33:** Nastavení atributů zabezpečení pro objekt názvu clusteru v kvoru sdílení souborů_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Nastavte kvorum určující sdílenou složku v Správce clusteru s podporou převzetí služeb při selhání

1. Otevřete Průvodce konfigurací nastavení kvora.

   ![Obrázek 34: spuštění Průvodce konfigurací nastavení kvora clusteru][sap-ha-guide-figure-3023]

   _**Obrázek 34:** Spuštění Průvodce konfigurací nastavení kvora clusteru_

2. Na stránce **Vybrat konfiguraci kvora** vyberte **Vybrat určující disk kvora**.

   ![Obrázek 35: konfigurace kvora, ze kterých si můžete vybrat][sap-ha-guide-figure-3024]

   _**Obrázek 35:** Konfigurace kvora, ze kterých si můžete vybrat_

3. Na stránce **Vybrat určující disk kvora** vyberte **Konfigurovat určující sdílenou složku**.

   ![Obrázek 36: vyberte určující sdílenou složku][sap-ha-guide-figure-3025]

   _**Obrázek 36:** Vybrat určující sdílenou složku_

4. Zadejte cestu UNC ke sdílené složce (v našem příkladu \\ domcontr-0\FSW). Chcete-li zobrazit seznam změn, které lze provést, vyberte možnost **Další**.

   ![Obrázek 37: definování umístění sdílené složky pro sdílenou složku s kopií clusteru][sap-ha-guide-figure-3026]

   _**Obrázek 37:** Zadejte umístění sdílené složky pro sdílenou složku s kopií clusteru._

5. Vyberte požadované změny a pak klikněte na tlačítko **Další**. Musíte úspěšně znovu nakonfigurovat konfiguraci clusteru, jak je znázorněno na obrázku 38.  

   ![Obrázek 38: potvrzení, že jste cluster znovu nakonfigurovali][sap-ha-guide-figure-3027]

   _**Obrázek 38:** Potvrzení, že jste cluster znovu nakonfigurovali_

Po úspěšné instalaci clusteru s podporou převzetí služeb při selhání v Azure je třeba provést změny v některých prahových hodnotách, aby bylo zjišťování převzetí služeb při selhání možné. Parametry, které se mají změnit, jsou popsány v tomto blogu: [https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) . Za předpokladu, že vaše dva virtuální počítače, které vytvářejí konfiguraci clusteru Windows pro ASCS/SCS, jsou ve stejné podsíti, je potřeba změnit následující parametry na tyto hodnoty:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- RoutingHistoryLength = 30  

Tato nastavení byla testována se zákazníky a poskytovala dobré kompromisy, aby byly na jedné straně dostatečně odolnější. Na druhé straně tato nastavení poskytovala v reálném čase dostatek chyb při selhání pro software SAP nebo uzel/virtuální počítač. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalace s tím, že Clusterová edice DataKeeper pro disk sdílené složky clusteru SAP ASCS/SCS

Teď máte v Azure fungující konfiguraci clusteringu s podporou převzetí služeb při selhání ve Windows serveru. K instalaci instance SAP ASCS/SCS ale potřebujete prostředek sdíleného disku. V Azure nemůžete vytvořit prostředky sdíleného disku, které potřebujete. V případě clusteru DataKeeper Edition je řešení třetí strany, které můžete použít k vytvoření prostředků sdíleného disku.

Instalace s tím, že Clusterová edice DataKeeper pro sdílený disk clusteru SAP ASCS/SCS zahrnuje tyto úlohy:

- Přidání .NET Framework 3,5
- Instaluje se s DataKeeper.
- Nastavování s datakeep

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Přidat .NET Framework 3,5
Microsoft .NET Framework 3,5 není automaticky aktivovaný ani nainstalovaný v systému Windows Server 2012 R2. Vzhledem k tomu, že s DataKeeper vyžaduje .NET Framework být na všech uzlech, na které instalujete datakeepes, je nutné nainstalovat .NET Framework 3,5 v hostovaném operačním systému všech virtuálních počítačů v clusteru.

Existují dva způsoby, jak přidat .NET Framework 3,5:

- Pomocí Průvodce přidáním rolí a funkcí v systému Windows, jak je znázorněno na obrázku 39.

  ![Obrázek 39: instalace .NET Framework 3,5 pomocí Průvodce přidáním rolí a funkcí][sap-ha-guide-figure-3028]

  _**Obrázek 39:** Instalace .NET Framework 3,5 pomocí Průvodce přidáním rolí a funkcí_

  ![Obrázek 40: indikátor průběhu instalace při instalaci .NET Framework 3,5 pomocí Průvodce přidáním rolí a funkcí][sap-ha-guide-figure-3029]

  _**Obrázek 40:** Indikátor průběhu instalace při instalaci .NET Framework 3,5 pomocí Průvodce přidáním rolí a funkcí_

- Použijte nástroj příkazového řádku dism.exe. Pro tento typ instalace budete potřebovat přístup k adresáři SxS na instalačním médiu Windows. Na příkazovém řádku se zvýšenými oprávněními zadejte:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> Instalace s DataKeeper

Nainstalujte cluster s DataKeeper Edition do každého uzlu v clusteru. Pokud chcete vytvořit virtuální sdílené úložiště s využitím DataKeeper, vytvořte synchronizovaný zrcadlový svazek a pak Simulujte sdílené úložiště clusteru.

Před instalací softwaru s **DataKeeperSvc** vytvořte uživatele domény.

> [!NOTE]
> Přidejte uživatele **DataKeeperSvc** do místní skupiny **správců** na obou uzlech clusteru.
>
>

Instalace s DataKeeper:

1. Nainstalujte software v obou uzlech clusteru.

   ![S instalačním programem][sap-ha-guide-figure-3030]

   ![Obrázek 41: první stránka Instalace s DataKeeper][sap-ha-guide-figure-3031]

   _**Obrázek 41:** První stránka Instalace s datakeeping_

2. V dialogovém okně zobrazeném na obrázku 42 vyberte **Ano**.

   ![Obrázek 42: DataKeeper vás informuje, že služba bude zakázaná.][sap-ha-guide-figure-3032]

   _**Obrázek 42:** DataKeeper vás informuje, že služba bude zakázaná._

3. V dialogovém okně zobrazeném na obrázku 43 doporučujeme vybrat **účet domény nebo serveru**.

   ![Obrázek 43: výběr uživatele pro s datakeeping][sap-ha-guide-figure-3033]

   _**Obrázek 43:** Výběr uživatele pro s datakeeping_

4. Zadejte uživatelské jméno a heslo účtu domény, které jste vytvořili pro datakeeping.

   ![Obrázek 44: zadejte uživatelské jméno a heslo domény pro instalaci s názvem s-Keep.][sap-ha-guide-figure-3034]

   _**Obrázek 44:** Zadejte uživatelské jméno a heslo domény pro instalaci s názvem s instalací._

5. Nainstalujte licenční klíč pro vaši instanci s tím, jak je znázorněno na obrázku 45.

   ![Obrázek 45: Zadejte svůj licenční klíč s klíčem DataKeeper.][sap-ha-guide-figure-3035]

   _**Obrázek 45:** Zadejte svůj licenční klíč s klíčem datakeeping._

6. Po zobrazení výzvy restartujte virtuální počítač.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Nastavení s s datakeeping

Po instalaci s datakeepce na obou uzlech musíte spustit konfiguraci. Cílem konfigurace je mít synchronní replikaci dat mezi dalšími virtuálními počítači připojenými ke každému z těchto virtuálních počítačů.

1. Spusťte nástroj pro správu a konfiguraci datakeep a pak vyberte **připojit server**. (Na obrázku 46 je tato možnost červeně zakroužkovaná.)

   ![Obrázek 46: s nástroje pro správu a konfiguraci datakeeping][sap-ha-guide-figure-3036]

   _**Obrázek 46:** Správa s nástrojem datakeeping a Configuration Tool_

2. Zadejte název nebo TCP/IP adresu prvního uzlu, ke kterému se má nástroj pro správu a konfiguraci připojit, a v druhém kroku druhý uzel.

   ![Obrázek 47: Vložte název nebo TCP/IP adresu prvního uzlu, ke kterému se má nástroj pro správu a konfiguraci připojit, a v druhém kroku druhý uzel][sap-ha-guide-figure-3037]

   _**Obrázek 47:** Vložte název nebo TCP/IP adresu prvního uzlu, ke kterému se má nástroj pro správu a konfiguraci připojit, a v druhém kroku druhý uzel._

3. Vytvořte úlohu replikace mezi dvěma uzly.

   ![Obrázek 48: vytvoření úlohy replikace][sap-ha-guide-figure-3038]

   _**Obrázek 48:** Vytvoření úlohy replikace_

   Průvodce vás provede procesem vytvoření úlohy replikace.
4. Zadejte název, TCP/IP adresu a diskový svazek zdrojového uzlu.

   ![Obrázek 49: definování názvu úlohy replikace][sap-ha-guide-figure-3039]

   _**Obrázek 49:** Zadejte název úlohy replikace._

   ![Obrázek 50: definujte základní data pro uzel, který by měl být aktuálním zdrojovým uzlem.][sap-ha-guide-figure-3040]

   _**Obrázek 50:** Definujte základní data pro uzel, který by měl být aktuálním zdrojovým uzlem._

5. Zadejte název, TCP/IP adresu a diskový svazek cílového uzlu.

   ![Obrázek 51: definujte základní data pro uzel, který by měl být aktuálním cílovým uzlem.][sap-ha-guide-figure-3041]

   _**Obrázek 51:** Definujte základní data pro uzel, který by měl být aktuálním cílovým uzlem._

6. Definujte algoritmy komprese. V našem příkladu doporučujeme komprimovat datový proud replikace. Obzvláště v situacích opakované synchronizace se komprese streamu replikace výrazně zkracuje i doba opakované synchronizace. Všimněte si, že komprese používá prostředky procesoru a paměti RAM virtuálního počítače. Jak se zvyšuje rychlost komprese, takže se použije objem prostředků procesoru. Toto nastavení můžete také upravit později.

7. Další nastavení, které je třeba ověřit, je, zda replikace probíhá asynchronně nebo synchronně. *Při ochraně konfigurací SAP ASCS/SCS je nutné použít synchronní replikaci*.  

   ![Obrázek 52: definování podrobností replikace][sap-ha-guide-figure-3042]

   _**Obrázek 52:** Definování podrobností replikace_

8. Určete, zda má být svazek replikovaný úlohou replikace reprezentován konfigurací clusteru služby Windows Server Failover Clustering jako sdílený disk. Pro konfiguraci SAP ASCS/SCS vyberte **Ano** , aby se cluster Windows mohl replikovat replikovaný svazek jako sdílený disk, který může použít jako svazek clusteru.

   ![Obrázek 53: vyberte Ano a nastavte replikovaný svazek jako svazek clusteru.][sap-ha-guide-figure-3043]

   _**Obrázek 53:** Vyberte **Ano** a nastavte replikovaný svazek jako svazek clusteru._

   Po vytvoření svazku se v nástroji pro správu a konfiguraci datakeepu zobrazí, že je úloha replikace aktivní.

   ![Obrázek 54: aktivní Synchronní zrcadlení datakeeping pro disk Share SAP ASCS/SCS je aktivní][sap-ha-guide-figure-3044]

   _**Obrázek 54:** Synchronní zrcadlení datakeeping pro disk sdílené složky SAP ASCS/SCS je aktivní._

   Správce clusteru s podporou převzetí služeb při selhání nyní zobrazuje disk jako datakeeping disk, jak je znázorněno na obrázku 55.

   ![Obrázek 55: Správce clusteru s podporou převzetí služeb při selhání zobrazuje disk, na který se replikuje datakeeping][sap-ha-guide-figure-3045]

   _**Obrázek 55:** Správce clusteru s podporou převzetí služeb při selhání zobrazuje disk, který replikuje datakeeping._

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalace systému SAP NetWeaver

Nepopisujeme instalaci DBMS, protože se nastavení liší v závislosti na použitém systému DBMS. Předpokládáme ale, že se s DBMS budou řešit problémy s vysokou dostupností s využitím různých výrobců DBMS pro Azure. Například trvale zapnuto nebo zrcadlení databáze pro SQL Server a Oracle data Guard pro databáze Oracle. Ve scénáři, který používáme v tomto článku, jsme nepřidali do systému DBMS větší ochranu.

Neexistují žádné zvláštní požadavky, pokud různé služby DBMS pracují s tímto druhem Clusterové konfigurace SAP ASCS/SCS v Azure.

> [!NOTE]
> Postupy instalace systémů SAP NetWeaver ABAP, systémů Java a ABAP + Java jsou skoro stejné. Nejvýznamnějším rozdílem je, že systém SAP ABAP má jednu instanci ASCS. Systém SAP Java má jednu instanci SCS. Systém SAP ABAP + Java má jednu instanci ASCS a jednu instanci SCS spuštěnou ve stejné skupině clusteru s podporou převzetí služeb při selhání společnosti Microsoft. Všechny rozdíly v instalacích pro jednotlivé instalační zásobníky SAP NetWeaver jsou výslovně zmíněné. Můžete předpokládat, že všechny ostatní části jsou stejné.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalace SAP s vysokou dostupností instance ASCS/SCS

> [!IMPORTANT]
> Nezapomeňte umístit stránkovací soubor na zrcadlené svazky datakeeping. DataKeeper nepodporuje zrcadlené svazky. Stránkovací soubor můžete ponechat na dočasné jednotce na virtuálním počítači Azure, což je výchozí nastavení. Pokud tam ještě není, přesuňte soubor stránky Windows na jednotku D virtuálního počítače Azure.
>
>

Instalace SAP s vysokou dostupností instance ASCS/SCS zahrnuje tyto úlohy:

- Vytvoření názvu virtuálního hostitele pro clusterovanou instanci SAP ASCS/SCS
- Instalace prvního uzlu clusteru SAP
- Úprava profilu SAP instance ASCS/SCS
- Přidání portu testu paměti
- Otevření portu testu brány Windows Firewall

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Vytvoří název virtuálního hostitele pro clusterovanou instanci SAP ASCS/SCS.

1. Ve Správci DNS systému Windows vytvořte záznam DNS pro název virtuálního hostitele instance ASCS/SCS.

   > [!IMPORTANT]
   > IP adresa, kterou přiřadíte názvu virtuálního hostitele instance ASCS/SCS, musí být stejná jako IP adresa, kterou jste přiřadili Azure Load Balancer (**< *SID*>-** dis--ASCS).  
   >
   >

   IP adresa virtuálního hostitele SAP ASCS/SCS (**PR1-ASCS-SAP**) se shoduje s ip adresou Azure Load Balancer (**PR1-9,1-ASCS**).

   ![Obrázek 56: definujte položku DNS pro virtuální název clusteru SAP ASCS/SCS a adresu TCP/IP.][sap-ha-guide-figure-3046]

   _**Obrázek 56:** Zadejte položku DNS pro virtuální název clusteru SAP ASCS/SCS a adresu TCP/IP._

2. Pokud chcete definovat IP adresu přiřazenou k názvu virtuálního hostitele, vyberte **doména Správce DNS**  >  **Domain**.

   ![Obrázek 57: nový virtuální název a adresa TCP/IP pro konfiguraci clusteru SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Obrázek 57:** Nový virtuální název a adresa TCP/IP pro konfiguraci clusteru SAP ASCS/SCS_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalace prvního uzlu clusteru SAP

1. Provede první možnost uzlu clusteru v uzlu clusteru A. Například na hostiteli **PR1-ASCS-0** .
2. Pokud chcete zachovat výchozí porty pro interní nástroj pro vyrovnávání zatížení Azure, vyberte:

   * **ABAP System**: **ASCS** instance číslo **00**
   * **Java System**: **SCS** instance číslo **01**
   * **ABAP + Java System**: **ASCS** instance číslo **00** a **SCS** instance číslo **01**

   Pokud chcete pro instanci ABAP ASCS použít jiné než 00 instancí a 01 pro instanci Java SCS, musíte nejdřív změnit výchozí pravidla vyrovnávání zatížení interního vyrovnávání zatížení Azure, která jsou popsaná v tématu Změna výchozích pravidel vyrovnávání zatížení [ASCS/SCS pro interní nástroj pro][sap-ha-guide-8.9]vyrovnávání zatížení Azure.

Několik dalších úloh není popsáno v dokumentaci standardní instalace SAP.

> [!NOTE]
> Dokumentace k instalaci SAP popisuje, jak nainstalovat první uzel clusteru ASCS/SCS.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Úprava profilu SAP instance ASCS/SCS

Je nutné přidat nový parametr profilu. Parametr Profile brání propojení mezi pracovními procesy SAP a serverem fronty v době, kdy jsou nečinné příliš dlouho. Uvedli jsme scénář problému v části [Přidání položek registru na obou uzlech clusteru instance SAP ASCS/SCS][sap-ha-guide-8.11]. V této části jsme také zavedli dvě změny některých základních parametrů připojení TCP/IP. V druhém kroku je potřeba nastavit server fronty tak, aby odesílal `keep_alive` signál, aby připojení nedosáhlo prahové hodnoty nečinnosti interního nástroje pro vyrovnávání zatížení Azure.

Postup úpravy profilu SAP instance ASCS/SCS:

1. Pokud používáte ENSA1, přidejte tento parametr profilu do profilu instance SAP ASCS/SCS.

   ```
   enque/encni/set_so_keepalive = true
   ```
   V našem příkladu je cesta:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Například na profil instance SAP SCS a odpovídající cestu:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

   Pro ENSA1 i ENSA2 se ujistěte, že `keepalive` jsou nastavené parametry operačního systému, jak je popsáno v tématu SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

2. Pokud chcete změny použít, restartujte instanci SAP ASCS/SCS.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Přidat port testu paměti

Pomocí funkce testu interního nástroje pro vyrovnávání zatížení můžete zajistit, aby celá konfigurace clusteru fungovala s Azure Load Balancer. Interní nástroj pro vyrovnávání zatížení Azure obvykle distribuuje příchozí zatížení rovnoměrně mezi zúčastněnými virtuálními počítači. To ale v některých konfiguracích clusteru nebude fungovat, protože je aktivní jenom jedna instance. Druhá instance je pasivní a nemůže přijmout žádnou z úloh. Funkce sondy pomáhá, když interní nástroj pro vyrovnávání zatížení Azure přiřadí práci pouze k aktivní instanci. Nástroj pro vyrovnávání zatížení může pomocí funkce sondy zjistit, které instance jsou aktivní, a potom cílit jenom na instanci s úlohou.

Postup přidání portu sondy:

1. Spusťte následující příkaz prostředí PowerShell a ověřte aktuální nastavení **ProbePort** . Spusťte ji v rámci jednoho z virtuálních počítačů v konfiguraci clusteru.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Definujte port testu paměti. Výchozí číslo portu testu je **0**. V našem příkladu používáme port testu **62000**.

   ![Obrázek 58: ve výchozím nastavení je port testu konfigurace clusteru 0.][sap-ha-guide-figure-3048]

   _**Obrázek 58:** Výchozí port testu konfigurace clusteru je 0._

   Číslo portu je definované v šablonách SAP Azure Resource Manager. V PowerShellu můžete přiřadit číslo portu.

   Pokud chcete nastavit novou hodnotu ProbePort pro prostředek clusteru **IP> SAP <*SID*** , spusťte následující skript prostředí PowerShell. Aktualizujte proměnné PowerShellu pro vaše prostředí. Po spuštění skriptu se zobrazí výzva k restartování skupiny clusterů SAP, aby se změny aktivovaly.

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Po převedení role clusteru **SAP <*SID* >** do online režimu ověřte, že je **ProbePort** nastavená na novou hodnotu.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Obrázek 59: test portu clusteru po nastavení nové hodnoty][sap-ha-guide-figure-3049]

   _**Obrázek 59:** Test portu clusteru po nastavení nové hodnoty_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Otevřete port testu brány Windows Firewall.

V obou uzlech clusteru musíte otevřít port sondy brány Windows Firewall. Pomocí následujícího skriptu otevřete port sondy brány Windows Firewall. Aktualizujte proměnné PowerShellu pro vaše prostředí.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** je nastavená na **62000**. Nyní můžete přistupovat ke sdílené složce **\\ \ascsha-clsap\sapmnt** z jiných hostitelů, například z **ascsha-specializující**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalace instance databáze

Pokud chcete nainstalovat instanci databáze, postupujte podle kroků popsaných v dokumentaci k instalaci SAP.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Instalace druhého uzlu clusteru

Pokud chcete nainstalovat druhý cluster, postupujte podle pokynů v příručce pro instalaci SAP.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Změna počátečního typu instance služby SAP OLAJÍCÍCH systému Windows

Změňte počáteční typ služby SAP OLAJÍCÍCH Windows na **automaticky (zpožděné spuštění)** na obou uzlech clusteru.

![Obrázek 60: Změna typu služby pro instanci SAP OLAJÍCÍCH na zpožděné automatické][sap-ha-guide-figure-3050]

_**Obrázek 60:** Změnit typ služby pro instanci SAP OLAJÍCÍCH na zpožděné automatické_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalace primárního aplikačního serveru SAP

Nainstalujte primární aplikační server (PAS) <*identifikátor SID*>-di-0 na virtuálním počítači, který jste určili pro hostování pas. Neexistují žádné závislosti na nastaveních specifických pro Azure nebo DataKeeper.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalace dalšího aplikačního serveru SAP

Nainstalujte další aplikační Server SAP (AAS) na všechny virtuální počítače, které jste určili pro hostování instance aplikačního serveru SAP. Například v <*sid*>-di-1 na <*SID*>-di- &lt; n &gt; .

> [!NOTE]
> Tím se dokončí instalace systému SAP NetWeaver s vysokou dostupností. Dále pokračujte v testování převzetí služeb při selhání.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Test převzetí služeb při selhání instance SAP ASCS/SCS a s replikaci
Můžete snadno testovat a monitorovat převzetí služeb při selhání instance SAP ASCS/SCS a replikaci s využitím disku pomocí Správce clusteru s podporou převzetí služeb při selhání a nástroje pro správu a konfiguraci s podporou datakeepers.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Instance SAP ASCS/SCS je spuštěná na uzlu clusteru A.

Clusterová skupina **SAP PR1** je spuštěná v uzlu clusteru A. Například na **PR1-ASCS-0**. Přiřaďte sdílenou diskovou jednotku, která je součástí skupiny clusterů **SAP PR1** a která instance ASCS/SCS používá, do clusteru node a.

![Obrázek 61: Správce clusteru s podporou převzetí služeb při selhání: Skupina clusterů> SAP <SID je spuštěná na uzlu clusteru A][sap-ha-guide-figure-5000]

_**Obrázek 61:** Správce clusteru s podporou převzetí služeb při selhání: Skupina clusterů> SAP <*SID* je spuštěná na uzlu clusteru A_

V nástroji pro správu a konfiguraci s daty můžete vidět, že data sdíleného disku se synchronně replikují ze zdrojového svazku na uzlu clusteru A na cílovou jednotku svazku v uzlu clusteru B. Například je replikován z **PR1-ASCS-0 [10.0.0.40]** na **PR1-ASCS-1 [10.0.0.41]**.

![Obrázek 62: v rámci s datakeepem replikuje místní svazek z uzlu clusteru A na uzel clusteru B.][sap-ha-guide-figure-5001]

_**Obrázek 62:** Replikace místního svazku v rámci s DataKeeper z uzlu clusteru A na uzel clusteru B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Převzetí služeb při selhání z uzlu A na uzel B

1. Vyberte jednu z těchto možností pro zahájení převzetí služeb při *selhání> skupiny* clusterů SAP <z uzlu clusteru a na uzel clusteru B:
   - Použít Správce clusteru s podporou převzetí služeb při selhání  
   - Použití PowerShellového clusteru pro převzetí služeb při selhání

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Restartujte uzel clusteru A v hostovaném operačním systému Windows (tím se spustí automatické převzetí služeb při *selhání> skupiny* clusterů SAP <z uzlu A na uzel B).  
3. Restartujte uzel clusteru A z Azure Portal (tím se zahájí automatické převzetí služeb při selhání> skupiny clusteru SAP <*SID* z uzlu A na uzel B).  
4. Restartujte uzel clusteru A pomocí Azure PowerShell (tím se zahájí automatické převzetí služeb při *selhání> skupiny* clusteru <SAP z uzlu A na uzel B).

   Po převzetí služeb při selhání je skupina clusteru SAP <*SID*> spuštěná na uzlu clusteru B. Například je spuštěný v **PR1-ASCS-1**.

   ![Obrázek 63: v Správce clusteru s podporou převzetí služeb při selhání Skupina clusteru> <SID služby SAP spuštěna na uzlu clusteru B][sap-ha-guide-figure-5002]

   _**Obrázek 63**: v Správce clusteru s podporou převzetí služeb při selhání skupina clusteru> <*SID* služby SAP spuštěna na uzlu clusteru B_

   Sdílený disk je nyní připojen k uzlu clusteru B. s zdrojem dat je replikace dat ze zdrojového svazku na clusteru B do cílové jednotky svazku v uzlu clusteru A. Například se replikuje z **PR1-ASCS-1 [10.0.0.41]** na **PR1-ASCS-0 [10.0.0.40]**.

   ![Obrázek 64: s DataKeeper replikuje místní svazek z uzlu clusteru B na uzel clusteru A.][sap-ha-guide-figure-5003]

   _**Obrázek 64:** S-datakeeping replikuje místní svazek z uzlu clusteru B na uzel clusteru A._
