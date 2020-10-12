---
title: Šifrování v infrastruktuře – Azure Database for MySQL
description: Přečtěte si informace o použití šifrování infrastruktury pro přidání druhé vrstvy šifrování pomocí klíčů spravovaných službou.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: c087d5eea88a3329d5486afdd2158e80f7ebf778
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91531021"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>Šifrování s dvojitou infrastrukturou Azure Database for MySQL

Azure Database for MySQL používá pro data šifrování úložiště dat [v klidovém](concepts-security.md#at-rest) formátu pomocí spravovaných klíčů společnosti Microsoft. Data včetně záloh se šifrují na disku a toto šifrování je vždycky zapnuté a nedá se zakázat. Šifrování používá šifrovací modul ověřený standardem FIPS 140-2 a šifrovací šifru AES 256 pro šifrování úložiště Azure.

Šifrování s dvojitou přesností přidá druhou vrstvu šifrování pomocí klíčů spravovaných službou. Používá algoritmus FIPS 140-2 ověřený kryptografický modul, ale s jiným šifrovacím algoritmem. Tím je zajištěna další úroveň ochrany vašich dat v klidovém umístění. Klíč používaný v šifrování s dvojitou infrastrukturou je také spravovaný službou Azure Database for MySQL. Šifrování s dvojitou přesností není ve výchozím nastavení povolené, protože další vrstva šifrování může mít vliv na výkon.

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for MySQL podporuje cenové úrovně "Pro obecné účely" a "paměťově optimalizovaná".

Šifrování vrstvy infrastruktury má výhodu, že se implementuje v rámci vrstvy nejbližší zařízení úložiště nebo síťovým vodičům. Azure Database for MySQL implementuje dvě vrstvy šifrování pomocí klíčů spravovaných službou. I když je ve vrstvě služeb stále technicky, je velmi blízko hardwaru, který ukládá neaktivní neaktivní data. Volitelně můžete povolit šifrování dat v klidovém formátu pomocí [spravovaného klíče zákazníka](concepts-data-encryption-mysql.md) pro zřízený Server MySQL. 

Implementace na vrstvách infrastruktury také podporuje různorodost klíčů. Infrastruktura musí být vědoma různých clusterů počítačů a sítí. V takovém případě se pro minimalizaci vysokého poloměru útoků na infrastrukturu a nejrůznějších selhání hardwaru a sítě používají různé klíče. 

> [!NOTE]
> Použití šifrování s dvojitou přesností bude mít vliv na Azure Database for MySQL server z důvodu dalšího procesu šifrování.

## <a name="benefits"></a>Výhody

Dvojité šifrování infrastruktury pro Azure Database for MySQL přináší následující výhody:

1. **Další různorodost implementace kryptografie** – plánované přesunutí na hardwarové šifrování bude dále rozrůznit implementace poskytnutím hardwarové implementace společně s softwarovou implementací.
2. **Chyby implementace** – dvě vrstvy šifrování v infrastruktuře vrstev chrání před chybami při ukládání do mezipaměti nebo ve správě paměti ve vyšších vrstvách, které zpřístupňují data v prostém textu. Kromě toho dvě vrstvy také zajišťují proti chybám v implementaci šifrování obecně.

Kombinace těchto možností poskytuje silnou ochranu před běžnými hrozbami a slabinami, které se používají k útoku na kryptografii.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Podporované scénáře s dvojitým šifrováním infrastruktury

Možnosti šifrování, které poskytuje Azure Database for MySQL, lze použít společně. Níže najdete souhrn různých scénářů, které můžete použít:

|  ##   | Výchozí šifrování | Dvojité šifrování infrastruktury | Šifrování dat pomocí klíčů spravovaných zákazníkem  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Ano*              | *Ne*                             | *Ne*                                         |
| 2     | *Ano*              | *Ano*                            | *Ne*                                         |
| 3     | *Ano*              | *Ne*                             | *Ano*                                        |
| 4     | *Ano*              | *Ano*                            | *Ano*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - Scénář 2 a 4 může zavést 5-10% propustnosti poklesu na základě typu úlohy pro Azure Database for MySQL server z důvodu další vrstvy šifrování infrastruktury.
> - Konfigurace dvojitého šifrování infrastruktury pro Azure Database for MySQL je povolená jenom během vytváření serveru. Po zřízení serveru nelze šifrování úložiště změnit. Přesto ale můžete povolit šifrování dat pomocí klíčů spravovaných zákazníkem pro server, který se vytvořil s nebo bez šifrování infrastruktury.

## <a name="limitations"></a>Omezení

U Azure Database for MySQL podporuje dvojité šifrování infrastruktury pomocí klíče spravovaného službou tato omezení:

* Podpora této funkce je omezená na **pro obecné účely** a **paměťově optimalizované** cenové úrovně.
* Můžete vytvořit Azure Database for MySQL s povoleným šifrováním infrastruktury v následujících oblastech:

   * East US
   * Středojižní USA
   * Západní USA 2
   
* * Tato funkce je podporována pouze v oblastech a serverech, které podporují úložiště až na 16 TB. Seznam oblastí Azure, které podporují úložiště až o 16 TB, najdete v dokumentaci k [úložišti](concepts-pricing-tiers.md#storage).

    > [!NOTE]
    > - Všechny **nové** servery MySQL vytvořené v oblastech uvedených výše podporují také šifrování dat s klíči manažera zákazníka. V takovém případě se servery vytvořené prostřednictvím obnovení PITR (Point-in-Time Restore) nebo čtení repliky neopravňují jako "nové".
    > - Pokud chcete ověřit, jestli zřízený Server podporuje až 16 TB, můžete přejít na okno cenová úroveň na portálu a podívat se, jestli se dá posuvník úložiště přesunout až o 16 TB. Pokud přesunete jenom posuvník o 4 TB, server možná nepodporuje šifrování se spravovanými klíči zákazníka; data se ale šifrují pomocí klíčů spravovaných službou. AskAzureDBforMySQL@service.microsoft.comPokud máte nějaké dotazy, obraťte se na něj.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nastavit dvojité šifrování infrastruktury pro Azure Database for MySQL](howto-double-encryption.md).
