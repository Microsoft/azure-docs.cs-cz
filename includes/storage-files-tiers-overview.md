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
ms.openlocfilehash: 7d0286b63703c165dda6cd12bb625fc64272aac1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377369"
---
Služba soubory Azure nabízí čtyři různé úrovně úložiště, Premium, transakce optimalizované, horké a studené, aby bylo možné přizpůsobit své sdílené složky na požadavky na výkon a cenu vašeho scénáře:

- **Premium**: záložní sdílené složky jsou založené na jednotkách SSD (Solid-State Drive), které jsou nasazené v typu **účtu úložiště** souborů. Soubory úrovně Premium poskytují pro úlohy náročné na vstupně-výstupní operace konzistentní vysoký výkon a nízkou latenci v rámci jedné číslice milisekund pro většinu vstupně-výstupních operací. Soubory úrovně Premium jsou vhodné pro širokou škálu úloh, jako jsou databáze, hostování webů a vývojová prostředí. Soubory úrovně Premium se dají použít společně s protokoly SMB (Server Message Block) i systémem NFS (Network File System).
- **Transakce optimalizovaná**: transakce – optimalizované sdílené složky povolují úlohy náročné na transakce, které nepotřebují latenci nabízené sdílením souborů Premium. Transakce optimalizované pro transakce jsou nabízeny na standardním hardwaru úložiště zajištěném jednotkami pevného disku (HDD) a jsou nasazeny v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** . Optimalizovaná transakce byla v minulosti označována jako "Standard", ale odkazuje na typ úložného média namísto samotné úrovně (horká a studená jsou také "standardní" úrovně, protože jsou na hardwaru úložiště úrovně Standard).
- **Hot**: Hot Shared File nabízejí úložiště optimalizované pro scénáře sdílení souborů pro obecné účely, jako jsou týmové sdílené složky a Azure File Sync. Hot Shared File jsou nabízeny na standardním hardwaru úložiště zajištěném HDD a jsou nasazeny v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** .
- **Studená**: studená sdílení souborů nabízejí cenově výhodné úložiště optimalizované pro scénáře úložiště online archivace. Azure File Sync může být vhodný i pro úlohy s nižšími změnami. Studené sdílené složky jsou nabízeny na standardním hardwaru úložiště zajištěném HDD a jsou nasazeny v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** .

Sdílené složky Premium jsou dostupné jenom v modelu zřízené fakturace. Další informace o modelu zřízené fakturace pro sdílené složky Premium najdete v tématu [Principy zřizování pro sdílené složky prémiových souborů](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Standardní sdílené složky, včetně transakcí optimalizovaných, Hot a studených sdílených složek, jsou k dispozici po fakturaci průběžných plateb.

Horké a studené sdílené složky jsou dostupné ve všech veřejných a Azure Government oblastech Azure. Transakce optimalizované pro transakce jsou k dispozici ve všech oblastech Azure, včetně oblastí Azure Čína a Azure Německo.

> [!Important]  
> Můžete přesouvat sdílené složky mezi vrstvami v rámci typů účtů úložiště GPv2 (transakce optimalizovaná, horká a studená). Sdílení se pohybuje mezi úrovněmi. transakce: přechod z Hotter úrovně na úroveň chladiče bude za každý soubor ve sdílené složce účtovat poplatek za transakci zápisu ve vrstvě chladicího objektu, zatímco přesun z úrovně chladicího počítače na Hotter vrstvu bude za každý soubor sdílené složky účtovat poplatek za transakce čtení ve studené vrstvě.