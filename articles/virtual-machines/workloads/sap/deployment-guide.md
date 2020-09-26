---
title: Nasazení Azure Virtual Machines pro SAP NetWeaver | Microsoft Docs
description: Naučte se, jak nasadit software SAP na virtuální počítače se systémem Linux v Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: 4494af89ac35a391f7dc6097b14d135a911d99dd
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91359609"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Nasazení Azure Virtual Machines pro SAP NetWeaver

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
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Nasazení Azure Virtual Machines DBMS pro SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Ukládání do mezipaměti pro virtuální počítače a virtuální pevné disky)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (POLE RAID softwaru)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktura nasazení RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Vysoká dostupnost a zotavení po havárii s virtuálními počítači Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 a novější)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (CU3 a starší verze SQL Server 2012 SP1)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Použití SQL Server obrázku z Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Obecné SQL Server pro SAP v Azure – souhrn)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifické pro SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Konfigurace úložiště)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Zálohování a obnovení)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Požadavky na výkon pro zálohování a obnovení)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Jiná)
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

[deployment-guide]:deployment-guide.md (Nasazení Azure Virtual Machines pro SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Prostředky SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Nasazení virtuálního počítače pomocí vlastní image)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scénář 1: nasazení virtuálního počítače z Azure Marketplace pro SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scénář 2: nasazení virtuálního počítače s vlastní imagí pro SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scénář 3: přesun virtuálního počítače z místního prostředí pomocí nezobecněného virtuálního pevného disku Azure s SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scénáře nasazení virtuálních počítačů pro SAP v Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Nasazení rutin Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Stažení a import rutin prostředí PowerShell relevantních pro SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Připojení virtuálního počítače k místní doméně – jenom Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Stažení, instalace a povolení agenta virtuálního počítače Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Rozhraní příkazového řádku Azure)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Konfigurace rozšíření Azure pro SAP)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Konfigurace nového rozšíření Azure pro SAP pomocí Azure PowerShell)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Konfigurace nového rozšíření Azure pro SAP pomocí Azure CLI)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Kontroly připravenosti pro rozšíření Azure pro SAP)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (Kontroly připravenosti pro nové rozšíření Azure pro SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Kontrolu stavu pro rozšíření Azure pro konfiguraci SAP)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (Kontrolu stavu pro nové rozšíření Azure pro konfiguraci SAP)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Řešení potíží s rozšířením Azure pro SAP)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (Řešení potíží s novým rozšířením Azure pro SAP)
[deployment-guide-contact-support]:deployment-guide.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Řešení potíží s rozšířením Azure pro SAP – kontaktní podpora)
[deployment-guide-run-the-script]:deployment-guide.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Řešení potíží s rozšířením Azure pro SAP – spuštění instalačního skriptu)
[deployment-guide-redeploy-after-sysprep]:deployment-guide.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Řešení potíží s rozšířením Azure pro SAP – opětovné nasazení po dokončení programu Sysprep)
[deployment-guide-fix-internet-connection]:deployment-guide.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 ( Řešení potíží s rozšířením Azure pro SAP – Oprava připojení k Internetu)


[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Nakonfigurovat rozšíření virtuálního počítače)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Kontroly a odstraňování potíží)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Plánování a implementace Azure Virtual Machines pro SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Prostředky)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Vysoká dostupnost a zotavení po havárii pro SAP NetWeaver běžící na Azure Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Vysoká dostupnost pro aplikační servery SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Použití autostart pro instance SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Nasazení jenom pro cloudové virtuální počítače v Azure bez závislostí v místní síti zákazníka)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Více míst – nasazení jednoho nebo více virtuálních počítačů SAP v Azure je plně integrované s místní sítí.)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Oblasti Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domény selhání)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Upgradovat domény)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Skupiny dostupnosti Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Koncept Microsoft Azure virtuálních počítačů)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Přesun virtuálního počítače z místního prostředí do Azure s nezobecněným diskem)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Nasazení virtuálního počítače s použitím obrázku specifického pro zákazníka)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Příprava na přesun virtuálního počítače z místního prostředí do Azure pomocí nezobecněného disku)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Příprava pro nasazení virtuálního počítače s využitím konkrétního zákaznického obrázku pro SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Příprava virtuálních počítačů pomocí SAP pro Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Rozdíl mezi diskem Azure a imagí Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Nahrání virtuálního pevného disku z místního prostředí do Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopírování disků mezi Azure Storage účty)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struktura virtuálních počítačů/VHD pro nasazení SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Nastavení automatického připojení pro připojené disky)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Řešení Azure Monitoring pro SAP)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure sítě)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Úložiště: Microsoft Azure Storage a datové disky)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
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
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Nasazení a správa virtuálních počítačů pomocí šablon Azure Resource Manager a Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Správa virtuálních počítačů pomocí Azure Resource Manager a PowerShellu)
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
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
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
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines je řešení pro organizace, které potřebují výpočetní prostředky a prostředky úložiště, a to za minimální dobu a bez zdlouhavých cyklů zásobování. K nasazení klasických aplikací, jako jsou aplikace založené na SAP NetWeaver, můžete v Azure použít Azure Virtual Machines. Rozšíří se spolehlivost a dostupnost aplikace bez dalších místních prostředků. Azure Virtual Machines podporuje připojení mezi různými místy, takže Azure Virtual Machines můžete integrovat do místních domén organizace, privátních cloudů a systému SAP na šířku.

V tomto článku jsme pokryli postup nasazení aplikací SAP na virtuálních počítačích v Azure, včetně alternativních možností nasazení a řešení potíží. Tento článek sestaví na informacích v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver][planning-guide]. Také doplňuje dokumentaci k instalaci SAP a poznámky SAP, které jsou primárními prostředky pro instalaci a nasazení softwaru SAP.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Nastavení virtuálního počítače Azure pro nasazení softwaru SAP zahrnuje několik kroků a prostředků. Než začnete, ujistěte se, že splňujete požadavky pro instalaci softwaru SAP na virtuální počítače v Azure.

### <a name="local-computer"></a>Místní počítač

Ke správě virtuálních počítačů se systémem Windows nebo Linux můžete použít skript prostředí PowerShell a Azure Portal. Pro oba nástroje potřebujete místní počítač se systémem Windows 7 nebo novější verzí systému Windows. Pokud chcete spravovat jenom virtuální počítače se systémem Linux a chcete pro tento úkol použít počítač se systémem Linux, můžete použít rozhraní příkazového řádku Azure CLI.

### <a name="internet-connection"></a>Připojení k Internetu

