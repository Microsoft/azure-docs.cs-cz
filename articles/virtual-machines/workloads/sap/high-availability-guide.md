---
title: Vysoká dostupnost virtuálních počítačů Azure pro SAP NetWeaver | Dokumenty společnosti Microsoft
description: Průvodce vysokou dostupností pro SAP NetWeaver na virtuálních počítačích Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa0810818bf7cfea21f925ee639b4b5a50dcb23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246120"
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
[azure-ps]:https://docs.microsoft.com/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md
[dbms-guide-2.1]:../../virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:../../virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:../../virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:../../virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:../../virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:../../virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:../../virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:../../virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:../../virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:../../virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:../../virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:../../virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:../../virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:../../virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:../../virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:../../virtual-machines-windows-sap-deployment-guide.md
[deployment-guide-2.2]:../../virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:../../virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:../../virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:../../virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:../../virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:../../virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:../../virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:../../virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:../../virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:../../virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:../../virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:../../virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:../../virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:../../virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:../../virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:../../virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:../../virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:../../virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:../../virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:../../virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:../../virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:../../virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:../../virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:../../virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:../../virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:../../virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:../../virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:../../virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

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

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (Konfigurace sap multi-SID s vysokou dostupností)


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
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
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
[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md
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


Virtuální počítače Azure je řešení pro organizace, které potřebují výpočetní, úložné a síťové prostředky v minimálním čase a bez zdlouhavých cyklů zásobování. Virtuální počítače Azure můžete použít k nasazení klasických aplikací, jako je Sap NetWeaver aABAP, Java a zásobník ABAP+Java. Rozšiřte spolehlivost a dostupnost bez dalších místních prostředků. Virtuální počítače Azure podporují připojení mezi místními oblastmi, takže virtuální počítače Azure můžete integrovat do místních domén vaší organizace, privátních cloudů a systémového prostředí SAP.

V tomto článku se zabýváme kroky, které můžete provést nasazení vysoce dostupnosti systémů SAP v Azure pomocí modelu nasazení Azure Resource Manager. Provedeme vás těmito hlavními úkoly:

* Najděte správné poznámky SAP a instalační příručky, které jsou uvedeny v části [Zdroje.][sap-ha-guide-2] Tento článek doplňuje dokumentaci k instalaci SAP a poznámky SAP, což jsou primární prostředky, které vám pomohou nainstalovat a nasadit software SAP na konkrétních platformách.
* Seznamte se s rozdíly mezi modelem nasazení Azure Resource Manageru a klasickým modelem nasazení Azure.
* Přečtěte si o režimech kvora clusteringu s podporou převzetí služeb při selhání systému Windows Server, abyste si mohli vybrat model, který je pro vaše nasazení Azure to pravé.
* Přečtěte si o sdíleném úložišti windows serveru s podporou převzetí služeb při selhání ve službách Azure.
* Zjistěte, jak v Azure chránit součásti s jedním bodem selhání, jako je Advanced Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) a systémy pro správu databází (DBMS) a redundantní součásti, jako je SAP Application Server.
* Pomocí Správce prostředků Azure postupujte krok za krokem k instalaci a konfiguraci vysoce dostupnosti systému SAP v clusteru clusteru Windows Server s podporou převzetí služeb při selhání v Azure.
* Přečtěte si o dalších krocích, které jsou nutné k použití clusteringu s podporou převzetí služeb při selhání windows serveru v Azure, ale které nejsou potřeba v místním nasazení.

Pro zjednodušení nasazení a konfigurace používáme v tomto článku šablony SAP třívrstvé správce prostředků s vysokou dostupností. Šablony automatizují nasazení celé infrastruktury, kterou potřebujete pro vysoce dostupnost systému SAP. Infrastruktura také podporuje sap application performance standard (SAPS) velikost vašeho systému SAP.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a>Požadavky
Než začnete, ujistěte se, že splňujete požadavky, které jsou popsány v následujících částech. Nezapomeňte také zkontrolovat všechny prostředky uvedené v části [Zdroje.][sap-ha-guide-2]

