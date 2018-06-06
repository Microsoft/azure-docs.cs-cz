---
title: Azure Service Fabric rozhraní příkazového řádku sfctl úložiště | Microsoft Docs
description: Popisuje příkazy sfctl úložiště Service Fabric rozhraní příkazového řádku.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 39ecf568c5c41c0007b358670af755be1dd5d99e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763234"
---
# <a name="sfctl-store"></a>sfctl store
Proveďte základní souborové úrovni operace na clusteru úložiště bitových kopií.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| odstraňovat | Odstraní existující obsahu úložiště bitové kopie. |
| kořenové informace | Získá informace o obsahu v kořenovém adresáři úložiště bitových kopií. |
| Stat | Získá informace o obsahu pro image store. |

## <a name="sfctl-store-delete"></a>Odstranění úložiště sfctl
Odstraní existující obsahu úložiště bitové kopie.

Odstraní existující obsah image úložiště byl nalezen v rámci dané image uložit relativní cestu. Tento příkaz lze použít k odstranění balíčků nahrané aplikace po jejich zřízení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Cesta obsahu [vyžaduje] | Relativní cesta k souboru nebo složce v úložišti bitové kopie z svůj kořen. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-store-root-info"></a>úložiště sfctl kořenové info
Získá informace o obsahu v kořenovém adresáři úložiště bitových kopií.

Vrací informace o úložišti bitové kopie obsahu v kořenovém adresáři úložiště bitových kopií.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-store-stat"></a>stat sfctl úložiště
Získá informace o obsahu pro image store.

Vrací informace o obsahu úložiště bitové kopie v zadané contentPath. ContentPath je relativní vůči kořenovému adresáři úložiště bitové kopie.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Cesta obsahu [vyžaduje] | Relativní cesta k souboru nebo složce v úložišti bitové kopie z svůj kořen. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="next-steps"></a>Další postup
- [Instalační program](service-fabric-cli.md) Service Fabric rozhraní příkazového řádku.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).