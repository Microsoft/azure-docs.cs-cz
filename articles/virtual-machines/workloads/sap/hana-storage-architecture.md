---
title: Architektura úložiště SAP Hana v Azure (velké instance) | Dokumentace Microsoftu
description: Architektura úložiště nasazení SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e692cc1fd8670cc14b42e4714d84356d4d4c53a2
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275985"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Architektura úložiště SAP HANA (velké instance)

Rozložení úložiště pro SAP HANA v Azure (velké instance) je nakonfigurována ve SAP HANA na model nasazení classic na SAP Doporučené pokyny. Pokyny jsou dokumentovány v článku [požadavky na úložiště SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) dokument white paper.

Velká Instance HANA typu můžu třídy součástí čtyřikrát paměti svazek jako svazek úložiště. Pro třídu typu II velká Instance HANA jednotek není čtyřnásobnou úložiště. Jednotky se dodávají s svazku, který je určený pro ukládání záloh protokolů transakcí HANA. Další informace najdete v tématu [nainstalujte a nakonfigurujte SAP HANA (velké instance) v Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

V následující tabulce, jde o přidělení úložiště. Tabulka uvádí přibližnou kapacitu pro různé svazky s různými jednotkami velká Instance HANA k dispozici.

| Velká Instance HANA SKU | data systému Hana / | Hana/log | Hana/shared | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S96 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4 608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m | 11,520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S192xm |  11,520 GB |  1 536 GB |  1,792 GB |  1 536 GB |
| S384 | 11,520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S384m | 12 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xm | 16 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xxm |  20 000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S576m | 20 000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4 096 GB | 2 048 GB | 4 096 GB |
| S768m | 28,000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S768xm | 40 960 GB | 6,144 GB | 4 096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2 050 GB | 4,100 GB |


Skutečné nasazené svazky může lišit v závislosti na nasazení a nástroje, který se používá k zobrazení velikostí svazku.

Pokud rozdělíte SKU velké Instance HANA, několik příkladů kusů možné dělení může vypadat:

| Oddíl paměti v GB | data systému Hana / | Hana/log | Hana/shared | Hana/log/zálohování |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1 024 GB | 640 GB |
| 1 536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


Tyto velikosti jsou čísla hrubý svazku, která mírně nasazení a nástroje používané ke podívejte se na svazky se může lišit. Existují také jiné velikosti oddílu, jako je například 2,5 TB. Tyto velikosti úložiště se vypočítávat pomocí vzorce podobný použitému pro předchozí oddíly. Termín "oddíly", neznamená, že operační systém, paměť nebo prostředky procesoru se nijak rozdělit na oddíly. Znamená to oddílů pro úložiště pro různé instance HANA, které můžete chtít nasazení v jedné jednotce velká Instance HANA. 

Potřebujete další úložiště. Přidání úložiště koupit další úložiště v jednotkách 1 TB. Toto dodatečné úložiště lze přidat jako další svazek. To lze také rozšířit nejméně jeden z existující svazky. Není možné snížit velikost svazků původně nasazené a většinou popsané v předchozích tabulkách. Také není možné změnit názvy svazků nebo připojit názvy. Výše popsaný svazky úložiště jsou připojeny k velká Instance HANA jednotky jako NFS4 svazky.

Snímky úložiště můžete používat pro účely obnovení zálohování a obnovení a po havárii. Další informace najdete v tématu [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Přečtěte si [HLI Podporované scénáře](hana-supported-scenario.md) podrobnosti rozložení úložiště pro váš scénář.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Spusťte několik instancí SAP HANA na jednu jednotku velká Instance HANA

Je možné k hostování více než jeden aktivní instanci SAP HANA na jednotkách velká Instance HANA. Poskytují možnosti snímků úložiště a zotavení po havárii, taková konfigurace vyžaduje svazku nastavit na jednu instanci. V současné době velká Instance HANA jednotky lze dále rozdělit následujícím způsobem:

- **S72 S72m, S96, S144, S192**: Dokupuje se násobek 256 GB, 256 GB od nejmenších po počáteční jednotky. Různé přírůstky například 256 GB až 512 GB, mohou být kombinovány pro maximální paměť jednotku.
- **S144m a S192m**: Dokupuje se násobek 256 GB, 512 GB nejmenší jednotka. Různé přírůstky například 512 GB a o velikosti 768 GB mohou být kombinovány pro maximální paměť jednotku.
- **Zadejte třídu II**: V přírůstcích po 512 GB, od nejmenších po počáteční jednotku 2 TB. Různé přírůstky například 512 GB, 1 TB a 1,5 TB mohou být kombinovány pro maximální paměť jednotku.

Několik příkladů spouštění více instancí SAP HANA může vypadat nějak takto.

| Skladová jednotka (SKU) | Velikost paměti | Velikost úložiště | Velikosti více databází |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB instance HANA<br /> nebo instance 1 x 512 GB + 1 x 256 GB instance<br /> nebo 3 x 256 GB instance | 
| S72m | 1,5 TB | 6 TB | instance HANA 3x512GB<br />nebo instance 1 x 512 GB + 1 x 1 TB instance<br />nebo instance 6 x 256 GB<br />nebo instance 1x1.5 TB | 
| S192m | 4 TB | 16 TB | instance 8 x 512 GB<br />nebo instancích o velikosti 4 x 1 TB<br />nebo instance 4 x 512 GB + instancích o velikosti 2 x 1 TB<br />nebo instance 4 x 768 GB + 2 x 512 GB instance<br />nebo instance 1 × 4 TB |
| S384xm | 8 TB | 22 TB | instancích o velikosti 4 x 2 TB<br />nebo instancích o velikosti 2 x 4 TB<br />nebo instancích o velikosti 2 x 3 TB + 1 × 2 TB instancí<br />nebo instance 2x2.5 TB + 1 × 3 TB instancí<br />nebo instance 1 × 8 TB |


Existují i další varianty konfigurací. 

## <a name="encryption-of-data-at-rest"></a>Šifrování neaktivních uložených dat
Úložiště pro velké Instance HANA umožňuje transparentní šifrování dat se ukládají na discích. Při nasazení jednotka velká Instance HANA, můžete povolit tento typ šifrování. Také můžete změnit na šifrovaných svazcích po nasazení se provádí. Přechod z nešifrované na šifrovaných svazcích je transparentní a nevyžaduje výpadek. 

S typem můžu třídy skladových jednotek, svazků spouštěcí logická jednotka je uložen na, je zašifrovaný. Pro třídu typu II skladové položky z velké instance HANA je potřeba šifrovat spouštěcí logické jednotky s operačním systémem metody. Další informace obraťte se na tým Microsoft Service Management.

**Další kroky**
- Přečtěte si [Podporované scénáře pro velké instance HANA](hana-supported-scenario.md)