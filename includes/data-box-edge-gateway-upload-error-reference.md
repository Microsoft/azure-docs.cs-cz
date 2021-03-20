---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 05673885336dbfb9a70843bd0ccc4e700091ad7e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67175676"
---
|     Kód chyby     |      Popis chyby     |
|--------------------|--------------------------|
|    100             | Název kontejneru nebo sdílené složky musí být dlouhý 3 až 63 znaků.|
|    101             | Název kontejneru nebo sdílené složky se musí skládat pouze z písmen, číslic a spojovníků.|
|    102             | Název kontejneru nebo sdílené složky se musí skládat pouze z písmen, číslic a spojovníků.|
|    103             | Název objektu blob nebo souboru obsahuje nepodporované řídicí znaky.|
|    104             | Název objektu blob nebo souboru obsahuje neplatné znaky.|
|    105             | Název objektu blob nebo souboru obsahuje příliš mnoho segmentů (jednotlivé segmenty jsou oddělené lomítkem -/).|
|    106             | Název objektu blob nebo souboru je příliš dlouhý.|
|    107             | Jeden ze segmentů v názvu objektu blob nebo souboru je příliš dlouhý. |
|    108             | Velikost souboru překračuje maximální velikost souboru pro nahrání.    |
|    109             | Objekt blob nebo soubor je nesprávně zarovnán.  |
|    110             | Název souboru s kódováním Unicode nebo objekt blob není platný.|
|    111             | Název nebo předpona soubor nebo objektu blob je vyhrazeným názvem, který není podporovaný (například COM1).|
|    2000            | Neshoda značek etag znamená, že došlo ke konfliktu mezi objektem blob v cloudu a v zařízení. Pokud chcete tento konflikt vyřešit, odstraňte jeden z těchto souborů – buď verzi v cloudu nebo verzi v zařízení.    |
|    2001            | Při zpracování souboru po jeho nahrání došlo k neočekávané chybě.    Pokud tuto chybu uvidíte a její trvání bude delší než 24 hodin, kontaktujte podporu. |
|    2002            | Soubor je již otevřený v jiném procesu a není možné ho nahrát, dokud nebude obslužná rutina zavřena.|
|    2003            | Nelze otevřít soubor pro nahrání. Pokud uvidíte tuto chybu, kontaktujte podporu Microsoftu.|
|    2004            | Nelze se připojit ke kontejneru, do kterého se mají data nahrávat.|
|    2005            | Nelze se připojit ke kontejneru, protože oprávnění účtu jsou nesprávná nebo zastaralá. Zkontrolujte svůj přístup.|
|    2006            | Nelze nahrát data na účet, protože účet nebo sdílená složka jsou zakázané.|
|    2007            | Nelze se připojit ke kontejneru, protože oprávnění účtu jsou nesprávná nebo zastaralá. Zkontrolujte svůj přístup.|
|    2008            | Nelze přidat nová data, protože kontejner je plný. Nahlédněte do specifikací Azure a ověřte podporované velikosti kontejnerů podle typu. Soubor Azure například podporuje pouze maximální velikost 5 TB.|
|    2009            | Data se nepovedlo nahrát, protože kontejner přidružený ke sdílené složce neexistuje.|    
|    2997            | Došlo k neočekávané chybě. Jedná se o přechodnou chybu, která se vyřeší sám.|
|    2998            | Došlo k neočekávané chybě. Chyba se může vyřešit sama, ale pokud přetrvá déle než 24 hodin, obraťte se na podporu Microsoftu.|
|    16000           | Tento soubor nelze zpracovat.|
|    16001           | Tento soubor nelze zpracovat, protože už v místním systému existuje.|
|    16002           |Tento soubor nelze aktualizovat, protože ještě není zcela nahraný.|

