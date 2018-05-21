---
title: Nasazení virtuálních počítačů Azure pro SAP NetWeaver | Microsoft Docs
description: Informace o nasazení SAP software na virtuální počítače s Linuxem v Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.openlocfilehash: a8929cfd0a484cdcd1adbd3d2ead4fd36cc49cd3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Nasazení virtuálních počítačů Azure pro SAP NetWeaver
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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Nasazení virtuálních počítačů databázového systému Azure pro SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Ukládání do mezipaměti pro virtuální počítače a virtuální pevné disky)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Softwaru diskového pole RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktura RDBMS nasazení)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Vysoká dostupnost a zotavení po havárii s virtuálními počítači Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 nebo novější)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 a starších verzích)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Pomocí bitové kopie systému SQL Server v Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Obecné SQL Server pro SAP v Azure souhrnu)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Podrobnosti k systému SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Konfigurace úložiště)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Zálohování a obnovení)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Důležité informace o výkonu pro zálohování a obnovení)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Další)
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

[deployment-guide]:deployment-guide.md (Nasazení virtuálních počítačů Azure pro SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Prostředky SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Nasazení virtuálního počítače pomocí vlastní image)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scénář 1: Nasazení virtuálního počítače z Azure Marketplace pro SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scénář 2: Nasazení virtuálního počítače s vlastní image pro SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scénář 3: Přesunutí virtuálního počítače z místního virtuálního pevného disku není zobecněný Azure pomocí SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scénáře nasazení virtuálních počítačů pro SAP v Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Nasazení rutin prostředí Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Stahování a import rutiny prostředí PowerShell relevantní pro SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Připojit virtuální počítač k doméně místní - pouze v systému Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Prostředí Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Rozhraní příkazového řádku Azure)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Konfigurace rozšíření Azure rozšířené monitorování pro SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Kontrola připravenosti Azure rozšířené monitorování pro SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Kontrola stavu Azure monitorování infrastruktury)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Řešení potíží s Azure monitorování pro SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Konfigurace monitorování)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Konfigurace proxy serveru)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Kontroly a řešení potíží pro nastavení sledování začátku do konce)

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

[planning-guide]:planning-guide.md (Azure virtuálních počítačů, plánování a implementace pro SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Prostředky)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Vysoká dostupnost a zotavení po havárii pro SAP NetWeaver spuštěna ve virtuálních počítačích Azure)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Vysoká dostupnost pro SAP aplikační servery)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Pomocí automatické spuštění pro instance SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Jenom pro cloud - nasazení virtuálních počítačů v Azure bez závislosti na místní sítě zákazníka)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Mezi různými místy - nasazení jedné nebo více virtuálních počítačů SAP v Azure plně integrována do místní sítě)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Oblasti Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domén selhání)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domén upgradu)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Skupiny dostupnosti Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Koncept virtuální počítače Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Přesunutí virtuálního počítače z místního do Azure s diskem zobecněn)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Nasazení virtuálního počítače s konkrétní image zákazníka)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Příprava pro přesun virtuálního počítače z místního do Azure s diskem zobecněn)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Příprava pro nasazení virtuálního počítače s konkrétní image zákazníka pro SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Příprava na Azure virtuálních počítačů s SAP)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Rozdíl mezi diskem Azure a Azure bitovou kopii)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Nahrání virtuálního pevného disku z místního do Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopírování disků mezi účty Azure Storage)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Virtuální počítač nebo virtuální pevný disk strukturu pro nasazení SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Nastavení automatického připojení pro připojené disky)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Jeden virtuální počítač s SAP NetWeaver demo a školení scénář)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Koncepty čistě cloudové nasazení SAP instancí)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure monitorování řešení pro SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Spravované disky)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Sítě Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Úložiště: Microsoft Azure Storage a datové disky)

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Matice dostupnosti produktu SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
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
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Nasazení a správě virtuálních počítačů pomocí šablon Azure Resource Manageru a rozhraní příkazového řádku Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Správa virtuálních počítačů pomocí Azure Resource Manageru a prostředí PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
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

Virtuální počítače Azure je řešení pro organizace, které potřebují výpočetní a úložnou kapacitu, minimální včas a bez zdlouhavé nákup cykly. Virtuální počítače Azure můžete použít k nasazení klasického aplikací, jako jsou aplikace založené na SAP NetWeaver v Azure. Rozšiřte spolehlivosti a dostupnosti bez dalších místních prostředků aplikace. Virtuální počítače Azure podporuje připojení mezi různými místy, takže virtuální počítače Azure můžete integrovat do místní domény vaší organizace, privátních cloudů a šířku systému SAP.

V tomto článku jsme zahrnují kroky k nasazení aplikací SAP na virtuálních počítačích (VM) v Azure, včetně možnosti alternativní nasazení a řešení potíží. Tento článek vychází informace v [virtuální počítače Azure plánování a implementace pro SAP NetWeaver][planning-guide]. Také doplňuje SAP instalace dokumentace a poznámky k SAP, které jsou primární prostředky pro instalaci a nasazení SAP softwaru.

## <a name="prerequisites"></a>Požadavky
Nastavení virtuálního počítače Azure pro nasazení softwaru SAP zahrnuje několik kroků a prostředky. Než začnete, ujistěte se, že splňujete požadavky pro instalaci softwaru SAP na virtuálních počítačích v Azure.

### <a name="local-computer"></a>Místní počítač
Ke správě systému Windows nebo Linux virtuálních počítačů, můžete použít skript prostředí PowerShell a portálu Azure. Pro oba nástroje budete potřebovat místní počítač se systémem Windows 7 nebo novější verzi systému Windows. Pokud chcete spravovat pouze virtuální počítače s Linuxem a chcete pro tuto úlohu použít počítač se systémem Linux, můžete použít rozhraní příkazového řádku Azure.

