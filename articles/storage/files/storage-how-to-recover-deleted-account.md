---
title: Obnovení odstraněného účtu úložiště
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252635"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Obnovení odstraněného účtu úložiště

Azure Storage poskytuje odolnost proti chybám dat prostřednictvím automatizovaných replik, ale nezabrání uživatelům nebo kódu aplikace v poškození dat, ať už náhodně nebo se zlými úmysly. Udržování věrnosti dat během instancí aplikace nebo chyby uživatele vyžaduje pokročilejší techniky, jako je například kopírování dat do sekundárního úložiště s protokolem auditu.

Následující tabulka obsahuje přehled rozsahu obnovení účtu úložiště v závislosti na strategii replikace.

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|Správce prostředků Azure účtu úložiště|Ano|Ano|Ano|Ano|
|Klasický účet úložiště|Ano|Ano|Ano|Ano|

Shromážděte následující informace a podejte žádost o podporu pomocí podpory společnosti Microsoft:

* Název účtu úložiště
* Datum výmazu
* Oblast účtu úložiště
* Jak byl účet úložiště odstraněn?
* Jakou metodou byl účet úložiště odstraněn? (Portál, PowerShell, atd.)

Důležité body

* Obecně může trvat až 15 dní od okamžiku odstranění pro službu úložiště k provedení uvolňování paměti, takže obnovení účtů úložiště nemusí být obnovena s SLA.
* Podpora společnosti Microsoft se pokusí obnovit kontejner/účet na základě nejlepší úsilí a nemůže zaručit obnovení.

> [!NOTE]
> Obnovení nemusí být úspěšné, pokud byl účet znovu vytvořen. Pokud jste již účet znovu vytvořili, musíte jej před pokusem o obnovení nejprve odstranit.
