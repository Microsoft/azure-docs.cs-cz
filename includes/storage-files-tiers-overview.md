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
ms.openlocfilehash: a2493ce764f2aed2aca1d555f98b738185159f7a
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064870"
---
Služba soubory Azure nabízí čtyři různé úrovně úložiště, Premium, transakce optimalizované, horké a studené, aby bylo možné přizpůsobit své sdílené složky na požadavky na výkon a cenu vašeho scénáře:

- **Premium**: záložní sdílené složky jsou založené na jednotkách SSD (Solid-State Drive), které jsou nasazené v typu **účtu úložiště** souborů. Soubory úrovně Premium poskytují pro úlohy náročné na vstupně-výstupní operace konzistentní vysoký výkon a nízkou latenci v rámci jedné číslice milisekund pro většinu vstupně-výstupních operací. Soubory úrovně Premium jsou vhodné pro širokou škálu úloh, jako jsou databáze, hostování webů a vývojová prostředí. 
- **Transakce optimalizovaná**: transakce – optimalizované sdílené složky povolují úlohy náročné na transakce, které nepotřebují latenci nabízené sdílením souborů Premium. Transakce optimalizované pro transakce jsou nabízeny na standardním hardwaru úložiště zajištěném jednotkami pevného disku (HDD) a jsou nasazeny v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** . Optimalizovaná transakce byla v minulosti označována jako "Standard", ale odkazuje na typ úložného média namísto samotné úrovně (horká a studená jsou také "standardní" úrovně, protože jsou na hardwaru úložiště úrovně Standard).
- **Hot**: Hot Shared File nabízejí úložiště optimalizované pro scénáře sdílení souborů pro obecné účely, jako jsou týmové sdílené složky a Azure File Sync. Hot Shared File jsou nabízeny na standardním hardwaru úložiště zajištěném HDD a jsou nasazeny v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** .
- **Studená**: studená sdílení souborů nabízejí cenově výhodné úložiště optimalizované pro scénáře úložiště online archivace. Azure File Sync může být vhodný i pro úlohy s nižšími změnami. Studené sdílené složky jsou nabízeny na standardním hardwaru úložiště zajištěném HDD a jsou nasazeny v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** .

Sdílené složky Premium jsou dostupné jenom v modelu zřízené fakturace. Další informace o modelu zřízené fakturace pro sdílené složky Premium najdete v tématu [Principy zřizování pro sdílené složky prémiových souborů](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Standardní sdílené složky, včetně transakcí optimalizovaných, Hot a studených sdílených složek, jsou k dispozici po fakturaci průběžných plateb.

Horká a studená sdílená složka je dostupná ve všech veřejných oblastech Azure. Transakce optimalizované pro transakce jsou k dispozici ve všech oblastech Azure, včetně národních cloudových oblastí.

Chcete-li nasadit horkou nebo studenou sdílenou složku, přečtěte si téma [Vytvoření horké nebo studené sdílené složky](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 