### <a name="internet-connection"></a>Připojení k Internetu
Ke stažení a spuštění nástroje a skripty, které jsou požadovány pro nasazení softwaru SAP, musí být připojen k Internetu. Virtuální počítač Azure, který běží Azure rozšířené monitorování rozšíření pro SAP také potřebuje přístup k Internetu. Pokud virtuální počítač Azure je součástí virtuální síť Azure nebo místní domény, ujistěte se, že jsou nastavené příslušné nastavení serveru proxy, jak je popsáno v [proxy server nakonfigurovat][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Předplatné Microsoft Azure
Potřebujete aktivní účet Azure.

### <a name="topology-and-networking"></a>Topologie a sítě
Je třeba definovat topologii a architektura nasazení SAP v Azure:

* Účty úložiště Azure, který se má použít
* Virtuální síť, ve které chcete nasazení systému SAP
* Skupina prostředků, do které chcete nasadit systému SAP
* Oblast Azure, ve které chcete nasazení systému SAP
* Konfigurace SAP (dvouvrstvá nebo třívrstvá)
* Velikosti virtuálních počítačů a počet dalších datových disků, které chcete připojit k virtuálním počítačům
* Konfigurace SAP oprava a přenosu systému (CTS)

Vytvořit a nakonfigurovat účty úložiště Azure (v případě potřeby) nebo virtuální sítě Azure, před zahájením procesu nasazení softwaru SAP. Informace o tom, jak vytvořit a nakonfigurovat těchto prostředků najdete v tématu [virtuální počítače Azure plánování a implementace pro SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Nastavení velikosti SAP
Následující informace pro nastavení velikosti SAP vědět:

* Předpokládané úlohy SAP, například pomocí nástroje pro rychlé přizpůsobení velikosti symbolů SAP a SAP aplikace výkonu standardní (přístupové body) číslo
* Požadovaný prostředek a paměti spotřeby procesoru systému SAP
* Požadované vstupní a výstupní (I/O) operace za sekundu
* Požadované šířky pásma sítě případné komunikace mezi virtuálními počítači v Azure
* Požadované šířku pásma sítě mezi místními prostředky a v systému SAP nasazení Azure

### <a name="resource-groups"></a>Skupiny prostředků
V Azure Resource Manager, můžete použít skupin prostředků ke správě všechny prostředky aplikace ve vašem předplatném Azure. Další informace najdete v tématu [přehled Azure Resource Manageru][resource-group-overview].

## <a name="resources"></a>Zdroje a prostředky

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Prostředky SAP
Pokud nastavujete nasazení SAP softwaru, je třeba na následujících odkazech SAP:

* Poznámka SAP [1928533], na kterém je:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporovány pro nasazení softwaru SAP
  * Kapacita důležité informace o velikosti virtuálního počítače Azure
  * Podporované SAP software a operační systém (OS) a kombinace databáze
  * Požadovaná verze SAP jádra pro Windows a Linux v Microsoft Azure

* Poznámka SAP [2015553] uvádí požadavky pro nasazení softwaru podporovaných SAP SAP v Azure.
* Poznámka SAP [2178632] obsahuje podrobné informace o veškeré monitorování metriky pro SAP v Azure.
* Poznámka SAP [1409604] má požadovaná verze SAP hostitele agenta pro Windows v Azure.
* Poznámka SAP [2191498] má požadovaná verze SAP hostitele agenta pro Linux v Azure.
* Poznámka SAP [2243692] obsahuje informace o licencích SAP v systému Linux v Azure.
* Poznámka SAP [1984787] má obecné informace o SUSE Linux Enterprise Server 12.
* Poznámka SAP [2002167] má obecné informace o Red Hat Enterprise Linux 7.x.
* Poznámka SAP [2069760] má obecné informace o Oracle Linux 7.x.
* Poznámka SAP [1999351] Další informace o řešení problémů s Azure rozšířené monitorování rozšíření pro SAP.
* Poznámka SAP [1597355] má obecné informace o odkládacího prostoru pro Linux.
* [Na stránce oznámení změny stavu Azure SAP](https://wiki.scn.sap.com/wiki/x/Pia7Gg) má zprávy a kolekce užitečné zdroje.
* [SAP komunity WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP poznámky pro Linux.
* Rutiny prostředí PowerShell specifické pro SAP, které jsou součástí [prostředí Azure PowerShell][azure-ps].
* Příkazy rozhraní příkazového řádku Azure specifické pro SAP, které jsou součástí [rozhraní příkazového řádku Azure][azure-cli].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Materiály pro Windows
Tyto články Microsoft popisuje nasazení SAP v Azure:

* [Azure virtuálních počítačů, plánování a implementace pro SAP NetWeaver][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP NetWeaver (v tomto článku)][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP NetWeaver][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scénáře nasazení SAP softwaru na virtuálních počítačích Azure
Máte několik možností pro nasazení virtuálních počítačů a přidruženými disky v Azure. Je důležité pochopit rozdíly mezi možnostmi nasazení, protože může trvat různé kroky, které připravit virtuální počítače pro nasazení na základě typu nasazení, které zvolíte.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scénář 1: Nasazení virtuálního počítače z Azure Marketplace pro SAP
Image zadaná společnost Microsoft nebo třetích stran v Azure Marketplace můžete použít k nasazení virtuálního počítače. Na webu Marketplace nabízí některé standardní bitové kopie operačního systému Windows Server a různých distribucí Linux. Obrázek, který zahrnuje správu databáze také můžete nasadit systém SKU, například Microsoft SQL Server. Další informace o používání bitové kopie s SKU databázového systému najdete v tématu [databázového systému virtuální počítače Azure nasazení pro SAP NetWeaver][dbms-guide].

Následující diagram ukazuje SAP konkrétní pořadí kroků pro nasazení virtuálního počítače z Azure Marketplace:

![Vývojový diagram nasazení virtuálního počítače pro systémy SAP pomocí bitové kopie virtuálních počítačů z Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače pomocí portálu Azure
Nejjednodušší způsob, jak vytvořit nový virtuální počítač s bitovou kopii z Azure Marketplace je pomocí portálu Azure.

1.  Přejděte do části <https://portal.azure.com/#create/hub> (Soubor > Nový > Jiné).  Nebo v nabídce portálu Azure vyberte **+ nový**.
2.  Vyberte **výpočetní**a pak vyberte typ operačního systému, kterou chcete nasadit. Například Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) nebo Oracle Linux 7.2. Výchozí zobrazení seznamu nezobrazuje, že všechny podporované operační systémy. Vyberte **zobrazit všechny** úplný seznam. Další informace o podporovaných operačních systémech pro nasazení softwaru SAP, viz poznámka SAP [1928533].
3.  Na další stránce přečtěte si podmínky a ujednání.
4.  V **vybrat model nasazení** vyberte **Resource Manager**.
5.  Vyberte **Vytvořit**.

Průvodce vás provede nastavení požadované parametry, které chcete vytvořit virtuální počítač, kromě všechny požadované prostředky, jako je síťová rozhraní a účty úložiště. Některé z těchto parametrů jsou:

1. **Základy**:
 * **Název**: název prostředku (název virtuálního počítače).
 * **Typ disku virtuálního počítače**: Vyberte typ disku disk operačního systému. Pokud chcete používat úložiště úrovně Premium pro datové disky, doporučujeme používat úložiště úrovně Premium pro také disk operačního systému.
 * **Uživatelské jméno a heslo** nebo **veřejný klíč SSH**: Zadejte uživatelské jméno a heslo uživatele, který se vytvoří během zřizování. Pro virtuální počítač s Linuxem můžete zadat veřejný klíč Secure Shell (SSH), který používáte k přihlášení k počítači.
 * **Předplatné**: Vyberte odběr, který chcete použít ke zřízení nového virtuálního počítače.
 * **Skupina prostředků**: název skupiny prostředků pro virtuální počítač. Můžete zadat název nové skupiny prostředků nebo název skupiny prostředků, která již existuje.
 * **Umístění**: kde nasadit nový virtuální počítač. Pokud se chcete připojit virtuální počítač k síti na pracovišti, nezapomeňte že vybrat umístění virtuální sítě, která se připojuje k vaší místní síti Azure. Další informace najdete v tématu [sítě Microsoft Azure] [ planning-guide-microsoft-azure-networking] v [virtuální počítače Azure plánování a implementace pro SAP NetWeaver] [ planning-guide].
2. **Velikost**:

     Seznam podporovaných typů virtuálních počítačů, viz poznámka SAP [1928533]. Ujistěte se, zda že jste vybrali správný typ virtuálního počítače, pokud chcete používat Azure Premium Storage. Ne všechny typy virtuálních počítačů podporují službu Premium Storage. Další informace najdete v tématu [úložiště: Microsoft Azure Storage a datové disky] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] a [Azure Premium Storage] [ planning-guide-azure-premium-storage] v [ Azure virtuálních počítačů, plánování a implementace pro SAP NetWeaver][planning-guide].

3. **Nastavení**:
  * **Úložiště**
    * **Typ disku**: Vyberte typ disku disk operačního systému. Pokud chcete používat úložiště úrovně Premium pro datové disky, doporučujeme používat úložiště úrovně Premium pro také disk operačního systému.
    * **Používat spravované disky**: Pokud chcete používat spravované disky, vyberte možnost Ano. Další informace o spravovaných disků, naleznete v kapitole [spravované disky] [ planning-guide-managed-disks] v příručce plánování.
    * **Účet úložiště**: Vyberte existující účet úložiště nebo vytvořte novou. Ne všechny typy úložiště fungovat pro spouštění aplikací SAP. Další informace o typech úložiště najdete v tématu [Microsoft Azure Storage] [ dbms-guide-2.3] v [databázového systému virtuální počítače Azure nasazení pro SAP NetWeaver] [ dbms-guide].
  * **Síť**
    * **Virtuální síť** a **podsíť**: integrovat virtuální počítač s intranetu, vyberte virtuální síť, který je připojen k síti na pracovišti.
    * **Veřejná IP adresa**: Vyberte veřejnou IP adresu, kterou chcete použít, nebo zadejte parametry pro vytvoření nové veřejné IP adresy. Veřejnou IP adresu můžete použít pro přístup k virtuálnímu počítači přes Internet. Ujistěte se, že vytvoříte skupinu zabezpečení sítě můžete líp zabezpečit přístup k virtuálnímu počítači.
    * **Skupina zabezpečení sítě**: Další informace najdete v tématu [řízení toku provozu sítě s skupin zabezpečení sítě][virtual-networks-nsg].
  * **Rozšíření**: můžete nainstalovat rozšíření virtuálního počítače je přidáte do nasazení. Není nutné přidat rozšíření v tomto kroku. Podpora SAP vyžaduje rozšíření jsou nainstalovány později. Naleznete v kapitole [konfiguraci Azure rozšířené monitorování rozšíření přístup k SAP] [ deployment-guide-4.5] v této příručce.
  * **Vysoká dostupnost**: Vyberte skupinu dostupnosti, nebo zadejte parametry, které chcete vytvořit novou skupinu dostupnosti. Další informace najdete v tématu [skupiny dostupnosti Azure][planning-guide-3.2.3].
  * **Monitorování**
    * **Spouštění diagnostiky**: můžete vybrat **zakázat** pro Diagnostika spouštění.
    * **Diagnostika hostovaného operačního systému**: můžete vybrat **zakázat** pro monitorování diagnostiky.

4. **Souhrn**:

  Zkontrolujte váš výběr a potom vyberte **OK**.

Virtuální počítač nasazen ve skupině prostředků, které jste vybrali.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Vytvoření virtuálního počítače pomocí šablony
Virtuální počítač můžete vytvořit pomocí jedné z šablon SAP publikované v [úložiště GitHub šablon azure rychlý Start][azure-quickstart-templates-github]. Můžete také můžete ručně vytvořit virtuální počítač pomocí [portál Azure][virtual-machines-windows-tutorial], [prostředí PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms], nebo [rozhraní příkazového řádku Azure][virtual-machines-linux-tutorial].

* [**Šablona dvouvrstvá konfigurace (jenom jeden virtuální počítač)** (sap-2vrstvy marketplace-image)][sap-templates-2-tier-marketplace-image]

  K vytvoření dvouvrstvé systému pomocí jenom jeden virtuální počítač, použijte tuto šablonu.
* [**Šablona dvouvrstvá konfigurace (jenom jeden virtuální počítač) - spravované disky** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  K vytvoření dvouvrstvé systému pomocí jenom jeden virtuální počítač a spravovat disky, tuto šablonu použijte.
* [**Šablona třívrstvá konfigurace (více virtuálních počítačů)** (sap-3vrstvy marketplace-image)][sap-templates-3-tier-marketplace-image]

  K vytvoření třívrstvé systému pomocí více virtuálních počítačů, použijte tuto šablonu.
* [**Šablona třívrstvá konfigurace (více virtuálních počítačů) - spravované disky** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  K vytvoření třívrstvé systému pomocí více virtuálních počítačů a spravovat disky, tuto šablonu použijte.

Na portálu Azure zadejte následující parametry šablony:

1. **Základy**:
  * **Předplatné**: předplatné pro použití k nasazení šablony.
  * **Skupina prostředků**: skupiny prostředků můžete použít k nasazení šablony. Můžete vytvořit novou skupinu prostředků, nebo můžete vybrat existující skupinu prostředků v předplatném.
  * **Umístění**: kde chcete nasadit šablonu. Pokud jste vybrali existující skupinu prostředků, použije se umístění této skupiny prostředků.

2. **Nastavení**:
  * **ID systému SAP**: ID systému SAP (SID).
  * **Typ operačního systému**: operačního systému, kterou chcete nasadit, například, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) nebo Oracle Linux 7.2.

    Zobrazení seznamu nezobrazuje, že všechny podporované operační systémy. Další informace o podporovaných operačních systémech pro nasazení softwaru SAP, viz poznámka SAP [1928533].
  * **Velikost systému SAP**: velikost systému SAP.

    Počet protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP vyžaduje systém, obraťte se na partnera technologie SAP nebo systémový integrátor.
  * **Dostupnost systému** (pouze šablony třívrstvá): dostupnost systému.

    Vyberte **HA** konfigurace, který je vhodný pro instalaci s vysokou dostupností. Vytvoří se dvou databázové servery a dva servery pro ABAP SAP centrální služby (ASC).
  * **Typ úložiště** (pouze šablony dvouvrstvá): typ úložiště používat.

    U větších systémů důrazně doporučujeme pomocí Azure Premium Storage. Další informace o typech úložiště najdete v těchto zdrojích:
      * [Používání úložiště Azure Premium SSD pro instanci databázového systému SAP][2367194]
      * [Microsoft Azure Storage] [ dbms-guide-2.3] v [databázového systému virtuální počítače Azure nasazení pro SAP NetWeaver][dbms-guide]
      * [Úložiště Premium: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
  * **Uživatelské jméno správce** a **heslo správce**: uživatelské jméno a heslo.
    Po vytvoření nového uživatele pro přihlášení k virtuálnímu počítači.
  * **Nový nebo existující podsíť**: Určuje, zda se vytvoří nová virtuální síť a podsíť, nebo se používá existující podsítí. Pokud již máte virtuální síť, která je připojen k síti na pracovišti, vyberte **existující**.
  * **ID podsítě**: ID podsítě virtuálních počítačů se připojí k. Vyberte podsíť virtuální sítě Azure ExpressRoute pro použití pro připojení k síti na pracovišti virtuální počítač nebo virtuální privátní sítě (VPN). ID obvykle vypadá takto: /subscriptions/&lt;id předplatného > /resourceGroups/&lt;název skupiny prostředků > /providers/Microsoft.Network/virtualNetworks/&lt;název virtuální sítě > /subnets/&lt;název podsítě >

3. **Podmínky a ujednání**:  
    Přečtěte si a přijměte právní podmínky.

4.  Vyberte **nákupu**.

Ve výchozím nastavení je nasadit agenta virtuálního počítače Azure, při použití bitovou kopii z Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru
V závislosti na konfiguraci místní sítě může být potřeba nastavení serveru proxy na vašem virtuálním počítači. Pokud virtuální počítač je připojený k vaší místní sítě prostřednictvím sítě VPN nebo ExpressRoute, virtuální počítač nemusí být možné získat přístup k Internetu a nebude ji již možné stáhnout požadované rozšíření nebo shromažďovat data monitorování. Další informace najdete v tématu [proxy server nakonfigurovat][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Připojení k doméně (jenom Windows)
Pokud vaše nasazení Azure je připojen k místní instance služby Active Directory nebo DNS prostřednictvím Azure připojení site-to-site VPN nebo ExpressRoute (to se označuje jako *mezi různými místy* v [plánování virtuálních počítačů Azure a implementaci pro SAP NetWeaver][planning-guide]), očekává se, že virtuální počítač je připojení k místní doméně. Další informace o požadavcích pro tuto úlohu najdete v tématu [připojit virtuální počítač k doméně místní (jenom Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurace monitorování
Aby byly SAP podporuje prostředí nastavit rozšíření monitorování Azure pro SAP, jak je popsáno v [konfiguraci Azure rozšířené monitorování rozšíření přístup k SAP][deployment-guide-4.5]. Kontrola předpokladů pro SAP monitorování a požadovaná minimální verze SAP jádra a Agent hostitele SAP, v prostředky uvedené v [SAP prostředky][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Monitorování kontroly
Zkontrolujte, zda monitorování funguje, jak je popsáno v [kontroly a řešení potíží pro nastavení sledování začátku do konce][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Kroky po nasazení
Po můžete vytvořit virtuální počítač a nasazení virtuálního počítače, musíte nainstalovat požadované softwarové komponenty ve virtuálním počítači. Z důvodu pořadí nasazení a software instalace v tomto typu nasazení virtuálního počítače nainstalovat software již musí být k dispozici, buď v Azure, na jiný počítač nebo jako disk, který lze připojit. Nebo, zvažte použití mezi různými místy scénář, ve které připojení k místní je uvedeno prostředky (Instalace sdílených složek).

Po nasazení virtuálního počítače v Azure, postupujte podle stejné pokyny a nástroje pro instalaci softwaru SAP na vašem virtuálním počítači, jako byste v místním prostředí. K instalaci softwaru SAP do virtuálního počítače Azure, doporučujeme SAP a Microsoft nahrát a uložit na instalačním médiu SAP na Azure virtuální pevné disky nebo disky spravované, nebo vytvořit virtuální počítač Azure, funguje jako souborový server, který obsahuje všechny požadované SAP instalačního média.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scénář 2: Nasazení virtuálního počítače s vlastní image pro SAP
Vzhledem k tomu, že různé verze operačního systému nebo databázového systému požadavky na jiné opravy, bitových kopií, které můžete najít v Azure Marketplace nemusí podle svých potřeb. Místo toho můžete chtít vytvořit virtuální počítač pomocí vlastní bitovou kopii operačního systému nebo databázového systému virtuálního počítače, kterou můžete nasadit znovu později.
Použijete různé kroky k vytvoření bitové kopie privátní pro Linux než chcete vytvořit jednu pro Windows.

- - -
> ![Windows][Logo_Windows] Windows
>
> Příprava bitové kopie systému Windows, který můžete použít k nasazení více virtuálních počítačů, musí být abstrahované nebo zobecněn ve virtuálním počítači místní nastavení systému Windows (např. Windows SID a název hostitele). Můžete použít [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) k tomu.
>
> ![Linux][Logo_Linux] Linux
>
> Chcete-li připravit bitovou kopii systému Linux, který můžete použít k nasazení více virtuálních počítačů, musí být některá nastavení Linux abstrahované nebo zobecněn na místní počítač. Můžete použít `waagent -deprovision` k tomu. Další informace najdete v tématu [zachytit virtuální počítač Linux spuštěné v Azure] [ virtual-machines-linux-capture-image] a [Azure Linux agent uživatelská příručka][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Příprava a vytvořit vlastní image a pak ho používali k vytváření více nové virtuální počítače. To je popsáno v [virtuální počítače Azure plánování a implementace pro SAP NetWeaver][planning-guide]. Nastavení databáze obsahu pomocí Správce zřizování SAP softwaru k instalaci nového systému SAP (obnoví ze zálohy z disku, který je připojen k virtuálnímu počítači) nebo přímo obnovením zálohy databáze ze služby Azure storage, pokud vaše databázového systému podporuje. Další informace najdete v tématu [databázového systému virtuální počítače Azure nasazení pro SAP NetWeaver][dbms-guide]. Pokud jste již nainstalovali systému SAP na váš místní virtuální počítač (hlavně u dvouvrstvá systémy), můžete upravit nastavení systému SAP po nasazení virtuálního počítače Azure pomocí postupu přejmenovat systému nepodporuje správce zřizování softwaru SAP (Poznámka SAP [1619720]). Jinak můžete nainstalovat SAP software po nasazení virtuálního počítače Azure.

Následující diagram ukazuje SAP konkrétní pořadí kroků pro nasazení virtuálního počítače z vlastní image:

![Vývojový diagram nasazení virtuálního počítače pro systémy SAP pomocí bitové kopie virtuálních počítačů v privátní Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače pomocí portálu Azure
Nejjednodušší způsob, jak vytvořit nový virtuální počítač z bitové kopie disku spravovat je pomocí portálu Azure. Další informace o tom, jak vytvořit Image disku spravovat, najdete v tématu [zaznamenat bitovou kopii spravované zobecněný virtuálního počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Přejděte do části <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages> (Soubor > Nový > Jiné). Nebo v nabídce portálu Azure vyberte **bitové kopie**.
2.  Vyberte bitovou kopii disku spravované chcete nasadit a klikněte na **vytvoření virtuálního počítače**

Průvodce vás provede nastavení požadované parametry, které chcete vytvořit virtuální počítač, kromě všechny požadované prostředky, jako je síťová rozhraní a účty úložiště. Některé z těchto parametrů jsou:

1. **Základy**:
 * **Název**: název prostředku (název virtuálního počítače).
 * **Typ disku virtuálního počítače**: Vyberte typ disku disk operačního systému. Pokud chcete používat úložiště úrovně Premium pro datové disky, doporučujeme používat úložiště úrovně Premium pro také disk operačního systému.
 * **Uživatelské jméno a heslo** nebo **veřejný klíč SSH**: Zadejte uživatelské jméno a heslo uživatele, který se vytvoří během zřizování. Pro virtuální počítač s Linuxem můžete zadat veřejný klíč Secure Shell (SSH), který používáte k přihlášení k počítači.
 * **Předplatné**: Vyberte odběr, který chcete použít ke zřízení nového virtuálního počítače.
 * **Skupina prostředků**: název skupiny prostředků pro virtuální počítač. Můžete zadat název nové skupiny prostředků nebo název skupiny prostředků, která již existuje.
 * **Umístění**: kde nasadit nový virtuální počítač. Pokud se chcete připojit virtuální počítač k síti na pracovišti, nezapomeňte že vybrat umístění virtuální sítě, která se připojuje k vaší místní síti Azure. Další informace najdete v tématu [sítě Microsoft Azure] [ planning-guide-microsoft-azure-networking] v [virtuální počítače Azure plánování a implementace pro SAP NetWeaver] [ planning-guide].
2. **Velikost**:

     Seznam podporovaných typů virtuálních počítačů, viz poznámka SAP [1928533]. Ujistěte se, zda že jste vybrali správný typ virtuálního počítače, pokud chcete používat Azure Premium Storage. Ne všechny typy virtuálních počítačů podporují službu Premium Storage. Další informace najdete v tématu [úložiště: Microsoft Azure Storage a datové disky] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] a [Azure Premium Storage] [ planning-guide-azure-premium-storage] v [ Azure virtuálních počítačů, plánování a implementace pro SAP NetWeaver][planning-guide].

3. **Nastavení**:
  * **Úložiště**
    * **Typ disku**: Vyberte typ disku disk operačního systému. Pokud chcete používat úložiště úrovně Premium pro datové disky, doporučujeme používat úložiště úrovně Premium pro také disk operačního systému.
    * **Používat spravované disky**: Pokud chcete používat spravované disky, vyberte možnost Ano. Další informace o spravovaných disků, naleznete v kapitole [spravované disky] [ planning-guide-managed-disks] v příručce plánování.
  * **Síť**
    * **Virtuální síť** a **podsíť**: integrovat virtuální počítač s intranetu, vyberte virtuální síť, který je připojen k síti na pracovišti.
    * **Veřejná IP adresa**: Vyberte veřejnou IP adresu, kterou chcete použít, nebo zadejte parametry pro vytvoření nové veřejné IP adresy. Veřejnou IP adresu můžete použít pro přístup k virtuálnímu počítači přes Internet. Ujistěte se, že vytvoříte skupinu zabezpečení sítě můžete líp zabezpečit přístup k virtuálnímu počítači.
    * **Skupina zabezpečení sítě**: Další informace najdete v tématu [řízení toku provozu sítě s skupin zabezpečení sítě][virtual-networks-nsg].
  * **Rozšíření**: můžete nainstalovat rozšíření virtuálního počítače je přidáte do nasazení. Není nutné přidat rozšíření v tomto kroku. Podpora SAP vyžaduje rozšíření jsou nainstalovány později. Naleznete v kapitole [konfiguraci Azure rozšířené monitorování rozšíření přístup k SAP] [ deployment-guide-4.5] v této příručce.
  * **Vysoká dostupnost**: Vyberte skupinu dostupnosti, nebo zadejte parametry, které chcete vytvořit novou skupinu dostupnosti. Další informace najdete v tématu [skupiny dostupnosti Azure][planning-guide-3.2.3].
  * **Monitorování**
    * **Spouštění diagnostiky**: můžete vybrat **zakázat** pro Diagnostika spouštění.
    * **Diagnostika hostovaného operačního systému**: můžete vybrat **zakázat** pro monitorování diagnostiky.

4. **Souhrn**:

  Zkontrolujte váš výběr a potom vyberte **OK**.

Virtuální počítač nasazen ve skupině prostředků, které jste vybrali.
#### <a name="create-a-virtual-machine-by-using-a-template"></a>Vytvoření virtuálního počítače pomocí šablony
K vytvoření nasazení s použitím privátní bitové kopie operačního systému z portálu Azure, použijte jednu z následujících šablon SAP. Tyto šablony jsou publikovány v [úložiště GitHub šablon azure rychlý Start][azure-quickstart-templates-github]. Můžete také můžete ručně vytvořit virtuální počítač pomocí [prostředí PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Šablona dvouvrstvá konfigurace (jenom jeden virtuální počítač)** (sap-2vrstvy uživatel image)][sap-templates-2-tier-user-image]

  K vytvoření dvouvrstvé systému pomocí jenom jeden virtuální počítač, použijte tuto šablonu.
* [**Šablona dvouvrstvá konfigurace (jenom jeden virtuální počítač) - spravované bitové kopie disku** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  K vytvoření dvouvrstvé systému pomocí jenom jeden virtuální počítač a bitové kopie disku spravované, pomocí této šablony.
* [**Šablona třívrstvá konfigurace (více virtuálních počítačů)** (sap-3vrstvy uživatel image)][sap-templates-3-tier-user-image]

  K vytvoření třívrstvé systému pomocí více virtuálních počítačů nebo vlastní image operačního systému, použijte tuto šablonu.
* [**Šablona třívrstvá konfigurace (více virtuálních počítačů) - spravované bitové kopie disku** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  K vytvoření třívrstvé systému pomocí více virtuálních počítačů nebo vlastní image operačního systému a bitové kopie disku spravované, pomocí této šablony.

Na portálu Azure zadejte následující parametry šablony:

1. **Základy**:
  * **Předplatné**: předplatné pro použití k nasazení šablony.
  * **Skupina prostředků**: skupiny prostředků můžete použít k nasazení šablony. Můžete vytvořit novou skupinu prostředků nebo vyberte existující skupinu prostředků v předplatném.
  * **Umístění**: kde chcete nasadit šablonu. Pokud jste vybrali existující skupinu prostředků, použije se umístění této skupiny prostředků.
2. **Nastavení**:
  * **ID systému SAP**: ID SAP systému.
  * **Typ operačního systému**: typ operačního systému, kterou chcete nasadit (Windows nebo Linux).
  * **Velikost systému SAP**: velikost systému SAP.

    Počet protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP vyžaduje systém, obraťte se na partnera technologie SAP nebo systémový integrátor.
  * **Dostupnost systému** (pouze šablony třívrstvá): dostupnost systému.

    Vyberte **HA** konfigurace, který je vhodný pro instalaci s vysokou dostupností. Vytvoří se dvou databázové servery a dva servery pro ASC.
  * **Typ úložiště** (pouze šablony dvouvrstvá): typ úložiště používat.

    U větších systémů důrazně doporučujeme pomocí Azure Premium Storage. Další informace o typech úložiště najdete v následujících zdrojích informací:
      * [Používání úložiště Azure Premium SSD pro instanci databázového systému SAP][2367194]
      * [Microsoft Azure Storage] [ dbms-guide-2.3] v [databázového systému virtuální počítače Azure nasazení pro SAP NetWeaver][dbms-guide]
      * [Úložiště Premium: Vysoce výkonné úložiště pro úlohy virtuálního počítače Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
  * **Uživatelská image URI virtuálního pevného disku** (pouze nespravovaná disku image šablony): identifikátoru URI privátní operačního systému bitové kopie disku VHD, například https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **Účet úložiště bitových kopií uživatele** (pouze nespravovaná disku image šablony): název účtu úložiště privátní bitové kopie operačního systému se uloží, například &lt;accountname > v https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **userImageId** (pouze spravovaných disků na image šablony): Id spravované disku Image, kterou chcete použít
  * **Uživatelské jméno správce** a **heslo správce**: uživatelské jméno a heslo.

    Po vytvoření nového uživatele pro přihlášení k virtuálnímu počítači.
  * **Nový nebo existující podsíť**: Určuje, zda se vytvoří nová virtuální síť a podsíť, nebo se používá existující podsítí. Pokud již máte virtuální síť, která je připojen k síti na pracovišti, vyberte **existující**.
  * **ID podsítě**: ID podsítě, do které budou virtuální počítače připojit k. Vyberte podsíť virtuální sítě VPN nebo ExpressRoute sloužící k připojení virtuálního počítače k síti na pracovišti. ID obvykle vypadá takto:

    /subscriptions/&lt;id předplatného > /resourceGroups/&lt;název skupiny prostředků > /providers/Microsoft.Network/virtualNetworks/&lt;název virtuální sítě > /subnets/&lt;název podsítě >

3. **Podmínky a ujednání**:  
    Přečtěte si a přijměte právní podmínky.

4.  Vyberte **nákupu**.

#### <a name="install-the-vm-agent-linux-only"></a>Nainstalujte agenta virtuálního počítače (pouze Linux)
Použití šablon popsané v předchozí části, musíte Linux Agent již nainstalován v bitové kopii uživatele nebo nasazení se nezdaří. Stáhněte a nainstalujte agenta virtuálního počítače v bitové kopii uživatele, jak je popsáno v [stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure][deployment-guide-4.4]. Pokud nepoužíváte šablony, můžete také nainstalovat agenta virtuálního počítače později.

#### <a name="join-a-domain-windows-only"></a>Připojení k doméně (jenom Windows)
Pokud vaše nasazení Azure je připojen k místní instance služby Active Directory nebo DNS prostřednictvím Azure připojení site-to-site VPN nebo Azure ExpressRoute (to se označuje jako *mezi různými místy* v [virtuální počítače Azure plánování a implementace pro SAP NetWeaver][planning-guide]), očekává se, že virtuální počítač je připojení k místní doméně. Další informace o aspektech týkajících se pro tento krok najdete v tématu [připojit virtuální počítač k doméně místní (jenom Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru
V závislosti na konfiguraci místní sítě může být potřeba nastavení serveru proxy na vašem virtuálním počítači. Pokud virtuální počítač je připojený k vaší místní sítě prostřednictvím sítě VPN nebo ExpressRoute, virtuální počítač nemusí být možné získat přístup k Internetu a nebude ji již možné stáhnout požadované rozšíření nebo shromažďovat data monitorování. Další informace najdete v tématu [proxy server nakonfigurovat][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Konfigurace monitorování
Aby byly SAP podporuje prostředí nastavit rozšíření monitorování Azure pro SAP, jak je popsáno v [konfiguraci Azure rozšířené monitorování rozšíření přístup k SAP][deployment-guide-4.5]. Kontrola předpokladů pro SAP monitorování a požadovaná minimální verze SAP jádra a Agent hostitele SAP, v prostředky uvedené v [SAP prostředky][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Monitorování kontroly
Zkontrolujte, zda monitorování funguje, jak je popsáno v [kontroly a řešení potíží pro nastavení sledování začátku do konce][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scénář 3: Přesun místní počítač pomocí virtuálního pevného disku není zobecněný Azure SAP
V tomto scénáři chcete přesunout konkrétního systému SAP do Azure z místního prostředí. To provedete tím, že nahrajete virtuální pevný disk, který má operačního systému, binární soubory SAP a nakonec databázového systému binárních souborů a virtuálních pevných disků se soubory protokolu a data z databázového systému, do Azure. Na rozdíl od podle scénáře popsaného v [scénář 2: nasazení virtuálního počítače s vlastní image pro SAP][deployment-guide-3.3], v takovém případě byste mít název hostitele, identifikátor SID SAP, a SAP uživatelských účtů ve virtuálním počítači Azure, protože byly nakonfigurovány v místním prostředí. Není nutné ke generalizaci operačního systému. Tento scénář platí nejčastěji používá pro scénáře mezi různými místy, kde součástí povahu SAP běží místně a jeho součástí běží na Azure.

V tomto scénáři je Agent virtuálního počítače **není** automaticky nainstalovaná při nasazení. Vzhledem k tomu, že Agent virtuálního počítače a Azure rozšířené monitorování rozšíření pro SAP jsou nutné ke spuštění SAP NetWeaver v Azure, musíte stáhnout, nainstalovat a povolit i komponent ručně po vytvoření virtuálního počítače.

Další informace o agenta virtuálního počítače Azure najdete v následujících materiálech.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Přehled služby Azure agenta virtuálního počítače][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Uživatelská příručka k Azure Linux Agent][virtual-machines-linux-agent-user-guide]
>
>

- - -

Následující vývojový diagram znázorňuje pořadí kroků pro přesun místní počítač pomocí virtuálního pevného disku není zobecněný Azure:

![Vývojový diagram nasazení virtuálního počítače pro SAP systémy pomocí disku virtuálního počítače][deployment-guide-figure-400]

Pokud disk již odeslán a definované v Azure (viz [virtuální počítače Azure plánování a implementace pro SAP NetWeaver][planning-guide]), provést úkoly popsané v další části několik.

#### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
K vytvoření nasazení pomocí privátní disk operačního systému, prostřednictvím portálu Azure, použijte šablonu SAP publikované v [úložiště GitHub šablon azure rychlý Start][azure-quickstart-templates-github]. Také můžete ručně vytvoříte virtuální počítač pomocí prostředí PowerShell.

* [**Šablona dvouvrstvá konfigurace (jenom jeden virtuální počítač)** (sap-2vrstvy uživatel disk)][sap-templates-2-tier-os-disk]

  K vytvoření dvouvrstvé systému pomocí jenom jeden virtuální počítač, použijte tuto šablonu.
* [**Šablona dvouvrstvá konfigurace (jenom jeden virtuální počítač) - spravované disku** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  K vytvoření dvouvrstvé systému pomocí jenom jeden virtuální počítač a Disk spravované, pomocí této šablony.

Na portálu Azure zadejte následující parametry šablony:

1. **Základy**:
  * **Předplatné**: předplatné pro použití k nasazení šablony.
  * **Skupina prostředků**: skupiny prostředků můžete použít k nasazení šablony. Můžete vytvořit novou skupinu prostředků nebo vyberte existující skupinu prostředků v předplatném.
  * **Umístění**: kde chcete nasadit šablonu. Pokud jste vybrali existující skupinu prostředků, použije se umístění této skupiny prostředků.
2. **Nastavení**:
  * **ID systému SAP**: ID SAP systému.
  * **Typ operačního systému**: typ operačního systému, kterou chcete nasadit (Windows nebo Linux).
  * **Velikost systému SAP**: velikost systému SAP.

    Počet protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP vyžaduje systém, obraťte se na partnera technologie SAP nebo systémový integrátor.
  * **Typ úložiště** (pouze šablony dvouvrstvá): typ úložiště používat.

    U větších systémů důrazně doporučujeme pomocí Azure Premium Storage. Další informace o typech úložiště najdete v následujících zdrojích informací:
      * [Používání úložiště Azure Premium SSD pro instanci databázového systému SAP][2367194]
      * [Microsoft Azure Storage] [ dbms-guide-2.3] v [nasazení virtuálního počítače Azure databázového systému pro SAP NetWeaver][dbms-guide]
      * [Úložiště Premium: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
  * **Identifikátor URI virtuálního pevného disku na disku operačního systému** (pouze nespravovaná disku šablony): URI privátní disk operačního systému, například https://&lt;accountname >.blob.core.windows.net/vhds/osdisk.vhd.
  * **Id disku spravované disku operačního systému** (pouze spravované disku šablony): Id disku spravované disku operačního systému, /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
  * **Nový nebo existující podsíť**: Určuje, zda se vytvoří nová virtuální síť a podsíť, nebo se používá existující podsítí. Pokud již máte virtuální síť, která je připojen k síti na pracovišti, vyberte **existující**.
  * **ID podsítě**: ID podsítě, do které budou virtuální počítače připojit k. Vyberte podsíť virtuální sítě VPN nebo Azure ExpressRoute sloužící k připojení virtuálního počítače k síti na pracovišti. ID obvykle vypadá takto:

    /subscriptions/&lt;id předplatného > /resourceGroups/&lt;název skupiny prostředků > /providers/Microsoft.Network/virtualNetworks/&lt;název virtuální sítě > /subnets/&lt;název podsítě >

3. **Podmínky a ujednání**:  
    Přečtěte si a přijměte právní podmínky.

4.  Vyberte **nákupu**.

#### <a name="install-the-vm-agent"></a>Nainstalujte agenta virtuálního počítače
Použití šablon popsané v předchozí části, musí být Agent virtuálního počítače nainstalovaný na disk operačního systému nebo nasazení se nezdaří. Stáhněte a nainstalujte agenta virtuálního počítače ve virtuálním počítači, jak je popsáno v [stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure][deployment-guide-4.4].

Pokud nepoužíváte šablony popsané v předchozí části, můžete také nainstalovat agenta virtuálního počítače později.

#### <a name="join-a-domain-windows-only"></a>Připojení k doméně (jenom Windows)
Pokud vaše nasazení Azure je připojen k místní instance služby Active Directory nebo DNS prostřednictvím Azure připojení site-to-site VPN nebo ExpressRoute (to se označuje jako *mezi různými místy* v [plánování virtuálních počítačů Azure a implementaci pro SAP NetWeaver][planning-guide]), očekává se, že virtuální počítač je připojení k místní doméně. Další informace o požadavcích pro tuto úlohu najdete v tématu [připojit virtuální počítač k doméně místní (jenom Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru
V závislosti na konfiguraci místní sítě může být potřeba nastavení serveru proxy na vašem virtuálním počítači. Pokud virtuální počítač je připojený k vaší místní sítě prostřednictvím sítě VPN nebo ExpressRoute, virtuální počítač nemusí být možné získat přístup k Internetu a nebude ji již možné stáhnout požadované rozšíření nebo shromažďovat data monitorování. Další informace najdete v tématu [proxy server nakonfigurovat][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Konfigurace monitorování
Aby byly SAP podporuje prostředí nastavit rozšíření monitorování Azure pro SAP, jak je popsáno v [konfiguraci Azure rozšířené monitorování rozšíření přístup k SAP][deployment-guide-4.5]. Kontrola předpokladů pro SAP monitorování a požadovaná minimální verze SAP jádra a Agent hostitele SAP, v prostředky uvedené v [SAP prostředky][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Monitorování kontroly
Zkontrolujte, zda monitorování funguje, jak je popsáno v [kontroly a řešení potíží pro nastavení sledování začátku do konce][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Aktualizovat konfiguraci monitorování pro SAP
Aktualizujte konfiguraci monitorování SAP v některém z následujících scénářů:
* Společné týmu Microsoft/SAP rozšiřuje možnosti monitorování a požadavky čítače více nebo méně.
* Microsoft zavádí novou verzi základní infrastrukturu Azure, který poskytuje data monitorování a Azure rozšířené monitorování rozšíření pro SAP musí být přizpůsobena tyto změny.
* Připojení dalších datových disků k virtuálnímu počítači Azure nebo odebrat datový disk. V tomto scénáři aktualizujte shromažďování dat souvisejících s úložištěm. Změna konfigurací přidáním nebo odstraněním koncových bodů nebo přiřazení IP adres k virtuálnímu počítači nemá vliv na konfiguraci monitorování.
* Změníte velikost virtuálního počítače Azure, například velikost A5 tak, aby ostatní velikost virtuálního počítače.
* K virtuálnímu počítači Azure přidáte nové síťových rozhraní.

Aktualizovat nastavení monitorování, aktualizace infrastruktury pro monitorování pomocí následujících kroků v [konfiguraci Azure rozšířené monitorování rozšíření přístup k SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Podrobný popis úkolů pro nasazení softwaru SAP
Tato část popsala postup při provádění konkrétní úlohy v procesu konfigurace a nasazení.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Nasazení rutin prostředí Azure PowerShell
1.  Přejděte na [Microsoft Azure stáhne](https://azure.microsoft.com/downloads/).
2.  V části **nástroje příkazového řádku**v části **prostředí PowerShell**, vyberte **nainstalovat Windows**.
3.  V dialogovém okně Microsoft Download Manager pro stažený soubor (například WindowsAzurePowershellGet.3f.3f.3fnew.exe), vyberte **spustit**.
4.  Ke spuštění instalačního programu webové platformy (Microsoft webové platformy), vyberte **Ano**.
5.  Stránka, která vypadá to, se zobrazuje:

  ![Stránka Instalace rutin prostředí Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Vyberte **nainstalovat**a pak přijměte licenční podmínky pro Software společnosti Microsoft.
7.  Prostředí PowerShell je nainstalovaný. Vyberte **Dokončit** zavřete průvodce instalací.

Často vyhledat aktualizace do rutin prostředí PowerShell, které obvykle jsou aktualizovány jednou měsíčně. Nejjednodušší způsob, jak zkontrolovat aktualizace je postup předchozí instalace, až na stránce instalace zobrazí v kroku 5. Číslo verze datum a verze rutin jsou zahrnuty v stránka zobrazená v kroku 5. Pokud není uvedeno jinak v Poznámka SAP [1928533] nebo Poznámka SAP [2015553], doporučujeme vám, že pracujete s nejnovější verzi rutin prostředí Azure PowerShell.

Pokud chcete zkontrolovat verzi rutin Azure Powershellu, které jsou nainstalovány v počítači, spusťte tento příkaz prostředí PowerShell:
```powershell
(Get-Module AzureRm.Compute).Version
```
Výsledek vypadá takto:

![Výsledek kontroly verze rutiny prostředí Azure PowerShell][deployment-guide-figure-600]
<a name="figure-6"></a>

Pokud je verze Azure rutiny v počítači nainstalována aktuální verze, první stránce Průvodce instalací znamenají, že přidáním **(nainstalována)** pro název produktu (viz následující snímek obrazovky). Vaše rutiny prostředí PowerShell Azure jsou aktuální. Zavřete průvodce instalací, vyberte **ukončení**.

![Instalační stránku rutin prostředí Azure PowerShell, která udává, zda jsou nainstalovány nejnovější verzi rutin prostředí Azure PowerShell][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Nasazení rozhraní příkazového řádku Azure
1.  Přejděte na [Microsoft Azure stáhne](https://azure.microsoft.com/downloads/).
2.  V části **nástroje příkazového řádku**v části **rozhraní příkazového řádku Azure**, vyberte **nainstalovat** odkaz pro váš operační systém.
3.  V dialogovém okně Microsoft Download Manager pro stažený soubor (například WindowsAzureXPlatCLI.3f.3f.3fnew.exe), vyberte **spustit**.
4.  Ke spuštění instalačního programu webové platformy (Microsoft webové platformy), vyberte **Ano**.
5.  Stránka, která vypadá to, se zobrazuje:

  ![Stránka Instalace rutin prostředí Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Vyberte **nainstalovat**a pak přijměte licenční podmínky pro Software společnosti Microsoft.
7.  Rozhraní příkazového řádku Azure je nainstalovaný. Vyberte **Dokončit** zavřete průvodce instalací.

Často vyhledat aktualizace pro rozhraní příkazového řádku Azure, která obvykle se aktualizuje jednou měsíčně. Nejjednodušší způsob, jak zkontrolovat aktualizace je postup předchozí instalace, až na stránce instalace zobrazí v kroku 5.

Pokud chcete zkontrolovat verzi rozhraní příkazového řádku Azure, který je nainstalován v počítači, spusťte tento příkaz:
```
azure --version
```

Výsledek vypadá takto:

![Výsledek kontroly verze rozhraní příkazového řádku Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Připojit virtuální počítač k doméně místní (jenom Windows)
Pokud nasadíte virtuální počítače SAP ve scénáři mezi různými místy, kde místní služby Active Directory a DNS jsou rozšířené v Azure, očekává se, že virtuální počítače jsou připojení k místní doméně. Podrobné kroky, které je třeba provést připojení k místní doméně a další software, musí být členem domény služby místní virtuální počítač se liší podle zákazníka. Obvykle Pokud chcete virtuální počítač připojit k místní doméně, musíte nainstalovat další software, jako je antimalwarový software a zálohování nebo monitorování softwaru.

V tomto scénáři musíte také ujistěte se, že pokud nastavení internetového proxy serveru jsou povinná, pokud virtuální počítač připojen do domény ve vašem prostředí, Windows místní systémový účet (S-1-5-18) ve virtuálním počítači hosta má stejné nastavení proxy serveru. Je nejjednodušší možnost vynutit proxy server pomocí zásad skupiny, které se vztahuje na systémy v doméně domény.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure
Pro virtuální počítače, které jsou nasazeny z image operačního systému, který není zobecněný (například obrázek, který není v nástroji pro přípravu systému Windows nebo nástroje sysprep, pocházejí) musíte ručně stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure.

Pokud nasadíte virtuální počítač z Azure Marketplace, tento krok není povinný. Bitové kopie z Azure Marketplace už mít agenta virtuálního počítače Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
1.  Stáhněte agenta virtuálního počítače Azure:
  1.  Stažení [instalační balíček agenta virtuálního počítače Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
  2.  Balíček MSI agenta virtuálního počítače ukládat místně na osobním počítači nebo serveru.
2.  Nainstalujte agenta virtuálního počítače Azure:
  1.  Připojení k nasazené virtuálního počítače Azure pomocí protokolu RDP (Remote Desktop).
  2.  Otevřete okno Průzkumníka Windows na virtuální počítač a vyberte cílový adresář pro soubor MSI agenta virtuálního počítače.
  3.  Přetáhněte soubor MSI Instalační program agenta virtuálního počítače Azure ze svého místního počítače nebo serveru na cílový adresář agenta virtuálního počítače na virtuálním počítači.
  4.  Poklikejte na soubor MSI ve virtuálním počítači.
3.  Pro virtuální počítače, které jsou připojeny k místní domény, ujistěte se, že případný nastavení proxy Internetu platí také pro účet místního systému Windows (S-1-5-18) ve virtuálním počítači, jak je popsáno v [proxy server nakonfigurovat][deployment-guide-configure-proxy]. Agent virtuálního počítače běží v tomto kontextu a musí být schopný se připojit k Azure.

Žádná interakce s uživatelem, je potřeba aktualizovat agenta virtuálního počítače Azure. Agent virtuálního počítače se automaticky aktualizuje a nevyžaduje restartování virtuálního počítače.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Pomocí následujících příkazů nainstalujte VM agenta pro Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) nebo Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Pokud agent je již nainstalován, aktualizace Azure Linux Agent, postupujte podle pokynů popsaných v [aktualizovat Azure Linux Agent na virtuálním počítači na nejnovější verzi z webu GitHub][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurace proxy serveru
Kroky, které je třeba provést konfiguraci proxy serveru v systému Windows se liší od způsob konfigurace proxy serveru v systému Linux.

#### <a name="windows"></a>Windows
Nastavení proxy serveru musí být nastavit správně pro místní systémový účet pro přístup k Internetu. Pokud vaše nastavení proxy serveru nejsou nastavené zásady skupiny, můžete nakonfigurovat nastavení pro účet místního systému.

1. Přejděte na **spustit**, zadejte **gpedit.msc**a potom vyberte **Enter**.
2. Vyberte **konfigurace počítače** > **šablony pro správu** > **součásti systému Windows** > **aplikace Internet Explorer**. Ujistěte se, že nastavení **zkontrolujte proxy nastavení podle počítače (nikoli na uživatele)** je zakázána nebo není nakonfigurována.
3. V **ovládací panely**, přejděte na **Centrum sítí a sdílení** > **Možnosti Internetu**.
4. Na **připojení** vyberte **nastavení místní sítě** tlačítko.
5. Vymazat **automaticky zjišťovat nastavení** zaškrtávací políčko.
6. Vyberte **použít proxy server pro síť LAN** zaškrtněte políčko a potom zadejte adresu proxy serveru a port.
7. Vyberte **Upřesnit** tlačítko.
8. V **výjimky** zadejte IP adresu **168.63.129.16**. Vyberte **OK**.

#### <a name="linux"></a>Linux
Správné proxy server nakonfigurovat v souboru konfigurace agenta hosta Microsoft Azure, která se nachází v \\atd\\waagent.conf.

Nastavte následující parametry:

1.  **HTTP proxy hostitele**. Například nastavte ji na **proxy.corp.local**.
  ```
  HttpProxy.Host=<proxy host>

  ```
2.  **Port proxy serveru HTTP**. Například nastavte ji na **80**.
  ```
  HttpProxy.Port=<port of the proxy host>

  ```
3.  Restartujte agenta.

  ```
  sudo service waagent restart
  ```

Nastavení proxy serveru v \\atd\\waagent.conf platí také pro požadované rozšíření virtuálního počítače. Pokud chcete používat úložiště Azure, ujistěte se, že přenosy na těchto úložiště není projít místního intranetu. Pokud jste vytvořili trasy definované uživatelem, chcete-li povolit vynucené tunelování, ujistěte se, že přidáte trasu který směruje provoz do úložiště přímo k Internetu a ne prostřednictvím připojení VPN typu site-to-site.

* **SLES**

  Musíte taky přidat trasy pro IP adresy uvedené v \\atd\\regionserverclnt.cfg. Následující obrázek znázorňuje příklad:

  ![Vynucené tunelování][deployment-guide-figure-50]


* **RHEL**

  Musíte taky přidat trasy pro IP adresy hostitelů uvedených v \\atd\\yum.repos.d\\rhui Vyrovnávání zatížení. Příklad podívejte se na předchozí obrázek.

* **Oracle Linux**

  Neexistují žádné úložiště pro Oracle Linux v Azure. Budete muset nakonfigurovat vlastní úložiště pro Oracle Linux nebo použít veřejné úložiště.

Další informace o trasy definované uživatelem, najdete v části [trasy definované uživatelem a předávání IP][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Konfigurace rozšíření Azure rozšířené monitorování pro SAP
Když jste virtuální počítač připravený, jak je popsáno v [scénáře nasazení virtuálních počítačů pro SAP v Azure][deployment-guide-3], Agent virtuálního počítače Azure je nainstalovaný na virtuálním počítači. Dalším krokem je pro nasazení Azure rozšířené monitorování rozšíření pro SAP, která je k dispozici v úložišti rozšíření Azure v globálních datových centrech Azure. Další informace najdete v tématu [virtuální počítače Azure plánování a implementace pro SAP NetWeaver][planning-guide-9.1].

Prostředí PowerShell nebo rozhraní příkazového řádku Azure můžete použít k instalaci a konfiguraci Azure rozšířené monitorování rozšíření pro SAP. Instalace rozšíření v systému Windows nebo virtuálního počítače s Linuxem pomocí počítače s Windows, najdete v části [prostředí Azure PowerShell][deployment-guide-4.5.1]. Pokud chcete nainstalovat rozšíření virtuálního počítače s Linuxem pomocí plochy Linux, najdete v části [rozhraní příkazového řádku Azure][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Prostředí Azure PowerShell pro systémy Linux a virtuálních počítačů Windows
Chcete-li nainstalovat Azure rozšířené monitorování rozšíření pro SAP pomocí prostředí PowerShell:

1. Ujistěte se, že instalaci nejnovější verze rutiny Azure Powershellu. Další informace najdete v tématu [rutin nasazení prostředí Azure PowerShell][deployment-guide-4.1].  
2. Spusťte následující rutinu Azure PowerShellu.
    Seznam dostupných prostředí, spusťte `commandlet Get-AzureRmEnvironment`. Pokud chcete použít globální Azure, je prostředí **AzureCloud**. Azure v Číně, vyberte **AzureChinaCloud**.

    ```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Connect-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Po zadání vaše data na účtu a identifikovat virtuální počítač Azure, skript nasadí požadované rozšíření a umožňuje požadované funkce. To může trvat několik minut.
Další informace o `Set-AzureRmVMAEMExtension`, najdete v části [Set-AzureRmVMAEMExtension][msdn-set-azurermvmaemextension].

![Úspěšné provedení specifické pro SAP Azure rutiny Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

`Set-AzureRmVMAEMExtension` Konfigurace nemá všechny kroky konfigurace hostitele monitorování pro SAP.

Výstup skriptu obsahuje následující informace:

* Potvrzení, že monitorování pro disk operačního systému a všechny další datové disky nakonfigurovaný.
* Další dvě zprávy potvrďte konfiguraci úložiště metriky pro účet konkrétní úložiště.
* Jeden řádek výstupu poskytuje stav skutečné aktualizace konfiguraci monitorování.
* Další řádek výstupu potvrdí, že byla konfigurace nasazení nebo aktualizovat.
* Poslední řádek výstupu je informační. Zobrazuje možnosti testování konfiguraci monitorování.
* Pokud chcete zkontrolovat úspěšně byly provedeny všechny kroky rozšířené monitorování Azure a že infrastruktury Azure poskytuje potřebná data, pokračujte kontrolu připravenosti Azure rozšířené monitorování rozšíření pro SAP, jak je popsáno v [kontrolu připravenosti pro Azure rozšířené monitorování pro SAP][deployment-guide-5.1].
* Počkejte, než 15 až 30 minut Azure Diagnostics shromažďovat relevantní data.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI pro virtuální počítače s Linuxem
Chcete-li nainstalovat Azure rozšířené monitorování rozšíření pro SAP pomocí rozhraní příkazového řádku Azure:

1. Nainstalovat Azure CLI 1.0, jak je popsáno v [nainstalovat Azure CLI 1.0][azure-cli].
2. Přihlaste se pomocí účtu Azure:

  ```
  azure login
  ```

3. Přepněte do režimu Azure Resource Manager:

  ```
  azure config mode arm
  ```

4. Povolte Azure rozšířené monitorování:

  ```
  azure vm enable-aem <resource-group-name> <vm-name>
  ```

5. Ověřte, že Azure rozšířené monitorování rozšíření na virtuální počítač s Linuxem Azure aktivní. Zkontrolujte, zda soubor \\var\\lib\\AzureEnhancedMonitor\\PerfCounters existuje. Pokud existuje, na příkazovém řádku, spusťte tento příkaz zobrazíte údaje shromážděné pomocí Azure rozšířené monitorování:
```
cat /var/lib/AzureEnhancedMonitor/PerfCounters
```

Výstup vypadá takto:
```
2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
???
???
```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Kontroly a řešení potíží pro sledování začátku do konce
Po nasazení virtuálního počítače Azure a nastavte příslušné monitorování infrastruktury Azure, zkontrolujte, zda jsou všechny součásti Azure rozšířené monitorování rozšíření funguje podle očekávání.

Spustit kontrolu připravenosti Azure rozšířené monitorování rozšíření pro SAP, jak je popsáno v [kontrolu připravenosti Azure rozšířené monitorování rozšíření pro SAP][deployment-guide-5.1]. Pokud jsou všechny výsledky kontroly připravenosti kladné a všechny relevantní čítače zobrazí OK, Azure monitorování byl úspěšně nastaven. Abyste mohli pokračovat instalace agenta SAP hostitele podle popisu v poznámkách k SAP v [SAP prostředky][deployment-guide-2.2]. Pokud kontrola připravenosti uvádí, že chybí čítače, spusťte kontrolu stavu pro monitorování infrastruktury Azure, jak je popsáno v [Kontrola stavu pro konfiguraci Azure monitorování infrastruktury][deployment-guide-5.2]. Další možnosti řešení potíží najdete v tématu [monitorování řešení potíží s Azure pro SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Kontrola připravenosti Azure rozšířené monitorování rozšíření pro SAP
Tato kontrola zajišťuje, že všechny metriky výkonu, které se zobrazují v aplikaci SAP jsou poskytovány základní monitorování infrastruktury Azure.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Spustit kontrolu připravenosti na virtuální počítač s Windows

1.  Přihlaste se k virtuální počítač Azure (pomocí účtu správce není potřeba).
2.  Otevřete okno příkazového řádku.
3.  Na příkazovém řádku změňte adresář na instalační složku Azure rozšířené monitorování rozšíření pro SAP: C:\\balíčky\\modulů plug-in\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;verze >\\vyřadit

  *Verze* v cestě k rozšíření monitorování mohou lišit. Pokud se zobrazí složek pro více verzí rozšíření monitorování ve složce instalace, zkontrolujte konfiguraci služby systému AzureEnhancedMonitoring Windows a pak přejděte do složky uvedené jako *cesta ke spustitelnému souboru*.

  ![Vlastnosti služby spuštěna Azure rozšířené monitorování rozšíření pro SAP][deployment-guide-figure-1000]

4.  Na příkazovém řádku spusťte **azperflib.exe** bez parametrů.

  > [!NOTE]
  > Azperflib.exe běží ve smyčce a aktualizuje počitadla shromažďovaných každých 60 sekund. Pro ukončení smyčky, zavřete okno příkazového řádku.
  >
  >

Pokud Azure rozšířené monitorování rozšíření není nainstalována nebo není spuštěná AzureEnhancedMonitoring, rozšíření nebyla nakonfigurována správně. Podrobné informace o tom, jak nasadit rozšíření najdete v tématu [řešení potíží s Azure monitorování infrastruktury pro SAP][deployment-guide-5.3].

##### <a name="check-the-output-of-azperflibexe"></a>Zkontrolujte výstup azperflib.exe
Azperflib.exe výstup ukazuje, že všechny čítače výkonu Azure vyplněný pro SAP. V dolní části seznamu shromážděných čítače indikátoru stavu a souhrn zobrazit stav monitorování Azure.

![Výstup Kontrola stavu spuštěním azperflib.exe, který označuje, že neexistují žádné problémy][deployment-guide-figure-1100]
<a name="figure-11"></a>

Zkontrolujte výsledek vrácený pro **čítače celkem** výstupu, který se použije v hlášení jako prázdný a pro **stav**, uvedené v předchozí obrázek.

Výsledné hodnoty interpretovat následujícím způsobem:

| Azperflib.exe výsledek hodnoty | Azure stav monitorování |
| --- | --- |
| **Volání rozhraní API – není k dispozici** | Čítače, které nejsou k dispozici může být buď není použitelná pro danou konfiguraci virtuálního počítače nebo chyby. V tématu **stav**. |
| **Čítače Celkový – prázdné** |Následující dva čítače úložiště Azure může být prázdný: <ul><li>Čtení z úložiště Op latence serveru MS</li><li>Čtení z úložiště Op latence E2E MS</li></ul>Všechny ostatní čítače musí mít hodnoty. |
| **Stav** |Pouze OK Pokud vrátit stav ukazuje **OK**. |
| **Diagnostika** |Podrobné informace o stavu. |

Pokud **stav** hodnota není **OK**, postupujte podle pokynů v [Kontrola stavu pro konfiguraci Azure monitorování infrastruktury][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Spustit kontrolu připravenosti na virtuální počítač s Linuxem

1.  Na virtuálním počítači Azure připojte pomocí protokolu SSH.

2.  Zkontrolujte výstup Azure rozšířené monitorování rozšíření.

  a.  Spusťte `more /var/lib/AzureEnhancedMonitor/PerfCounters`.

   **Očekávaný výsledek**: vrátí seznam čítačů výkonu. Soubor by neměl být prázdný.

 b. Spusťte `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`.

   **Očekávaný výsledek**: vrátí jeden řádek, kde je chyba **žádné**, například **3; konfigurace; Chyba; 0; 0; žádná; 0; 1456416792; tst-servercs;**

  c. Spusťte `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`.

    **Očekávaný výsledek**: vrátí jako prázdný nebo neexistuje.

Pokud předchozí kontroly nebyla úspěšná, spusťte tyto další kontroly:

1.  Ujistěte se, že příkaz waagent nainstalován a povolen.

  a.  Spusťte `sudo ls -al /var/lib/waagent/`.

      **Očekávaný výsledek**: obsahuje seznam obsahu, který příkaz waagent adresáře.

  b.  Spusťte `ps -ax | grep waagent`.

   **Očekávaný výsledek**: Zobrazí jednu položku podobnou: `python /usr/sbin/waagent -daemon`

3.   Ujistěte se, zda je rozšíření monitorování rozšířeného Azure nainstalovaná a spuštěná.

  a.  Spusťte `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`.

    **Očekávaný výsledek**: uvádí obsah adresáře Azure rozšířeného rozšíření monitorování.

  b. Spusťte `ps -ax | grep AzureEnhanced`.

     **Očekávaný výsledek**: Zobrazí jednu položku podobnou: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

3. Nainstalujte agenta hostitele SAP, jak je popsáno v Poznámka SAP [1031096]a zkontrolujte výstup `saposcol`.

  a.  Spusťte `/usr/sap/hostctrl/exe/saposcol -d`.

  b.  Spusťte `dump ccm`.

  c.  Zkontrolujte, zda **Virtualization_Configuration\Enhanced monitorování přístupu** metrika **true**.

Pokud je již nainstalována aplikační server SAP NetWeaver ABAP, otevřete transakce ST06 sekce a zkontrolujte, zda je povoleno rozšířené monitorování.

Pokud některý z těchto kontrol selhání a podrobné informace o tom, jak znovu nasaďte rozšíření najdete v tématu [řešení potíží s Azure monitorování infrastruktury pro SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Kontrola stavu pro konfiguraci Azure monitorování infrastruktury
Pokud některé z monitorování dat není správně, jak je uvedeno testem popsané v doručí [kontrolu připravenosti pro Azure rozšířené monitorování pro SAP][deployment-guide-5.1]spusťte `Test-AzureRmVMAEMExtension` rutiny zkontrolujte, zda Azure monitorování infrastruktury a monitorování rozšíření pro SAP jsou správně nakonfigurované.

1.  Ujistěte se, že instalaci nejnovější verze rutiny Azure Powershellu, jak je popsáno v [rutin nasazení prostředí Azure PowerShell][deployment-guide-4.1].
2.  Spusťte následující rutinu Azure PowerShellu. Seznam dostupných prostředí, spusťte rutinu `Get-AzureRmEnvironment`. Chcete-li použít globální Azure, vyberte **AzureCloud** prostředí. Azure v Číně, vyberte **AzureChinaCloud**.
  ```powershell
  $env = Get-AzureRmEnvironment -Name <name of the environment>
  Connect-AzureRmAccount -Environment $env
  Set-AzureRmContext -SubscriptionName <subscription name>
  Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
  ```

3.  Zadejte vaše data na účtu a identifikovat virtuální počítač Azure.

  ![Vstupní stránky specifické pro SAP Azure rutiny Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

4. Skript testy konfigurace virtuálního počítače, které vyberete.

  ![Výstup úspěšný test Azure monitorování infrastruktury pro SAP][deployment-guide-figure-1300]

Ujistěte se, že je každý výsledek kontroly stavu **OK**. Pokud nějaké kontroly nezobrazovat **OK**, spusťte rutinu aktualizace, jak je popsáno v [konfiguraci Azure rozšířené monitorování rozšíření přístup k SAP][deployment-guide-4.5]. Počkejte 15 minut a opakujte kontroly popsané v [kontrolu připravenosti pro Azure rozšířené monitorování pro SAP] [ deployment-guide-5.1] a [Kontrola stavu pro konfigurace monitorování infrastruktury Azure][deployment-guide-5.2]. Pokud kontroly stále znamenat problém související s některé nebo všechny čítače, přečtěte si téma [řešení potíží s Azure monitorování infrastruktury pro SAP][deployment-guide-5.3].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Řešení potíží s Azure monitorování infrastruktury pro SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Čítače výkonu Azure se nezobrazí vůbec
Služba systému AzureEnhancedMonitoring Windows shromažďuje metriky výkonu v Azure. Pokud služba není správně nainstalovaná. nebo pokud není spuštěn v virtuálního počítače, se můžou shromažďovat metrik výkonu.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Instalační adresář Azure rozšířené monitorování rozšíření je prázdná

###### <a name="issue"></a>Problém
Instalační adresář C:\\balíčky\\modulů plug-in\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;verze >\\vynechání je prázdný.

###### <a name="solution"></a>Řešení
Rozšíření není nainstalována. Určí, zda se jedná o problém proxy (jak je popsáno výše). Možná muset restartovat počítač nebo znovu spustit `Set-AzureRmVMAEMExtension` konfigurační skript.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Služba pro rozšířené monitorování Azure neexistuje.

###### <a name="issue"></a>Problém
Služba systému AzureEnhancedMonitoring Windows neexistuje.

Výstup Azperflib.exe vrátí chybu:

![Provádění azperflib.exe označuje, zda není spuštěna služba Azure rozšířené monitorování rozšíření pro SAP][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Řešení
Pokud služba neexistuje, Azure rozšířené monitorování rozšíření pro SAP nebyl nainstalován správně. Znovu nasaďte rozšíření pomocí kroků popsaných pro váš scénář nasazení v [scénáře nasazení virtuálních počítačů pro SAP v Azure][deployment-guide-3].

Poté, co nasadíte rozšíření, po jedné hodině, zkontrolujte znovu, zda jsou čítače výkonu Azure součástí virtuálních počítačů Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Služba pro rozšířené monitorování Azure existuje, ale nepodaří spustit

###### <a name="issue"></a>Problém
Služba systému AzureEnhancedMonitoring Windows existuje a je povoleno, ale nepodaří spustit. Další informace najdete v protokolu událostí aplikace.

###### <a name="solution"></a>Řešení
Konfigurace je nesprávná. Restartujte rozšíření monitorování pro virtuální počítač, jak je popsáno v [konfiguraci Azure rozšířené monitorování rozšíření přístup k SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Chybí některé čítače výkonu Azure
Služba systému AzureEnhancedMonitoring Windows shromažďuje metriky výkonu v Azure. Služba získá data z několika zdrojů. Některé konfigurační data se shromažďují místně a některé metriky výkonu se načítají z Azure Diagnostics. Z vaší protokolování na úrovni předplatného úložiště jsou použity čítače úložiště.

Pokud řešení potíží pomocí příkazu Poznámka SAP [1999351] problém nevyřešíte, spusťte znovu `Set-AzureRmVMAEMExtension` konfigurační skript. Možná bude muset počkat hodinu, protože úložiště analytics nebo diagnostiky čítače nemusí být vytvářeny ihned po jsou povoleny. Pokud potíže potrvají, otevřete zprávu SAP zákaznické podpory na BC OP NT AZR součásti pro systém Windows nebo BC-OP-LNX-AZR pro virtuální počítač s Linuxem.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Čítače výkonu Azure se nezobrazí vůbec
Démon shromážděné metriky výkonu v Azure. Pokud není spuštěn démon, se můžou shromažďovat metrik výkonu.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Instalační adresář rozšíření Azure rozšířené monitorování je prázdná

###### <a name="issue"></a>Problém
Adresář \\var\\lib\\příkaz waagent\\ nemá podadresáři rozšíření Azure rozšířené monitorování.

###### <a name="solution"></a>Řešení
Rozšíření není nainstalována. Určí, zda se jedná o problém proxy (jak je popsáno výše). Možná budete muset restartovat počítač a znovu spustit `Set-AzureRmVMAEMExtension` konfigurační skript.

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Chybí některé čítače výkonu Azure
Démon procesu, který získá data z několika zdrojů shromážděné metriky výkonu v Azure. Některé konfigurační data se shromažďují místně a některé metriky výkonu se načítají z Azure Diagnostics. Úložiště čítače pocházet z protokolů v rámci vašeho předplatného úložiště.

Pro dokončení a aktuální seznam známých problémů, viz poznámka SAP [1999351], která obsahuje další informace o řešení potíží pro rozšířené monitorování Azure pro SAP.

Pokud řešení potíží pomocí příkazu Poznámka SAP [1999351] neobsahuje problém vyřešit, spusťte znovu `Set-AzureRmVMAEMExtension` konfigurační skript, jak je popsáno v [konfiguraci Azure rozšířené monitorování rozšíření přístup k SAP][deployment-guide-4.5]. Bude pravděpodobně nutné čekat na jednu hodinu, protože úložiště analytics nebo diagnostiky čítače nemusí vytvořit okamžitě po jsou povoleny. Pokud potíže potrvají, otevřete zprávu SAP zákaznické podpory na BC OP NT AZR součásti pro systém Windows nebo BC-OP-LNX-AZR pro virtuální počítač s Linuxem.
