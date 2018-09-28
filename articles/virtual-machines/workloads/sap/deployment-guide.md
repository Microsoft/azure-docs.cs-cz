---
title: Nasazení virtuálních počítačů pro SAP NetWeaver v Azure | Dokumentace Microsoftu
description: Informace o nasazení softwaru SAP na virtuálních počítačích s Linuxem v Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: sedusch
ms.openlocfilehash: 9208f2cb207daff2b122550fede48a8dda11d1db
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407922"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Nasazení virtuálních počítačů pro SAP NetWeaver v Azure
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
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Nasazení Azure Virtual Machines DBMS pro SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Ukládání do mezipaměti pro virtuální počítače a virtuální pevné disky)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Softwarového pole RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktura nasazení relační databázový systém)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Vysoká dostupnost a zotavení po havárii pomocí virtuálních počítačů Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 nebo novější)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 a dřívějších verzích)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Pomocí bitové kopie systému SQL Server na Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Obecné systém SQL Server pro SAP v Azure souhrn)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifika na relační databázový systém SQL Server)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Konfigurace úložiště)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Zálohování a obnovení)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Otázky výkonu při zálohování a obnovení)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Ostatní)
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

[deployment-guide]:deployment-guide.md (Nasazení virtuálních počítačů pro SAP v Azure)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Prostředky SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Nasazení virtuálního počítače s použitím vlastní image)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scénář 1: Nasazení virtuálního počítače na webu Azure Marketplace pro SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scénář 2: Nasazení virtuálního počítače s použitím vlastní image pro SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scénář 3: Přesunutí virtuálního počítače pomocí virtuálního pevného disku není zobecněný Azure s řešením SAP v místním)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scénáře nasazení virtuálních počítačů pro SAP v Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Zavedení rutin Azure Powershellu)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Stáhnout a importovat rutiny Powershellu příslušných SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Připojení virtuálního počítače s místní doménou – pouze Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Prostředí Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Rozhraní příkazového řádku Azure)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Konfigurovat rozšíření Azure rozšířené monitorování pro SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Kontrola připravenosti pro Azure Enhanced Monitoring pro SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Kontroly stavu pro monitorování infrastruktury Azure)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Řešení potíží pro SAP pro monitorování Azure)

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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Kontroly a řešení potíží pro nastavení monitorování začátku do konce)

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

[msdn-set-azurermvmaemextension]:https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmaemextension

[planning-guide]:planning-guide.md (Azure Virtual Machines, plánování a implementace SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Prostředky)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Vysoká dostupnost a zotavení po havárii pro SAP NetWeaver provozovaný v Azure Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Vysoká dostupnost pro aplikační servery SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Pomocí automatické spuštění pro instance SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Výhradně cloudový – nasazení virtuálních počítačů v Azure bez závislosti na místní síť zákazníka)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Mezi různými místy – nasazení jednoho nebo několika virtuálních počítačích SAP v Azure plně integrovaná s místní sítí)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Oblasti Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domény selhání)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Upgradovací domény)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Skupinu dostupnosti Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Koncept virtuální počítače Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Přesun virtuálního počítače z místního Azure s-zobecněný disk)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Nasazení virtuálního počítače s konkrétní imagí zákazníka)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Příprava pro přesun virtuálního počítače z místního Azure s-zobecněný disk)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Příprava pro nasazení virtuálního počítače s konkrétní imagí zákazníka pro SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Příprava virtuálních počítačů s řešením SAP pro Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Rozdíl mezi disku Azure a Azure obrázku)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Odeslání VHD z místního úložiště do Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopírování disků mezi účty Azure Storage)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struktura virtuálního počítače nebo virtuálního pevného disku pro nasazení SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Nastavení automatického připojení pro připojené disky)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Jeden virtuální počítač se SAP Netweaverem ukázku/trénovací scénář)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Koncepty cloudového nasazení instancí SAP)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure řešení pro monitorování pro SAP)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Síťových služeb Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Úložiště: Microsoft Azure Storage a datové disky)

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Přehled dostupnosti SAP produktu)
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
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Nasazení a správa virtuálních počítačů pomocí šablon Azure Resource Manageru a Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Správa virtuálních počítačů pomocí Azure Resource Manageru a Powershellu)
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

Azure Virtual Machines je řešení pro organizace, které potřebují výpočetní a úložnou kapacitu, minimální včas a bez zdlouhavé zajišťování cykly. Azure Virtual Machines můžete použít k nasazení klasického aplikací, jako jsou aplikace založené na systému SAP NetWeaver v Azure. Rozšíření aplikace spolehlivosti a dostupnosti bez další místní prostředky. Azure Virtual Machines podporuje připojení mezi místními sítěmi, Azure Virtual Machines vám umožní integraci do místní domény vaší organizace, privátních cloudů a prostředí systému SAP.

V tomto článku se budeme zabývat kroky k nasazení aplikací SAP na virtuálních počítačích (VM) v Azure, včetně možnosti alternativní nasazení a řešení potíží. Tento článek vychází z informací v [Azure Virtual Machines plánování a implementace SAP Netweaveru][planning-guide]. Také doplňuje dokumentaci k instalaci SAP a SAP poznámky, které jsou primární zdroje pro instalaci a nasazení softwaru SAP.

## <a name="prerequisites"></a>Požadavky
Nastavení virtuálního počítače Azure pro nasazení softwaru SAP zahrnuje více kroků a prostředky. Než začnete, ujistěte se, že splňujete požadavky pro instalaci softwaru SAP na virtuálních počítačích v Azure.

### <a name="local-computer"></a>Místní počítač
Pokud chcete spravovat, Windows nebo Linuxem, můžete použít skript prostředí PowerShell a webu Azure portal. Pro oba nástroje budete potřebovat místní počítač se systémem Windows 7 nebo novější verzi Windows. Pokud chcete spravovat pouze virtuální počítače s Linuxem a chcete použít počítač s Linuxem pro tuto úlohu, můžete použít rozhraní příkazového řádku Azure.

