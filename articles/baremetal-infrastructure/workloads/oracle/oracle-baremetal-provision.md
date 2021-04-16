---
title: Zřídit BareMetal pro Oracle
description: Přečtěte si o zřizování infrastruktury BareMetal pro Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 45ca2bf8bfc61c3820b4b14daa998e2e82e972fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558984"
---
# <a name="provision-baremetal-for-oracle"></a>Zřídit BareMetal pro Oracle

V tomto článku se podíváme na to, jak zřídit BareMetal instance infrastruktury pro úlohy Oracle. 

Prvním krokem ke zřízení instancí BareMetal je práce s Microsoft CSA. Pomůžou vám na základě konkrétních potřeb úloh a architektury, kterou nasazujete, ať už jedna instance, jeden uzel RAC nebo RAC. Další informace o těchto topologiích najdete v tématu [Architektura infrastruktury BareMetal pro Oracle](oracle-baremetal-architecture.md).

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Aktivní předplatné Azure
> * Smlouva o podpoře Microsoft Premier
> * Licence pro Red Hat Enterprise Linux 7,6
> * Kontrakt podpory Oracle 
> * Licence a součásti instalace softwaru pro Oracle
> * ExpressRoute připojená **místně k Azure** (**volitelně** můžete nakonfigurovat ExpressRoute Global REACH pro přímé připojení z místního prostředí k Oracle Database).   
> * Virtuální síť
> * Vytvoření brány
> * Virtuální počítače ve virtuální síti (pole s odkazy)

## <a name="information-to-provide-microsoft-operations"></a>Informace k poskytování operací Microsoftu

Do CSAu budete muset zadat následující informace:

1. Adresní prostor virtuální sítě. Tento rozsah musí být/24 podsítí; například 10.11.0.0/24.
2. Rozsah P2P Tento rozsah musí být podsíť/29; například 10.12.0.0/29.
3. Fond IP adres serveru. Doporučený rozsah je/24; například 10.13.0.0/24.
4. IP adresa serveru. Vyberte IP adresu z fondu IP adres serveru.

    > [!Note] 
    > Prvních třicet IP adres je vyhrazených pro konfiguraci infrastruktury Microsoftu. V tomto příkladu by byla vaše první dostupná IP adresa pro okno 10.13.0.30.

5. Vyžaduje se oblast Azure; například západní USA 2.
6. Požadovaná SKU infrastruktury BareMetal; například S192 (dva počítače).

## <a name="storage-requirements"></a>Požadavky na úložiště

Pracujte s vaším zástupcem CSA za požadavky na úložiště během žádosti o zřízení, včetně očekávaných potřeb úložiště na základě budoucího růstu. Přidané úložiště je v přírůstcích po 1 TB.

V případě svazků budeme dodržovat [Standard OFA (optimální flexibilní architektura)](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)Oracle s konfigurací Basic a Enterprise. Pokud máte jiné požadavky na úložiště než standard "Změna velikosti pro trička", udělejte vlastní požadavek pomocí CSA.

| Základní konfigurace (ověření koncepce/testování) | Popis | Malá | Střední | Velká |
| --- | --- | --- | --- | --- |
| /u01 | Binární soubory Oracle | 500 GB | 500 GB | 500 GB |
| /u02 | Intenzivní čtení nebo správce | 500 GB | 1 TB | 5 TB |
| /u03 | Intenzivní zápisy/protokoly | 500 GB | 1 TB | 5 TB |
| /u09 | Backup | 5 TB | 10 TB | 15 TB |

| Podniková konfigurace | Popis | Malá | Střední | Velká | Velmi velký |
| --- | --- | --- | --- | --- | --- |
| /u01 | Binární soubory Oracle | 500 GB | 500 GB | 500 GB | 500 GB |
| /u02 | Správce | 100 GB | 100 GB | 100 GB | 100 GB |
| /U10 na/U59 | Náročné na čtení | 500 GB | 5 TB | 10 TB | 20 TB |
| /U60 na/U89 | Náročné na zápis | 500 GB | 5 TB | 10 TB | 20 TB |
| /U90 na/u91 | Protokoly opětovného provedení | 500 GB | 500 GB | 1 TB | 1 TB |
| /u95 | Archiv | 10 TB | 10 TB | 20 TB | 20 TB |
| /u98 | Backup | 25 TB | 25 TB | 50 TB | 50 TB |

## <a name="next-step"></a>Další krok

Přečtěte si další informace o infrastruktuře BareMetal pro Oracle.

> [!div class="nextstepaction"]
> [Co je BareMetal infrastruktura v Azure?](../../concepts-baremetal-infrastructure-overview.md)
