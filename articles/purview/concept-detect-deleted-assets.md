---
title: Jak se při prohledávání zjišťují odstraněné prostředky
description: Tento článek vysvětluje, jak účet Azure dosah detekuje odstraněné prostředky během kontrol.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553369"
---
# <a name="how-scans-detect-deleted-assets"></a>Jak se při prohledávání zjišťují odstraněné prostředky

Tento článek popisuje, jak Azure dosah využívá výsledky kontroly ke zjištění odstraněných prostředků.

## <a name="background-info"></a>Informace na pozadí

Katalog Azure dosah má informace o stavu úložiště dat, jenom když ho kontroluje. Aby katalog věděl, jestli se odstranil soubor, tabulka nebo kontejner, porovná výstup poslední kontroly s aktuálním výstupem kontroly. Předpokládejme například, že při poslední kontrole účtu Azure Data Lake Storage Gen2 zahrnula složku s názvem *složku1*. Po opětovném prohledání stejného účtu chybí *složku1* . Katalog proto předpokládá, že se složka odstranila.

## <a name="detecting-deleted-files"></a>Zjištění odstraněných souborů

Logika zjišťování chybějících souborů funguje pro více kontrol stejného uživatele i pro různé uživatele. Předpokládejme například, že uživatel spouští jednorázovou kontrolu Data Lake Storage Gen2 úložiště dat ve složkách A, B a C. Později jiný uživatel ve stejném účtu spustí jinou jednorázovou kontrolu ve složkách C, D a E stejného úložiště dat. Vzhledem k tomu, že se složka C prohledal dvakrát, katalog zkontroluje možné odstranění. Složky A, B, D a E se ale kontrolovaly jenom jednou a katalog je nekontroluje pro odstraněné prostředky.

Pokud chcete zachovat odstraněné soubory z katalogu, je důležité spustit pravidelné kontroly. Interval kontroly je důležitý, protože katalog nemůže detekovat odstraněné prostředky, dokud není spuštěná jiná kontrola. Pokud tedy spustíte prohledávání jednou měsíčně v konkrétním úložišti, katalog nebude moci detekovat žádné odstraněné datové prostředky v tomto úložišti, dokud nespustíte další kontrolu za měsíc později.

Při vytváření výčtu rozsáhlých úložišť dat, jako je Data Lake Storage Gen2, existuje více způsobů (včetně chyb výčtu a vynechaných událostí) k neúspěšným informacím. Při konkrétní kontrole může dojít k neúspěšnému vytvoření nebo odstranění souboru. Takže pokud se v katalogu neodstraní určitý soubor, neodstraní se z katalogu. Tato strategie znamená, že může dojít k chybám, když soubor, který neexistuje v úložišti naskenovaných dat, existuje i v katalogu. V některých případech může být nutné před započetím některých odstraněných prostředků prohledat úložiště dat dvakrát nebo třikrát.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít s katalogy Azure dosah, najdete v tématu [rychlý Start: vytvoření účtu dosah](create-catalog-portal.md).
