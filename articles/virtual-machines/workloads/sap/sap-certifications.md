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
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: f78d5f839c5dd7816ae28eeed175503427abb43c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87035838"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certifikace a konfigurace SAP běžící na Microsoft Azure

SAP a Microsoft mají dlouhou historii spolupráce ve silném partnerství, které má pro zákazníky vzájemné výhody. Microsoft průběžně aktualizuje svou platformu a odešle nové podrobnosti o certifikaci do SAP, aby se zajistilo, že Microsoft Azure je nejlepší platformou, na které se budou spouštět úlohy SAP. Následující tabulky popisují podporované konfigurace a seznam rostoucích certifikátů SAP v Azure. Tento seznam obsahuje seznam přehledů, které se můžou odchýlit od oficiálních seznamů SAP. Podrobné informace najdete v článku o [tom, jaký software SAP se pro nasazení Azure podporuje](./sap-supported-product-on-azure.md) .

## <a name="sap-hana-certifications"></a>SAP HANA certifikace
Odkazy

- [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pro SAP HANA podporu nativních virtuálních počítačů Azure a velkých instancí Hana.

| Produkt SAP | Podporovaný operační systém | Nabídky Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (včetně klientského softwaru HANA, který se skládá z ovladače SQLDBC, ODBO-Windows jenom pro ODBC, ovladače JDBC, HANA Studio a HANA Database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Řada virtuálních počítačů řady D-Series |
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


| Produkt SAP | Hostovaný operační systém | RDBMS | Typy virtuálních počítačů |
| --- | --- | --- | --- |
| Software SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (jenom Windows a Oracle Linux), DB2, pomocného programu SAP |A5 na A11, D11 na D14, DS11 na DS14, DS11_v2 DS15_v2, GS1 na GS5, D2s_v3 D64s_v3 D2as_v4 D64as_v4 E2s_v3 E64s_v3 E2as_v4 E64as_v4 M208s_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,,,,,,, M208ms_v2, M416s_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (jenom Windows a Oracle Linux), DB2, pomocného programu SAP |A5 na A11, D11 na D14, DS11 na DS14, DS11_v2 DS15_v2, GS1 na GS5, D2s_v3 D64s_v3 D2as_v4 D64as_v4 E2s_v3 E64s_v3 E2as_v4 E64as_v4 M208s_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,,,,,,, M208ms_v2, M416s_v2 |
| SAP BusinessObjects BI | Windows |Není k dispozici |A5 na A11, D11 na D14, DS11 na DS14, DS11_v2 DS15_v2, GS1 na GS5, D2s_v3 D64s_v3 D2as_v4 D64as_v4 E2s_v3 E64s_v3 E2as_v4 E64as_v4 M208s_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,,,,,,, M208ms_v2, M416s_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (jenom Windows a Oracle Linux), DB2, pomocného programu SAP |A5 na A11, D11 na D14, DS11 na DS14, DS11_v2 DS15_v2, GS1 na GS5, D2s_v3 D64s_v3 D2as_v4 D64as_v4 E2s_v3 E64s_v3 E2as_v4 E64as_v4 M208s_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,,,,,,, M208ms_v2, M416s_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Další úlohy SAP podporované v Azure

| Produkt SAP | Hostovaný operační systém | RDBMS | Typy virtuálních počítačů |
| --- | --- | --- | --- |
| SAP Business One na SQL Server | Windows  | SQL Server | Všechny typy virtuálních počítačů s certifikací NetWeaver<br /> [Poznámka ke SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BITŮ 10,01 MS SP08 | Windows a Linux | | Všechny typy virtuálních počítačů s certifikací NetWeaver<br /> Poznámka ke SAP #2451795 |
| Platforma SAP obchodních objektů BI | Windows a Linux | | Poznámka ke SAP #2145537 |
| SAP Data Services 4,2 | | | Poznámka ke SAP #2288344 |
| Platforma SAP Hybris pro Commerce  | Windows | SQL Server, Oracle | Všechny typy virtuálních počítačů s certifikací NetWeaver <br /> [Dokumentace k Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| Platforma SAP Hybris pro Commerce  | SLES 12 nebo novější | SAP HANA | Všechny typy virtuálních počítačů s certifikací NetWeaver <br /> [Dokumentace k Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| Platforma SAP Hybris pro Commerce  | RHEL 7 nebo novější | SAP HANA | Všechny typy virtuálních počítačů s certifikací NetWeaver <br /> [Dokumentace k Hybris]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP (Hybris) Commerce Platform 1811 a novější  | Windows, SLES nebo RHEL | SQL Azure DB | Všechny typy virtuálních počítačů s certifikací NetWeaver <br /> [Dokumentace k Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
