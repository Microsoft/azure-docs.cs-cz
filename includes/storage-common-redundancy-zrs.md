---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4a43966180850645584043690b1be9d6ae232f6e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027443"
---
Zónově redundantní úložiště (ZRS) vaše data synchronně replikuje mezi tři úložnými clustery v jedné oblasti. Každý cluster úložiště je fyzicky oddělená od ostatních a je umístěn ve své vlastní zóně dostupnosti (AZ). Každá zóna dostupnosti a cluster ZRS obsahuje, je autonomní pomocí samostatných nástrojů a možností sítě.

Ukládání dat v účtu ZRS zajistí, že budete mít přístup a spravovat vaše data v případě, že zónu přestane být k dispozici. ZRS poskytuje vynikající výkon a nízkou latencí. ZRS poskytuje stejné [cíle škálovatelnosti](../articles/storage/common/storage-scalability-targets.md) jako [místně redundantní úložiště (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Zvažte použití ZRS pro scénáře, které vyžadují silnou konzistenci, silné odolnost a vysokou dostupnost i v případě výpadku nebo přírodní katastrofě vykreslí oblastmi datového centra není k dispozici. ZRS poskytuje pro objekty úložiště alespoň 99,9999999999 % (12 9) průběhu daného roku.

Další informace o zónách dostupnosti najdete v tématu [Přehled zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview).