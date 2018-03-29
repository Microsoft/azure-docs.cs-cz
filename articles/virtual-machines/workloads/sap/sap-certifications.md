---
title: Certifikace Microsoft Azure pro SAP | Microsoft Docs
description: Aktualizovaný seznam aktuální konfigurace a certifikátů SAP na platformě Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: 96a3b6f8f8a03019f0446aa7ba42f53298e21009
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certifikace SAP a konfigurace systémem Microsoft Azure

SAP a Microsoft mají dlouhou spolupráci ve silné spolupráci, která má oboustranně zákazníkům. Společnost Microsoft průběžně aktualizuje jeho platforma a odeslání nové podrobnosti certifikační SAP za účelem zajištění Microsoft Azure je nejlepší platformě, na které se mají spouštět úlohy SAP. Následující tabulky popisují naše podporované konfigurace a seznam narůstají certifikace. 

## <a name="sap-hana-certifications"></a>Certifikace SAP HANA
Odkazy:

- [Poznámka SAP 2316233 - SAP HANA v Microsoft Azure (velké instance)](https://launchpad.support.sap.com/#/notes/2316233) pokrývajících velké instancí HANA týkající se podpory SAP HANA.
- [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Amazon%20Web%20Services%2CMicrosoft%20Azure) pro SAP HANA podpora pro nativní virtuální počítače Azure.

| Produkt SAP | Podporovaný operační systém | Nabídky Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (včetně klientský software HANA skládá z SQLODBC, pouze ODBO – Windows, rozhraní ODBC, ovladače JDBC, HANA studio a HANA databáze) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Počítač D-Series rodiny |
| Obchodní jeden na HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP HANA S NEBO 4 |Red Hat Enterprise Linux, SUSE Linux Enterprise | Řízené dostupnost pro GS5, SAP HANA v Azure (velké instance) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 pro nasazení jednoho uzlu pro scénáře nevýrobní prostředí SAP HANA v Azure (velké instance) |
| HANA Enterprise for BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 pro jeden uzel nasazení SAP HANA v Azure (velké instance) |
| SAP HANA BW NEBO 4 | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 pro jeden uzel nasazení SAP HANA v Azure (velké instance) |

## <a name="sap-netweaver-certifications"></a>Certifikace SAP NetWeaver
Microsoft Azure má certifikaci pro následující produkty SAP, se zárukou plné podpory od Microsoftu i SAPu.
Odkazy:

- [1928533 - SAP aplikace v Azure: typy podporovaných produktů a virtuální počítač Azure](https://launchpad.support.sap.com/#/notes/1928533) pro všechny aplikace SAP NetWeaver na základě, včetně SAP TREX, SAP LiveCache a obsahu serveru SAP. A všechny databáze, s výjimkou SAP HANA.


| Produkt SAP | Hostovaného operačního systému | RDBMS | Typy virtuálních počítačů |
| --- | --- | --- | --- |
| SAP Business Suite Software |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows a Oracle Linux pouze), DB2, SAP App Service Environment |A5 na A11 D11 k D14 DS11 k DS14, DS11_v2 k DS15_v2, spravuje organizace GS1 k GS5, D2s_v3 k D64s_v3, E2s_v3 k E64s_v3, M64s k M128ms |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (Windows a Oracle Linux pouze), DB2, SAP App Service Environment |A5 na A11 D11 k D14 DS11 k DS14, DS11_v2 k DS15_v2, spravuje organizace GS1 k GS5, D2s_v3 k D64s_v3, E2s_v3 k E64s_v3, M64s k M128ms |
| SAP BusinessObjects BI |Windows |Nevztahuje se. |A5 na A11 D11 k D14 DS11 k DS14, DS11_v2 k DS15_v2, spravuje organizace GS1 k GS5, D2s_v3 k D64s_v3, E2s_v3 k E64s_v3, M64s k M128ms |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (Windows a Oracle Linux pouze), DB2, SAP App Service Environment |A5 na A11 D11 k D14 DS11 k DS14, DS11_v2 k DS15_v2, spravuje organizace GS1 k GS5, D2s_v3 k D64s_v3, E2s_v3 k E64s_v3, M64s k M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Další úlohy SAP podporované v Azure

| Produkt SAP | Hostovaného operačního systému | RDBMS | Typy virtuálních počítačů |
| --- | --- | --- | --- |
| SAP Business na systém SQL Server | Windows  | SQL Server | Všechny NetWeaver certifikované typy virtuálních počítačů |
| SAP BPC 10.01 MS SP08 | Windows | | Všechny typy NetWeaver certifikované virtuálních počítačů<br /> Poznámka SAP #2451795 |
| SAP Business objekty BI platformy | Windows | | Poznámka SAP #2145537 |
| Datové služby SAP 4.2 | | | Poznámka SAP #2288344 |
| SAP Hybris Commerce Platform 5.x a 6.x | Windows | Systém SQL Server, Oracle | Všechny NetWeaver certifikované typy virtuálních počítačů<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
