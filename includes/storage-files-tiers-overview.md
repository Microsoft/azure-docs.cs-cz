---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597782"
---
Azure Files nabízí dvě různé úrovně úložiště, premium a standard, které vám umožní přizpůsobit své akcie na výkon a cenové požadavky vašeho scénáře:

- **Sdílené složky Premium**: Sdílené složky Premium jsou zálohovány jednotkami SSD (SSD) a jsou nasazeny v **typu úložiště úložiště Úložiště souboru.** Sdílené složky Premium poskytují konzistentní vysoký výkon a nízkou latenci v rámci jednociferných milisekund pro většinu operací vi pro úlohy náročné na videa. Díky tomu jsou vhodné pro širokou škálu úloh, jako jsou databáze, hostování webových stránek a vývojová prostředí. Sdílené složky Premium jsou k dispozici jenom v modelu zřízené fakturace. Další informace o zřízeném modelu fakturace pro sdílené složky s prémiovými soubory naleznete [v tématu Principy zřizování sdílených složek s prémií](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- **Standardní sdílené složky**: Standardní sdílené složky jsou zálohovány pevnými disky (HDD) a jsou nasazeny v **obecném účelu verze 2 (GPv2) typu úložiště.** Standardní sdílené složky poskytují spolehlivý výkon pro úlohy vstupně-io, které jsou méně citlivé na variabilitu výkonu, jako jsou sdílené složky pro obecné účely a vývojová a testovací prostředí. Standardní sdílené složky jsou k dispozici pouze v modelu fakturace s průběžným platbou.