Chcete-li stáhnout a spustit nástroje a skripty, které jsou požadovány pro nasazení softwaru SAP, musíte být připojeni k Internetu. Virtuální počítač Azure s rozšířením Azure pro SAP taky potřebuje přístup k Internetu. Pokud je virtuální počítač Azure součástí virtuální sítě Azure nebo místní domény, ujistěte se, že jsou nastavená příslušná nastavení proxy serveru, jak je popsáno v tématu [konfigurace proxy serveru][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Předplatné Microsoft Azure

Potřebujete aktivní účet Azure.

### <a name="topology-and-networking"></a>Topologie a sítě

Je potřeba definovat topologii a architekturu nasazení SAP v Azure:

* Účty služby Azure Storage, které se mají použít
* Virtuální síť, ve které chcete nasadit systém SAP
* Skupina prostředků, do které chcete nasadit systém SAP
* Oblast Azure, ve které chcete nasadit systém SAP
* Konfigurace SAP (dvě vrstva nebo tři vrstvy)
* Velikosti virtuálních počítačů a počet dalších datových disků, které se mají připojit k virtuálním počítačům
* Konfigurace systému SAP pro opravy a přenosové systémy (CTS)

Vytvořte a nakonfigurujte účty služby Azure Storage (Pokud je to potřeba) nebo virtuální sítě Azure, než zahájíte proces nasazení softwaru SAP. Informace o tom, jak vytvořit a nakonfigurovat tyto prostředky, najdete v tématu [plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Určení velikosti SAP

Pro určení velikosti SAP si můžete sdělit následující informace:

* Předpokládané úlohy SAP, například pomocí nástroje SAP Quick Sizer Tool a čísla SAP (Application Performance Standard) SAP
* Požadovaná spotřeba prostředků procesoru a paměti systému SAP
* Požadované vstupně-výstupní operace (v/v) za sekundu
* Požadovaná šířka pásma sítě při případné komunikaci mezi virtuálními počítači v Azure
* Požadovaná šířka pásma sítě mezi místními prostředky a systémem SAP nasazeným v Azure

### <a name="resource-groups"></a>Skupiny prostředků

V Azure Resource Manager můžete použít skupiny prostředků ke správě všech prostředků aplikace ve vašem předplatném Azure. Další informace najdete v článku [Přehled Azure Resource Manageru][resource-group-overview].

## <a name="resources"></a>Zdroje a prostředky

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Prostředky SAP

Při nastavování nasazení softwaru SAP budete potřebovat následující prostředky SAP:

* Poznámka [1928533]pro SAP obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553] uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Pro SAP Note [2178632] najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* V případě SAP Note [1409604] je požadovaná verze agenta hostitele SAP pro Windows v Azure.
* V případě SAP Poznámka [2191498] je požadovaná verze agenta hostitele SAP pro Linux v Azure.
* Poznámka SAP Poznámka [2243692] obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
* Poznámka SAP poznámky [1984787] obsahuje obecné informace o SUSE Linux Enterprise Server 12.
* Poznámka SAP poznámky [2002167] obsahuje obecné informace o Red Hat Enterprise Linux 7. x.
* Poznámka SAP poznámky [2069760] obsahuje obecné informace o Oracle Linux 7. x.
* Poznámka SAP Poznámka [1999351] obsahuje další informace o řešení potíží pro rozšíření Azure pro SAP.
* Poznámka SAP Poznámka [1597355] obsahuje obecné informace o výměně místa pro Linux.
* [Stránka SAP na Azure SCN](https://wiki.scn.sap.com/wiki/x/Pia7Gg) obsahuje zprávy a kolekci užitečných prostředků.
* [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* Rutiny prostředí PowerShell specifické pro SAP, které jsou součástí [Azure PowerShell][azure-ps].
* Příkazy rozhraní příkazového řádku Azure CLI specifické pro SAP, které jsou součástí [Azure CLI][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Prostředky Windows

Tyto články Microsoftu zahrnují nasazení SAP v Azure:

* [Plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP NetWeaver (Tento článek)][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scénáře nasazení pro software SAP na virtuálních počítačích Azure

Máte k dispozici několik možností pro nasazení virtuálních počítačů a přidružených disků v Azure. Je důležité porozumět rozdílům mezi možnostmi nasazení, protože při přípravě virtuálních počítačů na nasazení na základě zvoleného typu nasazení můžete provést různé kroky.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scénář 1: nasazení virtuálního počítače z Azure Marketplace pro SAP

K nasazení virtuálního počítače můžete použít Image od Microsoftu nebo třetí strany v Azure Marketplace. Marketplace nabízí několik standardních imagí operačních systémů Windows Server a různých distribucí systému Linux. Také můžete nasadit bitovou kopii, která zahrnuje SKU systému správy databáze (DBMS), například Microsoft SQL Server. Další informace o používání imagí s SKU správy SKU najdete v tématu [nasazení Azure Virtual Machines DBMS pro SAP NetWeaver][dbms-guide].

Následující vývojový diagram znázorňuje posloupnost kroků specifických pro SAP pro nasazení virtuálního počítače z Azure Marketplace:

![Vývojový diagram nasazení virtuálních počítačů pro systémy SAP pomocí image virtuálního počítače z Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače pomocí Azure Portal

Nejjednodušší způsob, jak vytvořit nový virtuální počítač s imagí z Azure Marketplace, je použití Azure Portal.

1.  Přejděte na <https://portal.azure.com/#create/hub>.  Nebo v nabídce Azure Portal vyberte **+ Nový**.
1.  Vyberte **COMPUTE**a potom vyberte typ operačního systému, který chcete nasadit. Například Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) nebo Oracle Linux 7,2. Výchozí zobrazení seznamu nezobrazuje všechny podporované operační systémy. Pro úplný seznam vyberte **Zobrazit vše** . Další informace o podporovaných operačních systémech pro nasazení softwaru SAP najdete v tématu SAP Note [1928533].
1.  Na další stránce si přečtěte podmínky a ujednání.
1.  V poli **Vyberte model nasazení** vyberte možnost **Správce prostředků**.
1.  Vyberte **Vytvořit**.

Průvodce vás provede nastavením požadovaných parametrů k vytvoření virtuálního počítače, kromě všech požadovaných prostředků, jako jsou síťová rozhraní a účty úložiště. Některé z těchto parametrů jsou:

1. **Základy**:
   * **Name**(název): název prostředku (název virtuálního počítače).
   * **Typ disku virtuálního počítače**: Vyberte typ disku disku s operačním systémem. Pokud chcete pro datové disky použít Premium Storage, doporučujeme použít také Premium Storage pro disk s operačním systémem.
   * **Uživatelské jméno a heslo** nebo **veřejný klíč SSH**: zadejte uživatelské jméno a heslo uživatele, který se vytvoří během zřizování. Pro virtuální počítač se systémem Linux můžete zadat klíč protokolu SSH (Public Secure Shell), který používáte k přihlášení k počítači.
   * **Předplatné**: vyberte předplatné, které chcete použít k zřízení nového virtuálního počítače.
   * **Skupina prostředků**: název skupiny prostředků pro virtuální počítač. Můžete zadat buď název nové skupiny prostředků, nebo název skupiny prostředků, která již existuje.
   * **Umístění**: kam se má nový virtuální počítač nasadit. Pokud chcete virtuální počítač připojit k místní síti, ujistěte se, že jste vybrali umístění virtuální sítě, která připojuje Azure k vaší místní síti. Další informace najdete v tématu [Microsoft Azure sítě][planning-guide-microsoft-azure-networking] v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver][planning-guide].
1. **Velikost**:

     Seznam podporovaných typů virtuálních počítačů najdete v tématu SAP Note [1928533]. Pokud chcete používat Azure Premium Storage, ujistěte se, že jste vybrali správný typ virtuálního počítače. Nepodporují Premium Storage všechny typy virtuálních počítačů. Další informace najdete v tématu [úložiště: Microsoft Azure Storage a datové disky][planning-guide-storage-microsoft-azure-storage-and-data-disks] a [Azure Storage pro úlohy sap](./planning-guide-storage.md) v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver][planning-guide].

1. **Nastavení**:
   * **Storage**
     * **Typ disku**: Vyberte typ disku disku s operačním systémem. Pokud chcete pro datové disky použít Premium Storage, doporučujeme použít také Premium Storage pro disk s operačním systémem.
     * **Použití spravovaných disků**: pokud chcete Managed disks použít, vyberte Ano. Další informace o Managed Disks naleznete v části kapitola [Managed disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) v příručce pro plánování.
     * **Účet úložiště**: Vyberte existující účet úložiště nebo vytvořte nový. Ne všechny typy úložišť fungují pro spouštění aplikací SAP. Další informace o typech úložiště najdete v tématu [Struktura úložiště virtuálního počítače pro nasazení RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Síť**
     * **Virtuální síť** a **podsíť**: Pokud chcete virtuální počítač integrovat s vaším intranetem, vyberte virtuální síť, která je připojená k vaší místní síti.
     * **Veřejná IP adresa**: Vyberte veřejnou IP adresu, kterou chcete použít, nebo zadejte parametry pro vytvoření nové veřejné IP adresy. Veřejnou IP adresu můžete použít pro přístup k virtuálnímu počítači přes Internet. Ujistěte se, že jste taky vytvořili skupinu zabezpečení sítě, která vám usnadní zabezpečení přístupu k virtuálnímu počítači.
     * **Skupina zabezpečení sítě**: Další informace najdete v tématu [řízení toku síťového provozu pomocí skupin zabezpečení sítě][virtual-networks-nsg].
   * **Rozšíření**: rozšíření virtuálních počítačů můžete nainstalovat přidáním do nasazení. V tomto kroku nemusíte přidávat rozšíření. Rozšíření požadovaná pro podporu SAP se instalují později. Viz kapitola [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5] v této příručce.
   * **Vysoká dostupnost**: vyberte skupinu dostupnosti, nebo zadejte parametry pro vytvoření nové skupiny dostupnosti. Další informace najdete v tématu [skupiny dostupnosti Azure][planning-guide-3.2.3].
   * **Monitorování**
     * **Diagnostika spouštění**: můžete vybrat možnost **Zakázat** pro diagnostiku spouštění.
     * **Diagnostika hostovaného operačního systému**: pro diagnostiku monitorování můžete vybrat **Zakázat** .

1. **Shrnutí**:

   Zkontrolujte výběr a pak vyberte **OK**.

Váš virtuální počítač se nasadí do skupiny prostředků, kterou jste vybrali.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Vytvoření virtuálního počítače pomocí šablony

Virtuální počítač můžete vytvořit pomocí jedné ze šablon SAP publikovaných v [úložišti GitHub Azure-Rychlé-Templates][azure-quickstart-templates-github]. Můžete také ručně vytvořit virtuální počítač pomocí [Azure Portal][virtual-machines-windows-tutorial], [PowerShellu][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]nebo rozhraní příkazového [řádku Azure CLI][virtual-machines-linux-tutorial].

* [**Šablona se dvěma vrstvami (jenom jedna virtuální počítač)** (SAP-2-vrstva-Marketplace-image)][sap-templates-2-tier-marketplace-image]

  K vytvoření dvou vrstev systému pomocí pouze jednoho virtuálního počítače použijte tuto šablonu.
* [**Konfigurace se dvěma vrstvami (jenom jeden virtuální počítač) – Managed disks** (SAP-2-vrstva-Marketplace-image-MD)][sap-templates-2-tier-marketplace-image-md]

  Chcete-li vytvořit dvoufázový systém s použitím pouze jednoho virtuálního počítače a Managed Disks, použijte tuto šablonu.
* [**Šablona pro konfiguraci tří vrstev (více virtuálních počítačů)** (SAP-3-vrstva-Marketplace-image)][sap-templates-3-tier-marketplace-image]

  Chcete-li vytvořit tři systémy pomocí více virtuálních počítačů, použijte tuto šablonu.
* [**Šablona s konfigurací na třech úrovních (více virtuálních počítačů) – Managed disks** (SAP-3-vrstva-Marketplace-image-MD)][sap-templates-3-tier-marketplace-image-md]

  Chcete-li vytvořit tři systémy pomocí více virtuálních počítačů a Managed Disks, použijte tuto šablonu.

V Azure Portal zadejte pro šablonu následující parametry:

1. **Základy**:
   * **Předplatné**: předplatné, které se má použít k nasazení šablony.
   * **Skupina prostředků**: Skupina prostředků, která se má použít k nasazení šablony. Můžete vytvořit novou skupinu prostředků, nebo můžete vybrat existující skupinu prostředků v rámci předplatného.
   * **Umístění**: kam se má šablona nasadit. Pokud jste vybrali existující skupinu prostředků, použije se umístění této skupiny prostředků.

1. **Nastavení**:
   * **ID systému SAP**: ID systému SAP (SID).
   * **Typ operačního**systému: operační systém, který chcete nasadit, například Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2), nebo Oracle Linux 7,2.

     V zobrazení seznamu se nezobrazuje všechny podporované operační systémy. Další informace o podporovaných operačních systémech pro nasazení softwaru SAP najdete v tématu SAP Note [1928533].
   * **Velikost systému SAP**: velikost systému SAP.

     Počet SAP, který nový systém poskytuje. Pokud si nejste jistí, kolik SAP systém vyžaduje, požádejte svého partnera technologie SAP nebo systémový integrátor.
   * **Dostupnost systému** (jenom šablona se třemi vrstvami): dostupnost systému.

     Vyberte **ha** pro konfiguraci, která je vhodná pro instalaci s vysokou dostupností. Vytvoří se dva databázové servery a dva servery pro ABAP SAP Central Services (ASCS).
   * **Typ úložiště** (jenom šablona se dvěma vrstvami): typ úložiště, který se má použít.

     U větších systémů důrazně doporučujeme použít Azure Premium Storage. Další informace o typech úložiště najdete v těchto zdrojích:
      * [Použití Azure SSD úrovně Premium Storage pro instanci SAP DBMS][2367194]
      * [Struktura úložiště virtuálního počítače pro nasazení RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
   * **Uživatelské jméno** a **heslo správce**: uživatelské jméno a heslo.
     Vytvoří se nový uživatel, který se přihlásí k virtuálnímu počítači.
   * **Nová nebo existující podsíť**: Určuje, jestli se vytvoří nová virtuální síť a podsíť, nebo se použije existující podsíť. Pokud už máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
   * **ID podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální sítě, kde máte definovanou podsíť, ke které je potřeba přiřadit virtuální počítač, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá takto: &lt; ID předplatného/subscriptions/>/ResourceGroups/název &lt; skupiny prostředků>/Providers/Microsoft.Network/virtualnetworks/název &lt; virtuální sítě>/subnets/ &lt; název podsítě>

1. Podmínky **a ujednání**:  
    Přečtěte si právní podmínky a přijměte je.

1. Vyberte **Koupit**.

Agent virtuálního počítače Azure se nasadí ve výchozím nastavení, když použijete image z Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

V závislosti na tom, jak je vaše místní síť nakonfigurovaná, možná budete muset nastavit proxy server na svém VIRTUÁLNÍm počítači. Pokud je váš virtuální počítač připojený k místní síti přes VPN nebo ExpressRoute, virtuální počítač nemusí mít přístup k Internetu a nebude moct stahovat požadovaná rozšíření virtuálních počítačů nebo shromažďovat informace o infrastruktuře Azure pro agenta hostitele SAP přes rozšíření SAP pro Azure. Další informace najdete v tématu [konfigurace proxy serveru][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Připojit k doméně (jenom Windows)

Pokud je vaše nasazení Azure připojené k místní službě Active Directory nebo instanci DNS prostřednictvím připojení VPN typu Site-to-site nebo ExpressRoute (Toto se nazývá *více* míst v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver][planning-guide]), předpokládá se, že se virtuální počítač připojuje k místní doméně. Další informace o požadavcích pro tuto úlohu najdete v tématu [připojení virtuálního počítače k místní doméně (jenom Windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Nakonfigurovat rozšíření virtuálního počítače

Abyste měli jistotu, že SAP podporuje vaše prostředí, nastavte rozšíření Azure pro SAP, jak je popsáno v tématu [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5]. V prostředcích uvedených v části [prostředky SAP][deployment-guide-2.2]ověřte požadavky pro SAP a požadované minimální verze systému SAP kernel a agenta hostitele SAP.

#### <a name="vm-extension-for-sap-check"></a>Rozšíření virtuálního počítače pro kontrolu SAP

Zkontrolujte, jestli rozšíření virtuálního počítače pro SAP funguje, jak je popsáno v tématu [kontroly a řešení potíží][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Kroky po nasazení

Po vytvoření virtuálního počítače a nasazení virtuálního počítače je potřeba na virtuální počítač nainstalovat požadované softwarové součásti. Z důvodu sekvence instalace nasazení nebo softwaru v tomto typu nasazení virtuálního počítače musí být nainstalovaný software již k dispozici v Azure, na jiném virtuálním počítači nebo jako disk, který lze připojit. Nebo zvažte použití mezimístního scénáře, ve kterém se přidělí připojení k místním prostředkům (sdílené složky instalace).

Po nasazení virtuálního počítače v Azure použijte stejné pokyny a nástroje k instalaci softwaru SAP na váš virtuální počítač, jako byste to měli v místním prostředí. Pro instalaci softwaru SAP na virtuální počítač Azure doporučujeme, abyste nahráli a uložili instalační média SAP na virtuálních pevných discích (VHD) Azure nebo Managed Disks nebo vytvoříte virtuální počítač Azure, který funguje jako souborový server, který má všechna požadovaná instalační média SAP.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scénář 2: nasazení virtuálního počítače s vlastní imagí pro SAP

Vzhledem k tomu, že různé verze operačního systému nebo DBMS mají jiné požadavky na opravu, obrázky, které najdete v Azure Marketplace nemusí vyhovovat vašim potřebám. Možná budete chtít vytvořit virtuální počítač pomocí vlastní image virtuálního počítače s operačním systémem nebo DBMS, kterou můžete nasadit znovu později.
Pomocí různých kroků můžete vytvořit soukromou bitovou kopii pro Linux, než je vytvoříte pro Windows.

---
> ![Logo Windows][Logo_Windows] Windows
>
> K přípravě image Windows, kterou můžete použít k nasazení více virtuálních počítačů, musí být nastavení systému Windows (například identifikátor SID Windows a název hostitele) abstraktní nebo zobecněná na místním virtuálním počítači. K tomu můžete použít [Nástroj Sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) .
>
> ![Logo Linux.][Logo_Linux] Linux
>
> K přípravě image pro Linux, kterou můžete použít k nasazení více virtuálních počítačů, je nutné, aby některá nastavení systému Linux byla na místním virtuálním počítači abstraktní nebo zobecněná. K tomu můžete použít `waagent -deprovision`  . Další informace najdete v tématu [zachycení virtuálního počítače se systémem Linux běžícího v Azure][virtual-machines-linux-capture-image] a v [uživatelské příručce agenta Azure Linux][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Můžete připravit a vytvořit vlastní image a pak ji použít k vytvoření několika nových virtuálních počítačů. To je popsané v tématu [plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver][planning-guide]. Pomocí Správce služby SAP software Provisioning nainstalujte nový systém SAP (obnovte zálohu databáze z disku připojeného k virtuálnímu počítači) nebo přímým obnovením zálohy databáze z Azure Storage, pokud ji DBMS podporuje. Další informace najdete v tématu [nasazení Azure Virtual Machines DBMS pro SAP NetWeaver][dbms-guide]. Pokud jste již nainstalovali systém SAP na místním VIRTUÁLNÍm počítači (obzvláště u dvou systémů), můžete nastavení systému SAP přizpůsobit po nasazení virtuálního počítače Azure pomocí postupu přejmenování systému podporovaného správcem SAP software Provisioning (SAP Note [1619720]). V opačném případě můžete nainstalovat software SAP po nasazení virtuálního počítače Azure.

Následující vývojový diagram znázorňuje posloupnost kroků specifických pro SAP pro nasazení virtuálního počítače z vlastní image:

![Vývojový diagram nasazení virtuálních počítačů pro systémy SAP pomocí image virtuálního počítače na soukromém webu Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače pomocí Azure Portal

Nejjednodušší způsob, jak vytvořit nový virtuální počítač z image spravovaného disku, je pomocí Azure Portal. Další informace o tom, jak vytvořit image pro správu disku, najdete [v tématu zachycení spravované image zobecněného virtuálního počítače v Azure](../../windows/capture-image-resource.md) .

1.  Přejděte na <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Nebo v nabídce Azure Portal vyberte možnost **obrázky**.
1.  Vyberte image spravovaného disku, kterou chcete nasadit, a klikněte na **vytvořit virtuální počítač** .

Průvodce vás provede nastavením požadovaných parametrů k vytvoření virtuálního počítače, kromě všech požadovaných prostředků, jako jsou síťová rozhraní a účty úložiště. Některé z těchto parametrů jsou:

1. **Základy**:
   * **Name**(název): název prostředku (název virtuálního počítače).
   * **Typ disku virtuálního počítače**: Vyberte typ disku disku s operačním systémem. Pokud chcete pro datové disky použít Premium Storage, doporučujeme použít také Premium Storage pro disk s operačním systémem.
   * **Uživatelské jméno a heslo** nebo **veřejný klíč SSH**: zadejte uživatelské jméno a heslo uživatele, který se vytvoří během zřizování. Pro virtuální počítač se systémem Linux můžete zadat klíč protokolu SSH (Public Secure Shell), který používáte k přihlášení k počítači.
   * **Předplatné**: vyberte předplatné, které chcete použít k zřízení nového virtuálního počítače.
   * **Skupina prostředků**: název skupiny prostředků pro virtuální počítač. Můžete zadat buď název nové skupiny prostředků, nebo název skupiny prostředků, která již existuje.
   * **Umístění**: kam se má nový virtuální počítač nasadit. Pokud chcete virtuální počítač připojit k místní síti, ujistěte se, že jste vybrali umístění virtuální sítě, která připojuje Azure k vaší místní síti. Další informace najdete v tématu [Microsoft Azure sítě][planning-guide-microsoft-azure-networking] v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver][planning-guide].
1. **Velikost**:

     Seznam podporovaných typů virtuálních počítačů najdete v tématu SAP Note [1928533]. Pokud chcete používat Azure Premium Storage, ujistěte se, že jste vybrali správný typ virtuálního počítače. Nepodporují Premium Storage všechny typy virtuálních počítačů. Další informace najdete v tématu [úložiště: Microsoft Azure Storage a datové disky][planning-guide-storage-microsoft-azure-storage-and-data-disks] a [Azure Storage pro úlohy sap](./planning-guide-storage.md) v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver][planning-guide].

1. **Nastavení**:
   * **Storage**
     * **Typ disku**: Vyberte typ disku disku s operačním systémem. Pokud chcete pro datové disky použít Premium Storage, doporučujeme použít také Premium Storage pro disk s operačním systémem.
     * **Použití spravovaných disků**: pokud chcete Managed disks použít, vyberte Ano. Další informace o Managed Disks naleznete v části kapitola [Managed disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) v příručce pro plánování.
   * **Síť**
     * **Virtuální síť** a **podsíť**: Pokud chcete virtuální počítač integrovat s vaším intranetem, vyberte virtuální síť, která je připojená k vaší místní síti.
     * **Veřejná IP adresa**: Vyberte veřejnou IP adresu, kterou chcete použít, nebo zadejte parametry pro vytvoření nové veřejné IP adresy. Veřejnou IP adresu můžete použít pro přístup k virtuálnímu počítači přes Internet. Ujistěte se, že jste taky vytvořili skupinu zabezpečení sítě, která vám usnadní zabezpečení přístupu k virtuálnímu počítači.
     * **Skupina zabezpečení sítě**: Další informace najdete v tématu [řízení toku síťového provozu pomocí skupin zabezpečení sítě][virtual-networks-nsg].
   * **Rozšíření**: rozšíření virtuálních počítačů můžete nainstalovat přidáním do nasazení. V tomto kroku nemusíte přidávat rozšíření. Rozšíření požadovaná pro podporu SAP se instalují později. Viz kapitola [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5] v této příručce.
   * **Vysoká dostupnost**: vyberte skupinu dostupnosti, nebo zadejte parametry pro vytvoření nové skupiny dostupnosti. Další informace najdete v tématu [skupiny dostupnosti Azure][planning-guide-3.2.3].
   * **Monitorování**
     * **Diagnostika spouštění**: můžete vybrat možnost **Zakázat** pro diagnostiku spouštění.
     * **Diagnostika hostovaného operačního systému**: pro diagnostiku monitorování můžete vybrat **Zakázat** .

1. **Shrnutí**:

   Zkontrolujte výběr a pak vyberte **OK**.

Váš virtuální počítač se nasadí do skupiny prostředků, kterou jste vybrali.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Vytvoření virtuálního počítače pomocí šablony

Pokud chcete vytvořit nasazení pomocí privátní image operačního systému z Azure Portal, použijte jednu z následujících šablon SAP. Tyto šablony jsou publikované v [úložišti GitHub Azure-Rychlé šablony][azure-quickstart-templates-github]. Můžete také ručně vytvořit virtuální počítač pomocí [prostředí PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Šablona se dvěma vrstvami (jenom jeden virtuální počítač)** (SAP-2-vrstva-User-image)][sap-templates-2-tier-user-image]

  K vytvoření dvou vrstev systému pomocí pouze jednoho virtuálního počítače použijte tuto šablonu.
* [**Konfigurace se dvěma vrstvami (jenom jeden virtuální počítač) – image spravovaná na disku** (SAP-2-vrstva-User-image-MD)][sap-templates-2-tier-user-image-md]

  K vytvoření dvou vrstev systému pomocí jenom jednoho virtuálního počítače a spravované image disku použijte tuto šablonu.
* [**Šablona pro konfiguraci na třech úrovních (více virtuálních počítačů)** (SAP-3-vrstva-User-image)][sap-templates-3-tier-user-image]

  Pokud chcete vytvořit trojrozměrnou aplikaci pomocí několika virtuálních počítačů nebo vlastní image operačního systému, použijte tuto šablonu.
* [**Bitová kopie disku spravovaná šablonou (více virtuálních počítačů)** (SAP-3-vrstva-User-image-MD)][sap-templates-3-tier-user-image-md]

  Pokud chcete vytvořit trojrozměrnou aplikaci pomocí několika virtuálních počítačů nebo vlastní image operačního systému a spravované image disku, použijte tuto šablonu.

V Azure Portal zadejte pro šablonu následující parametry:

1. **Základy**:
   * **Předplatné**: předplatné, které se má použít k nasazení šablony.
   * **Skupina prostředků**: Skupina prostředků, která se má použít k nasazení šablony. Můžete vytvořit novou skupinu prostředků nebo vybrat existující skupinu prostředků v rámci předplatného.
   * **Umístění**: kam se má šablona nasadit. Pokud jste vybrali existující skupinu prostředků, použije se umístění této skupiny prostředků.
1. **Nastavení**:
   * **ID systému SAP**: ID systému SAP.
   * **Typ operačního**systému: typ operačního systému, který chcete nasadit (Windows nebo Linux).
   * **Velikost systému SAP**: velikost systému SAP.

     Počet SAP, který nový systém poskytuje. Pokud si nejste jistí, kolik SAP systém vyžaduje, požádejte svého partnera technologie SAP nebo systémový integrátor.
   * **Dostupnost systému** (jenom šablona se třemi vrstvami): dostupnost systému.

     Vyberte **ha** pro konfiguraci, která je vhodná pro instalaci s vysokou dostupností. Vytvoří se dva databázové servery a dva servery pro ASCS.
   * **Typ úložiště** (jenom šablona se dvěma vrstvami): typ úložiště, který se má použít.

     U větších systémů důrazně doporučujeme použít Azure Premium Storage. Další informace o typech úložiště najdete v následujících zdrojích informací:
      * [Použití Azure SSD úrovně Premium Storage pro instanci SAP DBMS][2367194]
      * [Struktura úložiště virtuálního počítače pro nasazení RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
   * **Identifikátor URI image virtuálního pevného** disku (jenom šablona image nespravovaného disku): identifikátor URI PRIVÁTNÍHO virtuálního pevného disku, například https:// &lt;> Account. blob.Core.Windows.NET/VHDs/userimage.VHD.
   * **Uživatelský účet úložiště image** (jenom šablona nespravovaného disku): název účtu úložiště, ve kterém je uložená image PRIVÁTNÍHO operačního systému, třeba &lt; account> v &lt; účtu https://>. blob.Core.Windows.NET/VHDs/userimage.VHD.
   * **userImageId** (jenom šablona image spravovaného disku): ID image spravovaného disku, kterou chcete použít.
   * **Uživatelské jméno** a **heslo správce**: uživatelské jméno a heslo.

     Vytvoří se nový uživatel, který se přihlásí k virtuálnímu počítači.
   * **Nová nebo existující podsíť**: Určuje, jestli se vytvoří nová virtuální síť a podsíť, nebo se použije existující podsíť. Pokud už máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
   * **ID podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální sítě, kde máte definovanou podsíť, ke které je potřeba přiřadit virtuální počítač, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá takto: &lt; ID předplatného/subscriptions/>/ResourceGroups/název &lt; skupiny prostředků>/Providers/Microsoft.Network/virtualnetworks/název &lt; virtuální sítě>/subnets/ &lt; název podsítě>

1. Podmínky **a ujednání**:  
    Přečtěte si právní podmínky a přijměte je.

1. Vyberte **Koupit**.

#### <a name="install-the-vm-agent-linux-only"></a>Instalace agenta virtuálního počítače (jenom Linux)

Chcete-li použít šablony popsané v předchozí části, je nutné, aby byl agent pro Linux nainstalován v uživatelské imagi, jinak nebude nasazení úspěšné. Stáhněte a nainstalujte agenta virtuálního počítače do image uživatele, jak je popsáno v tématu [stažení, instalace a povolení agenta virtuálního počítače Azure][deployment-guide-4.4]. Pokud šablony nepoužíváte, můžete agenta virtuálního počítače nainstalovat taky později.

#### <a name="join-a-domain-windows-only"></a>Připojit k doméně (jenom Windows)

Pokud je vaše nasazení Azure připojené k místní službě Active Directory nebo instanci DNS prostřednictvím připojení VPN typu Site-to-site nebo Azure ExpressRoute (Tento postup se nazývá *více* míst v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver][planning-guide]), očekává se, že se virtuální počítač připojuje k místní doméně. Další informace o požadavcích tohoto kroku najdete v tématu [připojení virtuálního počítače k místní doméně (jenom Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

V závislosti na tom, jak je vaše místní síť nakonfigurovaná, možná budete muset nastavit proxy server na svém VIRTUÁLNÍm počítači. Pokud je váš virtuální počítač připojený k místní síti prostřednictvím sítě VPN nebo ExpressRoute, virtuální počítač nemusí mít přístup k Internetu a nebude moct stáhnout požadovaná rozšíření virtuálních počítačů nebo shromažďovat informace o infrastruktuře Azure pro agenta hostitele SAP přes rozšíření SAP pro Azure najdete v tématu [konfigurace proxy serveru][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurace rozšíření Azure VM pro SAP

Abyste měli jistotu, že SAP podporuje vaše prostředí, nastavte rozšíření Azure pro SAP, jak je popsáno v tématu [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5]. V prostředcích uvedených v části [prostředky SAP][deployment-guide-2.2]ověřte požadavky pro SAP a požadované minimální verze systému SAP kernel a agenta hostitele SAP.

#### <a name="sap-vm-extension-check"></a>Kontroler rozšíření SAP VM

Zkontrolujte, jestli rozšíření virtuálního počítače pro SAP funguje, jak je popsáno v tématu [kontroly a řešení potíží][deployment-guide-troubleshooting-chapter].


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scénář 3: přesunutí místního virtuálního počítače pomocí nezobecněného virtuálního pevného disku Azure s SAP

V tomto scénáři plánujete přesunout konkrétní systém SAP z místního prostředí do Azure. Můžete to udělat tak, že nahrajete virtuální pevný disk, který obsahuje operační systém, binární soubory SAP a nakonec binární soubory DBMS, a virtuální pevné disky s daty a soubory protokolů systému DBMS do Azure. Na rozdíl od scénáře popsaného ve [scénáři 2: nasazení virtuálního počítače s vlastní imagí pro SAP][deployment-guide-3.3]v tomto případě zachováte uživatelské účty název hostitele, SAP SID a SAP na virtuálním počítači Azure, protože byly nakonfigurovány v místním prostředí. Nemusíte generalizovat operační systém. Tento scénář se často používá u různých scénářů, ve kterých se v rámci prostředí SAP na pracovišti spouští místně a jeho součástí běží na Azure.

V tomto scénáři se agent virtuálního počítače během **nasazování automaticky nenainstaluje** . Vzhledem k tomu, že agent virtuálního počítače a rozšíření Azure pro SAP se vyžadují ke spuštění SAP NetWeaver v Azure, je potřeba stáhnout, nainstalovat a povolit obě komponenty ručně, i když vytvoříte virtuální počítač.

Další informace o agentovi virtuálního počítače Azure najdete v následujících zdrojích informací.

---
> ![Logo Windows][Logo_Windows] Windows
>
> [Přehled agenta virtuálního počítače Azure][virtual-machines-windows-agent-user-guide]
>
> ![Logo Linux.][Logo_Linux] Linux
>
> [Uživatelská příručka nástroje Azure Linux Agent][virtual-machines-linux-agent-user-guide]
>
>

---

Následující vývojový diagram znázorňuje posloupnost kroků přesunu místního virtuálního počítače pomocí negeneralizované jednotky VHD Azure:

![Vývojový diagram nasazení virtuálních počítačů pro systémy SAP pomocí disku virtuálního počítače][deployment-guide-figure-400]

Pokud je už disk nahraný a definovaný v Azure (viz [plánování a implementace Azure Virtual Machines pro SAP NetWeaver][planning-guide]), udělejte úlohy popsané v následujících částech.

#### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

K vytvoření nasazení pomocí privátního disku s operačním systémem prostřednictvím Azure Portal použijte šablonu SAP publikovanou v [úložišti GitHub Azure-Rychlé šablony][azure-quickstart-templates-github]. Můžete také ručně vytvořit virtuální počítač pomocí prostředí PowerShell.

* [**Šablona se dvěma vrstvami (jenom jeden virtuální počítač)** (SAP-2-vrstva-uživatel-disk)][sap-templates-2-tier-os-disk]

  K vytvoření dvou vrstev systému pomocí pouze jednoho virtuálního počítače použijte tuto šablonu.
* [**Konfigurace se dvěma vrstvami (jenom jeden virtuální počítač) – spravovaný disk** (SAP-2-vrstva-uživatel-disk-MD)][sap-templates-2-tier-os-disk-md]

  K vytvoření dvou vrstev systému pomocí pouze jednoho virtuálního počítače a spravovaného disku použijte tuto šablonu.

V Azure Portal zadejte pro šablonu následující parametry:

1. **Základy**:
   * **Předplatné**: předplatné, které se má použít k nasazení šablony.
   * **Skupina prostředků**: Skupina prostředků, která se má použít k nasazení šablony. Můžete vytvořit novou skupinu prostředků nebo vybrat existující skupinu prostředků v rámci předplatného.
   * **Umístění**: kam se má šablona nasadit. Pokud jste vybrali existující skupinu prostředků, použije se umístění této skupiny prostředků.
1. **Nastavení**:
   * **ID systému SAP**: ID systému SAP.
   * **Typ operačního**systému: typ operačního systému, který chcete nasadit (Windows nebo Linux).
   * **Velikost systému SAP**: velikost systému SAP.

     Počet SAP, který nový systém poskytuje. Pokud si nejste jistí, kolik SAP systém vyžaduje, požádejte svého partnera technologie SAP nebo systémový integrátor.
   * **Typ úložiště** (jenom šablona se dvěma vrstvami): typ úložiště, který se má použít.

     U větších systémů důrazně doporučujeme použít Azure Premium Storage. Další informace o typech úložiště najdete v následujících zdrojích informací:
      * [Použití Azure SSD úrovně Premium Storage pro instanci SAP DBMS][2367194]
      * [Struktura úložiště virtuálního počítače pro nasazení RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
   * **Identifikátor URI VHD disku s operačním systémem** (jenom šablona nespravovaného disku): identifikátor URI privátního disku s operačním systémem, například https://\ &lt; účet \>. blob.Core.Windows.NET/VHDs/OSDisk.VHD.
   * **ID spravovaného disku disku s operačním systémem** (jenom šablona spravovaného disku): ID disku operačního systému spravovaného disku,/Subscriptions/92d102f7-81a5-4DF7-9877-54987ba97dd9/resourceGroups/Group/Providers/Microsoft.COMPUTE/disks/Win
   * **Nová nebo existující podsíť**: Určuje, jestli se vytvoří nová virtuální síť a podsíť, nebo se použije existující podsíť. Pokud už máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
   * **ID podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální sítě, kde máte definovanou podsíť, ke které je potřeba přiřadit virtuální počítač, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá takto: &lt; ID předplatného/subscriptions/>/ResourceGroups/název &lt; skupiny prostředků>/Providers/Microsoft.Network/virtualnetworks/název &lt; virtuální sítě>/subnets/ &lt; název podsítě>

1. Podmínky **a ujednání**:  
    Přečtěte si právní podmínky a přijměte je.

1. Vyberte **Koupit**.

#### <a name="install-the-vm-agent"></a>Instalace agenta virtuálního počítače

Chcete-li použít šablony popsané v předchozí části, musí být agent virtuálního počítače nainstalován na disk s operačním systémem, jinak se nasazení nezdaří. Stáhněte a nainstalujte agenta virtuálního počítače do virtuálního počítače, jak je popsáno v tématu [stažení, instalace a povolení agenta virtuálního počítače Azure][deployment-guide-4.4].

Pokud nepoužíváte šablony popsané v předchozí části, můžete také nainstalovat agenta virtuálního počítače.

#### <a name="join-a-domain-windows-only"></a>Připojit k doméně (jenom Windows)

Pokud je vaše nasazení Azure připojené k místní službě Active Directory nebo instanci DNS prostřednictvím připojení VPN typu Site-to-site nebo ExpressRoute (Toto se nazývá *více* míst v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver][planning-guide]), předpokládá se, že se virtuální počítač připojuje k místní doméně. Další informace o požadavcích pro tuto úlohu najdete v tématu [připojení virtuálního počítače k místní doméně (jenom Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

V závislosti na tom, jak je vaše místní síť nakonfigurovaná, možná budete muset nastavit proxy server na svém VIRTUÁLNÍm počítači. Pokud je váš virtuální počítač připojený k místní síti prostřednictvím sítě VPN nebo ExpressRoute, virtuální počítač nemusí mít přístup k Internetu a nebude moct stáhnout požadovaná rozšíření virtuálních počítačů nebo shromažďovat informace o infrastruktuře Azure pro agenta hostitele SAP přes rozšíření SAP pro Azure najdete v tématu [konfigurace proxy serveru][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurace rozšíření Azure VM pro SAP

Abyste měli jistotu, že SAP podporuje vaše prostředí, nastavte rozšíření Azure pro SAP, jak je popsáno v tématu [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5]. V prostředcích uvedených v části [prostředky SAP][deployment-guide-2.2]ověřte požadavky pro SAP a požadované minimální verze systému SAP kernel a agenta hostitele SAP.

#### <a name="sap-vm-check"></a>Kontroly virtuálních počítačů SAP

Zkontrolujte, jestli rozšíření virtuálního počítače pro SAP funguje, jak je popsáno v tématu [kontroly a řešení potíží][deployment-guide-troubleshooting-chapter].

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Aktualizace konfigurace rozšíření Azure pro SAP

Aktualizujte konfiguraci rozšíření Azure pro SAP v některém z následujících scénářů:
* Společný tým Microsoftu/SAP rozšiřuje možnosti rozšíření virtuálního počítače a požaduje více nebo méně čítačů.
* Společnost Microsoft zavádí novou verzi základní infrastruktury Azure, která zajišťuje data a rozšíření Azure pro SAP musí být přizpůsobeno těmto změnám.
* K VIRTUÁLNÍmu počítači Azure připojíte další datové disky nebo odeberete datový disk. V tomto scénáři aktualizujte kolekci dat týkajících se úložišť. Změna konfigurace přidáním nebo odstraněním koncových bodů nebo přiřazením IP adres k virtuálnímu počítači nemá vliv na konfiguraci rozšíření.
* Velikost virtuálního počítače Azure změníte například z velikosti A5 na jinou velikost virtuálního počítače.
* Do virtuálního počítače Azure přidáte nová síťová rozhraní.

Pokud chcete aktualizovat nastavení, aktualizujte konfiguraci rozšíření Azure pro SAP podle kroků v části [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Podrobné úlohy pro nasazení softwaru SAP

V této části najdete podrobný postup pro konkrétní úkoly v procesu konfigurace a nasazení.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Nasazení rutin Azure PowerShell

Postupujte podle kroků popsaných v článku [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps) .

Pravidelně kontrolujte aktualizace rutin PowerShellu, které se obvykle aktualizují měsíčně. Postupujte podle kroků popsaných v [tomto](/powershell/azure/install-az-ps#update-the-azure-powershell-module) článku. Pokud není v SAP Note [1928533] nebo sap Note [2015553]uvedeno jinak, doporučujeme pracovat s nejnovějšími verzemi Azure PowerShell rutin.

Pokud chcete zjistit verzi rutin Azure PowerShell, které jsou nainstalované na vašem počítači, spusťte tento příkaz PowerShellu:

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Nasazení Azure CLI

Postupujte podle kroků popsaných v článku [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli) .

Pravidelně kontrolujte aktualizace rozhraní příkazového řádku Azure, které se obvykle aktualizují měsíčně.

Pokud chcete zjistit verzi rozhraní příkazového řádku Azure, která je v počítači nainstalovaná, spusťte tento příkaz:

```console
az --version
```

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Připojení virtuálního počítače k místní doméně (jenom Windows)

Pokud nasadíte virtuální počítače SAP do různých scénářů, kde jsou místní služba Active Directory a DNS v Azure rozšířené, očekává se, že se virtuální počítače připojují k místní doméně. Podrobný postup, jak se připojit k virtuálnímu počítači k místní doméně a další software nutný pro členství v místní doméně, se liší podle zákazníka. Aby bylo možné připojit virtuální počítač k místní doméně, je třeba nainstalovat další software, například antimalwarový software, a software pro zálohování nebo monitorování.

V tomto scénáři je potřeba zajistit, aby se při připojení virtuálního počítače k doméně ve vašem prostředí vynutilo i nastavení internetového proxy serveru, účet místního systému Windows (S-1-5-18) v hostovaném počítači hosta má stejné nastavení proxy serveru. Nejjednodušší možností je vynutit proxy server pomocí Zásady skupiny domény, která se vztahuje na systémy v doméně.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Stažení, instalace a povolení agenta virtuálního počítače Azure

Pro virtuální počítače, které jsou nasazené z image operačního systému, která není zobecněná (například Image, která nepochází z přípravy systému Windows nebo nástroje Sysprep, nástroj), musíte ručně stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure.

Pokud virtuální počítač nasadíte z Azure Marketplace, tento krok se nevyžaduje. Image z Azure Marketplace už mají agenta virtuálního počítače Azure.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Stáhněte si agenta virtuálního počítače Azure:
   1.  Stáhněte si [balíček Instalační služby agenta virtuálního počítače Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Uložte balíček MSI agenta virtuálního počítače místně do osobního počítače nebo serveru.
1. Nainstalujte agenta virtuálního počítače Azure:
   1.  Připojte se k nasazenému virtuálnímu počítači Azure pomocí protokol RDP (Remote Desktop Protocol) (RDP).
   1.  Na virtuálním počítači otevřete okno Průzkumníka Windows a vyberte cílový adresář pro soubor MSI agenta virtuálního počítače.
   1.  Přetáhněte soubor MSI instalační služby agenta virtuálního počítače Azure z místního počítače nebo serveru do cílového adresáře agenta virtuálního počítače na VIRTUÁLNÍm počítači.
   1.  Dvakrát klikněte na soubor MSI na virtuálním počítači.
1. U virtuálních počítačů, které jsou připojené k místním doménám, se ujistěte, že se pro účet místního systému Windows (S-1-5-18) ve virtuálním počítači použijí i nastavení, které je popsané v části [konfigurace proxy][deployment-guide-configure-proxy]serveru. Agent virtuálního počítače běží v tomto kontextu a musí být schopný se připojit k Azure.

K aktualizaci agenta virtuálního počítače Azure není nutná žádná interakce s uživatelem. Agent virtuálního počítače se automaticky aktualizuje a nevyžaduje restartování virtuálního počítače.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Pomocí následujících příkazů nainstalujte agenta virtuálního počítače pro Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) nebo Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Pokud je agent už nainstalovaný, aktualizujte agenta Azure Linux provedením kroků popsaných v tématu [aktualizace agenta Azure Linux na virtuálním počítači na nejnovější verzi z GitHubu][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurace proxy serveru

Postup pro konfiguraci proxy serveru ve Windows se liší od způsobu konfigurace proxy serveru v systému Linux.

#### <a name="windows"></a>Windows

Aby bylo možné získat přístup k Internetu, musí být nastavení proxy serveru správně nastaveno pro účet místního systému. Pokud vaše nastavení proxy serveru nejsou nastavená Zásady skupiny, můžete nakonfigurovat nastavení pro účet místního systému.

1. Přejděte na **Start**, zadejte **gpedit. msc**a pak vyberte **ENTER**.
1. Vyberte **Konfigurace počítače**  >  **šablony pro správu**  >  **součásti systému Windows**  >  **Internet Explorer**. Ujistěte se, že nastavení **nastavit proxy na počítač (nikoli na uživatele)** je zakázané nebo není nakonfigurované.
1. V **Ovládacích panelech**klikněte na možnosti **sítě a sdílení**na webu  >  **Internet Options**.
1. Na kartě **připojení** vyberte tlačítko **nastavení sítě LAN** .
1. Zrušte zaškrtnutí políčka **Automaticky zjišťovat nastavení**.
1. Zaškrtněte políčko **použít proxy server pro vaši síť LAN** a potom zadejte adresu a port proxy serveru.
1. Vyberte tlačítko **Upřesnit** .
1. Do pole **výjimky** zadejte IP adresu **168.63.129.16**. Vyberte **OK**.

#### <a name="linux"></a>Linux

Nakonfigurujte správný proxy server v konfiguračním souboru Microsoft Azureho agenta hosta, který se nachází na adrese a na úrovni služby ( \\ \\ waagent. conf).

Nastavte následující parametry:

1. **Hostitel proxy HTTP**. Nastavte ho například na **proxy. corp. Local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Port proxy serveru http**. Nastavte ho například na **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Restartujte agenta.

   ```console
   sudo service waagent restart
   ```

Nastavení proxy serveru v \\ etc \\ waagent. conf platí také pro požadovaná rozšíření virtuálních počítačů. Pokud chcete používat úložiště Azure, ujistěte se, že provoz do těchto úložišť neprojde vaším místním intranetem. Pokud jste vytvořili uživatelsky definované trasy, abyste mohli povolit vynucené tunelování, nezapomeňte přidat trasu, která směruje provoz do úložišť přímo na Internet, a ne prostřednictvím připojení VPN typu Site-to-site.

* **SLES**

  Také je nutné přidat trasy pro IP adresy uvedené v \\ \\ regionserverclnt. cfg. Příklad ukazuje následující obrázek:

  ![Vynucené tunelování][deployment-guide-figure-50]


* **RHEL**

  Také je nutné přidat trasy pro IP adresy hostitelů uvedených v \\ \\ Yumu. úložiště. d \\ rhui – služby Vyrovnávání zatížení. Příklad naleznete na předchozím obrázku.

* **Oracle Linux**

  K dispozici nejsou žádná úložiště pro Oracle Linux v Azure. Je nutné nakonfigurovat vlastní úložiště pro Oracle Linux nebo použití veřejného úložiště.

Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem a předávání IP][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Konfigurace rozšíření Azure pro SAP

> [!NOTE]
> Obecný příkaz podpory: Pokud potřebujete podporu pro rozšíření Azure pro SAP, vždy prosím otevřete incident pomocí SAP pro komponentu BC-OP-NT-AZR pro Windows nebo BC-OP-LNX-AZR.
> V systému podpory SAP existují vyhrazení technici podpory Microsoftu, které vám pomůžou s našimi společnými zákazníky.

Po přípravě virtuálního počítače, jak je popsáno v tématu [scénáře nasazení virtuálních počítačů pro SAP v Azure][deployment-guide-3], je na virtuálním počítači nainstalovaný agent virtuálního počítače Azure. Dalším krokem je nasazení rozšíření Azure pro SAP, které je k dispozici v úložišti rozšíření Azure v globálních datacentrech Azure. Další informace najdete v tématu [plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver][planning-guide-9.1].

Právě probíhá vydání nové verze rozšíření Azure pro SAP. Nové rozšíření používá systémem přiřazenou identitu virtuálního počítače k získání informací o připojených discích, síťových rozhraních a samotném virtuálním počítači. Aby bylo možné získat přístup k těmto prostředkům, musí systémová identita virtuálního počítače mít oprávnění ke čtení pro virtuální počítač, disk s operačním systémem, datové disky a síťová rozhraní. V současné době doporučujeme instalovat pouze nové rozšíření v následujících scénářích:

1. Chcete nainstalovat rozšíření s Terraformu, Azure Resource Manager šablonou nebo jinými prostředky než Azure CLI nebo Azure PowerShell
1. Chcete nainstalovat rozšíření na SUSE SLES 15 nebo vyšší.
1. Podpora Microsoftu nebo SAP vás vyzve k instalaci nového rozšíření.
1. Chcete použít Azure Ultra disk nebo Standard Managed Disks

V těchto scénářích postupujte podle pokynů v části [Konfigurace nového rozšíření Azure pro SAP pomocí Azure PowerShell][deployment-guide-configure-new-extension-ps] pro Azure PowerShell nebo [Konfigurace nového rozšíření Azure pro SAP pomocí rozhraní příkazového řádku Azure CLI][deployment-guide-configure-new-extension-cli] pro Azure CLI.

Pro instalaci a konfiguraci standardní verze rozšíření Azure pro SAP použijte [Azure PowerShell][deployment-guide-4.5.1] nebo rozhraní příkazového [řádku Azure][deployment-guide-4.5.2] .

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell pro virtuální počítače se systémy Linux a Windows

Postup instalace rozšíření Azure pro SAP pomocí prostředí PowerShell:

1. Ujistěte se, že máte nainstalovanou nejnovější verzi rutiny Azure PowerShell. Další informace najdete v tématu [nasazení rutin Azure PowerShell][deployment-guide-4.1].  
1. Spusťte následující rutinu Azure PowerShellu.
    Seznam dostupných prostředí získáte spuštěním rutiny `Get-AzEnvironment` . Pokud chcete používat globální Azure, vaše prostředí je **AzureCloud**. V případě Azure Čína 21Vianet vyberte **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Po zadání dat účtu nasadí skript požadovaná rozšíření a povolí požadované funkce. Může to trvat několik minut.
Další informace o `Set-AzVMAEMExtension` naleznete v tématu [set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Úspěšné spuštění rutiny Azure specifické pro SAP – AzVMAEMExtension][deployment-guide-figure-900]

`Set-AzVMAEMExtension`Konfigurace provádí všechny kroky pro konfiguraci shromažďování dat hostitele pro SAP.

Výstup skriptu obsahuje následující informace:

* Potvrzení, že je nakonfigurované shromažďování dat pro disk s operačním systémem a všechny další datové disky.
* Následující dvě zprávy potvrzují konfiguraci metrik úložiště pro určitý účet úložiště.
* Jeden řádek výstupu udává stav skutečné aktualizace rozšíření virtuálních počítačů pro konfiguraci SAP.
* Další řádek výstupu potvrzuje, že konfigurace byla nasazena nebo aktualizována.
* Poslední řádek výstupu je informativní. Zobrazuje vaše možnosti pro testování rozšíření virtuálního počítače pro konfiguraci SAP.
* Pokud chcete ověřit, že se úspěšně provedly všechny kroky rozšíření Azure VM Extension pro SAP a že infrastruktura Azure poskytuje potřebná data, pokračujte v kontrole připravenosti pro rozšíření Azure pro SAP, jak je popsané v tématu [kontroly připravenosti pro rozšíření][deployment-guide-5.1]Azure pro SAP.
* Počkejte 15-30 minut, než Azure Diagnostics shromáždí relevantní data.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI pro virtuální počítače se systémem Linux

Instalace rozšíření Azure pro SAP pomocí Azure CLI:

   1. Nainstalujte rozhraní příkazového řádku Azure Classic, jak je popsáno v tématu [instalace rozhraní příkazového řádku Azure Classic][azure-cli].
   1. Přihlaste se pomocí svého účtu Azure:

      ```console
      azure login
      ```

   1. Přepněte do režimu Azure Resource Manager:

      ```console
      azure config mode arm
      ```

   1. Povolit rozšíření Azure pro SAP:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Instalace pomocí Azure CLI 2,0

   1. Nainstalujte rozhraní příkazového řádku Azure CLI 2,0, jak je popsáno v tématu [instalace Azure cli 2,0][azure-cli-2].
   1. Přihlaste se pomocí svého účtu Azure:

      ```azurecli
      az login
      ```

   1. Instalace rozšíření Azure CLI AEM
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Nainstalujte rozšíření pomocí nástroje.
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Ověřte, jestli je rozšíření Azure pro SAP aktivní na virtuálním počítači Azure Linux. Ověřte, zda \\ existuje soubor var \\ lib \\ AzureEnhancedMonitor \\ PerfCounters. Pokud existuje, spusťte na příkazovém řádku tento příkaz, který zobrazí informace shromažďované rozšířením Azure pro SAP:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Výstup vypadá takto:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-powershell"></a><a name="2ad55a0d-9937-4943-9dd2-69bc2b5d3de0"></a>Konfigurace nového rozšíření Azure pro SAP pomocí Azure PowerShell

Nové rozšíření VM pro SAP používá pro přístup k datům monitorování a konfigurace virtuálního počítače spravovanou identitu přiřazenou k virtuálnímu počítači. Pokud chcete pro SAP nainstalovat nové rozšíření Azure pomocí PowerShellu, musíte nejdřív přiřadit takovou identitu k virtuálnímu počítači a udělit této identitě přístup ke všem prostředkům používaným tímto virtuálním počítačem, třeba k diskům a síťovým rozhraním.

> [!NOTE]
> Následující kroky vyžadují oprávnění vlastníka pro skupinu prostředků nebo jednotlivé prostředky (virtuální počítač, datové disky atd.).

1. Ujistěte se, že používáte agenta hostitele SAP 7,21 PL 47 nebo vyšší.
1. Ujistěte se, že jste odinstalovali aktuální verzi rozšíření virtuálního počítače pro SAP. Instalace obou verzí rozšíření virtuálního počítače pro SAP na stejném virtuálním počítači není podporovaná.
1. Ujistěte se, že máte nainstalovanou nejnovější verzi rutiny Azure PowerShell (aspoň 4.3.0). Další informace najdete v tématu [nasazení rutin Azure PowerShell][deployment-guide-4.1].
1. Spusťte následující rutinu Azure PowerShellu.
    Seznam dostupných prostředí získáte spuštěním rutiny `Get-AzEnvironment` . Pokud chcete používat globální Azure, vaše prostředí je **AzureCloud**. V případě Azure Čína 21Vianet vyberte **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-cli"></a><a name="c8749c24-fada-42ad-b114-f9aae2dc37da"></a>Konfigurace nového rozšíření Azure pro SAP pomocí Azure CLI

Nové rozšíření VM pro SAP používá pro přístup k datům monitorování a konfigurace virtuálního počítače spravovanou identitu přiřazenou k virtuálnímu počítači. Pokud chcete pro SAP nainstalovat nové rozšíření Azure pomocí rozhraní příkazového řádku Azure, musíte nejdřív přiřadit takovou identitu k virtuálnímu počítači a udělit této identitě přístup ke všem prostředkům používaným tímto virtuálním počítačem, třeba k diskům a síťovým rozhraním.

> [!NOTE]
> Následující kroky vyžadují oprávnění vlastníka pro skupinu prostředků nebo jednotlivé prostředky (virtuální počítač, datové disky atd.).

1. Ujistěte se, že používáte agenta hostitele SAP 7,21 PL 47 nebo vyšší.
1. Ujistěte se, že jste odinstalovali aktuální verzi rozšíření virtuálního počítače pro SAP. Instalace obou verzí rozšíření virtuálního počítače pro SAP na stejném virtuálním počítači není podporovaná.
1. Nainstalujte rozhraní příkazového řádku Azure CLI 2,0, jak je popsáno v tématu [instalace Azure cli 2,0][azure-cli-2].

1. Přihlaste se pomocí svého účtu Azure:

   ```azurecli
   az login
   ```

1. Postupujte podle kroků v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure CLI][qs-configure-cli-windows-vm] a povolte pro virtuální počítač spravovanou identitu přiřazenou systémem. Spravované identity přiřazené uživatelem nejsou podporovány rozšířením virtuálního počítače pro SAP. Můžete ale povolit i identitu přiřazenou systémem i uživatele.

   Příklad:
   ```azurecli
   az vm identity assign -g <resource-group-name> -n <vm name>
   ```

1. Přiřaďte přístup spravovaných identit ke skupině prostředků virtuálního počítače nebo všem síťovým rozhraním, spravovaným diskům a samotnému virtuálnímu počítači, jak je popsáno v tématu [přiřazení spravované identity k prostředku pomocí Azure CLI][howto-assign-access-cli] .

    Příklad:

    ```azurecli
    spID=$(az resource show -g <resource-group-name> -n <vm name> --query identity.principalId --out tsv --resource-type Microsoft.Compute/virtualMachines)
    rgId=$(az group show -g <resource-group-name> --query id --out tsv)
    az role assignment create --assignee $spID --role 'Reader' --scope $rgId
    ```

1. Spuštěním následujícího příkazu rozhraní příkazového řádku Azure nainstalujte rozšíření Azure pro SAP.
    Rozšíření je aktuálně podporováno pouze v AzureCloud. Azure Čína 21Vianet, Azure Government nebo některá z dalších speciálních prostředí se zatím nepodporují.

    ```azurecli
    # For Linux machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Linux --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'

    #For Windows machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Windows --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'
    ```

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Kontroly a odstraňování potíží

Po nasazení virtuálního počítače Azure a nastavení relevantního rozšíření Azure pro SAP ověřte, jestli všechny komponenty rozšíření fungují podle očekávání.

Spusťte kontrolu připravenosti pro rozšíření Azure pro SAP, jak je popsané v [části kontrolu připravenosti pro Azure Extension pro SAP][deployment-guide-5.1]. Pokud jsou všechny výsledky kontroly připravenosti kladné a všechny relevantní čítače výkonu se zobrazí v pořádku, rozšíření Azure pro SAP se úspěšně nastavilo. Můžete pokračovat v instalaci agenta hostitele SAP, jak je popsáno v tématu poznámky SAP v tématu [prostředky SAP][deployment-guide-2.2]. Pokud kontrolu připravenosti indikuje, že čítače chybí, spusťte kontrolu stavu pro Azure Extension pro SAP, jak je popsáno v tématu [o kontrole stavu rozšíření Azure pro konfiguraci SAP][deployment-guide-5.2]. Další možnosti řešení potíží najdete v tématu [řešení potíží s rozšířením Azure pro SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Kontroly připravenosti pro rozšíření Azure pro SAP

> [!NOTE]
> Existují dvě verze rozšíření virtuálního počítače. Tato kapitola se zabývá výchozím rozšířením virtuálního počítače. Pokud jste nainstalovali nové rozšíření virtuálních počítačů, přečtěte si prosím téma [kontroly připravenosti kapitoly pro nové rozšíření Azure pro SAP][deployment-guide-5.1-new] .

Tato kontrolu zajistí, že se všechny metriky výkonu, které se zobrazí v aplikaci SAP, poskytují pomocí základního rozšíření Azure pro SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Spuštění kontroly připravenosti na virtuálním počítači s Windows

1. Přihlaste se k virtuálnímu počítači Azure (použití účtu správce není nutné).
1. Otevřete okno příkazového řádku.
1. Na příkazovém řádku změňte adresář na instalační složku rozšíření Azure pro SAP: C: \\ balíčky \\ plugins \\ Microsoft. zákaznického poradního. AzureEnhancedMonitoring. AzureCATExtensionHandler, \\ &lt;>\\ drop

   *Verze* v cestě k příponě se může lišit. Pokud se v instalační složce zobrazují složky pro více verzí rozšíření, zkontrolujte konfiguraci služby Windows AzureEnhancedMonitoring a pak přepněte do složky označené jako *cesta ke spustitelnému souboru*.

   ![Vlastnosti služby, na které běží rozšíření Azure pro SAP][deployment-guide-figure-1000]

1. Na příkazovém řádku spusťte příkaz **azperflib.exe** bez parametrů.

   > [!NOTE]
   > Azperflib.exe běží ve smyčce a aktualizuje shromážděné čítače každých 60 sekund. Chcete-li ukončit smyčku, zavřete okno příkazového řádku.
   >
   >

Pokud rozšíření Azure pro SAP není nainstalované nebo služba AzureEnhancedMonitoring není spuštěná, rozšíření není správně nakonfigurované. Podrobné informace o tom, jak rozšíření nasadit, najdete v tématu [řešení potíží s rozšířením Azure pro SAP][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe je komponenta, kterou nelze použít pro vlastní účely. Jedná se o součást, která poskytuje data infrastruktury Azure související s virtuálním počítačem výhradně pro agenta hostitele SAP.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Kontrolovat výstup azperflib.exe

Azperflib.exe výstup zobrazuje všechny naplněné čítače výkonu Azure pro SAP. V dolní části seznamu shromážděných čítačů zobrazuje souhrn a indikátor stavu stav rozšíření Azure pro SAP.

![Výstup kontroly stavu spuštěním azperflib.exe, což znamená, že neexistují žádné problémy][deployment-guide-figure-1100]
<a name="figure-11"></a>

Zkontroluje výsledek vrácený pro **čítače celkový** výstup, který je hlášený jako prázdný, a pro **stav**uvedený na předchozím obrázku.

Vyhodnotit výsledné hodnoty následujícím způsobem:

| Hodnoty výsledků Azperflib.exe | Rozšíření Azure pro stav SAP |
| --- | --- |
| **Volání rozhraní API – nedostupné** | Čítače, které nejsou k dispozici, nemusí být možné použít pro konfiguraci virtuálního počítače, nebo se jedná o chyby. Zobrazení **stavu**. |
| **Čítače celkem – prázdné** |Následující dva čítače služby Azure Storage můžou být prázdné: <ul><li>Serverová latence čtení z paměti – MS</li><li>Čtení z paměti op E2E MS</li></ul>Všechny ostatní čítače musí mít hodnoty. |
| **Stav** |Pouze v případě, že se v zobrazení stav vrátí **OK**. |
| **Diagnostika** |Podrobné informace o stavu. |

Pokud hodnota **stavu** není v **pořádku**, postupujte podle pokynů v části [kontroly stavu pro rozšíření Azure pro konfiguraci SAP][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Spuštění kontroly připravenosti na virtuálním počítači se systémem Linux

1. Připojte se k virtuálnímu počítači Azure pomocí SSH.

1. Podívejte se na výstup rozšíření Azure pro SAP.

   a.  Spuštěním příkazu `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Očekávaný výsledek**: vrátí seznam čítačů výkonu. Soubor by neměl být prázdný.

   b. Spuštěním příkazu `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Očekávaný výsledek**: vrátí jeden řádek, kde chyba je **none**, například **3; config; Chyba; 0; 0; 0; None; 0; 1456416792; TST-servercs;**

   c. Spuštěním příkazu `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Očekávaný výsledek**: vrátí hodnotu jako prázdnou nebo neexistuje.

Pokud předchozí kontrola nebyla úspěšná, spusťte tyto další kontroly:

1. Ujistěte se, že je waagent nainstalovaný a povolený.

   a.  Spuštěním příkazu `sudo ls -al /var/lib/waagent/`

     **Očekávaný výsledek**: Vypíše obsah adresáře waagent.

   b.  Spuštěním příkazu `ps -ax | grep waagent`

   **Očekávaný výsledek**: zobrazuje jednu položku podobnou této: `python /usr/sbin/waagent -daemon`

1. Ujistěte se, že je nainstalovaná a spuštěná přípona Azure pro SAP.

   a.  Spuštěním příkazu `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Očekávaný výsledek**: zobrazuje obsah rozšíření Azure pro adresář SAP.

   b. Spuštěním příkazu `ps -ax | grep AzureEnhanced`

   **Očekávaný výsledek**: zobrazuje jednu položku podobnou této: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Nainstalujte agenta hostitele SAP, jak je popsáno v tématu SAP Note [1031096], a zkontrolujte výstup `saposcol` .

   a.  Spuštěním příkazu `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Spuštěním příkazu `dump ccm`

   c.  Ověřte, zda je metrika **přístupu Virtualization_Configuration \enhanced pro monitorování** **pravdivá**.

Pokud již máte nainstalovaný aplikační Server SAP NetWeaver ABAP, otevřete transakční ST06 a ověřte, zda je monitorování povoleno.

Pokud některá z těchto kontrol selže a podrobnější informace o tom, jak toto rozšíření nasadit, najdete v tématu [řešení potíží s rozšířením Azure pro SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-new-azure-extension-for-sap"></a><a name="7bf24f59-7347-4c7a-b094-4693e4687ee5"></a>Kontroly připravenosti pro nové rozšíření Azure pro SAP

> [!NOTE]
> Existují dvě verze rozšíření virtuálního počítače. Tato kapitola se zabývá novým rozšířením virtuálního počítače. Pokud jste nainstalovali výchozí rozšíření virtuálního počítače, přečtěte si prosím téma [kontroly připravenosti kapitoly pro Azure Extension pro SAP][deployment-guide-5.1].

Tato kontrolu zajistí, že se všechny metriky výkonu, které se zobrazí v aplikaci SAP, poskytují pomocí základního rozšíření Azure pro SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Spuštění kontroly připravenosti na virtuálním počítači s Windows

1. Přihlaste se k virtuálnímu počítači Azure (použití účtu správce není nutné).
1. Otevřete webový prohlížeč a přejděte na http://127.0.0.1:11812/azure4sap/metrics
1. V prohlížeči by se měl zobrazit nebo stáhnout soubor XML, který obsahuje data monitorování virtuálního počítače. V takovém případě se ujistěte, že je nainstalovaná přípona Azure pro SAP.

##### <a name="check-the-content-of-the-xml-file"></a>Ověřte obsah souboru XML

Soubor XML, ke kterému máte přístup, http://127.0.0.1:11812/azure4sap/metrics obsahuje všechny naplněné čítače výkonu Azure pro SAP. Obsahuje taky Shrnutí a indikátor stavu stavu rozšíření Azure pro SAP.

Ověřte hodnotu prvku **Popis stavu poskytovatele** . Pokud hodnota není **OK**, postupujte podle pokynů v části [kontroly stavu pro nové rozšíření Azure pro konfiguraci SAP][deployment-guide-5.2-new].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Spuštění kontroly připravenosti na virtuálním počítači se systémem Linux

1. Připojte se k virtuálnímu počítači Azure pomocí SSH.

1. Podívejte se na výstup následujícího příkazu.

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Očekávaný výsledek**: vrátí dokument XML, který obsahuje informace o monitorování virtuálního počítače, jeho disků a síťových rozhraní.

Pokud předchozí kontrola nebyla úspěšná, spusťte tyto další kontroly:

1. Ujistěte se, že je waagent nainstalovaný a povolený.

   a.  Spuštěním příkazu `sudo ls -al /var/lib/waagent/`

     **Očekávaný výsledek**: Vypíše obsah adresáře waagent.

   b.  Spuštěním příkazu `ps -ax | grep waagent`

   **Očekávaný výsledek**: zobrazuje jednu položku podobnou této: `python /usr/sbin/waagent -daemon`

1. Ujistěte se, že je nainstalovaná a spuštěná přípona Azure pro SAP.

   a.  Spuštěním příkazu `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`

   **Očekávaný výsledek**: zobrazuje obsah rozšíření Azure pro adresář SAP.

   b. Spuštěním příkazu `ps -ax | grep AzureEnhanced`

   **Očekávaný výsledek**: zobrazuje jednu položku podobnou této: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`

1. Nainstalujte agenta hostitele SAP, jak je popsáno v tématu SAP Note [1031096], a zkontrolujte výstup `saposcol` .

   a.  Spuštěním příkazu `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Spuštěním příkazu `dump ccm`

   c.  Ověřte, zda je metrika **přístupu Virtualization_Configuration \enhanced pro monitorování** **pravdivá**.

Pokud již máte nainstalovaný aplikační Server SAP NetWeaver ABAP, otevřete transakční ST06 a ověřte, zda je monitorování povoleno.

Pokud některá z těchto kontrol selže a získáte podrobné informace o tom, jak toto rozšíření znovu nasadit, přečtěte si téma [řešení potíží s novým rozšířením Azure pro SAP][deployment-guide-5.3-new].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Kontrolu stavu pro rozšíření Azure pro konfiguraci SAP

> [!NOTE]
> Existují dvě verze rozšíření virtuálního počítače. Tato kapitola se zabývá výchozím rozšířením virtuálního počítače. Pokud jste nainstalovali nové rozšíření virtuálních počítačů, přečtěte si prosím téma [o kontrole stavu pro nové rozšíření Azure pro konfiguraci SAP][deployment-guide-5.2-new].

Pokud některá data infrastruktury nejsou doručena správně, jak je uvedeno v testu popsaných v tématu [Kontrola připravenosti pro Azure Extension pro SAP][deployment-guide-5.1], spusťte `Test-AzVMAEMExtension` rutinu, abyste zkontrolovali, jestli je správně nakonfigurovaná infrastruktura Azure a rozšíření Azure pro SAP.

1. Ujistěte se, že máte nainstalovanou nejnovější verzi rutiny Azure PowerShell, jak je popsáno v tématu [Deploying Azure PowerShell rutiny][deployment-guide-4.1].
1. Spusťte následující rutinu Azure PowerShellu. Seznam dostupných prostředí získáte spuštěním rutiny `Get-AzEnvironment` . Pokud chcete použít globální Azure, vyberte prostředí **AzureCloud** . V případě Azure Čína 21Vianet vyberte **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Skript testuje konfiguraci virtuálního počítače, který vyberete.

   ![Výstup úspěšného testu rozšíření Azure pro SAP][deployment-guide-figure-1300]

Ujistěte se, že všechny výsledky kontroly stavu jsou v **pořádku**. Pokud se některé kontroly nezobrazují, spusťte rutinu **aktualizace, jak**je popsáno v tématu [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5]. Počkejte 15 minut a opakujte kontroly popsané v části [Kontrola připravenosti pro Azure rozšíření pro SAP][deployment-guide-5.1] a [kontrolu stavu pro rozšíření Azure pro konfiguraci SAP][deployment-guide-5.2]. Pokud kontroly stále indikují problém s některými nebo všemi čítači, přečtěte si téma [řešení potíží s rozšířením Azure pro SAP][deployment-guide-5.3].

> [!Note]
> V případech, kdy používáte spravované standardní disky Azure, se můžete setkat s upozorněními. Místo testů se zobrazí upozornění vracející "OK". To je normální a určené pro případ tohoto typu disku. Viz také téma [řešení potíží s rozšířením Azure pro SAP][deployment-guide-5.3] .
> 

### <a name="health-check-for-the-new-azure-extension-for-sap-configuration"></a><a name="464ac96d-7d3c-435d-a5ae-3faf3bfef4b3"></a>Kontrolu stavu pro nové rozšíření Azure pro konfiguraci SAP

> [!NOTE]
> Existují dvě verze rozšíření virtuálního počítače. Tato kapitola se zabývá novým rozšířením virtuálního počítače. Pokud jste nainstalovali výchozí rozšíření virtuálního počítače, přečtěte si prosím téma [o kontrole stavu pro rozšíření Azure pro konfiguraci SAP][deployment-guide-5.2].

Pokud některá z dat infrastruktury nejsou dodána správně, jak je uvedeno v testu popsaných v tématu [Kontrola připravenosti pro Azure Extension pro SAP][deployment-guide-5.1-new], spusťte `Get-AzVMExtension` rutinu, abyste zkontrolovali, jestli je nainstalované rozšíření Azure pro SAP. `Test-AzVMAEMExtension`Ještě nepodporuje nové rozšíření. Jakmile rutina podporuje nové rozšíření, aktualizujeme Tento článek.

1. Ujistěte se, že máte nainstalovanou nejnovější verzi rutiny Azure PowerShell, jak je popsáno v tématu [Deploying Azure PowerShell rutiny][deployment-guide-4.1].
1. Spusťte následující rutinu Azure PowerShellu. Seznam dostupných prostředí získáte spuštěním rutiny `Get-AzEnvironment` . Pokud chcete použít globální Azure, vyberte prostředí **AzureCloud** . V případě Azure Čína 21Vianet vyberte **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Rutina testuje konfiguraci rozšíření virtuálního počítače pro SAP na virtuálním počítači, který vyberete.

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Řešení potíží s rozšířením Azure pro SAP

> [!NOTE]
> Existují dvě verze rozšíření virtuálního počítače. Tato kapitola se zabývá výchozím rozšířením virtuálního počítače. Pokud jste nainstalovali nové rozšíření virtuálních počítačů, přečtěte si kapitolu [Poradce při potížích s novým rozšířením Azure pro SAP][deployment-guide-5.3-new].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Logo Windows][Logo_Windows] Čítače výkonu Azure se vůbec nezobrazují

Služba systému Windows AzureEnhancedMonitoring shromažďuje metriky výkonu v Azure. Pokud služba není nainstalovaná správně nebo pokud ve vašem VIRTUÁLNÍm počítači není spuštěná, nemůžete shromažďovat metriky výkonu.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Instalační adresář rozšíření Azure pro SAP je prázdný.

###### <a name="issue"></a>Problém

Instalační adresář C: \\ balíčky \\ Plug-in \\ Microsoft. zákaznického poradního. AzureEnhancedMonitoring. AzureCATExtensionHandler \\ &lt; verze>\\ drop jsou prázdné.

###### <a name="solution"></a>Řešení

Rozšíření není nainstalované. Určete, zda se jedná o problém s proxy serverem (jak je popsáno výše). Možná budete muset restartovat počítač nebo znovu spustit `Set-AzVMAEMExtension` konfigurační skript.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Služba pro rozšíření Azure pro SAP neexistuje.

###### <a name="issue"></a>Problém

Služba systému Windows AzureEnhancedMonitoring neexistuje.

Azperflib.exe výstup vyvolá chybu:

![Spuštění azperflib.exe označuje, že služba rozšíření Azure pro SAP není spuštěná.][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Řešení

Pokud služba neexistuje, rozšíření Azure pro SAP se nenainstalovalo správně. Znovu nasaďte rozšíření pomocí kroků popsaných ve scénáři nasazení v tématu [scénáře nasazení virtuálních počítačů pro SAP v Azure][deployment-guide-3].

Po nasazení rozšíření po jedné hodině znovu ověřte, zda jsou na virtuálním počítači Azure k dispozici čítače výkonu Azure.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Služba pro rozšíření Azure pro SAP existuje, ale nepodařilo se ji spustit.

###### <a name="issue"></a>Problém

Služba AzureEnhancedMonitoring systému Windows existuje a je povolená, ale nespustí se. Další informace najdete v protokolu událostí aplikace.

###### <a name="solution"></a>Řešení

Konfigurace je nesprávná. Restartujte rozšíření Azure pro SAP na virtuálním počítači, jak je popsáno v tématu [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5].

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Logo Windows][Logo_Windows] Chybí některé čítače výkonu Azure.

Služba systému Windows AzureEnhancedMonitoring shromažďuje metriky výkonu v Azure. Služba získává data z několika zdrojů. Některá konfigurační data se shromažďují místně a některé metriky výkonu se čtou z Azure Diagnostics. Čítače úložiště se používají z vašeho protokolování na úrovni předplatného úložiště.

Pokud řešení potíží pomocí SAP Note [1999351] problém nevyřeší, spusťte znovu `Set-AzVMAEMExtension` konfigurační skript. Možná budete muset počkat hodinu, protože diagnostické a diagnostické čítače nemusí být vytvořené hned po povolení. Pokud se problém opakuje, otevřete zprávu zákaznická podpora SAP na komponentě BC-OP-NT-AZR pro Windows nebo BC-OP-LNX-AZR pro virtuální počítač se systémem Linux.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Logo Linux.][Logo_Linux] Čítače výkonu Azure se vůbec nezobrazují

Metriky výkonu v Azure se shromažďují pomocí démona. Pokud démon není spuštěn, nelze shromáždit žádné metriky výkonu.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Instalační adresář rozšíření Azure pro SAP je prázdný.

###### <a name="issue"></a>Problém

Složka \\ var \\ lib waagent neobsahuje podadresář \\ \\ pro rozšíření Azure pro SAP.

###### <a name="solution"></a>Řešení

Rozšíření není nainstalované. Určete, zda se jedná o problém s proxy serverem (jak je popsáno výše). Možná budete muset restartovat počítač nebo znovu spustit `Set-AzVMAEMExtension` konfigurační skript.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Spuštění Set-AzVMAEMExtension a test-AzVMAEMExtension zobrazit varovné zprávy s oznámením, že standardní Managed Disks nejsou podporovány.

###### <a name="issue"></a>Problém

Při spouštění set-AzVMAEMExtension nebo AzVMAEMExtension zpráv, jako jsou tyto zprávy, se zobrazí následující:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Spuštění azperfli.exe jak bylo popsáno výše, můžete získat výsledek, který indikuje, že stav není v pořádku. 

###### <a name="solution"></a>Řešení

Zprávy jsou způsobeny faktem, že standardní Managed Disks neposkytují rozhraní API používaná rozšířením SAP pro SAP pro kontrolu statistik standardních Azure Storagech účtů. Nejedná se o obavy. Důvod, proč se shromažďování dat pro účty Standard Disk Storage omezuje na vstupy a výstupy, ke kterým došlo často. Služba Managed disks se vyhne omezování tím, že omezuje počet disků v účtu úložiště. Proto tento typ dat není kritický.


#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Logo Linux.][Logo_Linux] Chybí některé čítače výkonu Azure.

Metriky výkonu v Azure se shromažďují pomocí démona, který získává data z několika zdrojů. Některá konfigurační data se shromažďují místně a některé metriky výkonu se čtou z Azure Diagnostics. Čítače úložiště přicházejí z protokolů v rámci vašeho předplatného úložiště.

Úplný a aktuální seznam známých problémů najdete v článku SAP Note [1999351], který obsahuje další informace o řešení potíží pro rozšíření Azure pro SAP.

Pokud problém nevyřešíte pomocí SAP Note [1999351] , spusťte konfigurační skript znovu, `Set-AzVMAEMExtension` jak je popsáno v tématu [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5]. Může se stát, že budete muset počkat na hodinu, protože čítače pro diagnostiku úložiště nebo diagnostické čítače se nemůžou vytvářet hned po povolení. Pokud se problém opakuje, otevřete zprávu zákaznická podpora SAP na komponentě BC-OP-NT-AZR pro Windows nebo BC-OP-LNX-AZR pro virtuální počítač se systémem Linux.

### <a name="troubleshooting-the-new-azure-extension-for-sap"></a><a name="b7afb8ef-a64c-495d-bb37-2af96688c530"></a>Řešení potíží s novým rozšířením Azure pro SAP

> [!NOTE]
> Existují dvě verze rozšíření virtuálního počítače. Tato kapitola se zabývá novým rozšířením virtuálního počítače. Pokud jste nainstalovali výchozí rozšíření virtuálního počítače, přečtěte si kapitolu [Poradce při potížích s rozšířením Azure pro SAP][deployment-guide-5.3].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Logo Windows][Logo_Windows] Čítače výkonu Azure se vůbec nezobrazují

Proces AzureEnhancedMonitoring shromažďuje metriky výkonu v Azure. Pokud proces ve vašem VIRTUÁLNÍm počítači neběží, nemůžete shromažďovat metriky výkonu.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Instalační adresář rozšíření Azure pro SAP je prázdný.

###### <a name="issue"></a>Problém

Instalační adresář C: \\ \\ moduly plug-in \\ Microsoft. zákaznického poradního. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt; verze> prázdné.

###### <a name="solution"></a>Řešení

Rozšíření není nainstalované. Určete, zda se jedná o problém s proxy serverem (jak je popsáno výše). Možná budete muset restartovat počítač nebo znovu nainstalovat rozšíření virtuálního počítače.

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Logo Windows][Logo_Windows] Chybí některé čítače výkonu Azure.

Proces Windows AzureEnhancedMonitoring shromažďuje metriky výkonu v Azure. Proces získává data z několika zdrojů. Některá konfigurační data se shromažďují místně a některé metriky výkonu se čtou z Azure Monitor.

Pokud problém nevyřešíte pomocí protokolu SAP Note [1999351] , otevřete zprávu zákaznická podpora SAP na komponentě BC-op-NT-AZR pro Windows nebo BC-op-LNX-AZR pro virtuální počítač se systémem Linux. Připojte prosím soubor protokolu C: \\ balíčky \\ plugins \\ Microsoft. zákaznického poradního. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt;>\\logapp.txt k incidentu.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Logo Linux.][Logo_Linux] Čítače výkonu Azure se vůbec nezobrazují

Metriky výkonu v Azure se shromažďují pomocí démona. Pokud démon není spuštěn, nelze shromáždit žádné metriky výkonu.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Instalační adresář rozšíření Azure pro SAP je prázdný.

###### <a name="issue"></a>Problém

Složka \\ var \\ lib waagent neobsahuje podadresář \\ \\ pro rozšíření Azure pro SAP.

###### <a name="solution"></a>Řešení

Rozšíření není nainstalované. Určete, zda se jedná o problém s proxy serverem (jak je popsáno výše). Možná budete muset restartovat počítač nebo znovu nainstalovat rozšíření virtuálního počítače.

#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Logo Linux.][Logo_Linux] Chybí některé čítače výkonu Azure.

Metriky výkonu v Azure se shromažďují pomocí démona, který získává data z několika zdrojů. Některá konfigurační data se shromažďují místně a některé metriky výkonu se čtou z Azure Monitor.

Úplný a aktuální seznam známých problémů najdete v článku SAP Note [1999351], který obsahuje další informace o řešení potíží pro rozšíření Azure pro SAP.

Pokud problém nevyřešíte pomocí SAP Note [1999351] , nainstalujte rozšíření znovu, jak je popsáno v tématu [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5]. Pokud se problém opakuje, otevřete zprávu zákaznická podpora SAP na komponentě BC-OP-NT-AZR pro Windows nebo BC-OP-LNX-AZR pro virtuální počítač se systémem Linux. Připojte prosím &lt; k incidentu soubor protokolu/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-verze>/logapp.txt.

## <a name="azure-extension-error-codes"></a>Kódy chyb rozšíření Azure

| ID chyby | Popis chyby | Řešení |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | Chybí konfigurace aplikace. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | V konfiguraci aplikace se nenašly žádné ID nasazení. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | V konfiguraci aplikace není RoleInstanceId. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | V konfiguraci aplikace není RoleInstanceId. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Nejde přečíst konfiguraci Azure. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | Chybí konfigurační soubor aplikace. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | V konfiguraci aplikace není žádná velikost virtuálního počítače. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | Čítač GlobalMemoryStatusEx se nezdařil. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | Čítač MaxHwFrequency se nezdařil. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | Čítače síťových adaptérů selhaly. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | Čítač mapování disků se nezdařil. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | Čítač názvu procesoru se nezdařil. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | Čítač mapování disků se nezdařil. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | V konfiguračním souboru rozšíření chybí metrika typ disku config.xml. ' Typ disku ' společně s některými jinými čítači byl představen v v 2.2.0.68 12/16/2015. Pokud jste rozšíření nasadili před 12/16/2015, použije se starý konfigurační soubor. Rozhraní rozšíření Azure automaticky upgraduje rozšíření na novější verzi, ale config.xml zůstane beze změny. Pokud chcete aktualizovat konfiguraci, Stáhněte a spusťte nejnovější instalační skript prostředí PowerShell. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Není ukládání do mezipaměti disku. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Žádná propustnost disku SLA. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Bez IOPS na disk SLA. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | Čítač mapování disků se nezdařil. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | Poslední čítač změny hardwaru se nezdařil. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | Čítače síťových adaptérů selhaly. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | Kvůli nástroji Sysprep virtuálního počítače se změnil identifikátor SID systému Windows. | [opětovné nasazení po dokončení programu Sysprep][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | Přístup k analýze úložiště se nezdařil. <br /><br />V případě, že populace dat služby Storage na nově vytvořeném virtuálním počítači může trvat až půl hodiny, může tato chyba zmizet i po nějaké době. Pokud se chyba stále zobrazuje, znovu spusťte instalační skript. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | Žádné čítače Analýza úložiště. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | Analýza úložiště se nezdařilo. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="wad_004"></a>WAD/004 | Chybná konfigurace WAD | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="wad_002"></a>WAD/002 | Neočekávaný formát WAD | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="wad_001"></a>WAD/001 | Nenašly se žádné WAD čítače. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="wad_040"></a>WAD/040 | Našly se zastaralé čítače WAD. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="wad_003"></a>WAD/003 | Nejde přečíst tabulku WAD. Neexistuje žádné připojení k tabulce WAD. Může to mít několik příčin:<br /><br /> 1) zastaralá konfigurace <br />2) žádné síťové připojení k Azure <br />3) problémy s instalací WAD | [spustit instalační skript][deployment-guide-run-the-script]<br />[opravit připojení k Internetu][deployment-guide-fix-internet-connection]<br />[kontaktujte podporu][deployment-guide-contact-support] |
| <a name="prf_011"></a>PRF/011 | Metriky síťové karty Perfmon selhaly. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="prf_012"></a>PRF/012 | Metriky disku Perfmon selhaly. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="prf_013"></a>PRF/013 | Některé metriky prefmon se nezdařily. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="prf_014"></a>PRF/014 | Nástroji PerfMon se nepodařilo vytvořit čítač. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | Nejsou nakonfigurovaní žádní zprostředkovatelé metriky. | [kontaktujte podporu][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | Chybná konfigurace Analýza úložiště. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | Nepovedlo se Analýza úložiště metriky. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | Jeden ze zprostředkovatelů metrik se nezdařil. | [spustit instalační skript][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | Vlákno zprostředkovatele se nezdařilo. | [kontaktujte podporu][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Podrobné pokyny k uvedeným řešením

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Spuštění instalačního skriptu

Postupujte podle kroků v části [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5] v tomto průvodci a nainstalujte rozšíření znovu. Všimněte si, že některé čítače můžou pro zřizování vyžadovat až 30 minut.

Pokud chyby nezmizí, obraťte se na [podporu][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Kontaktujte podporu

Neočekávaná chyba nebo neexistuje žádné známé řešení. Shromážděte soubor AzureEnhancedMonitoring_service. log umístěný ve složce C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Drop (Windows) nebo/var/log/Azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) a požádejte o další pomoc podporu SAP.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Opětovné nasazení po dokončení programu Sysprep

Pokud plánujete sestavit zobecněnou bitovou kopii operačního systému nástroje Sysprep (která může zahrnovat software SAP), doporučuje se, aby tato image neobsahovala rozšíření Azure pro SAP. Po nasazení nové instance zobecněné image operačního systému byste měli nainstalovat rozšíření Azure pro SAP.

Pokud ale zobecněná image operačního systému už obsahuje rozšíření Azure pro SAP, můžete použít následující alternativní řešení pro Překonfiguraci rozšíření na nově nasazené instanci virtuálního počítače:

* Na nově nasazené instanci virtuálního počítače odstraňte obsah následujících složek:  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Status

* Postupujte podle kroků v části [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5] v tomto průvodci a nainstalujte rozšíření znovu.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Opravit připojení k Internetu

Microsoft Azure virtuální počítač, na kterém běží rozšíření Azure pro SAP, vyžaduje přístup k Internetu. Pokud je tento virtuální počítač Azure součástí Virtual Network Azure nebo místní domény, ujistěte se, že jsou nastavená příslušná nastavení proxy serveru. Tato nastavení musí být také platná pro účet LocalSystem pro přístup k Internetu. Postup najdete v části [konfigurace proxy serveru][deployment-guide-configure-proxy] v této příručce.

Pokud navíc potřebujete nastavit statickou IP adresu pro virtuální počítač Azure, neprovádějte ruční nastavení ve virtuálním počítači Azure, ale nastavte ho pomocí [Azure PowerShell](../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md)rozhraní příkazového [řádku Azure](../../../virtual-network/virtual-networks-static-private-ip-arm-cli.md) [Azure Portal](../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Statická IP adresa je šířena prostřednictvím služby Azure DHCP.

Ruční nastavení statické IP adresy uvnitř virtuálního počítače Azure se nepodporuje a může vést k problémům s rozšířením Azure pro SAP.
