---
title: 'SAP v Azure: Průvodce plánováním a implementací'
description: Plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver
author: MSSedusch
manager: juergent
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3050d0c61b6278b32b8e9272f228a863c9a0a244
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89458684"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver

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
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
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

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines/windows/index.yml
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#format-and-mount-the-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
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
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image



Microsoft Azure umožňuje společnostem získávat výpočetní prostředky a prostředky úložiště v minimální době bez zdlouhavých cyklů zásobování. Služba Azure Virtual Machine umožňuje společnostem nasazovat klasické aplikace, jako jsou aplikace založené na SAP NetWeaver, do Azure a rozšířit jejich spolehlivost a dostupnost bez dalších dostupných prostředků v místním prostředí. Služby virtuálních počítačů Azure taky podporují připojení mezi různými místy, což umožňuje společnostem aktivně integrovat Azure Virtual Machines do jejich místních domén, jejich privátních cloudů a jejich systému SAP na šířku.
Tento dokument white paper popisuje základy Microsoft Azure virtuálních počítačů a poskytuje podrobné pokyny k plánování a implementaci pro instalace SAP NetWeaver v Azure a jako takový by měl být dokument, který se má přečíst před zahájením aktuálních nasazení SAP NetWeaver v Azure.
Papír doplňuje dokumentaci k instalaci SAP a poznámky SAP, které reprezentují primární prostředky pro instalace a nasazení softwaru SAP na daných platformách.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Shrnutí
Cloud Computing je široce využívaný termín, který v rámci IT oboru získá více a větší důležitost, od malých společností až po velké a nadnárodní společnosti.

Microsoft Azure je Cloud Services platforma od Microsoftu, která nabízí rozsáhlou škálu nových možností. Zákazníci teď můžou rychle zřídit a zrušit zřízení aplikací jako služby v cloudu, takže nejsou omezené na technické nebo rozpočtové omezení. Namísto investování času a rozpočtu do hardwarové infrastruktury se mohou společnosti soustředit na aplikace, obchodní procesy a její výhody pro zákazníky a uživatele.

Pomocí služeb Microsoft Azure pro virtuální počítače nabízí společnost Microsoft komplexní platformu infrastruktury jako služby (IaaS). Služba Azure Virtual Machines teď v rámci IaaS podporuje aplikace využívající SAP NetWeaver. Tento dokument white paper popisuje, jak plánovat a implementovat aplikace založené na SAP NetWeaver v rámci Microsoft Azure jako platforma, kterou si zvolíte.

Samotný dokument se zaměřuje na dvě hlavní aspekty:

* První část popisuje dva podporované vzory nasazení pro aplikace SAP NetWeaver založené na Azure. Popisuje také obecné zpracování Azure s nasazeními SAP.
* Druhá část podrobně popisuje implementace různých scénářů popsaných v první části.

Další zdroje najdete v tématu [prostředky][planning-guide-1.2] kapitoly v tomto dokumentu.

### <a name="definitions-upfront"></a>Definice dopředu
V celém dokumentu používáme následující výrazy:

* IaaS: infrastruktura jako služba
* PaaS: platforma jako služba
* SaaS: software jako služba
* Součást SAP: samostatná aplikace SAP, například ECC, ČERNOBÍLé, správce řešení nebo S/4HANA.  Komponenty SAP můžou být založené na tradičních technologiích ABAP a Java nebo na NetWeaver aplikaci, jako jsou třeba obchodní objekty.
* Prostředí SAP: jedna nebo víc komponent SAP se logicky seskupují tak, aby prováděly obchodní funkce, jako je vývoj, QAS, školení, DR nebo produkce.
* SAP na šířku: Tento pojem odkazuje na celé prostředky SAP v zákaznickém oddělení IT. SAP na šířku zahrnuje všechna produkční a neprodukční prostředí.
* Systém SAP: kombinace vrstvy systému DBMS a vrstvy aplikace, například vývojového systému SAP ERP, SAP BW testovacího systému, SAP CRM produkčního systému atd. V nasazeních Azure není podpora rozdělují tyto dvě vrstvy mezi místními a Azure. Znamená, že systém SAP je buď nasazený místně, nebo je nasazený v Azure. Můžete ale nasadit různé systémy služby SAP na šířku do Azure nebo místního prostředí. Můžete například nasadit vývojové a testovací systémy SAP CRM v Azure, ale produkční systém SAP CRM v místním prostředí.
* Propojení mezi různými místy: popisuje situaci, kdy se virtuální počítače nasazují do předplatného Azure, které má připojení typu Site-to-site, Multi-Site nebo ExpressRoute, mezi místními datacentra a Azure. V běžné dokumentaci k Azure jsou tyto typy nasazení popsány také jako mezi různými místními nebo hybridními scénáři. Důvodem připojení je rozšiřování místních domén, místní služby Active Directory/OpenLDAP a místní DNS do Azure. Místní orientace se rozšiřuje na prostředky v rámci předplatného Azure. S tímto rozšířením můžou být virtuální počítače součástí místní domény. Uživatelé domény v místní doméně mají přístup k serverům a můžou na těchto virtuálních počítačích spouštět služby (například služby DBMS). Je možné komunikovat a překlad názvů mezi virtuálními počítači nasazenými místně a virtuálními počítači nasazenými v Azure. Toto je nejběžnější a skoro exkluzivní případ nasazení prostředků SAP do Azure. Další informace najdete v [tomto][vpn-gateway-cross-premises-options] [článku a v tomto článku][vpn-gateway-site-to-site-create].
* Rozšíření monitorování Azure, rozšířené monitorování a rozšíření Azure pro SAP: popisují jednu a stejnou položku. Popisuje rozšíření virtuálního počítače, které je potřeba nasadit, abyste mohli poskytovat základní data o infrastruktuře Azure pro agenta hostitele SAP. Poznámky SAP v SAP se můžou odkazovat jako na rozšíření monitorování nebo rozšířené monitorování. V Azure odkazujeme na ni jako na **rozšíření Azure pro SAP**.

> [!NOTE]
> Mezi místní nebo hybridní nasazení systémů SAP, kde je Azure Virtual Machines běžící systémy SAP, jsou pro produkční systémy SAP podporovány členy místní domény. Mezi místními nebo hybridními konfiguracemi se podporuje nasazení částí nebo kompletní řešení SAP v Azure do Azure. I když v Azure běží kompletní prostředí SAP na pracovišti, je potřeba mít tyto virtuální počítače v rámci místní domény a reklamy/OpenLDAP.
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Zdroje a prostředky
V dokumentaci k vstupnímu bodu pro úlohu SAP v Azure najdete [tady](./get-started.md). Počínaje tímto vstupním bodem najdete mnoho článků týkajících se těchto témat:

- SAP NetWeaver a Business One v Azure
- Příručky SAP DBMS pro různé systémy DBMS v Azure
- Vysoká dostupnost a zotavení po havárii pro úlohy SAP v Azure
- Konkrétní pokyny pro provozování SAP HANA v Azure
- Doprovodné materiály týkající se velkých instancí Azure HANA pro SAP HANA DBMS


