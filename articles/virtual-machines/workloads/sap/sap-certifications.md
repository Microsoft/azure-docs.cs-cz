---
title: Microsoft Azure certifikace pro SAP | Microsoft Docs
description: Aktualizovaný seznam aktuálních konfigurací a certifikace SAP na platformě Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 43fc2e9369b9d11b1ac0205beddea01b633fb633
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598370"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certifikace a konfigurace SAP běžící na Microsoft Azure

SAP a Microsoft mají dlouhou historii spolupráce ve silném partnerství, které má pro zákazníky vzájemné výhody. Microsoft průběžně aktualizuje svou platformu a odešle nové podrobnosti o certifikaci do SAP, aby se zajistilo, že Microsoft Azure je nejlepší platformou, na které se budou spouštět úlohy SAP. Následující tabulky popisují podporované konfigurace a seznam rostoucích certifikátů SAP v Azure. 

## <a name="sap-hana-certifications"></a>SAP HANA certifikace
Odkazy

- [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pro SAP HANA podporu nativních virtuálních počítačů Azure a velkých instancí Hana.

| Produkt SAP | Podporovaný operační systém | Nabídky Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (včetně klientského softwaru HANA, který se skládá jenom z ovladače SQLDBC, ODBO-Windows, ovladače ODBC, JDBC, HANA Studio a HANA Database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Řada virtuálních počítačů řady D-Series |
| Podnikání One v HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Řízená dostupnost pro GS5. Plná podpora pro M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2 M416ms_v2 <br /> SAP HANA v Azure (velké instance) [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite na HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2 SAP HANA v Azure (velké instance) [SAP HANA certifikovaných platforem IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise pro ČERNOBÍLé zpracování, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2 SAP HANA v Azure (velké instance) [SAP HANA certifikovaných platforem IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2 SAP HANA v Azure (velké instance) <br /> [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Uvědomte si, že SAP používá termín "clusteringu" v [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) jako synonymum pro "škálování na více systémů" a není pro clustering s vysokou dostupností.

## <a name="sap-netweaver-certifications"></a>Certifikace SAP NetWeaver
Microsoft Azure je certifikováno pro následující produkty SAP s plnou podporou od společnosti Microsoft a SAP.
Odkazy

- [1928533 – aplikace SAP v Azure: podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533) pro všechny aplikace založené na SAP NetWeaver, včetně SAP TReX, SAP LIVECACHE a SAP Content serveru. A všechny databáze kromě SAP HANA.


| Produkt SAP | Operační systém hosta | RDBMS | Typy virtuálních počítačů |
| --- | --- | --- | --- |
| Software SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (jenom Windows a Oracle Linux), DB2, pomocného programu SAP |A5 na A11, D11 na D14, DS11 na DS14, DS11_v2 DS15_v2, GS1 na GS5, D2s_v3 D64s_v3 D2as_v4 D64as_v4 E2s_v3 E64s_v3 E2as_v4 E64as_v4 M208s_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (jenom Windows a Oracle Linux), DB2, pomocného programu SAP |A5 na A11, D11 na D14, DS11 na DS14, DS11_v2 DS15_v2, GS1 na GS5, D2s_v3 D64s_v3 D2as_v4 D64as_v4 E2s_v3 E64s_v3 E2as_v4 E64as_v4 M208s_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP BusinessObjects BI | Windows |NEUŽÍVÁ SE. |A5 na A11, D11 na D14, DS11 na DS14, DS11_v2 DS15_v2, GS1 na GS5, D2s_v3 D64s_v3 D2as_v4 D64as_v4 E2s_v3 E64s_v3 E2as_v4 E64as_v4 M208s_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (jenom Windows a Oracle Linux), DB2, pomocného programu SAP |A5 na A11, D11 na D14, DS11 na DS14, DS11_v2 DS15_v2, GS1 na GS5, D2s_v3 D64s_v3 D2as_v4 D64as_v4 E2s_v3 E64s_v3 E2as_v4 E64as_v4 M208s_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, , M208ms_v2, M416s_v2, M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Další úlohy SAP podporované v Azure

| Produkt SAP | Operační systém hosta | RDBMS | Typy virtuálních počítačů |
| --- | --- | --- | --- |
| SAP Business One na SQL Server | Windows  | Server SQL | Všechny typy virtuálních počítačů s certifikací NetWeaver<br /> [Poznámka ke SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BITŮ 10,01 MS SP08 | Windows a Linux | | Všechny typy virtuálních počítačů s certifikací NetWeaver<br /> Poznámka ke SAP #2451795 |
| Platforma SAP obchodních objektů BI | Windows a Linux | | Poznámka ke SAP #2145537 |
| SAP Data Services 4,2 | | | Poznámka ke SAP #2288344 |
| SAP Hybris pro obchod s platformou 5. x a 6. x | Windows | SQL Server, Oracle | Všechny typy virtuálních počítačů s certifikací NetWeaver<br /> [Wiki Hybris](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
