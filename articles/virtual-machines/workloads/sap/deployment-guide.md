---
title: Nasazení virtuálních počítačů Azure pro SAP NetWeaver | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nasadit software SAP na virtuální počítače s Linuxem v Azure.
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
ms.date: 09/16/2019
ms.author: sedusch
ms.openlocfilehash: 7fb87380047d046a580d1ad62b1d7107a94bb297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239886"
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
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Nasazení DBMS virtuálních počítačů Azure pro SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Ukládání do mezipaměti pro virtuální měny a virtuální disponáři)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Softwarový raid)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Úložiště Microsoft Azure)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktura nasazení RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Vysoká dostupnost a zotavení po havárii pomocí virtuálních počítačů Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 a novější)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 a dřívější verze)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Použití image SQL Serveru z Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Obecné SQL Server pro SAP v azure souhrnu)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifika služby SQL Server RDBMS)
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
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Zdroje SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Nasazení virtuálního virtuálního aplikace pomocí vlastní image)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scénář 1: Nasazení virtuálního počítače z Azure Marketplace pro SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scénář 2: Nasazení virtuálního počítače s vlastní image pro SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scénář 3: Přesunutí virtuálního počítače z místního prostředí pomocí negeneralizovaného virtuálního pevného disku Azure se sapem)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scénáře nasazení virtuálních počítačů pro SAP v Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Nasazení rutin Prostředí Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Stažení a import rutin prostředí PowerShell relevantních pro SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Připojení virtuálního počítače k místní doméně – jenom Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Stažení, instalace a povolení agenta virtuálních počítačů Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Konfigurace rozšíření Azure pro SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Kontrola připravenosti pro rozšíření Azure pro SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Kontrola stavu pro konfiguraci rozšíření Azure pro SAP)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Poradce při potížích s rozšířením Azure pro SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Konfigurace rozšíření virtuálního počítače)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Konfigurace serveru proxy)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Zkontroluje a řeší komplexní sběr dat pro hostitelského agenta SAP.)

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

[planning-guide]:planning-guide.md (Plánování a implementace virtuálních počítačů Azure pro SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Zdroje)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Vysoká dostupnost a zotavení po havárii pro SAP NetWeaver spuštěné na virtuálních počítačích Azure)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Vysoká dostupnost aplikačních serverů SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Použití automatického spuštění pro instance SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Jenom cloud – nasazení virtuálních strojů v Azure bez závislostí v místní zákaznické síti)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Mezimístní – nasazení jednoho nebo více virtuálních počítačů SAP v Azure plně integrované s místní sítí)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Oblasti Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domény selhání)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Upgradovat domény)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure dostupnost sady)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Koncept virtuálních počítačů Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Úložiště Azure Premium)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Přesunutí virtuálního počítače z místního do Azure s negeneralizovaným diskem)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Nasazení virtuálního virtuálního virtuálního ms s bitovou fotkou specifickou pro zákazníka)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Příprava na přesunutí virtuálního počítače z místního do Azure s negeneralizovaným diskem)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Příprava na nasazení virtuálního virtuálního virtuálního uživatele s image specifické pro zákazníka pro SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Příprava virtuálních počítačů se SAP pro Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Rozdíl mezi diskem Azure a image Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Nahrání virtuálního pevného disku z místního do Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopírování disků mezi účty Úložiště Azure)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struktura Virtuálního počítače/Virtuálního pevného disku pro nasazení SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Nastavení automatického připojení pro připojené disky)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Jeden virtuální virtuální počítače se scénářem ukázky/školení SAP NetWeaver)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Koncepty nasazení instancí SAP pouze v cloudu)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Řešení azure monitoringu pro SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Úložiště Azure Premium)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Síť Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Úložiště: Úložiště Microsoft Azure a datové disky)

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
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
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
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Nasazení a správa virtuálních počítačů pomocí šablon Azure Resource Manager a azure cli)
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

Virtuální počítače Azure je řešení pro organizace, které potřebují výpočetní prostředky a prostředky úložiště, v minimálním čase a bez zdlouhavých cyklů zásobování. Virtuální počítače Azure můžete použít k nasazení klasických aplikací, jako jsou aplikace založené na SAP NetWeaver, v Azure. Rozšiřte spolehlivost a dostupnost aplikace bez dalších místních prostředků. Virtuální počítače Azure podporují připojení mezi místními oblastmi, takže virtuální počítače Azure můžete integrovat do místních domén vaší organizace, privátních cloudů a systémového prostředí SAP.

V tomto článku se zabýváme kroky nasazení aplikací SAP na virtuálních počítačích (VM) v Azure, včetně alternativních možností nasazení a řešení potíží. Tento článek vychází z informací v [plánování a implementaci virtuálních počítačů Azure pro SAP NetWeaver][planning-guide]. Doplňuje také dokumentaci k instalaci SAP a poznámky SAP, které jsou primárními prostředky pro instalaci a nasazení softwaru SAP.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Nastavení virtuálního počítače Azure pro nasazení softwaru SAP zahrnuje několik kroků a prostředků. Než začnete, ujistěte se, že splňujete předpoklady pro instalaci softwaru SAP na virtuální počítače v Azure.

### <a name="local-computer"></a>Místní počítač

Ke správě virtuálních aplikací pro Windows nebo Linux můžete použít skript PowerShellu a portál Azure. Pro oba nástroje potřebujete místní počítač se systémem Windows 7 nebo novější verzí systému Windows. Pokud chcete spravovat jenom virtuální počítače s Linuxem a chcete pro tento úkol použít počítač s Linuxem, můžete použít Azure CLI.

### <a name="internet-connection"></a>Připojení k internetu

