---
title: Azure Service Fabric CLI sfctl úložiště | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl úložiště.
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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 034ae29b5fabae15aa2b6b96e7fefaef23c1c5a1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275223"
---
# <a name="sfctl-store"></a>sfctl store
Provedení operace na úrovni základní soubor na úložiště imagí clusteru.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní existující obsah úložiště image. |
| kořenovými informacemi | Získá informace o obsahu v kořenovém adresáři úložiště image store. |
| Stat | Získá informace o obsahu úložiště bitové kopie. |

## <a name="sfctl-store-delete"></a>sfctl store delete
Odstraní existující obsah úložiště image.

Odstraní existující image store obsahu byl nalezen v rámci danou image uložit relativní cestu. Tento příkaz slouží k odstranění balíčků nahrané aplikace po jejich zřízení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinné] cesta obsahu | Relativní cesta k souboru nebo složky v úložišti imagí z jeho kořenové složky. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-store-root-info"></a>sfctl úložiště root-info
Získá informace o obsahu v kořenovém adresáři úložiště image store.

Vrátí informace o úložiště image store obsahu v kořenovém adresáři úložiště image store.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-store-stat"></a>stat sfctl úložiště
Získá informace o obsahu úložiště bitové kopie.

Vrátí informace o obsahu úložiště bitové kopie na zadaná cesta contentPath. ContentPath je relativní vzhledem ke kořenové složce úložiště image store.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinné] cesta obsahu | Relativní cesta k souboru nebo složky v úložišti imagí z jeho kořenové složky. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |


## <a name="next-steps"></a>Další postup
- [Instalační program](service-fabric-cli.md) příkazového řádku Service Fabric.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).