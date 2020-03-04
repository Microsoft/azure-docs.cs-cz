---
title: Jak obnovit odstraněný účet úložiště
description: Přečtěte si, jak obnovit odstraněný účet úložiště.
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252635"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Jak obnovit odstraněný účet úložiště

Azure Storage zajišťuje odolnost dat prostřednictvím automatizovaných replik, ale nebrání uživatelům nebo kódu aplikace v poškozených datech, ať už omylem nebo zlomyslným. Zachování přesnosti dat během výskytů aplikace nebo chyby uživatele vyžaduje pokročilejší techniky, jako je například kopírování dat do sekundárního úložiště s protokolem auditu.

V následující tabulce najdete přehled rozsahu obnovení účtu úložiště v závislosti na strategii replikace.

| |LRS|ZRS|GRS|RA – GRS|
|---|---|---|---|---|
|Azure Resource Manager účtu úložiště|Ano|Ano|Ano|Ano|
|Účet úložiště – klasický|Ano|Ano|Ano|Ano|

Shromážděte následující informace a Zažádejte si žádost o podporu s podpora Microsoftu:

* Název účtu úložiště
* Datum odstranění
* Oblast účtu úložiště
* Jak se odstranil účet úložiště?
* Jakou metodu odstranili účet úložiště? (Portál, PowerShell atd.)

Důležité body

* Může obecně trvat až 15 dní od okamžiku odstranění služby úložiště, aby bylo možné provést uvolňování paměti, takže obnovení účtů úložiště se nemusí obnovit se smlouvou SLA.
* Podpora Microsoftu se pokusí obnovit kontejner nebo účet na základě optimálního úsilí a nemůže zaručit obnovení.

> [!NOTE]
> Pokud byl účet znovu vytvořen, obnovení nemusí být úspěšné. Pokud jste už účet znovu vytvořili, musíte ho nejdřív odstranit, aby se mohl pokusit o obnovení.