Chcete-li stáhnout a spustit nástroje a skripty, které jsou požadovány pro nasazení softwaru SAP, musíte být připojeni k Internetu. Virtuální počítač Azure, který používá rozšíření Azure pro SAP, také potřebuje přístup k internetu. Pokud je virtuální počítač Azure součástí virtuální sítě Azure nebo místní domény, ujistěte se, že jsou nastavena příslušná nastavení proxy serveru, jak je popsáno v [části Konfigurace proxy serveru][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Předplatné Microsoft Azure

Potřebujete aktivní účet Azure.

### <a name="topology-and-networking"></a>Topologie a vytváření sítí

Je třeba definovat topologii a architekturu nasazení SAP v Azure:

* Účty úložiště Azure, které se mají používat
* Virtuální síť, do které chcete nasadit systém SAP
* Skupina prostředků, do které chcete nasadit systém SAP
* Oblast Azure, kde chcete nasadit systém SAP
* Konfigurace SAP (dvouvrstvá nebo třívrstvá)
* Velikosti virtuálních počítačů a počet dalších datových disků, které mají být připojeny k virtuálním počítačům
* Konfigurace systému korekce a transportního systému SAP (CTS)

Vytvořte a nakonfigurujte účty úložiště Azure (v případě potřeby) nebo virtuální sítě Azure před zahájením procesu nasazení softwaru SAP. Informace o tom, jak vytvořit a nakonfigurovat tyto prostředky, najdete v [tématu plánování a implementace virtuálních počítačů Azure pro SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Velikost SAP

Znát následující informace, pro velikost SAP:

* Předpokládané zatížení SAP, například pomocí nástroje SAP Quick Sizer a čísla SAP Application Performance Standard (SAPS)
* Požadovaná potřeba procesoru a spotřeba paměti systému SAP
* Požadované operace vstupu/výstupu za sekundu
* Požadovaná šířka pásma sítě pro případnou komunikaci mezi virtuálními počítači v Azure
* Požadovaná šířka pásma sítě mezi místními prostředky a systémem SAP nasazeným v Azure

### <a name="resource-groups"></a>Skupiny prostředků

Ve Správci prostředků Azure můžete použít skupiny prostředků ke správě všech prostředků aplikací ve vašem předplatném Azure. Další informace naleznete v tématu [Přehled Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Prostředky

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Zdroje SAP

Při nastavování nasazení softwaru SAP potřebujete následující prostředky SAP:

* SAP Poznámka [1928533], která má:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačních systémů (OS) a databází
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované sapem.
* SAP Note [2178632] má podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [1409604] má požadovanou verzi SAP Host Agent pro Windows v Azure.
* SAP Note [2191498] má požadovanou verzi SAP Host Agent pro Linux v Azure.
* SAP Note [2243692] má informace o licencování SAP na Linuxu v Azure.
* SAP Note [1984787] má obecné informace o SUSE Linux Enterprise Server 12.
* SAP Note [2002167] má obecné informace o Red Hat Enterprise Linux 7.x.
* SAP Note [2069760] má obecné informace o Oracle Linux 7.x.
* SAP Note [1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* SAP Note [1597355] má obecné informace o swap-space pro Linux.
* [SAP na stránce Azure SCN](https://wiki.scn.sap.com/wiki/x/Pia7Gg) obsahuje novinky a kolekci užitečných prostředků.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP Notes pro Linux.
* Rutiny Prostředí PowerShell specifické pro SAP, které jsou součástí [Azure PowerShellu][azure-ps].
* Příkazy Azure CLI specifické pro SAP, které jsou součástí [azure cli][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Prostředky systému Windows

Tyto články microsoftu popisují nasazení SAP v Azure:

* [Plánování a implementace virtuálních počítačů Azure pro SAP NetWeaver][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP NetWeaver (tento článek)][deployment-guide]
* [Nasazení dbms virtuálních počítačů Azure pro SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scénáře nasazení softwaru SAP na virtuálních počítačích Azure

Máte několik možností pro nasazení virtuálních počítačů a přidružených disků v Azure. Je důležité pochopit rozdíly mezi možnostmi nasazení, protože můžete provést různé kroky k přípravě virtuálních počítačů pro nasazení na základě typu nasazení, který zvolíte.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scénář 1: Nasazení virtuálního počítače z Azure Marketplace pro SAP

K nasazení virtuálního počítače můžete použít bitovou kopii poskytovanou společností Microsoft nebo třetí stranou na Azure Marketplace. Marketplace nabízí některé standardní image operačního systému Windows Serveru a různých distribucí Linuxu. Můžete také nasadit bitovou kopii, která zahrnuje skumany snažení systému správy databáze (DBMS), například Microsoft SQL Server. Další informace o používání bitových kopií s umezy DBMS najdete v tématu [nasazení dbms služby Azure Virtual Machines pro SAP NetWeaver][dbms-guide].

Následující vývojový diagram ukazuje posloupnost kroků pro nasazení virtuálního počítače z Azure Marketplace specifickou pro SAP:

![Vývojový diagram nasazení virtuálních počítačů pro systémy SAP pomocí image virtuálního počítače z Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače pomocí portálu Azure

Nejjednodušší způsob, jak vytvořit nový virtuální počítač s image z Azure Marketplace, je pomocí portálu Azure.

1.  Přejděte do části <https://portal.azure.com/#create/hub> (Soubor > Nový > Jiné).  Nebo v nabídce portálu Azure vyberte **+ Nový**.
1.  Vyberte **Vypočítat**a pak vyberte typ operačního systému, který chcete nasadit. Například Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) nebo Oracle Linux 7.2. Ve výchozím zobrazení seznamu nejsou uvedeny všechny podporované operační systémy. Vyberte **zobrazit vše** pro úplný seznam. Další informace o podporovaných operačních systémech pro nasazení softwaru SAP naleznete v poznámce SAP [1928533].
1.  Na další stránce si přečtěte smluvní podmínky.
1.  V poli **Vybrat model nasazení** vyberte Správce **prostředků**.
1.  Vyberte **Vytvořit**.

Průvodce vás provede nastavením požadovaných parametrů pro vytvoření virtuálního počítače, kromě všech požadovaných prostředků, jako jsou síťová rozhraní a účty úložiště. Některé z těchto parametrů jsou:

1. **Základy**:
   * **Název**: Název prostředku (název virtuálního počítače).
   * **Typ disku virtuálního počítače**: Vyberte typ disku operačního systému. Pokud chcete pro datové disky používat úložiště Premium Storage, doporučujeme použít úložiště Premium pro disk operačního systému.
   * **Uživatelské jméno a heslo** nebo **veřejný klíč SSH**: Zadejte uživatelské jméno a heslo uživatele, který je vytvořen během zřizování. Pro virtuální počítač s Linuxem můžete zadat veřejný klíč Secure Shell (SSH), který používáte k přihlášení k počítači.
   * **Předplatné**: Vyberte předplatné, které chcete použít k zřízení nového virtuálního počítače.
   * **Skupina prostředků**: Název skupiny prostředků pro virtuální ho snažek. Můžete zadat název nové skupiny prostředků nebo název skupiny prostředků, která již existuje.
   * **Umístění:** Kde nasadit nový virtuální počítač. Pokud chcete virtuální počítač připojit k místní síti, ujistěte se, že vyberete umístění virtuální sítě, která propojuje Azure s vaší místní sítí. Další informace najdete v [tématu Microsoft Azure networking][planning-guide-microsoft-azure-networking] in [Azure Virtual Machines planning and implementation for SAP NetWeaver][planning-guide].
1. **Size** (Velikost):

     Seznam podporovaných typů virtuálních počítačů najdete v tématu SAP Note [1928533]. Ujistěte se, že jste vybrali správný typ virtuálního počítače, pokud chcete používat Azure Premium Storage. Úložiště Premium nepodporují všechny typy virtuálních počítačů. Další informace najdete v [tématu Úložiště: Microsoft Azure Storage a datové disky][planning-guide-storage-microsoft-azure-storage-and-data-disks] a [Azure Premium Storage][planning-guide-azure-premium-storage] ve [virtuálních počítačích Azure plánování a implementace pro SAP NetWeaver][planning-guide].

1. **Nastavení**:
   * **Úložiště**
     * **Typ disku**: Vyberte typ disku operačního systému. Pokud chcete pro datové disky používat úložiště Premium Storage, doporučujeme použít úložiště Premium pro disk operačního systému.
     * **Použití spravovaných disků**: Pokud chcete spravované disky používat, vyberte Ano. Další informace o spravovaných discích naleznete v kapitole [Spravované disky][planning-guide-managed-disks] v průvodci plánováním.
     * **Účet úložiště**: Vyberte existující účet úložiště nebo vytvořte nový. Ne všechny typy úložišť fungují pro spouštění aplikací SAP. Další informace o typech úložišť najdete [v tématu Struktura úložiště virtuálního počítače pro nasazení RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Síť**
     * **Virtuální síť** a **Podsíť**: Chcete-li integrovat virtuální počítač s intranetem, vyberte virtuální síť, která je připojená k místní síti.
     * **Veřejná IP adresa**: Vyberte veřejnou IP adresu, kterou chcete použít, nebo zadejte parametry pro vytvoření nové veřejné IP adresy. Veřejnou IP adresu můžete použít pro přístup k virtuálnímu počítači přes Internet. Ujistěte se, že jste také vytvořit skupinu zabezpečení sítě pomoci zabezpečit přístup k virtuálnímu počítači.
     * **Skupina zabezpečení sítě**: Další informace naleznete v tématu [Řízení toku síťového provozu pomocí skupin zabezpečení sítě][virtual-networks-nsg].
   * **Rozšíření**: Rozšíření virtuálních strojů můžete nainstalovat jejich přidáním do nasazení. V tomto kroku není nutné přidávat rozšíření. Rozšíření požadovaná pro podporu SAP jsou nainstalována později. Viz kapitola [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5] v této příručce.
   * **Vysoká dostupnost**: Vyberte sadu dostupnosti nebo zadejte parametry pro vytvoření nové skupiny dostupnosti. Další informace najdete v [tématu Azure dostupnost sady][planning-guide-3.2.3].
   * **Sledování**
     * **Diagnostika spouštění**: Můžete vybrat **zakázat** pro diagnostiku spouštění.
     * **Diagnostika hostovaného operačního systému**: Můžete vybrat **zakázat** pro monitorování diagnostiky.

1. **Shrnutí**:

   Zkontrolujte výběr y a pak vyberte **OK**.

Váš virtuální počítač se nasadí ve vybrané skupině prostředků.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Vytvoření virtuálního počítače pomocí šablony

Virtuální počítač můžete vytvořit pomocí jedné ze šablon SAP publikovaných v [úložišti GitHub azure-quickstart-templates][azure-quickstart-templates-github]. Virtuální počítač můžete taky vytvořit ručně pomocí [portálu Azure][virtual-machines-windows-tutorial], [PowerShellu][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]nebo [Azure CLI][virtual-machines-linux-tutorial].

* [**Šablona dvouvrstvé konfigurace (pouze jeden virtuální počítač)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Chcete-li vytvořit dvouvrstvý systém pomocí pouze jednoho virtuálního počítače, použijte tuto šablonu.
* [**Šablona dvouvrstvé konfigurace (pouze jeden virtuální počítač) – spravované disky** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Chcete-li vytvořit dvouvrstvý systém pomocí pouze jednoho virtuálního počítače a spravovaných disků, použijte tuto šablonu.
* [**Šablona třívrstvé konfigurace (více virtuálních počítačů)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Chcete-li vytvořit třívrstvý systém pomocí více virtuálních počítačů, použijte tuto šablonu.
* [**Šablona třívrstvé konfigurace (více virtuálních počítačů) – spravované disky** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Chcete-li vytvořit třívrstvý systém pomocí více virtuálních počítačů a spravovaných disků, použijte tuto šablonu.

Na webu Azure zadejte pro šablonu následující parametry:

1. **Základy**:
   * **Předplatné**: Předplatné použít k nasazení šablony.
   * **Skupina prostředků**: Skupina prostředků, která má být použita k nasazení šablony. Můžete vytvořit novou skupinu prostředků nebo můžete vybrat existující skupinu prostředků v předplatném.
   * **Umístění**: Kde nasadit šablonu. Pokud jste vybrali existující skupinu prostředků, použije se umístění této skupiny prostředků.

1. **Nastavení**:
   * **ID systému SAP:** ID systému SAP (SID).
   * **Typ operačního systému**: Operační systém, který chcete nasadit, například Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) nebo Oracle Linux 7.2.

     Zobrazení seznamu nezobrazuje všechny podporované operační systémy. Další informace o podporovaných operačních systémech pro nasazení softwaru SAP naleznete v poznámce SAP [1928533].
   * **Velikost systému SAP:** Velikost systému SAP.

     Počet SAPS nový systém poskytuje. Pokud si nejste jisti, kolik SAPS systém vyžaduje, zeptejte se svého technologického partnera SAP nebo systémového integrátora.
   * **Dostupnost systému** (pouze třívrstvá šablona): Dostupnost systému.

     Vyberte **HA** pro konfiguraci, která je vhodná pro instalaci s vysokou dostupností. Jsou vytvořeny dva databázové servery a dva servery pro ABAP SAP Central Services (ASCS).
   * **Typ úložiště** (pouze dvouvrstvá šablona): Typ úložiště, které se má použít.

     Pro větší systémy důrazně doporučujeme používat Azure Premium Storage. Další informace o typech úložišť najdete v těchto zdrojích:
      * [Použití úložiště Azure Premium SSD pro instanci SAP DBMS][2367194]
      * [Struktura úložiště virtuálního počítače pro nasazení RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Úložiště Premium: Vysoce výkonné úložiště pro úlohy virtuálních strojů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
   * **Admin uživatelské jméno** a **admin heslo**: uživatelské jméno a heslo.
     Pro přihlášení k virtuálnímu počítači se vytvoří nový uživatel.
   * **Nová nebo existující podsíť**: Určuje, zda je vytvořena nová virtuální síť a podsíť nebo zda se použije existující podsíť. Pokud už máte virtuální síť, která je připojená k místní síti, vyberte **Existující**.
   * **ID podsítě**: Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k které by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID&lt;obvykle vypadá takto: /subscriptions/ id předplatného&lt;>/resourceGroups/ název skupiny prostředků&lt;>/providers/Microsoft.Network/virtualNetworks/ název virtuální sítě>/podsítě/&lt;název podsítě>

1. **Obchodní podmínky**:  
    Zkontrolujte a přijměte právní podmínky.

1. Vyberte **Koupit**.

Agent virtuálního počítače Azure se nasadí ve výchozím nastavení, když použijete image z Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

V závislosti na konfiguraci místní sítě možná budete muset nastavit proxy server na virtuálním počítači. Pokud je váš virtuální počítač připojený k vaší místní síti přes VPN nebo ExpressRoute, virtuální počítač nemusí mít přístup k Internetu a nebude moct stáhnout požadovaná rozšíření virtuálního počítače nebo shromažďovat informace o infrastruktuře Azure pro agenta SAP Host prostřednictvím rozšíření SAP. pro Azure. Další informace naleznete [v tématu Konfigurace serveru proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Připojení k doméně (pouze Windows)

Pokud je vaše nasazení Azure připojené k místní instanci služby Active Directory nebo DNS prostřednictvím připojení VPN azure site-to-site nebo ExpressRoute *(to* se ve [virtuálních počítačích Azure][planning-guide]říká plánování a implementace pro SAP NetWeaver ), očekává se, že se virtuální počítač připojuje k místní doméně. Další informace o aspektech této úlohy najdete [v tématu připojení virtuálního počítače k místní doméně (jenom windows).][deployment-guide-4.3]

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurace rozšíření virtuálního počítače

Chcete-li si být jisti, že SAP podporuje vaše prostředí, nastavte rozšíření Azure pro SAP, jak je popsáno v [části Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5]. Zkontrolujte předpoklady pro SAP a požadované minimální verze jádra SAP a hostitele SAP Agent, v prostředcích uvedených v [sap zdrojů][deployment-guide-2.2].

#### <a name="vm-extension-for-sap-check"></a>Rozšíření virtuálního virtuálního uživatele pro kontrolu SAP

Zkontrolujte, zda rozšíření virtuálního uživatele pro SAP funguje, jak je popsáno v [části Kontroly a řešení potíží pro shromažďování dat od konce na konci pro agenta hostitele SAP][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Kroky po nasazení

Po vytvoření virtuálního počítače a virtuálního počítače se nasadí, je potřeba nainstalovat požadované softwarové součásti ve virtuálním počítače. Z důvodu pořadí instalace nasazení/softwaru v tomto typu nasazení virtuálního počítače musí být software, který se má nainstalovat, už k dispozici, buď v Azure, na jiném virtuálním počítači, nebo jako disk, který lze připojit. Nebo zvažte použití scénáře mezi místními, ve kterém je k dispozici připojení k místním prostředkům (instalačním sdíleným položkám).

Po nasazení virtuálního počítače v Azure postupujte podle stejných pokynů a nástrojů pro instalaci softwaru SAP na virtuální počítač, jako byste to udělali v místním prostředí. Chcete-li nainstalovat software SAP na virtuální počítač Azure, SAP i Microsoft doporučují nahrát a uložit instalační médium SAP na Azure VNd nebo spravované disky, nebo vytvořit virtuální počítač Azure, který funguje jako souborový server, který má všechna požadovaná instalační média SAP.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scénář 2: Nasazení virtuálního počítače s vlastní image pro SAP

Vzhledem k tomu, že různé verze operačního systému nebo dbms mají různé požadavky na opravu, nemusí obrázky, které najdete na Azure Marketplace, splňovat vaše potřeby. Místo toho můžete chtít vytvořit virtuální počítač pomocí vlastní image virtuálního počítače os/DBMS, které můžete znovu nasadit později.
Pomocí různých kroků můžete vytvořit soukromou bitovou kopii pro Linux než pro windows.

---
> ![Windows][Logo_Windows] Windows
>
> Chcete-li připravit bitovou kopii systému Windows, kterou můžete použít k nasazení více virtuálních počítačů, musí být nastavení systému Windows (jako je Windows SID a název hostitele) abstrahováno nebo zobecněno na místním virtuálním počítači. K tomu lze použít [sysprep.](https://msdn.microsoft.com/library/hh825084.aspx)
>
> ![Linux][Logo_Linux] Linux
>
> Chcete-li připravit bitovou kopii Linuxu, kterou můžete použít k nasazení více virtuálních počítačů, některá nastavení Linuxu musí být abstrahovaná nebo generalizovaná na místním virtuálním počítači. Můžete použít `waagent -deprovision` k tomu. Další informace najdete [v tématu Zachycení virtuálního počítače S Linuxem spuštěného v Azure][virtual-machines-linux-capture-image] a uživatelské [příručce pro agenta Azure Linux][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Můžete připravit a vytvořit vlastní image a pak ji použít k vytvoření více nových virtuálních počítačů. To je popsáno v [plánování a implementaci virtuálních počítačů Azure pro SAP NetWeaver][planning-guide]. Nastavte obsah databáze buď pomocí SAP Software Provisioning Manager k instalaci nového systému SAP (obnoví zálohu databáze z disku připojeného k virtuálnímu počítači), nebo přímým obnovením zálohy databáze z úložiště Azure, pokud váš DBMS podporuje. Další informace najdete v tématu [nasazení DBMS virtuálních počítačů Azure pro SAP NetWeaver][dbms-guide]. Pokud jste už nainstalovali systém SAP na místním virtuálním počítači (zejména pro dvouvrstvé systémy), můžete upravit nastavení systému SAP po nasazení virtuálního počítače Azure pomocí postupu přejmenování systému podporované sap software Zřizování (SAP Note [1619720]). V opačném případě můžete nainstalovat software SAP po nasazení virtuálního počítače Azure.

Následující vývojový diagram ukazuje posloupnost kroků pro nasazení virtuálního virtuálního uživatele z vlastní image specifické pro SAP:

![Vývojový diagram nasazení virtuálních virtuálních her pro systémy SAP pomocí image virtuálního počítače na privátním Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Vytvoření virtuálního počítače pomocí portálu Azure

Nejjednodušší způsob, jak vytvořit nový virtuální počítač z bitové kopie spravovaného disku, je pomocí portálu Azure. Další informace o tom, jak vytvořit spravovat bitovou kopii disku, [načtete-li se načlánekl, že v Azure zachytíte spravovanou bitovou kopii zobecněného virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Přejděte do části <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages> (Soubor > Nový > Jiné). Nebo v nabídce portálu Azure vyberte **Obrázky**.
1.  Vyberte bitovou kopii spravovaného disku, kterou chcete nasadit, a klikněte na **Vytvořit virtuální počítač.**

Průvodce vás provede nastavením požadovaných parametrů pro vytvoření virtuálního počítače, kromě všech požadovaných prostředků, jako jsou síťová rozhraní a účty úložiště. Některé z těchto parametrů jsou:

1. **Základy**:
   * **Název**: Název prostředku (název virtuálního počítače).
   * **Typ disku virtuálního počítače**: Vyberte typ disku operačního systému. Pokud chcete pro datové disky používat úložiště Premium Storage, doporučujeme použít úložiště Premium pro disk operačního systému.
   * **Uživatelské jméno a heslo** nebo **veřejný klíč SSH**: Zadejte uživatelské jméno a heslo uživatele, který je vytvořen během zřizování. Pro virtuální počítač s Linuxem můžete zadat veřejný klíč Secure Shell (SSH), který používáte k přihlášení k počítači.
   * **Předplatné**: Vyberte předplatné, které chcete použít k zřízení nového virtuálního počítače.
   * **Skupina prostředků**: Název skupiny prostředků pro virtuální ho snažek. Můžete zadat název nové skupiny prostředků nebo název skupiny prostředků, která již existuje.
   * **Umístění:** Kde nasadit nový virtuální počítač. Pokud chcete virtuální počítač připojit k místní síti, ujistěte se, že vyberete umístění virtuální sítě, která propojuje Azure s vaší místní sítí. Další informace najdete v [tématu Microsoft Azure networking][planning-guide-microsoft-azure-networking] in [Azure Virtual Machines planning and implementation for SAP NetWeaver][planning-guide].
1. **Size** (Velikost):

     Seznam podporovaných typů virtuálních počítačů najdete v tématu SAP Note [1928533]. Ujistěte se, že jste vybrali správný typ virtuálního počítače, pokud chcete používat Azure Premium Storage. Úložiště Premium nepodporují všechny typy virtuálních počítačů. Další informace najdete v [tématu Úložiště: Microsoft Azure Storage a datové disky][planning-guide-storage-microsoft-azure-storage-and-data-disks] a [Azure Premium Storage][planning-guide-azure-premium-storage] ve [virtuálních počítačích Azure plánování a implementace pro SAP NetWeaver][planning-guide].

1. **Nastavení**:
   * **Úložiště**
     * **Typ disku**: Vyberte typ disku operačního systému. Pokud chcete pro datové disky používat úložiště Premium Storage, doporučujeme použít úložiště Premium pro disk operačního systému.
     * **Použití spravovaných disků**: Pokud chcete spravované disky používat, vyberte Ano. Další informace o spravovaných discích naleznete v kapitole [Spravované disky][planning-guide-managed-disks] v průvodci plánováním.
   * **Síť**
     * **Virtuální síť** a **Podsíť**: Chcete-li integrovat virtuální počítač s intranetem, vyberte virtuální síť, která je připojená k místní síti.
     * **Veřejná IP adresa**: Vyberte veřejnou IP adresu, kterou chcete použít, nebo zadejte parametry pro vytvoření nové veřejné IP adresy. Veřejnou IP adresu můžete použít pro přístup k virtuálnímu počítači přes Internet. Ujistěte se, že jste také vytvořit skupinu zabezpečení sítě pomoci zabezpečit přístup k virtuálnímu počítači.
     * **Skupina zabezpečení sítě**: Další informace naleznete v tématu [Řízení toku síťového provozu pomocí skupin zabezpečení sítě][virtual-networks-nsg].
   * **Rozšíření**: Rozšíření virtuálních strojů můžete nainstalovat jejich přidáním do nasazení. V tomto kroku není nutné přidávat rozšíření. Rozšíření požadovaná pro podporu SAP jsou nainstalována později. Viz kapitola [Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5] v této příručce.
   * **Vysoká dostupnost**: Vyberte sadu dostupnosti nebo zadejte parametry pro vytvoření nové skupiny dostupnosti. Další informace najdete v [tématu Azure dostupnost sady][planning-guide-3.2.3].
   * **Sledování**
     * **Diagnostika spouštění**: Můžete vybrat **zakázat** pro diagnostiku spouštění.
     * **Diagnostika hostovaného operačního systému**: Můžete vybrat **zakázat** pro monitorování diagnostiky.

1. **Shrnutí**:

   Zkontrolujte výběr y a pak vyberte **OK**.

Váš virtuální počítač se nasadí ve vybrané skupině prostředků.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Vytvoření virtuálního počítače pomocí šablony

Chcete-li vytvořit nasazení pomocí privátní image operačního systému z portálu Azure, použijte jednu z následujících šablon SAP. Tyto šablony jsou publikovány v [úložišti GitHub azure-quickstart-templates][azure-quickstart-templates-github]. Můžete také ručně vytvořit virtuální počítač pomocí [prostředí PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Šablona dvouvrstvé konfigurace (pouze jeden virtuální počítač)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Chcete-li vytvořit dvouvrstvý systém pomocí pouze jednoho virtuálního počítače, použijte tuto šablonu.
* [**Šablona dvouvrstvé konfigurace (pouze jeden virtuální počítač) – image spravovaného disku** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Chcete-li vytvořit dvouvrstvý systém pomocí pouze jednoho virtuálního počítače a bitové kopie spravovaného disku, použijte tuto šablonu.
* [**Šablona třívrstvé konfigurace (více virtuálních počítačů)** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  Chcete-li vytvořit třívrstvý systém pomocí více virtuálních počítačů nebo vlastní bitové kopie operačního systému, použijte tuto šablonu.
* [**Šablona třívrstvé konfigurace (více virtuálních počítačů) – image spravovaného disku** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Chcete-li vytvořit třívrstvý systém pomocí více virtuálních počítačů nebo vlastní bitové kopie operačního systému a bitové kopie spravovaného disku, použijte tuto šablonu.

Na webu Azure zadejte pro šablonu následující parametry:

1. **Základy**:
   * **Předplatné**: Předplatné použít k nasazení šablony.
   * **Skupina prostředků**: Skupina prostředků, která má být použita k nasazení šablony. Můžete vytvořit novou skupinu prostředků nebo vybrat existující skupinu prostředků v předplatném.
   * **Umístění**: Kde nasadit šablonu. Pokud jste vybrali existující skupinu prostředků, použije se umístění této skupiny prostředků.
1. **Nastavení**:
   * **ID systému SAP:** ID systému SAP.
   * **Typ operačního systému**: Typ operačního systému, který chcete nasadit (Windows nebo Linux).
   * **Velikost systému SAP:** Velikost systému SAP.

     Počet SAPS nový systém poskytuje. Pokud si nejste jisti, kolik SAPS systém vyžaduje, zeptejte se svého technologického partnera SAP nebo systémového integrátora.
   * **Dostupnost systému** (pouze třívrstvá šablona): Dostupnost systému.

     Vyberte **HA** pro konfiguraci, která je vhodná pro instalaci s vysokou dostupností. Jsou vytvořeny dva databázové servery a dva servery pro ASCS.
   * **Typ úložiště** (pouze dvouvrstvá šablona): Typ úložiště, které se má použít.

     Pro větší systémy důrazně doporučujeme používat Azure Premium Storage. Další informace o typech úložišť najdete v následujících zdrojích informací:
      * [Použití úložiště Azure Premium SSD pro instanci SAP DBMS][2367194]
      * [Struktura úložiště virtuálního počítače pro nasazení RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Úložiště Premium: Vysoce výkonné úložiště pro úlohy virtuálních strojů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
   * **Identifikátor URI uživatelskébitové bitové kopie** (pouze nespravovaná šablona bitové&lt;kopie disku): Identifikátor URI soukromého virtuálního pevného disku bitové kopie operačního systému, například https:// název_účtu>.blob.core.windows.net/vhds/userimage.vhd.
   * **Účet úložiště uživatelských bitových kopií** (pouze nespravovaná šablona bitové kopie disku): Název účtu úložiště, ve kterém je uložena soukromá bitová kopie operačního systému, &lt;například název účtu> v https://&lt;název_účtu>.blob.core.windows.net/vhds/userimage.vhd.
   * **userImageId** (pouze šablona spravované bitové kopie disku): Id bitové kopie spravovaného disku, kterou chcete použít
   * **Admin uživatelské jméno** a **admin heslo**: uživatelské jméno a heslo.

     Pro přihlášení k virtuálnímu počítači se vytvoří nový uživatel.
   * **Nová nebo existující podsíť**: Určuje, zda je vytvořena nová virtuální síť a podsíť nebo zda se použije existující podsíť. Pokud už máte virtuální síť, která je připojená k místní síti, vyberte **Existující**.
   * **ID podsítě**: Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k které by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID&lt;obvykle vypadá takto: /subscriptions/ id předplatného&lt;>/resourceGroups/ název skupiny prostředků&lt;>/providers/Microsoft.Network/virtualNetworks/ název virtuální sítě>/podsítě/&lt;název podsítě>

1. **Obchodní podmínky**:  
    Zkontrolujte a přijměte právní podmínky.

1. Vyberte **Koupit**.

#### <a name="install-the-vm-agent-linux-only"></a>Instalace agenta virtuálního počítače (jenom Linux)

Chcete-li použít šablony popsané v předchozí části, musí být agent Linuxu již nainstalován v bitové kopii uživatele, jinak se nasazení nezdaří. Stáhněte a nainstalujte agenta virtuálního počítače do bitové kopie uživatele, jak je popsáno v [části Stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure][deployment-guide-4.4]. Pokud šablony nepoužíváte, můžete agenta virtuálního počítače nainstalovat i později.

#### <a name="join-a-domain-windows-only"></a>Připojení k doméně (pouze Windows)

Pokud je vaše nasazení Azure připojené k místní instanci služby Active Directory nebo DNS prostřednictvím připojení VPN azure site-to-site nebo Azure ExpressRoute *(to* se ve [virtuálních počítačích Azure říká plánování a implementace pro SAP NetWeaver][planning-guide]), očekává se, že se virtuální počítač připojuje k místní doméně. Další informace o aspektech tohoto kroku najdete [v tématu připojení virtuálního počítači k místní doméně (jenom windows).][deployment-guide-4.3]

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

V závislosti na konfiguraci místní sítě možná budete muset nastavit proxy server na virtuálním počítači. Pokud je váš virtuální počítač připojený k vaší místní síti přes VPN nebo ExpressRoute, virtuální počítač nemusí mít přístup k Internetu a nebude moct stáhnout požadovaná rozšíření virtuálního počítače nebo shromažďovat informace o infrastruktuře Azure pro agenta SAP Host prostřednictvím rozšíření SAP pro Azure, přečtěte si informace [o konfiguraci proxy][deployment-guide-configure-proxy]serveru .

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurace rozšíření virtuálních počítačů Azure pro SAP

Chcete-li si být jisti, že SAP podporuje vaše prostředí, nastavte rozšíření Azure pro SAP, jak je popsáno v [části Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5]. Zkontrolujte předpoklady pro SAP a požadované minimální verze jádra SAP a hostitele SAP Agent, v prostředcích uvedených v [sap zdrojů][deployment-guide-2.2].

#### <a name="sap-vm-extension-check"></a>Kontrola rozšíření virtuálního uživatele SAP

Zkontrolujte, zda rozšíření virtuálního uživatele pro SAP funguje, jak je popsáno v [části Kontroly a řešení potíží pro shromažďování dat od konce na konci pro agenta hostitele SAP][deployment-guide-troubleshooting-chapter].


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scénář 3: Přesunutí místního virtuálního počítače pomocí negeneralizovaného virtuálního pevného disku Azure se sapem

V tomto scénáři plánujete přesunout konkrétní systém SAP z místního prostředí do Azure. Můžete to provést nahráním virtuálního pevného disku, který má operační systém, binární soubory SAP a nakonec binární soubory DBMS, plus virtuální pevné disky s daty a soubory protokolu DBMS do Azure. Na rozdíl od scénáře popsaného ve [scénáři 2: Nasazení virtuálního počítače s vlastní image pro SAP][deployment-guide-3.3], v tomto případě zachovat název hostitele, SAP SID a SAP uživatelské účty v virtuálním počítači Azure, protože byly nakonfigurované v místním prostředí. Není nutné zobecnit operační ho. Tento scénář se nejčastěji vztahuje na scénáře mezi místními oblastmi, kde část prostředí SAP běží místně a část z ní běží v Azure.

V tomto scénáři agent virtuálního počítače **není** automaticky nainstalován během nasazení. Vzhledem k tomu, že agent virtuálního počítače a rozšíření Azure pro SAP jsou nutné ke spuštění SAP NetWeaver v Azure, je třeba stáhnout, nainstalovat a povolit obě součásti ručně po vytvoření virtuálního počítače.

Další informace o agentovi virtuálních počítačích Azure najdete v následujících prostředcích.

---
> ![Windows][Logo_Windows] Windows
>
> [Přehled agenta virtuálního počítače Azure][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Uživatelská příručka nástroje Azure Linux Agent][virtual-machines-linux-agent-user-guide]
>
>

---

Následující vývojový diagram ukazuje pořadí kroků pro přesunutí místního virtuálního počítače pomocí negeneralizovaného Azure VHD:

![Vývojový diagram nasazení virtuálních počítačů pro systémy SAP pomocí disku virtuálního počítače][deployment-guide-figure-400]

Pokud je disk už nahraný a definovaný v Azure (viz [Plánování a implementace virtuálních počítačů Azure pro SAP NetWeaver][planning-guide]), proveďte úkoly popsané v několika následujících částech.

#### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Chcete-li vytvořit nasazení pomocí privátního disku operačního systému prostřednictvím portálu Azure, použijte šablonu SAP publikovanou v [úložišti GitHub azure-quickstart-templates][azure-quickstart-templates-github]. Můžete také ručně vytvořit virtuální počítač pomocí Prostředí PowerShell.

* [**Šablona dvouvrstvé konfigurace (pouze jeden virtuální počítač)** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  Chcete-li vytvořit dvouvrstvý systém pomocí pouze jednoho virtuálního počítače, použijte tuto šablonu.
* [**Šablona dvouvrstvé konfigurace (pouze jeden virtuální počítač) – spravovaný disk** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Chcete-li vytvořit dvouvrstvý systém pomocí pouze jednoho virtuálního počítače a spravovaného disku, použijte tuto šablonu.

Na webu Azure zadejte pro šablonu následující parametry:

1. **Základy**:
   * **Předplatné**: Předplatné použít k nasazení šablony.
   * **Skupina prostředků**: Skupina prostředků, která má být použita k nasazení šablony. Můžete vytvořit novou skupinu prostředků nebo vybrat existující skupinu prostředků v předplatném.
   * **Umístění**: Kde nasadit šablonu. Pokud jste vybrali existující skupinu prostředků, použije se umístění této skupiny prostředků.
1. **Nastavení**:
   * **ID systému SAP:** ID systému SAP.
   * **Typ operačního systému**: Typ operačního systému, který chcete nasadit (Windows nebo Linux).
   * **Velikost systému SAP:** Velikost systému SAP.

     Počet SAPS nový systém poskytuje. Pokud si nejste jisti, kolik SAPS systém vyžaduje, zeptejte se svého technologického partnera SAP nebo systémového integrátora.
   * **Typ úložiště** (pouze dvouvrstvá šablona): Typ úložiště, které se má použít.

     Pro větší systémy důrazně doporučujeme používat Azure Premium Storage. Další informace o typech úložišť najdete v následujících zdrojích informací:
      * [Použití úložiště Azure Premium SSD pro instanci SAP DBMS][2367194]
      * [Struktura úložiště virtuálního počítače pro nasazení RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Úložiště Premium: Vysoce výkonné úložiště pro úlohy virtuálních strojů Azure][storage-premium-storage-preview-portal]
      * [Úvod do Microsoft Azure Storage][storage-introduction]
   * **Identifikátor URI pevného disku operačního systému** (pouze nespravovaná šablona&lt;disku): Identifikátor URI soukromého disku operačního systému, například https:// název_účtu>.blob.core.windows.net/vhds/osdisk.vhd.
   * **Id spravovaného disku operačního systému** (pouze šablona spravovaného disku): ID disku operačního systému spravovaného disku, /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **Nová nebo existující podsíť**: Určuje, zda je vytvořena nová virtuální síť a podsíť nebo zda se použije existující podsíť. Pokud už máte virtuální síť, která je připojená k místní síti, vyberte **Existující**.
   * **ID podsítě**: Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k které by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID&lt;obvykle vypadá takto: /subscriptions/ id předplatného&lt;>/resourceGroups/ název skupiny prostředků&lt;>/providers/Microsoft.Network/virtualNetworks/ název virtuální sítě>/podsítě/&lt;název podsítě>

1. **Obchodní podmínky**:  
    Zkontrolujte a přijměte právní podmínky.

1. Vyberte **Koupit**.

#### <a name="install-the-vm-agent"></a>Instalace agenta virtuálního soudu

Chcete-li použít šablony popsané v předchozí části, musí být agent virtuálního počítače nainstalován na disku operačního systému, jinak se nasazení nezdaří. Stáhněte a nainstalujte agenta virtuálního počítače do virtuálního počítače, jak je popsáno v [části Stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure][deployment-guide-4.4].

Pokud nepoužíváte šablony popsané v předchozí části, můžete také nainstalovat agenta virtuálního počítače později.

#### <a name="join-a-domain-windows-only"></a>Připojení k doméně (pouze Windows)

Pokud je vaše nasazení Azure připojené k místní instanci služby Active Directory nebo DNS prostřednictvím připojení VPN azure site-to-site nebo ExpressRoute *(to* se ve [virtuálních počítačích Azure][planning-guide]říká plánování a implementace pro SAP NetWeaver ), očekává se, že se virtuální počítač připojuje k místní doméně. Další informace o aspektech této úlohy najdete [v tématu připojení virtuálního počítače k místní doméně (jenom windows).][deployment-guide-4.3]

#### <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

V závislosti na konfiguraci místní sítě možná budete muset nastavit proxy server na virtuálním počítači. Pokud je váš virtuální počítač připojený k vaší místní síti přes VPN nebo ExpressRoute, virtuální počítač nemusí mít přístup k Internetu a nebude moct stáhnout požadovaná rozšíření virtuálního počítače nebo shromažďovat informace o infrastruktuře Azure pro agenta SAP Host prostřednictvím rozšíření SAP pro Azure, přečtěte si informace [o konfiguraci proxy][deployment-guide-configure-proxy]serveru .

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurace rozšíření virtuálních počítačů Azure pro SAP

Chcete-li si být jisti, že SAP podporuje vaše prostředí, nastavte rozšíření Azure pro SAP, jak je popsáno v [části Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5]. Zkontrolujte předpoklady pro SAP a požadované minimální verze jádra SAP a hostitele SAP Agent, v prostředcích uvedených v [sap zdrojů][deployment-guide-2.2].

#### <a name="sap-vm-check"></a>Kontrola virtuálního uživatele SAP

Zkontrolujte, jestli rozšíření virtuálního uživatele pro SAP funguje, jak je popsáno v [části Kontroly a řešení potíží pro shromažďování dat od konce pro hostitele SAP][deployment-guide-troubleshooting-chapter].

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Aktualizace konfigurace rozšíření Azure pro SAP

Aktualizujte konfiguraci rozšíření Azure pro SAP v některém z následujících scénářů:
* Společný tým Microsoft/SAP rozšiřuje možnosti rozšíření virtuálního počítače a požaduje více nebo méně čítačů.
* Microsoft zavádí novou verzi základní infrastruktury Azure, která poskytuje data, a rozšíření Azure pro SAP musí být přizpůsobeno těmto změnám.
* Připojíte další datové disky k virtuálnímu počítači Azure nebo odeberete datový disk. V tomto scénáři aktualizujte shromažďování dat souvisejících s úložištěm. Změna konfigurace přidáním nebo odstraněním koncových bodů nebo přiřazením IP adres virtuálnímu počítači nemá vliv na konfiguraci rozšíření.
* Změníte velikost virtuálního počítače Azure, například z velikosti A5 na jakoukoli jinou velikost virtuálního počítače.
* Do virtuálního počítače Azure přidáte nová síťová rozhraní.

Chcete-li aktualizovat nastavení, aktualizujte konfiguraci rozšíření Azure pro SAP podle kroků v [části Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Podrobné úkoly pro nasazení softwaru SAP

Tato část obsahuje podrobné kroky pro provedení konkrétních úkolů v procesu konfigurace a nasazení.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Nasazení rutin prostředí Azure PowerShell

1. Přejděte na [obsah Ke stažení v Microsoft Azure](https://azure.microsoft.com/downloads/).
1. V části **Nástroje příkazového řádku**vyberte v části **PowerShell** **instalaci systému Windows**.
1. V dialogovém okně Microsoft Download Manager vyberte pro stažený soubor (například WindowsAzurePowershellGet.3f.3f.3fnew.exe) **možnost Spustit**.
1. Chcete-li spustit Instalační službu webové platformy Společnosti Microsoft (Microsoft Web PI), vyberte **možnost Ano**.
1. Zobrazí se stránka, která vypadá takto:

   ![Stránka instalace pro rutiny Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Vyberte **Instalovat**a přijměte licenční podmínky pro software společnosti Microsoft.
1. Prostředí PowerShell je nainstalováno. Chcete-li průvodce instalací zavřít, vyberte **dokončit.**

Často kontrolujte aktualizace rutin prostředí PowerShell, které se obvykle aktualizují měsíčně. Nejjednodušší způsob, jak zkontrolovat aktualizace, je provést předchozí kroky instalace až po stránku instalace zobrazenou v kroku 5. Datum vydání a číslo vydání rutin jsou uvedeny na stránce uvedené v kroku 5. Pokud není uvedeno jinak v SAP Note [1928533] nebo SAP Note [2015553], doporučujeme pracovat s nejnovější verzí rutin Azure PowerShell.

Chcete-li zkontrolovat verzi rutin Prostředí Azure PowerShell, které jsou nainstalované v počítači, spusťte tento příkaz prostředí PowerShell:

```powershell
(Get-Module Az.Compute).Version
```

Výsledek vypadá takto:

![Výsledek kontroly verze rutiny prostředí Azure PowerShell][deployment-guide-figure-600]
<a name="figure-6"></a>

Pokud je aktuální verze rutiny Azure nainstalovaná v počítači, první stránka průvodce instalací ji označí přidáním **(nainstalováno)** k názvu produktu (viz následující snímek obrazovky). Vaše rutiny PowerShell Azure jsou aktuální. Průvodce instalací zavřete vyberte **možnost Ukončit**.

![Stránka instalace pro rutiny Prostředí Azure PowerShell označující, že je nainstalovaná nejnovější verze rutin Prostředí Azure PowerShell][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Nasazení azure cli

1. Přejděte na [obsah Ke stažení v Microsoft Azure](https://azure.microsoft.com/downloads/).
1. V části **Nástroje příkazového řádku**vyberte v části **Rozhraní příkazového řádku Azure**odkaz **Instalovat** pro svůj operační systém.
1. V dialogovém okně Microsoft Download Manager vyberte pro stažený soubor (například WindowsAzureXPlatCLI.3f.3f.3fnew.exe) **možnost Spustit**.
1. Chcete-li spustit Instalační službu webové platformy Společnosti Microsoft (Microsoft Web PI), vyberte **možnost Ano**.
1. Zobrazí se stránka, která vypadá takto:

   ![Stránka instalace pro rutiny Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Vyberte **Instalovat**a přijměte licenční podmínky pro software společnosti Microsoft.
1. Azure CLI je nainstalován. Chcete-li průvodce instalací zavřít, vyberte **dokončit.**

Často kontrolujte aktualizace příkazového příkazového příkazu Azure, které se obvykle aktualizují měsíčně. Nejjednodušší způsob, jak zkontrolovat aktualizace, je provést předchozí kroky instalace až po stránku instalace zobrazenou v kroku 5.

Chcete-li zkontrolovat verzi příkazového příkazu Azure, která je nainstalovaná v počítači, spusťte tento příkaz:

```console
azure --version
```

Výsledek vypadá takto:

![Výsledek kontroly verze nastavení příkazu Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Připojení virtuálního počítače k místní doméně (jenom Windows)

Pokud nasadíte virtuální počítače SAP v mezimístním scénáři, kde se v Azure rozšiřují místní služby Active Directory a DNS, očekává se, že se virtuální počítače připojují k místní doméně. Podrobné kroky, které provedete pro připojení virtuálního počítače k místní doméně, a další software, který musí být členem místní domény, se liší podle zákazníka. Chcete-li připojit virtuální počítač k místní doméně, je obvykle nutné nainstalovat další software, jako je antimalwarový software a software pro zálohování nebo monitorování.

V tomto scénáři je také nutné se ujistit, že pokud nastavení proxy Internetu jsou vynuceny, když virtuální počítač připojí k doméně ve vašem prostředí, windows místní systémový účet (S-1-5-18) v hostovaném virtuálním počítači má stejné nastavení proxy. Nejjednodušší možností je vynutit proxy server pomocí zásad skupiny domény, která se vztahuje na systémy v doméně.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Stažení, instalace a povolení agenta virtuálních počítačů Azure

Pro virtuální počítače, které jsou nasazeny z bitové kopie operačního systému, která není generalizovaná (například bitová kopie, která nepochází z nástroje Příprava systému Windows nebo nástroj sysprep), je třeba ručně stáhnout, nainstalovat a povolit agenta virtuálního počítače Azure.

Pokud nasadíte virtuální počítač z Azure Marketplace, tento krok není povinný. Image z Azure Marketplace už agenta virtuálního počítače Azure už mají.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Stáhněte si agenta virtuálního počítače Azure:
   1.  Stáhněte si [instalační balíček agenta virtuálního počítače Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Uložte balíček Agent VM MSI místně na osobní počítač nebo server.
1. Nainstalujte agenta virtuálního počítače Azure:
   1.  Připojte se k nasazenému virtuálnímu počítači Azure pomocí protokolu RDP (Remote Desktop Protocol).
   1.  Otevřete okno Průzkumníka Windows na virtuálním počítači a vyberte cílový adresář pro soubor MSI agenta virtuálního počítače.
   1.  Přetáhněte soubor MSI agenta virtuálního počítače Azure z místního počítače nebo serveru do cílového adresáře agenta virtuálního počítače na virtuálním počítači.
   1.  Poklepejte na soubor MSI na virtuálním počítači.
1. U virtuálních počítačů, které jsou připojeny k místním doménám, se ujistěte, že případné nastavení proxy internetu platí také pro účet místního systému Windows (S-1-5-18) ve virtuálním počítači, jak je popsáno v [části Konfigurace proxy][deployment-guide-configure-proxy]serveru . Agent virtuálního počítače běží v tomto kontextu a potřebuje se k připojení k Azure.

K aktualizaci agenta virtuálního počítače Azure není nutná žádná interakce s uživatelem. Agent virtuálního počítače se automaticky aktualizuje a nevyžaduje restartování virtuálního počítače.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

K instalaci agenta virtuálního počítače pro Linux použijte následující příkazy:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) nebo Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Pokud je agent už nainstalovaný, chcete-li aktualizovat agenta Azure Linux, proveďte kroky popsané v [části Aktualizace agenta Azure Linux u virtuálního počítače na nejnovější verzi z GitHubu][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurace serveru proxy

Kroky, které provedete ke konfiguraci proxy v systému Windows se liší od způsobu konfigurace proxy v Systému Linux.

#### <a name="windows"></a>Windows

Chcete-li získat přístup k Internetu, musí být nastavení serveru proxy správně nastaveno. Pokud nastavení serveru proxy není nastaveno zásadami skupiny, můžete nastavení pro účet místního systému nakonfigurovat.

1. Přejděte na **úvodní obrazovku**, zadejte **soubor gpedit.msc**a vyberte **možnost Enter**.
1. Vyberte možnost**Šablony pro správu** >  **konfigurace** > počítače**Součásti** > systému Windows Internet**Explorer**. Ujistěte se, že nastavení **Nastavit nastavení proxy serveru pro jednotlivé počítače (nikoli na uživatele)** je zakázáno nebo není nakonfigurováno.
1. V **Ovládacích panelech**přejděte na**internetovou možnosti** **Centra** > síťových připojení a sdílení .
1. Na kartě **Připojení** vyberte tlačítko **nastavení sítě LAN.**
1. Zrušte zaškrtnutí políčka **Automaticky zjišťovat nastavení**.
1. Zaškrtněte **políčko Použít proxy server pro síť LAN** a zadejte adresu proxy a port.
1. Vyberte tlačítko **Upřesnit.**
1. Do pole **Výjimky** zadejte IP adresu **168.63.129.16**. Vyberte **OK**.

#### <a name="linux"></a>Linux

Nakonfigurujte správný proxy server v konfiguračním \\\\souboru agenta hosta Microsoft Azure, který se nachází na adrese etc waagent.conf.

Nastavte následující parametry:

1. **Hostitel serveru proxy HTTP**. Nastavte ji například na **proxy.corp.local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Http proxy port**. Například nastavte ji na **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Restartujte agenta.

   ```console
   sudo service waagent restart
   ```

Nastavení proxy \\v\\etc waagent.conf platí také pro požadovaná rozšíření virtuálního počítače. Pokud chcete používat úložiště Azure, ujistěte se, že provoz do těchto úložišť neprochází prostřednictvím místního intranetu. Pokud jste vytvořili uživatelem definované trasy pro umožnění vynuceného tunelového propojení, nezapomeňte přidat trasu, která směruje provoz do úložišť přímo do Internetu, a nikoli prostřednictvím připojení VPN mezi lokalitami.

* **SLES**

  Je také nutné přidat trasy pro IP \\adresy\\uvedené v etc regionserverclnt.cfg. Následující obrázek ukazuje příklad:

  ![Vynucené tunelování][deployment-guide-figure-50]


* **RHEL**

  Musíte také přidat trasy pro IP adresy hostitelů \\uvedených v etc\\yum.repos.d\\rhui-load-balancers. Příklad naleznete na předchozím obrázku.

* **Oracle Linux**

  Oracle Linux v Azure nemá žádné repozitáře. Musíte nakonfigurovat vlastní repozitáře pro Oracle Linux nebo použít veřejné repozitáře.

Další informace o uživatelem definovaných trasách naleznete v [tématu Uživatelem definované trasy a předávání IP][virtual-networks-udr-overview]adres .

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Konfigurace rozšíření Azure pro SAP

Když jste připravili virtuální počítač, jak je popsáno ve [scénářích nasazení virtuálních počítače pro SAP v Azure][deployment-guide-3], agent virtuálních zařízení Azure se nainstaluje na virtuálním počítači. Dalším krokem je nasazení rozšíření Azure pro SAP, které je dostupné v úložišti rozšíření Azure v globálních datových centrech Azure. Další informace najdete v tématu [plánování a implementace virtuálních počítačů Azure pro SAP NetWeaver][planning-guide-9.1].

K instalaci a konfiguraci rozšíření Azure pro SAP můžete použít PowerShell nebo Azure CLI. Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows nebo Linux pomocí počítače s Windows, přečtěte si informace o [Azure PowerShellu][deployment-guide-4.5.1]. Pokud chcete nainstalovat rozšíření na virtuální počítač s Linuxem pomocí linuxové plochy, přečtěte si informace o [nastavení příkazového příkazu k Azure][deployment-guide-4.5.2].

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell pro Linux a virtuální počítače s Windows

Instalace rozšíření Azure pro SAP pomocí PowerShellu:

1. Ujistěte se, že jste nainstalovali nejnovější verzi rutiny Prostředí Azure PowerShell. Další informace najdete [v tématu Nasazení rutin prostředí Azure PowerShell][deployment-guide-4.1].  
1. Spusťte následující rutinu Azure PowerShellu.
    Seznam dostupných prostředí zobrazíte `commandlet Get-AzEnvironment`v seznamu . Pokud chcete používat globální Azure, vaše prostředí je **AzureCloud**. Pro Azure v Číně vyberte **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Po zadání dat účtu a identifikaci virtuálního počítače Azure skript nasadí požadovaná rozšíření a povolí požadované funkce. Může to trvat několik minut.
Další informace `Set-AzVMAEMExtension`o tématu [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Úspěšné spuštění rutiny Azure specifické pro SAP Set-AzVMAEMExtension][deployment-guide-figure-900]

Konfigurace `Set-AzVMAEMExtension` provede všechny kroky konfigurace shromažďování dat hostitele pro SAP.

Výstup skriptu obsahuje následující informace:

* Potvrzení, že sběr dat pro disk operačního systému a všechny další datové disky byl nakonfigurován.
* Další dvě zprávy potvrzují konfiguraci metrik úložiště pro konkrétní účet úložiště.
* Jeden řádek výstupu udává stav skutečné aktualizace rozšíření virtuálního počítače pro konfiguraci SAP.
* Další řádek výstupu potvrzuje, že konfigurace byla nasazena nebo aktualizována.
* Poslední řádek výstupu je informační. Zobrazuje možnosti testování rozšíření virtuálního počítače pro konfiguraci SAP.
* Chcete-li zkontrolovat, zda byly úspěšně provedeny všechny kroky rozšíření virtuálního počítače Azure pro konfiguraci SAP a zda infrastruktura Azure poskytuje potřebná data, pokračujte v kontrole připravenosti pro rozšíření Azure pro SAP, jak je popsáno v [kontrole připravenosti pro rozšíření Azure pro SAP][deployment-guide-5.1].
* Počkejte 15–30 minut, než diagnostika Azure shromáždí příslušná data.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI pro virtuální počítače s Linuxem

Instalace rozšíření Azure pro SAP pomocí azure cli:

   1. Nainstalujte klasické příkazové příkazové příkazové příkazové příkazy Azure, jak je popsáno v [části Instalace klasického příkazového příkazového příkazového příkazu k příkazu Azure][azure-cli].
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

1. Instalace pomocí Azure CLI 2.0

   1. Nainstalujte Azure CLI 2.0, jak je popsáno v [instalaci Azure CLI 2.0][azure-cli-2].
   1. Přihlaste se pomocí svého účtu Azure:

      ```azurecli
      az login
      ```

   1. Instalace rozšíření Azure CLI AEM
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Nainstalujte rozšíření pomocí
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Ověřte, že rozšíření Azure pro SAP je aktivní na virtuálním počítači Azure Linux. Zkontrolujte, \\zda\\\\existuje soubor\\var lib AzureEnhancedMonitor PerfCounters. Pokud existuje, spusťte na příkazovém řádku tento příkaz a zobrazte informace shromážděné rozšířením Azure pro SAP:

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

## <a name="checks-and-troubleshooting-for-end-to-end-data-collection-for-sap-host-agent"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Zkontroluje a řeší komplexní sběr dat pro hostitelského agenta SAP.

Po nasazení virtuálního počítače Azure a nastavení příslušného rozšíření Azure pro SAP zkontrolujte, jestli všechny součásti rozšíření fungují podle očekávání.

Spusťte kontrolu připravenosti pro rozšíření Azure pro SAP, jak je popsáno v [kontrole připravenosti pro rozšíření Azure pro SAP][deployment-guide-5.1]. Pokud jsou všechny výsledky kontroly připravenosti kladné a všechny příslušné čítače výkonu se zobrazí OK, rozšíření Azure pro SAP byla úspěšně nastavena. Můžete pokračovat v instalaci sap host agenta, jak je popsáno v poznámkách SAP v [sap zdrojů][deployment-guide-2.2]. Pokud kontrola připravenosti označuje, že čítače chybí, spusťte kontrolu stavu rozšíření Azure pro SAP, jak je popsáno v [kontrole stavu pro azure rozšíření pro konfiguraci SAP][deployment-guide-5.2]. Další možnosti řešení potíží najdete [v tématu Poradce při potížích s rozšířením Azure pro SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Kontrola připravenosti pro rozšíření Azure pro SAP

Tato kontrola zajišťuje, že všechny metriky výkonu, které se zobrazují uvnitř vaší aplikace SAP, jsou poskytovány základní rozšíření Azure pro SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Spuštění kontroly připravenosti na virtuálním počítači se systémem Windows

1. Přihlaste se k virtuálnímu počítači Azure (pomocí účtu správce není nutné).
1. Otevřete okno příkazového řádku.
1. Na příkazovém řádku změňte adresář do instalační složky rozšíření\\\\Azure\\pro SAP: C: Balíčky Pluginy\\&lt;Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler verze>\\drop

   *Verze* v cestě k rozšíření se může lišit. Pokud se v instalační složce zobrazí složky pro více verzí rozšíření, zkontrolujte konfiguraci služby AzureEnhancedMonitoring Windows a přepněte do složky označené jako *Cesta ke spustitelnému souboru*.

   ![Vlastnosti služby se spuštěnou rozšířením Azure pro SAP][deployment-guide-figure-1000]

1. Na příkazovém řádku spusťte **azperflib.exe** bez jakýchkoli parametrů.

   > [!NOTE]
   > Azperflib.exe běží ve smyčce a aktualizuje shromážděné čítače každých 60 sekund. Chcete-li smyčku ukončit, zavřete okno příkazového řádku.
   >
   >

Pokud není nainstalováno rozšíření Azure pro SAP nebo služba AzureEnhancedMonitoring není spuštěná, rozšíření nebylo správně nakonfigurované. Podrobné informace o nasazení rozšíření najdete v [tématu Poradce při potížích s rozšířením Azure pro SAP][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe je součást, kterou nelze použít pro vlastní účely. Jedná se o součást, která poskytuje data infrastruktury Azure související s virtuálním počítačem pro hostitele SAP agenta výhradně.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Zkontrolujte výstup azperflib.exe

Výstup Azperflib.exe zobrazuje všechny počítadla výkonu Azure pro SAP. V dolní části seznamu shromážděných čítačů zobrazuje souhrnný a indikátor stavu stav rozšíření Azure pro SAP.

![Výstup kontroly stavu provedením azperflib.exe, což znamená, že neexistují žádné problémy][deployment-guide-figure-1100]
<a name="figure-11"></a>

Zkontrolujte výsledek vrácený pro celkový výstup **čítačů,** který je vykázán jako prázdný, a stav **stav**, který je uveden na předchozím obrázku.

Výsledné hodnoty interpretujte následujícím způsobem:

| Hodnoty výsledků Azperflib.exe | Rozšíření Azure pro stav SAP |
| --- | --- |
| **Volání rozhraní API – není k dispozici** | Čítače, které nejsou k dispozici, nemusí být použitelné pro konfiguraci virtuálního počítače nebo jsou chyby. Viz **Stavový stav**. |
| **Čítače celkem - prázdné** |Následující dva čítače úložiště Azure může být prázdné: <ul><li>Server latence op latence čtení úložiště msec</li><li>Latence op čtení úložiště E2E msec</li></ul>Všechny ostatní čítače musí mít hodnoty. |
| **Zdravotní stav** |Pouze OK, pokud stav vrácení zobrazuje **OK**. |
| **Diagnostika** |Podrobné informace o zdravotním stavu. |

Pokud hodnota **stavu** není **ok**, postupujte podle pokynů v kontrole stavu pro rozšíření Azure [pro konfiguraci SAP][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Spuštění kontroly připravenosti na virtuálním počítači s Linuxem

1. Připojte se k virtuálnímu počítači Azure pomocí SSH.

1. Zkontrolujte výstup rozšíření Azure pro SAP.

   a.  Spusťte `more /var/lib/AzureEnhancedMonitor/PerfCounters`.

   **Očekávaný výsledek**: Vrátí seznam čítačů výkonu. Soubor by neměl být prázdný.

   b. Spusťte `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`.

   **Očekávaný výsledek**: Vrátí jeden řádek, kde chyba není **žádná**, například **3;config; Chyba;;0;0; žádný;0;1456416792;tst-servercs;**

   c. Spusťte `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`.

   **Očekávaný výsledek**: Vrátí jako prázdný nebo neexistuje.

Pokud předchozí kontrola nebyla úspěšná, spusťte tyto další kontroly:

1. Ujistěte se, že je waagent nainstalován a povolen.

   a.  Spusťte `sudo ls -al /var/lib/waagent/`.

     **Očekávaný výsledek**: Zobrazí obsah adresáře waagent.

   b.  Spusťte `ps -ax | grep waagent`.

   **Očekávaný výsledek**: Zobrazí jednu položku podobnou:`python /usr/sbin/waagent -daemon`

1. Ujistěte se, že rozšíření Azure pro SAP je nainstalované a spuštěné.

   a.  Spusťte `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`.

   **Očekávaný výsledek**: Zobrazí seznam obsahu adresáře Rozšíření Azure pro SAP.

   b. Spusťte `ps -ax | grep AzureEnhanced`.

   **Očekávaný výsledek**: Zobrazí jednu položku podobnou:`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Nainstalujte hostitele SAP, jak je popsáno v poznámce SAP [1031096], a zkontrolujte výstup . `saposcol`

   a.  Spusťte `/usr/sap/hostctrl/exe/saposcol -d`.

   b.  Spusťte `dump ccm`.

   c.  Zkontrolujte, zda je hodnota **Virtualization_Configuration\Rozšířené monitorování přístupu** **true**.

Pokud již máte nainstalovaný aplikační server SAP NetWeaver ABAP, otevřete transakci ST06 a zkontrolujte, zda je povoleno rozšířené monitorování.

Pokud se některá z těchto kontrol nezdaří a podrobné informace o tom, jak znovu nasadit rozšíření, najdete [v tématu řešení potíží s rozšířením Azure pro SAP][deployment-guide-5.3].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Kontrola stavu pro konfiguraci rozšíření Azure pro SAP

Pokud některá data infrastruktury nejsou doručována správně, jak je uvedeno v testu popsaném v [kontrole připravenosti pro rozšíření Azure pro SAP][deployment-guide-5.1], spusťte rutinu `Test-AzVMAEMExtension` a zkontrolujte, jestli jsou infrastruktura Azure a rozšíření Azure pro SAP správně nakonfigurované.

1. Ujistěte se, že jste nainstalovali nejnovější verzi rutiny Azure PowerShell, jak je popsáno v [nasazení rutin Azure PowerShell][deployment-guide-4.1].
1. Spusťte následující rutinu Azure PowerShellu. Seznam dostupných prostředí zobrazíte spuštěním `Get-AzEnvironment`rutiny . Pokud chcete používat globální Azure, vyberte prostředí **AzureCloud.** Pro Azure v Číně vyberte **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Zadejte data svého účtu a identifikujte virtuální počítač Azure.

   ![Vstupní stránka rutiny Azure specifické pro SAP test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. Skript testuje konfiguraci vybraného virtuálního počítače.

   ![Výstup úspěšného testu rozšíření Azure pro SAP][deployment-guide-figure-1300]

Ujistěte se, že každý výsledek kontroly stavu je **v pořádku**. Pokud některé kontroly nezobrazují **OK**, spusťte rutinu aktualizace, jak je popsáno v [konfiguraci rozšíření Azure pro SAP][deployment-guide-4.5]. Počkejte 15 minut a opakujte kontroly popsané v [kontrole připravenosti pro rozšíření Azure pro SAP][deployment-guide-5.1] a kontrolu stavu pro [konfiguraci rozšíření Azure pro konfiguraci SAP][deployment-guide-5.2]. Pokud kontroly stále označují problém s některými nebo všemi čítači, [přečtěte si článek Řešení potíží s rozšířením Azure pro SAP][deployment-guide-5.3].

> [!Note]
> Některá upozornění můžete zaznamenat v případech, kdy používáte spravované standardní disky Azure. Místo testů, které vrátí "OK", se zobrazí upozornění. To je normální a určené v případě tohoto typu disku. Viz taky [článek Řešení potíží s rozšířením Azure pro SAP][deployment-guide-5.3]
> 

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Poradce při potížích s rozšířením Azure pro SAP

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Čítače výkonu Azure se vůbec nezobrazují

Služba AzureEnhancedMonitoring Windows shromažďuje metriky výkonu v Azure. Pokud služba nebyla správně nainstalována nebo pokud není spuštěna ve vašem virtuálním počítači, nelze shromažďovat žádné metriky výkonu.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Instalační adresář rozšíření Azure pro SAP je prázdný

###### <a name="issue"></a>Problém

Instalační adresář\\C:\\Balíčky\\Plugins Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;verze>\\drop je prázdný.

###### <a name="solution"></a>Řešení

Rozšíření není nainstalováno. Určete, zda se jedná o problém proxy (jak je popsáno výše). Možná budete muset restartovat počítač `Set-AzVMAEMExtension` nebo znovu spustit konfigurační skript.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Služba pro rozšíření Azure pro SAP neexistuje.

###### <a name="issue"></a>Problém

Služba AzureEnhancedMonitoring Windows neexistuje.

Azperflib.exe výstup vyvolá chybu:

![Spuštění azperflib.exe označuje, že služba rozšíření Azure pro SAP není spuštěna][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Řešení

Pokud služba neexistuje, rozšíření Azure pro SAP nebyla správně nainstalována. Rozšíření znovu nasadit pomocí kroků popsaných pro váš scénář nasazení ve [scénářích nasazení virtuálních počítačů pro SAP v Azure][deployment-guide-3].

Po nasazení rozšíření, po jedné hodině, zkontrolujte znovu, zda čítače výkonu Azure jsou k dispozici v virtuálním počítači Azure.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Služba pro rozšíření Azure pro SAP existuje, ale nepodaří se spustit

###### <a name="issue"></a>Problém

Služba AzureEnhancedMonitoring Windows existuje a je povolena, ale nedaří se spustit. Další informace naleznete v protokolu událostí aplikace.

###### <a name="solution"></a>Řešení

Konfigurace je nesprávná. Restartujte rozšíření Azure pro SAP ve virtuálním počítači, jak je popsáno v [části Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5].

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Některé čítače výkonu Azure chybí

Služba AzureEnhancedMonitoring Windows shromažďuje metriky výkonu v Azure. Služba získává data z několika zdrojů. Některá konfigurační data se shromažďují místně a některé metriky výkonu se čtou z Diagnostiky Azure. Čítače úložiště se používají z vašeho přihlášení na úrovni předplatného úložiště.

Pokud řešení potíží pomocí sap note [1999351] problém nevyřeší, spusťte `Set-AzVMAEMExtension` konfigurační skript. Možná budete muset počkat hodinu, protože aktualizace analýzy úložiště nebo diagnostické čítače nemusí být vytvořeny ihned po jejich povolení. Pokud problém přetrvává, otevřete zprávu zákaznické podpory SAP na komponentě BC-OP-NT-AZR pro Windows nebo BC-OP-LNX-AZR pro virtuální počítač s Linuxem.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Čítače výkonu Azure se vůbec nezobrazují

Metriky výkonu v Azure shromažďuje daemon. Pokud daemon není spuštěn, nelze shromažďovat žádné metriky výkonu.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Instalační adresář rozšíření Azure pro SAP je prázdný

###### <a name="issue"></a>Problém

Adresář \\var\\\\lib\\ waagent nemá podadresář pro rozšíření Azure pro SAP.

###### <a name="solution"></a>Řešení

Rozšíření není nainstalováno. Určete, zda se jedná o problém proxy (jak je popsáno výše). Možná budete muset restartovat počítač nebo `Set-AzVMAEMExtension` znovu spustit konfigurační skript.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Spuštění rozšíření Set-AzVMAEMExtension a Test-AzVMAEMExtension zobrazuje varovné zprávy oznamující, že standardní spravované disky nejsou podporovány

###### <a name="issue"></a>Problém

Při provádění Set-AzVMAEMExtension nebo Test-AzVMAEMExtension zprávy, jako jsou tyto jsou zobrazeny:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Provádění azperfli.exe, jak je popsáno výše, můžete získat výsledek, který označuje stav nezdravé. 

###### <a name="solution"></a>Řešení

Zprávy jsou způsobeny skutečností, že standardní spravované disky nedoručují api používaná rozšířením SAP pro SAP ke kontrole statistik standardních účtů úložiště Azure. To není otázka zájmu. Důvodem pro zavedení shromažďování dat pro účty úložiště disků bylo omezení vstupně-va, ke kterým často došlo. Spravované disky zabrání takovému omezení omezením tím, že omezí počet disků v účtu úložiště. Proto není mít tento typ těchto dat není důležité.


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Některé čítače výkonu Azure chybí

Metriky výkonu v Azure shromažďuje daemon, který získává data z několika zdrojů. Některá konfigurační data se shromažďují místně a některé metriky výkonu se čtou z Diagnostiky Azure. Čítače úložiště pocházejí z protokolů v předplatném úložiště.

Úplný a aktuální seznam známých problémů najdete v tématu SAP Note [1999351], který obsahuje další informace o řešení potíží pro rozšíření Azure pro SAP.

Pokud řešení potíží pomocí SAP Note [1999351] problém nevyřeší, znovu `Set-AzVMAEMExtension` spusťte konfigurační skript, jak je popsáno v [tématu Konfigurace rozšíření Azure pro SAP][deployment-guide-4.5]. Možná budete muset počkat hodinu, protože aktualizace analýzy úložiště nebo diagnostické čítače nemusí být vytvořeny ihned po jejich povolení. Pokud problém přetrvává, otevřete zprávu zákaznické podpory SAP na komponentě BC-OP-NT-AZR pro Windows nebo BC-OP-LNX-AZR pro virtuální počítač s Linuxem.
