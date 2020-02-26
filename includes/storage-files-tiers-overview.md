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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597782"
---
Služba soubory Azure nabízí dvě různé úrovně úložiště, Premium a Standard, které vám umožní přizpůsobit vaše sdílené složky na požadavky na výkon a cenu vašeho scénáře:

- **Prémiové sdílené složky**: soubory úrovně Premium jsou založené na jednotkách SSD (Solid-State Drive) a jsou nasazené v typu **účtu úložiště** souborů. Soubory úrovně Premium poskytují pro úlohy náročné na vstupně-výstupní operace konzistentní vysoký výkon a nízkou latenci v rámci jedné číslice milisekund pro většinu vstupně-výstupních operací. Díky tomu jsou vhodné pro širokou škálu úloh, jako jsou databáze, hostování webů a vývojová prostředí. Sdílené složky Premium jsou dostupné jenom v modelu zřízené fakturace. Další informace o modelu zřízené fakturace pro sdílené složky Premium najdete v tématu [Principy zřizování pro sdílené složky prémiových souborů](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- **Standardní sdílené složky**: standardní sdílené složky se zálohují na jednotky pevného disku (HDD) a jsou nasazené v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** . Standardní sdílené složky poskytují spolehlivý výkon pro vstupně-výstupní úlohy, které jsou méně citlivé na variabilitu výkonu, jako jsou například sdílené složky pro obecné účely a vývoj a testovací prostředí. Standardní sdílené složky jsou dostupné jenom v modelu fakturace s průběžnými platbami.