### <a name="internet-connection"></a>Připojení k Internetu
Ke stažení a spuštění nástroje a skripty, které jsou požadovány pro nasazení softwaru SAP, musíte být připojeni k Internetu. Virtuální počítač Azure, na kterém běží rozšířené monitorování rozšíření Azure pro SAP také potřebuje přístup k Internetu. Pokud virtuální počítač Azure v rámci virtuální sítě Azure nebo místní domény, ujistěte se, že jsou nastavené příslušné nastavení serveru proxy, jak je popsáno v [proxy server nakonfigurovat][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Předplatné Microsoft Azure
Budete potřebovat aktivní účet Azure.

### <a name="topology-and-networking"></a>Topologie a sítě
Budete muset definovat topologii a architektura nasazení SAP v Azure:

* Účty služby Azure storage, který se má použít
* Virtuální síť, ve které chcete nasadit systém SAP
* Skupina prostředků, na kterou chcete nasazení systému SAP
* Oblasti Azure, ve které chcete nasadit systém SAP
* Konfigurace SAP (dvouvrstvé a třívrstvé)
* Velikosti virtuálních počítačů a řady dalších datových disků připojit k virtuálním počítačům
* Konfigurace SAP opravy a přenosu systému (CTS)

Vytvoření a konfigurace účtů služby Azure storage (v případě potřeby) nebo virtuální sítě Azure, před zahájením procesu nasazení softwaru SAP. Informace o tom, jak vytvořit a nakonfigurovat těchto prostředků najdete v tématu [Azure Virtual Machines plánování a implementace SAP Netweaveru][planning-guide].

### <a name="sap-sizing"></a>Nastavení velikosti SAP
Následující informace o určení velikosti SAP vědět:

* Plánované úlohy SAP, například pomocí nástroje pro rychlé přizpůsobení velikosti symbolů SAP a SAP aplikace výkonu Standard (přístupové body) číslo
* Vyžaduje procesor spotřebu prostředků a paměti systému SAP
* Požadované vstupně výstupní operace (vstupně-výstupních operací) za sekundu
* Požadované šířky pásma sítě konečné komunikace mezi virtuálními počítači v Azure
* Požadovaná šířka pásma sítě mezi místním výpočetním prostředkům a systémem Azure nasazení SAP

### <a name="resource-groups"></a>Skupiny prostředků
V Azure Resource Manageru pro správu všech prostředků aplikace ve vašem předplatném Azure můžete použít skupiny prostředků. Další informace najdete v tématu [přehled Azure Resource Manageru][resource-group-overview].

## <a name="resources"></a>Zdroje a prostředky

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Prostředky SAP
Pokud nastavujete nasazení softwaru SAP, budete potřebovat následující SAP prostředků:

* Poznámka SAP [1928533], který obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporovány pro nasazení softwaru SAP
  * Informace o kapacitě důležité pro velikosti virtuálních počítačů Azure
  * Podporované SAP software a operační systém (OS) a kombinace databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* Poznámka SAP [2015553] uvádí předpoklady pro nasazení softwaru SAP nepodporuje SAP v Azure.
* Poznámka SAP [2178632] podrobné informace o všech monitorování metrik pro SAP v Azure.
* Poznámka SAP [1409604] má požadovaná verze SAP hostitele agenta pro Windows v Azure.
* Poznámka SAP [2191498] má požadovaná verze SAP hostitele agenta pro Linux v Azure.
* Poznámka SAP [2243692] obsahuje informace o SAP programech v Linuxu v Azure.
* Poznámka SAP [1984787] obsahuje obecné informace o operačním systémem SUSE Linux Enterprise Server 12.
* Poznámka SAP [2002167] obsahuje obecné informace o Red Hat Enterprise Linux 7.x.
* Poznámka SAP [2069760] obsahuje obecné informace o Oracle Linux 7.x.
* Poznámka SAP [1999351] obsahuje další informace o odstraňování potíží pro rozšířené monitorování rozšíření Azure pro SAP.
* Poznámka SAP [1597355] obsahuje obecné informace o odkládacího prostoru pro Linux.
* [SAP v Azure oznámení změny stavu stránky](https://wiki.scn.sap.com/wiki/x/Pia7Gg) obsahuje zprávy a kolekce užitečné zdroje informací.
* [WIKI komunity SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* Rutiny prostředí PowerShell specifické pro SAP, které jsou součástí [prostředí Azure PowerShell][azure-ps].
* Příkazy Azure CLI specifické pro SAP, které jsou součástí [rozhraní příkazového řádku Azure][azure-cli].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Materiály pro Windows
Tyto články Microsoft pokrytí nasazení SAP v Azure:

* [Azure Virtual Machines, plánování a implementace SAP NetWeaver][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP NetWeaver (Tento článek)][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP NetWeaver][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scénáře nasazení softwaru SAP na virtuálních počítačích Azure
Máte několik možností pro nasazení virtuálních počítačů a přidružené disky v Azure. Je důležité znát rozdíly mezi možnostmi nasazení, protože může trvat jiný zásah při přípravě na vašich virtuálních počítačů pro nasazení na základě typu nasazení, které zvolíte.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scénář 1: Nasazení virtuálního počítače na webu Azure Marketplace pro SAP
Obrázek k dispozici společností Microsoft nebo třetích stran na Azure Marketplace můžete použít k nasazení virtuálního počítače. Na webu Marketplace nabízí některá standardní bitové kopie operačního systému Windows Server a různých Linuxových distribucí. Také můžete nasadit bitovou kopii, která obsahuje databázi management system (DBMS) skladové položky, například Microsoft SQL Server. Další informace o používání imagí DBMS skladových položek, naleznete v tématu [nasazení DBMS virtuálních počítačů Azure pro SAP NetWeaver][dbms-guide].

Následující diagram ukazuje specifické pro SAP postupně jednotlivé kroky pro nasazení virtuálního počítače z Azure Marketplace:

![Vývojový diagram nasazení virtuálního počítače pro systémů SAP pomocí image virtuálního počítače z Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače pomocí webu Azure portal
Nejjednodušší způsob, jak vytvořit nový virtuální počítač pomocí image z Azure Marketplace je pomocí webu Azure portal.

1.  Přejděte do části <https://portal.azure.com/#create/hub> (Soubor > Nový > Jiné).  Nebo v nabídce portálu Azure vyberte **+ nová**.
1.  Vyberte **Compute**a pak vyberte typ operačního systému, kterou chcete nasadit. Například Windows Server 2012 R2, operačním systémem SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) nebo Oracle Linux 7.2. Výchozí zobrazení seznamu nezobrazuje, že všechny podporované operační systémy. Vyberte **zobrazit všechny** zobrazit úplný seznam. Další informace o podporovaných operačních systémech pro nasazení softwaru SAP, viz poznámka SAP [1928533].
1.  Na další stránce přečtěte si podmínky a ujednání.
1.  V **vybrat model nasazení** vyberte **Resource Manageru**.
1.  Vyberte **Vytvořit**.

Průvodce vás provede nastavením povinné parametry pro vytvoření virtuálního počítače, kromě všechny požadované prostředky, jako jsou síťovým rozhraním a účty úložiště. Zde jsou některé z těchto parametrů:

1. **Základy**:
 * **Název**: název prostředku (název virtuálního počítače).
 * **Typ disku virtuálního počítače**: Vyberte typ disku disk s operačním systémem. Pokud chcete používat Premium Storage pro datové disky, doporučujeme používat Premium Storage pro disk s operačním systémem i.
 * **Uživatelské jméno a heslo** nebo **veřejný klíč SSH**: Zadejte uživatelské jméno a heslo uživatele, který se vytvoří během zřizování. Pro virtuální počítač s Linuxem můžete zadat veřejný klíč Secure Shell (SSH), který používáte k přihlášení k počítači.
 * **Předplatné**: Vyberte předplatné, pro kterou chcete použít ke zřízení nového virtuálního počítače.
 * **Skupina prostředků**: název skupiny prostředků pro virtuální počítač. Můžete zadat název nové skupiny prostředků nebo název skupiny prostředků, který již existuje.
 * **Umístění**: kde nasadíte nový virtuální počítač. Pokud chcete virtuální počítač připojit k vaší místní sítě, ujistěte se, že vyberete umístění virtuální sítě, která se připojuje k vaší místní síti Azure. Další informace najdete v tématu [síťových služeb Microsoft Azure] [ planning-guide-microsoft-azure-networking] v [Azure Virtual Machines plánování a implementace SAP Netweaveru] [ planning-guide].
1. **Velikost**:

     Seznam podporovaných typů virtuálních počítačů, viz poznámka SAP [1928533]. Ujistěte se, zda že jste vybrali správný typ virtuálního počítače, pokud chcete používat Azure Premium Storage. Ne všechny typy virtuálních počítačů podporují službu Premium Storage. Další informace najdete v tématu [úložiště: Microsoft Azure Storage a datové disky] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] a [Azure Premium Storage] [ planning-guide-azure-premium-storage] v [ Azure Virtual Machines, plánování a implementace SAP Netweaveru][planning-guide].

1. **Nastavení**:
  * **Úložiště**
    * **Typ disku**: Vyberte typ disku disk s operačním systémem. Pokud chcete používat Premium Storage pro datové disky, doporučujeme používat Premium Storage pro disk s operačním systémem i.
    * **Použití spravovaných disků**: Pokud chcete používat službu Managed Disks, vyberte Ano. Další informace o službě Managed Disks, naleznete v kapitole [Managed Disks] [ planning-guide-managed-disks] v Průvodci plánem.
    * **Účet úložiště**: Vyberte existující účet úložiště nebo vytvořte novou. Ne všechny typy úložišť fungovat pro provozování aplikací SAP. Další informace o typech úložiště najdete v tématu [Microsoft Azure Storage] [ dbms-guide-2.3] v [nasazení DBMS virtuálních počítačů Azure pro SAP NetWeaver] [ dbms-guide].
  * **Síť**
    * **Virtuální síť** a **podsítě**: virtuální počítač integrovat intranetu, vyberte virtuální síť, která je připojená k vaší místní síti.
    * **Veřejná IP adresa**: Vyberte veřejnou IP adresu, kterou chcete použít, nebo zadejte parametry pro vytvoření nové veřejné IP adresy. Veřejné IP adresy můžete použít pro přístup k virtuálnímu počítači přes Internet. Ujistěte se, že vytvoříte skupinu zabezpečení sítě můžete líp zabezpečit přístup k vašemu virtuálnímu počítači.
    * **Skupina zabezpečení sítě**: Další informace najdete v tématu [řízení toku provozu sítě s použitím skupin zabezpečení sítě][virtual-networks-nsg].
  * **Rozšíření**: můžete nainstalovat rozšíření virtuálního počítače tak, že přidáte do nasazení. Nemusíte přidávat rozšíření v tomto kroku. Rozšíření vyžadované pro podporu SAP jsou nainstalovány později. Naleznete v kapitole [konfigurovat rozšířené monitorování rozšíření Azure pro SAP] [ deployment-guide-4.5] v této příručce.
  * **Vysoká dostupnost**: Vyberte skupinu dostupnosti, nebo zadejte parametry pro vytvoření nové skupiny dostupnosti. Další informace najdete v tématu [skupinami dostupnosti Azure][planning-guide-3.2.3].
  * **Monitorování**
    * **Diagnostika spouštění**: můžete vybrat **zakázat** pro diagnostiku spouštění.
    * **Diagnostika hostovaného operačního systému**: můžete vybrat **zakázat** pro monitorování diagnostiky.

1. **Souhrn**:

  Zkontrolujte zvolené položky a pak vyberte **OK**.

Nasazení virtuálního počítače ve skupině prostředků, kterou jste vybrali.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Vytvoření virtuálního počítače pomocí šablony
Virtuální počítač můžete vytvořit pomocí jedné z šablony SAP publikované v [úložišti GitHub šablon pro rychlý start azure –][azure-quickstart-templates-github]. Můžete také můžete ručně vytvořit virtuální počítač pomocí [webu Azure portal][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms], nebo [příkazovéhořádkuAzure] [virtual-machines-linux-tutorial].

* [**Šablona dvouvrstvé konfigurace (jenom jeden virtuální počítač)** (sap-2úroveň--image marketplace)][sap-templates-2-tier-marketplace-image]

  Dvouvrstvá systému s použitím pouze jeden virtuální počítač vytvoříte pomocí této šablony.
* [**Šablona dvouvrstvé konfigurace (jenom jeden virtuální počítač) - Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  K vytvoření dvouvrstvé systému pomocí pouze jednoho virtuálního počítače a spravované disky, pomocí této šablony.
* [**Šablona třívrstvé konfigurace (více virtuálních počítačů)** (sap-3úroveň--image marketplace)][sap-templates-3-tier-marketplace-image]

  Třívrstvá systému s využitím více virtuálních počítačů vytvoříte pomocí této šablony.
* [**Šablona třívrstvé konfigurace (více virtuálních počítačů) - Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Třívrstvé systému s použitím více virtuálních počítačů a Managed Disks vytvoříte pomocí této šablony.

Na webu Azure Portal zadejte následující parametry pro šablonu:

1. **Základy**:
  * **Předplatné**: předplatné, které chcete použít k nasazení šablony.
  * **Skupina prostředků**: Skupina prostředků k nasazení šablony. Můžete vytvořit novou skupinu prostředků, nebo můžete vybrat existující skupinu prostředků v předplatném.
  * **Umístění**: Pokud chcete nasadit šablonu. Pokud jste vybrali existující skupinu prostředků, se používá umístění této skupiny prostředků.

1. **Nastavení**:
  * **ID systému SAP**: ID systému SAP (SID).
  * **Typ operačního systému**: operačního systému, kterou chcete nasadit, například Windows Server 2012 R2, operačním systémem SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) nebo Oracle Linux 7.2.

    Zobrazení seznamu nezobrazuje, že všechny podporované operační systémy. Další informace o podporovaných operačních systémech pro nasazení softwaru SAP, viz poznámka SAP [1928533].
  * **Velikost systému SAP**: velikost systému SAP.

    Počet protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP bude systém vyžadovat, požádejte SAP technologické partnery nebo systémový integrátor.
  * **Dostupnost systému** (pouze šablona třívrstvé): dostupnost systému.

    Vyberte **HA** pro konfiguraci, která je vhodná pro instalaci s vysokou dostupností. Vytvoří se dvěma servery databáze a dva servery pro ABAP SAP Central Services (ASCS).
  * **Typ úložiště** (pouze šablona dvouvrstvé): typ úložiště používat.

    Pro větší systémy důrazně doporučujeme využít Azure Premium Storage. Další informace o typech úložiště naleznete v následujících zdrojích:
      * [Použití Azure Premium Storage SSD pro instanci SAP DBMS][2367194]
      * [Microsoft Azure Storage] [ dbms-guide-2.3] v [nasazení DBMS virtuálních počítačů Azure pro SAP NetWeaver][dbms-guide]
      * [Premium Storage: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
  * **Uživatelské jméno správce** a **heslo správce**: uživatelské jméno a heslo.
    Nový uživatel je vytvořený pro přihlášení k virtuálnímu počítači.
  * **Nové nebo existující podsíti**: Určuje, jestli se vytvoří nová virtuální síť a podsíť, nebo použít existující podsítě. Pokud již máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
  * **ID podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální síť ve kterých máte definované podsíti virtuálního počítače by se měla přiřadit k pojmenování ID tuto konkrétní podsíť. ID obvykle vypadá takto: /subscriptions/&lt;id předplatného > /resourceGroups/&lt;název skupiny prostředků > /providers/Microsoft.Network/virtualNetworks/&lt;název virtuální sítě > /subnets/&lt;název podsítě >

1. **Podmínky a ujednání**:  
    Přečtěte si a přijměte právní podmínky.

1.  Vyberte **Koupit**.

Agent virtuálního počítače Azure se nasadí ve výchozím nastavení, pokud použijete image z Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru
V závislosti na konfiguraci místní sítě můžete potřebovat nastavit proxy server na virtuálním počítači. Pokud se váš virtuální počítač je připojený k vaší místní síti prostřednictvím sítě VPN nebo ExpressRoute, virtuální počítač nemusí být možné získat přístup k Internetu a nebudou moct stáhnout požadované rozšíření nebo shromažďování dat monitorování. Další informace najdete v tématu [proxy server nakonfigurovat][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Připojte se k doméně (jenom Windows)
Pokud nasazení vašeho řešení Azure je připojené k místní službě Active Directory nebo DNS instance prostřednictvím Azure připojení site-to-site VPN nebo ExpressRoute (tomu se říká *mezi různými místy* v [plánování virtuálních počítačů Azure a implementaci SAP Netweaveru][planning-guide]), očekává se, že virtuální počítač připojuje k místní doméně. Další informace o aspektech týkajících se pro tuto úlohu, naleznete v tématu [připojení virtuálního počítače k doméně místní (jen Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurace monitorování
Opravdu SAP podporuje prostředí nastavit rozšíření monitorování Azure pro SAP, jak je popsáno v [konfigurovat rozšířené monitorování rozšíření Azure pro SAP][deployment-guide-4.5]. Kontrola předpokladů pro monitorování SAP a požadované minimální verze jádra SAP a SAP hostitele agenta, v prostředcích uvedených v [SAP prostředky][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Kontrola monitorování
Zkontrolujte, zda monitorování funguje, jak je popsáno v [kontroly a řešení potíží pro nastavení monitorování začátku do konce][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Kroky po nasazení
Po vytvoření virtuálního počítače a virtuální počítač nasazený, je potřeba nainstalovat požadované softwarové komponenty ve virtuálním počítači. Z důvodu pořadí instalace nasazení/softwaru v tomto typu nasazení virtuálních počítačů instalaci softwaru již musí být k dispozici, buď v Azure, na jiný virtuální počítač nebo jako disk, který je možné připojit. Nebo, zvažte použití mezi různými místy scénář, ve kterých připojení k místní prostředky (Instalace sdílených složek) zadané.

Po nasazení virtuálního počítače v Azure, použijte stejné pokyny a nástroje k instalaci softwaru SAP na vašem virtuálním počítači, stejně jako v místním prostředí. K instalaci softwaru SAP na Virtuálním počítači Azure, doporučujeme SAPU a Microsoftu nahrávat a ukládat instalačním médiu nástroje SAP na virtuálních pevných disků Azure nebo na Managed Disks, nebo vytvořit virtuální počítač Azure, který funguje jako souborový server, který má všechny požadované SAP instalačního média.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scénář 2: Nasazení virtuálního počítače s použitím vlastní image pro SAP
Vzhledem k tomu, že různé verze operačního systému nebo DBMS požadavky různých opravy, obrázky, které najdete na webu Azure Marketplace nemusí vašim potřebám. Místo toho můžete chtít vytvořit virtuální počítač s použitím vlastní image virtuálního počítače OS/DBMS, které můžete nasadit znovu později.
Použijete jiný postup k vytvoření privátní image pro Linux, než se vytvořte jedno pro Windows.

- - -
> ![Windows][Logo_Windows] Windows
>
> Příprava image Windows, který vám pomůže nasadit více virtuálních počítačů, nastavení Windows (např. Windows SID nebo název hostitele) abstrahovaná nebo na místní virtuální počítač generalizovaný. Můžete použít [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) provedete to tak.
>
> ![Linux][Logo_Linux] Linux
>
> K přípravě image Linuxu, který vám umožní nasadit více virtuálních počítačů, musí být některá nastavení Linux abstrahovaná nebo na místní virtuální počítač generalizovaný. Můžete použít `waagent -deprovision` provedete to tak. Další informace najdete v tématu [zachytit virtuální počítač s Linuxem běžících v Azure] [ virtual-machines-linux-capture-image] a [uživatelská příručka agenta Azure Linux][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Můžete připravit a vytvořit vlastní image a použijte ji k vytvoření více nových virtuálních počítačů. To je popsáno v [Azure Virtual Machines plánování a implementace SAP Netweaveru][planning-guide]. Nastavení databáze obsahu instalace nového systému SAP (záloha databáze obnoví z disku, který je připojen k virtuálnímu počítači) pomocí Správce zřizování softwaru SAP nebo přímo obnovení zálohy databáze ze služby Azure storage, pokud váš systém DBMS podporuje. Další informace najdete v tématu [nasazení DBMS virtuálních počítačů Azure pro SAP NetWeaver][dbms-guide]. Pokud jste již nainstalovali systémem SAP na vaše místní virtuální počítač (hlavně u dvouvrstvé systémy), můžete upravit nastavení systému SAP po nasazení virtuálního počítače Azure postupem přejmenovat systému podporována službou SAP Software zřizování správce (SAP Mějte na paměti [1619720]). Jinak můžete nainstalovat SAP software po nasazení virtuálního počítače Azure.

Následující diagram ukazuje specifické pro SAP postupně jednotlivé kroky k nasazení virtuálních počítačů z vlastní image:

![Vývojový diagram nasazení virtuálního počítače pro systémů SAP pomocí image virtuálního počítače v privátním Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače pomocí webu Azure portal
Nejjednodušší způsob, jak vytvořit nový virtuální počítač z bitové kopie spravovaného disku je pomocí webu Azure portal. Další informace o tom, jak vytvořit spravovat Image disku najdete [zachycení spravované image zobecněného virtuálního počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Přejděte do části <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages> (Soubor > Nový > Jiné). Nebo v nabídce portálu Azure vyberte **image**.
1.  Vyberte image spravovaného disku, kterou chcete nasadit a klikněte na **vytvořit virtuální počítač.**

Průvodce vás provede nastavením povinné parametry pro vytvoření virtuálního počítače, kromě všechny požadované prostředky, jako jsou síťovým rozhraním a účty úložiště. Zde jsou některé z těchto parametrů:

1. **Základy**:
 * **Název**: název prostředku (název virtuálního počítače).
 * **Typ disku virtuálního počítače**: Vyberte typ disku disk s operačním systémem. Pokud chcete používat Premium Storage pro datové disky, doporučujeme používat Premium Storage pro disk s operačním systémem i.
 * **Uživatelské jméno a heslo** nebo **veřejný klíč SSH**: Zadejte uživatelské jméno a heslo uživatele, který se vytvoří během zřizování. Pro virtuální počítač s Linuxem můžete zadat veřejný klíč Secure Shell (SSH), který používáte k přihlášení k počítači.
 * **Předplatné**: Vyberte předplatné, pro kterou chcete použít ke zřízení nového virtuálního počítače.
 * **Skupina prostředků**: název skupiny prostředků pro virtuální počítač. Můžete zadat název nové skupiny prostředků nebo název skupiny prostředků, který již existuje.
 * **Umístění**: kde nasadíte nový virtuální počítač. Pokud chcete virtuální počítač připojit k vaší místní sítě, ujistěte se, že vyberete umístění virtuální sítě, která se připojuje k vaší místní síti Azure. Další informace najdete v tématu [síťových služeb Microsoft Azure] [ planning-guide-microsoft-azure-networking] v [Azure Virtual Machines plánování a implementace SAP Netweaveru] [ planning-guide].
1. **Velikost**:

     Seznam podporovaných typů virtuálních počítačů, viz poznámka SAP [1928533]. Ujistěte se, zda že jste vybrali správný typ virtuálního počítače, pokud chcete používat Azure Premium Storage. Ne všechny typy virtuálních počítačů podporují službu Premium Storage. Další informace najdete v tématu [úložiště: Microsoft Azure Storage a datové disky] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] a [Azure Premium Storage] [ planning-guide-azure-premium-storage] v [ Azure Virtual Machines, plánování a implementace SAP Netweaveru][planning-guide].

1. **Nastavení**:
  * **Úložiště**
    * **Typ disku**: Vyberte typ disku disk s operačním systémem. Pokud chcete používat Premium Storage pro datové disky, doporučujeme používat Premium Storage pro disk s operačním systémem i.
    * **Použití spravovaných disků**: Pokud chcete používat službu Managed Disks, vyberte Ano. Další informace o službě Managed Disks, naleznete v kapitole [Managed Disks] [ planning-guide-managed-disks] v Průvodci plánem.
  * **Síť**
    * **Virtuální síť** a **podsítě**: virtuální počítač integrovat intranetu, vyberte virtuální síť, která je připojená k vaší místní síti.
    * **Veřejná IP adresa**: Vyberte veřejnou IP adresu, kterou chcete použít, nebo zadejte parametry pro vytvoření nové veřejné IP adresy. Veřejné IP adresy můžete použít pro přístup k virtuálnímu počítači přes Internet. Ujistěte se, že vytvoříte skupinu zabezpečení sítě můžete líp zabezpečit přístup k vašemu virtuálnímu počítači.
    * **Skupina zabezpečení sítě**: Další informace najdete v tématu [řízení toku provozu sítě s použitím skupin zabezpečení sítě][virtual-networks-nsg].
  * **Rozšíření**: můžete nainstalovat rozšíření virtuálního počítače tak, že přidáte do nasazení. Nepotřebujete přidat rozšíření v tomto kroku. Rozšíření vyžadované pro podporu SAP jsou nainstalovány později. Naleznete v kapitole [konfigurovat rozšířené monitorování rozšíření Azure pro SAP] [ deployment-guide-4.5] v této příručce.
  * **Vysoká dostupnost**: Vyberte skupinu dostupnosti, nebo zadejte parametry pro vytvoření nové skupiny dostupnosti. Další informace najdete v tématu [skupinami dostupnosti Azure][planning-guide-3.2.3].
  * **Monitorování**
    * **Diagnostika spouštění**: můžete vybrat **zakázat** pro diagnostiku spouštění.
    * **Diagnostika hostovaného operačního systému**: můžete vybrat **zakázat** pro monitorování diagnostiky.

1. **Souhrn**:

  Zkontrolujte zvolené položky a pak vyberte **OK**.

Nasazení virtuálního počítače ve skupině prostředků, kterou jste vybrali.
#### <a name="create-a-virtual-machine-by-using-a-template"></a>Vytvoření virtuálního počítače pomocí šablony
Vytvoření nasazení s použitím privátní image operačního systému z webu Azure portal, použijte jednu z následujících šablon SAP. Tyto šablony jsou zveřejněné v [úložišti GitHub šablon pro rychlý start azure –][azure-quickstart-templates-github]. Můžete také můžete ručně vytvořit virtuální počítač pomocí [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Šablona dvouvrstvé konfigurace (jenom jeden virtuální počítač)** (sap-2úroveň uživatel image)][sap-templates-2-tier-user-image]

  Dvouvrstvá systému s použitím pouze jeden virtuální počítač vytvoříte pomocí této šablony.
* [**Šablona dvouvrstvé konfigurace (jenom jeden virtuální počítač) - spravované Image disku** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  K vytvoření dvouvrstvé systému pomocí pouze jednoho virtuálního počítače a image spravovaného disku, pomocí této šablony.
* [**Šablona třívrstvé konfigurace (více virtuálních počítačů)** (sap-3úroveň uživatel image)][sap-templates-3-tier-user-image]

  Třívrstvá systému pomocí několika virtuálních počítačů nebo vlastní image operačního systému vytvoříte pomocí této šablony.
* [**Šablona třívrstvé konfigurace (více virtuálních počítačů) - spravované Image disku** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Třívrstvá systému pomocí několika virtuálních počítačů nebo vlastní image operačního systému a image spravovaného disku vytvoříte pomocí této šablony.

Na webu Azure Portal zadejte následující parametry pro šablonu:

1. **Základy**:
  * **Předplatné**: předplatné, které chcete použít k nasazení šablony.
  * **Skupina prostředků**: Skupina prostředků k nasazení šablony. Můžete vytvořit novou skupinu prostředků nebo vyberte existující skupinu prostředků v předplatném.
  * **Umístění**: Pokud chcete nasadit šablonu. Pokud jste vybrali existující skupinu prostředků, se používá umístění této skupiny prostředků.
1. **Nastavení**:
  * **ID systému SAP**: ID systému SAP
  * **Typ operačního systému**: typ operačního systému, kterou chcete nasadit (Windows nebo Linuxem).
  * **Velikost systému SAP**: velikost systému SAP.

    Počet protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP bude systém vyžadovat, požádejte SAP technologické partnery nebo systémový integrátor.
  * **Dostupnost systému** (pouze šablona třívrstvé): dostupnost systému.

    Vyberte **HA** pro konfiguraci, která je vhodná pro instalaci s vysokou dostupností. Vytvoří se dvěma servery databáze a dva servery pro ASC.
  * **Typ úložiště** (pouze šablona dvouvrstvé): typ úložiště používat.

    Pro větší systémy důrazně doporučujeme využít Azure Premium Storage. Další informace o typech úložiště najdete v článku na následujících odkazech:
      * [Použití Azure Premium Storage SSD pro instanci SAP DBMS][2367194]
      * [Microsoft Azure Storage] [ dbms-guide-2.3] v [nasazení DBMS virtuálních počítačů Azure pro SAP NetWeaver][dbms-guide]
      * [Premium Storage: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
  * **Uživatelská image URI virtuálního pevného disku** (pouze šablona nespravovaný disk image): identifikátor URI privátní operační systém obrazu virtuálního pevného disku, třeba https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **Účet úložiště bitových kopií uživatele** (pouze šablona nespravovaný disk image): název účtu úložiště, kde privátní image operačního systému uložena, například &lt;accountname > v https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
  * **userImageId** (pouze šablona image spravovaného disku): Id image spravovaného disku, kterou chcete použít
  * **Uživatelské jméno správce** a **heslo správce**: uživatelské jméno a heslo.

    Nový uživatel je vytvořený pro přihlášení k virtuálnímu počítači.
  * **Nové nebo existující podsíti**: Určuje, jestli se vytvoří nová virtuální síť a podsíť, nebo použít existující podsítě. Pokud již máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
  * **ID podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální síť ve kterých máte definované podsíti virtuálního počítače by se měla přiřadit k pojmenování ID tuto konkrétní podsíť. ID obvykle vypadá takto: /subscriptions/&lt;id předplatného > /resourceGroups/&lt;název skupiny prostředků > /providers/Microsoft.Network/virtualNetworks/&lt;název virtuální sítě > /subnets/&lt;název podsítě >

1. **Podmínky a ujednání**:  
    Přečtěte si a přijměte právní podmínky.

1.  Vyberte **Koupit**.

#### <a name="install-the-vm-agent-linux-only"></a>Nainstalujte agenta virtuálního počítače (pouze Linux)
Použití šablon je popsáno v předchozí části, agenta pro Linux musí už být nainstalovaný v bitové kopii uživatele nebo nasazení se nezdaří. Stáhněte a nainstalujte agenta virtuálního počítače v uživatelská image, jak je popsáno v [stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure][deployment-guide-4.4]. Pokud nepoužíváte šablony, můžete také nainstalovat agenta virtuálního počítače později.

#### <a name="join-a-domain-windows-only"></a>Připojte se k doméně (jenom Windows)
Pokud nasazení vašeho řešení Azure je připojené k místní službě Active Directory nebo DNS instance prostřednictvím Azure připojení k síti VPN site-to-site nebo Azure ExpressRoute (tomu se říká *mezi různými místy* v [Azure Virtual Machines plánování a implementace SAP Netweaveru][planning-guide]), očekává se, že virtuální počítač připojuje k místní doméně. Další informace o aspektech týkajících se pro tento krok najdete v tématu [připojení virtuálního počítače k doméně místní (jen Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru
V závislosti na konfiguraci místní sítě můžete potřebovat nastavit proxy server na virtuálním počítači. Pokud se váš virtuální počítač je připojený k vaší místní síti prostřednictvím sítě VPN nebo ExpressRoute, virtuální počítač nemusí být možné získat přístup k Internetu a nebudou moct stáhnout požadované rozšíření nebo shromažďování dat monitorování. Další informace najdete v tématu [proxy server nakonfigurovat][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Konfigurace monitorování
Opravdu SAP podporuje prostředí nastavit rozšíření monitorování Azure pro SAP, jak je popsáno v [konfigurovat rozšířené monitorování rozšíření Azure pro SAP][deployment-guide-4.5]. Kontrola předpokladů pro monitorování SAP a požadované minimální verze jádra SAP a SAP hostitele agenta, v prostředcích uvedených v [SAP prostředky][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Kontrola monitorování
Zkontrolujte, zda monitorování funguje, jak je popsáno v [kontroly a řešení potíží pro nastavení monitorování začátku do konce][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scénář 3: Přesunutí místní virtuální počítač pomocí virtuálního pevného disku není zobecněný Azure s řešením SAP
V tomto scénáři máte v úmyslu přesunout konkrétního systému SAP z místního prostředí do Azure. Můžete to provést tak, že nahrajete virtuálního pevného disku obsahujícího operační systém, binární soubory SAP a nakonec DBMS binární soubory a navíc virtuální pevné disky se soubory protokolu a data DBMS, do Azure. Na rozdíl od podle scénáře popsaného v [scénář 2: nasazení virtuálního počítače s použitím vlastní image pro SAP][deployment-guide-3.3], v tomto případě zachovat název hostitele, SAP SID a SAP uživatelské účty ve virtuálním počítači Azure, protože bylo nakonfigurováno v v místním prostředí. Nemusíte zobecní operační systém. Tento scénář týká nejčastěji mezi různými místy situacích, kdy běží v místním součástí prostředí SAP a jeho součástí běží na Azure.

V tomto scénáři je Agent virtuálního počítače **není** automaticky instalovanými během nasazení. Protože Agent virtuálního počítače a rozšířené monitorování rozšíření Azure pro SAP jsou potřeba ke spouštění SAP NetWeaver v Azure, musíte stáhnout, nainstalovat a povolit obě komponenty ručně po vytvoření virtuálního počítače.

Další informace o agenta virtuálního počítače Azure najdete v následující prostředky.

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

Následující diagram ukazuje postupně jednotlivé kroky pro přesun místní virtuální počítač pomocí virtuálního pevného disku není zobecněný Azure:

![Vývojový diagram nasazení virtuálního počítače pro SAP systémy pomocí disku virtuálního počítače][deployment-guide-figure-400]

Pokud disk už nahráli a definované v Azure (viz [Azure Virtual Machines plánování a implementace SAP Netweaveru][planning-guide]), proveďte úkoly popsané v následujících částech.

#### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
K vytvoření nasazení pomocí privátních disk s operačním systémem na webu Azure portal, použijte šablonu SAP publikovaných [úložišti GitHub šablon pro rychlý start azure –][azure-quickstart-templates-github]. Také můžete ručně vytvoříte virtuální počítač s použitím prostředí PowerShell.

* [**Šablona dvouvrstvé konfigurace (jenom jeden virtuální počítač)** (sap-2úroveň uživatele disk)][sap-templates-2-tier-os-disk]

  Dvouvrstvá systému s použitím pouze jeden virtuální počítač vytvoříte pomocí této šablony.
* [**Šablona dvouvrstvé konfigurace (jenom jeden virtuální počítač) - Managed Disks** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  K vytvoření dvouvrstvé systému pomocí pouze jednoho virtuálního počítače a spravovaný Disk, pomocí této šablony.

Na webu Azure Portal zadejte následující parametry pro šablonu:

1. **Základy**:
  * **Předplatné**: předplatné, které chcete použít k nasazení šablony.
  * **Skupina prostředků**: Skupina prostředků k nasazení šablony. Můžete vytvořit novou skupinu prostředků nebo vyberte existující skupinu prostředků v předplatném.
  * **Umístění**: Pokud chcete nasadit šablonu. Pokud jste vybrali existující skupinu prostředků, se používá umístění této skupiny prostředků.
1. **Nastavení**:
  * **ID systému SAP**: ID systému SAP
  * **Typ operačního systému**: typ operačního systému, kterou chcete nasadit (Windows nebo Linuxem).
  * **Velikost systému SAP**: velikost systému SAP.

    Počet protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP bude systém vyžadovat, požádejte SAP technologické partnery nebo systémový integrátor.
  * **Typ úložiště** (pouze šablona dvouvrstvé): typ úložiště používat.

    Pro větší systémy důrazně doporučujeme využít Azure Premium Storage. Další informace o typech úložiště najdete v článku na následujících odkazech:
      * [Použití Azure Premium Storage SSD pro instanci SAP DBMS][2367194]
      * [Microsoft Azure Storage] [ dbms-guide-2.3] v [nasazení DBMS virtuálního počítače Azure pro SAP NetWeaver][dbms-guide]
      * [Premium Storage: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
  * **Identifikátor URI VHD disku operačního systému** (pouze šablona nespravovaných disků): identifikátor URI soukromý disk s operačním systémem, třeba https://&lt;accountname >.blob.core.windows.net/vhds/osdisk.vhd.
  * **Id disku spravovaného disku operačního systému** (pouze šablona spravovaného disku): Id spravovaného disku operačního systému disku /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
  * **Nové nebo existující podsíti**: Určuje, jestli se vytvoří nová virtuální síť a podsíť, nebo použít existující podsítě. Pokud již máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
  * **ID podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální síť ve kterých máte definované podsíti virtuálního počítače by se měla přiřadit k pojmenování ID tuto konkrétní podsíť. ID obvykle vypadá takto: /subscriptions/&lt;id předplatného > /resourceGroups/&lt;název skupiny prostředků > /providers/Microsoft.Network/virtualNetworks/&lt;název virtuální sítě > /subnets/&lt;název podsítě >

1. **Podmínky a ujednání**:  
    Přečtěte si a přijměte právní podmínky.

1.  Vyberte **Koupit**.

#### <a name="install-the-vm-agent"></a>Nainstalujte agenta virtuálního počítače
Použití šablon je popsáno v předchozí části, musí být Agent virtuálního počítače nainstalovaný na disk s operačním systémem nebo nasazení se nezdaří. Stáhněte a nainstalujte agenta virtuálního počítače ve virtuálním počítači, jak je popsáno v [stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure][deployment-guide-4.4].

Pokud nepoužíváte šablony je popsáno v předchozí části, můžete také nainstalovat agenta virtuálního počítače později.

#### <a name="join-a-domain-windows-only"></a>Připojte se k doméně (jenom Windows)
Pokud nasazení vašeho řešení Azure je připojené k místní službě Active Directory nebo DNS instance prostřednictvím Azure připojení site-to-site VPN nebo ExpressRoute (tomu se říká *mezi různými místy* v [plánování virtuálních počítačů Azure a implementaci SAP Netweaveru][planning-guide]), očekává se, že virtuální počítač připojuje k místní doméně. Další informace o aspektech týkajících se pro tuto úlohu, naleznete v tématu [připojení virtuálního počítače k doméně místní (jen Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru
V závislosti na konfiguraci místní sítě můžete potřebovat nastavit proxy server na virtuálním počítači. Pokud se váš virtuální počítač je připojený k vaší místní síti prostřednictvím sítě VPN nebo ExpressRoute, virtuální počítač nemusí být možné získat přístup k Internetu a nebudou moct stáhnout požadované rozšíření nebo shromažďování dat monitorování. Další informace najdete v tématu [proxy server nakonfigurovat][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Konfigurace monitorování
Opravdu SAP podporuje prostředí nastavit rozšíření monitorování Azure pro SAP, jak je popsáno v [konfigurovat rozšířené monitorování rozšíření Azure pro SAP][deployment-guide-4.5]. Kontrola předpokladů pro monitorování SAP a požadované minimální verze jádra SAP a SAP hostitele agenta, v prostředcích uvedených v [SAP prostředky][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Kontrola monitorování
Zkontrolujte, zda monitorování funguje, jak je popsáno v [kontroly a řešení potíží pro nastavení monitorování začátku do konce][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Aktualizovat konfiguraci monitorování pro SAP
Aktualizujte konfiguraci monitorování SAP v některém z následujících scénářů:
* Společný tým Microsoft a SAP rozšiřuje možnosti monitorování a žádosti více nebo méně čítače.
* Microsoft zavádí novou verzi základní infrastrukturu Azure, který poskytuje data monitorování a rozšířené monitorování rozšíření Azure pro SAP musí být přizpůsobena tyto změny.
* Připojení dalších datových disků k virtuálnímu počítači Azure nebo odebrání datového disku. V tomto scénáři aktualizace kolekce dat týkajících se úložiště. Když změníte konfiguraci přidávání nebo odstraňování koncových bodů nebo přiřazení IP adres k virtuálnímu počítači nemá vliv na konfiguraci monitorování.
* Změníte velikost virtuálního počítače Azure, například velikost A5 pro ostatní velikosti virtuálních počítačů.
* Přidat nový síťová rozhraní k virtuálnímu počítači Azure.

Pokud chcete aktualizovat nastavení monitorování, aktualizovat monitorování infrastruktury podle postupu v [konfigurovat rozšířené monitorování rozšíření Azure pro SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Podrobný popis úkolů pro nasazení softwaru SAP
Tato část obsahuje podrobný postup k provádění konkrétních úkolů v procesu konfigurace a nasazení.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Nasazení rutin Azure Powershellu
1.  Přejděte na [Microsoft Azure, soubory ke stažení](https://azure.microsoft.com/downloads/).
1.  V části **nástroje příkazového řádku**v části **PowerShell**vyberte **nainstalovat Windows**.
1.  V dialogovém okně Správce stahování společnosti Microsoft pro stažený soubor (například WindowsAzurePowershellGet.3f.3f.3fnew.exe), vyberte **spustit**.
1.  Ke spuštění instalačního programu webové platformy (webové platformy společnosti Microsoft), vyberte **Ano**.
1.  Stránka, která vypadá jako tento popis se zobrazuje:

  ![Stránka instalaci rutinám Azure Powershellu][deployment-guide-figure-500]<a name="figure-5"></a>

1.  Vyberte **nainstalovat**a poté přijměte licenční podmínky pro Software společnosti Microsoft.
1.  Je nainstalovaný PowerShell. Vyberte **Dokončit** zavřete průvodce instalací.

Často vyhledat aktualizace do rutin prostředí PowerShell, které obvykle se aktualizuje každý měsíc. Nejjednodušší způsob, jak vyhledat aktualizace je provést předchozí kroky instalace až na stránku instalace uvedené v kroku 5. Číslo verze data a verze rutin jsou zahrnuty na stránce uvedené v kroku 5. Pokud není uvedeno jinak v Poznámka SAP [1928533] nebo Poznámka SAP [2015553], doporučujeme vám, že pracujete s nejnovější verzi rutin Azure Powershellu.

Pokud chcete zkontrolovat verzi rutin Azure Powershellu, které jsou nainstalovány v počítači, spusťte tento příkaz Powershellu:
```powershell
(Get-Module AzureRm.Compute).Version
```
Výsledek vypadá takto:

![Výsledek kontroly verze rutiny Azure Powershellu][deployment-guide-figure-600]
<a name="figure-6"></a>

Pokud v počítači nainstalovanou verzi rutiny Azure je současná verze, na první stránce Průvodce instalací označují, že přidáním **(nainstalovat)** pro název produktu (viz následující snímek obrazovky). Vaše rutiny prostředí PowerShell Azure jsou aktuální. Zavřete průvodce instalací, vyberte **ukončovací**.

![Stránka instalaci rutinám Azure Powershellu označující, zda jsou nainstalovány nejnovější verzi rutin Powershellu pro Azure][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Nasazení rozhraní příkazového řádku Azure
1.  Přejděte na [Microsoft Azure, soubory ke stažení](https://azure.microsoft.com/downloads/).
1.  V části **nástroje příkazového řádku**v části **rozhraní příkazového řádku Azure**, vyberte **nainstalovat** odkaz pro váš operační systém.
1.  V dialogovém okně Správce stahování společnosti Microsoft pro stažený soubor (například WindowsAzureXPlatCLI.3f.3f.3fnew.exe), vyberte **spustit**.
1.  Ke spuštění instalačního programu webové platformy (webové platformy společnosti Microsoft), vyberte **Ano**.
1.  Stránka, která vypadá jako tento popis se zobrazuje:

  ![Stránka instalaci rutinám Azure Powershellu][deployment-guide-figure-500]<a name="figure-5"></a>

1.  Vyberte **nainstalovat**a poté přijměte licenční podmínky pro Software společnosti Microsoft.
1.  Je nainstalovaný Azure CLI. Vyberte **Dokončit** zavřete průvodce instalací.

Často vyhledávat aktualizace rozhraní příkazového řádku Azure, které obvykle se aktualizuje každý měsíc. Nejjednodušší způsob, jak vyhledat aktualizace je provést předchozí kroky instalace až na stránku instalace uvedené v kroku 5.

Pokud chcete zkontrolovat verzi Azure CLI, který je nainstalován v počítači, spusťte tento příkaz:
```
azure --version
```

Výsledek vypadá takto:

![Výsledek kontroly verze rozhraní příkazového řádku Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Připojení virtuálního počítače k doméně místní (jen Windows)
Pokud nasadíte virtuální počítače SAP ve scénáři míst, kde v místní službě Active Directory a DNS jsou rozšířeny v Azure, očekává se, že virtuální počítače jsou připojení k místní doméně. Podrobný postup, jak připojit virtuální počítač s místní doménou a další software, musí být členem místní domény, se liší podle zákazníka. Obvykle virtuální počítač připojit k místní doméně, musíte nainstalovat další software, jako je antimalwarový software a software pro zálohování nebo monitorování.

V tomto scénáři musíte také ověřte, že pokud nastavení internetového proxy serveru jsou povinná, pokud virtuální počítač připojí k doméně ve vašem prostředí, Windows místní systémový účet (S-1-5-18) ve virtuálním počítači hosta má stejné nastavení proxy serveru. Nejjednodušší způsob je vynutit proxy server pomocí doménových zásadách skupiny, která se vztahuje na systémech v doméně.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure
Pro virtuální počítače, které jsou nasazeny z image operačního systému, který není zobecněný (například obrázek, který nepochází v nástroji pro přípravu systému Windows nebo nástroje sysprep,) budete muset ručně stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure.

Pokud provádíte nasazení virtuálního počítače z Azure Marketplace, tento krok není povinný. Image z Azure Marketplace je již Agent virtuálního počítače Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
1.  Stáhněte agenta virtuálního počítače Azure:
  1.  Stáhněte si [instalační balíček agenta virtuálního počítače Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
  1.  Balíček MSI agenta virtuálního počítače Store místně v osobním počítači nebo na serveru.
1.  Nainstalujte agenta virtuálního počítače Azure:
  1.  Připojení k nasazenému virtuálnímu počítači Azure s použitím protokolu RDP (Remote Desktop).
  1.  Otevřete okno Průzkumníka Windows na virtuálním počítači a vybrat cílový adresář pro soubor MSI agenta virtuálního počítače.
  1.  Přetáhněte soubor MSI Instalační služby agenta virtuálního počítače Azure ze svého místního počítače nebo serveru do cílového adresáře agenta virtuálního počítače na virtuálním počítači.
  1.  Poklikejte na soubor MSI ve virtuálním počítači.
1.  Pro virtuální počítače, které jsou připojené k místní domény, ujistěte se, že se konečné nastavení internetového proxy serveru platí také pro účet místního systému Windows (S-1-5-18) ve virtuálním počítači, jak je popsáno v [proxy server nakonfigurovat][deployment-guide-configure-proxy]. Agent virtuálního počítače v tomto kontextu spuštění a musí být schopný se připojit k Azure.

Aktualizace agenta virtuálního počítače Azure nevyžaduje žádná interakce s uživatelem. Agent virtuálního počítače se automaticky aktualizuje a nevyžaduje restartování virtuálního počítače.

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

Pokud je agent již nainstalovaný, chcete-li aktualizovat agenta Azure Linux postupujte podle pokynů popsaných v [aktualizace agenta Azure Linux ve virtuálním počítači na nejnovější verzi z Githubu][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurace proxy serveru
Kroky, jak nakonfigurovat proxy server ve Windows se liší od tak, jak nakonfigurovat proxy server v Linuxu.

#### <a name="windows"></a>Windows
Nastavení proxy serveru musí být zařídit správné nastavení pro místní systémový účet pro přístup k Internetu. Pokud vaše nastavení proxy serveru nejsou nastavené v zásadách skupiny, můžete nakonfigurovat nastavení pro účet místního systému.

1. Přejděte na **Start**, zadejte **gpedit.msc**a pak vyberte **Enter**.
1. Vyberte **konfigurace počítače** > **šablony pro správu** > **součásti Windows**  >   **Aplikace Internet Explorer**. Ujistěte se, že nastavení **vytvořit proxy nastavení vázaná na počítač (a ne na uživatele)** je zakázaná nebo není nakonfigurováno.
1. V **ovládací panely**, přejděte na stránku **Centrum sítí a sdílení** > **Možnosti Internetu**.
1. Na **připojení** kartu, vyberte **nastavení místní sítě** tlačítko.
1. Zrušte **automaticky zjišťovat nastavení** zaškrtávací políčko.
1. Vyberte **používat proxy server pro síť LAN** zaškrtněte políčko a zadejte adresu proxy serveru a port.
1. Vyberte **Upřesnit** tlačítko.
1. V **výjimky** zadejte IP adresu **168.63.129.16**. Vyberte **OK**.

#### <a name="linux"></a>Linux
Správný proxy server nakonfigurovat v konfiguračním souboru hostovaného agenta Microsoft Azure, které se nacházejí v \\atd\\waagent.conf.

Nastavte následující parametry:

1.  **HTTP proxy hostitele**. Například nastavte ho na **proxy.corp.local**.
  ```
  HttpProxy.Host=<proxy host>

  ```
1.  **Port proxy serveru HTTP**. Například nastavte ho na **80**.
  ```
  HttpProxy.Port=<port of the proxy host>

  ```
1.  Restartujte agenta.

  ```
  sudo service waagent restart
  ```

Nastavení proxy serveru v \\atd\\waagent.conf platí také pro požadované rozšíření virtuálních počítačů. Pokud chcete používat úložiště Azure, ujistěte se, že se provoz do těchto úložišť probíhá v místním intranetu. Když vytvoříte trasy definované uživatelem, chcete-li povolit vynucené tunelové propojení, ujistěte se, že přidáte trasu, která směruje přenosy do úložiště přímo k Internetu a nikoli prostřednictvím připojení VPN typu site-to-site.

* **SLES**

  Musíte také přidat trasy pro IP adresy uvedené v \\atd\\regionserverclnt.cfg. Následující obrázek ukazuje příklad:

  ![Vynucené tunelování][deployment-guide-figure-50]


* **RHEL**

  Budete také muset přidat trasy pro IP adresy hostitelů uvedených v \\atd\\yum.repos.d\\rhui Vyrovnávání zatížení. Příklad viz předchozí obrázek.

* **Oracle Linux**

  Neexistují žádná úložiště pro Oracle Linux v Azure. Budete muset nakonfigurovat vlastní úložiště pro Oracle Linux nebo použít veřejných úložištích.

Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem a předávání IP][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Konfigurovat rozšíření Azure rozšířené monitorování pro SAP
Pokud jste připravili virtuálního počítače, jak je popsáno v [scénáře nasazení virtuálních počítačů pro SAP v Azure][deployment-guide-3], Agent virtuálního počítače Azure je nainstalovaný na virtuálním počítači. Dalším krokem je nasazení rozšířené monitorování rozšíření Azure pro SAP, která je k dispozici v úložišti Azure rozšíření v globálních datových centrech Azure. Další informace najdete v tématu [Azure Virtual Machines plánování a implementace SAP Netweaveru][planning-guide-9.1].

Prostředí PowerShell nebo rozhraní příkazového řádku Azure můžete použít k instalaci a konfiguraci rozšířené monitorování rozšíření Azure pro SAP. Chcete-li nainstalovat rozšíření virtuálního počítače s Linuxem a Windows s použitím počítače s Windows, naleznete v tématu [prostředí Azure PowerShell][deployment-guide-4.5.1]. Chcete-li nainstalovat rozšíření virtuálního počítače s Linuxem pomocí plochy Linux, najdete v článku [rozhraní příkazového řádku Azure][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Prostředí Azure PowerShell pro systémy Linux a virtuální počítače s Windows
Chcete-li nainstalovat rozšířené monitorování rozšíření Azure pro SAP pomocí prostředí PowerShell:

1. Ujistěte se, že jste nainstalovali nejnovější verzi rutin Azure Powershellu. Další informace najdete v tématu [rutin nasazení prostředí Azure PowerShell][deployment-guide-4.1].  
1. Spusťte následující rutinu Azure PowerShellu.
    Seznam dostupných prostředí, spusťte `commandlet Get-AzureRmEnvironment`. Pokud chcete použít globální Azure, je vaše prostředí **AzureCloud**. Azure v Číně, vyberte **AzureChinaCloud**.

    ```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Connect-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Po zadání data vašeho účtu a identifikaci virtuálních počítačů Azure, skript nasadí požadované rozšíření a umožňuje požadované funkce. To může trvat několik minut.
Další informace o `Set-AzureRmVMAEMExtension`, naleznete v tématu [Set-AzureRmVMAEMExtension][msdn-set-azurermvmaemextension].

![Úspěšné provedení specifické pro SAP Azure rutiny Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

`Set-AzureRmVMAEMExtension` Konfigurace dělá všechny kroky ke konfiguraci monitorování pro SAP hostitele.

Výstup skriptu obsahuje následující informace:

* Potvrzení, že monitorování pro disk s operačním systémem a všechny další datové disky není nakonfigurovaná.
* Další dvě zprávy. potvrďte konfiguraci Storage Metrics pro konkrétnímu účtu úložiště.
* Jeden řádek výstupu poskytuje stav skutečné aktualizace konfigurace monitorování.
* Další řádek výstup potvrzuje, že konfigurace byly nasazeny nebo aktualizovány.
* Poslední řádek výstupu je informační. Ukazuje možnosti testování konfiguraci monitorování.
* Zkontrolujte, že všechny kroky pro Azure Enhanced Monitoring spustily úspěšně a, infrastruktura Azure poskytuje potřebná data, pokračujte kontroly připravenosti pro Azure Enhanced Monitoring rozšíření pro SAP, jak je popsáno v [Kontroly připravenosti pro Azure Enhanced Monitoring for SAP][deployment-guide-5.1].
* Počkejte 15 – 30 minut, než se Azure Diagnostics shromažďovat relevantní data.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI pro virtuální počítače s Linuxem
Instalace rozšířené monitorování rozšíření Azure pro SAP pomocí Azure CLI:

   1. Instalace Azure classic CLI, jak je popsáno v [instalace Azure classic CLI][azure-cli].
   1. Přihlaste se pomocí svého účtu Azure:

      ```
      azure login
      ```

   1. Přepnout do režimu Azure Resource Manageru:

      ```
      azure config mode arm
      ```

   1. Povolte Azure Enhanced Monitoring:

      ```
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Instalace pomocí příkazového řádku Azure CLI 2.0

   1. Instalace Azure CLI 2.0, jak je popsáno v [instalace Azure CLI 2.0][azure-cli-2].
   1. Přihlaste se pomocí svého účtu Azure:

      ```
      az login
      ```

   1. Nainstalujte rozšíření Azure CLI AEM
  
      ```
      az extension add --name aem
      ```
  
   1. Instalace rozšíření
  
      ```
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Ověřte, že Azure Enhanced Monitoring rozšíření aktivní na Linuxovém virtuálním počítači Azure. Zkontrolujte, zda soubor \\var\\lib\\AzureEnhancedMonitor\\PerfCounters existuje. Pokud existuje, na příkazovém řádku, spusťte tento příkaz pro zobrazení údajů shromážděných službou Azure Monitor rozšířené:

   ```
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Výstup vypadá takto:
   ```
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Kontroly a řešení potíží pro monitorování začátku do konce
Po nasazení virtuálního počítače Azure a nastavit příslušné monitorování infrastruktury Azure, zkontrolujte, zda jsou všechny komponenty Azure Enhanced Monitoring rozšíření funguje podle očekávání.

Spuštění kontroly připravenosti pro Azure Enhanced Monitoring rozšíření pro SAP, jak je popsáno v [kontroly připravenosti pro Azure Enhanced Monitoring rozšíření pro SAP][deployment-guide-5.1]. Pokud jsou všechny výsledky kontroly připravenosti kladné a zobrazí všechny relevantní čítače OK, monitorování Azure byl úspěšně nastaven. Instalace agenta hostitele SAP může pokračovat, jak je popsáno v poznámkách k SAP v [SAP prostředky][deployment-guide-2.2]. Pokud kontrolu připravenosti označuje, že nebyly nalezeny čítače, spusťte kontrolu stavu pro monitorování infrastruktury Azure, jak je popsáno v [Kontrola stavu pro konfiguraci Azure monitorování infrastruktury] [ deployment-guide-5.2]. Více možností pro řešení potíží, najdete v části [monitorování řešení potíží s Azure pro SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Kontrola připravenosti pro Azure Enhanced Monitoring rozšíření pro SAP
Tato kontrola zajišťuje, že všechny metriky výkonu, které se zobrazí uvnitř aplikace SAP poskytuje základní infrastrukturu Azure monitorování.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Spusťte kontrolu připravenosti na virtuálním počítači s Windows

1.  Přihlaste se k virtuálnímu počítači Azure (pomocí účtu správce není nutné).
1.  Otevřete okno příkazového řádku.
1.  Na příkazovém řádku změňte adresář na instalační složku rozšířené monitorování rozšíření Azure pro SAP: C:\\balíčky\\moduly plug-in\\ Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;verze >\\vyřadit

  *Verze* v cestě k rozšíření monitorování se může lišit. Pokud se zobrazí složek pro více verzí rozšíření monitorování v instalační složce Nástroje, zkontrolujte konfiguraci služby AzureEnhancedMonitoring Windows a pak přejděte do složky, které jsou označeny jako *cesta ke spustitelnému souboru* .

  ![Vlastnosti služby spuštěné rozšířené monitorování rozšíření Azure pro SAP][deployment-guide-figure-1000]

1.  Na příkazovém řádku spusťte **azperflib.exe** bez parametrů.

  > [!NOTE]
  > Azperflib.exe spouští ve smyčce a aktualizuje počitadla shromážděných každých 60 sekund. Pro ukončení smyčky, zavřete okno příkazového řádku.
  >
  >

Pokud Azure Enhanced Monitoring rozšíření není nainstalované nebo AzureEnhancedMonitoring služba není spuštěná, rozšíření nebyla nakonfigurována správně. Podrobné informace o tom, jak nasadit rozšíření najdete v tématu [Poradce při potížích Azure monitorování infrastruktury SAP][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe je komponenta, která nelze použít pro vlastní účely. Je komponenta, která poskytuje data související s virtuální počítač SAP hostitele agenta pro monitorování Azure.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Zkontrolujte výstup azperflib.exe
Azperflib.exe výstup ukazuje, že vyplní všechny čítače výkonu Azure pro SAP. V dolní části Seznam shromážděných čítačů výkonu summary a stavu ukazatele zobrazit stav monitorování Azure.

![Výstup Kontrola stavu pomocí provádí azperflib.exe, což znamená, že neexistují žádné problémy][deployment-guide-figure-1100]
<a name="figure-11"></a>

Zkontrolujte výsledek vrácený pro **čítače celkem** výstup, který se použije v hlášení jako prázdný a pro **stav**, jak je znázorněno na předchozím obrázku.

Výsledné hodnoty interpretujte takto:

| Azperflib.exe výsledné hodnoty | Monitorování stavu Azure |
| --- | --- |
| **Volání rozhraní API – není k dispozici** | Čítače, které nejsou k dispozici může být buď není pro danou konfiguraci virtuálního počítače nebo chyby. Zobrazit **stav**. |
| **Celkem čítače – prázdný** |Následující dva čítače úložiště Azure může být prázdný: <ul><li>Op latence čtení úložiště serveru MS</li><li>Úložiště Op latence čtení E2E MS</li></ul>Další čítače musí mít hodnoty. |
| **Stav** |Vrátit pouze OK if ukazuje stav **OK**. |
| **Diagnostika** |Podrobné informace o stavu. |

Pokud **stav** hodnota není **OK**, postupujte podle pokynů v [Kontrola stavu pro konfiguraci Azure monitorování infrastruktury] [ deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Spustit kontrolu připravenosti na virtuální počítač s Linuxem

1.  Připojení k virtuálnímu počítači Azure pomocí protokolu SSH.

1.  Zkontrolujte výstup Azure Enhanced Monitoring rozšíření.

  a.  Spusťte `more /var/lib/AzureEnhancedMonitor/PerfCounters`.

   **Očekávaný výsledek**: vrátí seznam čítačů výkonu. Soubor by neměl být prázdný.

 b. Spusťte `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`.

   **Očekávaný výsledek**: vrátí jeden řádek, kde je chyba **žádný**, například **3; konfigurace; Chyba; 0; 0; NONE; 0; 1456416792; tst servercs;**

  c. Spusťte `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`.

    **Očekávaný výsledek**: vrací jako prázdný nebo neexistuje.

Pokud se předchozí kontrola neproběhne úspěšně, spusťte tyto další kontroly:

1.  Ujistěte se, že je waagent nainstalován a povolen.

  a.  Spusťte `sudo ls -al /var/lib/waagent/`.

      **Očekávaný výsledek**: uvádí obsah waagent adresáře.

  b.  Spusťte `ps -ax | grep waagent`.

   **Očekávaný výsledek**: zobrazí podobná jednu položku: `python /usr/sbin/waagent -daemon`

1.   Ujistěte se, že Azure Enhanced Monitoring rozšíření je nainstalovaná a spuštěná.

  a.  Spusťte `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`.

    **Očekávaný výsledek**: uvádí obsah adresáře rozšíření rozšířené monitorování Azure.

  b. Spusťte `ps -ax | grep AzureEnhanced`.

     **Očekávaný výsledek**: zobrazí podobná jednu položku: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Nainstalujte agenta hostitele SAP, jak je popsáno v Poznámka SAP [1031096]a zkontrolujte výstup `saposcol`.

  a.  Spusťte `/usr/sap/hostctrl/exe/saposcol -d`.

  b.  Spusťte `dump ccm`.

  c.  Zkontrolujte, zda **Virtualization_Configuration\Enhanced monitorování přístupu** metrika **true**.

Pokud už máte nainstalovaný aplikační server SAP NetWeaver ABAP, otevřete transakce ST06 sekce a zkontrolujte, zda je povoleno rozšířené monitorování.

Pokud některý z těchto kontrol selhání a podrobné informace o tom, jak znovu nasadit rozšíření, naleznete v tématu [Poradce při potížích Azure monitorování infrastruktury SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Kontroly stavu pro konfiguraci Azure monitorování infrastruktury
Když některé z monitorování data se doručí správně, je určeno test popsaný v [kontroly připravenosti pro Azure Enhanced Monitoring for SAP][deployment-guide-5.1], spusťte `Test-AzureRmVMAEMExtension` rutiny ke kontrole, jestli Monitorování infrastruktury a rozšíření monitorování pro SAP Azure, které jsou nakonfigurované správně.

1.  Ujistěte se, že jste nainstalovali nejnovější verzi rutin Azure Powershellu, jak je popsáno v [rutin nasazení prostředí Azure PowerShell][deployment-guide-4.1].
1.  Spusťte následující rutinu Azure PowerShellu. Seznam dostupných prostředí, spusťte rutinu `Get-AzureRmEnvironment`. Chcete-li použít globální Azure, vyberte **AzureCloud** prostředí. Azure v Číně, vyberte **AzureChinaCloud**.
  ```powershell
  $env = Get-AzureRmEnvironment -Name <name of the environment>
  Connect-AzureRmAccount -Environment $env
  Set-AzureRmContext -SubscriptionName <subscription name>
  Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
  ```

1.  Zadejte data vašeho účtu a identifikaci virtuálních počítačů Azure.

  ![Vstupní stránky specifické pro SAP Azure rutiny Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. Skript testy konfigurace virtuálního počítače, které vyberete.

  ![Výstup úspěšné testovací infrastruktury pro monitorování Azure pro SAP][deployment-guide-figure-1300]

Zajistěte, aby byl každý výsledek kontroly stavu **OK**. Pokud se nezobrazují některé kontroly **OK**, spusťte rutinu aktualizace, jak je popsáno v [konfigurovat rozšířené monitorování rozšíření Azure pro SAP][deployment-guide-4.5]. Počkejte 15 minut a opakujte kontroly podle [kontroly připravenosti pro Azure Enhanced Monitoring for SAP] [ deployment-guide-5.1] a [Kontrola stavu pro monitorování infrastruktury konfigurace Azure] [deployment-guide-5.2]. Pokud kontroly stále indikovat problém s některé nebo všechny čítače, přečtěte si téma [Poradce při potížích Azure monitorování infrastruktury SAP][deployment-guide-5.3].

> [!Note]
> Upozornění v případech, kdy používat spravované disky Azure úrovně Standard může docházet. Upozornění se zobrazí místo testy, vrácení "OK". To je normální a určené v případě typu disku. Viz také najdete [řešení potíží s Azure monitorování infrastruktury pro SAP][deployment-guide-5.3]
> 

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Řešení potíží s Azure monitorování infrastruktury pro SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Čítače výkonu Azure nezobrazí vůbec
Služba Windows AzureEnhancedMonitoring shromažďuje metriky výkonu v Azure. Pokud služba nebyla správně nainstalována, nebo pokud není spuštěná ve virtuálním počítači, můžete být shromažďovány žádné metriky výkonu.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Instalační adresář Azure Enhanced Monitoring rozšíření je prázdná

###### <a name="issue"></a>Problém
V instalačním adresáři C:\\balíčky\\moduly plug-in\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;verze >\\e-maily je prázdný.

###### <a name="solution"></a>Řešení
Rozšíření není nainstalována. Určení, zda se jedná problém s proxy (jak je popsáno dříve). Vám může být nutné restartovat počítač nebo znovu spustit `Set-AzureRmVMAEMExtension` konfigurační skript.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Služba pro Azure Enhanced Monitoring neexistuje

###### <a name="issue"></a>Problém
Služba Windows AzureEnhancedMonitoring neexistuje.

Výstup Azperflib.exe vyvolá chybu:

![Provádění azperflib.exe označuje, zda není spuštěna služba Azure Enhanced Monitoring rozšíření pro SAP][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Řešení
Pokud služba neexistuje, rozšířené monitorování rozšíření Azure pro SAP nebyl nainstalován správně. Znovu nasadit rozšíření pomocí kroků popsaných vašemu scénáři nasazení v [scénáře nasazení virtuálních počítačů pro SAP v Azure][deployment-guide-3].

Poté, co nasadíte rozšíření po jedné hodině, znovu zkontrolujte, zda čítače výkonu Azure jsou k dispozici ve virtuálním počítači Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Služba pro Azure Enhanced Monitoring existuje, ale nepovede spustit

###### <a name="issue"></a>Problém
Služba Windows AzureEnhancedMonitoring existuje a je povoleno, ale nepodaří spustit. Další informace najdete v protokolu událostí aplikace.

###### <a name="solution"></a>Řešení
Konfigurace je nesprávná. Restartujte rozšíření monitorování pro virtuální počítač, jak je popsáno v [konfigurovat rozšířené monitorování rozšíření Azure pro SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Chybí některé čítače výkonu Azure
Služba Windows AzureEnhancedMonitoring shromažďuje metriky výkonu v Azure. Služba načte data z různých zdrojů. Některé konfigurační data se shromažďují místně a některé metriky výkonu se načítají z Azure Diagnostics. Vaše přihlášení na úrovni předplatného úložiště jsou použity čítače úložiště.

Pokud tento poradce potíže s použitím Poznámka SAP [1999351] nebude tento problém vyřešit, spusťte znovu `Set-AzureRmVMAEMExtension` konfigurační skript. Budete muset počkejte hodinu, protože storage analytics nebo diagnostiky čítačů nemusí vytvořit, ihned po jsou povoleny. Pokud se problém nevyřeší, otevřete zprávu SAP Zákaznická podpora na komponentu BC OP NT AZR pro Windows nebo BC-OP – LNX-AZR pro virtuální počítač s Linuxem.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Čítače výkonu Azure nezobrazí vůbec
Metriky výkonu v Azure byly shromážděny sadou démon. Pokud proces démon neběží, je možné shromažďovat žádná metrik výkonu.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Instalační adresář rozšíření Azure Enhanced Monitoring je prázdný

###### <a name="issue"></a>Problém
Adresář \\var\\lib\\waagent\\ nemá podadresář pro rozšíření Azure Enhanced Monitoring.

###### <a name="solution"></a>Řešení
Rozšíření není nainstalována. Určení, zda se jedná problém s proxy (jak je popsáno dříve). Možná budete muset restartovat počítač a/nebo znovu spustit `Set-AzureRmVMAEMExtension` konfigurační skript.

##### <a name="the-execution-of-set-azurermvmaemextension-and-test-azurermvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Provedení příkazu Set-AzureRmVMAEMExtension a Test-AzureRmVMAEMExtension zobrazit varovné zprávy s oznámením, že se nepodporují Standard Managed Disks

###### <a name="issue"></a>Problém
Když jsou uvedeny provádění Set-AzureRmVMAEMExtension a Test-AzureRmVMAEMExtension zpráv, jako jsou tyto:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Provádění azperfli.exe, jak je popsáno výše můžete získat výsledek, který je určující stav není v pořádku. 

###### <a name="solution"></a>Řešení
Zprávy jsou způsobeny skutečnost, že Standard Managed Disks se poskytuje rozhraní API používaná v rozšíření monitorování ke kontrole statistik standardní účty úložiště Azure. To není problém z hlediska. Důvod Úvod do monitorování pro účty úložiště úrovně Standard disku byla omezování vstupně-výstupních operací, které často došlo k chybě. Spravované disky se vyhnete takové omezení šířky pásma tím, že omezíte počet disků v účtu úložiště. Proto nemá daný typ dat monitorování není důležité.


#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Chybí některé čítače výkonu Azure
Metriky výkonu v Azure byly shromážděny sadou démona, která načte data z různých zdrojů. Některé konfigurační data se shromažďují místně a některé metriky výkonu se načítají z Azure Diagnostics. Čítače úložiště pocházejí z protokolů ve vašem předplatném úložiště.

Nejúplnější a nejaktuálnější seznam známých problémů, viz poznámka SAP [1999351], který obsahuje další informace o odstraňování potíží pro rozšířené monitorování Azure pro SAP.

Pokud tento poradce potíže s použitím Poznámka SAP [1999351] neobsahuje problém vyřešit, spusťte znovu `Set-AzureRmVMAEMExtension` konfigurační skript, jak je popsáno v [konfigurovat rozšířené monitorování rozšíření Azure pro SAP] [deployment-guide-4.5]. Budete muset počkat na hodinu, protože storage analytics nebo diagnostiky čítačů nemusí být vytvářeny ihned poté, co jsou povoleny. Pokud se problém nevyřeší, otevřete zprávu SAP Zákaznická podpora na komponentu BC OP NT AZR pro Windows nebo BC-OP – LNX-AZR pro virtuální počítač s Linuxem.
