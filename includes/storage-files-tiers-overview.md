---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6d06a46d2eaaad362890f1e3e44dbc746fa10898
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633280"
---
Služba soubory Azure nabízí čtyři různé úrovně úložiště, Premium, transakce optimalizované, horké a studené, aby bylo možné přizpůsobit své sdílené složky na požadavky na výkon a cenu vašeho scénáře:

- **Premium**: sdílené složky Premium jsou založené na jednotkách SSD (Solid-State Drive) a poskytují konzistentní vysoký výkon a nízkou latenci v rámci jedné číslice milisekundy pro většinu vstupně-výstupních operací pro úlohy náročné na vstupně-výstupní operace. Soubory úrovně Premium jsou vhodné pro širokou škálu úloh, jako jsou databáze, hostování webů a vývojová prostředí. Soubory úrovně Premium se dají použít společně s protokoly SMB (Server Message Block) i systémem NFS (Network File System).
- **Transakce optimalizovaná**: transakce – optimalizované sdílené složky povolují úlohy náročné na transakce, které nepotřebují latenci nabízené sdílením souborů Premium. Transakce optimalizované pro transakce jsou nabízeny na standardním hardwaru úložiště zajištěném jednotkami pevného disku (HDD). Optimalizovaná transakce byla v minulosti označována jako "Standard", ale odkazuje na typ úložného média namísto samotné úrovně (horká a studená jsou také "standardní" úrovně, protože jsou na hardwaru úložiště úrovně Standard).
- **Hot**: Hot Shared File nabízejí úložiště optimalizované pro scénáře sdílení souborů pro obecné účely, jako jsou týmové sdílené složky. Hot Shared File jsou nabízeny na standardním hardwaru úložiště, který je zálohovaný pomocí HDD.
- **Studená**: studená sdílení souborů nabízejí cenově výhodné úložiště optimalizované pro scénáře úložiště online archivace. Studené sdílené složky jsou nabízeny na standardním hardwaru úložiště, který je zálohovaný pomocí HDD.

Soubory úrovně Premium se nasazují v typu **účtu úložiště** souborů a jsou dostupné jenom v modelu zřízené fakturace. Další informace o modelu zřízené fakturace pro sdílené složky Premium najdete v tématu [Principy zřizování pro sdílené složky prémiových souborů](../articles/storage/files/understanding-billing.md#provisioned-model). Standardní sdílené složky, včetně transakce optimalizované, horké a studené sdílené složky, se nasazují v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** a při fakturaci jsou k dispozici prostřednictvím průběžných plateb. Horké a studené sdílené složky jsou dostupné ve všech veřejných a Azure Government oblastech Azure. Transakce optimalizované pro transakce jsou k dispozici ve všech oblastech Azure, včetně oblastí Azure Čína a Azure Německo.

Při výběru vrstvy úložiště pro vaše úlohy zvažte požadavky na výkon a využití. Pokud vaše úloha vyžaduje latenci s jednou číslicí nebo používáte úložné médium SSD místně, je pravděpodobně nejlepší přizpůsobit úroveň Premium. Pokud se nízká latence nevejde do značné míry, například u týmových sdílených složek, které jsou místně připojené z Azure nebo v místní mezipaměti pomocí Synchronizace souborů Azure, může být úložiště úrovně Standard lépe vhodné z hlediska nákladů.

Jakmile vytvoříte sdílenou složku v účtu úložiště, nemůžete ji přesunout do vrstev výhradně na různé typy účtů úložiště. Například pro přesunutí transakce optimalizované sdílené složky do úrovně Premium musíte v účtu úložiště úložiště vytvořit novou sdílenou složku a zkopírovat data z původní sdílené složky do nové sdílené složky v účtu úložiště. K kopírování dat mezi sdílenými složkami Azure doporučujeme používat AzCopy, ale můžete použít také nástroje jako `robocopy` ve Windows nebo `rsync` pro MacOS a Linux. 

Sdílené složky nasazené v rámci účtů úložiště GPv2 se dají přesouvat mezi standardními úrovněmi (transakce optimalizovaná, horká a studená) bez vytvoření nového účtu úložiště a migrace dat, ale při změně úrovně se vám budou účtovat náklady za transakce. Když přesunete sdílenou složku z vrstvy Hotter do úrovně chladicího počítače, bude se účtovat poplatek za transakci zápisu na úrovni chladicích souborů pro každý soubor ve sdílené složce. Když přesunete sdílenou složku z úrovně chladicích souborů na úroveň Hotter, bude se účtovat poplatek za transakce čtení ve studené vrstvě pro každý soubor ve sdílené složce.

Další informace najdete v tématu [Principy fakturace služby soubory Azure](../articles/storage/files/understanding-billing.md) .