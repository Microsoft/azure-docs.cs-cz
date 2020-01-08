---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ac3e87d7f921da2c1089eb6f2c7e61fc2c432f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463930"
---
Zóna – redundantní úložiště (ZRS) replikuje data synchronně v rámci tří clusterů úložiště v jedné oblasti. Každý cluster úložiště je fyzicky oddělený od ostatních a je umístěný ve vlastní zóně dostupnosti (AZ). Každá zóna dostupnosti&mdash;a cluster ZRS v něm&mdash;je autonomní a zahrnuje samostatné nástroje a síťové funkce. Požadavek na zápis do účtu úložiště ZRS se úspěšně vrátí až po zápisu dat do všech replik ve třech clusterech.

Když ukládáte data do účtu úložiště pomocí replikace ZRS, můžete i nadále přistupovat k datům a spravovat je, pokud se zóna dostupnosti stane nedostupnou. ZRS poskytuje vynikající výkon a nízkou latenci. ZRS nabízí stejné cíle škálovatelnosti jako [místně redundantní úložiště (LRS)](../articles/storage/common/storage-redundancy-lrs.md). Další informace o cílech škálovatelnosti pro účty úložiště úrovně Standard najdete v tématu [cíle škálovatelnosti pro účty úložiště úrovně Standard](../articles/storage/common/scalability-targets-standard-account.md).

Zvažte ZRS pro scénáře, které vyžadují konzistenci, odolnost a vysokou dostupnost. I v případě výpadku nebo přirozené havárie nedostupné zóny dostupnosti nabízí ZRS odolnost pro objekty úložiště alespoň 99,9999999999% (12 9) v průběhu daného roku.

Geograficky redundantní úložiště (GZRS) (Preview) replikuje data synchronně v rámci tří zón dostupnosti Azure v primární oblasti a pak asynchronně replikuje data do sekundární oblasti. GZRS poskytuje vysokou dostupnost dohromady s maximální odolností. GZRS je navržený tak, aby poskytoval alespoň 99.99999999999999% (16 9) odolnosti objektů v průběhu daného roku. Pro přístup pro čtení dat v sekundární oblasti povolte přístup pro čtení Geo-Zone-redundantní úložiště (RA-GZRS). Další informace o GZRS najdete v článku [geograficky redundantní úložiště pro vysokou dostupnost a maximální trvanlivost (Preview)](../articles/storage/common/storage-redundancy-gzrs.md).

Další informace o zónách dostupnosti najdete v tématu [přehled zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview).