> [!IMPORTANT]
> Všude, kde je možné použít odkaz na pokyny k instalaci SAP nebo jinou dokumentaci SAP (Reference InstGuide-01, viz <http://service.sap.com/instguides> ). V případě požadavků, procesu instalace nebo podrobností konkrétních funkcí SAP by měla být dokumentace a příručky SAP vždy čtena, protože dokumenty společnosti Microsoft se vztahují pouze na konkrétní úlohy pro software SAP nainstalovaný a provozovaný ve Microsoft Azurem virtuálním počítači.
>
>

Následující poznámky SAP souvisejí s tématem SAP v Azure:

| Číslo poznámky | Nadpis |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: podporované produkty a velikost |
| [2015553] |SAP v Microsoft Azure: požadavky na podporu |
| [1999351] |Řešení potíží s vylepšeným monitorováním Azure pro SAP |
| [2178632] |Klíčové metriky monitorování pro SAP v Microsoft Azure |
| [1409604] |Virtualizace ve Windows: rozšířené monitorování |
| [2191498] |SAP v systému Linux s Azure: rozšířené monitorování |
| [2243692] |Virtuální počítač se systémem Linux na Microsoft Azure (IaaS): problémy s licencí SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: poznámky k instalaci |
| [2002167] |Red Hat Enterprise Linux 7. x: instalace a upgrade |
| [2069760] |Instalace a upgrade systému Oracle Linux 7. x SAP |
| [1597355] |Doporučení pro zaměněné místo pro Linux |

Přečtěte si také [SCN wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) , který obsahuje všechny poznámky SAP pro Linux.

Obecná výchozí omezení a maximální omezení předplatných Azure najdete v [tomto článku][azure-resource-manager/management/azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Možné scénáře
SAP se často zobrazuje jako jedna z nejdůležitějších aplikací v podnicích. Architektura a operace těchto aplikací jsou většinou složité a je důležité zajistit, aby byly splněny požadavky na dostupnost a výkon.

Proto podniky musí pečlivě zvážit, který poskytovatel cloudu si zvolí pro provozování těchto důležitých podnikových procesů. Azure je ideální veřejná cloudová platforma pro důležité podnikové aplikace a obchodní procesy SAP. Téměř všechny stávající systémy SAP NetWeaver a S/4HANA je možné v Azure ještě dnes hostovat v rámci celé řady infrastruktury Azure. Azure poskytuje virtuální počítače s mnoha terabajty paměti a více než 200 procesorů. Mimo to, že Azure nabízí [velké instance Hana](./hana-overview-architecture.md), což umožňuje nasazení Hana s možností horizontálního navýšení kapacity až na 24 tb a SAP HANA nasazení se škálováním na více instancí až na 120 TB. Jedna může dnes zastavovat, že téměř všechny místní scénáře SAP lze spustit také v Azure.

Hrubý popis scénářů a některých nepodporovaných scénářů najdete v tématu věnovaném [scénářům dokumentace SAP na virtuálních počítačích Azure](./sap-planning-supported-configurations.md), které se podporují.

Tyto scénáře a některé z podmínek, které se jmenovaly jako nepodporované, najdete v odkazované dokumentaci v průběhu plánování a vývoje vaší architektury, kterou chcete nasadit do Azure.

Celkový společný vzor nasazení je scénář pro více míst, jako je zobrazený

![SÍŤ VPN s připojením typu Site-to-Site (mezi místními sítěmi)][planning-guide-figure-300]

Důvodem pro mnoho zákazníků, jak použít model nasazení mezi různými místy, je to, že je nejdůležitější pro všechny aplikace, aby se do Azure rozšířily místně do Azure pomocí Azure ExpressRoute a považovaly Azure za virtuální datové centrum. Vzhledem k přesunu dalších prostředků do Azure se zvýší infrastruktura nasazená v Azure a síťová infrastruktura a místní prostředky se tím sníží. Vše transparentní pro uživatele a aplikace.

Aby bylo možné úspěšně nasadit systémy SAP do Azure IaaS nebo IaaS, je důležité porozumět významným rozdílům mezi nabídkami tradičních outsourcingů nebo hostitelů a IaaS nabídkami. Vzhledem k tomu, že tradiční hostitel nebo modul pro odsourceky připraví infrastrukturu (síť, úložiště a typ serveru) na zatížení, které chce zákazník hostovat, je místo toho úkolem zákazníka nebo partnera, aby charakterizoval zatížení a zvolili správné komponenty Azure pro nasazení virtuálních počítačů, úložišť a sítě pro nasazení IaaS.

Aby bylo možné shromažďovat data pro plánování nasazení do Azure, je důležité:

- Vyhodnotit, které produkty SAP se podporují na virtuálních počítačích Azure
- Vyhodnoťte, jaké konkrétní verze operačních systémů jsou pro tyto produkty SAP podporované konkrétními virtuálními počítači Azure
- Vyhodnocení podporovaných verzí systému DBMS pro produkty SAP s konkrétními virtuálními počítači Azure
- Vyhodnoťte, jestli některé z požadovaných verzí operačního systému/DBMS vyžadují, abyste mohli provést podporu pro vydání SAP, upgrade balíčku a upgrady jádra, aby se získala podporovaná konfigurace.
- Vyhodnoťte, jestli potřebujete přesunout do různých operačních systémů, abyste mohli nasadit v Azure.

Podrobnosti o podporovaných součástech SAP v Azure, podporované jednotky infrastruktury Azure a související verze operačních systémů a vydání DBMS jsou vysvětleny v článku o [tom, jaký software SAP je podporován pro nasazení Azure](./sap-supported-product-on-azure.md). Výsledky získané při vyhodnocování platných vydání SAP, operačních systémů a systémů DBMS mají velký dopad na úsilí o přesun systémů SAP do Azure. Výsledkem tohoto vyhodnocení je definování toho, jestli v případech, kdy je potřeba upgradované verze SAP nebo změny operačních systémů, mohlo dojít k výraznému přípravnému úsilí.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Oblasti Azure
Služby Azure od Microsoftu se shromažďují v oblastech Azure. Oblast Azure je jedna nebo kolekce z datových center, která obsahují hardware a infrastrukturu, která běží a hostuje různé služby Azure. Tato infrastruktura zahrnuje velký počet uzlů, které fungují jako výpočetní uzly nebo uzly úložiště, nebo spouštějí síťové funkce.

Seznam různých oblastí Azure najdete v tématu geografické oblasti [Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Ne všechny oblasti Azure nabízejí stejné služby. Závisí na produktu SAP, který chcete spustit, a na operačním systému a DBMS, které se k němu vztahují. můžete tak skončit v situaci, kdy určitá oblast nenabízí požadované typy virtuálních počítačů. To platí zejména pro spuštěné SAP HANA, kde obvykle potřebujete virtuální počítače řady virtuálních počítačů M/Mv2. Tyto rodiny virtuálních počítačů se nasazují jenom v podmnožině oblastí. Můžete zjistit, co přesně přesný virtuální počítač, typy, typy úložiště Azure nebo jiné služby Azure jsou k dispozici v rámci kterých oblastí s technickou [dostupností produktů v jednotlivých](https://azure.microsoft.com/global-infrastructure/services/)oblastech. Při zahájení plánování a splnění určitých oblastí jako primární oblasti a nakonec sekundární oblasti je třeba nejprve prozkoumat, zda jsou v těchto oblastech k dispozici nezbytné služby.

### <a name="availability-zones"></a>Zóny dostupnosti
Některé oblasti Azure implementovaly koncept nazvaný Zóny dostupnosti. Zóny dostupnosti jsou fyzicky samostatná umístění v oblasti Azure. Každou zónu dostupnosti tvoří jedno nebo několik datových center vybavených nezávislým napájením, chlazením a sítí. Například nasazení dvou virtuálních počítačů do dvou Zóny dostupnosti Azure a implementace architektury s vysokou dostupností pro váš systém SAP DBMS nebo centrální služby SAP vám nabízí nejlepší smlouvu SLA v Azure. Pro tuto konkrétní smlouvu SLA pro virtuální počítač v Azure se podívejte na nejnovější verzi [SLA virtuálního počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Vzhledem k tomu, že se oblasti Azure vyvinuly a v posledních letech rychle rozšířily, topologie oblastí Azure, počet fyzických Datacenter, vzdálenost mezi těmito datacentry a vzdálenost mezi Zóny dostupnosti Azure se může lišit. A s tím, že latence sítě.

Princip Zóny dostupnosti se nevztahuje na službu HANA [velkých instancí Hana](./hana-overview-architecture.md). Smlouvy o úrovni služeb pro velké instance HANA najdete v článku [o smlouvě SLA pro SAP HANA ve velkých instancích Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/)


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domény selhání
Domény selhání reprezentují fyzickou jednotku selhání, úzce souvisí s fyzickou infrastrukturou obsaženou v datových centrech, zatímco fyzické okno nebo stojan se dá považovat za doménu selhání, protože mezi nimi není žádné přímé mapování 1:1.

Když nasadíte více Virtual Machines jako součást jednoho systému SAP v Microsoft Azure služeb virtuálních počítačů, můžete ovlivnit kontroler prostředků infrastruktury Azure za účelem nasazení vaší aplikace do různých domén selhání, a tím i splnění vyšších požadavků SLA dostupnosti. Ale distribuce domén selhání přes jednotku škálování Azure (kolekce stovek výpočetních uzlů nebo uzlů úložiště a sítě) nebo přiřazení virtuálních počítačů do konkrétní domény selhání je něco, co nemusíte mít přímý ovládací prvek. Aby bylo možné nasměrovat řadič Azure Fabric tak, aby nasadil sadu virtuálních počítačů do různých domén selhání, musíte k virtuálním počítačům přiřadit skupinu dostupnosti Azure v době nasazení. Další informace o skupinách dostupnosti Azure najdete v části [skupiny dostupnosti Azure][planning-guide-3.2.3] v tomto dokumentu.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Upgradovat domény
Upgradovací domény reprezentují logickou jednotku, která pomáhá určit, jak se aktualizuje virtuální počítač v rámci systému SAP, který se skládá z instancí SAP spuštěných ve více virtuálních počítačích. Když dojde k upgradu, Microsoft Azure projde procesem aktualizace těchto domén upgradu o jednu po jednom. Rozšíříte-li virtuální počítače v době nasazení v různých upgradovacích doménách, můžete chránit systém SAP částečně před potenciálním výpadkem. Aby mohl Azure nasazovat virtuální počítače systému SAP v různých upgradovacích doménách, je potřeba nastavit konkrétní atribut v době nasazení každého virtuálního počítače. Podobně jako u domén selhání je jednotka škálování Azure rozdělená do několika upgradovacích domén. Aby bylo možné nasměrovat řadič Azure Fabric na nasazení sady virtuálních počítačů v různých upgradovacích doménách, musíte k virtuálním počítačům v době nasazení přiřadit skupinu dostupnosti Azure. Další informace o skupinách dostupnosti Azure najdete níže v kapitole věnovaném [sadám dostupnosti Azure][planning-guide-3.2.3] .


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Skupiny dostupnosti Azure
Azure Virtual Machines v rámci jedné skupiny dostupnosti Azure distribuuje kontroler prostředků infrastruktury Azure v různých doménách selhání a upgradu. Účelem distribuce v různých doménách selhání a upgradu je zabránit vypnutí všech virtuálních počítačů systému SAP v případě údržby infrastruktury nebo selhání v rámci jedné domény selhání. Ve výchozím nastavení nejsou virtuální počítače součástí skupiny dostupnosti. Účast na virtuálním počítači ve skupině dostupnosti je definována v okamžiku nasazení nebo později v důsledku opětovné konfigurace a opětovného nasazení virtuálního počítače.

Další informace o konceptu skupin dostupnosti Azure a způsobu, jakým se skupiny dostupnosti vztahují k doménám selhání a upgradu, najdete v [tomto článku][virtual-machines-manage-availability].

Při definování skupin dostupnosti a pokusu o kombinaci různých virtuálních počítačů různých rodin virtuálních počítačů v rámci jedné skupiny dostupnosti dojde k problémům, které vám zabrání v zahrnutí určitého typu virtuálního počítače do této skupiny dostupnosti. Důvodem je, že skupina dostupnosti je svázána s jednotkou škálování, která obsahuje určitý typ výpočetních hostitelů. A určitý typ výpočetního hostitele může spouštět jenom určité typy rodin virtuálních počítačů. Pokud třeba vytvoříte skupinu dostupnosti a nasadíte první virtuální počítač do skupiny dostupnosti a zvolíte typ virtuálního počítače Esv3 a potom se pokusíte nasadit jako druhý virtuální počítač pro virtuální počítač řady M, bude se vám v druhém přidělení zamítnout. Důvodem je, že virtuální počítače Esv3 Family neběží na stejném hostitelském hardwaru jako virtuální počítače řady M. K tomuto problému může dojít při pokusu o změnu velikosti virtuálních počítačů a pokus o přesun virtuálního počítače z rodiny Esv3 na typ virtuálního počítače řady M. V případě změny velikosti na rodinu virtuálních počítačů, které se nedají hostovat na stejném hostitelském hardwaru, musíte vypnout všechny virtuální počítače ve skupině dostupnosti a změnit jejich velikost, aby bylo možné je spustit na jiném typu hostitelského počítače. SLA virtuálních počítačů, které jsou nasazené v rámci skupiny dostupnosti, najdete v článku [SLA virtuálního počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Zásady skupiny dostupnosti a související aktualizace a doména selhání se nevztahují na službu HANA pro [velké instance služby Hana](./hana-overview-architecture.md). Smlouvy o úrovni služeb pro velké instance HANA najdete v článku věnovaném [smlouvě SLA pro SAP HANA ve velkých instancích Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

> [!IMPORTANT]
> Pojmy Zóny dostupnosti Azure a sady dostupnosti Azure se vzájemně vylučují. To znamená, že můžete nasadit pár nebo několik virtuálních počítačů do konkrétní zóny dostupnosti nebo do skupiny dostupnosti Azure. Ale ne obojí.

### <a name="azure-paired-regions"></a>Spárované oblasti Azure
Azure nabízí páry oblastí Azure, kde je povolená replikace určitých dat mezi těmito páry pevné oblasti. Párování oblastí je popsáno v článku [provozní kontinuita a zotavení po havárii (BCDR): spárované oblasti Azure](../../../best-practices-availability-paired-regions.md). Jak je popsáno v článku, replikace dat je vázaná na typy úložiště Azure, které se dají nakonfigurovat k replikaci do spárované oblasti. Další informace najdete [v článku redundance úložiště v sekundární oblasti](../../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region). Typy úložišť, které povolují takovou replikaci, jsou typy úložiště, které nejsou vhodné pro úlohy DBMS. Vzhledem k tomu, že použitelnost replikace úložiště Azure je omezená na úložiště objektů BLOB v Azure (například pro účely zálohování) nebo jiné scénáře úložiště s vysokou latencí. Při kontrole spárovaných oblastí a služeb, které chcete použít jako primární nebo sekundární oblast, se může stát, že v spárované oblasti nejsou k dispozici typy služeb Azure a/nebo virtuálních počítačů, které chcete použít ve vaší primární oblasti. Případně se můžete setkat s situací, kdy spárované oblasti Azure není přijatelné z důvodů dodržování předpisů. V takových případech je nutné použít nepárové oblasti jako sekundární nebo oblast zotavení po havárii. V takovém případě se budete muset postarat o replikaci některých částí dat, která by Azure replikoval sami. Příklad replikace služby Active Directory a DNS do vaší oblasti zotavení po havárii je popsán v článku [Nastavení zotavení po havárii pro službu Active Directory a DNS](../../../site-recovery/site-recovery-active-directory.md) .


## <a name="azure-virtual-machine-services"></a>Služby virtuálních počítačů Azure
Azure nabízí širokou škálu virtuálních počítačů, které můžete vybrat k nasazení. Nemusíte mít k dispozici žádné další technologie a nákupy infrastruktury. Nabídka služby virtuálního počítače Azure zjednodušuje údržbu a provozní aplikace tím, že poskytuje výpočetní prostředky a úložiště na vyžádání pro hostování, škálování a správu webových aplikací a připojených aplikací. Správa infrastruktury je automatizovaná s platformou, která je navržená pro vysokou dostupnost a dynamické škálování, aby odpovídala potřebám využití s možností několika různých cenových modelů.

![Umístění služeb Microsoft Azure virtuálních počítačů][planning-guide-figure-400]

S virtuálními počítači Azure vám Microsoft umožňuje nasazovat vlastní serverové image do Azure jako instance IaaS. Nebo si z Galerie imagí Azure můžete vybrat z bohatě vydaných imagí operačních systémů s možnostmi, které jsou k dispozici.

Z provozní perspektivy služba virtuálního počítače Azure nabízí podobné prostředí jako virtuální počítače nasazené místně. Zodpovídáte za správu, operace a také opravy konkrétního operačního systému, který běží na virtuálním počítači Azure a jeho aplikacích v daném virtuálním počítači. Společnost Microsoft neposkytuje žádné další služby nad rámec hostování tohoto virtuálního počítače v infrastruktuře Azure (infrastruktura jako služba – IaaS). Pro úlohy SAP, které jste jako nasazení zákazníka, nemá společnost Microsoft žádné nabídky mimo nabídky IaaS.

Microsoft Azure platforma je víceklientské platforma. V důsledku toho jsou úložiště, sítě a výpočetní prostředky, které hostují virtuální počítače Azure, s několika výjimkami sdíleny mezi klienty. Inteligentní omezení a logika kvót se používají k tomu, aby jeden tenant neovlivnil výkon jiného tenanta (s vysokou úrovní jednosměrového souseda) drasticky. Zejména pro certifikaci platformy Azure pro SAP HANA musí společnost Microsoft prokázat izolaci prostředků v případech, kdy je možné pravidelně spouštět více virtuálních počítačů na stejném hostiteli s SAP. I když se logika v Azure snaží zachovat odchylky v případě malých, vysoce sdílených platforem, což má za následek větší odchylky při dostupnosti prostředků nebo šířky pásma, než se zákazníci můžou setkat s jejich místními nasazeními. Pravděpodobnost, že systém SAP v Azure může vyskytnout větší odchylky než v místním systému, je potřeba vzít v úvahu.

### <a name="azure-virtual-machines-for-sap-workload"></a>Virtuální počítače Azure pro úlohu SAP

Pro úlohy SAP jsme výběr omezili na různé rodiny virtuálních počítačů, které jsou vhodné pro úlohy SAP a SAP HANA úlohy. Způsob, jakým najdete správný typ virtuálního počítače a jeho schopnost pracovat prostřednictvím úlohy SAP, je popsán v dokumentu, [který software SAP podporuje pro nasazení Azure](./sap-supported-product-on-azure.md).

> [!NOTE]
> Typy virtuálních počítačů, které jsou certifikované pro úlohy SAP, neexistují žádné prostředky procesoru a paměti při nadměrném zřizování.

Mimo výběr čistě podporovaných typů virtuálních počítačů musíte také ověřit, jestli jsou tyto typy virtuálních počítačů dostupné v konkrétní oblasti založené na [dostupných produktech v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Ale důležitější je, že je potřeba vyhodnotit, jestli:

- Prostředky procesoru a paměti různých typů virtuálních počítačů
- Šířka pásma IOPS různých typů virtuálních počítačů
- Síťové možnosti různých typů virtuálních počítačů
- Počet disků, které lze připojit
- Možnost využít určité typy úložiště Azure

podle potřeby. Většinu těchto dat najdete [tady (Linux)][virtual-machines-sizes-linux] a [tady (Windows)][virtual-machines-sizes-windows] pro konkrétní typ virtuálního počítače.

V případě cenového modelu máte několik různých cenových možností, jako jsou:

- Průběžné platby
- Rezervovaný jeden rok
- Rezervované tři roky
- Spotové ceny

Ceny každé z různých nabídek s různými nabídkami služeb v rámci operačních systémů a různých oblastí jsou k dispozici na webu [Linux Virtual Machines ceny](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows Virtual Machines ceny](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Podrobnosti a flexibilitu za jeden rok a tři roky rezervované instance najdete v těchto článcích:

- [Co jsou rezervace Azure?](../../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Flexibilita velikosti virtuálních počítačů s rezervovanými instancemi virtuálních počítačů](../../windows/reserved-vm-instance-size-flexibility.md)
- [Jak se na virtuální počítače uplatňuje sleva za rezervaci Azure](../../../cost-management-billing/manage/understand-vm-reservation-charges.md)

Další informace o cenách na místě najdete v článku [Virtual Machines na místě Azure](https://azure.microsoft.com/pricing/spot/). Ceny stejného typu virtuálního počítače se taky můžou lišit mezi různými oblastmi Azure. Pro některé zákazníky je vhodné provést nasazení do levnější oblasti Azure.

Azure navíc nabízí koncepty vyhrazeného hostitele. Koncept vyhrazeného hostitele nabízí větší kontrolu nad cykly oprav, které provádí Azure. Můžete si čas opravit podle vašich vlastních plánů. Tato nabídka je konkrétně zaměřená na zákazníky s úlohou, která nemusí následovat po běžném cyklu úlohy. Pokud si chcete přečíst koncepty nabídek vyhrazených hostitelů Azure, přečtěte si článek [vyhrazený hostitel Azure](../../windows/dedicated-hosts.md). Použití této nabídky je podporováno pro úlohy SAP a používá se několika zákazníky SAP, kteří chtějí mít větší kontrolu nad opravou infrastruktury a s případnými plány údržby společnosti Microsoft. Další informace o tom, jak Microsoft udržuje a opraví infrastrukturu Azure, která je hostitelem virtuálních počítačů, najdete [v článku údržba virtuálních počítačů v Azure](../../maintenance-and-updates.md).

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Generace 1 a generace 2 – virtuální počítače
Hypervisor Microsoftu dokáže zvládnout dvě různé generace virtuálních počítačů. Tyto formáty se nazývají **generace 1** a **generace 2**. **Generace 2** byla představena v roce 2012 s hypervisorem systému Windows Server 2012. Azure se spustil s použitím virtuálních počítačů 1. generace. Při nasazení virtuálních počítačů Azure stále používá výchozí formát 1. generace. Mezitím můžete nasadit i formáty virtuálních počítačů 2. generace. [Podpora virtuálních počítačů 2. generace v Azure](../../windows/generation-2.md) obsahuje seznam rodin virtuálních počítačů Azure, které se dají nasadit jako virtuální počítač 2. generace. Tento článek obsahuje taky důležité funkční rozdíly virtuálních počítačů generace 2, které můžou běžet na privátním cloudu Hyper-V a v Azure. Důležitější Tento článek také obsahuje seznam funkčních rozdílů mezi virtuálními počítači 1. generace a virtuálními počítači 2. generace, které jsou spuštěny v Azure.

> [!NOTE]
> Existují funkční rozdíly virtuálních počítačů generace 1 a generace 2 spuštěných v Azure. Přečtěte si článek  [Podpora virtuálních počítačů 2. generace v Azure, kde](../../windows/generation-2.md) najdete seznam těchto rozdílů.

Přesunutí stávajícího virtuálního počítače z jedné generace do druhé generace není možné. Pokud chcete změnit generaci virtuálního počítače, musíte nasadit nový virtuální počítač pro generaci, který si přejete, a znovu nainstalovat software, který používáte ve virtuálním počítači generace. Tato změna má vliv jenom na základní image virtuálního pevného disku virtuálního počítače a nemá žádný vliv na datové disky nebo připojené sdílené složky systému souborů NFS ani SMB. Datové disky, NFS nebo sdílené složky SMB, které byly původně přiřazeny, například na virtuálním počítači generace 1.

> [!NOTE]
> Nasazení virtuálních počítačů řady Mv1 VM jako virtuálních počítačů generace 2 je možné od 1. května 2020. To znamená, že zdá se, že a možnost mezi virtuálními počítači s více hodnotami Mv1 a Mv2.


### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Úložiště: Microsoft Azure Storage a datové disky
Microsoft Azure Virtual Machines využívá jiné typy úložišť. Při implementaci SAP na službách virtuálních počítačů Azure je důležité pochopit rozdíly mezi těmito dvěma hlavními typy úložiště:

* Netrvalé a stálé úložiště.
* Trvalé úložiště.

Virtuální počítače Azure po nasazení virtuálního počítače nabízejí netrvalé disky. V případě restartování virtuálního počítače se vymaže veškerý obsah na těchto jednotkách. Proto je vzhledem k tomu, že datové soubory a soubory protokolů a opětovného provedení databáze by měly být v žádném případě umístěny na těchto netrvalých jednotkách. Existují výjimky pro některé databáze, kde tyto netrvalé jednotky můžou být vhodné pro databáze tempdb a dočasné tabulkové prostory. Ale nepoužívejte tyto jednotky pro virtuální počítače řady A-Series, protože tyto jednotky, které nejsou trvalé, jsou v rámci této rodiny virtuálních počítačů omezené v propustnosti. Další podrobnosti najdete v článku [Principy dočasné jednotky na virtuálních počítačích s Windows v Azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines) .

---
> ![Windows][Logo_Windows] Windows
>
> D:\ jednotky na virtuálním počítači Azure je netrvalá jednotka, na které se zálohují některé místní disky na výpočetním uzlu Azure. Protože není trvale trvalé, znamená to, že všechny změny provedené v obsahu D:\ Při restartu virtuálního počítače dojde ke ztrátě jednotky. Podle "jakýchkoli změn", jako jsou uložené soubory, adresáře vytvořené, nainstalované aplikace atd.
>
> ![Linux][Logo_Linux] Linux
>
> Virtuální počítače se systémem Linux automaticky připojují jednotku na/mnt/Resource, která je netrvalou jednotkou zajištěnou místními disky na výpočetním uzlu Azure. Protože není trvale trvalé, znamená to, že všechny změny provedené v obsahu v/mnt/Resource budou při restartování virtuálního počítače ztraceny. O jakékoli změny, jako jsou uložené soubory, adresáře, které se nainstalují, aplikace atd.
>
>

#### <a name="azure-storage-accounts"></a>Účty úložiště Azure

Při nasazování služeb nebo virtuálních počítačů v Azure jsou nasazení VHD a image virtuálních počítačů uspořádány v jednotkách s názvem Azure Storage účty. [Účty úložiště Azure](../../../storage/common/storage-account-overview.md) mají omezení buď v IOPS, propustnosti, nebo velikosti, které může obsahovat. V předchozích omezeních, která jsou popsána v následujících případech:

- [Cíle škálovatelnosti pro účty úložiště úrovně Standard](../../../storage/common/scalability-targets-standard-account.md)
- [Cíle škálovatelnosti pro účty úložiště objektů blob stránky úrovně Premium](../../../storage/blobs/scalability-targets-premium-page-blobs.md)

hraje důležitou roli při plánování nasazení SAP v Azure. Bylo na vás, abyste mohli spravovat počet trvalých disků v rámci účtu úložiště. Je potřeba spravovat účty úložiště a nakonec vytvořit nové účty úložiště a vytvořit tak další trvalé disky.

V posledních letech se zavedení služby [Azure Managed disk](../../managed-disks-overview.md) z těchto úloh zbavuje. Doporučení pro nasazení SAP je využít Azure Managed disks místo správy účtů Azure Storage sami. Služba Azure Managed disks bude distribuovat disky napříč různými účty úložiště, takže nebudou překročeny limity jednotlivých účtů úložiště.

V rámci účtu úložiště máte typ konceptu s názvem Containers, který se dá použít k seskupení určitých disků do konkrétních kontejnerů.

V rámci Azure se název disku/virtuálního pevného disku řídí následujícím připojením k pojmenování, které musí v Azure zadat jedinečný název VHD:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

Výše uvedený řetězec musí jednoznačně identifikovat disk nebo virtuální pevný disk, který je uložený v Azure Storage.


#### <a name="azure-persisted-storage-types"></a>Typy trvalého úložiště Azure
Azure nabízí celou řadu možností trvalého úložiště, které se dají použít pro úlohy SAP a specifické komponenty zásobníku SAP. Další podrobnosti najdete v dokumentu  [Azure Storage pro úlohy SAP](./planning-guide-storage.md).


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure sítě

Microsoft Azure poskytuje síťovou infrastrukturu, která umožňuje mapování všech scénářů, které chceme realizovat se softwarem SAP. Možnosti:

* Přístup z vnějšku přímo k virtuálním počítačům prostřednictvím Terminálové služby systému Windows nebo SSH/VNC
* Přístup ke službám a konkrétním portům používaným aplikacemi v rámci virtuálních počítačů
* Interní komunikace a překlad názvů mezi skupinou virtuálních počítačů nasazených jako virtuální počítače Azure
* Propojení mezi místními sítěmi v místní síti zákazníka a síti Azure
* Propojení mezi lokalitami Azure mezi různými oblastmi Azure nebo datovým centrem

Další informace najdete tady: <https://azure.microsoft.com/documentation/services/virtual-network/>

Existuje mnoho různých možností konfigurace názvů a rozlišení IP v Azure. K dispozici je také služba Azure DNS, která se dá použít místo nastavování vlastního serveru DNS. Další informace najdete v [tomto článku][virtual-networks-manage-dns-in-vnet] a na [této stránce](https://azure.microsoft.com/services/dns/).

U různých místních nebo hybridních scénářů se spoléháme na skutečnost, že místní služba AD/OpenLDAP/DNS se rozšířila prostřednictvím sítě VPN nebo privátního připojení k Azure. V některých scénářích, jak je uvedeno zde, může být nutné mít v Azure nainstalovanou repliku AD/OpenLDAP.

Vzhledem k tomu, že je síť a překlad názvů důležitou součástí nasazení databáze pro systém SAP, tento koncept je podrobněji popsán v [Průvodci nasazením systému DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Virtuální sítě Azure

Vytvořením Virtual Network Azure můžete definovat rozsah adres privátních IP adres přidělených funkcemi Azure DHCP. Ve scénářích mezi místními lokalitami je definovaný rozsah IP adres stále přidělen pomocí protokolu DHCP v Azure. Překlad názvů domén se ale provádí místně (za předpokladu, že virtuální počítače jsou součástí místní domény), a proto může překládat adresy mimo jiné Cloud Services Azure.

Každý virtuální počítač v Azure musí být připojený k Virtual Network.

Další podrobnosti najdete v [tomto článku][resource-groups-networking] a na [této stránce](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Ve výchozím nastavení se po nasazení virtuálního počítače nedá změnit konfigurace Virtual Network. Nastavení protokolu TCP/IP musí být ponecháno na serveru DHCP Azure. Výchozím chováním je dynamické přidělování IP adres.
>
>

Adresa MAC karty virtuální sítě se může změnit, například po změně velikosti a hostovaného operačního systému Windows nebo Linux, kde se vybere nová síťová karta a v tomto případě se automaticky použije DHCP k přiřazení IP adresy a adres DNS.

##### <a name="static-ip-assignment"></a>Přiřazení statických IP adres
K virtuálním počítačům v rámci Azure Virtual Network je možné přiřadit pevné nebo rezervované IP adresy. Spuštění virtuálních počítačů v Azure Virtual Network otevře skvělou možnost pro využití této funkce v případě potřeby nebo vyžadované v některých případech. Přiřazení IP adres zůstává po celou dobu existence virtuálního počítače platné, nezávisle na tom, jestli je virtuální počítač spuštěný nebo vypnutý. V důsledku toho je potřeba při definování rozsahu IP adres pro Virtual Network vzít v úvahu celkový počet virtuálních počítačů (spuštěných a zastavených). IP adresa zůstane přiřazená buď do chvíle, kdy se virtuální počítač a jeho síťové rozhraní odstraní, nebo dokud se IP adresa znovu nevrátí. Další informace najdete v [tomto článku][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Statické IP adresy byste měli přiřadit prostřednictvím Azure do jednotlivých virtuální síťové adaptéry. V rámci hostovaného operačního systému do vNIC byste neměli přiřazování statických IP adres. Některé služby Azure, jako je například služba Azure Backup, spoléhají na skutečnost, že minimálně primární vNIC je nastavená na DHCP, a ne na statické IP adresy. Viz také dokument [řešení potíží se zálohováním virtuálních počítačů Azure](../../../backup/backup-azure-vms-troubleshoot.md#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Více síťových karet na virtuální počítač

Pro virtuální počítač Azure můžete definovat několik karet vNIC (Virtual Network Interface). Díky možnosti mít více virtuální síťové adaptéry můžete začít nastavovat oddělení síťových přenosů, kde je například klientský provoz směrován přes jeden vNIC a provoz back-endu je směrován přes druhý vNIC. Závisí na typu virtuálního počítače. existují různá omezení počtu virtuální síťové adaptéry, které je možné přiřadit k virtuálnímu počítači. Přesné podrobnosti, funkčnost a omezení najdete v těchto článcích:

* [Vytvoření virtuálního počítače s Windows s několika síťovými kartami][virtual-networks-multiple-nics-windows]
* [Vytvoření virtuálního počítače se systémem Linux s několika síťovými kartami][virtual-networks-multiple-nics-linux]
* [Nasazení virtuálních počítačů s více síťovými kartami pomocí šablony][virtual-network-deploy-multinic-arm-template]
* [Nasazení virtuálních počítačů s více síťovými kartami pomocí PowerShellu][virtual-network-deploy-multinic-arm-ps]
* [Nasazení virtuálních počítačů s více síťovými kartami pomocí Azure CLI][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Připojení Site-to-site

Mezi místními sítěmi jsou virtuální počítače Azure a místní propojení s transparentním a trvalým připojením k síti VPN. Očekává se, že se v Azure stane nejběžnějším vzorem nasazení SAP. Předpokladem je, že provozní postupy a procesy s instancemi SAP v Azure by měly fungovat transparentně. To znamená, že byste měli být schopni tisknout z těchto systémů a také použít systém SAP Transport Management System (TMS) k přenosu změn z vývojového systému v Azure do testovacího systému, který je nasazený místně. Další dokumentaci k řešení site-to-site najdete v [tomto článku][vpn-gateway-create-site-to-site-rm-powershell] .

##### <a name="vpn-tunnel-device"></a>Tunelové zařízení VPN

Aby bylo možné vytvořit připojení typu Site-to-Site (místní datové centrum k datovému centru Azure), je nutné buď získat a nakonfigurovat zařízení VPN, nebo použít službu Směrování a vzdálený přístup (RRAS), která byla představena jako softwarová součást s Windows Serverem 2012.

* [Vytvoření virtuální sítě s připojením VPN typu Site-to-site pomocí PowerShellu][vpn-gateway-create-site-to-site-rm-powershell]
* [Informace o zařízeních VPN pro připojení typu Site-to-Site ke službě VPN Gateway][vpn-gateway-about-vpn-devices]
* [Nejčastější dotazy k branám VPN][vpn-gateway-vpn-faq]

![Připojení Site-to-site mezi místními počítači a Azure][planning-guide-figure-600]

Výše uvedený obrázek ukazuje dvě předplatná Azure mají rezervované podrozsahy IP adres, které jsou rezervované pro použití ve virtuálních sítích v Azure. Připojení z místní sítě k Azure se vytváří prostřednictvím sítě VPN.

#### <a name="point-to-site-vpn"></a>SÍŤ VPN typu Point-to-site

SÍŤ VPN typu Point-to-site vyžaduje, aby se každý klientský počítač připojil k vlastní síti VPN do Azure. V případě scénářů SAP se díváte na připojení Point-to-site, které není praktické. Proto se pro připojení VPN typu Point-to-site neudělí žádné další odkazy.

Další informace najdete tady.
* [Konfigurace připojení typu Point-to-site k virtuální síti pomocí Azure Portal](../../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Konfigurace připojení Point-to-Site k virtuální síti pomocí prostředí PowerShell](../../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

#### <a name="multi-site-vpn"></a>SÍŤ VPN s více lokalitami

Azure také současné době nabízí možnost vytvořit připojení VPN s více lokalitami pro jedno předplatné Azure. Předchozí předplatné bylo omezené na jedno připojení typu Site-to-Site VPN. Toto omezení je u jednoho předplatného pryč s připojeními VPN s více lokalitami. Díky tomu je možné využít více než jednu oblast Azure pro konkrétní předplatné prostřednictvím konfigurací mezi různými místy.

Další dokumentaci najdete v [tomto článku][vpn-gateway-create-site-to-site-rm-powershell] .

#### <a name="vnet-to-vnet-connection"></a>Připojení virtuální sítě k virtuální síti

Pomocí sítě VPN s více lokalitami musíte v každé oblasti nakonfigurovat samostatné Virtual Network Azure. Nicméně často máte požadavek, aby softwarové komponenty v různých oblastech měly vzájemně komunikovat. V ideálním případě by tato komunikace neměla být směrována z jedné oblasti Azure do místní a z ní do jiné oblasti Azure. Pro zástupce Azure nabízí možnost nakonfigurovat připojení z jednoho Virtual Network Azure v jedné oblasti do jiné služby Azure Virtual Network hostované v jiné oblasti. Tato funkce se nazývá připojení VNet-to-VNet. Další podrobnosti o této funkci najdete tady: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/> .

#### <a name="private-connection-to-azure-expressroute"></a>Soukromé připojení k Azure ExpressRoute

Microsoft Azure ExpressRoute umožňuje vytváření privátních připojení mezi datovými centry Azure a místní infrastrukturou zákazníka nebo v prostředí ve společném umístění. ExpressRoute jsou nabízeny různými poskytovateli sítě VPN MPLS (přepnutí paketů) nebo jinými poskytovateli síťových služeb. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Připojení ExpressRoute nabízejí vyšší zabezpečení, větší spolehlivost prostřednictvím několika paralelních okruhů, rychlejší rychlost a nižší latenci než typická připojení přes Internet.

Další podrobnosti o Azure ExpressRoute a nabídkách najdete tady:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Express Route umožňuje více předplatných Azure prostřednictvím jednoho okruhu ExpressRoute, jak je popsáno zde.

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Vynucené tunelové propojení v případě více míst
U virtuálních počítačů, které se připojují k místním doménám prostřednictvím sítě Site-to-site, Point-to-site nebo ExpressRoute, musíte zajistit, aby se nastavení internetového proxy serveru nasadilo pro všechny uživatele v těchto virtuálních počítačích. Ve výchozím nastavení software spuštěný v těchto virtuálních počítačích nebo uživatelích, kteří používají prohlížeč pro přístup k Internetu, neprojde prostřednictvím proxy serveru společnosti, ale připojí se přímo přes Azure k Internetu. Ale i nastavení proxy serveru není 100% řešení pro směrování provozu prostřednictvím proxy společnosti, protože zodpovídá software a služby pro kontrolu proxy serveru. Pokud software spuštěný ve virtuálním počítači nedělá ani správce, může být provoz na internetu znovu vyhlídkaný přímo přes Azure do Internetu.

Abyste se vyhnuli přímému připojení k Internetu, můžete nakonfigurovat vynucené tunelování s připojením typu Site-to-site mezi místními prostředími a Azure. Podrobný popis funkce vynuceného tunelování se zveřejňuje tady. <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Vynucené tunelování pomocí ExpressRoute je povoleno zákazníky, kteří si vyberou výchozí trasu prostřednictvím relací partnerských vztahů protokolu BGP ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Shrnutí sítí Azure

Tato kapitola obsahuje mnoho důležitých bodů o sítích Azure. Tady je souhrn hlavních bodů:

* Virtuální sítě Azure umožňují do nasazení Azure umístit síťovou strukturu. Virtuální sítě je možné izolovat vzájemně nebo pomocí skupin zabezpečení sítě mezi virtuální sítě je možné řídit provoz.
* Virtuální sítě Azure je možné využít k přiřazení rozsahů IP adres k virtuálním počítačům nebo přiřazení pevných IP adres k virtuálním počítačům.
* Pokud chcete nastavit připojení typu Site-to-site nebo Point-to-site, musíte nejdřív vytvořit Virtual Network Azure.
* Po nasazení virtuálního počítače už nebude možné změnit Virtual Network přiřazeného k VIRTUÁLNÍmu počítači.

### <a name="quotas-in-azure-virtual-machine-services"></a>Kvóty ve službách virtuálních počítačů Azure
Musíme mít jasné informace o tom, že infrastruktura úložiště a sítě je sdílená mezi virtuálními počítači s různými službami v infrastruktuře Azure. Stejně jako v zákaznických datových centrech dochází k nadměrnému zajišťování některých prostředků infrastruktury v určité míře. Microsoft Azure platforma používá disk, procesor, síť a další kvóty k omezení spotřeby prostředků a zachování konzistentního a deterministického výkonu.  Různé typy virtuálních počítačů (A5, A6 atd.) mají různé kvóty pro počet disků, procesor, paměť RAM a síť.

> [!NOTE]
> Prostředky procesoru a paměti typů virtuálních počítačů podporovaných systémem SAP jsou předem přiděleny na hostitelských uzlech. To znamená, že jakmile se virtuální počítač nasadí, zpřístupní se prostředky v hostiteli podle definice typu virtuálního počítače.
>
>

Při plánování a velikosti SAP v řešeních Azure je třeba zvážit kvóty pro jednotlivé velikosti virtuálních počítačů. Kvóty virtuálních počítačů jsou popsané [tady (Linux)][virtual-machines-sizes-linux] a [tady (Windows)][virtual-machines-sizes-windows].

Popsané kvóty reprezentují teoretické maximální hodnoty.  Limit počtu vstupně-výstupních operací na disk lze dosáhnout pomocí malých vstupně-výstupních operací (8 KB), ale možná se nedosáhne s velkým I/v operačním systémem (1 MB).  Limit IOPS se vynutil v členitosti na jeden disk.

Vzhledem k tomu, že se rozhodnete, jestli systém SAP zapadá do služeb virtuálních počítačů Azure a jeho schopností nebo jestli je potřeba nakonfigurovat jiný systém, aby se mohl nasadit systém v Azure, je možné použít následující rozhodovací strom:

![Rozhodovací strom pro rozhodování o možnosti nasazení SAP v Azure][planning-guide-figure-700]

1. Nejdůležitější informace, které je třeba začít používat, jsou požadavkem SAP pro daný systém SAP. Požadavky SAP musí být odděleny na součást systému DBMS a část aplikace SAP, i když je systém SAP již nasazen místně v konfiguraci 2. úrovně. V případě stávajících systémů se v závislosti na stávajících srovnávacích testech SAP dají určit nebo odhadnout i body SAP týkající se často používaného hardwaru. Výsledky najdete [tady](https://sap.com/about/benchmark.html). U nově nasazených systémů SAP byste se měli dodávat prostřednictvím cvičení změny velikosti, které by mělo určovat požadavky systému SAP.
1. U stávajících systémů je třeba změřit vstupně-výstupní operace a vstupně-výstupní operace za sekundu na serveru DBMS. U nově plánovaných systémů by se při uplatnění změny velikosti pro nový systém měl také na straně systému DBMS poskytnout hrubou představu o požadavcích na vstupně-výstupní operace. Pokud si nejste jistí, nakonec je potřeba provést zkoušku konceptu.
1. Porovnání požadavku SAP pro server DBMS s protokoly SAP můžou poskytnout různé typy virtuálních počítačů Azure. Informace o protokolech SAP různých typů virtuálních počítačů Azure jsou zdokumentovány v části SAP Note [1928533]. Fokus by měl být nejprve na virtuálním počítači DBMS, protože databázová vrstva je vrstva v systému SAP NetWeaver, která se ve většině nasazení Nehorizontální škálovat. Naproti tomu může být horizontální navýšení kapacity aplikační vrstvy SAP. Pokud žádný z podporovaných typů virtuálních počítačů Azure nemůže doručovat požadované SAP, zatížení plánovaného systému SAP nejde v Azure spustit. Musíte buď nasadit systém místně, nebo potřebujete změnit svazek zatížení systému.
1. Jak je uvedeno [tady (Linux)][virtual-machines-sizes-linux] a [tady (Windows)][virtual-machines-sizes-windows], Azure vynutilo kvótu IOPS za disku nezávisle na tom, jestli používáte úložiště Standard nebo Premium Storage. V závislosti na typu virtuálního počítače se počet datových disků, které se dají připojit, liší. V důsledku toho můžete vypočítat maximální počet vstupně-výstupních operací, které je možné dosáhnout u každého z různých typů virtuálních počítačů. V závislosti na rozložení souboru databáze můžete disky proniknout, aby se v hostovaném operačním systému staly jedním svazkem. Pokud ale aktuální svazek IOPS nasazeného systému SAP překračuje vypočtené limity pro největší typ virtuálního počítače Azure a pokud nemůžete kompenzovat větší množství paměti, může být zatížení systému SAP vážně ovlivněno. V takových případech můžete narazit na místo, kde byste neměli nasazovat systém na Azure.
1. Zvláště v systémech SAP, které jsou nasazené místně v konfiguracích dvou vrstev, je pravděpodobné, že systém může být potřeba nakonfigurovat v Azure v konfiguraci s 3 vrstvami. V tomto kroku je potřeba ověřit, jestli je v aplikační vrstvě SAP nějaká součást, kterou nejde škálovat a které se nevejdou do prostředků procesoru a paměti, které nabízí jiný typ virtuálních počítačů Azure. Pokud taková součást skutečně existuje, systém SAP a její zatížení se nedají nasadit do Azure. Pokud ale můžete škálovat komponenty aplikace SAP na více virtuálních počítačů Azure, je možné systém nasadit do Azure.

Pokud se komponenty aplikační vrstvy DBMS a SAP dají spustit ve virtuálních počítačích Azure, musí být definovaná konfigurace s ohledem na:

* Počet virtuálních počítačů Azure
* Typy virtuálních počítačů pro jednotlivé součásti
* Počet virtuálních pevných disků v systému DBMS, které poskytují dostatek IOPS

## <a name="managing-azure-assets"></a>Správa prostředků Azure

### <a name="azure-portal"></a>portál Azure

Azure Portal je jedno ze tří rozhraní pro správu nasazení virtuálních počítačů Azure. Základní úlohy správy, jako je nasazení virtuálních počítačů z imagí, je možné provádět prostřednictvím Azure Portal. Kromě toho vytváření účtů úložiště, virtuálních sítí a dalších komponent Azure také provádí úlohy, které mohou Azure Portal dobře zvládnout. Nicméně funkce, jako je nahrání virtuálních pevných disků z místního prostředí do Azure nebo kopírování VHD v rámci Azure, jsou úkoly, které vyžadují nástroje nebo správu třetích stran prostřednictvím PowerShellu nebo rozhraní příkazového řádku.

![Přehled portál Microsoft Azure – virtuální počítač][planning-guide-figure-800]


Úlohy správy a konfigurace pro instanci virtuálního počítače jsou možné v rámci Azure Portal.

Kromě restartování a vypnutí virtuálního počítače můžete také připojit, odpojit a vytvořit datové disky pro instanci virtuálního počítače, zachytit instance pro přípravu bitové kopie a nakonfigurovat velikost instance virtuálního počítače.

Azure Portal poskytuje základní funkce pro nasazení a konfiguraci virtuálních počítačů a mnoho dalších služeb Azure. Netýká se však Azure Portal všech dostupných funkcí. V Azure Portal není možné provádět úlohy, jako například:

* Nahrávají se virtuální pevné disky do Azure.
* Kopírování virtuálních počítačů


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Správa prostřednictvím rutin Microsoft Azure PowerShell

Windows PowerShell je výkonné a rozšiřitelné rozhraní, které zákazníci, kteří nasazují větší počty systémů v Azure, široce přijali. Po instalaci rutin PowerShellu na stolní počítač, přenosný počítač nebo na vyhrazenou stanici pro správu je možné rutiny prostředí PowerShell spustit vzdáleně.

V [tomto článku][powershell-install-configure]se dozvíte, jak povolit místní pracovní plochu nebo přenosný počítač pro použití rutin Azure PowerShell a jak je nakonfigurovat pro použití s předplatnými Azure.

Podrobnější postup, jak nainstalovat, aktualizovat a nakonfigurovat Azure PowerShell rutiny, najdete v [této kapitole příručky pro nasazení][deployment-guide-4.1].

V takovém případě už bylo prostředí PowerShell (PS) v tomto případě výkonnějším nástrojem pro nasazení virtuálních počítačů a vytváření vlastních kroků v nasazení virtuálních počítačů. Všichni zákazníci, kteří používají instance SAP v Azure, používají rutiny PS k dodávání úloh správy, které dělají v Azure Portal, nebo používají rutiny PS výhradně ke správě jejich nasazení v Azure. Vzhledem k tomu, že rutiny specifické pro Azure mají stejné zásady vytváření názvů jako více než 2000 rutin týkajících se systému Windows, je pro správce Windows snadné využít tyto rutiny.

Viz příklad zde: <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


Nasazení rozšíření Azure pro SAP (viz kapitola [Azure Extension for SAP][planning-guide-9.1] v tomto dokumentu) je možné jenom prostřednictvím PowerShellu nebo rozhraní příkazového řádku. Proto je nutné nastavit a nakonfigurovat prostředí PowerShell nebo rozhraní příkazového řádku při nasazení nebo správě systému SAP NetWeaver v Azure.

Jelikož Azure poskytuje další funkce, přidají se nové rutiny PS, které vyžadují aktualizaci rutin. Proto má smysl kontrolovat web Azure Download alespoň jednou měsíčně <https://azure.microsoft.com/downloads/> pro novou verzi rutin. Nová verze je nainstalovaná nad starší verzí.

Obecný seznam příkazů PowerShellu souvisejících s Azure najdete tady: <https://docs.microsoft.com/powershell/azure/> .

### <a name="management-via-microsoft-azure-cli-commands"></a>Správa prostřednictvím příkazů rozhraní příkazového řádku Microsoft Azure

Pro zákazníky, kteří používají Linux a chtějí spravovat Azure Resources PowerShell, nemusí být možnost. Microsoft jako alternativu nabízí rozhraní příkazového řádku Azure.
Rozhraní příkazového řádku Azure nabízí sadu open source příkazů pro různé platformy pro práci s platformou Azure. Rozhraní příkazového řádku Azure CLI poskytuje mnoho stejných funkcí, jaké se nacházejí v Azure Portal.

Informace o instalaci, konfiguraci a způsobu použití příkazů rozhraní příkazového řádku k provádění úkolů Azure najdete v tématu.

* [Instalace Azure Classic CLI][xplat-cli]
* [Nasazení a správa virtuálních počítačů pomocí šablon Azure Resource Manager a Azure CLI] [.. /.. /linux/create-ssh-secured-vm-from-template.md]
* [Použití rozhraní příkazového řádku Azure Classic pro Mac, Linux a Windows s Azure Resource Manager][xplat-cli-azure-resource-manager]

Přečtěte si také kapitolu [Azure CLI pro virtuální počítače se systémem Linux][deployment-guide-4.5.2] v [Průvodci nasazením][planning-guide] , jak pomocí rozhraní příkazového řádku Azure nasadit rozšíření Azure pro SAP.


## <a name="first-steps-planning-a-deployment"></a>První postup plánování nasazení
Prvním krokem při plánování nasazení není vyhledání virtuálních počítačů, které jsou dostupné pro běh SAP. Prvním krokem může být časově náročné, ale nejdůležitější je spolupracovat s týmy pro dodržování předpisů a zabezpečení ve vaší společnosti na základě toho, jaké jsou podmínky hranice pro nasazení, který typ úlohy SAP nebo obchodní proces do veřejného cloudu. Pokud vaše společnost nasadila jiný software před do Azure, může být tento proces snadný. Pokud je vaše společnost na začátku cesty větší, můžou být potřebné větší diskuze, aby bylo možné zjistit podmínky hranic, podmínky zabezpečení, které umožňují hostování určitých dat SAP a obchodních procesů SAP ve veřejném cloudu.

Jako užitečnou nápovědu můžete Ukázat na [nabídky dodržování předpisů Microsoftu](/microsoft-365/compliance/offering-home) , které nabízí seznam dodržování předpisů, které může společnost Microsoft poskytnout.

Další oblasti otázek, jako je šifrování dat pro neaktivní nebo jiné šifrování ve službě Azure, jsou popsány v části [Přehled šifrování Azure](../../../security/fundamentals/encryption-overview.md).

Podceňují skutečnou tuto fázi projektu ve vašem plánování. Jenom v případě, že k tomuto tématu máte smlouvu a pravidla, musíte přejít na další krok, což je plánování síťové architektury, kterou nasazujete v Azure.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Různé způsoby nasazení virtuálních počítačů pro SAP v Azure

V této části se seznámíte s různými způsoby nasazení virtuálního počítače v Azure. V této kapitole jsou uvedené další postupy přípravy, jakož i manipulace s virtuálními pevnými disky a virtuálními počítači v Azure.

### <a name="deployment-of-vms-for-sap"></a>Nasazení virtuálních počítačů pro SAP

Microsoft Azure nabízí několik způsobů, jak nasadit virtuální počítače a přidružené disky. Proto je důležité porozumět rozdílům, protože přípravy virtuálních počítačů se mohou lišit v závislosti na metodě nasazení. Obecně se podívejme na následující scénáře:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Přesun virtuálního počítače z místního prostředí do Azure s nezobecněným diskem

Plánujete přesunout konkrétní systém SAP z místního prostředí do Azure. To se dá udělat tak, že nahrajete VHD, který obsahuje operační systém, binární soubory SAP a DBMS binárních souborů a také virtuální pevné disky s daty a soubory protokolů systému DBMS do Azure. Na rozdíl od [#2 níže][planning-guide-5.1.2]je na virtuálním počítači Azure, který jste nakonfigurovali v místním prostředí, zachovány uživatelské účty název hostitele, SAP SID a SAP. Proto není potřeba zobecnit image. Další informace najdete v kapitolách [Příprava pro přesun virtuálního počítače z místního prostředí do Azure pomocí nezobecněného disku][planning-guide-5.2.1] tohoto dokumentu pro místní přípravné kroky a nahrání nezobecněných virtuálních počítačů nebo virtuálních pevných disků do Azure. Přečtěte si článek [scénář 3: přesun virtuálního počítače z místního prostředí pomocí nezobecněného virtuálního pevného disku Azure s SAP][deployment-guide-3.4] v [Průvodci nasazením][deployment-guide] , kde najdete podrobný postup nasazení takové image v Azure.

Další možností, které v této příručce nebudeme podrobně pojednávat, je použití Azure Site Recovery k replikaci aplikačních serverů SAP NetWeaver a služeb SAP NetWeaver Central do Azure. Pro databázovou vrstvu nedoporučujeme používat Azure Site Recovery a místo toho používat mechanismy replikace specifické pro databázi, jako je například replikace systému HANA. Další informace najdete v tématu věnovaném [ochraně SAP](https://docs.microsoft.com/azure/site-recovery/site-recovery-workload#protect-sap) [pro zotavení po havárii pro místní aplikace](https://docs.microsoft.com/azure/site-recovery/site-recovery-workload) .

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Nasazení virtuálního počítače s obrázkem specifickým pro zákazníka

V důsledku specifických požadavků na opravu vaší verze operačního systému nebo systému DBMS nemusí zadané obrázky v Azure Marketplace odpovídat vašim potřebám. Proto je možné, že budete muset vytvořit virtuální počítač s použitím vlastní image virtuálního počítače s operačním systémem nebo DBMS, která se dá nasadit několikrát později. K přípravě privátní image pro duplikaci je potřeba vzít v úvahu tyto položky:

---
> ![Windows][Logo_Windows] Windows
>
> Další podrobnosti najdete tady: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> nastavení systému Windows (jako je Windows SID a hostname) se musí na místním virtuálním počítači pomocí příkazu Sysprep abstrakcovat a zobecnit.
>
>
> ![Linux][Logo_Linux] Linux
>
> Pomocí kroků popsaných v těchto článcích pro [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]nebo [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle]Připravte virtuální pevný disk, který se nahraje do Azure.
>
>

---
Pokud jste již nainstalovali obsah SAP v místním VIRTUÁLNÍm počítači (zvláště u systémů se dvěma vrstvami), můžete upravit nastavení systému SAP po nasazení virtuálního počítače Azure prostřednictvím postupu přejmenování instance podporovaného správcem SAP software Provisioning (SAP Note [1619720]). Informace najdete v kapitolách [Příprava pro nasazení virtuálního počítače pomocí Image specifické pro zákazníka pro SAP][planning-guide-5.2.2] a [nahrání virtuálního pevného disku z místního prostředí do Azure][planning-guide-5.3.2] v rámci místních přípravných kroků a NAHRÁNÍ zobecněného virtuálního počítače do Azure. Přečtěte si kapitolu [scénář 2: nasazení virtuálního počítače s vlastní imagí pro SAP][deployment-guide-3.3] v [Průvodci nasazením][deployment-guide] , kde najdete podrobný postup nasazení takové image v Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Nasazení virtuálního počítače z Azure Marketplace

Pokud chcete nasadit virtuální počítač, použijte k nasazení image virtuálního počítače od Microsoftu nebo jiného výrobce z Azure Marketplace. Po nasazení virtuálního počítače v Azure se řiďte stejnými pokyny a nástroji pro instalaci softwaru SAP a/nebo DBMS do svého virtuálního počítače, jako byste to provedli v místním prostředí. Podrobnější popis nasazení najdete v kapitole [scénář 1: nasazení virtuálního počítače z Azure Marketplace pro SAP][deployment-guide-3.2] v [Průvodci nasazením][deployment-guide].

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Příprava virtuálních počítačů pomocí SAP pro Azure

Před nahráním virtuálních počítačů do Azure je potřeba zajistit, aby virtuální počítače a virtuální pevné disky splňovaly určité požadavky. V závislosti na použité metodě nasazení existují malé rozdíly.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Příprava na přesun virtuálního počítače z místního prostředí do Azure pomocí nezobecněného disku

Běžným způsobem nasazení je přesunout existující virtuální počítač, který spouští systém SAP z místního prostředí do Azure. Tento virtuální počítač a systém SAP ve virtuálním počítači by se měly spouštět jenom v Azure se stejným názvem hostitele a nejspíš se stejným identifikátorem SAP SID. V takovém případě by se měl hostující operační systém virtuálního počítače zobecnit pro více nasazení. Pokud se místní síť rozšířila do Azure, pak se v rámci virtuálního počítače můžou použít i stejné účty domény jako ty, které se používaly ještě v místním prostředí.

Požadavky při přípravě vlastního disku virtuálního počítače Azure jsou:

* Původní virtuální pevný disk obsahující operační systém může mít maximálně 127 GB. Toto omezení bylo na konci března 2015 eliminováno. Virtuální pevný disk obsahující operační systém teď může mít velikost až 1 TB stejně jako všechny ostatní Azure Storage hostovaného virtuálního pevného disku.
* Musí být v pevném formátu VHD. Dynamické virtuální pevné disky nebo virtuální pevné disky ve formátu VHDx se v Azure zatím nepodporují. Když nahráváte VHD pomocí PowerShellu rutin nebo CLI, budou dynamické virtuální pevné disky převedeny na statické virtuální pevné disky.
* Virtuální pevné disky, které jsou připojené k virtuálnímu počítači a měly by se znovu připojit k virtuálnímu počítači v Azure, musí být v pevném formátu VHD. Přečtěte si [Tento článek](../../managed-disks-overview.md) , kde najdete omezení velikosti datových disků. Když nahráváte VHD pomocí PowerShellu rutin nebo CLI, budou dynamické virtuální pevné disky převedeny na statické virtuální pevné disky.
* Přidejte další místní účet s oprávněními správce, který může používat podpora Microsoftu, nebo které se dají přiřadit jako kontext pro služby a aplikace, které se mají spustit v nástroji, dokud není nasazený virtuální počítač a nebude možné používat vhodné uživatele.
* Přidejte další místní účty, které by mohly být potřeba pro konkrétní scénář nasazení.

---
> ![Windows][Logo_Windows] Windows
>
> V tomto scénáři není k nahrání a nasazení virtuálního počítače v Azure nutný žádný generalizace (Sysprep) virtuálního počítače.
> Ujistěte se, že jednotka D:\ se nepoužívá.
> Pro připojené disky nastavte automount disku, jak je popsáno v kapitole [Nastavení automatického připojení pro připojené disky][planning-guide-5.5.3] v tomto dokumentu.
>
> ![Linux][Logo_Linux] Linux
>
> V tomto scénáři není k nahrání a nasazení virtuálního počítače v Azure potřeba žádné generalizace (waagent-disvisioning) virtuálního počítače.
> Ujistěte se, že se/mnt/Resource nepoužívá a že jsou všechny disky připojené přes UUID. V případě disku s operačním systémem se ujistěte, že položka zaváděcího programu pro spouštění odráží taky připojení na bázi identifikátoru UUID.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Příprava k nasazení virtuálního počítače pomocí Image specifické pro zákazníka pro SAP

Soubory VHD, které obsahují zobecněný operační systém, jsou uložené v kontejnerech na účtech Azure Storage nebo jako image spravovaného disku. Nový virtuální počítač můžete z takové image nasadit odkazem na bitovou kopii VHD nebo na spravovaném disku jako zdroj v souborech šablon nasazení, jak je popsáno v kapitole [scénář 2: nasazení virtuálního počítače s vlastní imagí pro SAP][deployment-guide-3.3] [Průvodce nasazením][deployment-guide].

Požadavky při přípravě vlastní image virtuálního počítače Azure:

* Původní virtuální pevný disk obsahující operační systém může mít maximálně 127 GB. Toto omezení bylo na konci března 2015 eliminováno. Virtuální pevný disk obsahující operační systém teď může mít velikost až 1 TB stejně jako všechny ostatní Azure Storage hostovaného virtuálního pevného disku.
* Musí být v pevném formátu VHD. Dynamické virtuální pevné disky nebo virtuální pevné disky ve formátu VHDx se v Azure zatím nepodporují. Když nahráváte VHD pomocí PowerShellu rutin nebo CLI, budou dynamické virtuální pevné disky převedeny na statické virtuální pevné disky.
* Virtuální pevné disky, které jsou připojené k virtuálnímu počítači a měly by se znovu připojit k virtuálnímu počítači v Azure, musí být v pevném formátu VHD. Přečtěte si [Tento článek](../../managed-disks-overview.md) , kde najdete omezení velikosti datových disků. Když nahráváte VHD pomocí PowerShellu rutin nebo CLI, budou dynamické virtuální pevné disky převedeny na statické virtuální pevné disky.
* Přidejte další místní účty, které by mohly být potřeba pro konkrétní scénář nasazení.
* Pokud bitová kopie obsahuje instalaci SAP NetWeaver a přejmenování názvu hostitele z původního názvu v místě nasazení Azure je nejspíš, doporučujeme zkopírovat do šablony nejnovější verze disku DVD správce zřizování softwaru SAP. To vám umožní snadno použít funkci přejmenovávání SAP k přizpůsobení změněného názvu hostitele nebo změně identifikátoru SID systému SAP v nasazené imagi virtuálního počítače, jakmile se spustí nová kopie.

---
> ![Windows][Logo_Windows] Windows
>
> Ujistěte se, že jednotka D:\ nepoužívá se pro připojené disky nastavení automatického připojení disku, jak je popsáno v kapitole [Nastavení automatického připojení pro připojené disky][planning-guide-5.5.3] v tomto dokumentu.
>
> ![Linux][Logo_Linux] Linux
>
> Ujistěte se, že se/mnt/Resource nepoužívá a že jsou všechny disky připojené přes UUID. Pro disk s operačním systémem se ujistěte, že položka zaváděcího programu pro spouštění odráží i připojení na základě identifikátoru UUID.
>
>

---
* Grafické uživatelské rozhraní SAP (pro účely správy a nastavení) se dá v takové šabloně nainstalovat předem.
* Další software nezbytný k úspěšnému spuštění virtuálních počítačů v různých scénářích se dá nainstalovat, pokud tento software může pracovat s přejmenováním virtuálního počítače.

Pokud je virtuální počítač dostatečně připravený, aby byl obecný a nakonec nezávisle na účtech/uživatelích, kteří nejsou k dispozici v cíleném scénáři nasazení Azure, provede se poslední přípravný krok generalizace takových imagí.

##### <a name="generalizing-a-vm"></a>Generalizace virtuálního počítače
---
> ![Windows][Logo_Windows] Windows
>
> Posledním krokem je přihlášení k virtuálnímu počítači pomocí účtu správce. Otevřete okno příkazového řádku systému Windows jako *správce*. Přejít na%windir%\Windows\System32\Sysprep a provést sysprep.exe.
> Zobrazí se malé okno. Je důležité zaškrtnout možnost **generalizace** (výchozí nastavení je nezaškrtnuto) a změnit možnost vypnutí z výchozí hodnoty ' restartovat ' na ' vypínání '. Tento postup předpokládá, že se proces Sysprep v hostovaném operačním systému virtuálního počítače spustil místně.
> Pokud chcete provést postup s virtuálním počítačem, který už běží v Azure, postupujte podle kroků popsaných v [tomto článku](../../windows/capture-image-resource.md).
>
> ![Linux][Logo_Linux] Linux
>
> [Jak zachytit virtuální počítač s Linuxem a použít ho jako šablonu Resource Manageru][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Přenos virtuálních počítačů a virtuálních pevných disků mezi místními počítači do Azure
Vzhledem k tomu, že nahrávání imagí virtuálních počítačů a disků do Azure není možné prostřednictvím Azure Portal, je nutné použít rutiny Azure PowerShell nebo rozhraní příkazového řádku. Další možností je použití nástroje ' AzCopy '. Tento nástroj může kopírovat virtuální pevné disky mezi místními počítači a Azure (v obou směrech). Také může kopírovat virtuální pevné disky mezi oblastmi Azure. Další informace najdete v [této dokumentaci][storage-use-azcopy] ke stažení a použití AzCopy.

Třetí alternativou by bylo použití různých nástrojů orientovaných na grafické rozhraní (GUI) třetích stran. Ujistěte se však, že tyto nástroje podporují objekty blob stránky Azure. Pro náš účel musíme použít Azure Page BLOB Store (rozdíly jsou popsané tady: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs> ). Nástroje, které poskytuje Azure, jsou také efektivní při komprimaci virtuálních počítačů a virtuálních pevných disků, které je potřeba nahrát. To je důležité, protože tato Efektivita komprese zkracuje dobu nahrávání (která se v závislosti na připojení k Internetu z místního zařízení a cíle Azure Deployment Target) omezuje. Je to spravedlivý předpoklad, že nahrání virtuálního počítače nebo virtuálního pevného disku z Evropského umístění do datacenter Azure Datacenter bude trvat déle než nahrávání stejných virtuálních počítačů a virtuálních pevných disků do evropských datových center Azure.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Nahrání virtuálního pevného disku z místního prostředí do Azure
Pokud chcete nahrát existující virtuální počítač nebo virtuální pevný disk z místní sítě, třeba virtuální počítač nebo virtuální pevný disk musí splňovat požadavky uvedené v kapitole [Příprava na přesun virtuálního počítače z místního prostředí do Azure pomocí nezobecněného disku][planning-guide-5.2.1] tohoto dokumentu.

Takový virtuální počítač se nemusí zobecnit a je možné ho nahrát ve stavu a tvar po vypnutí na straně místního prostředí. Totéž platí pro další virtuální pevné disky, které neobsahují žádný operační systém.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Nahrání virtuálního pevného disku a jeho zpřístupnění na disku Azure
V tomto případě chceme nahrát VHD, a to buď s operačním systémem, nebo bez něj, a připojit ho k virtuálnímu počítači jako datový disk nebo ho použít jako disk s operačním systémem. Toto je proces s více kroky

**PowerShell**

* Přihlaste se ke svému předplatnému pomocí *Connect-AzAccount* .
* Nastavte předplatné kontextu pomocí *set-AzContext* a parametrů SubscriptionId nebo Subscription – viz <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Nahrání virtuálního pevného disku pomocí *Add-AzVhd* na účet Azure Storage – viz <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Volitelné Vytvoření spravovaného disku z VHD pomocí *New-AzDisk*  -See <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Nastavte disk s operačním systémem nové konfigurace virtuálního počítače na VHD nebo spravovaný disk pomocí *set-AzVMOSDisk* – viz <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Vytvoření nového virtuálního počítače z konfigurace virtuálního počítače pomocí *New-AzVM* -See <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Přidání datového disku k novému virtuálnímu počítači pomocí *Add-AzVMDataDisk* -See <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Azure CLI**

* Přihlaste se ke svému předplatnému pomocí *AZ Login*
* Vyberte své předplatné pomocí *AZ Account set--Subscription `<subscription name or id` > *
* Nahrajte virtuální pevný disk pomocí příkaz *AZ Storage BLOB upload* – viz [použití Azure CLI s Azure Storage][storage-azure-cli]
* Volitelné Vytvoření spravovaného disku z virtuálního pevného disku pomocí *AZ disk Create* -See https://docs.microsoft.com/cli/azure/disk
* Vytvořte nový virtuální počítač, který určuje nahraný virtuální pevný disk nebo spravovaný disk jako disk s operačním systémem pomocí *AZ VM Create* a Parameter *--Attach-OS-disk*
* Přidání datového disku k novému virtuálnímu počítači pomocí *AZ VM disk Attach* a Parameter *--New*

**Šablona**

* Nahrání VHD pomocí PowerShellu nebo Azure CLI
* Volitelné Vytvoření spravovaného disku z VHD pomocí PowerShellu, rozhraní příkazového řádku Azure nebo Azure Portal
* Nasaďte virtuální počítač se šablonou JSON odkazující na virtuální pevný disk, jak je znázorněno v [tomto příkladu šablony JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) , nebo pomocí Managed disks, jak je znázorněno v [tomto příkladu šablony JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Nasazení image virtuálního počítače
Pokud chcete nahrát existující virtuální počítač nebo virtuální pevný disk z místní sítě, aby ho bylo možné použít jako image virtuálního počítače Azure, třeba virtuální počítač nebo virtuální pevný disk musí splňovat požadavky uvedené v kapitole [Příprava pro nasazení virtuálního počítače pomocí image pro SAP tohoto dokumentu specifické pro zákazníka][planning-guide-5.2.2] .

* Použití *nástroje Sysprep* v systému Windows nebo *waagent – zrušení zřízení* na platformě Linux pro generalizaci virtuálního počítače – viz [technické informace o nástroji Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) pro systém Windows nebo [postup zachycení virtuálního počítače se systémem Linux pro použití jako šablony Správce prostředků][capture-image-linux-step-2-create-vm-image] pro Linux
* Přihlaste se ke svému předplatnému pomocí *Connect-AzAccount* .
* Nastavte předplatné kontextu pomocí *set-AzContext* a parametrů SubscriptionId nebo Subscription – viz <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Nahrání virtuálního pevného disku pomocí *Add-AzVhd* na účet Azure Storage – viz <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Volitelné Vytvoření image spravovaného disku z VHD pomocí *New-AzImage*  -See <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Nastavte disk s operačním systémem nové konfigurace virtuálního počítače na
  * VHD s *set-AzVMOSDisk-SourceImageUri-CreateOption fromImage* – viz <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Sada imagí spravovaného disku *– AzVMSourceImage* – viz <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Vytvoření nového virtuálního počítače z konfigurace virtuálního počítače pomocí *New-AzVM* -See <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Azure CLI**

* Použití *nástroje Sysprep* v systému Windows nebo *waagent – zrušení zřízení* na platformě Linux pro generalizaci virtuálního počítače – viz [technické informace o nástroji Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) pro systém Windows nebo [postup zachycení virtuálního počítače se systémem Linux pro použití jako šablony Správce prostředků][capture-image-linux-step-2-create-vm-image] pro Linux
* Přihlaste se ke svému předplatnému pomocí *AZ Login*
* Vyberte své předplatné pomocí *AZ Account set--Subscription `<subscription name or id` > *
* Nahrajte virtuální pevný disk pomocí příkaz *AZ Storage BLOB upload* – viz [použití Azure CLI s Azure Storage][storage-azure-cli]
* Volitelné Vytvoření bitové kopie spravovaného disku z VHD pomocí *AZ image Create* -See https://docs.microsoft.com/cli/azure/image
* Vytvoření nového virtuálního počítače s nahraným virtuálním pevným diskem nebo imagí spravovaného disku jako disku s operačním systémem pomocí *AZ VM Create* a parametr *--Image*

**Šablona**

* Použití *nástroje Sysprep* v systému Windows nebo *waagent – zrušení zřízení* na platformě Linux pro generalizaci virtuálního počítače – viz [technické informace o nástroji Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) pro systém Windows nebo [postup zachycení virtuálního počítače se systémem Linux pro použití jako šablony Správce prostředků][capture-image-linux-step-2-create-vm-image] pro Linux
* Nahrání VHD pomocí PowerShellu nebo Azure CLI
* Volitelné Vytvoření bitové kopie spravovaného disku z VHD pomocí PowerShellu, rozhraní příkazového řádku Azure nebo Azure Portal
* Nasaďte virtuální počítač se šablonou JSON odkazující na VHD s imagí, jak je znázorněno v [tomto příkladu šablony JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) , nebo pomocí Image spravovaného disku, jak je znázorněno v [tomto příkladu šablony JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Stahování VHD nebo Managed Disks do místního prostředí
Infrastruktura Azure jako služba není jednosměrná ulice, která dokáže nahrávat jenom virtuální pevné disky a systémy SAP. Systémy SAP můžete přesunout z Azure zpátky do místního světa.

V době stahování VHD nebo Managed Disks nejde aktivovat. I když stahujete disky, které jsou připojené k virtuálním počítačům, musí být virtuální počítač vypnutý a navrácený. Pokud chcete stáhnout jenom obsah databáze, v takovém případě by se měla použít k nastavení nového systému v místním prostředí, a pokud je to přijatelné, že během stahování a nastavení nového systému, který může systém v Azure fungovat, se můžete vyhnout dlouhé výpadky tím, že provedete komprimaci komprimované databáze na disk a stačí stáhnout tento disk místo toho, aby se také stáhl základní virtuální počítač s operačním systémem.

#### <a name="powershell"></a>PowerShell

* Stahuje se spravovaný disk. nejdřív potřebujete získat přístup k základnímu objektu BLOB spravovaného disku. Pak můžete původní objekt BLOB zkopírovat do nového účtu úložiště a stáhnout objekt BLOB z tohoto účtu úložiště.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Stažení virtuálního pevného disku po zastavení systému SAP a vypnutí virtuálního počítače můžete použít rutinu PowerShellu `Save-AzVhd` na místním cíli a stáhnout disky VHD zpátky do místního světa. Abyste to mohli udělat, potřebujete adresu URL virtuálního pevného disku, který najdete v části "úložiště" Azure Portal (je třeba přejít na účet úložiště a kontejner úložiště, kde byl virtuální pevný disk vytvořen) a potřebujete znát, kam se má VHD zkopírovat.

  Pak můžete použít příkaz definováním parametru SourceUri jako adresy URL disku VHD ke stažení a LocalFilePath jako fyzického umístění virtuálního pevného disku (včetně jeho názvu). Příkaz by mohl vypadat takto:

  ```powershell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Další podrobnosti o rutině Save-AzVhd najdete tady <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd> .

#### <a name="azure-cli"></a>Azure CLI
* Stahuje se spravovaný disk. nejdřív potřebujete získat přístup k základnímu objektu BLOB spravovaného disku. Pak můžete původní objekt BLOB zkopírovat do nového účtu úložiště a stáhnout objekt BLOB z tohoto účtu úložiště.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Stažení virtuálního pevného disku po zastavení systému SAP a vypnutí virtuálního počítače můžete použít příkaz Azure CLI `_azure storage blob download_` na místním cíli a stáhnout tak disky VHD zpátky do místního světa. Abyste to mohli udělat, potřebujete název a kontejner virtuálního pevného disku, který najdete v části "úložiště" Azure Portal (musí přejít na účet úložiště a kontejner úložiště, kde byl virtuální pevný disk vytvořen) a potřebujete znát, kam se má VHD zkopírovat.

  Pak můžete použít příkaz definováním objektů BLOB parametrů a kontejneru VHD ke stažení a cíle jako fyzického cílového umístění virtuálního pevného disku (včetně jeho názvu). Příkaz by mohl vypadat takto:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Přenos virtuálních počítačů a disků v rámci Azure

#### <a name="copying-sap-systems-within-azure"></a>Kopírování systémů SAP v Azure

Systém SAP nebo dokonce vyhrazený server DBMS, který podporuje aplikační vrstvu SAP, se nejspíš skládá z několika disků, které obsahují buď operační systém, binární soubory nebo data a soubory protokolů databáze SAP. Funkce Azure pro kopírování disků ani funkce Azure pro ukládání disků na místní disk má mechanismus synchronizace, který se konzistentně zaznamená na více discích. Proto se stav kopírovaných nebo uložených disků i v případě, že jsou připojené ke stejnému virtuálnímu počítači, liší. To znamená, že v konkrétním případě mají různá data a soubory protokolu obsažené na různých discích, databáze na konci by byla nekonzistentní.

**Závěr: aby bylo možné kopírovat nebo ukládat disky, které jsou součástí konfigurace systému SAP, je nutné zastavit systém SAP a také vypnout nasazený virtuální počítač. Jenom potom můžete zkopírovat nebo stáhnout sadu disků, abyste buď vytvořili kopii systému SAP v Azure, nebo v místním prostředí.**

Datové disky se dají ukládat jako soubory VHD v účtu Azure Storage a dají se přímo připojit k virtuálnímu počítači nebo použít jako image. V takovém případě se virtuální pevný disk před připojením k virtuálnímu počítači zkopíruje do jiného umístění. Úplný název souboru VHD v Azure musí být v rámci Azure jedinečný. Jak už bylo zmíněno dříve, název je druh názvu se třemi částmi, který vypadá nějak takto:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

Datové disky je také možné Managed Disks. V takovém případě se ke vytvoření nového spravovaného disku před připojením k virtuálnímu počítači používá spravovaný disk. Název spravovaného disku musí být v rámci skupiny prostředků jedinečný.

##### <a name="powershell"></a>PowerShell

Pomocí rutin Azure PowerShell můžete zkopírovat VHD, jak je znázorněno v [tomto článku][storage-powershell-guide-full-copy-vhd]. K vytvoření nového spravovaného disku použijte New-AzDiskConfig a New-AzDisk, jak je znázorněno v následujícím příkladu.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure CLI

Pomocí Azure CLI můžete kopírovat VHD. Pokud chcete vytvořit nový spravovaný disk, použijte příkaz *AZ disk Create* , jak je znázorněno v následujícím příkladu.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Nástroje Azure Storage

* <https://storageexplorer.com/>

Edice Professional Azure Storage Exploreru najdete tady:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

Kopie samotného virtuálního pevného disku v rámci účtu úložiště je proces, který trvá jenom pár sekund (podobně jako hardware sítě SAN vytváření snímků s opožděným kopírováním a kopírováním při zápisu). Po zkopírování souboru VHD ho můžete připojit k virtuálnímu počítači nebo ho použít jako image k připojení kopií VHD k virtuálním počítačům.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```

##### <a name="azure-cli"></a>Azure CLI

```azurecli

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Kopírování disků mezi Azure Storage účty
Tuto úlohu nelze provést na Azure Portal. Můžete použít rutiny Azure PowerShell, Azure CLI nebo prohlížeč úložiště třetích stran. Rutiny PowerShellu nebo příkazy rozhraní příkazového řádku můžou vytvářet a spravovat objekty blob, mezi které patří možnost asynchronního kopírování objektů BLOB mezi účty úložiště a napříč oblastmi v rámci předplatného Azure.

##### <a name="powershell"></a>PowerShell
Můžete také kopírovat virtuální pevné disky mezi předplatnými. Další informace najdete v [tomto článku][storage-powershell-guide-full-copy-vhd].

Základní tok logiky rutin PS vypadá takto:

* Vytvořte kontext účtu úložiště pro účet **zdrojového** úložiště pomocí *New-AzStorageContext* -See <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Vytvořte kontext účtu úložiště pro **cílový** účet úložiště pomocí *New-AzStorageContext* -See <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Spusťte kopii pomocí

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Zkontroluje stav kopie ve smyčce s

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Připojte nový virtuální pevný disk k virtuálnímu počítači, jak je popsáno výše.

Příklady najdete v [tomto článku][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Azure CLI
* Spusťte kopii pomocí

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Kontroluje stav, pokud je kopie stále ve smyčce s

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Připojte nový virtuální pevný disk k virtuálnímu počítači, jak je popsáno výše.

### <a name="disk-handling"></a>Zpracování disků

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Struktura virtuálních počítačů a disků pro nasazení SAP

V ideálním případě by mělo být zpracování struktury virtuálního počítače a přidružených disků jednoduché. V případě místních instalací zákazníci vyvinuli mnoho způsobů strukturování instalace serveru.

* Jeden základní disk, který obsahuje operační systém a všechny binární soubory systému DBMS a/nebo SAP. Od března 2015 může mít tento disk velikost až 1 TB místo předchozích omezení, která jsou omezená na 127 GB.
* Jeden nebo více disků, které obsahují soubor protokolu DBMS databáze SAP a soubor protokolu oblasti dočasného úložiště DBMS (pokud to DBMS podporuje). Pokud jsou požadavky databáze IOPS v protokolu vysoké, je nutné rozkládat více disků, aby bylo možné dosáhnout požadovaného objemu IOPS.
* Řada disků obsahující jeden nebo dva databázové soubory databáze SAP a soubory s dočasnými daty DBMS (pokud to DBMS podporuje).

![Referenční konfigurace virtuálního počítače Azure s IaaS pro SAP][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> Díky mnoha zákazníkům jsme viděli konfigurace, kde se například v c:\ nenainstalovaly binární soubory SAP a DBMS. jednotka, ve které byl operační systém nainstalován. K tomu došlo z různých důvodů, ale když jsme se vrátili do kořenového adresáře, obvykle se jednalo o malé a minimální upgrady operačního systému, které jsou potřeba ještě před 10-15 lety. Obě podmínky se již nevztahují na tyto dny příliš často. Dnešní c:\ jednotku je možné namapovat na velké disky svazků nebo virtuální počítače. Aby bylo nasazení v rámci své struktury jednoduché, doporučujeme postupovat podle následujícího vzoru nasazení pro systémy SAP NetWeaver v Azure.
>
> Stránkovací soubor operačního systému Windows by měl být na jednotce D: (netrvalý disk).
>
> ![Linux][Logo_Linux] Linux
>
> Swapfile pro Linux umístěte do/mnt/mnt/Resource v systému Linux, jak je popsáno v [tomto článku][virtual-machines-linux-agent-user-guide]. Odkládací soubor se dá nakonfigurovat v konfiguračním souboru/etc/waagent.conf. agenta pro Linux. Přidejte nebo změňte následující nastavení:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Chcete-li aktivovat změny, je nutné restartovat agenta pro Linux s nástrojem

```console
sudo service waagent restart
```

Další podrobnosti o Doporučené velikosti stránkovacího souboru najdete v tématu SAP Note [1597355] .

---
Počet disků, které se používají pro datové soubory DBMS, a typ Azure Storage, na kterých jsou tyto disky hostované, by měly být určeny požadavky na IOPS a latencí. Přesné kvóty jsou popsány v [tomto článku (Linux)][virtual-machines-sizes-linux] a v [tomto článku (Windows)][virtual-machines-sizes-windows].

Zkušenosti s nasazením SAP za poslední dva roky nám pomohou s některými lekcemi, které se dají shrnout jako:

* Provoz IOPS do různých datových souborů není vždycky stejný, protože stávající systémy zákazníků můžou mít jinak velikost datových souborů, které představují jejich databáze SAP. V důsledku toho je lepší používat konfiguraci RAID přes více disků k umístění datových souborů LUN Carved z těch. Existují situace, zejména ve službě Azure Storage úrovně Standard, kde frekvence IOPS dosáhla kvóty jednoho disku proti transakčnímu protokolu DBMS. V takových scénářích se doporučuje použít Premium Storage nebo můžete agregovat více standardních disků úložiště pomocí softwarového proužku.

---
> ![Windows][Logo_Windows] Windows
>
> * [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Konfigurace softwarového pole RAID v systému Linux][virtual-machines-linux-configure-raid]
> * [Konfigurace LVM na virtuálním počítači se systémem Linux v Azure][virtual-machines-linux-configure-lvm]
>
>

---
* Premium Storage se zobrazuje s významným lepším výkonem, zejména pro zápis kritických protokolů transakcí. U scénářů SAP, u kterých se očekává, že dodávají produkční prostředí jako výkon, se důrazně doporučuje použít řadu virtuálních počítačů, které můžou využít Azure Premium Storage.

Mějte na paměti, že disk, který obsahuje operační systém a jak doporučujeme, binární soubory SAP a databáze (základní virtuální počítač) už nejsou omezené na 127 GB. Teď může mít velikost až 1 TB. V takovém případě by měl být dostatek místa pro uchování všech potřebných souborů, například protokolů dávkové úlohy SAP.

Další návrhy a další podrobnosti, konkrétně pro virtuální počítače s DBMS, najdete v [Průvodci nasazením DBMS][dbms-guide] .

#### <a name="disk-handling"></a>Zpracování disků

Ve většině scénářů je potřeba vytvořit další disky, aby bylo možné nasadit databázi SAP do virtuálního počítače. Mluvili o požadavcích na počet disků v kapitole [VM/disková struktura pro nasazení SAP][planning-guide-5.5.1] tohoto dokumentu. Azure Portal umožňuje připojit a odpojit disky po nasazení základního virtuálního počítače. Disky je možné připojit/odpojit, když je virtuální počítač v provozu i když je zastavený. Když připojíte disk, Azure Portal nabídky připojit prázdný disk nebo existující disk, který v tomto okamžiku není připojen k jinému virtuálnímu počítači.

**Poznámka**: disky je možné v daném okamžiku připojit pouze k jednomu virtuálnímu počítači.

![Připojení a odpojení disků pomocí Azure Storage úrovně Standard][planning-guide-figure-1400]

Během nasazování nového virtuálního počítače se můžete rozhodnout, jestli chcete použít Managed Disks nebo umístit disky na účty Azure Storage. Pokud chcete použít Premium Storage, doporučujeme použít Managed Disks.

V dalším kroku se musíte rozhodnout, jestli chcete vytvořit nový a prázdný disk, nebo jestli chcete vybrat existující disk, který jste nahráli dříve, a měl by se teď připojit k virtuálnímu počítači.

**Důležité**: **nechcete používat** ukládání hostitelů do mezipaměti ve službě Azure Storage úrovně Standard. Měli byste ponechat předvolby mezipaměti hostitele ve výchozím nastavení NONE. V případě služby Azure Premium Storage je vhodné povolit ukládání do mezipaměti pro čtení, pokud jsou vstupně-výstupní charakteristiky většinou čteny jako typický vstupně-výstupní přenos dat pro databázové datové soubory. V případě souboru protokolu transakcí databáze není doporučeno ukládat do mezipaměti.

---
> ![Windows][Logo_Windows] Windows
>
> [Postup připojení datového disku v Azure Portal][virtual-machines-linux-attach-disk-portal]
>
> Pokud jsou disky připojené, musíte se přihlásit k virtuálnímu počítači a otevřít Správce disků Windows. Pokud není v kapitole [Nastavení automatického připojení pro připojené disky][planning-guide-5.5.3]povolené automount jako doporučené, je potřeba, aby byl nově připojený svazek online a inicializovaný.
>
> ![Linux][Logo_Linux] Linux
>
> Pokud jsou disky připojené, musíte se přihlásit k virtuálnímu počítači a inicializovat disky, jak je popsáno v [tomto článku][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux] .
>
>

---
Pokud je nový disk prázdný disk, budete muset také naformátovat disk. Pro formátování, zejména pro systémy souborů a soubory protokolů, se používají stejná doporučení jako pro nasazení systému DBMS na holé systémy.

Účet Azure Storage neposkytuje nekonečné prostředky z pohledu na vstupně-výstupní svazek, IOPS a objem dat. Obvykle jsou virtuální počítače se systémem DBMS ovlivněny tímto. Pro každý virtuální počítač může být nejvhodnější použít samostatný účet úložiště, pokud máte více virtuálních počítačů s vysokým počtem vstupně-výstupních operací k nasazení, aby bylo možné zůstat v rámci limitu Azure Storageho objemu účtu. V opačném případě potřebujete zjistit, jak můžete vyrovnávat tyto virtuální počítače mezi různými účty úložiště, aniž byste dosáhli limitu každého jednoho účtu úložiště. Další podrobnosti jsou popsány v [Průvodci nasazením systému DBMS][dbms-guide]. Měli byste taky zachovat tato omezení pro virtuální počítače s čistým aplikačním serverem SAP nebo jiné virtuální počítače, což může vyžadovat další virtuální pevné disky. Tato omezení neplatí při použití spravovaného disku. Pokud plánujete použít Premium Storage, doporučujeme použít spravovaný disk.

Dalším tématem, které je relevantní pro účty úložiště, je to, jestli se virtuální pevné disky v účtu úložiště získávají geograficky replikované. Geografická replikace je povolená nebo zakázaná na úrovni účtu úložiště, ne na úrovni virtuálního počítače. Pokud je geografická replikace povolená, virtuální pevné disky v účtu úložiště se replikují do jiného datového centra Azure v rámci stejné oblasti. Před tím, než se rozhodnete, je třeba zvážit následující omezení:

Geografická replikace Azure funguje lokálně na každém virtuálním pevném disku virtuálního počítače a nereplikuje vstupně-výstupních prostředí v chronologickém pořadí napříč více virtuálními pevnými disky ve virtuálním počítači. Proto se virtuální pevný disk, který představuje základní virtuální počítač, a všechny další virtuální pevné disky připojené k virtuálnímu počítači replikují nezávisle na sobě. To znamená, že nedochází k žádné synchronizaci mezi změnami v různých virtuálních pevných discích. Skutečnost, že I/o se replikují nezávisle na pořadí, ve kterém jsou zapsána, znamená, že geografická replikace není hodnotou pro databázové servery, které mají své databáze distribuovány přes více virtuálních pevných disků. Kromě systému DBMS můžou existovat také další aplikace, ve kterých procesy napisují nebo zpracovávají data na různých virtuálních pevných discích a tam, kde je důležité zachovat pořadí změn. V takovém případě by se geografická replikace v Azure neměla povolit. Závisí na tom, jestli potřebujete nebo chcete geografickou replikaci pro sadu virtuálních počítačů, ale ne pro jinou sadu, můžete své virtuální počítače a jejich související virtuální pevné disky kategorizovat do různých účtů úložiště, které mají povolenou nebo zakázanou geografickou replikaci.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Nastavení automatického připojení pro připojené disky
---
> ![Windows][Logo_Windows] Windows
>
> U virtuálních počítačů, které se vytvářejí z vlastních imagí nebo disků, je nutné zaškrtnout a případně nastavit parametr automount. Nastavením tohoto parametru povolíte virtuálnímu počítači po restartování nebo opětovném nasazení v Azure a automaticky připojí připojené nebo připojené jednotky.
> Parametr je nastaven pro bitové kopie poskytnuté společností Microsoft v Azure Marketplace.
>
> Pokud chcete nastavit automount, Projděte si dokumentaci ke spustitelnému souboru příkazového řádku diskpart.exe tady:
>
> * [Parametry příkazového řádku nástroje DiskPart](/previous-versions/windows/it-pro/windows-xp/bb490893(v=technet.10))
> * [Automaticky připojit](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc753703(v=ws.11))
>
> Okno příkazového řádku systému Windows by mělo být otevřeno jako správce.
>
> Pokud jsou disky připojené, musíte se přihlásit k virtuálnímu počítači a otevřít Správce disků Windows. Pokud není v kapitole [Nastavení automatického připojení pro připojené disky][planning-guide-5.5.3]povolené automount jako doporučené, musí být nově připojený svazek >online a inicializovaný.
>
> ![Linux][Logo_Linux] Linux
>
> Je potřeba inicializovat nově připojený prázdný disk, jak je popsáno v [tomto článku][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Také je potřeba přidat do adresáři/etc/fstab. nové disky.
>
>

---
### <a name="final-deployment"></a>Konečné nasazení

Poslední nasazení a přesný postup, zejména v souvislosti s nasazením rozšíření Azure pro SAP, najdete v [Průvodci nasazením][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Přístup k systémům SAP běžícím v rámci virtuálních počítačů Azure

Pro scénáře, kdy se chcete připojit k těmto systémům SAP na veřejném Internetu pomocí grafického uživatelského rozhraní SAP, je nutné použít následující postupy.

Později v dokumentu se podíváme na další hlavní scénář, který se připojuje k systémům SAP v různých nasazeních, která mají připojení typu Site-to-Site (tunel VPN) nebo připojení Azure ExpressRoute mezi místními systémy a systémy Azure.

### <a name="remote-access-to-sap-systems"></a>Vzdálený přístup k systémům SAP

V Azure Resource Manager neexistují žádné výchozí koncové body, které by již v bývalém modelu Classic nevypadaly. Všechny porty Azure Resource Manager virtuálního počítače jsou otevřené, dokud:

1. Pro podsíť nebo síťové rozhraní není definovaná žádná skupina zabezpečení sítě. Síťový provoz do virtuálních počítačů Azure je možné zabezpečit prostřednictvím volání "skupiny zabezpečení sítě". Další informace najdete v článku [Skupina zabezpečení sítě][virtual-networks-nsg].
2. Pro síťové rozhraní není definovaná žádná Azure Load Balancer.

Podívejte se na rozdíl architektury mezi klasickým modelem a ARM, jak je popsáno v [tomto článku][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Konfigurace systému SAP a připojení grafického uživatelského rozhraní SAP přes Internet

V tomto článku najdete popis podrobností k tomuto tématu:

<https://docs.microsoft.com/archive/blogs/saponsqlserver/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure>

#### <a name="changing-firewall-settings-within-vm"></a>Změna nastavení brány firewall v rámci virtuálního počítače

Je možné, že bude nutné nakonfigurovat bránu firewall na virtuálních počítačích, aby umožňovala příchozí provoz do systému SAP.

---
> ![Windows][Logo_Windows] Windows
>
> Ve výchozím nastavení je brána Windows Firewall v rámci virtuálního počítače nasazeného v Azure zapnutá. Nyní je třeba umožnit otevření portu SAP, jinak se grafické uživatelské rozhraní SAP nebude moci připojit.
> Použijte následující postup:
>
> * Otevřete ovládací panely \ systém a Security\Windows firewall pro **Pokročilá nastavení**.
> * Nyní klikněte pravým tlačítkem na příchozí pravidla a zvolte **nové pravidlo**.
> * V následujícím průvodci se zvolilo vytvoření nového pravidla **portu** .
> * V dalším kroku průvodce ponechte nastavení na TCP a zadejte číslo portu, který chcete otevřít. Vzhledem k tomu, že naše ID instance SAP je 00, trvala 3200. Pokud má vaše instance jiné číslo instance, je nutné otevřít port, který jste definovali dříve v závislosti na číslu instance.
> * V další části Průvodce musíte opustit položku **Povolení připojení** zaškrtnuto.
> * V dalším kroku průvodce potřebujete definovat, jestli se pravidlo vztahuje k doméně, soukromé a veřejné síti. Podle potřeby je upravte. Pokud se ale připojujete pomocí grafického uživatelského rozhraní SAP z vnějšku přes veřejnou síť, musíte použít pravidlo pro veřejnou síť.
> * V posledním kroku průvodce pojmenujte pravidlo a uložte ho stisknutím tlačítka **Dokončit**.
>
> Pravidlo se okamžitě projeví.
>
> ![Definice pravidla portu][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Image Linux v Azure Marketplace ve výchozím nastavení nepovolí bránu firewall softwaru iptables a připojení k vašemu systému SAP by mělo fungovat. Pokud jste povolili softwaru iptables nebo jinou bránu firewall, přečtěte si dokumentaci k softwaru iptables nebo použitou bránu firewall, která povolí příchozí provoz TCP na portu 32xx (kde XX je systémové číslo vašeho systému SAP).
>
>

---
#### <a name="security-recommendations"></a>Doporučení zabezpečení

Grafické rozhraní SAP se nepřipojuje okamžitě k žádné instanci SAP (port 32xx), která je spuštěná, ale nejdřív se připojuje přes port otevřený k procesu serveru zpráv SAP (port 36xx). V minulosti byl stejný port použit serverem zpráv pro interní komunikaci s instancemi aplikace. Aby místní aplikační servery nechtěně komunikovaly se serverem zpráv v Azure, je možné změnit interní komunikační porty. Důrazně doporučujeme změnit interní komunikaci mezi serverem zpráv SAP a jeho instancemi aplikace na jiné číslo portu v systémech, které byly naklonovány z místních systémů, jako je klon vývoje pro testování projektu atd. Dá se to udělat s výchozím parametrem profilu:

> rdisp/msserv_internal
>
>

Jak je popsáno v [nastavení zabezpečení pro server zpráv SAP](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Jeden virtuální počítač se scénářem Ukázky/školení SAP NetWeaver

![Spuštění ukázkových systémů SAP s jedním virtuálním počítačem se stejnými názvy virtuálních počítačů, které jsou izolované v Azure Cloud Services][planning-guide-figure-1700]

V tomto scénáři implementujeme Typický scénář školení/ukázkový systém, ve kterém je kompletní scénář školení/ukázky obsažený v jednom virtuálním počítači. Předpokládáme, že se nasazení provádí prostřednictvím šablon imagí virtuálních počítačů. Předpokládáme také, že více těchto virtuálních počítačů ukázek a školení je potřeba nasadit s virtuálními počítači se stejným názvem. Všechny školicí systémy nemají připojení k místním prostředkům a jsou naopak pro hybridní nasazení.

Předpokladem je, že jste vytvořili image virtuálního počítače, jak je popsáno v některých částech kapitoly [Příprava virtuálních počítačů pomocí SAP pro Azure][planning-guide-5.2] v tomto dokumentu.

Sekvence událostí pro implementaci scénáře vypadá takto:

##### <a name="powershell"></a>PowerShell

* Vytvořit novou skupinu prostředků pro každé školení/ukázku na šířku

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Pokud nechcete používat Managed Disks, vytvořte nový účet úložiště.

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Vytvořte novou virtuální síť pro každé školení nebo ukázku na šířku, abyste mohli povolit použití stejného názvu hostitele a IP adresy. Virtuální síť je chráněná skupinou zabezpečení sítě, která umožňuje přístup ke vzdálené ploše a portu 22 pro SSH jenom přes přenosy přes port 3389.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Vytvořte novou veřejnou IP adresu, kterou je možné použít pro přístup k virtuálnímu počítači z Internetu.

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Vytvořit nové síťové rozhraní pro virtuální počítač

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Vytvoří virtuální počítač. Pro tento scénář bude mít každý virtuální počítač stejný název. Identifikátor SID SAP instancí SAP NetWeaver v těchto virtuálních počítačích bude stejný i nadále. V rámci skupiny prostředků Azure musí být název virtuálního počítače jedinečný, ale v různých skupinách prostředků Azure můžete spustit virtuální počítače se stejným názvem. Výchozí účet správce Windows nebo root pro Linux není platný. Proto je nutné definovat nové uživatelské jméno správce společně s heslem. Velikost virtuálního počítače musí být taky definovaná.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Volitelně přidejte další disky a obnovte potřebný obsah. Všechny názvy objektů BLOB (adresy URL objektů BLOB) musí být v rámci Azure jedinečné.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>Rozhraní příkazového řádku

V systému Linux lze použít následující vzorový kód. V případě systému Windows buď použijte prostředí PowerShell, jak je popsáno výše, nebo tento příklad Přizpůsobte, aby místo $rgName používal% rgName%, a nastavte proměnnou prostředí pomocí *sady*příkazů systému Windows.

* Vytvořit novou skupinu prostředků pro každé školení/ukázku na šířku

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Vytvoření nového účtu úložiště

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Vytvořte novou virtuální síť pro každé školení nebo ukázku na šířku, abyste mohli povolit použití stejného názvu hostitele a IP adresy. Virtuální síť je chráněná skupinou zabezpečení sítě, která umožňuje přístup ke vzdálené ploše a portu 22 pro SSH jenom přes přenosy přes port 3389.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Vytvořte novou veřejnou IP adresu, kterou je možné použít pro přístup k virtuálnímu počítači z Internetu.

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Vytvořit nové síťové rozhraní pro virtuální počítač

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Vytvoří virtuální počítač. Pro tento scénář bude mít každý virtuální počítač stejný název. Identifikátor SID SAP instancí SAP NetWeaver v těchto virtuálních počítačích bude stejný i nadále. V rámci skupiny prostředků Azure musí být název virtuálního počítače jedinečný, ale v různých skupinách prostředků Azure můžete spustit virtuální počítače se stejným názvem. Výchozí účet správce Windows nebo root pro Linux není platný. Proto je nutné definovat nové uživatelské jméno správce společně s heslem. Velikost virtuálního počítače musí být taky definovaná.

```azurecli
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```azurecli
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Volitelně přidejte další disky a obnovte potřebný obsah. Všechny názvy objektů BLOB (adresy URL objektů BLOB) musí být v rámci Azure jedinečné.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Šablona

Ukázkové šablony můžete použít v úložišti Azure-Rychlé šablony na GitHubu.

* [Jednoduchý virtuální počítač se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Jednoduchý virtuální počítač s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Virtuální počítač z Image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementace sady virtuálních počítačů, které komunikují v rámci Azure

Tento nehybridní scénář je typický scénář pro školení a demonstrační účely, kde je software představující scénář ukázky/školení rozložený na více virtuálních počítačů. Různé komponenty nainstalované v různých virtuálních počítačích potřebují vzájemně komunikovat. V tomto scénáři se v takovém případě nevyžaduje žádná místní síťová komunikace ani scénář mezi místními sítěmi.

Tento scénář je rozšířením instalace popsané v kapitole [jeden virtuální počítač se scénářem Ukázky/školení SAP NetWeaver][planning-guide-7.1] v tomto dokumentu. V takovém případě se do existující skupiny prostředků přidá víc virtuálních počítačů. V následujícím příkladu se školení na šířku skládá z virtuálního počítače SAP ASCS/SCS, virtuálního počítače se systémem DBMS a instance aplikačního serveru SAP.

Než sestavíte tento scénář, musíte se zamyslet nad základními nastaveními, která už ve scénáři využijete.

#### <a name="resource-group-and-virtual-machine-naming"></a>Skupina prostředků a pojmenování virtuálních počítačů

Všechny názvy skupin prostředků musí být jedinečné. Vytvořte si vlastní schéma pojmenování prostředků, jako je například `<rg-name`> přípona.

Název virtuálního počítače musí být v rámci skupiny prostředků jedinečný.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Nastavení sítě pro komunikaci mezi různými virtuálními počítači

![Sada virtuálních počítačů v rámci Azure Virtual Network][planning-guide-figure-1900]

Aby se zabránilo kolizím názvů pomocí klonů se stejným školením nebo demonstrační krajinou, je třeba vytvořit Virtual Network Azure pro všechny na šířku. Překlad názvů DNS bude poskytovat Azure, nebo můžete nakonfigurovat vlastní server DNS mimo Azure (tady se nezabývá popsáním). V tomto scénáři nemůžeme nakonfigurovat vlastní DNS. U všech virtuálních počítačů v rámci jedné služby Azure Virtual Network bude povolená komunikace prostřednictvím názvů hostitelů.

Důvody pro oddělení školení nebo ukázkové krajiny pro virtuální sítě a nikoli jenom skupiny prostředků:

* Nastavení SAP na šířku vyžaduje vlastní AD/OpenLDAP a server domény musí být součástí každé z krajiny.
* Nastavení SAP na šířku jako má komponenty, které potřebují pracovat s pevnými IP adresami.

Další podrobnosti o virtuálních sítích Azure a jejich definování najdete v [tomto článku][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Nasazení virtuálních počítačů SAP s připojením k podnikové síti (mezi místními sítěmi)

Spustíte prostředí SAP na šířku a chcete rozdělit nasazení mezi holé servery DBMS, místní virtualizovaná prostředí pro vrstvy aplikací a menší 2 úrovně nakonfigurované systémy SAP a Azure IaaS. Základním předpokladem je, že systémy SAP v rámci jedné technologie SAP na šířku potřebují vzájemně komunikovat a s mnoha dalšími softwarovými součástmi, které jsou nasazené ve společnosti, nezávisle na jejich formuláři nasazení. Pro koncového uživatele, který se připojuje s grafickým rozhraním SAP nebo jinými rozhraními, by neměly být zavedeny žádné rozdíly ve formuláři nasazení. Tyto podmínky můžou být splněné jenom v případě, že máme místní služby Active Directory/OpenLDAP a DNS, které se rozšiřují na systémy Azure prostřednictvím připojení typu Site-to-site/Multi-Site nebo privátní připojení, jako je Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Scénář technologie SAP na šířku

Scénář mezi různými místy nebo hybridním scénářem může být zhruba popsaný jako na obrázku níže:

![Připojení Site-to-site mezi místními prostředky a prostředky Azure][planning-guide-figure-2100]

Minimálním požadavkem je použití zabezpečených komunikačních protokolů, jako je protokol SSL/TLS pro přístup z prohlížeče nebo připojení k síti VPN pro přístup k systému do služeb Azure. Předpokladem je, že společnosti zpracovávají připojení VPN mezi podnikovou sítí a Azure odlišně. Některé společnosti můžou prázdné porty otevřít na všech těchto portech. Některé jiné společnosti můžou být přesné v tom, které porty potřebují otevřít atd.

V tabulce níže jsou uvedeny typické komunikační porty SAP. V podstatě stačí otevřít port brány SAP.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Služba | Název portu | Příklad `<nn`> = 01 | Výchozí rozsah (min-max) | Komentář |
| --- | --- | --- | --- | --- |
| Dispečer |sapdp `<nn>` Viz * |3201 |3200 – 3299 |SAP Dispatcher, používané grafickým uživatelským rozhraním SAP pro Windows a Java |
| Server zpráv |sapms `<sid`> viz * * |3600 |bezplatné sapms`<anySID`> |SID = SAP-System-ID |
| brána |sapgw `<nn`> viz * |3301 |free |Brána SAP, která se používá pro komunikaci CPIC a RFC |
| Směrovač SAP |sapdp99 |3299 |free |V/etc/Services se dá po instalaci přiřadit jenom názvy služeb CI (centrální instance) k libovolné hodnotě. |

*) NN = číslo instance SAP

* *) SID = SAP-System-ID

Podrobnější informace o portech vyžadovaných pro různé produkty nebo služby SAP podle produktů SAP najdete tady <https://scn.sap.com/docs/DOC-17124> .
V tomto dokumentu byste měli být schopni otevřít vyhrazené porty v zařízení VPN, které jsou nezbytné pro konkrétní produkty a scénáře SAP.

Další bezpečnostní opatření při nasazení virtuálních počítačů v takovém scénáři by mohla být vytvoření [skupiny zabezpečení sítě][virtual-networks-nsg] pro definování pravidel přístupu.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Tisk na tiskárně místní sítě z instance SAP v Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Tisk přes protokol TCP/IP ve scénáři mezi místními sítěmi

Nastavení místních tiskáren založených na protokolu TCP/IP na VIRTUÁLNÍm počítači Azure je stejné jako ve vaší podnikové síti, a to za předpokladu, že máte navázáno tunelování VPN typu Site-to-site nebo ExpressRoute připojení.

---
> ![Windows][Logo_Windows] Windows
>
> Použijte následující postup:
>
> * Některé síťové tiskárny se dodávají s Průvodcem konfigurací, který usnadňuje nastavení tiskárny ve virtuálním počítači Azure. Pokud se s tiskárnou nedistribuuje žádný software průvodce, ručním způsobem nastavení tiskárny je vytvoření nového portu tiskárny TCP/IP.
> * Otevření Ovládacích panelů – > zařízení a tiskáren – > přidání tiskárny
> * Vyberte možnost Přidat tiskárnu pomocí adresy protokolu TCP/IP nebo názvu hostitele.
> * Zadejte IP adresu tiskárny.
> * Port tiskárny Standard 9100
> * V případě potřeby nainstalujte vhodný ovladač tiskárny ručně.
>
> ![Linux][Logo_Linux] Linux
>
> * Podobně jako u Windows stačí postupovat podle standardního postupu instalace síťové tiskárny.
> * Stačí postupovat podle pokynů pro veřejné Linux pro [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) nebo [Red Hat a Oracle Linux](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/deployment_guide/sec-printer_configuration) o tom, jak přidat tiskárnu.
>
>

---
![Tisk v síti][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Tiskárna založená na hostiteli přes SMB (sdílená tiskárna) ve scénáři mezi místními místy

Tiskárny založené na hostitelích nejsou návrhem kompatibilním se sítí. Tiskárnu založenou na hostiteli ale můžete sdílet mezi počítači v síti, pokud je tiskárna připojená k počítači, na kterém je zapnutý. Připojte svou podnikovou síť k síti nebo ExpressRoute a sdílejte ji na místní tiskárně. Protokol SMB používá namísto služby DNS jako názvových služeb rozhraní NetBIOS. Název hostitele pro rozhraní NetBIOS se může lišit od názvu hostitele DNS. Standardním případem je, že název hostitele rozhraní NetBIOS a název hostitele DNS jsou identické. Doména DNS nesmysluje v oboru názvů NetBIOS. Proto se v názvovém prostoru NetBIOS nesmí používat plně kvalifikovaný název hostitele DNS, který se skládá z názvu hostitele DNS a domény DNS.

Sdílená složka tiskárny je v síti identifikována jedinečným názvem:

* Název hostitele pro hostitele SMB (vždy potřeba)
* Název sdílené složky (vždy je potřeba).
* Název domény, Pokud sdílená tiskárna není ve stejné doméně jako systém SAP.
* Kromě toho může být vyžadováno uživatelské jméno a heslo pro přístup ke sdílení tiskárny.

Postup:

---
> ![Windows][Logo_Windows] Windows
>
> Sdílejte místní tiskárnu.
> Na virtuálním počítači Azure otevřete Průzkumníka Windows a zadejte název sdílené složky tiskárny.
> Průvodce instalací tiskárny vás provede procesem instalace.
>
> ![Linux][Logo_Linux] Linux
>
> Tady jsou některé příklady dokumentace týkající se konfigurace síťových tiskáren v systému Linux nebo včetně kapitoly týkající se tisku v systému Linux. Bude fungovat stejným způsobem jako virtuální počítač Azure Linux, pokud je virtuální počítač součástí sítě VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL nebo Oracle Linux <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/system_administrators_guide/index#sec-Starting_Print_Settings_Config>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>Tiskárna USB (přesměrování tiskárny)

V Azure schopnost vzdálené plochy poskytovat uživatelům přístup k místním zařízením tiskáren ve vzdálené relaci není k dispozici.

---
> ![Windows][Logo_Windows] Windows
>
> Další podrobnosti o tisku v systému Windows najdete tady: <https://technet.microsoft.com/library/jj590748.aspx> .
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integrace systémů SAP Azure do systému pro opravy a přenosové systémy (TMS) ve více místech

Systém SAP Change and Transporting (TMS) musí být nakonfigurovaný tak, aby vyexportovali a importoval požadavky na přenos napříč systémy na šířku. Předpokládáme, že vývojové instance systému SAP (DEV) se nacházejí v Azure, zatímco služba zabezpečování kvality (QA) a produktivní systémy (PRD) jsou místní. Dále předpokládáme, že existuje centrální adresář pro přenos.

##### <a name="configuring-the-transport-domain"></a>Konfigurace domény přenosu

Nakonfigurujte svou doménu přenosu v systému, který jste označili jako transportní řadič domény, jak je popsáno v tématu [Konfigurace přenosového řadiče domény](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Vytvoří se systémový uživatel TMSADM a bude vygenerován požadovaný cíl RFC. Tato připojení RFC můžete kontrolovat pomocí SM59 transakce. V rámci vaší domény přenosu musí být povoleno rozlišení názvu hostitele.

Postup:

* V našem scénáři jsme se rozhodli, že místní systém QAS bude řadičem domény CTS. Zavolejte STMS transakce. Zobrazí se dialogové okno organizace TMS. Zobrazí se dialogové okno Konfigurovat doménu přenosu. (Toto dialogové okno se zobrazí pouze v případě, že jste ještě nenakonfigurovali doménu přenosu.)
* Zajistěte, aby byl automaticky vytvořený uživatel TMSADM autorizovaný (SM59-> ABAP Connection-> TMSADM@E61.DOMAIN_E61 >-Details-> Utilities (M) – > autorizační test). Úvodní obrazovka transakčního STMS by měla Ukázat, že tento systém SAP teď funguje jako řadič domény přenosu, jak je znázorněno zde:

![Úvodní obrazovka transakce STMS na řadiči domény][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Zahrnutí systémů SAP v doméně přenosu

Sekvence zahrnutí systému SAP v doméně přenosu vypadá takto:

* V systému pro vývoj v Azure se přečtěte do přenosového systému (klient 000) a zavolejte STMS transakce. V dialogovém okně vyberte jinou konfiguraci a pokračujte zahrnutým systémem v doméně. Zadejte řadič domény jako cílového hostitele ([včetně systémů SAP v doméně přenosu](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Systém nyní čeká na zahrnutí v doméně přenosu.
* Z bezpečnostních důvodů pak musíte přejít zpět na řadič domény a žádost potvrdit. Vyberte Přehled systému a schvalte čekající systém. Pak potvrďte výzvu a konfigurace bude distribuována.

Tento systém SAP teď obsahuje informace potřebné pro všechny ostatní systémy SAP v doméně přenosu. Data o novém systému SAP se ve stejnou dobu odesílají do všech ostatních systémů SAP a systém SAP se zadává do přenosového profilu programu řízení přenosů. Ověřte, jestli jsou dokumenty RFC a přístup k adresáři přenosu práce v doméně.

Pokračujte v konfiguraci přenosového systému obvyklým způsobem, jak je popsáno v dokumentaci pro [změnu a přenos systému](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Postup:

* Ujistěte se, že je váš STMS místní konfigurace správně nakonfigurovaný.
* Ujistěte se, že se název hostitele přenosového řadiče domény dá vyřešit vaším virtuálním počítačem v Azure a naopak.
* Volání transakce STMS-> jiný konfigurační > systém zahrnuje v doméně.
* Potvrďte připojení v místním systému TMS.
* Konfigurujte trasy přenosů, skupiny a vrstvy obvyklým způsobem.

V případě propojení mezi místními prostředími mezi lokalitami může být latence mezi místními a Azure pořád značná. Pokud budeme postupovat podle sekvence přenosů objektů pomocí vývojových a testovacích systémů do produkčního prostředí, nebo si myslíte, že se bude používat přenosové a podpůrné balíčky v různých systémech, zjistíte, že závisí na umístění centrálního transportního adresáře, ale u některých systémů dojde ke vysoké latenci při čtení nebo zápisu dat do centrálního adresáře přenosu. Tato situace je podobná konfiguracím SAP na šířku, kde jsou různé systémy rozloženy prostřednictvím různých datových center s významnou vzdáleností mezi datovými centry.

Aby bylo možné tuto latenci obejít a tyto systémy budou rychle fungovat při čtení nebo zápisu do nebo z adresáře přenosu, můžete nastavit dvě STMS dopravních domén (jeden pro místní a druhý se systémy v Azure a propojit domény přenosu. Podívejte se na tuto dokumentaci, která vysvětluje principy tohoto konceptu v tématu SAP TMS: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm> .

Postup:

* Nastavení domény přenosu v každém umístění (místně a Azure) pomocí transakčního STMS <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Propojte domény s propojením domény a potvrďte propojení mezi těmito dvěma doménami.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuujte konfiguraci do odkazovaného systému.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Přenos RFC mezi instancemi SAP umístěnými v Azure a místním prostředím (mezi místními počítači)

Data RFC mezi systémy, které jsou místní a v Azure, musí fungovat. Pro nastavení transakce volání připojení SM59 ve zdrojovém systému, ve kterém potřebujete definovat připojení RFC k cílovému systému. Konfigurace je podobná standardnímu nastavení připojení RFC.

Předpokládáme, že ve scénáři mezi místními počítači jsou virtuální počítače, které spouštějí systémy SAP, které musí vzájemně komunikovat, ve stejné doméně. Proto se nastavení připojení RFC mezi systémy SAP neliší od kroků instalace a vstupů v místních scénářích.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Přístup k místním sdíleným složkám z instancí SAP, které se nacházejí v Azure, nebo naopak

Instance SAP umístěné v Azure potřebují přístup ke sdíleným složkám, které se nacházejí v podnikových podnicích. Kromě toho musí místní instance SAP přistupovat ke sdíleným složkám, které se nacházejí v Azure. Pokud chcete sdílené složky povolit, musíte nakonfigurovat možnosti oprávnění a sdílení v místním systému. Nezapomeňte otevřít porty na připojení VPN nebo ExpressRoute mezi Azure a vaším datovým centrem.

## <a name="supportability"></a>Možnosti podpory

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Rozšíření Azure pro SAP

Aby bylo možné podávat určitou část informací o infrastruktuře Azure pro důležité systémy SAP do instancí agenta hostitele SAP instalovaných na virtuálních počítačích, musí být pro nasazené virtuální počítače nainstalovaná přípona Azure (VM) pro SAP. Vzhledem k tomu, že požadavky podle SAP byly specifické pro aplikace SAP, společnost Microsoft se rozhodla, že do Azure neimplementuje obecně implementaci požadovaných funkcí, ale ponechá zákazníkům, aby nasadili potřebné rozšíření a konfigurace virtuálního počítače na jejich Virtual Machines spuštěné v Azure. Nicméně Správa nasazení a životního cyklu rozšíření virtuálního počítače Azure pro SAP bude většinou automatizovaná Azure.

#### <a name="solution-design"></a>Návrh řešení

Řešení vyvinuté za účelem povolení agenta hostitele SAP získání požadovaných informací vychází z architektury agenta virtuálního počítače Azure a architektury rozšíření. Cílem agenta virtuálního počítače Azure a rozhraní rozšíření je dovolit instalaci softwarových aplikací, které jsou k dispozici v galerii rozšíření virtuálních počítačů Azure v rámci virtuálního počítače. Principem tohoto pojmu je povolení (v případech, jako je rozšíření Azure pro SAP), nasazení zvláštních funkcí do virtuálního počítače a konfigurace takového softwaru v době nasazení.

Agent virtuálního počítače Azure, který umožňuje zpracování specifických rozšíření virtuálních počítačů Azure v rámci virtuálního počítače, se ve výchozím nastavení vloží do virtuálních počítačů s Windows při vytváření virtuálních počítačů v Azure Portal. V případě SUSE, Red Hat nebo Oracle Linux je agent virtuálního počítače už součástí Azure Marketplace image. V případě, že jeden z nich nahraje virtuální počítač Linux z místního prostředí do Azure, musí být agent virtuálního počítače nainstalovaný ručně.

Základní stavební bloky řešení, které poskytují informace o infrastruktuře Azure pro hostitele SAP v Azure, vypadají takto:

![Komponenty rozšíření Microsoft Azure][planning-guide-figure-2400]

Jak je znázorněno na obrázku blokový diagram výše, jedna část řešení je hostována v imagi virtuálního počítače Azure a v galerii rozšíření Azure, což je globálně replikované úložiště spravované operacemi Azure. Je zodpovědností společného týmu SAP/MS pracujícího na implementaci SAP pro Azure pro práci s operacemi Azure pro publikování nových verzí rozšíření Azure pro SAP.

Když nasadíte nový virtuální počítač s Windows, agent virtuálního počítače Azure se automaticky přidá do virtuálního počítače. Funkce tohoto agenta slouží k koordinaci načítání a konfigurace rozšíření Azure pro virtuální počítače. V případě virtuálních počítačů se systémem Linux je agent virtuálního počítače Azure již součástí bitové kopie Azure Marketplace operačního systému.

Existuje však krok, který je stále potřeba provést zákazníkem. Toto je povolení a konfigurace shromažďování výkonu. Proces související s konfigurací je automatizovaný pomocí skriptu PowerShellu nebo příkazu CLI. Skript PowerShellu se dá stáhnout do centra skriptů Microsoft Azure, jak je popsáno v [Průvodci nasazením][deployment-guide].

Celková architektura rozšíření Azure pro SAP vypadá takto:

![Rozšíření Azure pro SAP ][planning-guide-figure-2500]

**Přesný postup a podrobné pokyny k používání těchto rutin PowerShellu nebo příkazu CLI během nasazení najdete v pokynech uvedených v [Průvodci nasazením][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integrace Azure umístěné instance SAP do SAProuter

Instance SAP běžící v Azure musí být dostupné i z SAProuter.

![Síťové připojení ke směrovači SAP][planning-guide-figure-2600]

SAProuter umožňuje komunikaci TCP/IP mezi zúčastněnými systémy, pokud není k dispozici žádné přímé připojení IP. Díky tomu je zajištěno, že žádné koncové spojení mezi komunikačními partnery není nutné na úrovni sítě. SAProuter ve výchozím nastavení naslouchá na portu 3299.
Pokud chcete spojit instance SAP prostřednictvím SAProuter, musíte zadat řetězec SAProuter a název hostitele s jakýmkoli pokusem o připojení.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver jako Java

Zatím byl zaměření dokumentu SAP NetWeaver v obecném nebo v zásobníku SAP NetWeaver ABAP. V této malé části jsou uvedeny konkrétní předpoklady pro sadu SAP Java Stack. Jednou z nejdůležitějších aplikací založených na systému SAP NetWeaver Java je Enterprise Portal SAP. Jiné aplikace založené na SAP NetWeaver jako SAP PI a SAP Solution Manager používají zásobníky SAP NetWeaver ABAP a Java. Proto je potřeba vzít v úvahu také konkrétní aspekty týkající se nástroje SAP NetWeaver Java Stack.

### <a name="sap-enterprise-portal"></a>Enterprise Portal SAP

Nastavení portálu SAP na virtuálním počítači Azure se neliší od instalace v místním prostředí, pokud nasazujete ve scénářích mezi místními místy. Vzhledem k tomu, že se DNS provádí v místním prostředí, můžete nastavení portů jednotlivých instancí provést místně. Doporučení a omezení popsaná v tomto dokumentu se zatím vztahují na aplikaci, jako je SAP Enterprise Portal, nebo v části obecná sada pro SAP NetWeaver Java.

![Vystavený portál SAP][planning-guide-figure-2700]

Speciálním scénářem nasazení některými zákazníky je přímá angažovanost Enterprise Portal SAP k Internetu, zatímco je hostitel virtuálního počítače připojený k podnikové síti prostřednictvím tunelového připojení VPN typu Site-to-site nebo ExpressRoute. V takovém případě je nutné se ujistit, že konkrétní porty jsou otevřené a nejsou blokované bránou firewall nebo skupinou zabezpečení sítě.

Počáteční identifikátor URI portálu je http (s): `<Portalserver`>:5XX00/irj, kde je port vytvořený pomocí SAP v <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm> .

![Konfigurace koncového bodu][planning-guide-figure-2800]

Pokud chcete přizpůsobit adresu URL a porty Enterprise Portal SAP, podívejte se na tuto dokumentaci:

* [Změnit adresu URL portálu](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Změna výchozích čísel portů, čísel portů portálu](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Vysoká dostupnost (HA) a zotavení po havárii (DR) pro SAP NetWeaver běžící na Azure Virtual Machines

### <a name="definition-of-terminologies"></a>Definice terminologií

Termín **vysoké dostupnosti (ha)** se obecně týká sady technologií, která minimalizuje přerušení provozu tím, že poskytuje provozní kontinuitu IT služeb prostřednictvím redundantních, odolných nebo převzetí služeb při selhání v rámci **stejného** datového centra. V našem případě v rámci jedné oblasti Azure.

**Zotavení po havárii (Dr)** se taky zaměřuje na minimalizaci přerušení služeb IT a jejich obnovení, ale napříč **různými** datovými centry, které jsou obvykle umístěné stovky kilometrů. V našem případě obvykle mezi různými oblastmi Azure v rámci stejné geopolitické oblasti nebo vámi vytvořeným jako zákazníkem.

### <a name="overview-of-high-availability"></a>Přehled vysoké dostupnosti

Diskuzi o vysoké dostupnosti SAP v Azure můžeme rozdělit do dvou částí:

* **Infrastruktura Azure s vysokou dostupností**, například ha pro výpočetní prostředky (virtuální počítače), síť, úložiště atd. a její výhody při zvyšování dostupnosti aplikace SAP.
* **Vysoká dostupnost aplikace SAP**, například ha softwarových komponent SAP:
  * Aplikační servery SAP
  * Instance SAP ASCS/SCS
  * Server DB

a jak se dá kombinovat s infrastrukturou Azure na vysokou dostupnost.

Při vysoké dostupnosti SAP v Azure jsou některé rozdíly v porovnání s vysokou dostupností v místním fyzickém nebo virtuálním prostředí. Následující dokument od SAP popisuje standardní konfigurace SAP s vysokou dostupností ve virtualizovaných prostředích ve Windows: <https://scn.sap.com/docs/DOC-44415> . Neexistuje žádná konfigurace SAP-HA integrovaná s sapinst pro Linux, stejně jako pro Windows. Informace o SAP HA v místním prostředí pro Linux najdete tady: <https://scn.sap.com/docs/DOC-8541> .

### <a name="azure-infrastructure-high-availability"></a>Vysoká dostupnost infrastruktury Azure

V současné době platí jedna z virtuálních počítačů SLA 99,9%. Pokud chcete zjistit, jak může vypadat dostupnost jednoho virtuálního počítače, můžete si vytvořit produkt z různých dostupných SLA Azure: <https://azure.microsoft.com/support/legal/sla/> .

Základem pro výpočet je 30 dní za měsíc nebo 43200 minut. Proto 0,05% výpadků odpovídá 21,6 minut. V obvyklých případech bude dostupnost různých služeb vynásobena následujícím způsobem:

(Služba dostupnosti #1/100) * (služba dostupnosti #2/100) * (služba dostupnosti #3/100)

Jako

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 nebo Celková dostupnost 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Vysoká dostupnost virtuálního počítače (VM)

Existují dva typy událostí platformy Azure, které mohou ovlivnit dostupnost virtuálních počítačů: plánovaná údržba a neplánovaná údržba.

* Plánované události údržby jsou pravidelné aktualizace od Microsoftu k základní platformě Azure za účelem zlepšení celkové spolehlivosti, výkonu a zabezpečení infrastruktury platformy, na které běží vaše virtuální počítače.
* Neplánované události údržby nastávají v případě, že dojde k nějakému selhání hardwaru nebo základní fyzické infrastruktury, na které stojí virtuální počítač. To může zahrnovat selhání místní sítě, selhání místního disku nebo další selhání na úrovni racku. Když se takové selhání detekuje, platforma Azure automaticky migruje virtuální počítač z nesprávného fyzického serveru, který hostuje váš virtuální počítač, na dobrý fyzický server. Takové události se vyskytují jen vzácně, ale také můžou způsobit restartování vašeho virtuálního počítače.

Další podrobnosti najdete v tématu [dostupnost virtuálních počítačů s Windows v Azure](../../windows/manage-availability.md) a [dostupnosti virtuálních počítačů se systémem Linux v Azure](../../linux/manage-availability.md).

#### <a name="azure-storage-redundancy"></a>Azure Storage redundance

Data ve vašem účtu Microsoft Azure Storage se vždycky replikují, aby se zajistila odolnost a vysoká dostupnost, a to na základě Azure Storage smlouvy SLA, a to i v případě přechodných selhání hardwaru.

Vzhledem k tomu, že Azure Storage udržuje tři image dat ve výchozím nastavení, není nutné, aby se RAID5 nebo RAID1 na více discích Azure.

Další podrobnosti najdete v tématu [Azure Storage redundance](../../../storage/common/storage-redundancy.md).

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Využití restartování virtuálních počítačů infrastruktury Azure k dosažení vyšší dostupnosti aplikací SAP

Pokud se rozhodnete, že nebudete používat funkce jako Windows Server Failover Clustering (WSFC) nebo Pacemaker v systému Linux (aktuálně se podporuje jenom pro SLES 12 a novější), používá se restart virtuálního počítače Azure k ochraně systému SAP proti plánovanému a neplánovanému výpadku infrastruktury fyzického serveru Azure a celkové základní platformy Azure.

> [!NOTE]
> Je důležité si zmínit, že restartování virtuálního počítače Azure primárně chrání virtuální počítače a ne aplikace. Restartování virtuálního počítače nenabízí vysokou dostupnost pro aplikace SAP, ale nabízí určitou úroveň dostupnosti infrastruktury, a proto nepřímo vyšší dostupnost systémů SAP. Pro čas, kdy bude trvat restartování virtuálního počítače po plánovaném nebo neplánovaném výpadku hostitele, neexistuje žádná smlouva SLA. Proto tato metoda vysoké dostupnosti není vhodná pro kritické součásti systému SAP, jako je (A) SCS nebo DBMS.
>
>

Dalším důležitým prvkem infrastruktury pro vysokou dostupnost je úložiště. Například Azure Storage SLA je 99,9% dostupnost. Pokud jeden z nich nasadí všechny virtuální počítače s disky do jednoho Azure Storage účtu, potenciální Azure Storage nedostupnost způsobí nedostupnost všech virtuálních počítačů, které jsou umístěné v daném Azure Storage účtu, a také všechny součásti SAP běžící v těchto virtuálních počítačích.

Místo vložení všech virtuálních počítačů do jednoho jediného Azure Storage účtu můžete pro každý virtuální počítač použít taky vyhrazené účty úložiště a tímto způsobem zvýšit dostupnost všech virtuálních počítačů a aplikací SAP pomocí víc nezávislých Azure Storage účtů.

Služby Azure Managed disks se automaticky umístí do domény selhání virtuálního počítače, ke kterému jsou připojené. Pokud nasadíte dva virtuální počítače do skupiny dostupnosti a použijete Managed Disks, platforma se postará o distribuci Managed Disks do různých domén selhání. Pokud plánujete použít Premium Storage, důrazně doporučujeme použít také správu disků.

Ukázková architektura systému SAP NetWeaver, který využívá infrastrukturu Azure HA a účty úložiště, může vypadat takto:

![Využití infrastruktury Azure HA k dosažení vyšší dostupnosti aplikace SAP][planning-guide-figure-2900]

Ukázková architektura systému SAP NetWeaver, který používá infrastrukturu Azure HA a Managed Disks, může vypadat takto:

![Využití infrastruktury Azure HA k dosažení vyšší dostupnosti aplikace SAP][planning-guide-figure-2901]

V případě kritických komponent SAP jsme dosáhli následujících možností:

* Vysoká dostupnost aplikačních serverů SAP (jako)

  Instance aplikačního serveru SAP jsou redundantní součásti. Každá instance SAP AS je nasazená na vlastním virtuálním počítači, který běží v jiné chybě Azure a upgradovací doména (viz téma kapitoly [selhání domény][planning-guide-3.2.1] a [upgradované][planning-guide-3.2.2]domény). To se zajišťuje pomocí skupin dostupnosti Azure (viz kapitola [Azure Availability Sets][planning-guide-3.2.3]). Potenciální plánovaná nebo neplánovaná neplánovaná nebo neplánovaná neplánovaná chyba Azure nebo upgradovací doména způsobí nedostupnost omezeného počtu virtuálních počítačů s jejich SAP jako instancemi.

  Každá instance SAP AS je umístěná na svém vlastním účtu Azure Storage – potenciální nedostupnost jednoho Azure Storageho účtu způsobí nedostupnost jednoho virtuálního počítače s jeho SAP jako instancí. Upozorňujeme však, že v rámci jednoho předplatného Azure existuje omezení Azure Storagech účtů. Chcete-li zajistit automatické spuštění (A) instance SCS po restartování virtuálního počítače, nezapomeňte nastavit parametr autostart v (A) SCS instance Start Profile popsaný v kapitole [použití autostart pro instance SAP][planning-guide-11.5].
  Další podrobnosti najdete také v části [Vysoká dostupnost pro aplikační servery SAP][planning-guide-11.4.1] .

  I když použijete Managed Disks, tyto disky se také ukládají v účtu Azure Storage a v případě výpadku úložiště můžou být nedostupné.

* *Vyšší* Dostupnost instance SAP (A) SCS

  Tady využijeme restart virtuálního počítače Azure pro ochranu virtuálního počítače s nainstalovanou instancí SAP (A) SCS. V případě plánovaného nebo neplánovaného výpadku severů Azure se virtuální počítače restartují na jiném dostupném serveru. Jak bylo zmíněno dříve, restartování virtuálního počítače Azure primárně chrání virtuální počítače a ne aplikace, v tomto případě instanci SCS (A). Při restartování virtuálního počítače budeme k dispozici nepřímo vyšší dostupnost instance SAP (A) SCS. Chcete-li po restartování virtuálního počítače SCS automatické spuštění instance (A), nezapomeňte nastavit parametr autostart v (A) SCS instance Start Profile popsaný v kapitole [použití autostart pro instance SAP][planning-guide-11.5]. To znamená, že instance SCS jako selhání (SPOF) spuštěná na jednom virtuálním počítači bude determinative faktor pro dostupnost celého prostředí SAP na šířku.

* *Vyšší* Dostupnost serveru DBMS

  Podobně jako v případě použití instance SAP (A) SCS využíváme restart virtuálního počítače Azure k ochraně virtuálního počítače pomocí nainstalovaného softwaru DBMS a dosahuje vyšší dostupnosti softwaru DBMS prostřednictvím restartování virtuálního počítače.
  Systémy DBMS běžící na jednom virtuálním počítači jsou také SPOF a jedná se determinative faktor pro dostupnost celého prostředí SAP na šířku.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Vysoká dostupnost aplikace SAP v Azure IaaS

Abychom dosáhli plné dostupnosti systému SAP, musíme chránit všechny důležité součásti systému SAP, například redundantní aplikační servery SAP a jedinečné součásti (například jediný bod selhání), jako je například instance SAP (A) SCS a DBMS.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Vysoká dostupnost pro aplikační servery SAP

Pro instance aplikačních serverů SAP a dialogů není nutné považovat za konkrétní řešení vysoké dostupnosti. Vysoká dostupnost se dosahuje redundancí a díky nim je dostatečně velká v různých virtuálních počítačích. Měli byste je umístit do stejné skupiny dostupnosti Azure, abyste se vyhnuli tomu, že se virtuální počítače můžou během plánovaného výpadku údržby aktualizovat současně. Základní funkce, které se vytvářejí v různých doménách upgradu a selhání v rámci jednotky škálování Azure, se už zavedly v kapitolách [upgradu][planning-guide-3.2.2]. Skupiny dostupnosti Azure se provedly v kapitolách dostupnosti tohoto dokumentu pro [Azure][planning-guide-3.2.3] .

Neexistuje žádný neomezený počet domén selhání a upgradu, které by mohly být používány sadou dostupnosti Azure v rámci jednotky škálování Azure. To znamená, že vložení řady virtuálních počítačů do jedné skupiny dostupnosti dřív nebo později více než jeden virtuální počítač končí ve stejné chybě nebo doméně upgradu.

Nasazení několika instancí aplikačního serveru SAP ve svých vyhrazených virtuálních počítačích a za předpokladu, že máme pět domén upgradu, na konci se objeví následující obrázek. Skutečný maximální počet domén selhání a aktualizačních domén v rámci skupiny dostupnosti se může v budoucnu změnit:

![HA aplikačních serverů SAP v Azure][planning-guide-figure-3000]


#### <a name="high-availability-for-sap-central-services-on-azure"></a>Vysoká dostupnost pro centrální služby SAP v Azure

V případě architektury vysoké dostupnosti služby SAP Central Services v Azure se podívejte na článek [architektura s vysokou dostupností a scénáře pro SAP NetWeaver](./sap-high-availability-architecture-scenarios.md) jako informace o položkách. Článek odkazuje na podrobnější popis pro konkrétní operační systémy.

#### <a name="high-availability-for-the-sap-database-instance"></a>Vysoká dostupnost instance databáze SAP

Typický instalační program SAP DBMS je založený na dvou virtuálních počítačích s DBMS, kde se k replikaci dat z aktivní instance systému DBMS na druhý virtuální počítač do pasivní instance DBMS používá funkce pro vysokou dostupnost systému DBMS.

V obecné [příručce pro nasazení][dbms-guide]systému DBMS jsou popsány funkce vysoká dostupnost a zotavení po havárii pro systémy DBMS a také konkrétní systémy DBMS.

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Koncová vysoká dostupnost pro kompletní systém SAP

Tady jsou dva příklady kompletní architektury SAP NetWeaver HA v Azure – jeden pro Windows a jeden pro Linux.

Jenom nespravované disky: Pokud nasazujete mnoho systémů SAP a počet nasazených virtuálních počítačů překračuje maximální limit účtů úložiště na předplatné, může být potřeba napadnout nějaký koncept, jak je vysvětleno níže. V takových případech je potřeba, aby se virtuální pevné disky virtuálních počítačů spojily v rámci jednoho účtu úložiště. Obvykle to provedete tak, že zkombinujete virtuální pevné disky virtuálních počítačů SAP aplikační vrstvy různých systémů SAP.  V jednom účtu Azure Storage jsme také spojili různé virtuální pevné disky různých virtuálních počítačů s DBMS různých systémů SAP. Omezení počtu IOPS Azure Storagech účtů tak, aby se zachovává ( <https://azure.microsoft.com/documentation/articles/storage-scalability-targets> )


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] HA ve Windows

![Architektura HA aplikace SAP NetWeaver s SQL Server v Azure IaaS][planning-guide-figure-3200]

Následující konstrukce Azure se používají pro systém SAP NetWeaver, aby se minimalizoval dopad na problémy infrastruktury a opravy hostitele:

* Úplný systém je nasazený v Azure (požadováno-DBMS vrstva, (A) SCS instance a kompletní aplikační vrstva musí běžet ve stejném umístění).
* Úplný systém běží v rámci jednoho předplatného Azure (povinné).
* Úplný systém běží v rámci jednoho Virtual Network Azure (povinné).
* Oddělení virtuálních počítačů jednoho systému SAP do tří skupin dostupnosti je možné i u všech virtuálních počítačů patřících ke stejnému Virtual Network.
* Každá vrstva (například DBMS, ASCS, aplikační servery) musí používat vyhrazenou skupinu dostupnosti.
* Všechny virtuální počítače se systémem DBMS instance jednoho systému SAP jsou v jedné skupině dostupnosti. Předpokládáme, že existuje více než jeden virtuální počítač, na kterém běží instance DBMS, protože se používají nativní funkce pro vysokou dostupnost systému DBMS, jako je například SQL Server AlwaysOn nebo Oracle data Guard.
* Všechny virtuální počítače s instancemi DBMS používají vlastní účet úložiště. Data a soubory protokolů DBMS se replikují z jednoho účtu úložiště do jiného účtu úložiště pomocí funkcí pro zajištění vysoké dostupnosti systému DBMS, které data synchronizují. Nedostupnost jednoho účtu úložiště způsobí nedostupnost jednoho uzlu clusteru SQL systému SQL, ale ne celou službu SQL Server.
* Všechny virtuální počítače se spuštěným SCS instancí jednoho systému SAP jsou v jedné skupině dostupnosti. V těchto virtuálních počítačích je nakonfigurovaný cluster Windows Server failover cluster (WSFC), aby se chránila instance SCS (A).
* Všechny virtuální počítače se spuštěným SCS instancemi používají vlastní účet úložiště. Určitého Soubory SCS instance a globální složka SAP se replikují z jednoho účtu úložiště do jiného účtu úložiště pomocí s replikací DataKeeper. Nedostupnost jednoho účtu úložiště způsobí nedostupnost jednoho (A) SCS uzlu clusteru Windows, ale ne celou službu (A) SCS.
* VŠECHNY virtuální počítače představující vrstvu aplikačního serveru SAP jsou ve třetí skupině dostupnosti.
* VŠECHNY virtuální počítače, na kterých běží aplikační servery SAP, používají vlastní účet úložiště. Nedostupnost jednoho účtu úložiště způsobí nedostupnost jednoho aplikačního serveru SAP, kde ostatní aplikační servery SAP nadále běží.

Následující obrázek znázorňuje stejnou šířku pomocí Managed Disks.

![Architektura HA aplikace SAP NetWeaver s SQL Server v Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] HA v systému Linux

Architektura pro SAP HA v systému Linux v Azure je v podstatě stejná jako pro Windows, jak je popsáno výše. Seznam podporovaných řešení s vysokou dostupností najdete v tématu SAP Note [1928533] .

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Použití autostart pro instance SAP

SAP nabízí funkci pro spuštění instancí SAP ihned po spuštění operačního systému v rámci virtuálního počítače. Přesné kroky byly popsané v článku znalostní báze SAP znalostní báze [1909114]. SAP ale nedoporučuje používat toto nastavení, protože v pořadí restartování není žádné řízení, za předpokladu, že byl ovlivněn více než jeden virtuální počítač, nebo se na virtuální počítač spustilo víc instancí. Za předpokladu, že Typický scénář Azure jedné instance aplikačního serveru SAP na virtuálním počítači a v případě jednoho virtuálního počítače se nakonec znovu nepoužívá, není automatické spuštění důležité a dá se povolit přidáním tohoto parametru:

`Autostart = 1`

Do počátečního profilu instance SAP ABAP a/nebo Java.

> [!NOTE]
> Parametr autostart může mít také několik downfalls. Ve více podrobnostech parametr při spuštění související služby systému Windows/Linux spustí spuštění instance SAP ABAP nebo Java. V takovém případě se jedná o případ, kdy se operační systém spustí. Restartování služeb SAP je ale také běžné věcí pro funkce správy životního cyklu softwaru SAP, jako je součet nebo jiné aktualizace nebo upgrady. U těchto funkcí není očekáváno automatické restartování instance. Před spuštěním takových úloh by proto měl být parametr autostart zakázán. Parametr autostart by se neměl používat i pro instance SAP, které jsou clusterované, jako je ASCS/SCS/CI.
>
>

Další informace týkající se autostart pro instance SAP najdete tady:

* [Spuštění/zastavení SAP společně s vaším serverem UNIX spustit/zastavit](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Spouštění a zastavování agentů pro správu SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Jak povolit automatické spuštění databáze HANA](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Větší systémy SAP úrovně 3
Aspekty vysoce dostupné konfigurace SAP pro 3 vrstvy byly popsány v předchozích oddílech. Ale informace o systémech, ve kterých jsou požadavky na server DBMS moc velké, aby se nacházely v Azure, ale aplikační vrstva SAP by mohla být nasazená do Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Umístění konfigurací pro 3 vrstvy SAP
Pro rozdělení samotné aplikační vrstvy nebo aplikace a DBMS mezi místními prostředími a Azure se nepodporuje. Systém SAP je buď zcela nasazený místně nebo v Azure. Také se nepodporuje, aby některé aplikační servery běžely v místním prostředí a jiné v Azure. To je výchozí bod diskuze. Nepodporujeme také komponenty DBMS systému SAP a vrstvy aplikačního serveru SAP nasazené ve dvou různých oblastech Azure. Například DBMS v Západní USA a aplikační vrstva SAP v Střed USA. Důvodem pro nepodporu takových konfigurací je citlivost na latenci architektury SAP NetWeaver.

Nicméně v průběhu minulého roku se partneři pro datacentrum vyvinuli ve společném umístění do oblastí Azure. Tato spoluumístění jsou často v blízké blízkosti fyzických datových center Azure v oblasti Azure. Krátká vzdálenost a spojení prostředků ve společném umístění až po ExpressRoute do Azure může způsobit latenci, která je kratší než 2 milisekundy. V takových případech je možné najít vrstvu DBMS (včetně sítě SAN úložiště/NAS) v takovém společném umístění a aplikační vrstvě SAP v Azure. [Velké instance Hana](./hana-overview-architecture.md)

### <a name="offline-backup-of-sap-systems"></a>Offline zálohování systémů SAP
V závislosti na zvolené konfiguraci SAP (2 vrstva nebo 3 vrstva) může být potřeba zálohovat. Obsah samotného virtuálního počítače je navíc k zálohování databáze. Očekává se, že zálohy související se systémem DBMS budou provedeny s metodami databáze. Podrobný popis pro různé databáze najdete v [příručce pro DBMS][dbms-guide]. Na druhé straně lze data SAP zálohovat v režimu offline (včetně obsahu databáze), jak je popsáno v této části nebo online, jak je popsáno v následující části.

Zálohování offline by v podstatě vyžadovalo vypnutí virtuálního počítače prostřednictvím Azure Portal a kopii základního disku virtuálního počítače a všech připojených disků k virtuálnímu počítači. Tím se zachová bitová kopie tohoto virtuálního počítače a jeho přidruženého disku v časovém bodě. Doporučuje se zkopírovat zálohy do jiného účtu Azure Storage. Proto platí postup popsaný v kapitole [kopírování disků mezi Azure Storagemi účty][planning-guide-5.4.2] tohoto dokumentu.


Obnovení tohoto stavu by představovalo odstranění základního virtuálního počítače i původní disky základního virtuálního počítače a připojených disků, kopírování uložených disků do původního účtu úložiště nebo skupiny prostředků pro spravované disky a následného opětovného nasazení systému.
Tento článek ukazuje příklad skriptu tohoto procesu v prostředí PowerShell: <http://www.westerndevs.com/azure-snapshots/>

Nezapomeňte nainstalovat novou licenci SAP od obnovení zálohy virtuálního počítače, jak je popsáno výše. vytvoří nový hardwarový klíč.

### <a name="online-backup-of-an-sap-system"></a>Online zálohování systému SAP

Zálohování systému DBMS se provádí pomocí metod specifických pro systém DBMS, jak je popsáno v [příručce pro systém DBMS][dbms-guide].

Další virtuální počítače v rámci systému SAP se dají zálohovat pomocí funkce zálohování virtuálních počítačů Azure. Zálohování virtuálního počítače Azure je standardní metoda pro zálohování kompletního virtuálního počítače v Azure. Azure Backup ukládá zálohy v Azure a umožňuje obnovení virtuálního počítače znovu.

> [!NOTE]
> Od prosince 2015 používá zálohování virtuálních počítačů jedinečné ID virtuálního počítače, které se používá pro licencování SAP. To znamená, že obnovení ze zálohy virtuálního počítače vyžaduje instalaci nového licenčního klíče SAP, protože obnovený virtuální počítač se považuje za nový virtuální počítač a nejedná se o náhradu za dříve uloženou kopii.
>
> ![Windows][Logo_Windows] Windows
>
> Teoreticky, virtuální počítače, na kterých běží databáze, se dají zálohovat konzistentním způsobem, a to i v případě, že systém DBMS podporuje Windows VSS (služba Stínová kopie svazku <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx> ) jako například SQL Server.
> Upozorňujeme ale, že na základě záloh virtuálních počítačů Azure není možné obnovit databáze v čase. Proto je doporučení provádět zálohování databází s využitím funkcí DBMS, nemusíte přitom spoléhat na zálohování virtuálních počítačů Azure.
>
> Pokud se chcete seznámit se zálohováním virtuálních počítačů Azure, začněte tady: <https://docs.microsoft.com/azure/backup/backup-azure-vms> .
>
> Další možností je použití kombinace Microsoft Data Protection Manager nainstalované na virtuálním počítači Azure a Azure Backup k zálohování a obnovení databází. Další informace najdete tady: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction> .
>
> ![Linux][Logo_Linux] Linux
>
> V systému Linux není k dispozici žádný ekvivalent služby Windows VSS. Proto jsou možné pouze zálohy konzistentní se soubory, ale nikoli zálohy konzistentní s aplikacemi. Zálohování SAP DBMS by se mělo provádět pomocí funkcí DBMS. Systém souborů, který obsahuje data týkající se SAP, lze uložit například pomocí funkce tar, jak je popsáno zde: <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Web Azure jako DR pro produkci SAP krajiny

Od Mid 2014 se rozšíření pro různé komponenty v rámci technologie Hyper-V, System Center a Azure povolilo používání Azure jako DR pro virtuální počítače, které jsou na místním počítači založené na technologii Hyper-V.

Blog s podrobnostmi, jak nasadit toto řešení, je popsán zde: <https://docs.microsoft.com/archive/blogs/saponsqlserver/protecting-sap-solutions-with-azure-site-recovery> .

## <a name="summary"></a>Shrnutí

Klíčové body vysoké dostupnosti pro systémy SAP v Azure jsou:

* V tomto okamžiku nemůžete jediný bod selhání protokolu SAP zabezpečit přesně stejným způsobem, jako se dá provést v místních nasazeních. Důvodem je to, že clustery sdílených disků se ještě nedají v Azure sestavit bez použití softwaru třetí strany.
* Pro vrstvu DBMS je potřeba použít funkce DBMS, které nezávisí na technologii sdíleného disku clusteru. Podrobnosti jsou popsány v [příručce pro DBMS][dbms-guide].
* Abyste minimalizovali dopad problémů v rámci domén selhání v infrastruktuře Azure nebo údržbě hostitele, měli byste použít skupiny dostupnosti Azure:
  * Doporučuje se mít pro vrstvu aplikace SAP jednu skupinu dostupnosti.
  * Doporučuje se mít pro vrstvu DBMS systému SAP samostatnou skupinu dostupnosti.
  * Nedoporučuje se použít stejnou skupinu dostupnosti pro virtuální počítače s různými systémy SAP.
  * Doporučujeme použít Managed Disks Premium.
* Pro účely zálohování vrstvy SAP DBMS si Projděte [příručku pro DBMS][dbms-guide].
* Zálohování instancí dialogových oken SAP má malý smysl, protože je obvykle rychlejší pro opětovné nasazení jednoduchých instancí dialogových oken.
* Zálohování virtuálního počítače, který obsahuje globální adresář systému SAP a má všechny profily různých instancí, má smysl a měl by se provádět se službou Windows Backup, nebo například tar v systému Linux. Vzhledem k tomu, že existují rozdíly mezi systémy Windows Server 2008 (R2) a Windows Server 2012 (R2), které usnadňují zálohování pomocí novějších verzí Windows serveru, doporučujeme používat Windows Server 2012 (R2) jako hostovaný operační systém Windows.

## <a name="next-steps"></a>Další kroky
Přečtěte si články:

- [Nasazení Azure Virtual Machines pro SAP NetWeaver](./deployment-guide.md)
- [Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_general.md)
- [Konfigurace infrastruktury SAP HANA a operace v Azure](/- azure/virtual-machines/workloads/sap/hana-vm-operations)
