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
ms.openlocfilehash: 1a08459d0f5a0321d8fa6635b7a2c7b9c9f8ebba
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422695"
---
Služba soubory Azure nabízí čtyři různé úrovně úložiště, Premium, transakce optimalizované, horké a studené, aby bylo možné přizpůsobit své sdílené složky na požadavky na výkon a cenu vašeho scénáře:

- **Premium**: záložní sdílené složky jsou založené na jednotkách SSD (Solid-State Drive), které jsou nasazené v typu **účtu úložiště** souborů. Soubory úrovně Premium poskytují pro úlohy náročné na vstupně-výstupní operace konzistentní vysoký výkon a nízkou latenci v rámci jedné číslice milisekund pro většinu vstupně-výstupních operací. Díky tomu jsou vhodné pro širokou škálu úloh, jako jsou databáze, hostování webů a vývojová prostředí. 
- **Transakce optimalizovaná**: transakce – optimalizované sdílené složky povolují úlohy náročné na transakce, které nepotřebují latenci nabízené sdílením souborů Premium. Transakce optimalizované pro transakce jsou nabízeny na standardním hardwaru úložiště zajištěném jednotkami pevného disku (HDD) a jsou nasazeny v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** . Tato úroveň úložiště se v minulosti nazývala "Standard", ale odkazuje na typ úložného média namísto samotné úrovně (horká a studená jsou také "standardní" úrovně, protože jsou na standardním hardwaru úložiště).
- **Hot**: Hot Shared File nabízejí úložiště optimalizované pro scénáře sdílení souborů pro obecné účely, jako jsou týmové sdílené složky a Azure File Sync. Hot Shared File jsou nabízeny na standardním hardwaru úložiště zajištěném HDD a jsou nasazeny v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** .
- **Studená**: studená sdílení souborů nabízejí cenově výhodné úložiště optimalizované pro scénáře úložiště online archivace. Azure File Sync může být vhodný i pro úlohy s nižšími změnami. Studené sdílené složky jsou nabízeny na standardním hardwaru úložiště zajištěném HDD a jsou nasazeny v typu **účtu úložiště pro obecné účely verze 2 (GPv2)** .

Sdílené složky Premium jsou dostupné jenom v modelu zřízené fakturace. Další informace o modelu zřízené fakturace pro sdílené složky Premium najdete v tématu [Principy zřizování pro sdílené složky prémiových souborů](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Standardní sdílené složky, včetně transakcí optimalizovaných, Hot a studených sdílených složek, jsou k dispozici v rámci modelu průběžných plateb.

Horké a studené sdílené složky jsou momentálně dostupné v následující podmnožině veřejných oblastí (k dispozici jsou ve všech oblastech Azure dostupné sdílené složky pro transakce):

- Austrálie – střed
- Austrálie – střed 2
- Austrálie – východ
- Austrálie – jihovýchod
- Brazil South
- Kanada – východ
- Střední Kanada
- Francie – střed
- Francie – jih
- Německo – sever (veřejné)
- Německo – středozápad (veřejné)
- Indie – střed
- Indie – jih
- Indie – západ
- Japonsko – východ
- Japonsko – západ
- Jižní Korea – střed
- Jižní Korea – jih
- Norsko – východ
- Norsko – západ
- Jižní Afrika – sever
- Jižní Afrika – západ
- Švýcarsko – sever
- Švýcarsko – západ
- Spojené arabské emiráty – střed
- Spojené arabské emiráty sever
- Spojené království – jih
- Spojené království – západ
- USA – středosever
- Středojižní USA
- USA – středozápad
- Západní USA 2

Chcete-li nasadit horkou nebo studenou sdílenou složku, přečtěte si téma [Vytvoření horké nebo studené sdílené složky](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 