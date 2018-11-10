---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 037996385f34c5037e0386686e3bdf8dc1b7a37a
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219830"
---
Zónově redundantní úložiště (ZRS) vaše data synchronně replikuje mezi tři úložnými clustery v jedné oblasti. Každý cluster úložiště je fyzicky oddělená od ostatních a nachází se ve své vlastní zóně dostupnosti (AZ). Každá zóna dostupnosti&mdash;a ZRS clusteru v rámci něj&mdash;je autonomní a zahrnuje samostatné nástroje a funkce sítě.

Při ukládání vašich dat v účtu úložiště pomocí replikací zónově redundantního úložiště, můžete nadále přístup k a spravovat vaše data, pokud se stane nedostupným zóně dostupnosti. ZRS poskytuje vynikající výkon a nízkou latencí. ZRS poskytuje stejné [cíle škálovatelnosti](../articles/storage/common/storage-scalability-targets.md) jako [místně redundantní úložiště (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Zvažte použití ZRS pro scénáře, které vyžadují konzistence, odolnost a vysokou dostupnost. I v případě výpadku nebo přírodní katastrofě vykreslí zóně dostupnosti není k dispozici, ZRS poskytuje pro objekty úložiště alespoň 99,9999999999 % (12 9) průběhu daného roku.

Další informace o zónách dostupnosti najdete v tématu [Přehled zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview).