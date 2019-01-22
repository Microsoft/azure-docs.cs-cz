---
title: Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver | Dokumentace Microsoftu
description: Příručka pro vysokou dostupnost pro SAP NetWeaver na virtuálních počítačích Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: goraco
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 691bb0c5ea6d84bd67b8b1b1fd5a05c25f75ba40
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437024"
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
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

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

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (Konfigurace vysoké dostupnosti SAP s několika SID)


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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (Přehled dostupnosti SAP produktu)
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
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/resource-group-overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager
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


Azure Virtual Machines je řešení pro organizace, které potřebují výpočetní prostředky, úložiště a síťové prostředky, minimální včas a bez zdlouhavé zajišťování cykly. Azure Virtual Machines můžete nasadit klasické aplikace, jako je založené na systému SAP NetWeaver ABAP, Java nebo balíčku ABAP + Java. Rozšiřte spolehlivosti a dostupnosti bez další místní prostředky. Azure Virtual Machines podporuje připojení mezi místními sítěmi, Azure Virtual Machines vám umožní integraci do místní domény vaší organizace, privátních cloudů a prostředí systému SAP.

V tomto článku se budeme zabývat kroky, které můžete provést při nasazování systémů SAP s vysokou dostupností v Azure s použitím modelu nasazení Azure Resource Manageru. Provedeme vás tyto hlavní úlohy:

* Najít správné poznámky SAP a průvodců instalací, uvedené v [prostředky] [ sap-ha-guide-2] oddílu. Tento článek doplňuje dokumentaci k instalaci SAP a SAP poznámky, které jsou primární zdroje, které vám umožňují instalaci a nasazení softwaru SAP na konkrétní platformy.
* Poznejte rozdíly mezi modelem nasazení Azure Resource Manageru a model nasazení Azure classic.
* Další informace o systému Windows Server Failover Clustering režimů kvora, takže můžete vybrat model, který je nejvhodnější pro nasazení vašeho řešení Azure.
* Další informace o systému Windows Server Failover Clustering sdílené úložiště ve službách Azure.
* Zjistěte, jak můžete ochránit jednoho bodu o selhání součásti jako Advanced Business Application programování (ABAP) SAP Central Services (ASCS) / SAP Central Services (SCS) a systémy pro správu databáze (DBMS) a redundantní komponenty, jako jsou aplikace SAP Server v Azure.
* Postupujte podle podrobného příklad o instalaci a konfiguraci systému SAP vysoké dostupnosti v clusteru Windows Server Failover Clustering v Azure pomocí Azure Resource Manageru.
* Další informace o dalších krocích muset použít Windows Server Failover Clustering v Azure, ale které nejsou potřebné v místním nasazení.

Pro zjednodušení nasazení a konfiguraci, v tomto článku používáme šablony Resource Manageru SAP třívrstvé vysokou dostupnost. Šablony můžete automatizovat nasazení celé infrastruktury, které potřebujete pro vysokou dostupnost systému SAP. Infrastruktura podporuje také SAP aplikace výkonu Standard (protokoly SAP) k určení velikosti systému SAP.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Požadované součásti
Než začnete, ujistěte se, že splňujete požadavky, které jsou popsány v následujících částech. Kromě toho je potřeba zkontrolovat všechny prostředky uvedené v [prostředky] [ sap-ha-guide-2] oddílu.

V tomto článku jsme pomocí šablony Azure Resource Manageru pro [třívrstvé SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Užitečný přehled šablon naleznete v tématu [SAP Azure Resource Manageru šablony](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Prostředky
Tyto články zahrnovat nasazení SAP v Azure:

* [Azure Virtual Machines, plánování a implementace SAP NetWeaver][planning-guide]
* [Nasazení virtuálních počítačů pro SAP NetWeaver v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP NetWeaver][dbms-guide]
* [Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver (Tato příručka)][sap-ha-guide]

> [!NOTE]
> Kdykoli je to možné, jsme vám odkaz k odkazující Průvodce instalací SAP (viz [průvodců instalací SAPU][sap-installation-guides]). Požadované součásti a informace o procesu instalace je vhodné si pozorně přečtěte průvodců instalací SAP Netweaveru. Tento článek se týká pouze konkrétní úlohy pro počítače se systémem SAP NetWeaver, které můžete použít s Azure Virtual Machines.
>
>

Tyto poznámky SAP se vztahují k tématu SAP v Azure:

| Poznámka: číslo | Titul |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: Podporované produkty a změna velikosti |
| [2015553] |SAP v Microsoft Azure: Požadavky pro podporu |
| [1999351] |Rozšířené monitorování Azure pro SAP |
| [2178632] |Klíč monitorování metrik pro SAP v Microsoft Azure |
| [1999351] |Virtualizace ve Windows: Rozšířené monitorování |
| [2243692] |Použití Azure Premium Storage SSD pro instanci SAP DBMS |

Další informace o [omezení předplatných Azure,][azure-subscription-service-limits-subscription], včetně obecných výchozí omezení a omezení maximální.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Vysoká dostupnost SAP pomocí Azure Resource Manageru a modelu nasazení Azure classic
Azure Resource Manageru a modely nasazení Azure classic se liší v následujících oblastech:

- Skupiny prostředků
- Azure interního nástroje pro vyrovnávání závislost na skupinu prostředků Azure
- Podpora scénářů řešení SAP s několika SID

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Skupiny prostředků
V Azure Resource Manageru pro správu všech prostředků aplikace ve vašem předplatném Azure můžete použít skupiny prostředků. Integrovaný přístup ve skupině prostředků, mají všechny prostředky mají stejný životní cyklus. Například všechny prostředky vytvořené v době, a odstraní se ve stejnou dobu. Další informace o [skupinách prostředků](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure interního nástroje pro vyrovnávání závislost na skupinu prostředků Azure

V modelu nasazení Azure classic neexistuje závislost mezi interní Azure load balancer (služba Azure Load Balancer) a skupinou služeb cloudu. Každý interního nástroje load balancer vyžaduje jednu skupinu cloudové služby.

V Azure Resource Manageru, není nutné použít nástroj pro vyrovnávání zatížení Azure skupiny prostředků Azure. Prostředí je jednodušší a flexibilnější.

### <a name="support-for-sap-multi-sid-scenarios"></a>Podpora scénářů řešení SAP s několika SID

V Azure Resource Manageru můžete nainstalovat více SAP systému identifikátor (SID) ASCS/SCS instancí v jednom clusteru. Instance s několika SID je možné z důvodu podpory pro několik IP adres pro každý nástroj pro vyrovnávání zatížení Azure interní.

Chcete-li použít model nasazení Azure classic, postupujte podle postupů popsaných v [SAP NetWeaver v Azure: Clusteringu SAP ASCS/SCS instance s použitím systému Windows Server Failover Clustering v Azure se SIOS Datakeeperem](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Důrazně doporučujeme použít model nasazení Azure Resource Manageru pro vaše instalace SAP. Nabízí řadu výhod, které nejsou k dispozici v modelu nasazení classic. Další informace o Azure [modely nasazení][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server Failover Clustering s
Windows Server Failover Clustering je základem instalace SAP ASCS/SCS vysokou dostupnost a DBMS ve Windows.

Cluster převzetí služeb při selhání je skupina 1 + n nezávislých serverů (uzly), které vzájemně spolupracují a zvyšují tak dostupnost aplikací a služeb. Pokud dojde k selhání uzlu, Windows Server Failover Clustering vypočítá počet chyb, které mohou nastat při udržování pořádku cluster, který poskytuje aplikacím a službám. K dosažení clustering převzetí služeb při selhání můžete vybírat kvora různé režimy.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Režimů kvora
Při použití systému Windows Server Failover Clustering, můžete vybrat ze čtyř režimů kvora:

* **Většina uzlů**. Každý uzel clusteru můžete hlasovat. Cluster funguje pouze s většinou hlasů, tedy s víc než poloviny hlasy. Doporučujeme tuto možnost pro clustery, které mají lichý počet uzlů. Například tři uzly v clusteru sedm uzlů může selhat a nepřesahuje clusteru dosahuje většinou a nadále běží.  
* **Majorita uzlů a disků**. Každý uzel a určený disk (disk s kopií clusteru) v úložišti clusteru můžete hlasovat, kdy jsou k dispozici a komunikace. Cluster funguje pouze s většinou hlasů, tedy s víc než poloviny hlasy. Tento režim dává smysl v prostředí clusteru s sudé číslo uzlů. Pokud se polovina uzlů a disk jsou online, cluster zůstane v dobrém stavu.
* **Uzel a sdílených složek většinou**. Každý uzel plus určené sdílené složky (určující sdílená složka), který vytvoří správce můžete hlasovat, bez ohledu na to, zda jsou k dispozici uzlů a sdílení souborů a v komunikaci. Cluster funguje pouze s většinou hlasů, tedy s víc než poloviny hlasy. Tento režim dává smysl v prostředí clusteru s sudé číslo uzlů. Podobá se Většina uzlů a disků režimu, ale používá sdílenou složku s kopií clusteru místo disku s kopií clusteru. Tento režim se snadno implementují, ale pokud sdílená složka samotný není vysoce dostupný, to může být kritickým prvkem způsobujícím selhání.
* **Bez většiny: Na disku pouze**. Cluster má kvorum, pokud je k dispozici a komunikace s konkrétním diskem v úložišti clusteru jeden uzel. Pouze uzly, které jsou také v komunikaci se tento disk můžete připojit ke clusteru. Doporučujeme vám, že je velmi riskantní používat tento režim.

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server převzetí služeb při selhání Clustering on-premises
Obrázek 1 ukazuje dva uzly clusteru. Pokud síťové připojení mezi uzly se nezdaří a jak pobytu uzlů nahoru a spuštěný, disk kvora nebo soubor sdílet určí, který uzel i nadále bude poskytovat aplikace a služby clusteru. Uzel, který má přístup k disku nebo sdílené složky kvora je uzel, který zajistí, že služby dál.

Vzhledem k tomu tento příklad používá dva uzly clusteru, můžeme použít režim kvora Majority sdílené složky souborů a uzel. Disk Majorita uzlů a také je platnou možností. V produkčním prostředí doporučujeme použít disk kvora. Technologie systému sítě a úložiště můžete použít k zajištění vysoké dostupnosti.

![Obrázek 1: Příklad konfigurace systému Windows Server Failover Clustering pro SAP ASCS/SCS v Azure][sap-ha-guide-figure-1000]

_**Obrázek 1:** Příklad konfigurace systému Windows Server Failover Clustering pro SAP ASCS/SCS v Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Sdílené úložiště
Obrázek 1 ukazuje také sdílené úložiště dva uzly clusteru. V místní sdílené úložiště clusteru zjistit všechny uzly v clusteru sdíleného úložiště. Mechanismus zamykání chrání data před poškozením. Všechny uzly, můžete zjistit, pokud jiný uzel selže. V případě selhání jednoho uzlu zbývající uzel převezme vlastnictví prostředků služby storage a zajišťuje dostupnost služeb.

> [!NOTE]
> Nepotřebujete sdílené disky pro zajištění vysoké dostupnosti s některými aplikacemi DBMS, stejně jako s SQL serverem. SQL serveru Always On replikuje DBMS dat a souborů protokolu z místního disku jednom uzlu clusteru na místní disk jiného uzlu clusteru. V takovém případě konfigurace clusteru Windows nemusí sdíleného disku.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Sítě a překladu
Klientské počítače oslovit clusteru přes virtuální IP adresu a název virtuálního hostitele, který poskytuje DNS server. Uzly s místními a DNS server může zpracovávat více IP adres.

V typické nastavení použijte dvě nebo víc síťových připojení:

* Vyhrazené připojení k úložišti
* Připojení clusteru interní síť pro prezenční signál
* Veřejné sítě, které používají klienti pro připojení ke clusteru

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server převzetí služeb při selhání řízení clusterů v Azure
Srovnání úplné nasazení systému nebo privátní cloud, Azure Virtual Machines vyžaduje další kroky pro konfiguraci systému Windows Server Failover Clustering. Když vytvoříte sdíleném disku clusteru, je nutné nastavit několik IP adresy a názvy virtuálních hostitelů pro instanci SAP ASCS/SCS.

V tomto článku se podíváme na klíčové koncepty a další kroky potřebné k vytvoření clusteru vysokou dostupnost služby centrální služby SAP v Azure. Ukážeme, jak nastavit nástroj třetí strany SIOS DataKeeper a jak nakonfigurovat nástroj pro vyrovnávání zatížení Azure interní. Tyto nástroje můžete použít k vytvoření clusteru převzetí služeb při selhání Windows s určující sdílená složka v Azure.

![Obrázek 2: Konfigurace systému Windows Server Failover Clustering v Azure bez sdíleného disku][sap-ha-guide-figure-1001]

_**Obrázek 2:** Konfigurace systému Windows Server Failover Clustering v Azure bez sdíleného disku_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> SIOS DataKeeper dostaly disk v Azure
Potřebujete cluster sdíleného úložiště pro instanci SAP ASCS/SCS vysokou dostupnost. Od září 2016 nebude Azure nabízí sdílené úložiště, které můžete použít k vytvoření clusteru sdíleného úložiště. Třetí strany software SIOS DataKeeper Cluster Edition slouží k vytvoření zrcadlené úložiště, který simuluje sdíleného úložiště v clusteru. SIOS řešení poskytuje data v reálném čase synchronní replikace. To je, jak můžete vytvořit prostředek sdílený disk pro cluster:

1. Připojte další Azure virtuální pevný disk (VHD) na všech virtuálních počítačích (VM) v konfiguraci clusteru Windows.
2. Spusťte SIOS DataKeeper Cluster Edition na oba uzly virtuálních počítačů.
3. Nakonfigurujte SIOS DataKeeper Cluster Edition, takže zrcadlí obsah další virtuální pevný disk připojený svazek ze zdrojového virtuálního počítače na další svazek připojený virtuální pevný disk cílového virtuálního počítače. SIOS DataKeeper abstrahuje zdroj a cíl místní svazky a prezentuje je na Windows Server Failover Clustering jako jeden sdílený disk.

Získejte další informace o [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![Obrázek 3: Konfigurace systému Windows Server Failover Clustering v Azure se SIOS Datakeeperem][sap-ha-guide-figure-1002]

_**Obrázek 3:** Konfigurace systému Windows Server Failover Clustering v Azure se SIOS Datakeeperem_

> [!NOTE]
> Není nutné sdílené disky pro zajištění vysoké dostupnosti s některými DBMS produkty, jako je SQL Server. SQL serveru Always On replikuje DBMS dat a souborů protokolu z místního disku jednom uzlu clusteru na místní disk jiného uzlu clusteru. V takovém případě konfigurace clusteru Windows nemusí sdíleného disku.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Překlad názvů v Azure
Cloudové platformy Azure nenabízí možnost nakonfigurovat virtuální IP adresy, jako je například plovoucí IP adresy. Je nutné nastavit virtuální IP adresu prostředku clusteru v cloudu dosáhnout alternativní řešení.
Azure má interního nástroje load balancer ve službě Azure Load Balancer. Pomocí interní služby load balancer klienti oslovit clusteru přes virtuální IP adresu clusteru.
Je třeba nasadit interní služby load balancer ve skupině prostředků, který obsahuje uzly clusteru. Nakonfigurujte všechny nezbytné port pravidla předávání s sondy porty interní služby load balancer.
Můžete se klienti připojují prostřednictvím název virtuálního hostitele. DNS server překládá IP adresu clusteru a port popisovače interního nástroje pro vyrovnávání předávání do aktivního uzlu clusteru.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver vysoké dostupnosti v Azure Infrastructure-as--Service (IaaS)
K dosažení vysoké dostupnosti aplikace SAP pro SAP softwarové komponenty, jako je třeba chránit následující komponenty:

* Instance aplikační Server SAP
* Instanci SAP ASCS/SCS
* DBMS server

Další informace o ochraně SAP součástí scénáře vysoké dostupnosti najdete v tématu [Azure Virtual Machines plánování a implementace SAP Netweaveru](planning-guide.md).

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Aplikační Server SAP s vysokou dostupností
Obvykle není nutné konkrétní řešení vysoké dostupnosti pro instance aplikační Server SAP a dialogové okno. Dosáhněte vysoké dostupnosti redundancí a nakonfigurujete více instancí dialogového okna v různých instancích virtuálních počítačů Azure. Měli byste mít aspoň dvě instance SAP aplikace nainstalované v dvě instance virtuálních počítačů Azure.

![Obrázek 4: Aplikační Server SAP s vysokou dostupností][sap-ha-guide-figure-2000]

_**Obrázek 4:** Aplikační Server SAP s vysokou dostupností_

Je nutné umístit všechny virtuální počítače, které hostitele aplikační Server SAP instance ve stejné skupině dostupnosti Azure nastavit. Skupině dostupnosti Azure zajišťuje, že:

* Všechny virtuální počítače jsou součástí stejné domény upgradu. Jednu upgradovací doménu, například zajistí, že virtuální počítače nejsou aktualizovány ve stejnou dobu, během plánované údržby výpadků.
* Všechny virtuální počítače jsou součástí stejné domény selhání. Doména selhání, například zajistí nasazení virtuálních počítačů, aby žádný jediný bod selhání má vliv na dostupnost všech virtuálních počítačů.

Další informace o tom, jak [Správa dostupnosti virtuálních počítačů][virtual-machines-manage-availability].

Účet úložiště Azure je možné jediný bod selhání, a proto je důležité mít aspoň dva účty úložiště Azure, ve kterých se distribuují alespoň dva virtuální počítače. V ideální nastavení discích jednotlivých virtuálních počítačů, na kterém běží instance SAP dialogové okno se nasazuje do jiného účtu úložiště.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Instanci SAP ASCS/SCS vysoké dostupnosti
Obrázek 5 je příkladem instanci SAP ASCS/SCS vysokou dostupnost.

![Obrázek 5: Instanci SAP ASCS/SCS vysoké dostupnosti][sap-ha-guide-figure-2001]

_**Obrázek 5:** Instanci SAP ASCS/SCS vysoké dostupnosti_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> SAP ASCS/SCS instance vysokou dostupnost s možnostmi Windows Server Failover Clustering v Azure
Srovnání úplné nasazení systému nebo privátní cloud, Azure Virtual Machines vyžaduje další kroky pro konfiguraci systému Windows Server Failover Clustering. Pokud chcete vytvořit cluster převzetí služeb při selhání Windows, potřebujete sdíleném disku clusteru, několik IP adres, několik názvy virtuálních hostitelů a Azure interní nástroj pro clusteringu SAP ASCS/SCS instance. Podíváme na to podrobněji dále v tomto článku.

![Obrázek 6: Windows Server Failover Clustering pro konfiguraci služby SAP ASCS/SCS v Azure s použitím SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Obrázek 6:** Windows Server Failover Clustering v Azure se SIOS Datakeeperem konfigurace SAP ASCS/SCS_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Instance databázového systému vysoké dostupnosti
Správce databáze je v systému SAP také jeden kontaktní bod. Potřebujete chránit s použitím řešení vysoké dostupnosti. Obrázek 7 znázorňuje řešení vysoké dostupnosti SQL serveru Always On v Azure, s Windows Server Failover Clustering a Azure interní nástroj pro vyrovnávání zatížení. SQL serveru Always On replikuje pomocí replikace na svůj vlastní systém DBMS DBMS dat a souborů protokolu. V takovém případě můžete není nutné cluster sdílených disků, což zjednodušuje celý instalační program.

![Obrázek 7: Příklad vysoké dostupnosti SAP DBMS, pomocí SQL serveru Always On][sap-ha-guide-figure-2003]

_**Obrázek 7:** Příklad vysoké dostupnosti SAP DBMS, pomocí SQL serveru Always On_

Další informace o clusteringu s SQL serverem v Azure pomocí modelu nasazení Azure Resource Manageru najdete v těchto článcích:

* [Konfigurace skupiny dostupnosti Always On v Azure Virtual Machines ručně pomocí Resource Manageru][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurace Azure interního nástroje pro skupiny dostupnosti Always On v Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Scénáře nasazení vysoké dostupnosti začátku do konce

### <a name="deployment-scenario-using-architectural-template-1"></a>Scénář nasazení s využitím architektury šablony 1

Obrázek 8 ukazuje příklad architektury vysoké dostupnosti SAP NetWeaver v Azure pro **jeden** systému SAP. Tento scénář je nastavte následujícím způsobem:

- Jeden vyhrazený cluster se používá pro instanci SAP ASCS/SCS.
- Jeden vyhrazený cluster se používá pro instanci DBMS.
- Instance aplikačního serveru SAP se nasazují do jejich vlastních vyhrazených virtuálních počítačích.

![Obrázek 8: Vysoká dostupnost architektury šablona 1, s vyhrazeném clusteru ASC/SCS a databázového systému SAP][sap-ha-guide-figure-2004]

_**Obrázek 8:** Šablona 1 architektury vysoké dostupnosti SAP vyhrazené clustery pro ASCS/SCS a DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scénář nasazení s využitím architektury šablony 2

Obrázek 9 ukazuje příklad architektury vysoké dostupnosti SAP NetWeaver v Azure pro **jeden** systému SAP. Tento scénář je nastavte následujícím způsobem:

- Jeden vyhrazený cluster se používá pro **obě** instanci SAP ASCS/SCS a systém DBMS.
- Instance aplikačního serveru SAP se nasazují do vlastních vyhrazených virtuálních počítačích.

![Obrázek 9: SAP vysoké dostupnosti architektury šablony 2, přičemž vyhrazeném clusteru pro ASCS/SCS a vyhrazeném clusteru pro systém DBMS][sap-ha-guide-figure-2005]

_**Obrázek 9:** SAP vysoké dostupnosti architektury šablony 2, přičemž vyhrazeném clusteru pro ASCS/SCS a vyhrazeném clusteru pro systém DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scénář nasazení s využitím architektury šablony 3

Obrázek 10 ukazuje příklad architektury vysoké dostupnosti SAP NetWeaver v Azure pro **dvě** systémů SAP s &lt;SID1&gt; a &lt;SID2&gt;. Tento scénář je nastavte následujícím způsobem:

- Jeden vyhrazený cluster se používá pro **obě** instance SAP ASCS/SCS SID1 *a* instance SAP ASCS/SCS SID2 (jeden cluster).
- Jeden vyhrazený cluster se používá pro DBMS SID1 a jiný vyhrazený cluster se používá pro DBMS SID2 (dva clustery).
- Instance aplikačního serveru SAP pro systém SAP SID1 mají své vlastní vyhrazených virtuálních počítačích.
- Instance aplikačního serveru SAP pro systém SAP SID2 mají své vlastní vyhrazených virtuálních počítačích.

![Obrázek 10: SAP vysoké dostupnosti architektury šablona 3, s clusterem vyhrazené pro různé instance ASCS/SCS][sap-ha-guide-figure-6003]

_**Obrázek 10:** SAP vysoké dostupnosti architektury šablona 3, s clusterem vyhrazené pro různé instance ASCS/SCS_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Příprava infrastruktury

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Příprava infrastruktury pro architektury šablony 1
Šablony Azure Resource Manageru pro SAP zjednodušit nasazení požadované prostředky.

Třívrstvé šablon v Azure Resource Manageru také podporují scénáře vysoké dostupnosti, například v architektuře 1 šablony, které má dva clustery. Každý cluster se SAP jediný bod selhání pro SAP ASCS/SCS a DBMS.

Zde je, kde lze získat šablony Azure Resource Manageru ukázkový scénář, který popisujeme v tomto článku:

* [Image z Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Příprava infrastruktury architektury šablona 1:

- Na webu Azure Portal na **parametry** okno v **SYSTEMAVAILABILITY** vyberte **HA**.

  ![Obrázek 11: Nastavení parametrů Azure Resource Manageru vysoké dostupnosti SAP][sap-ha-guide-figure-3000]

_**Obrázek 11:** Nastavení parametrů Azure Resource Manageru vysoké dostupnosti SAP_


  Vytvoření šablony:

  * **Virtuální počítače**:
    * Virtuální počítače aplikačního serveru SAP: <*SAPSystemSID*> - di – <*číslo*>
    * Virtuální počítače clusteru ASC/SCS: <*SAPSystemSID*> - ascs – <*číslo*>
    * Systém DBMS clusteru: <*SAPSystemSID*> - db - <*číslo*>

  * **Síťové karty pro všechny virtuální počítače s přidružené IP adresy**:
    * <*SAPSystemSID*>-nic-di-<*Number*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*>-nic-db-<*Number*>

  * **Účty služby Azure storage**

  * **Skupiny dostupnosti** pro:
    * Virtuální počítače aplikačního serveru SAP: <*SAPSystemSID*> - avset - di
    * SAP ASCS/SCS clusteru virtuálních počítačů: <*SAPSystemSID*> - avset - ASC
    * Systém DBMS clusteru virtuálních počítačů: <*SAPSystemSID*> - avset - db

  * **Nástroje pro vyrovnávání zatížení Azure interní**:
    * Se všemi porty pro instanci ASCS/SCS IP adresa <*SAPSystemSID*> - lb - ASC
    * Se všemi porty pro SQL Server DBMS a IP adresu <*SAPSystemSID*> - lb - db

  * **Skupina zabezpečení sítě**: <*SAPSystemSID*> - nsg - ascs-0  
    * S otevřít externí protokolu RDP (Remote Desktop) port <*SAPSystemSID*> - ascs - 0 virtuálního počítače

> [!NOTE]
> Všechny IP adresy síťových karet a Azure interní služby load balancer se **dynamické** ve výchozím nastavení. Změnit tak, aby **statické** IP adresy. Zjistíte, jak to udělat později v tomto článku.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Nasazení virtuálních počítačů s připojením k podnikové síti (mezi různými místy) pro použití v produkčním prostředí
Pro produkční systémy SAP, nasaďte virtuální počítače Azure s [připojení k podnikové síti (mezi různými místy)] [ planning-guide-2.2] pomocí Azure Site-to-Site VPN nebo Azure ExpressRoute.

> [!NOTE]
> Vaše instance Azure Virtual Network můžete použít. Virtuální síť a podsíť již byly vytvořeny a připraveny.
>
>

1.  Na webu Azure Portal na **parametry** okno v **NEWOREXISTINGSUBNET** vyberte **existující**.
2.  V **SUBNETID** přidejte úplný řetězec připraveného sítí Azure SubnetID, kde plánujete nasadit virtuální počítače Azure.
3.  Pokud chcete získat seznam všech podsítí síť Azure, spusťte tento příkaz Powershellu:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  **ID** pole zobrazí **SUBNETID**.
4. Chcete-li získat seznam všech **SUBNETID** hodnoty, spusťte tento příkaz Powershellu:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  **SUBNETID** vypadá přibližně takto:

  ```
  /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
  ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Výhradně cloudový instance SAP pro testování a Ukázka nasazení
Vysoká dostupnost systému SAP v čistě cloudové nasazení modelu můžete nasadit. Tento typ nasazení se především užitečné pro ukázky a testovací případy použití. Není vhodné pro případy použití v produkčním prostředí.

- Na webu Azure Portal na **parametry** okno v **NEWOREXISTINGSUBNET** vyberte **nové**. Nechte **SUBNETID** prázdné pole.

  Šablony SAP Azure Resource Manageru automaticky vytvoří virtuální síť Azure a podsíť.

> [!NOTE]
> Také je nutné nasadit alespoň jeden vyhrazený virtuální počítač pro Active Directory a DNS ve stejné instanci Azure Virtual Network. Šablona nevytváří tyto virtuální počítače.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Příprava infrastruktury pro architektury šablony 2

Můžete použít tuto šablonu Azure Resource Manageru pro SAP, která pomůže zjednodušit nasazování prostředků infrastruktury požadované pro SAP architektury šablonu 2.

Zde je, kde lze získat šablony Azure Resource Manageru pro tento scénář nasazení:

* [Image z Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Příprava infrastruktury pro architektury šablony 3

Můžete připravit infrastrukturu a nakonfigurujte SAP pro **několika identifikátorů SID**. Například můžete přidat další instanci SAP ASCS/SCS do *existující* konfigurace clusteru. Další informace najdete v tématu [nakonfigurovat další instanci SAP ASCS/SCS do existující konfigurace clusteru pro vytvoření konfigurace několika identifikátorů SID SAP v Azure Resource Manageru][sap-ha-multi-sid-guide].

Pokud chcete vytvořit nový cluster s několika SID, můžete použít s několika SID [šablony quickstart na Githubu](https://github.com/Azure/azure-quickstart-templates).
Chcete-li vytvořit nový cluster s několika SID, budete muset nasadit následující tři šablony:

* [Šablona ASCS/SCS](#ASCS-SCS-template)
* [Databázové šablony](#database-template)
* [Šablona aplikace servery](#application-servers-template)

Následující části obsahují více podrobností o šablony a parametrů, které je potřeba zadat v šablonách.

#### <a name="ASCS-SCS-template"></a> Šablona ASCS/SCS

Šablona ASCS/SCS nasadí dva virtuální počítače, které můžete použít k vytvoření clusteru převzetí služeb při selhání Windows serveru, který je hostitelem více instancí ASCS/SCS.

Nastavení v šabloně několika identifikátorů SID ASCS/SCS [šablony s několika SID ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image], zadejte hodnoty následujících parametrů:

  - **Předpona prostředků**.  Nastavte zdroj předpona, která se používá jako předpona všechny prostředky, které jsou vytvořeny během nasazení. Protože prostředky nepatří do jediného systému SAP, předpona prostředku není identifikátor SID jednoho systému SAP.  Předpona, která musí být v rozmezí **tři až šest znaků**.
  - **Stack – typ**. Vyberte typ zásobníku systému SAP. V závislosti na typu zásobníku Azure Load Balancer má jeden (ABAP a Java pouze) nebo dvě (ABAP + Java) privátních IP adres na systému SAP.
  -  **Typ operačního systému**. Vyberte operační systém z virtuálních počítačů.
  -  **Počet systémů SAP**. Vyberte počet systémů SAP, kterou chcete nainstalovat v tomto clusteru.
  -  **Dostupnost systému**. Vyberte **HA**.
  -  **Uživatelské jméno Admin a heslo správce**. Vytvoření nového uživatele, který můžete použít k přihlášení k počítači.
  -  **Nové nebo existující podsíti**. Nastavte, zda se má vytvořit novou virtuální síť a podsíť, nebo by měla sloužit existující podsítě. Pokud již máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
  -  **Id podsítě**. Pokud chcete nasadit virtuální počítač do existující virtuální síť ve kterých máte definované podsíti virtuálního počítače by se měla přiřadit k pojmenování ID tuto konkrétní podsíť. ID obvykle vypadá takto: /subscriptions/ <*id předplatného*> /resourceGroups/ <*název skupiny prostředků*> /providers/Microsoft.Network/virtualNetworks/ < *název virtuální sítě*> /subnets/ <*název podsítě*>

Šablona nasadí jednu instanci Azure Load Balancer, která podporuje několik systémů SAP.

- Instance ASC se konfigurují pro číslo instance 00, 10, 20...
- Instance SCS se konfigurují pro instanci číslo od 01, 11, 21...
- Instance ASCS zařadit do fronty replikace serveru (Lajících) (pouze Linux) se konfigurují pro číslo instance 02, 12, 22...
- Instance SCS Lajících (pouze Linux) se konfigurují pro číslo instance 03, 13, 23...

Nástroje pro vyrovnávání zatížení obsahuje 1 (2 pro Linux) VIP(s), 1 x virtuální IP adresu pro ASCS/SCS a 1 x virtuální IP adresu pro Lajících (pouze Linux).

Následující seznam obsahuje všechna pravidla (kde x je počet systému SAP, například 1, 2, 3...) pro vyrovnávání zatížení:
- Windows specifické porty protokolu pro každý systém SAP: 445, 5985
- Porty ASCS (číslo instance x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Porty SCS (číslo instance x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Porty ASCS Lajících v Linuxu (číslo instance x2): 33x2, 5x213, 5x214, 5x216
- Porty SCS Lajících v Linuxu (číslo instance x3): 33x3, 5x313, 5x314, 5x316

Nástroje pro vyrovnávání zatížení je nakonfigurován na použití následující porty sondy (kde x je počet systému SAP, například 1, 2, 3...):
- Portu sondy nástroje pro vyrovnávání zatížení interní ASCS/SCS: 620x0
- Zatížení Lajících interní nástroje pro vyrovnávání portu sondy (pouze Linux): 621x2

#### <a name="database-template"></a> Databázové šablony

Databáze šablona nasadí jeden nebo dva virtuální počítače, které můžete použít k instalaci systému pro správu relačních databází (RDBMS) pro jeden systému SAP. Například pokud nasadíte šablonu ASCS/SCS pro pět systémů SAP, musíte nasadit tuto šablonu pětkrát.

V šabloně několika identifikátorů SID databázi nastavit [šablona databází s několika SID][sap-templates-3-tier-multisid-db-marketplace-image], zadejte hodnoty následujících parametrů:

  -  **Id systému SAP**. Zadejte ID systému SAP systému SAP, ve kterém chcete nainstalovat. ID se použije jako předpona pro prostředky, které jsou nasazené.
  -  **Typ operačního systému**. Vyberte operační systém z virtuálních počítačů.
  -  **Hodnota DbType**. Vyberte typ databáze, kterou chcete nainstalovat na clusteru. Vyberte **SQL** Pokud chcete nainstalovat Microsoft SQL Server. Vyberte **HANA** Pokud budete chtít nainstalovat SAP HANA na virtuálních počítačích. Je nutné vybrat typ správný operační systém: vyberte **Windows** pro SQL a vyberte Linuxovou distribuci pro HANA. Azure Load Balancer, která je připojená k virtuálním počítačům se nakonfiguruje pro podporu typ vybrané databáze:
    * **SQL**. Nástroje pro vyrovnávání zatížení bude – nástroj pro vyrovnávání zatížení port 1433. Ujistěte se, že tento port použít pro nastavení SQL serveru Always On.
    * **HANA**. Nástroje pro vyrovnávání zatížení bude porty 35015 a 35017 – nástroj pro vyrovnávání zatížení. Ujistěte se, že instalace SAP HANA s instancí číslo **50**.
    Nástroj pro vyrovnávání zatížení použije portu sondy 62550.
  -  **Velikost systému SAP**. Nastavte počet protokoly SAP poskytne nový systém. Pokud si nejste jisti kolik protokoly SAP bude systém vyžadovat, požádejte SAP technologické partnery nebo systémový integrátor.
  -  **Dostupnost systému**. Vyberte **HA**.
  -  **Uživatelské jméno Admin a heslo správce**. Vytvoření nového uživatele, který můžete použít k přihlášení k počítači.
  -  **Id podsítě**. Zadejte ID podsítě, který jste použili při nasazení ASCS/SCS šablony nebo ID podsítě, který byl vytvořen jako součást nasazení šablony ASCS/SCS.

#### <a name="application-servers-template"></a> Šablona aplikace servery

Šablona servery aplikace nasadí dvě nebo více virtuálních počítačů, které může sloužit jako aplikační Server SAP instance systému SAP jeden. Například pokud nasadíte šablonu ASCS/SCS pro pět systémů SAP, musíte nasadit tuto šablonu pětkrát.

V šabloně několika identifikátorů SID servery aplikace nastavit [šablony s několika SID servery aplikace][sap-templates-3-tier-multisid-apps-marketplace-image], zadejte hodnoty následujících parametrů:

  -  **Id systému SAP**. Zadejte ID systému SAP systému SAP, ve kterém chcete nainstalovat. ID se použije jako předpona pro prostředky, které jsou nasazené.
  -  **Typ operačního systému**. Vyberte operační systém z virtuálních počítačů.
  -  **Velikost systému SAP**. Počet protokoly SAP poskytne nový systém. Pokud si nejste jisti kolik protokoly SAP bude systém vyžadovat, požádejte SAP technologické partnery nebo systémový integrátor.
  -  **Dostupnost systému**. Vyberte **HA**.
  -  **Uživatelské jméno Admin a heslo správce**. Vytvoření nového uživatele, který můžete použít k přihlášení k počítači.
  -  **Id podsítě**. Zadejte ID podsítě, který jste použili při nasazení ASCS/SCS šablony nebo ID podsítě, který byl vytvořen jako součást nasazení šablony ASCS/SCS.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Virtuální síť Azure
V našem příkladu je adresní prostor virtuální sítě Azure 10.0.0.0/16. Existuje jedna podsíť s názvem **podsítě**, s rozsahem adres 10.0.0.0/24. Všechny virtuální počítače a interní služby load balancer, jsou nasazené v této virtuální síti.

> [!IMPORTANT]
> Neprovádějte žádné změny k nastavení sítě v hostovaném operačním systému. To zahrnuje IP adresy serverů DNS a podsítě. Konfigurace nastavení sítě v Azure. Služba Dynamic Host Configuration Protocol (DHCP) rozšíří vaše nastavení.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP addresses

Chcete-li nastavit požadované DNS IP adresy, proveďte následující kroky.

1.  Na webu Azure Portal na **servery DNS** okno, ujistěte se, že vaše virtuální síť **servery DNS** je možnost nastavená na **vlastního DNS**.
2.  Vyberte nastavení na základě typu sítě, které máte. Další informace najdete v následujících materiálech:
    * [Připojení k podnikové síti (mezi různými místy)][planning-guide-2.2]: Přidejte IP adresy na místních serverech DNS.  
    Můžete rozšířit místní servery DNS pro virtuální počítače, na kterých běží v Azure. V tomto scénáři můžete přidat IP adresy virtuálních počítačů Azure na které spouštíte službu DNS.
    * [Výhradně cloudový nasazení][planning-guide-2.1]: Nasaďte další virtuální počítač ve stejné virtuální síti instanci, která slouží jako DNS server. Přidejte IP adresy virtuálních počítačů Azure, které jste nastavili pro spuštění služby DNS.

    ![Obrázek 12: Konfigurace serverů DNS pro Azure Virtual Network][sap-ha-guide-figure-3001]

    _**Obrázek 12:** Konfigurace serverů DNS pro Azure Virtual Network_

  > [!NOTE]
  > Pokud změníte IP adresy serverů DNS, budete muset restartovat virtuální počítače Azure na použití změny a šíření nových serverů DNS.
  >
  >

V našem příkladu služba DNS nainstalovaná a nakonfigurovaná na těchto virtuálních počítačích Windows:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První server DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Druhý server DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Názvy hostitelů a statické IP adresy pro clusterovou instanci SAP ASCS/SCS a Clusterové instance DBMS

Pro místní nasazení je třeba tyto názvy vyhrazené hostitele a IP adresy:

| Virtuální hostitel název role | Název virtuálního hostitele | Virtuální statická IP adresa |
| --- | --- | --- |
| SAP ASCS/SCS první cluster virtuální název hostitele (Správa clusteru) |pr1-ascs-vir |10.0.0.42 |
| Název virtuálního hostitele instanci SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| K SAP DBMS druhý cluster virtuálního hostitele název (Správa clusteru) |pr1-dbms-vir |10.0.0.32 |

Při vytváření clusteru vytvořit názvy virtuálních hostitelů **pr1. ascs vir** a **pr1. dbms vir** a přidružené IP adresy, které spravují samotného clusteru. Informace o tom, jak to provést, najdete v tématu [shromažďovat uzly clusteru v konfiguraci clusteru][sap-ha-guide-8.12.1].

Další dva názvy virtuálních hostitelů, můžete ručně vytvořit **pr1 ascs sap** a **pr1 dbms sap**a přidružené IP adresy na serveru DNS. Tyto prostředky používat Clusterované instance SAP ASCS/SCS a Clusterové instance DBMS. Informace o tom, jak to provést, najdete v tématu [vytvořte název virtuálního hostitele pro clusterovou instanci SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Nastavení statické IP adresy pro SAP virtual machines
Po nasazení virtuálních počítačů pro použití v clusteru, je nutné nastavit statických IP adres pro všechny virtuální počítače. To udělat v konfiguraci Azure Virtual Network a ne v hostovaném operačním systému.

1.  Na webu Azure Portal, vyberte **skupiny prostředků** > **síťová karta** > **nastavení** > **IP adresu**.
2.  Na **IP adresy** okně v části **přiřazení**vyberte **statické**. V **IP adresu** zadejte IP adresu, kterou chcete použít.

  > [!NOTE]
  > Pokud změníte IP adresu síťové karty, budete muset restartovat virtuální počítače Azure na použití změny.  
  >
  >

  ![Obrázek 13: Nastavení statické IP adresy pro síťové karty jednotlivých virtuálních počítačů][sap-ha-guide-figure-3002]

  _**Obrázek 13:** Nastavení statické IP adresy pro síťové karty jednotlivých virtuálních počítačů_

  Tento krok opakujte pro všechna síťová rozhraní, které se pro všechny virtuální počítače, včetně virtuálních počítačů, které chcete použít pro vaši službu Active Directory a DNS.

V našem příkladu máme tyto virtuální počítače a statické IP adresy:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Název síťové karty | Statická IP adresa |
| --- | --- | --- | --- |
| První instance aplikační Server SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Druhou instanci aplikační Server SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Poslední instance aplikační Server SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Prvním uzlu clusteru pro instanci ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Druhý uzel clusteru pro instanci ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Prvním uzlu clusteru pro instanci DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Druhý uzel clusteru pro instanci DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Nastavit statickou IP adresu nástroje pro vyrovnávání zatížení Azure interní

Vytvoří šablona SAP Azure Resource Manageru Azure interní nástroj pro vyrovnávání zatížení, který se používá pro SAP ASCS/SCS instanci clusteru a clusteru DBMS.

> [!IMPORTANT]
> IP adresa SAP ASCS/SCS název virtuálního hostitele je stejný jako IP adresu interního nástroje SAP ASCS/SCS: **pr1-lb-ascs**.
> IP adresa virtuální název tohoto správce databáze je stejný jako IP adresu interního nástroje DBMS: **pr1-lb-dbms**.
>
>

Nastavení statické IP adresy Azure interního nástroje load balancer:

1.  Počáteční nasazení nastaví IP adresu interního nástroje pro vyrovnávání na **dynamické**. Na webu Azure Portal na **IP adresy** okně v části **přiřazení**vyberte **statické**.
2.  Nastavit IP adresu interního nástroje **pr1-lb-ascs** IP adresu virtuálního hostitele název instance SAP ASCS/SCS.
3.  Nastavit IP adresu interního nástroje **pr1-lb-dbms** IP adresu virtuálního hostitele název instance databázového systému.

  ![Obrázek 14: Nastavení statické IP adresy pro interní služby load balancer pro instanci SAP ASCS/SCS][sap-ha-guide-figure-3003]

  _**Obrázek 14:** Nastavení statické IP adresy pro interní služby load balancer pro instanci SAP ASCS/SCS_

V našem příkladu máme dvě Azure interní služby load balancer, které mají tyto statické IP adresy:

| Role Vyrovnávání zatížení Azure interní | Název nástroje pro vyrovnávání zatížení Azure interní | Statická IP adresa |
| --- | --- | --- |
| SAP ASCS/SCS instance interního nástroje load balancer |pr1-lb-ascs |10.0.0.43 |
| K SAP DBMS interní nástroj pro vyrovnávání zatížení |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Výchozí pravidla pro Azure interního nástroje pro vyrovnávání zatížení ASCS/SCS

SAP Azure Resource Manageru šablony vytvoří porty, které potřebujete:
* Instance ABAP ASCS pomocí výchozí instance číslo **00**
* Instanci Java SCS pomocí výchozí instance číslo **01**

Pokud nainstalujete instanci SAP ASCS/SCS, je nutné použít číslo výchozí instance **00** pro vaši instanci ABAP ASCS a číslo výchozí instance **01** pro vaši instanci Java SCS.

Dále vytvořte požadované interní koncové body pro SAP NetWeaver porty Vyrovnávání zatížení.

K vytvoření požadované interního vyrovnávání koncové body, nejdřív vytvořte tyto koncové body pro porty SAP NetWeaver ABAP ASCS pro vyrovnávání zatížení:

| Název pravidla Vyrovnávání zatížení/služby | Výchozí čísla portů | Konkrétní porty (ASCS instance s instancí číslo 00) (Lajících s 10) |
| --- | --- | --- |
| Enqueue Server / *lbrule3200* |32<*InstanceNumber*> |3200 |
| Server ABAP zpráv / *lbrule3600* |36<*InstanceNumber*> |3600 |
| Zpráva o interní ABAP / *lbrule3900* |39<*InstanceNumber*> |3900 |
| Message Server HTTP / *Lbrule8100* |81<*InstanceNumber*> |8100 |
| SAP Start služba ASC HTTP / *Lbrule50013* |5<*InstanceNumber*>13 |50013 |
| SAP Start služba ASC HTTPS / *Lbrule50014* |5<*InstanceNumber*>14 |50014 |
| Zařazení do fronty replikace / *Lbrule50016* |5<*InstanceNumber*>16 |50016 |
| SAP spuštění služby Lajících HTTP *Lbrule51013* |5<*InstanceNumber*>13 |51013 |
| SAP spuštění služby Lajících HTTP *Lbrule51014* |5<*InstanceNumber*>14 |51014 |
| Vyhrajte RM *Lbrule5985* | |5985 |
| Sdílená složka *Lbrule445* | |445 |

_**Tabulka 1:** Čísla portů instancí SAP NetWeaver ABAP ASCS_

Vytvořte tyto koncové body pro SAP NetWeaver Java SCS porty pro vyrovnávání zatížení:

| Název pravidla Vyrovnávání zatížení/služby | Výchozí čísla portů | Konkrétní porty (SCS instance s instancí číslo 01) (Lajících s 11) |
| --- | --- | --- |
| Enqueue Server / *lbrule3201* |32<*InstanceNumber*> |3201 |
| Gateway Server / *lbrule3301* |33<*InstanceNumber*> |3301 |
| Java Message Server / *lbrule3900* |39<*InstanceNumber*> |3901 |
| Message Server HTTP / *Lbrule8101* |81<*InstanceNumber*> |8101 |
| SAP spuštění služby SCS HTTP / *Lbrule50113* |5<*InstanceNumber*>13 |50113 |
| SAP spuštění služby SCS HTTPS / *Lbrule50114* |5<*InstanceNumber*>14 |50114 |
| Zařazení do fronty replikace / *Lbrule50116* |5<*InstanceNumber*>16 |50116 |
| SAP spuštění služby Lajících HTTP *Lbrule51113* |5<*InstanceNumber*>13 |51113 |
| SAP spuštění služby Lajících HTTP *Lbrule51114* |5<*InstanceNumber*>14 |51114 |
| Vyhrajte RM *Lbrule5985* | |5985 |
| Sdílená složka *Lbrule445* | |445 |

_**Tabulka 2:** Čísla portů instancí SAP NetWeaver Java SCS_

![Obrázek 15: Výchozí pravidla pro Azure interního nástroje pro vyrovnávání zatížení ASCS/SCS][sap-ha-guide-figure-3004]

_**Obrázek 15:** Výchozí pravidla pro Azure interního nástroje pro vyrovnávání zatížení ASCS/SCS_

Nastavit IP adresu nástroje pro vyrovnávání zatížení **pr1-lb-dbms** IP adresu virtuálního hostitele název instance databázového systému.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Změna pravidel nástroje pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC/SCS

Pokud chcete použít různá čísla pro instance SAP ASCS nebo SCS, musíte změnit názvy a hodnoty jejich porty z výchozí hodnoty.

1.  Na webu Azure Portal, vyberte  **< *SID*> Nástroje pro vyrovnávání zatížení - lb - ascs** > **pravidla Vyrovnávání zatížení**.
2.  Pro všechny zátěže pravidla, která patří k instanci SAP ASCS nebo SCS změňte tyto hodnoty:

  * Název
  * Port
  * Port back-end

  Například pokud chcete změnit výchozí číslo instance ASCS od 00 do 31, budete muset provést změny pro všechny porty uvedené v tabulce 1.

  Tady je příklad příkazu update pro port *lbrule3200*.

  ![Obrázek 16: Změna pravidel nástroje pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC/SCS][sap-ha-guide-figure-3005]

  _**Obrázek 16:** Změna pravidel nástroje pro vyrovnávání zatížení Azure interní Vyrovnávání zatížení výchozí ASC/SCS_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Přidání Windows virtuálních počítačů k doméně

Po přiřazení statické IP adresy k virtuálním počítačům, přidejte virtuální počítače k doméně.

![Obrázek 17: Přidat virtuální počítač k doméně][sap-ha-guide-figure-3006]

_**Obrázek 17:** Přidat virtuální počítač k doméně_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Přidat položky registru v obou uzlů clusteru z instance SAP ASCS/SCS

Nástroj Azure Load Balancer má interního nástroje load balancer, že čas zavře připojení po určitou dobu nastavit jsou nečinné připojení (časový limit nečinnosti). SAP pracovní procesy v dialogovém okně otevřená připojení instancí SAP zařadit do fronty zpracovat jako první zařadit do fronty/dequeue požadavku musí být odeslán. Tato připojení zůstanou obvykle zavedené až do pracovního procesu nebo restartování procesu zařazení do fronty. Ale pokud je připojení nastavte dobu nečinnosti, nástroje pro vyrovnávání zatížení Azure interní zavře připojení. To není problém, protože pracovní proces SAP obnoví připojení k procesu zařazení do fronty, pokud už existuje. Tyto aktivity jsou popsány v trasování pro vývojáře SAP procesů, ale vytvářet velké množství další obsah v těchto trasování. Je vhodné změnit TCP/IP `KeepAliveTime` a `KeepAliveInterval` na oba uzly clusteru. Sloučit tyto změny v protokolu TCP/IP parametry s parametry profilu SAP, je popsáno dále v tomto článku.

Chcete-li přidat položky registru v obou uzlů clusteru z instance SAP ASCS/SCS, nejprve přidejte tyto položky registru Windows na oba uzly clusteru Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveTime` |
| Typ proměnné |REG_DWORD (decimální) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabulka 3:** Změnit první parametr protokolu TCP/IP_

Pak přidejte této položky registru Windows na oba uzly clusteru Windows pro SAP ASCS/SCS:

| Cesta | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Název proměnné |`KeepAliveInterval` |
| Typ proměnné |REG_DWORD (decimální) |
| Hodnota |120000 |
| Odkaz na dokumentaci |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabulka 4:** Změňte druhý parametr protokolu TCP/IP_

**Aby se změny projevily, restartujte obou uzlů clusteru**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Nastavení clusteru Windows Server Failover Clustering pro instanci SAP ASCS/SCS

Nastavení clusteru Windows Server Failover Clustering pro instanci SAP ASCS/SCS zahrnuje tyto úlohy:

- Shromažďují se uzly clusteru v konfiguraci clusteru
- Konfigurace určující sdílené složky clusteru

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Shromažďovat uzly clusteru v konfiguraci clusteru

1.  Přidat Role a funkce průvodce přidejte clusteringu obou uzlů clusteru převzetí služeb při selhání.
2.  Nastavení clusteru převzetí služeb při selhání pomocí Správce clusteru převzetí služeb při selhání. V modulu Správce clusteru převzetí služeb při selhání vyberte **vytvořit Cluster**a pak přidejte název první cluster uzel A. Bez přidání druhého uzlu ještě; přidejte druhý uzel v pozdějším kroku.

  ![Obrázek 18: Přidat název serveru nebo na virtuálním počítači na prvním uzlu clusteru][sap-ha-guide-figure-3007]

  _**Obrázek 18:** Přidat název serveru nebo na virtuálním počítači na prvním uzlu clusteru_

3.  Zadejte síťový název (název hostitele virtuálního) clusteru.

  ![Obrázek 19: Zadejte název clusteru][sap-ha-guide-figure-3008]

  _**Obrázek 19:** Zadejte název clusteru_

4.  Po vytvoření clusteru, spusťte test ověření clusteru.

  ![Obrázek 20: Spusťte kontrolu ověření clusteru][sap-ha-guide-figure-3009]

  _**Obrázek 20:** Spusťte kontrolu ověření clusteru_

  Můžete ignorovat jakékoli upozornění o discích v tuto chvíli v procesu. Přidáte určující sdílené složky a SIOS sdílené disky později. V této fázi se nemusíte obávat o kvorum.

  ![Obrázek 21: Nenajde žádný disk kvora][sap-ha-guide-figure-3010]

  _**Obrázek 21:** Nenajde žádný disk kvora_

  ![Obrázek 22: Základní prostředky clusteru potřebuje novou IP adresu][sap-ha-guide-figure-3011]

  _**Obrázek 22:** Základní prostředky clusteru potřebuje novou IP adresu_

5.  Změna IP adresy clusteru služby jádra. Clusteru nelze spustit dokud nezměníte IP adresu clusteru služby jádra, protože IP adresa serveru odkazuje na jednom uzlu virtuálního počítače. To udělat na **vlastnosti** stránky Clusterová služba core IP prostředku.

  Například, musíme přiřadit IP adresu (v našem příkladu **10.0.0.42**) pro název virtuálního hostitele clusteru **pr1. ascs vir**.

  ![Obrázek 23: V dialogovém okně Vlastnosti změňte IP adresu][sap-ha-guide-figure-3012]

  _**Obrázek 23:** V **vlastnosti** dialogové okno pole, změňte IP adresu_

  ![Obrázek 24: Přiřaďte IP adresu, která je vyhrazena pro cluster][sap-ha-guide-figure-3013]

  _**Obrázek 24:** Přiřaďte IP adresu, která je vyhrazena pro cluster_

6.  Přeneste virtuální hostitel název clusteru online.

  ![Obrázek 25: Clusterová služba jádra je v provozu a pracovat a pomocí správné IP adres][sap-ha-guide-figure-3014]

  _**Obrázek 25:** Clusterová služba jádra je v provozu a pracovat a pomocí správné IP adres_

7.  Přidání druhého uzlu clusteru.

  Teď, když jádro Clusterová služba je spuštěná, můžete přidat druhý uzel clusteru.

  ![Obrázek 26: Přidání druhého uzlu clusteru][sap-ha-guide-figure-3015]

  _**Obrázek 26:** Přidání druhého uzlu clusteru_

8.  Zadejte název druhého uzlu clusterů hostitelů.

  ![Obrázek 27: Zadejte název hostitele druhý uzel clusteru][sap-ha-guide-figure-3016]

  _**Obrázek 27:** Zadejte název hostitele druhý uzel clusteru_

  > [!IMPORTANT]
  > Ujistěte se, že **přidat do clusteru veškeré oprávněné úložiště** zaškrtávací políčko je **není** vybrané.  
  >
  >

  ![Obrázek 28: Nevybírejte zaškrtávací políčko][sap-ha-guide-figure-3017]

  _**Obrázek 28:** Proveďte **není** vyberte zaškrtávací políčko_

  Můžete ignorovat varování o kvora a disky. Můžete nastavit kvora a sdílet disk později, jak je popsáno v [instalace SIOS DataKeeper Cluster Edition pro disk sdílená složka clusteru SAP ASCS/SCS][sap-ha-guide-8.12.3].

  ![Obrázek 29: Ignorovat upozornění na disku kvora][sap-ha-guide-figure-3018]

  _**Obrázek 29:** Ignorovat upozornění na disku kvora_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurovat určující sdílenou složku clusteru

Konfigurace určující sdílené složky clusteru zahrnuje tyto úlohy:

- Vytvoření sdílené složky
- Nastavení souboru sdílenou složku s kopií clusteru kvora v modulu Správce clusteru převzetí služeb při selhání

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Vytvoření sdílené složky

1.  Vyberte určující sdílená složka místo disk kvora. SIOS DataKeeper podporuje tuto možnost.

  V příkladech v tomto článku se určující sdílená složka na serveru/DNS služby Active Directory, na kterém běží v Azure. Určující sdílená složka se nazývá **domcontr 0**. Vzhledem k tomu, že by jste nakonfigurovali připojení VPN k Azure (prostřednictvím sítě Site-to-Site VPN nebo Azure ExpressRoute), vaše/DNS služby Active Directory je místní služba a není vhodná umožňuje spustit soubor sdílet s kopií clusteru.

  > [!NOTE]
  > Pokud vaše služba Active Directory a DNS používá pouze v místním, nekonfigurujte v operačním systému Windows Active Directory a DNS, na kterém běží v místním vaše určující sdílenou složku. Latence sítě mezi uzly clusteru, které jsou spuštěné v Azure a/DNS služby Active Directory v místním může být příliš velký a způsobit problémy s připojením. Je potřeba nakonfigurovat určující sdílenou složku na virtuálním počítači Azure, na kterém běží blízko uzlu clusteru.  
  >
  >

  Disk kvora potřebuje aspoň 1 024 MB volného místa. Doporučujeme, abyste 2 048 MB volného místa pro disk kvora.

2.  Přidejte objekt názvu clusteru.

  ![Obrázek 30: Přiřazení oprávnění pro sdílenou složku pro objekt názvu clusteru][sap-ha-guide-figure-3019]

  _**Obrázek 30:** Přiřazení oprávnění pro sdílenou složku pro objekt názvu clusteru_

  Ujistěte se, že oprávnění zahrnují oprávnění ke změně dat ve sdílené složce pro objekt názvu clusteru (v našem příkladu **pr1. ascs vir$**).

3.  Chcete-li přidat objekt názvu clusteru do seznamu, **přidat**. Změňte filtr, aby se vyhledávat objekty počítačů, kromě těch uvedených v obrázek 31.

  ![Obrázek 31: Změnit typy objektů, které chcete zahrnout počítače][sap-ha-guide-figure-3020]

  _**Obrázek 31:** Změnit typy objektů, které chcete zahrnout počítače_

  ![Obrázek 32: Zaškrtněte políčko počítače][sap-ha-guide-figure-3021]

  _**Obrázek 32:** Vyberte **počítače** zaškrtávací políčko_

4.  Zadejte objekt názvu clusteru, jak ukazuje obrázek 31. Protože byl vytvořen záznam, můžete změnit oprávnění, jak ukazuje obrázek 30.

5.  Vyberte **zabezpečení** podrobnější kartu sdílené složky a pak nastavte oprávnění pro objekt názvu clusteru.

  ![Obrázek 33: Nastavit atributy zabezpečení pro objekt názvu clusteru na sdílenou složku kvora souboru][sap-ha-guide-figure-3022]

  _**Obrázek 33:** Nastavit atributy zabezpečení pro objekt názvu clusteru na sdílenou složku kvora souboru_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Nastavení kvora s kopií clusteru sdílené složky souboru v modulu Správce clusteru převzetí služeb při selhání

1.  Otevřít kvora nastavení Průvodce konfigurací.

  ![Obrázek 34: Spuštění Průvodce konfigurací kvora clusteru nastavení][sap-ha-guide-figure-3023]

  _**Obrázek 34:** Spuštění Průvodce konfigurací kvora clusteru nastavení_

2.  Na **vyberte takovou konfiguraci kvóra** stránce **vybrat určující disk kvora**.

  ![Obrázek 35: Konfigurace kvora, které si můžete vybrat z][sap-ha-guide-figure-3024]

  _**Obrázek 35:** Konfigurace kvora, které si můžete vybrat z_

3.  Na **vybrat určující disk kvora** stránce **nakonfigurovat určující sdílenou složku souboru**.

  ![Obrázek 36: Vybrat určující sdílenou složku][sap-ha-guide-figure-3025]

  _**Obrázek 36:** Vybrat určující sdílenou složku_

4.  Zadejte cestu UNC ke sdílené složce (v našem příkladu \\domcontr 0\FSW). Pokud chcete zobrazit seznam změn, můžete provést, vyberte **Další**.

  ![Obrázek 37: Definovat umístění sdílené složky souboru pro sdílenou složku s kopií clusteru][sap-ha-guide-figure-3026]

  _**Obrázek 37:** Definovat umístění sdílené složky souboru pro sdílenou složku s kopií clusteru_

5.  Vyberte možnost změny a pak vyberte **Další**. Je potřeba překonfigurovat úspěšně konfigurace clusteru, jak ukazuje obrázek 38.  

  ![Obrázek 38: Potvrzení, že jste změnili konfiguraci clusteru][sap-ha-guide-figure-3027]

  _**Obrázek 38:** Potvrzení, že jste změnili konfiguraci clusteru_

Po instalaci clusteru převzetí služeb při selhání Windows úspěšně, třeba změny provedené některé prahové hodnoty pro přizpůsobení převzetí služeb při selhání detekce podmínky v Azure. Změnit parametry jsou popsané v tomto blogu: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Za předpokladu, že dva virtuální počítače, které sestavení konfigurace clusteru Windows pro ASCS/SCS jsou ve stejné podsíti, je potřeba změnit tak, aby tyto hodnoty následujících parametrů:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Tato nastavení byly testovány se zákazníky a k dispozici dobré ohrožení odolné proti chybám dostatečně na jedné straně. Na druhé straně se tato nastavení rychle poskytuje dostatek převzetí služeb při selhání ve skutečné chybové stavy při selhání uzlu nebo virtuální počítač nebo softwaru SAP. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalace SIOS DataKeeper Cluster Edition pro sdílenou složku disk clusteru SAP ASCS/SCS

Nyní máte funkční konfigurace systému Windows Server Failover Clustering v Azure. Ale pokud chcete nainstalovat instanci SAP ASCS/SCS, musíte prostředek sdíleného disku. Nelze vytvořit sdílené síťové prostředky, které potřebujete v Azure. SIOS DataKeeper Cluster Edition je řešení třetích stran, které můžete použít k vytvoření sdílené síťové prostředky.

Instalace pro sdílenou složku disk clusteru SAP ASCS/SCS SIOS DataKeeper Cluster Edition zahrnuje tyto úlohy:

- Přidání rozhraní .NET Framework 3.5
- Instalace SIOS DataKeeper
- Nastavení SIOS DataKeeper

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Přidání rozhraní .NET Framework 3.5
Rozhraní Microsoft .NET Framework 3.5 není automaticky aktivuje nebo nainstalovaný v systému Windows Server 2012 R2. Protože SIOS DataKeeper vyžaduje rozhraní .NET Framework na všechny uzly, které instalujete DataKeeper, musíte nainstalovat rozhraní .NET Framework 3.5 na hostovaný operační systém všech virtuálních počítačů v clusteru.

Existují dva způsoby, jak přidat rozhraní .NET Framework 3.5:

- Pomocí funkce Průvodce přidáním rolí a ve Windows, jak ukazuje obrázek 39.

  ![Obrázek 39: Instalace rozhraní .NET Framework 3.5 pomocí funkce Průvodce přidáním rolí a][sap-ha-guide-figure-3028]

  _**Obrázek 39:** Instalace rozhraní .NET Framework 3.5 pomocí funkce Průvodce přidáním rolí a_

  ![Obrázek 40: Průběh instalace panelu při instalaci rozhraní .NET Framework 3.5 pomocí funkce Průvodce přidáním rolí a][sap-ha-guide-figure-3029]

  _**Obrázek 40:** Průběh instalace panelu při instalaci rozhraní .NET Framework 3.5 pomocí funkce Průvodce přidáním rolí a_

- Pomocí příkazového řádku nástroj dism.exe. Pro tento typ instalace potřebujete přístup k adresáři SxS na instalačním médiu nástroje Windows. Na příkazovém řádku se zvýšenými oprávněními zadejte příkaz:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Nainstalujte SIOS DataKeeper

SIOS DataKeeper Cluster Edition nainstalujte na každý uzel v clusteru. Vytvořit virtuální sdílené úložiště se SIOS Datakeeperem, synchronizované zrcadlení a potom simulovat clusteru sdíleného úložiště.

Než nainstalujete SIOS software, vytvořte uživatele domény **DataKeeperSvc**.

> [!NOTE]
> Přidat **DataKeeperSvc** uživateli **Local Administrator** na obou uzlů clusteru.
>
>

Chcete-li nainstalovat SIOS DataKeeper:

1.  Nainstalujte SIOS software na oba uzly clusteru.

  ![SIOS instalačního programu][sap-ha-guide-figure-3030]

  ![Obrázek 41: První stránka Instalace SIOS DataKeeper][sap-ha-guide-figure-3031]

  _**Obrázek 41:** První stránka Instalace SIOS DataKeeper_

2.  V dialogovém okně znázorňuje obrázek 42 vyberte **Ano**.

  ![Obrázek 42: DataKeeper vás informuje, že služba se deaktivuje.][sap-ha-guide-figure-3032]

  _**Obrázek 42:** DataKeeper vás informuje, že služba se deaktivuje._

3.  V dialogovém okně zobrazí obrázek 43, doporučujeme vám, že vyberete **účet domény nebo serveru**.

  ![Obrázek 43: Výběr uživatele pro SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Obrázek 43:** Výběr uživatele pro SIOS DataKeeper_

4.  Zadejte uživatelské jméno účtu domény a hesla, které jste vytvořili pro SIOS DataKeeper.

  ![Obrázek 44: Zadejte doménu uživatelské jméno a heslo pro instalaci SIOS DataKeeper][sap-ha-guide-figure-3034]

  _**Obrázek 44:** Zadejte doménu uživatelské jméno a heslo pro instalaci SIOS DataKeeper_

5.  Licenční klíč pro vaši instanci SIOS DataKeeper nainstalujte, jak ukazuje obrázek 45.

  ![Obrázek 45: Zadejte svůj SIOS DataKeeper licenční klíč][sap-ha-guide-figure-3035]

  _**Obrázek 45:** Zadejte svůj SIOS DataKeeper licenční klíč_

6.  Po zobrazení výzvy restartujte virtuální počítač.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Nastavit SIOS DataKeeper

Jakmile nainstalujete SIOS DataKeeper na oba uzly, budete muset spustit konfiguraci. Cíl konfigurace je, aby synchronní replikaci dat mezi další virtuální pevné disky připojené k jednotlivým virtuálním počítačům.

1.  Spusťte nástroj Správa DataKeeper a konfigurace a pak vyberte **připojení serveru**. (V obrázek 46, tato možnost je v kruhu červeně.)

  ![Obrázek 46: SIOS DataKeeper Správa a konfigurace nástroje][sap-ha-guide-figure-3036]

  _**Obrázek 46:** SIOS DataKeeper Správa a konfigurace nástroje_

2.  Zadejte název nebo adresu protokolu TCP/IP na prvním uzlu, Správa a konfigurace nástroje by měla připojit na a druhý krok, druhý uzel.

  ![Obrázek 47: Vložit název nebo adresa protokolu TCP/IP na prvním uzlu pro správu a konfigurační nástroj má připojit na a druhý krok, druhý uzel][sap-ha-guide-figure-3037]

  _**Obrázek 47:** Vložit název nebo adresa protokolu TCP/IP na prvním uzlu pro správu a konfigurační nástroj má připojit na a druhý krok, druhý uzel_

3.  Vytvoření úlohy replikace mezi dvěma uzly.

  ![Obrázek 48: Vytvoření úlohy replikace][sap-ha-guide-figure-3038]

  _**Obrázek 48:** Vytvoření úlohy replikace_

  Průvodce vás provede procesem vytvoření úlohy replikace.
4.  Zadejte název, adresa TCP/IP a diskový svazek zdrojový uzel.

  ![Obrázek 49: Definujte název úlohy replikace][sap-ha-guide-figure-3039]

  _**Obrázek 49:** Definujte název úlohy replikace_

  ![Obrázek 50: Definuje základní data pro uzel, který by měl být aktuální zdrojový uzel][sap-ha-guide-figure-3040]

  _**Obrázek 50:** Definuje základní data pro uzel, který by měl být aktuální zdrojový uzel_

5.  Zadejte název, adresa TCP/IP a diskový svazek cílový uzel.

  ![Obrázek 51: Definuje základní data pro uzel, který by měl být aktuální cílový uzel][sap-ha-guide-figure-3041]

  _**Obrázek 51:** Definuje základní data pro uzel, který by měl být aktuální cílový uzel_

6.  Definujte algoritmy komprese. V našem příkladu doporučujeme kompresi streamu replikace. Zejména v situacích, opětovné synchronizace komprese streamu replikace výrazně snižuje čas synchronizace. Všimněte si, že komprese používá prostředky procesoru a paměť RAM virtuálního počítače. Jak se zvyšuje rychlost komprese zločinců se stejně objem prostředků procesoru použít. Můžete také upravit tato nastavení později.

7.  Další nastavení, které je potřeba zkontrolovat je, zda dojde k replikaci synchronně nebo asynchronně. *Když chráníte konfigurace SAP ASCS/SCS, je nutné použít synchronní replikace*.  

  ![Obrázek 52: Zadejte podrobnosti replikace][sap-ha-guide-figure-3042]

  _**Obrázek 52:** Zadejte podrobnosti replikace_

8.  Definujte, jestli by měly být zastoupeny svazek, který se replikuje pomocí replikace úlohy ke konfiguraci clusteru Windows Server Failover Clustering jako sdíleného disku. Konfigurace SAP ASCS/SCS, vyberte **Ano** tak, aby Windows cluster považuje replikovaného svazku sdíleného disku, který můžete použít jako svazek clusteru.

  ![Obrázek 53: Vyberte Ano. Pokud chcete nastavit replikovaného svazku jako svazek clusteru][sap-ha-guide-figure-3043]

  _**Obrázek 53:** Vyberte **Ano** nastavení replikovaného svazku jako svazek clusteru_

  Po vytvoření svazku DataKeeper Správa a konfigurace nástroje ukazuje, že úloha replikace je aktivní.

  ![Obrázek 54: Synchronní zrcadlení DataKeeper SAP ASCS/SCS sdílené složky disku je aktivní][sap-ha-guide-figure-3044]

  _**Obrázek 54:** Synchronní zrcadlení DataKeeper SAP ASCS/SCS sdílené složky disku je aktivní_

  Správce clusteru převzetí služeb při selhání disku jako disku DataKeeper teď zobrazuje, jak ukazuje obrázek 55.

  ![Obrázek 55: Správce clusteru převzetí služeb při selhání se zobrazí na disku, který DataKeeper replikovaná][sap-ha-guide-figure-3045]

  _**Obrázek 55:** Správce clusteru převzetí služeb při selhání se zobrazí na disku, který DataKeeper replikovaná_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalace systému SAP NetWeaver

Nebude popisujeme nastavení DBMS vzhledem k tomu, že nastavení se liší v závislosti na tom, které můžete použít systém DBMS. Však předpokládáme, že aspekty vysoké dostupnosti se systémem DBMS se tak vyřeší, pomocí funkce různých výrobců DBMS podporu pro Azure. Například Always On nebo zrcadlení databáze pro SQL Server a Oracle Data Guard pro databáze Oracle. Ve scénáři, které používáme v tomto článku jsme neměli přidat další ochranu správce databáze.

Při interakci různých DBMS služeb s Tento druh Clusterované konfigurace SAP ASCS/SCS v Azure nejsou žádné zvláštní požadavky.

> [!NOTE]
> Postupy instalace systémů SAP NetWeaver ABAP, Java systémy a systémy ABAP + Java jsou téměř shodné. Nejdůležitější rozdíl je, že systém SAP ABAP má jednu instanci ASC. Systém SAP Java obsahuje jednu instanci SCS. Systém SAP ABAP + Java obsahuje jednu instanci ASCS a jednu instanci SCS spuštěné ve stejné skupině clusteru převzetí služeb při selhání Microsoft. Případné rozdíly instalace pro každý zásobník instalace SAP NetWeaver jsou explicitně uvedena. Můžete předpokládat, že všechny ostatní části jsou stejné.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalace SAP s ASCS/SCS instancí vysoké dostupnosti

> [!IMPORTANT]
> Nezapomeňte toto stránkovacím souboru na svazcích DataKeeper zrcadlení. DataKeeper nepodporuje zrcadlených svazků. Můžete nechat stránkovacím souboru na dočasné jednotce D virtuálního počítače Azure, což je výchozí hodnota. Pokud není již existuje, přesuňte stránkovací soubor Windows na jednotce D vašeho virtuálního počítače Azure.
>
>

Instalace SAP s ASCS/SCS instancí vysoké dostupnosti zahrnuje tyto úlohy:

- Vytváří se virtuální hostitel název Clusterové instance SAP ASCS/SCS
- Instalace SAP prvním uzlu clusteru
- Úprava profilu SAP ASCS/SCS instance
- Přidávání portu sondy
- Otevření portu sondy Windows firewall

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Vytvořte virtuální hostitel název Clusterové instance SAP ASCS/SCS

1.  Ve správci Windows DNS vytvořte položku DNS pro název virtuálního hostitele ASCS/SCS instance.

  > [!IMPORTANT]
  > IP adresa, která je přiřazena k názvu virtuální hostitel ASCS/SCS instance musí být stejné jako IP adresu, který jste přiřadili do Azure Load Balancer (**<*SID*> - lb - ascs**).  
  >
  >

  IP adresa virtuální název hostitele SAP ASCS/SCS (**pr1 ascs sap**) je stejná jako IP adresu nástroje pro vyrovnávání zatížení Azure (**pr1-lb-ascs**).

  ![Obrázek 56: Definujte položku DNS pro SAP ASCS/SCS virtuální název clusteru a adresa TCP/IP][sap-ha-guide-figure-3046]

  _**Obrázek 56:** Definujte položku DNS pro SAP ASCS/SCS virtuální název clusteru a adresa TCP/IP_

2.  K definování IP adresy přiřazené k názvu virtuální hostitele, vyberte **Správce DNS** > **domény**.

  ![Obrázek 57: Nový virtuální název a adresu protokolu TCP/IP pro SAP ASCS/SCS konfigurace clusteru][sap-ha-guide-figure-3047]

  _**Obrázek 57:** Nový virtuální název a adresu protokolu TCP/IP pro SAP ASCS/SCS konfigurace clusteru_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalace SAP prvním uzlu clusteru

1.  Spuštění první možnost uzlu clusteru v uzlu clusteru A. Třeba na **pr1-ascs-0** hostitele.
2.  Ponechat výchozí porty Azure interního nástroje load balancer, vyberte:

  * **Systém ABAP**: **ASCS** instance číslo **00**
  * **Java systému**: **SCS** instance číslo **01**
  * **Systém ABAP + Java**: **ASCS** instance číslo **00** a **SCS** instance číslo **01**

  Pro účely čísla instance než 00 instance ABAP ASCS a 01 pro instanci Java SCS, nejprve potřebujete změnit pravidla, podle Vyrovnávání zatížení výchozí Azure interního nástroje pro vyrovnávání [změnit pravidla pro vyrovnávání zatížení výchozí ASC/SCS Nástroje pro vyrovnávání zatížení Azure interní][sap-ha-guide-8.9].

Další několik úloh nejsou popsané v dokumentaci k instalaci standardní SAP.

> [!NOTE]
> Dokumentaci k instalaci SAP popisuje postup instalace prvního uzlu clusteru ASC/SCS.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Upravit profil SAP ASCS/SCS instance

Musíte přidat nový parametr profilu. Parametr profilu brání připojení mezi SAP pracovní procesy a server zařazování zavření po nečinnosti příliš dlouho. Už jsme zmínili scénář v [přidat položky registru v obou uzlů clusteru z instance SAP ASCS/SCS][sap-ha-guide-8.11]. V této části taky zavedli jsme dvě změny některých základních parametry připojení TCP/IP. V druhém kroku, je nutné nastavit server zařadit do fronty k odeslání `keep_alive` signálu tak, aby připojení není spuštění nástroje pro vyrovnávání zatížení Azure vnitřní limit nečinnosti.

Upravit profil SAP ASCS/SCS instance:

1.  Tento parametr profil přidáte do profilu instance SAP ASCS/SCS:

  ```
  enque/encni/set_so_keepalive = true
  ```
  V našem příkladu je cesta:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Například na SAP SCS instance profilu a odpovídajících cestu:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Aby se změny projevily, restartujte instanci SAP ASCS /SCS.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Přidejte port testu

Pomocí funkce interní služby load balancer sondy usnadňují konfiguraci celý cluster, pracovat s Azure Load Balancer. Nástroje pro vyrovnávání zatížení Azure interní obvykle distribuuje příchozí zatížení rovnoměrně mezi zúčastněných virtuálních počítačů. Ale tato funkce nebude pracovat v některých konfiguracích clusteru vzhledem k tomu, že je aktivní pouze jedna instance. Druhá instance je pasivní činnost a nemůže přijmout zatížení. Funkce testu pomáhá při nástroje pro vyrovnávání zatížení Azure interní přiřadí pracovní jenom aktivní instanci. S funkcemi testu interní služby load balancer může zjistit, která instance jsou aktivní a pak zaměřit jenom instance s úlohou.

Chcete-li přidat port testu:

1.  Zkontrolujte aktuální **ProbePort** nastavení spuštěním následujícího příkazu Powershellu. Spusťte ho v jednom z virtuálních počítačů v konfiguraci clusteru.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

2.  Definování portu sondy. Výchozí číslo portu sondy je **0**. V našem příkladu používáme portu sondy **62000**.

  ![Obrázek 58: Port testu konfigurace clusteru je 0 ve výchozím nastavení][sap-ha-guide-figure-3048]

  _**Obrázek 58:** Výchozí port sondy konfigurace clusteru je 0_

  Číslo portu je definován v šablonách SAP Azure Resource Manageru. Číslo portu v prostředí PowerShell můžete přiřadit.

  Chcete-li nastavit novou hodnotu ProbePort **SAP <*SID*> IP** prostředek clusteru, spusťte následující skript prostředí PowerShell. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí. Po spuštění skriptu budete vyzváni k restartování skupiny clusteru SAP k aktivaci změny.

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

  Po přepnutí **SAP <*SID*>** Clusterové role online, ověřte, že **ProbePort** je nastavena na novou hodnotu.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```

  ![Obrázek 59: Po nastavení je nová hodnota pro zjišťování port clusteru][sap-ha-guide-figure-3049]

  _**Obrázek 59:** Po nastavení je nová hodnota pro zjišťování port clusteru_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Otevření portu sondy Windows firewall

Budete muset otevřít port testu Windows firewall na oba uzly clusteru. Pomocí následujícího skriptu pro otevření portu sondy Windows firewall. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** je nastavena na **62000**. Nyní můžete přístup ke sdílené složce  **\\\ascsha-clsap\sapmnt** od ostatních hostitelů, například jako z **ascsha specializující**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Nainstalujte instanci databáze

Pokud chcete nainstalovat instanci databáze, postupujte podle procesu popsaného v dokumentaci k instalaci SAP.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Nainstalujte druhém uzlu clusteru

Pokud chcete nainstalovat druhým clusterem výpočetní cluster, postupujte podle kroků v Průvodci instalací SAP.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Změnit typ spuštění instance služby Windows Lajících SAP

Změnit typ spuštění služby Windows Lajících SAP **automaticky (zpožděné spuštění)** na oba uzly clusteru.

![Obrázek 60: Změnit typ služby pro instanci SAP Lajících na hodnotu zpožděného automaticky][sap-ha-guide-figure-3050]

_**Obrázek 60:** Změnit typ služby pro instanci SAP Lajících na hodnotu zpožděného automaticky_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Nainstalovat primární aplikační Server SAP

Instalace serveru primární aplikace (Pa adresy) instance <*SID*> - di - 0 na virtuálním počítači, který jste určili pro hostování Pa ADRESAMI. Neexistují žádné závislosti na Azure nebo DataKeeper konkrétní nastavení.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Nainstalovat další aplikační Server SAP

Instalace SAP Server pro další aplikace (AAS) u všech virtuálních počítačů, které jste označili jako hostitel instance aplikační Server SAP. Třeba na <*SID*> - di - 1 pro <*SID*> - di -&lt;n&gt;.

> [!NOTE]
> Tím se dokončí instalace systému SAP NetWeaver vysokou dostupnost. V dalším kroku pokračujte v testování převzetí služeb při selhání.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testovací převzetí služeb při selhání SAP ASCS/SCS instance a SIOS replikace
Je snadno testujte a monitorujte SAP ASCS/SCS instance převzetí služeb při selhání a SIOS replikace disku pomocí Správce clusteru převzetí služeb při selhání a SIOS DataKeeper správu a konfigurační nástroje.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> V uzlu clusteru A je spuštěna instance SAP ASCS/SCS

**SAP PR1** skupina clusteru běží na uzlu clusteru A. Třeba na **pr1-ascs-0**. Přiřadit sdílené diskové jednotce S, která je součástí sady **SAP PR1** skupina clusteru a používající instanci ASCS/SCS, A. uzlu v clusteru

![Obrázek 61: Správce clusteru převzetí služeb při selhání: Skupina clusteru SAP < SID > běží na uzlu clusteru A][sap-ha-guide-figure-5000]

_**Obrázek 61:** Správce clusteru převzetí služeb při selhání: SAP <*SID*> Skupina clusteru běží na uzlu clusteru A_

V nástroji SIOS DataKeeper Správa a konfigurace uvidíte, že data sdíleného disku je synchronně replikovaných z disku zdrojového svazku S na uzlu clusteru A na jednotku cílový svazek S na uzlu clusteru B. Například se replikují z **pr1 ascs 0 [10.0.0.40]** k **pr1-ascs-1 [10.0.0.41]**.

![62 obrázek: V SIOS DataKeeper replikujte místní svazek z uzlu clusteru A k uzlům clusteru B][sap-ha-guide-figure-5001]

_**62 obrázek:** V SIOS DataKeeper replikujte místní svazek z uzlu clusteru A k uzlům clusteru B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Převzetí služeb při selhání z uzlu A uzel B

1.  Vyberte jednu z těchto možností k zahájení převzetí služeb při selhání SAP <*SID*> Skupina clusteru z uzlu clusteru A k uzlu clusteru B:
  - Pomocí Správce clusteru převzetí služeb při selhání  
  - Použití prostředí PowerShell pro Cluster převzetí služeb při selhání

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Restartovat A uzlu clusteru v rámci hostovaného operačního systému Windows (tím se spustí automatické převzetí služeb při selhání SAP <*SID*> Skupina clusteru z uzlu A uzel B).  
3.  Restartovat uzel A cluster na webu Azure Portal (tím se spustí automatické převzetí služeb při selhání SAP <*SID*> Skupina clusteru z uzlu A uzel B).  
4.  Restartovat A uzlu clusteru pomocí prostředí Azure PowerShell (tím se spustí automatické převzetí služeb při selhání SAP <*SID*> Skupina clusteru z uzlu A uzel B).

  Po převzetí služeb při selhání systému SAP <*SID*> Skupina clusteru běží na uzlu clusteru B. Například je spuštěn na **pr1-ascs-1**.

  ![Obrázek 63: V modulu Správce clusteru převzetí služeb při selhání skupiny SAP < SID > clusteru běží na uzlu clusteru B][sap-ha-guide-figure-5002]

  _**Obrázek 63**: V převzetí služeb při selhání modulu Správce clusteru systému SAP <*SID*> Skupina clusteru běží na uzlu clusteru B_

  Sdílený disk je teď připojená na clusteru uzel B. SIOS DataKeeper je replikace dat ze zdrojového svazku jednotky S na uzlu clusteru B do cílového svazku jednotky S na uzlu clusteru A. Například je replikace z **pr1-ascs-1 [10.0.0.41]** k **pr1 ascs 0 [10.0.0.40]**.

  ![Obrázek 64: SIOS DataKeeper replikuje místní svazek z uzlu clusteru B A uzlu v clusteru][sap-ha-guide-figure-5003]

  _**Obrázek 64:** SIOS DataKeeper replikuje místní svazek z uzlu clusteru B A uzlu v clusteru_