V tomto článku používáme šablony Azure Resource Manager pro [třívrstvé SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Užitečný přehled šablon najdete v tématu [ŠABLONY SAP Azure Resource Manager](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>Zdroje
Tyto články popisují nasazení SAP v Azure:

* [Plánování a implementace virtuálních počítačů Azure pro SAP NetWeaver][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP NetWeaver][deployment-guide]
* [Nasazení dbms virtuálních počítačů Azure pro SAP NetWeaver][dbms-guide]
* [Azure Virtuální počítače vysoká dostupnost pro SAP NetWeaver (tato příručka)][sap-ha-guide]

> [!NOTE]
> Kdykoli je to možné, poskytneme vám odkaz na odkazující instalační příručku SAP (viz [instalační příručky SAP).][sap-installation-guides] Pro předpoklady a informace o procesu instalace je vhodné pečlivě si přečíst instalační příručky SAP NetWeaver. Tento článek popisuje jenom konkrétní úkoly pro systémy založené na SAP NetWeaver, které můžete použít s virtuálními počítači Azure.
>
>

Tyto poznámky SAP se vztahují k tématu SAP v Azure:

| Číslo poznámky | Nadpis |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: Podporované produkty a velikost |
| [2015553] |SAP v Microsoft Azure: Požadavky podpory |
| [1999351] |Vylepšené monitorování Azure pro SAP |
| [2178632] |Klíčové metriky monitorování pro SAP v Microsoft Azure |
| [1999351] |Virtualizace ve Windows: Rozšířené monitorování |
| [2243692] |Použití úložiště Azure Premium SSD pro instanci SAP DBMS |

Přečtěte si další informace o [omezeních předplatných Azure][azure-resource-manager/management/azure-subscription-service-limits-subscription], včetně obecných výchozích omezení a maximálních omezení.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Sap s vysokou dostupností díky Azure Resource Manageru vs. klasickému modelu nasazení Azure
Modely klasického nasazení Azure a Azure se liší v následujících oblastech:

- Skupiny prostředků
- Závislost azure internal balancer na skupině prostředků Azure
- Podpora scénářů SAP s více SID

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a>Skupiny prostředků
Ve Správci prostředků Azure můžete použít skupiny prostředků ke správě všech prostředků aplikací ve vašem předplatném Azure. Integrovaný přístup ve skupině prostředků, všechny prostředky mají stejný životní cyklus. Například všechny prostředky jsou vytvořeny současně a jsou odstraněny současně. Další informace o [skupinách prostředků](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Závislost azure internal balancer na skupině prostředků Azure

V modelu klasického nasazení Azure existuje závislost mezi interním vyrovnáváním zatížení Azure (služba Azure Load Balancer) a skupinou cloudových služeb. Každý interní systém vyrovnávání zatížení potřebuje jednu skupinu cloudových služeb.

Ve Správci prostředků Azure nepotřebujete skupinu prostředků Azure k používání nástroje Azure Load Balancer. Prostředí je jednodušší a flexibilnější.

### <a name="support-for-sap-multi-sid-scenarios"></a>Podpora scénářů SAP s více SID

Ve Správci prostředků Azure můžete nainstalovat více instancí ASCS/SCS systémových identifikátorů SAP (SID) do jednoho clusteru. Instance s více SID jsou možné z důvodu podpory více IP adres pro každý interní vyrovnávání zatížení Azure.

Pokud chcete použít klasický model nasazení Azure, postupujte podle postupů popsaných v [SAP NetWeaver v Azure: Clustering SAP ASCS/SCS instance pomocí Windows Server převzetí služeb při selhání clustering v Azure s SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Důrazně doporučujeme použít model nasazení Azure Resource Manager pro instalace SAP. Nabízí mnoho výhod, které nejsou k dispozici v modelu klasické nasazení. Další informace o [modelech nasazení][virtual-machines-azure-resource-manager-architecture-benefits-arm]Azure .   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Clustering s podporou převzetí služeb při selhání systému Windows Server
Clustering s podporou převzetí služeb při selhání systému Windows Server je základem instalace SAP ASCS/SCS s vysokou dostupností a systému DBMS v systému Windows.

Cluster s podporou převzetí služeb při selhání je skupina 1+n nezávislých serverů (uzlů), které spolupracují na zvýšení dostupnosti aplikací a služeb. Pokud dojde k selhání uzlu, clustering s podporou převzetí služeb při selhání systému Windows Server vypočítá počet selhání, ke kterým může dojít při zachování clusteru v pořádku za účelem poskytování aplikací a služeb. Můžete si vybrat z různých režimů kvora k dosažení clustering převzetí služeb při selhání.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>Režimy kvora
Při použití clusteringu s podporou převzetí služeb při selhání systému Windows Server si můžete vybrat ze čtyř režimů kvora:

* **Uzel Většina**. Každý uzel clusteru může hlasovat. Klastr funguje pouze s většinou hlasů, tedy s více než polovinou hlasů. Doporučujeme tuto možnost pro clustery, které mají nerovnoměrný počet uzlů. Například tři uzly v clusteru se sedmi uzly může selhat a cluster upozorňování dosáhne většiny a nadále běží.  
* **Většina uzlů a disků**. Každý uzel a určený disk (disk s kopií clusteru) v úložišti clusteru mohou hlasovat, pokud jsou k dispozici a v komunikaci. Klastr funguje pouze s většinou hlasů, tedy s více než polovinou hlasů. Tento režim dává smysl v prostředí clusteru s sudým počtem uzlů. Pokud polovina uzlů a disk jsou online, cluster zůstane v pořádku stavu.
* **Většina uzlů a sdílení souborů**. Každý uzel plus určená sdílená složka (důkaz ní sdílení souborů), který správce vytvoří, může hlasovat bez ohledu na to, zda jsou k dispozici uzly a sdílená složka a komunikace. Klastr funguje pouze s většinou hlasů, tedy s více než polovinou hlasů. Tento režim dává smysl v prostředí clusteru s sudým počtem uzlů. Je podobný režimu většina uzlů a disků, ale používá sdílenou složku s kopií clusteru namísto disku s kopií clusteru. Tento režim je snadno implementovat, ale pokud samotná sdílená složka není vysoce dostupná, může se stát jediným bodem selhání.
* **Žádná většina: Pouze disk**. Cluster má kvorum, pokud je k dispozici jeden uzel a v komunikaci s určitým diskem v úložišti clusteru. Ke clusteru se mohou připojit pouze uzly, které jsou také v komunikaci s tímto diskem. Doporučujeme tento režim nepoužívat.

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>Místní clustering s podporou převzetí služeb při selhání systému Windows Server
Obrázek 1 znázorňuje shluk dvou uzlů. Pokud síťové připojení mezi uzly selže a oba uzly zůstanou v provozu, disk nebo sdílená složka kvora určí, který uzel bude nadále poskytovat aplikace a služby clusteru. Uzel, který má přístup k disku nebo sdílené složce kvora je uzel, který zajišťuje, že služby pokračovat.

Vzhledem k tomu, že tento příklad používá cluster se dvěma uzly, používáme režim kvora většina uzlů a sdílení souborů. Uzel a disk majorita je také platná možnost. V produkčním prostředí doporučujeme použít disk kvora. Můžete použít technologii síťového a úložného systému, aby byla vysoce dostupná.

![Obrázek 1: Příklad konfigurace clusteru s podporou převzetí služeb při selhání systému Windows Server pro SAP ASCS/SCS v Azure][sap-ha-guide-figure-1000]

_**Obrázek 1:** Příklad konfigurace clusteru s podporou převzetí služeb při selhání systému Windows Server pro SAP ASCS/SCS v Azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>Sdílené úložiště
Obrázek 1 také ukazuje cluster sdíleného úložiště se dvěma uzny. V místním clusteru sdíleného úložiště všechny uzly v clusteru detekují sdílené úložiště. Uzamykací mechanismus chrání data před poškozením. Všechny uzly můžete zjistit, pokud jiný uzel selže. Pokud jeden uzel selže, zbývající uzel převezme vlastnictví prostředků úložiště a zajišťuje dostupnost služeb.

> [!NOTE]
> Nepotřebujete sdílené disky pro vysokou dostupnost s některými aplikacemi DBMS, jako je například sql server. SQL Server Always On replikuje data dbms a soubory protokolu z místního disku jednoho uzlu clusteru na místní disk jiného uzlu clusteru. V takovém případě konfigurace clusteru Windows nepotřebuje sdílený disk.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>Sítě a překlad názvů
Klientské počítače se do clusteru dostanou přes virtuální IP adresu a název virtuálního hostitele, který poskytuje server DNS. Místní uzly a server DNS mohou zpracovávat více adres IP.

V typickém nastavení používáte dvě nebo více síťových připojení:

* Vyhrazené připojení k úložišti
* Síťové připojení interního clusteru pro prezenční signál
* Veřejná síť, kterou klienti používají k připojení ke clusteru

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>Clustering s podporou převzetí služeb při selhání systému Windows Server v Azure
Ve srovnání s holým nebo privátním cloudovým nasazením vyžadují virtuální počítače Azure další kroky ke konfiguraci clusteru s podporou převzetí služeb při selhání windows serveru. Při vytváření sdíleného disku clusteru je třeba nastavit několik adres IP a názvů virtuálních hostitelů pro instanci SAP ASCS/SCS.

V tomto článku se zabýváme klíčovými koncepty a dalšími kroky potřebnými k vytvoření clusteru centrálních služeb SAP s vysokou dostupností v Azure. Ukážeme vám, jak nastavit nástroj třetí strany SIOS DataKeeper a jak nakonfigurovat interní nástroj pro vyrovnávání zatížení Azure. Pomocí těchto nástrojů můžete vytvořit cluster s podporou převzetí služeb při selhání systému Windows s důkazem sdílení souborů v Azure.

![Obrázek 2: Konfigurace clusteru s podporou převzetí služeb při selhání systému Windows Server v Azure bez sdíleného disku][sap-ha-guide-figure-1001]

_**Obrázek 2:** Konfigurace clusteringu s podporou převzetí služeb při selhání systému Windows Server v Azure bez sdíleného disku_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>Sdílený disk v Azure se sios ovým datovým keeperem
Potřebujete sdílené úložiště clusteru pro instanci SAP ASCS/SCS s vysokou dostupností. Od září 2016 Azure nenabízí sdílené úložiště, které můžete použít k vytvoření clusteru sdíleného úložiště. Pomocí softwaru jiných výrobců SIOS DataKeeper Cluster Edition můžete vytvořit zrcadlené úložiště, které simuluje sdílené úložiště clusteru. Řešení SIOS poskytuje synchronní replikaci dat v reálném čase. Tímto způsobem můžete vytvořit prostředek sdíleného disku pro cluster:

1. Připojte další virtuální pevný disk Azure (VHD) ke každému virtuálnímu počítači (VM) v konfiguraci clusteru Windows.
2. Spusťte sios DataKeeper Cluster Edition na obou uzlech virtuálních strojů.
3. Konfigurace aplikace SIOS DataKeeper Cluster Edition tak, aby zrcadlila obsah dalšího svazku připojeného v pevném disku virtuálního pevného disku ze zdrojového virtuálního počítače do dalšího svazku připojeného v pevném disku cílového virtuálního počítače. Sios DataKeeper abstrahuje zdrojové a cílové místní svazky a pak je prezentuje clusteringu s podporou převzetí služeb při selhání systému Windows Server jako jeden sdílený disk.

Získejte více informací o [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Obrázek 3: Konfigurace clusteru s podporou převzetí služeb při selhání systému Windows Server v Azure pomocí správce dat SIOS][sap-ha-guide-figure-1002]

_**Obrázek 3:** Konfigurace clusteru s podporou převzetí služeb při selhání systému Windows Server v Azure pomocí správce dat SIOS_

> [!NOTE]
> Nepotřebujete sdílené disky pro vysokou dostupnost s některými produkty DBMS, jako je SQL Server. SQL Server Always On replikuje data dbms a soubory protokolu z místního disku jednoho uzlu clusteru na místní disk jiného uzlu clusteru. V takovém případě konfigurace clusteru Windows nepotřebuje sdílený disk.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Překlad názvů v Azure
Cloudová platforma Azure nenabízí možnost konfigurace virtuálních IP adres, jako jsou plovoucí IP adresy. Potřebujete alternativní řešení pro nastavení virtuální IP adresy pro dosažení prostředku clusteru v cloudu.
Azure má interní vyrovnávání zatížení ve službě Azure Load Balancer. S interním nástrojem pro vyrovnávání zatížení se klienti dostanou do clusteru přes virtuální IP adresu clusteru.
Je třeba nasadit interní vyrovnávání zatížení ve skupině prostředků, která obsahuje uzly clusteru. Potom nakonfigurujte všechna potřebná pravidla předávání portů pomocí portů sondy interního systému vyrovnávání zatížení.
Klienti se mohou připojit prostřednictvím názvu virtuálního hostitele. Server DNS řeší adresu IP clusteru a interní správce zatížení zpracovává předávání portů do aktivního uzlu clusteru.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>Vysoká dostupnost SAP NetWeaver v infrastruktuře Azure jako služby (IaaS)
Chcete-li dosáhnout vysoké dostupnosti aplikace SAP, například pro softwarové součásti SAP, je třeba chránit následující součásti:

* Instance aplikačního serveru SAP
* Instance SAP ASCS/SCS
* Server DBMS

Další informace o ochraně součástí SAP ve scénářích s vysokou dostupností najdete v [tématu Plánování a implementace virtuálních počítačů Azure pro SAP NetWeaver](planning-guide.md).

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Aplikační server SAP s vysokou dostupností
Obvykle nepotřebujete konkrétní řešení s vysokou dostupností pro aplikační server SAP a instance dialogů. Vysokou dostupnost dosáhnete redundancí a nakonfigurujete více instancí dialogových oken v různých instancích virtuálních počítačů Azure. Měli byste mít alespoň dvě instance aplikace SAP nainstalované ve dvou instancích virtuálních počítačů Azure.

![Obrázek 4: Server aplikací SAP s vysokou dostupností][sap-ha-guide-figure-2000]

_**Obrázek 4:** Aplikační server SAP s vysokou dostupností_

Všechny virtuální počítače, které hostují instance aplikačního serveru SAP, musí být umístěny ve stejné sadě dostupnosti Azure. Sada dostupnosti Azure zajišťuje, že:

* Všechny virtuální počítače jsou součástí stejné domény upgradu. Upgradovací doména například zajišťuje, že virtuální počítače nejsou aktualizovány současně během plánovaných prostojů údržby.
* Všechny virtuální počítače jsou součástí stejné domény selhání. Doména selhání například zajišťuje, že virtuální počítače jsou nasazeny tak, aby žádný jediný bod selhání nemá vliv na dostupnost všech virtuálních počítačů.

Další informace o tom, jak [spravovat dostupnost virtuálních počítačů][virtual-machines-manage-availability].

Vzhledem k tomu, že účet úložiště Azure je potenciální jediný bod selhání, je důležité mít alespoň dva účty úložiště Azure, ve kterých jsou distribuovány alespoň dva virtuální počítače. V ideálním nastavení by se disky každého virtuálního počítače, ve které je spuštěna instance dialogového okna SAP, nasazují v jiném účtu úložiště.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>Instance SAP ASCS/SCS s vysokou dostupností
Obrázek 5 je příkladem instance SAP ASCS/SCS s vysokou dostupností.

![Obrázek 5: Instance SAP ASCS/SCS s vysokou dostupností][sap-ha-guide-figure-2001]

_**Obrázek 5:** Instance SAP ASCS/SCS s vysokou dostupností_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>Vysoká dostupnost instance SAP ASCS/SCS díky clusteringu s podporou převzetí služeb při selhání windows serveru v Azure
Ve srovnání s holým nebo privátním cloudovým nasazením vyžadují virtuální počítače Azure další kroky ke konfiguraci clusteru s podporou převzetí služeb při selhání windows serveru. K vytvoření clusteru s podporou převzetí služeb při selhání systému Windows potřebujete sdílený disk clusteru, několik adres IP, několik názvů virtuálních hostitelů a interní nástroj pro vyrovnávání zatížení Azure pro clustering instance SAP ASCS/SCS. Podrobněji o tom diskutujeme později v článku.

![Obrázek 6: Clustering s podporou převzetí služeb při selhání systému Windows Server pro konfiguraci SAP ASCS/SCS v Azure pomocí správce dat SIOS][sap-ha-guide-figure-1002]

_**Obrázek 6:** Clustering s podporou převzetí služeb při selhání systému Windows Server pro konfiguraci SAP ASCS/SCS v Azure pomocí správce dat SIOS_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Instance DBMS s vysokou dostupností
DBMS je také jediným kontaktním místem v systému SAP. Je třeba jej chránit pomocí řešení s vysokou dostupností. Obrázek 7 znázorňuje řešení SQL Server always on high-availability v Azure, s clusteringem s podporou převzetí služeb při selhání Windows Serveru a interním nástroje pro vyrovnávání zatížení Azure. SQL Server Always On replikuje data dbms a soubory protokolu pomocí vlastní replikace DBMS. V takovém případě nepotřebujete sdílené disky clusteru, což zjednodušuje celé nastavení.

![Obrázek 7: Příklad sap DBMS s vysokou dostupností s SQL Serverem vždy zapnutým][sap-ha-guide-figure-2003]

_**Obrázek 7:** Příklad vysoce dostupnosti SAP DBMS s SQL Serverem vždy zapnutým_

Další informace o clusteringu SQL Serveru v Azure pomocí modelu nasazení Azure Resource Manageru najdete v těchto článcích:

* [Konfigurace skupiny dostupnosti always on ve virtuálních počítačích Azure ručně pomocí Správce prostředků][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurace interního zařízení pro vyrovnávání zatížení Azure pro skupinu dostupnosti always on v Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>Scénáře nasazení s vysokou dostupností od konce

### <a name="deployment-scenario-using-architectural-template-1"></a>Scénář nasazení pomocí architektonické šablony 1

Obrázek 8 znázorňuje příklad architektury sap NetWeaver s vysokou dostupností v Azure pro **jeden** systém SAP. Tento scénář je nastaven takto:

- Jeden vyhrazený cluster se používá pro instanci SAP ASCS/SCS.
- Jeden vyhrazený cluster se používá pro instanci DBMS.
- Instance aplikačního serveru SAP se nasazují ve vlastních vyhrazených virtuálních serverech.

![Obrázek 8: Vysoce dostupné architektonické šablony SAP 1 s vyhrazeným clusterem pro ASCS/SCS a DBMS][sap-ha-guide-figure-2004]

_**Obrázek 8:** Vysoce dostupné architektonické šablony SAP 1, vyhrazených clusterů pro ASCS/SCS a DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scénář nasazení pomocí architektonické šablony 2

Obrázek 9 znázorňuje příklad architektury sap NetWeaver s vysokou dostupností v Azure pro **jeden** systém SAP. Tento scénář je nastaven takto:

- Jeden vyhrazený cluster se používá jak pro instanci SAP ASCS/SCS, **tak** pro SYSTÉM DBMS.
- Instance aplikačního serveru SAP se nasazují ve vlastních vyhrazených virtuálních serverech.

![Obrázek 9: Vysoce dostupné architektonické šabloně SAP 2 s vyhrazeným clusterem pro ASCS/SCS a vyhrazeným clusterem pro DBMS][sap-ha-guide-figure-2005]

_**Obrázek 9:** Vysoce dostupné architektonické šablony SAP 2 s vyhrazeným clusterem pro ASCS/SCS a vyhrazeným clusterem pro DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scénář nasazení pomocí architektonické šablony 3

Obrázek 10 znázorňuje příklad architektury sap NetWeaver s vysokou dostupností&gt; v &lt;Azure&gt;pro **dva** systémy SAP, se &lt;SID1 a SID2 . Tento scénář je nastaven takto:

- Jeden vyhrazený cluster se používá **jak** pro instanci SAP ASCS/SCS SID1, tak pro instanci SAP ASCS/SCS SID2 (jeden cluster). *and*
- Jeden vyhrazený cluster se používá pro DBMS SID1 a jiný vyhrazený cluster se používá pro DBMS SID2 (dva clustery).
- Instance aplikačního serveru SAP pro systém SAP SID1 mají své vlastní vyhrazené virtuální uživatele.
- Instance aplikačního serveru SAP pro systém SAP SID2 mají své vlastní vyhrazené virtuální uživatele.

![Obrázek 10: Vysoce dostupné architektonické šablona SAP 3 s vyhrazeným clusterem pro různé instance ASCS/SCS][sap-ha-guide-figure-6003]

_**Obrázek 10:** Vysoce dostupné architektonické šablony SAP 3 s vyhrazeným clusterem pro různé instance ASCS/SCS_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>Příprava infrastruktury

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Příprava infrastruktury pro architektonickou šablonu 1
Šablony Azure Resource Managerpro SAP pomáhají zjednodušit nasazení požadovaných prostředků.

Třívrstvé šablony ve Správci prostředků Azure také podporují scénáře s vysokou dostupností, například v architektonické šabloně 1, která má dva clustery. Každý cluster je sap jediný bod selhání pro SAP ASCS/SCS a DBMS.

Tady můžete získat šablony Azure Resource Manageru pro ukázkový scénář, který popisujeme v tomto článku:

* [Image Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Vlastní obrázek](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Příprava infrastruktury pro architektonickou šablonu 1:

- Na portálu Azure v okně **Parametry** v poli **SYSTEMAVAILABILITY** vyberte **MOŽNOST HA**.

  ![Obrázek 11: Nastavení sap u vysoce dostupnosti parametrů Azure Resource Manageru][sap-ha-guide-figure-3000]

_**Obrázek 11:** Nastavení parametrů Azure Resource Manageru s vysokou dostupností SAP_


  Šablony vytvářejí:

  * **Virtuální počítače**:
    * Virtuální počítače aplikačního serveru SAP: <*SAPSystemSID*>-di-<*Číslo*>
    * Virtuální počítače clusteru ASCS/SCS: <*SAPSystemSID*>-ascs-<*číslo*>
    * Cluster DBMS: <*SAPSystemSID*>-db-<*číslo*>

  * **Síťové karty pro všechny virtuální počítače s přidruženými IP adresami**:
    * <*SAPSystemSID*>-nic-di-<*Číslo*>
    * <*SAPSystemSID*>-nic-ascs-<*číslo*>
    * <*SAPSystemSID*>-nic-db-<*číslo*>

  * **Účty služby Azure Storage**

  * **Skupiny dostupnosti** pro:
    * Virtuální počítače aplikačního serveru SAP: <*SAPSystemSID*>-avset-di
    * Virtuální počítače clusteru SAP ASCS/SCS: <*SAPSystemSID*>-avset-ascs
    * Virtuální počítače clusteru DBMS: <*SAPSystemSID*>-avset-db

  * **Interní systém pro vyrovnávání zatížení Azure**:
    * Se všemi porty pro instanci ASCS/SCS a IP adresu <*SAPSystemSID*>-lb-ascs
    * Se všemi porty pro SQL Server DBMS a IP adresy <*SAPSystemSID*>-lb-db

  * **Skupina zabezpečení sítě**: <*SAPSystemSID*>-nsg-ascs-0  
    * S otevřeným externím portem RDP (Remote Desktop Protocol) do <virtuálním *počítači SAPSystemSID*>-ascs-0

> [!NOTE]
> Všechny IP adresy síťových karet a interních vykladačů zatížení Azure jsou ve výchozím nastavení **dynamické.** Změňte je na **statické** ADRESY IP. Popisujeme, jak to udělat později v článku.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Nasazení virtuálních počítačů s připojením k podnikové síti (mezi místními) pro použití v produkčním prostředí
Pro produkční systémy SAP nasazujte virtuální počítače Azure s připojením k podnikové síti pomocí azure site-to-site VPN nebo Azure ExpressRoute.

> [!NOTE]
> Můžete použít instanci virtuální sítě Azure. Virtuální síť a podsíť již byly vytvořeny a připraveny.
>
>

1. Na portálu Azure vyberte v okně **Parameters** v poli **NEWOREXISTINGSUBNET** **existující**.
2. Do pole **SUBNETID** přidejte úplný řetězec připraveného Podsíťového id sítě Azure, kde plánujete nasazení virtuálních počítačů Azure.
3. Pokud chcete získat seznam všech síťových podsítí Azure, spusťte tento příkaz PowerShellu:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   V poli **ID** se zobrazí **SUBNETID**.
4. Chcete-li získat seznam všech hodnot **SUBNETID,** spusťte tento příkaz prostředí PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **SUBNETID** vypadá takto:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Nasazení instancí SAP pouze pro cloud pro testování a ukázku
Systém SAP s vysokou dostupností můžete nasadit v modelu nasazení pouze v cloudu. Tento druh nasazení je primárně užitečné pro ukázkové a testovací případy použití. Není vhodný pro případy použití výroby.

- Na portálu Azure vyberte v okně **Parameters** v poli **NEWOREXISTINGSUBNET** **nový**. Ponechte pole **SUBNETID** prázdné.

  Šablona SAP Azure Resource Manager automaticky vytvoří virtuální síť Azure a podsíť.

> [!NOTE]
> Musíte také nasadit alespoň jeden vyhrazený virtuální počítač pro službu Active Directory a SLUŽBU DNS ve stejné instanci virtuální sítě Azure. Šablona nevytváří tyto virtuální počítače.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Příprava infrastruktury pro architektonickou šablonu 2

Pomocí této šablony Azure Resource Manager pro SAP můžete zjednodušit nasazení požadovaných prostředků infrastruktury pro architektonickou šablonu SAP 2.

Tady můžete získat šablony Azure Resource Manageru pro tento scénář nasazení:

* [Image Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Vlastní obrázek](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Příprava infrastruktury pro architektonickou šablonu 3

Můžete připravit infrastrukturu a nakonfigurovat SAP pro **multi-SID**. Můžete například přidat další instanci SAP ASCS/SCS do *existující* konfigurace clusteru. Další informace najdete [v tématu Konfigurace další instance SAP ASCS/SCS do existující konfigurace clusteru k vytvoření konfigurace SAP s více SID ve Správci prostředků Azure][sap-ha-multi-sid-guide].

Pokud chcete vytvořit nový cluster s více SID, můžete použít šablony rychlého spuštění více SID [na GitHubu](https://github.com/Azure/azure-quickstart-templates).
Chcete-li vytvořit nový cluster s více sid, je třeba nasadit následující tři šablony:

* [Šablona ASCS/SCS](#ASCS-SCS-template)
* [Šablona databáze](#database-template)
* [Šablona aplikačních serverů](#application-servers-template)

V následujících částech jsou uvedeny další podrobnosti o šablonách a parametrech, které je třeba v šablonách zadat.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>Šablona ASCS/SCS

Šablona ASCS/SCS nasazuje dva virtuální počítače, které můžete použít k vytvoření clusteru s podporou převzetí služeb při selhání systému Windows Server, který hostuje více instancí ASCS/SCS.

Chcete-li nastavit šablonu ASCS/SCS multi-SID, zadejte do [šablony ASCS/SCS multi-SID][sap-templates-3-tier-multisid-xscs-marketplace-image]hodnoty pro následující parametry:

  - **Předpona zdroje**.  Nastavte předponu prostředku, která se používá k předponě všech prostředků, které jsou vytvořeny během nasazení. Vzhledem k tomu, že prostředky nepatří pouze do jednoho systému SAP, předpona prostředku není SID jednoho systému SAP.  Předpona musí být mezi **třemi a šesti znaky**.
  - **Typ zásobníku**. Vyberte typ zásobníku systému SAP. V závislosti na typu zásobníku má Nástroj pro vyrovnávání zatížení Azure jednu (jenom ABAP nebo Java) nebo dvě privátní IP adresy (ABAP+Java) na jeden systém SAP.
  -  **Typ operačního .** Vyberte operační systém virtuálních počítačů.
  -  **Počet systémů SAP**. Vyberte počet systémů SAP, které chcete nainstalovat v tomto clusteru.
  -  **Dostupnost systému**. Vyberte **možnost HA**.
  -  **Admin Uživatelské jméno a admin heslo**. Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
  -  **Nová nebo existující podsíť**. Nastavte, zda má být vytvořena nová virtuální síť a podsíť nebo zda má být použita existující podsíť. Pokud už máte virtuální síť, která je připojená k místní síti, vyberte **existující**.
  -  **Id podsítě**. Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k čemuž by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá takto: /subscriptions/<*id předplatného*>/resourceGroups/<*název skupiny prostředků*>/providers/Microsoft.Network/virtualNetworks/<název virtuální *sítě*>/podsítě/<*název podsítě*>

Šablona nasazuje jednu instanci Azure Load Balancer, která podporuje více systémů SAP.

- Instance ASCS jsou konfigurovány pro instanci číslo 00, 10, 20...
- Instance SCS jsou konfigurovány pro instanci číslo 01, 11, 21...
- Instance serveru ascs enqueue replication server (ERS) (pouze Linux) jsou konfigurovány pro instanci číslo 02, 12, 22...
- Instance SCS ERS (pouze Linux) jsou konfigurovány pro například číslo 03, 13, 23...

Nástroj pro vyrovnávání zatížení obsahuje 1 (2 pro Linux) VIP(y), 1x VIP pro ASCS /SCS a 1x VIP pro ERS (pouze Linux).

Následující seznam obsahuje všechna pravidla vyrovnávání zatížení (kde x je číslo systému SAP, například 1, 2, 3...):
- Porty specifické pro Windows pro každý systém SAP: 445, 5985
- Porty ASCS (číslo instance x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Porty SCS (číslo instance x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Porty ASCS ERS na Linuxu (číslo instance x2): 33x2, 5x213, 5x214, 5x216
- Porty SCS ERS na Linuxu (číslo instance x3): 33x3, 5x313, 5x314, 5x316

Nástroj pro vyrovnávání zatížení je nakonfigurován tak, aby používal následující porty sondy (kde x je číslo systému SAP, například 1, 2, 3...):
- Port sondy interního systému pro vyrovnávání zatížení ASCS/SCS: 620x0
- PORT interní sondy ERS (pouze Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a>Šablona databáze

Šablona databáze nasazuje jeden nebo dva virtuální počítače, které můžete použít k instalaci systému správy relačních databází (RDBMS) pro jeden systém SAP. Pokud například nasadíte šablonu ASCS/SCS pro pět systémů SAP, budete muset tuto šablonu nasadit pětkrát.

Chcete-li nastavit databázovou šablonu multi-SID, zadejte do [šablony database multi-SID][sap-templates-3-tier-multisid-db-marketplace-image]hodnoty pro následující parametry:

- **Id systému Sap**. Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID bude použito jako předpona pro prostředky, které jsou nasazeny.
- **Typ operačního .** Vyberte operační systém virtuálních počítačů.
- **Dbtype**. Vyberte typ databáze, kterou chcete do clusteru nainstalovat. Chcete-li nainstalovat server Microsoft SQL Server, vyberte **sql.** Pokud plánujete instalaci SAP HANA do virtuálních počítačů, vyberte **hana.** Ujistěte se, že vyberete správný typ operačního systému: vyberte **Windows** pro SQL a vyberte linuxovou distribuci pro HANA. Nástroj pro vyrovnávání zatížení Azure, který je připojený k virtuálním počítačům, bude nakonfigurován tak, aby podporoval vybraný typ databáze:
  * **SQL**. Vyrovnávání zatížení bude zatížení port 1433. Ujistěte se, že tento port používáte pro nastavení serveru SQL Server Always On.
  * **Hana**. Vykladač zatížení bude vyvažovací porty 35015 a 35017. Ujistěte se, že jste nainstalovali SAP HANA s instancí číslo **50**.
  Vykladač zatížení použije port sondy 62550.
- **Velikost systému Sap**. Nastavte počet SAPS, které nový systém poskytne. Pokud si nejste jisti, kolik SAPS bude systém vyžadovat, zeptejte se svého technologického partnera SAP nebo systémového integrátora.
- **Dostupnost systému**. Vyberte **možnost HA**.
- **Admin Uživatelské jméno a admin heslo**. Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
- **Id podsítě**. Zadejte ID podsítě, kterou jste použili při nasazení šablony ASCS/SCS, nebo ID podsítě, která byla vytvořena jako součást nasazení šablony ASCS/SCS.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a>Šablona aplikačních serverů

Šablona aplikačních serverů nasazuje dva nebo více virtuálních počítačů, které lze použít jako instance aplikačního serveru SAP pro jeden systém SAP. Pokud například nasadíte šablonu ASCS/SCS pro pět systémů SAP, budete muset tuto šablonu nasadit pětkrát.

Chcete-li nastavit šablonu aplikačních serverů s více SID, zadejte do [šablony více SID aplikačních serverů][sap-templates-3-tier-multisid-apps-marketplace-image]hodnoty pro následující parametry:

  -  **Id systému Sap**. Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID bude použito jako předpona pro prostředky, které jsou nasazeny.
  -  **Typ operačního .** Vyberte operační systém virtuálních počítačů.
  -  **Velikost systému Sap**. Počet SAPS nový systém bude poskytovat. Pokud si nejste jisti, kolik SAPS bude systém vyžadovat, zeptejte se svého technologického partnera SAP nebo systémového integrátora.
  -  **Dostupnost systému**. Vyberte **možnost HA**.
  -  **Admin Uživatelské jméno a admin heslo**. Vytvořte nového uživatele, který lze použít k přihlášení k počítači.
  -  **Id podsítě**. Zadejte ID podsítě, kterou jste použili při nasazení šablony ASCS/SCS, nebo ID podsítě, která byla vytvořena jako součást nasazení šablony ASCS/SCS.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Virtuální síť Azure
V našem příkladu je adresní prostor virtuální sítě Azure 10.0.0.0/16. Existuje jedna podsíť s názvem **Podsíť**s rozsahem adres 10.0.0.0/24. Všechny virtuální počítače a interní nástroje pro vyrovnávání zatížení se nasazují v této virtuální síti.

> [!IMPORTANT]
> Neprovávejte žádné změny v nastavení sítě v hostovaném operačním systému. To zahrnuje adresy IP, servery DNS a podsíť. Nakonfigurujte všechna nastavení sítě v Azure. Služba DHCP (Dynamic Host Configuration Protocol) rozšíří vaše nastavení.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>IP adresy DNS

Chcete-li nastavit požadované adresy IP DNS, postupujte takto.

1. Na webu Azure Portal se v okně **DNS serverů** ujistěte, že je možnost **serverů DNS** virtuální sítě nastavená na vlastní **DNS**.
2. Vyberte nastavení na základě typu sítě, kterou máte. Další informace najdete v následujících materiálech:
   * Přidejte adresy IP místních serverů DNS.  
   Místní servery DNS můžete rozšířit na virtuální počítače, které běží v Azure. V takovém scénáři můžete přidat IP adresy virtuálních počítačů Azure, na kterých spustíte službu DNS.
   * Pro nasazení izolovaná v Azure: Nasaďte další virtuální počítač ve stejné instanci virtuální sítě, která slouží jako server DNS. Přidejte IP adresy virtuálních počítačů Azure, které jste nastavili pro spuštění služby DNS.

   ![Obrázek 12: Konfigurace serverů DNS pro virtuální síť Azure][sap-ha-guide-figure-3001]

   _**Obrázek 12:** Konfigurace serverů DNS pro virtuální síť Azure_

   > [!NOTE]
   > Pokud změníte IP adresy serverů DNS, budete muset restartovat virtuální počítače Azure použít změnu a šířit nové servery DNS.
   >
   >

V našem příkladu je služba DNS nainstalována a nakonfigurována na těchto virtuálních počítačích s Windows:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První DNS server |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Druhý server DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Názvy hostitelů a statické adresy IP pro clusterověvěvěvě vsap ASCS/SCS a clusterovnou instanci DBMS

Pro místní nasazení potřebujete tyto vyhrazené názvy hostitelů a IP adresy:

| Role názvu virtuálního hostitele | Název virtuálního hostitele | Virtuální statická IP adresa |
| --- | --- | --- |
| SAP ASCS/SCS první název virtuálního hostitele clusteru (pro správu clusteru) |pr1-ascs-vir |10.0.0.42 |
| Název virtuálního hostitele instance SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Název virtuálního hostitele SAP DBMS druhý cluster (správa clusteru) |pr1-dbms-vir |10.0.0.32 |

Při vytváření clusteru vytvořte názvy virtuálních hostitelů **pr1-ascs-vir** a **pr1-dbms-vir** a přidružené IP adresy, které spravují samotný cluster. Informace o tom, jak to provést, naleznete [v tématu Shromažďování uzlů clusteru v konfiguraci clusteru][sap-ha-guide-8.12.1].

Můžete ručně vytvořit další dva názvy virtuálních hostitelů, **pr1-ascs-sap** a **pr1-dbms-sap**a přidružené IP adresy na serveru DNS. Clusterovaná instance SAP ASCS/SCS a clusterovaná instance DBMS používají tyto prostředky. Informace o tom, jak to provést, naleznete [v tématu Vytvoření názvu virtuálního hostitele pro clusterově ovou instanci SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Nastavení statických IP adres pro virtuální počítače SAP
Po nasazení virtuálních počítačů pro použití v clusteru je potřeba nastavit statické IP adresy pro všechny virtuální počítače. Udělejte to v konfiguraci virtuální sítě Azure a ne v hostovaném operačním systému.

1. Na portálu Azure vyberte**IP adresa****nastavení** > **síťové karty** >  **skupiny** > prostředků .
2. V okně **IP adresy** vyberte v části **Přiřazení** **položku Static**. Do pole **IP adresa** zadejte adresu IP, kterou chcete použít.

   > [!NOTE]
   > Pokud změníte IP adresu síťové karty, budete muset restartovat virtuální počítače Azure použít změnu.  
   >
   >

   ![Obrázek 13: Nastavení statických IP adres pro síťovou kartu každého virtuálního počítače][sap-ha-guide-figure-3002]

   _**Obrázek 13:** Nastavení statických IP adres pro síťovou kartu každého virtuálního počítače_

   Tento krok opakujte pro všechna síťová rozhraní, tj.

V našem příkladu máme tyto virtuální počítače a statické IP adresy:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První instance aplikačního serveru SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Druhá instance aplikačního serveru SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Poslední instance aplikačního serveru SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| První uzel clusteru pro instanci ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Druhý uzel clusteru pro instanci ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| První uzel clusteru pro instanci DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Druhý uzel clusteru pro instanci DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Nastavení statické IP adresy pro interní systém vyrovnávání zatížení Azure

Šablona SAP Azure Resource Manager vytvoří interní nástroje pro vyrovnávání zatížení Azure, který se používá pro cluster instancí SAP ASCS/SCS a cluster DBMS.

> [!IMPORTANT]
> IP adresa názvu virtuálního hostitele SAP ASCS/SCS je stejná jako IP adresa interního nástroje pro vyrovnávání zatížení SAP ASCS/SCS: **pr1-lb-ascs**.
> IP adresa virtuálního názvu DBMS je stejná jako IP adresa interního nástroje pro vyrovnávání zatížení DBMS: **pr1-lb-dbms**.
>
>

Nastavení statické IP adresy pro interní systém vyrovnávání zatížení Azure:

1. Počáteční nasazení nastaví adresu IP interního vykladače zatížení na **dynamickou**. Na portálu Azure vyberte v okně **IP adresy** v části **Přiřazení** **možnost Statická**.
2. Nastavte IP adresu interního nástroje pro vyrovnávání zatížení **pr1-lb-ascs** na IP adresu názvu virtuálního hostitele instance SAP ASCS/SCS.
3. Nastavte IP adresu interního nástroje pro vyrovnávání zatížení **pr1-lb-dbms** na IP adresu názvu virtuálního hostitele instance DBMS.

   ![Obrázek 14: Nastavení statických IP adres pro interní vyrovnávání zatížení pro instanci SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Obrázek 14:** Nastavení statických IP adres pro interní systém vyrovnávání zatížení pro instanci SAP ASCS/SCS_

V našem příkladu máme dva interní vykladače zatížení Azure, které mají tyto statické IP adresy:

| Role interního vyrovnávání zatížení Azure | Název interního systému vyrovnávání zatížení Azure | Statická IP adresa |
| --- | --- | --- |
| Interní systém vyrovnávání zatížení instance SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Interní systém vyrovnávání zatížení SAP DBMS |pr1-lb-dbms |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure

Šablona SAP Azure Resource Manager vytvoří porty, které potřebujete:
* Instance ASCS ABAP s výchozím číslem instance **00**
* Instance Java SCS s výchozím číslem instance **01**

Při instalaci instance SAP ASCS/SCS je nutné použít výchozí číslo instance **00** pro instanci ABAP ASCS a výchozí číslo instance **01** pro instanci Java SCS.

Dále vytvořte požadované koncové body internal load balancing pro porty SAP NetWeaver.

Chcete-li vytvořit požadované koncové body internal vyrovnávání zatížení, vytvořte nejprve tyto koncové body vyrovnávání zatížení pro porty ASCS SAP NetWeaver:

| Název pravidla vyrovnávání služby/zatížení | Výchozí čísla portů | Konkrétní porty pro (instance ASCS s číslem instance 00) (ERS s 10) |
| --- | --- | --- |
| Enqueue Server / *lbrule3200* |32<*Číslo instance*> |3200 |
| Server zpráv ABAP / *lbrule3600* |36<*Číslo instance*> |3600 |
| Interní zpráva ABAP / *lbrule3900* |39<*Číslo instance*> |3900 |
| Server zpráv HTTP / *Lbrule8100* |81<*Číslo instance*> |8100 |
| Služba SAP Start ASCS HTTP / *Lbrule50013* |5<*Číslo instance*>13 |50013 |
| Sap Start Service ASCS HTTPS / *Lbrule50014* |5<*Číslo instance*>14 |50014 |
| Replikace fronty / *Lbrule50016* |5<*Číslo instance*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*Číslo instance*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*Číslo instance*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Sdílení souborů *Lbrule445* | |445 |

_**Tabulka 1:** Čísla portů instancí ASCS SAP NetWeaver ABAP_

Potom vytvořte tyto koncové body vyrovnávání zatížení pro porty SAP NetWeaver Java SCS:

| Název pravidla vyrovnávání služby/zatížení | Výchozí čísla portů | Konkrétní porty pro (instance SCS s číslem instance 01) (ERS s 11) |
| --- | --- | --- |
| Enqueue Server / *lbrule3201* |32<*Číslo instance*> |3201 |
| Server brány / *lbrule3301* |33<*Číslo instance*> |3301 |
| Java Message Server / *lbrule3900* |39<*Číslo instance*> |3901 |
| Server zpráv HTTP / *Lbrule8101* |81<*Číslo instance*> |8101 |
| Služba SAP Start SCS HTTP / *Lbrule50113* |5<*Číslo instance*>13 |50113 |
| Služba SAP Start SCS HTTPS / *Lbrule50114* |5<*Číslo instance*>14 |50114 |
| Replikace fronty / *Lbrule50116* |5<*Číslo instance*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*Číslo instance*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5<*Číslo instance*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Sdílení souborů *Lbrule445* | |445 |

_**Tabulka 2:** Čísla portů instancí Java SCS SAP NetWeaver_

![Obrázek 15: Výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure][sap-ha-guide-figure-3004]

_**Obrázek 15:** Výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure_

Nastavte IP adresu nástroje pro vyrovnávání zatížení **pr1-lb-dbms** na IP adresu názvu virtuálního hostitele instance DBMS.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure

Pokud chcete použít různá čísla pro instance SAP ASCS nebo SCS, musíte změnit názvy a hodnoty jejich portů z výchozích hodnot.

1. Na webu Azure Portal vyberte >  ** < *SID*>-lb-ascs**pravidla**vyrovnávání zatížení**.
2. Pro všechna pravidla vyrovnávání zatížení, která patří do instance SAP ASCS nebo SCS, změňte tyto hodnoty:

   * Name (Název)
   * Port
   * Back-end port

   Chcete-li například změnit výchozí číslo instance ASCS z 00 na 31, je třeba provést změny pro všechny porty uvedené v tabulce 1.

   Zde je příklad aktualizace pro port *lbrule3200*.

   ![Obrázek 16: Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure][sap-ha-guide-figure-3005]

   _**Obrázek 16:** Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Přidání virtuálních počítačů Windows do domény

Po přiřazení statické IP adresy virtuálním počítačům přidejte virtuální počítače do domény.

![Obrázek 17: Přidání virtuálního počítače do domény][sap-ha-guide-figure-3006]

_**Obrázek 17:** Přidání virtuálního počítače do domény_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Přidání položek registru v obou uzlech clusteru instance SAP ASCS/SCS

Azure Load Balancer má interní vyrovnávání zatížení, který zavře připojení, když jsou připojení nečinná po nastavenou dobu (časový limit nečinnosti). Pracovní procesy SAP v instanci dialogu otevírají připojení k procesu zařazení SAP, jakmile je třeba odeslat první požadavek fronty/vyřazení z fronty. Tato připojení obvykle zůstávají vytvořena, dokud se nerestartuje pracovní proces nebo proces zařazení do fronty. Pokud je však připojení nečinné po nastavenou dobu, interní vyrovnávání zatížení Azure ukončí připojení. To není problém, protože pracovní proces SAP obnoví připojení k procesu zařazení do fronty, pokud již neexistuje. Tyto aktivity jsou dokumentovány v vývoji trasování procesů SAP, ale vytvářejí velké množství dalšího obsahu v těchto trasování. Je vhodné změnit protokol TCP/IP `KeepAliveTime` a `KeepAliveInterval` na obou uzlech clusteru. Zkombinujte tyto změny v parametrech Protokolu TCP/IP s parametry profilu SAP, které jsou popsány dále v článku.

Chcete-li přidat položky registru v obou uzlech clusteru instance SAP ASCS/SCS, přidejte nejprve tyto položky registru systému Windows v obou uzlech clusteru Systému Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parametry |
| --- | --- |
| Název proměnné |`KeepAliveTime` |
| Typ proměnné |REG_DWORD (desetinné číslo) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabulka 3:** Změna prvního parametru Protokolu TCP/IP_

Potom přidejte tyto položky registru systému Windows na obou uzlech clusteru Systému Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parametry |
| --- | --- |
| Název proměnné |`KeepAliveInterval` |
| Typ proměnné |REG_DWORD (desetinné číslo) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabulka 4:** Změna druhého parametru Protokolu TCP/IP_

**Chcete-li změny použít, restartujte oba uzly clusteru**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Nastavení clusteru clusteru s podporou převzetí služeb při selhání systému Windows Server pro instanci SAP ASCS/SCS

Nastavení clusteru clusteru s podporou převzetí služeb při selhání systému Windows Server pro instanci SAP ASCS/SCS zahrnuje tyto úkoly:

- Shromažďování uzlů clusteru v konfiguraci clusteru
- Konfigurace důkazu sdílení souborů clusteru

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Shromáždění uzlů clusteru v konfiguraci clusteru

1. V Průvodci přidáním role a funkcí přidejte clustering s podporou převzetí služeb při selhání do obou uzlů clusteru.
2. Nastavte cluster s podporou převzetí služeb při selhání pomocí Správce clusteru s podporou převzetí služeb při selhání. Ve Správci clusteru s podporou převzetí služeb při selhání vyberte **vytvořit cluster**a přidejte pouze název prvního clusteru, uzel A. Ještě nepřidávejte druhý uzel; přidáte druhý uzel v pozdějším kroku.

   ![Obrázek 18: Přidání názvu serveru nebo virtuálního počítače prvního uzlu clusteru][sap-ha-guide-figure-3007]

   _**Obrázek 18:** Přidání názvu serveru nebo virtuálního počítače prvního uzlu clusteru_

3. Zadejte název sítě (název virtuálního hostitele) clusteru.

   ![Obrázek 19: Zadejte název clusteru][sap-ha-guide-figure-3008]

   _**Obrázek 19:** Zadejte název clusteru._

4. Po vytvoření clusteru spusťte ověřovací test clusteru.

   ![Obrázek 20: Spuštění kontroly ověření clusteru][sap-ha-guide-figure-3009]

   _**Obrázek 20:** Spuštění kontroly ověření clusteru_

   V tomto okamžiku procesu můžete ignorovat všechna upozornění týkající se disků. Později přidáte důkaz čehož připojili jste sdílené složky a sdílené disky SIOS. V této fázi se nemusíte starat o kvorum.

   ![Obrázek 21: Nebyl nalezen disk kvora.][sap-ha-guide-figure-3010]

   _**Obrázek 21:** Nebyl nalezen žádný disk kvora._

   ![Obrázek 22: Základní prostředek clusteru potřebuje novou IP adresu][sap-ha-guide-figure-3011]

   _**Obrázek 22:** Základní prostředek clusteru potřebuje novou ADRESU IP_

5. Změňte adresu IP základní clusterové služby. Cluster nelze spustit, dokud nezměníte IP adresu základní clusterové služby, protože ADRESA IP serveru odkazuje na jeden z uzlů virtuálního počítače. Udělejte to na stránce **Vlastnosti** prostředku IP základní clusterové služby.

   Například musíme přiřadit IP adresu (v našem příkladu **10.0.0.42**) pro název virtuálního hostitele clusteru **pr1-ascs-vir**.

   ![Obrázek 23: V dialogovém okně Vlastnosti změňte adresu IP][sap-ha-guide-figure-3012]

   _**Obrázek 23:** V dialogovém okně **Vlastnosti** změňte adresu IP_

   ![Obrázek 24: Přiřazení adresy IP, která je vyhrazena pro cluster][sap-ha-guide-figure-3013]

   _**Obrázek 24:** Přiřazení adresy IP, která je vyhrazena pro cluster_

6. Přenesete název virtuálního hostitele clusteru do režimu online.

   ![Obrázek 25: Základní služba clusteru je v provozu a se správnou ip adresou][sap-ha-guide-figure-3014]

   _**Obrázek 25:** Základní služba clusteru je v provozu a se správnou adresou IP_

7. Přidejte druhý uzel clusteru.

   Nyní, když je základní clusterová služba v provozu, můžete přidat druhý uzel clusteru.

   ![Obrázek 26: Přidání druhého uzlu clusteru][sap-ha-guide-figure-3015]

   _**Obrázek 26:** Přidání druhého uzlu clusteru_

8. Zadejte název druhého hostitele uzlu clusteru.

   ![Obrázek 27: Zadání názvu hostitele druhého uzlu clusteru][sap-ha-guide-figure-3016]

   _**Obrázek 27:** Zadejte název hostitele druhého uzlu clusteru._

   > [!IMPORTANT]
   > Ujistěte se, že **není** zaškrtnuto políčko **Přidat do clusteru všechny způsobilé úložiště.**  
   >
   >

   ![Obrázek 28: Nezaškrtávat políčko][sap-ha-guide-figure-3017]

   _**Obrázek 28:** Nezaškrtávat políčko **not**_

   Upozornění týkající se kvora a disků můžete ignorovat. Kvorum nastavíte a disk budete sdílet později, jak je popsáno v [části Instalace sios datakeeper clusteru Edition pro disk sdílení clusteru SAP ASCS/SCS][sap-ha-guide-8.12.3].

   ![Obrázek 29: Ignorovat upozornění týkající se kvora disku][sap-ha-guide-figure-3018]

   _**Obrázek 29:** Ignorovat upozornění týkající se kvora disku_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurace důkazu sdílení souborů clusteru

Konfigurace sdílené složky clusteru zahrnuje tyto úkoly:

- Vytvoření sdílené složky
- Nastavení kvora důkazního názoru sdílení souborů ve Správci clusteru s podporou převzetí služeb při selhání

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Vytvoření sdílené složky

1. Vyberte důkaz čehož it share souboru místo disku kvora. SIOS DataKeeper tuto možnost podporuje.

   V příkladech v tomto článku je důkaz čehož it na serveru Active Directory/DNS, který běží v Azure. Důkaz ní se nazývá **domcontr-0**. Vzhledem k tomu, že byste nakonfigurovali připojení VPN k Azure (prostřednictvím sítě SITE-to-Site VPN nebo Azure ExpressRoute), vaše služba Active Directory/DNS je místní a není vhodná ke spuštění důkazu sdílení souborů.

   > [!NOTE]
   > Pokud vaše služba Active Directory/DNS běží pouze místně, nekonfigurujte důkaz sledování sdílené složky v místním operačním systému Active Directory/DNS Windows. Latence sítě mezi uzly clusteru spuštěná v Azure a místní službou Active Directory/DNS může být příliš velká a může způsobit problémy s připojením. Nezapomeňte nakonfigurovat důkaz pro sdílení souborů na virtuálním počítači Azure, který běží v blízkosti uzlu clusteru.  
   >
   >

   Jednotka kvora potřebuje alespoň 1 024 MB volného místa. Pro jednotku kvora doporučujeme 2 048 MB volného místa.

2. Přidejte objekt názvu clusteru.

   ![Obrázek 30: Přiřazení oprávnění ke sdílené složce pro objekt názvu clusteru][sap-ha-guide-figure-3019]

   _**Obrázek 30:** Přiřazení oprávnění ke sdílené složce pro objekt názvu clusteru_

   Ujistěte se, že oprávnění zahrnují oprávnění ke změně dat ve sdílené složce pro objekt názvu clusteru (v našem příkladu **pr1-ascs-vir$**).

3. Chcete-li přidat objekt názvu clusteru do seznamu, vyberte **přidat**. Změňte filtr a zkontrolujte, zda nechcete mít počítačové objekty, kromě objektů uvedených na obrázku 31.

   ![Obrázek 31: Změna typů objektů tak, aby zahrnovaly počítače][sap-ha-guide-figure-3020]

   _**Obrázek 31:** Změna typů objektů tak, aby zahrnovaly počítače_

   ![Obrázek 32: Zaškrtněte políčko Počítače][sap-ha-guide-figure-3021]

   _**Obrázek 32:** Zaškrtněte políčko **Počítače.**_

4. Zadejte objekt názvu clusteru, jak je znázorněno na obrázku 31. Vzhledem k tomu, že záznam již byl vytvořen, můžete změnit oprávnění, jak je znázorněno na obrázku 30.

5. Vyberte kartu **Zabezpečení** sdílené položky a nastavte podrobnější oprávnění pro objekt názvu clusteru.

   ![Obrázek 33: Nastavení atributů zabezpečení objektu názvu clusteru v kvoru sdílené složky][sap-ha-guide-figure-3022]

   _**Obrázek 33:** Nastavení atributů zabezpečení objektu názvu clusteru v kvoru sdílené složky_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Nastavení kvora důkazního názoru sdílené složky ve Správci clusteru s podporou převzetí služeb při selhání

1. Otevřete Průvodce konfigurací nastavení kvora.

   ![Obrázek 34: Spuštění Průvodce konfigurací kvora clusteru][sap-ha-guide-figure-3023]

   _**Obrázek 34:** Spuštění Průvodce konfigurací kvora clusteru_

2. Na stránce **Vybrat konfiguraci kvora** vyberte **vybrat důkaz kvora**.

   ![Obrázek 35: Konfigurace kvora, ze kterých si můžete vybrat][sap-ha-guide-figure-3024]

   _**Obrázek 35:** Konfigurace kvora, ze kterých si můžete vybrat_

3. Na stránce **Vybrat důkaz kvora** vyberte **Konfigurovat důkaz sdílení souborů**.

   ![Obrázek 36: Vyberte důkaz čehož připojili se ke složce sdílení souborů.][sap-ha-guide-figure-3025]

   _**Obrázek 36:** Vyberte důkaz sdílení souborů_

4. Zadejte cestu UNC ke sdílené složce \\(v našem příkladu domcontr-0\FSW). Chcete-li zobrazit seznam změn, které můžete provést, vyberte **možnost Další**.

   ![Obrázek 37: Definování umístění sdílené složky pro sdílenou složku s kopií clusteru][sap-ha-guide-figure-3026]

   _**Obrázek 37:** Definujte umístění sdílené složky pro sdílenou složku s kopií clusteru_

5. Vyberte požadované změny a pak vyberte **Další**. Je třeba úspěšně překonfigurovat konfiguraci clusteru, jak je znázorněno na obrázku 38.  

   ![Obrázek 38: Potvrzení, že jste překonfigurovali cluster][sap-ha-guide-figure-3027]

   _**Obrázek 38:** Potvrzení, že jste překonfigurovali cluster_

Po úspěšné instalaci clusteru windows převzetí služeb při selhání je třeba provést změny některých prahových hodnot, aby bylo možné přizpůsobit zjišťování služeb při selhání podmínkám v Azure. Parametry, které mají být změněny, [https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)jsou popsány v tomto blogu: . Za předpokladu, že vaše dva virtuální počítače, které vytvářejí konfiguraci clusteru Windows pro ASCS/SCS, jsou ve stejné podsíti, je třeba změnit následující parametry na tyto hodnoty:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- Délka historie směrování = 30  

Tato nastavení byla testována se zákazníky a poskytla dobrý kompromis, aby byla dostatečně odolná na jedné straně. Na druhou stranu tato nastavení poskytovala dostatečně rychlé převzetí služeb při selhání v reálných chybových podmínkách v softwaru SAP nebo selhání uzlu a virtuálního počítače. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Instalace aplikace SIOS DataKeeper Cluster Edition pro disk sdílení clusteru SAP ASCS/SCS

Nyní máte funkční konfiguraci clusteringu Windows Server s podporou převzetí služeb při selhání v Azure. Ale k instalaci instance SAP ASCS/SCS potřebujete prostředek sdíleného disku. V Azure nemůžete vytvořit prostředky sdíleného disku, které potřebujete. SIOS DataKeeper Cluster Edition je řešení jiného výrobce, které můžete použít k vytvoření sdílených diskových prostředků.

Instalace aplikace SIOS DataKeeper Cluster Edition pro disk sdílení clusteru SAP ASCS/SCS zahrnuje tyto úkoly:

- Přidání rozhraní .NET Framework 3.5
- Instalace datového serveru SIOS
- Nastavení datového keeperu SIOS

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Přidání rozhraní .NET Framework 3.5
Rozhraní Microsoft .NET Framework 3.5 není automaticky aktivováno nebo nainstalováno v systému Windows Server 2012 R2. Vzhledem k tomu, že sios datakeeper vyžaduje rozhraní .NET Framework být na všech uzlech, které nainstalujete DataKeeper na, je nutné nainstalovat rozhraní .NET Framework 3.5 na hostovaném operačním systému všech virtuálních počítačů v clusteru.

Rozhraní .NET Framework 3.5 lze přidat dvěma způsoby:

- Použijte Průvodce přidáním rolí a funkcí v systému Windows, jak je znázorněno na obrázku 39.

  ![Obrázek 39: Instalace rozhraní .NET Framework 3.5 pomocí Průvodce přidáním rolí a funkcí][sap-ha-guide-figure-3028]

  _**Obrázek 39:** Instalace rozhraní .NET Framework 3.5 pomocí Průvodce přidáním rolí a funkcí_

  ![Obrázek 40: Indikátor průběhu instalace při instalaci rozhraní .NET Framework 3.5 pomocí Průvodce přidáním rolí a funkcí][sap-ha-guide-figure-3029]

  _**Obrázek 40:** Indikátor průběhu instalace při instalaci rozhraní .NET Framework 3.5 pomocí Průvodce přidáním rolí a funkcí_

- Použijte nástroj příkazového řádku dism.exe. Pro tento typ instalace je třeba získat přístup k adresáři SxS na instalačním médiu systému Windows. Na příkazovém řádku se zvýšenými oprávněními zadejte:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>Instalace datového měřiče SIOS

Nainstalujte sios DataKeeper Cluster Edition na každém uzlu v clusteru. Chcete-li vytvořit virtuální sdílené úložiště pomocí SIOS DataKeeper, vytvořte synchronizované zrcadlo a pak simulujte sdílené úložiště clusteru.

Před instalací softwaru SIOS vytvořte uživatele domény **DataKeeperSvc**.

> [!NOTE]
> Přidejte uživatele **DataKeeperSvc** do skupiny **Místní správce** v obou uzlech clusteru.
>
>

Instalace SIOS DataKeeper:

1. Nainstalujte software SIOS do obou uzlů clusteru.

   ![Instalační program SIOS][sap-ha-guide-figure-3030]

   ![Obrázek 41: První stránka instalace SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Obrázek 41:** První stránka instalace SIOS DataKeeper_

2. V dialogovém okně zobrazeném na obrázku 42 vyberte **Ano**.

   ![Obrázek 42: DataKeeper vás informuje, že služba bude zakázána][sap-ha-guide-figure-3032]

   _**Obrázek 42:** DataKeeper vás informuje, že služba bude zakázána_

3. V dialogovém okně znázorněném na obrázku 43 doporučujeme vybrat **účet domény nebo serveru**.

   ![Obrázek 43: Výběr uživatele pro SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Obrázek 43:** Výběr uživatele pro SIOS DataKeeper_

4. Zadejte uživatelské jméno a hesla účtu domény, které jste vytvořili pro SIOS DataKeeper.

   ![Obrázek 44: Zadejte uživatelské jméno domény a heslo pro instalaci SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Obrázek 44:** Zadejte uživatelské jméno domény a heslo pro instalaci SIOS DataKeeper_

5. Nainstalujte licenční klíč pro instanci SIOS DataKeeper, jak je znázorněno na obrázku 45.

   ![Obrázek 45: Zadejte licenční klíč SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Obrázek 45:** Zadejte licenční klíč SIOS DataKeeper_

6. Po zobrazení výzvy restartujte virtuální počítač.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Nastavit sios datakeeper

Po instalaci SIOS DataKeeper na obou uzlech, je třeba spustit konfiguraci. Cílem konfigurace je mít synchronní replikaci dat mezi dalšími virtuálními počítači připojenými ke každému virtuálnímu počítači.

1. Spusťte nástroj Správa a konfigurace datakeeperu a vyberte **příkaz Připojit server**. (Na obrázku 46 je tato možnost zakroužkována červeně.)

   ![Obrázek 46: Nástroj pro správu a konfiguraci datových brankářů SIOS][sap-ha-guide-figure-3036]

   _**Obrázek 46:** Nástroj pro správu a konfiguraci datových brankářů SIOS_

2. Zadejte název nebo adresu TCP/IP prvního uzlu, ke kterému by se měl nástroj Pro správu a konfiguraci připojit, a v druhém kroku druhý uzel.

   ![Obrázek 47: Vložte název nebo adresu TCP/IP prvního uzlu, ke kterému by se měl nástroj Pro správu a konfiguraci připojit, a v druhém kroku druhý uzel][sap-ha-guide-figure-3037]

   _**Obrázek 47:** Vložte název nebo adresu TCP/IP prvního uzlu, ke kterému by se měl nástroj Pro správu a konfiguraci připojit, a ve druhém kroku druhý uzel_

3. Vytvořte úlohu replikace mezi dvěma uzly.

   ![Obrázek 48: Vytvoření úlohy replikace][sap-ha-guide-figure-3038]

   _**Obrázek 48:** Vytvoření úlohy replikace_

   Průvodce vás provede procesem vytváření úlohy replikace.
4. Definujte název, adresu TCP/IP a svazek disku zdrojového uzlu.

   ![Obrázek 49: Definování názvu úlohy replikace][sap-ha-guide-figure-3039]

   _**Obrázek 49:** Definovat název úlohy replikace_

   ![Obrázek 50: Definování základních dat pro uzel, který by měl být aktuální zdrojový uzel][sap-ha-guide-figure-3040]

   _**Obrázek 50:** Definujte základní data pro uzel, který by měl být aktuální zdrojový uzel_

5. Definujte název, adresu TCP/IP a svazek disku cílového uzlu.

   ![Obrázek 51: Definování základních dat pro uzel, který by měl být aktuální cílový uzel][sap-ha-guide-figure-3041]

   _**Obrázek 51:** Definujte základní data pro uzel, který by měl být aktuálnícílový uzel_

6. Definujte kompresní algoritmy. V našem příkladu doporučujeme komprimovat datový proud replikace. Zejména v situacích resynchronizace komprese replikačního proudu výrazně snižuje dobu synchronizace. Všimněte si, že komprese používá prostředky procesoru a paměti RAM virtuálního počítače. S tím, jak se zvyšuje rychlost komprese, se zvyšuje i objem použitých prostředků procesoru. Toto nastavení můžete také upravit později.

7. Další nastavení, které je třeba zkontrolovat, je, zda replikace probíhá asynchronně nebo synchronně. *Při ochraně konfigurací SAP ASCS/SCS je nutné použít synchronní replikaci*.  

   ![Obrázek 52: Definování podrobností replikace][sap-ha-guide-figure-3042]

   _**Obrázek 52:** Definování podrobností replikace_

8. Definujte, zda má být svazek replikovaný úlohou replikace reprezentován ke konfiguraci clusteru clusteru s podporou převzetí služeb při selhání systému Windows Server jako sdílený disk. Pro konfiguraci SAP ASCS/SCS vyberte **Ano,** aby cluster Windows viděl replikovaný svazek jako sdílený disk, který může použít jako svazek clusteru.

   ![Obrázek 53: Výběrem možnosti Ano nastavte replikovaný svazek jako svazek clusteru.][sap-ha-guide-figure-3043]

   _**Obrázek 53:** Výběrem **možnosti Ano** nastavte replikovaný svazek jako svazek clusteru._

   Po vytvoření svazku nástroj Správa a konfigurace datakeeperu zobrazí, že úloha replikace je aktivní.

   ![Obrázek 54: Synchronní zrcadlení DataKeeper pro disk sdílené složky SAP ASCS/SCS je aktivní][sap-ha-guide-figure-3044]

   _**Obrázek 54:** Synchronní zrcadlení DataKeeper pro disk sdílení SAP ASCS/SCS je aktivní_

   Správce clusteru s podporou převzetí služeb při selhání nyní zobrazuje disk jako disk DataKeeper, jak je znázorněno na obrázku 55.

   ![Obrázek 55: Správce clusteru s podporou převzetí služeb při selhání zobrazuje disk, který datakeeper replikoval][sap-ha-guide-figure-3045]

   _**Obrázek 55:** Správce clusteru s podporou převzetí služeb při selhání zobrazuje disk, který datový modul replikoval._

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>Instalace systému SAP NetWeaver

Nebudeme popisovat nastavení DBMS, protože nastavení se liší v závislosti na systému DBMS, který používáte. Předpokládáme však, že obavy o vysokou dostupnost s DBMS jsou řešeny s funkcemi, které podporují různí dodavatelé DBMS pro Azure. Například Always On nebo zrcadlení databáze pro SQL Server a Oracle Data Guard pro databáze Oracle. Ve scénáři, který používáme v tomto článku, jsme nepřidali další ochranu dbms.

Neexistují žádné zvláštní aspekty při různých službách DBMS interakci s tímto druhem clusterované konfigurace SAP ASCS/SCS v Azure.

> [!NOTE]
> Instalační postupy systémů SAP NetWeaver ABAP, Java systémů a ABAP+Java jsou téměř identické. Nejvýznamnější rozdíl je, že systém SAP ABAP má jednu instanci ASCS. Systém SAP Java má jednu instanci SCS. Systém SAP ABAP+Java má jednu instanci ASCS a jednu instanci SCS spuštěnou ve stejné skupině clusterů microsoft u převzetí služeb při selhání. Všechny rozdíly v instalaci pro každý instalační balíček SAP NetWeaver jsou výslovně uvedeny. Můžete předpokládat, že všechny ostatní části jsou stejné.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalace SAP s vysokou dostupností instance ASCS/SCS

> [!IMPORTANT]
> Nezapomeňte umístit stránkovací soubor na zrcadlené svazky DataKeeper. DataKeeper nepodporuje zrcadlené svazky. Stránkovací soubor můžete ponechat na dočasné jednotce D virtuálního počítače Azure, což je výchozí. Pokud ještě není k dispozici, přesuňte stránkovací soubor Windows tak, aby řídil D vašeho virtuálního počítače Azure.
>
>

Instalace SAP s vysokou dostupností instance ASCS/SCS zahrnuje tyto úkoly:

- Vytvoření názvu virtuálního hostitele pro clusterovnou instanci SAP ASCS/SCS
- Instalace prvního uzlu clusteru SAP
- Změna profilu SAP instance ASCS/SCS
- Přidání portu sondy
- Otevření portu sondy brány firewall systému Windows

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Vytvoření názvu virtuálního hostitele pro clusterovnou instanci SAP ASCS/SCS

1. Ve Správci SLUŽBY DNS systému Windows vytvořte položku DNS pro název virtuálního hostitele instance ASCS/SCS.

   > [!IMPORTANT]
   > IP adresa, kterou přiřadíte k názvu virtuálního hostitele instance ASCS/SCS, musí být stejná jako IP adresa, kterou jste přiřadili k nástroji pro vyrovnávání zatížení Azure**<*(SID*>-lb-ascs).**  
   >
   >

   IP adresa virtuálního názvu hostitele SAP ASCS/SCS **(pr1-ascs-sap)** je stejná jako IP adresa nástroje Azure Load Balancer (**pr1-lb-ascs**).

   ![Obrázek 56: Definování položky DNS pro virtuální název clusteru SAP ASCS/SCS a adresu TCP/IP][sap-ha-guide-figure-3046]

   _**Obrázek 56:** Definování položky DNS pro virtuální název clusteru SAP/SCS a adresu TCP/IP_

2. Chcete-li definovat adresu IP přiřazenou názvu virtuálního hostitele, vyberte**možnost Doména** **správce** > DNS .

   ![Obrázek 57: Nový virtuální název a adresa TCP/IP pro konfiguraci clusteru SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Obrázek 57:** Nový virtuální název a adresa TCP/IP pro konfiguraci clusteru SAP ASCS/SCS_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Instalace prvního uzlu clusteru SAP

1. Spusťte první možnost uzlu clusteru v uzlu Clusteru A. Například na **pr1-ascs-0** hostitele.
2. Pokud chcete zachovat výchozí porty pro interní systém vyrovnávání zatížení Azure, vyberte:

   * **Systém ABAP**: **Instance ASCS** číslo **00**
   * **Java systém**: **SCS** instance číslo **01**
   * **Systém ABAP+Java**: **Instance ASCS** číslo **00** a Číslo instance **SCS** **01**

   Chcete-li použít čísla instancí než 00 pro instanci Ascs ABAP a 01 pro instanci Java SCS, musíte nejprve změnit výchozí pravidla vyrovnávání zatížení Azure internal load balancer, popsaná v [části Změna výchozích pravidel vyrovnávání zatížení ASCS/SCS pro interní vyrovnávání zatížení Azure][sap-ha-guide-8.9].

Několik dalších úkolů není popsáno ve standardní dokumentaci k instalaci SAP.

> [!NOTE]
> Instalační dokumentace SAP popisuje, jak nainstalovat první uzel clusteru ASCS/SCS.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Změna profilu SAP instance ASCS/SCS

Je třeba přidat nový parametr profilu. Parametr profilu zabraňuje připojení mezi pracovní procesy SAP a enqueue server uzavření, když jsou nečinné příliš dlouho. Problémový scénář jsme zmínili v [části Přidat položky registru na obou uzlech clusteru instance SAP ASCS/SCS][sap-ha-guide-8.11]. V této části jsme také zavedli dvě změny některých základních parametrů připojení TCP/IP. Ve druhém kroku je třeba nastavit server fronty `keep_alive` pro odeslání signálu tak, aby připojení nedosáhne prahové hodnoty nečinnosti interního vyvažovače zatížení Azure.

Chcete-li upravit profil SAP instance ASCS/SCS:

1. Přidejte tento parametr profilu do profilu instance SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   V našem příkladu je cesta:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Například do profilu instance SAP SCS a odpovídající cesty:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Chcete-li změny použít, restartujte instanci SAP ASCS /SCS.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Přidání portu sondy

Pomocí funkce sondy nástroje pro vyrovnávání zatížení můžete celou konfiguraci clusteru zpracovat pomocí nástroje Azure Load Balancer. Nástroj pro interní vyrovnávání zatížení Azure obvykle distribuuje příchozí úlohy rovnoměrně mezi zúčastněné virtuální počítače. V některých konfiguracích clusteru to však nebude fungovat, protože je aktivní pouze jedna instance. Druhá instance je pasivní a nemůže přijmout žádné úlohy. Funkce sondy pomáhá, když interní vyrovnávání zatížení Azure přiřadí práci pouze aktivní instanci. Pomocí funkce sondy může interní vyvažovač zatížení zjistit, které instance jsou aktivní, a pak cílit pouze na instanci s úlohou.

Přidání portu sondy:

1. Zkontrolujte aktuální nastavení **ProbePort** spuštěním následujícího příkazu PowerShell. Spusťte jej z jednoho z virtuálních počítačů v konfiguraci clusteru.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Definujte port sondy. Výchozí číslo portu sondy je **0**. V našem příkladu používáme port sondy **62000**.

   ![Obrázek 58: Port konfigurační sondy clusteru je ve výchozím nastavení 0][sap-ha-guide-figure-3048]

   _**Obrázek 58:** Výchozí port konfigurační sondy clusteru je 0_

   Číslo portu je definováno v šablonách SAP Azure Resource Manager. Číslo portu můžete přiřadit v prostředí PowerShell.

   Chcete-li nastavit novou hodnotu ProbePort pro prostředek clusteru **SAP <*SID*> IP,** spusťte následující skript prostředí PowerShell. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí. Po spuštění skriptu budete vyzváni k restartování skupiny clusterů SAP k aktivaci změn.

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

   Po přepnete roli clusteru **SAP <*SID* > ** do režimu online, ověřte, zda je **probeport** nastaven na novou hodnotu.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Obrázek 59: Sonda portu clusteru po nastavení nové hodnoty][sap-ha-guide-figure-3049]

   _**Obrázek 59:** Sonda portu clusteru po nastavení nové hodnoty_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Otevření portu sondy brány firewall systému Windows

Je třeba otevřít port sondy brány firewall systému Windows v obou uzlech clusteru. Pomocí následujícího skriptu otevřete port sondy brány firewall systému Windows. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** je nastaven na **62000**. Nyní můžete získat přístup ke sdílené složce ** \\\ascsha-clsap\sapmnt** z jiných hostitelů, například z **ascsha-dbas**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Instalace instance databáze

Chcete-li nainstalovat instanci databáze, postupujte podle postupu popsaného v dokumentaci k instalaci SAP.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalace druhého uzlu clusteru

Chcete-li nainstalovat druhý cluster, postupujte podle pokynů v instalační příručce SAP.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Změna typu spuštění instance služby SAP ERS windows

Změňte typ spuštění služby SAP ERS Windows na **automatické (zpožděné spuštění)** v obou uzlech clusteru.

![Obrázek 60: Změna typu služby pro instanci SAP ERS na zpožděnou automatickou][sap-ha-guide-figure-3050]

_**Obrázek 60:** Změna typu služby pro instanci SAP ERS na zpožděnou automatickou_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Instalace primárního aplikačního serveru SAP

Nainstalujte instanci primárního aplikačního serveru (PAS) <*SID*>-di-0 na virtuální mši, kterou jste určili k hostování pas. Neexistují žádné závislosti na azure nebo datakeeper specifické nastavení.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Instalace dalšího aplikačního serveru SAP

Nainstalujte sap další aplikační server (AAS) na všechny virtuální počítače, které jste určili pro hostování instance aplikačního serveru SAP. Například na <*SID*>-di-1 na <*SID*>-di-&lt;n&gt;.

> [!NOTE]
> Tím je dokončena instalace vysoce dostupnosti systému SAP NetWeaver. Dále pokračujte v testování převzetí služeb při selhání.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Testování převzetí služeb při selhání instance SAP ASCS/SCS a replikace SIOS
Je snadné testovat a monitorovat převzetí služeb při selhání instance SAP ASCS/SCS a replikaci disku SIOS pomocí Správce clusteru s podporou převzetí služeb při selhání a nástroje pro správu a konfiguraci datového keeperu SIOS.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>Instance SAP ASCS/SCS je spuštěna na uzlu clusteru A

Skupina clusteru **SAP PR1** je spuštěna v uzlu Clusteru A. Například na **pr1-ascs-0**. Přiřaďte uzel A sdílené diskové jednotky S, která je součástí skupiny clusterů **SAP PR1** a kterou instance ASCS/SCS používá.

![Obrázek 61: Správce clusteru s podporou převzetí služeb při selhání: Skupina> clusteru SAP <SID je spuštěna v uzlu Clusteru A][sap-ha-guide-figure-5000]

_**Obrázek 61:** Správce clusteru s podporou převzetí služeb při selhání: Skupina> clusteru SAP <*SID* je spuštěna v uzlu Clusteru A_

V nástroji pro správu a konfiguraci datového keeperu SIOS uvidíte, že data sdíleného disku jsou synchronně replikována ze zdrojové jednotky svazku S v uzlu Clusteru A na cílovou jednotku svazku S v uzlu clusteru B. Například je replikován z **pr1-ascs-0 [10.0.0.40]** na **pr1-ascs-1 [10.0.0.41]**.

![Obrázek 62: V souboru SIOS DataKeeper replikujte místní svazek z uzlu clusteru A do uzlu Cluster B][sap-ha-guide-figure-5001]

_**Obrázek 62:** V souboru SIOS DataKeeper replikujte místní svazek z uzlu clusteru A do uzlu Clusterb_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Převzetí služeb při selhání z uzlu A do uzlu B

1. Vyberte jednu z těchto možností, chcete-li zahájit převzetí služeb při selhání skupiny SAP <*SID*> clusteru z uzlu Clusteru A do uzlu Clusteru B:
   - Použít Správce clusteru s podporou převzetí služeb při selhání  
   - Použití prostředí PowerShell clusteru s podporou převzetí služeb při selhání

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Restartujte uzel clusteru A v rámci hostovaného operačního systému Windows (tím se iniciuje automatické převzetí služeb při selhání sap <*SID*> skupiny clusterů z uzlu A do uzlu B).  
3. Restartujte uzel clusteru A z portálu Azure (to iniciuje automatické převzetí služeb při selhání SAP <*SID*> skupiny clusterů z uzlu A do uzlu B).  
4. Restartujte uzel clusteru A pomocí Azure PowerShellu (to iniciuje automatické převzetí služeb při selhání SAP <*SID*> skupiny clusterů z uzlu A do uzlu B).

   Po převzetí služeb při selhání je skupina SAP <*SID*> clusteru spuštěna v uzlu clusteru B. Například běží na **pr1-ascs-1**.

   ![Obrázek 63: Ve Správci clusterů s podporou převzetí služeb při selhání je v uzlu Clusteru B spuštěna skupina SAP <SID> clusteru][sap-ha-guide-figure-5002]

   _**Obrázek 63**: Ve Správci clusterů s podporou převzetí služeb při selhání je v uzlu Clusteru B spuštěna skupina SAP <*SID*> clusteru_

   Sdílený disk je nyní připojen k uzlu clusteru B. SIOS DataKeeper replikuje data ze zdrojové jednotky svazku S v uzlu clusteru B na cílovou jednotku svazku S v uzlu clusteru A. Například je replikace z **pr1-ascs-1 [10.0.0.41]** na **pr1-ascs-0 [10.0.0.40]**.

   ![Obrázek 64: Správce dat SIOS replikuje místní svazek z uzlu clusteru B do uzlu Clusteru A.][sap-ha-guide-figure-5003]

   _**Obrázek 64:** Sios DataKeeper replikuje místní svazek z uzlu clusteru B do uzlu Clusteru A_
