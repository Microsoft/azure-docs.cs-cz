---
title: Certifikace Microsoft Azure pro SAP | Dokumenty společnosti Microsoft
description: Aktualizovaný seznam aktuálních konfigurací a certifikací SAP na platformě Azure.
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
ms.openlocfilehash: c19471fee9235faffba12a12d9f92de77f60fd4d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770522"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certifikace a konfigurace SAP spuštěné v Microsoft Azure

SAP a Microsoft mají dlouhou historii spolupráce v silném partnerství, které má vzájemné výhody pro své zákazníky. Společnost Microsoft neustále aktualizuje svou platformu a odesílá do sapu nové podrobnosti o certifikaci, aby zajistila, že Microsoft Azure je nejlepší platformou pro spuštění úloh SAP. Následující tabulky popisují podporované konfigurace Azure a seznam rostoucích certifikací SAP. Tento seznam je přehledový seznam, který by se mohl tu a tam odchýlit od oficiálních seznamů SAP. Jak se dostat k podrobným datům, je zdokumentováno v článku [Jaký software SAP je podporován pro nasazení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)

## <a name="sap-hana-certifications"></a>Certifikace SAP HANA
Odkazy:

- [Sap HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pro podporu SAP HANA pro nativní virtuální počítače Azure a velké instance HANA.

| Sap produkt | Podporovaný operační systém | Nabídky Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (včetně klientského softwaru HANA, který se skládá z databáze SQLODBC, POUZE ODBO-Windows, ODBC, ovladačů JDBC, studia HANA a databáze HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Řada virtuálních důzí řady |
| Business One na HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [Platformy IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Řízená dostupnost pro GS5. Plná podpora pro M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA v Azure (velké instance) [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Apartmá na HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA v Azure (velké instance) [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise pro BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA v Azure (velké instance) [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA v Azure (velké instance) <br /> [Platformy IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Uvědomte si, že SAP používá termín "clustering" v [platformách IaaS certifikovaných pro SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) jako synonymum pro "horizontální navýšení kapacity" a NIKOLI pro vysoce dostupnost "clustering"

## <a name="sap-netweaver-certifications"></a>Certifikace SAP NetWeaver
Microsoft Azure je certifikovaný pro následující produkty SAP s plnou podporou od Microsoftu a SAP.
Odkazy:

- [1928533 – APLIKACE SAP v Azure: Podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533) pro všechny aplikace založené na SAP NetWeaver, včetně SAP TREX, SAP LiveCache a SAP Content Server. A všechny databáze, s výjimkou SAP HANA.


| Sap produkt | Hostovaný operační systém | RDBMS | Typy virtuálních počítačů |
| --- | --- | --- | --- |
| SAP Business Suite Software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (pouze Windows a Oracle Linux), DB2, SAP ASE |A5 až A11, D11 až D14, DS11 až DS14, DS11_v2 na DS15_v2, GS1 až GS5, D2s_v3 do D64s_v3, D2as_v4 do D64as_v4, E2s_v3 do E64s_v3, E2as_v4 až E64as_v4, M64s, M128s, M128ms, M64ls, M32ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2 M416ms_v2 |
| SAP Business Vše v jednom | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (pouze Windows a Oracle Linux), DB2, SAP ASE |A5 až A11, D11 až D14, DS11 až DS14, DS11_v2 na DS15_v2, GS1 až GS5, D2s_v3 do D64s_v3, D2as_v4 do D64as_v4, E2s_v3 do E64s_v3, E2as_v4 až E64as_v4, M64s, M128s, M128ms, M64ls, M32ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2 M416ms_v2 |
| SAP BusinessObjects BI | Windows |– |A5 až A11, D11 až D14, DS11 až DS14, DS11_v2 na DS15_v2, GS1 až GS5, D2s_v3 do D64s_v3, D2as_v4 do D64as_v4, E2s_v3 do E64s_v3, E2as_v4 až E64as_v4, M64s, M128s, M128ms, M64ls, M32ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2 M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (pouze Windows a Oracle Linux), DB2, SAP ASE |A5 až A11, D11 až D14, DS11 až DS14, DS11_v2 na DS15_v2, GS1 až GS5, D2s_v3 do D64s_v3, D2as_v4 do D64as_v4, E2s_v3 do E64s_v3, E2as_v4 až E64as_v4, M64s, M128s, M128ms, M64ls, M32ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2 M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Další úlohy SAP podporované v Azure

| Sap produkt | Hostovaný operační systém | RDBMS | Typy virtuálních počítačů |
| --- | --- | --- | --- |
| SAP Business One na SQL Serveru | Windows  | SQL Server | Všechny typy virtuálních počítačů certifikované společností NetWeaver<br /> [#928839 poznámky SAP](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows a Linux | | Všechny typy virtuálních počítačů s certifikací NetWeaver<br /> #2451795 poznámky SAP |
| Platforma SAP Business Objects BI | Windows a Linux | | #2145537 poznámky SAP |
| Datové služby SAP 4.2 | | | SAP Poznámka #2288344 |
| Obchodní platforma SAP Hybris  | Windows | SQL Server, Oracle | Všechny typy virtuálních počítačů certifikované společností NetWeaver <br /> [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| Obchodní platforma SAP Hybris  | SLES 12 nebo novější | SAP HANA | Všechny typy virtuálních počítačů certifikované společností NetWeaver <br /> [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| Obchodní platforma SAP Hybris  | RHEL 7 nebo novější | SAP HANA | Všechny typy virtuálních počítačů certifikované společností NetWeaver <br /> [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| Obchodní platforma SAP (Hybris) 1811 a novější  | Okna, SLES nebo RHEL | DATABÁZE SQL Azure | Všechny typy virtuálních počítačů certifikované společností NetWeaver <br /> [Hybris Dokumentace](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
