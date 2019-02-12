---
title: Zpráva k vydání verze Microsoft Azure Stack Development Kit | Dokumentace Microsoftu
description: Vylepšení, oprav a známých problémů pro Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 485d88a4765d7cedcb171a5b325fe5f366fff1f9
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004743"
---
# <a name="asdk-release-notes"></a>Zpráva k vydání verze ASDK

Tento článek obsahuje informace o změnách, opravy a známé problémy v Azure Stack Development Kit (ASDK). Pokud si nejste jistí, kterou verzi používáte, můžete si [použití portálu ke kontrole](../azure-stack-updates.md#determine-the-current-version).

Udržujte si s tím, co se přihlásíte k odběru je novinkou ASDK [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanálu](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11901095"></a>Sestavení 1.1901.0.95

### <a name="changes"></a>Změny

Toto sestavení obsahuje následující vylepšení pro službu Azure Stack:

- Součásti protokolu BGP a překladu adres jsou nyní nasadit na fyzickém hostiteli. To eliminuje nutnost mít dvě veřejné nebo firemní IP adresy pro nasazení ASDK a také zjednodušuje nasazení.
- Azure Stack integrované systémy zálohování nyní mohou [ověřit](asdk-validate-backup.md) pomocí **asdk installer.ps1** skript prostředí PowerShell.

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v tématu [v této části](../azure-stack-update-1901.md#new-features) poznámky k verzi služby Azure Stack.

### <a name="fixed-and-known-issues"></a>Oprava a známé problémy

- Seznam opravených chybách v této verzi najdete v tématu [v této části](../azure-stack-update-1901.md#fixed-issues) poznámky k verzi služby Azure Stack. Seznam známých problémů najdete v tématu [v této části](../azure-stack-update-1901.md#known-issues-post-installation).
- Všimněte si, že [dostupných oprav hotfix Azure Stack](../azure-stack-update-1901.md#azure-stack-hotfixes) se nevztahují na Azure Stack ASDK.

## <a name="build-118110101"></a>Sestavení 1.1811.0.101

### <a name="changes"></a>Změny

Toto sestavení obsahuje následující vylepšení pro službu Azure Stack:  

- Existuje sada nový minimální a doporučené hardwarové a softwarové požadavky pro ASDK. Tyto nové doporučené specifikace jsou dokumentovány v článku [aspektech plánování nasazení Azure Stack](asdk-deploy-considerations.md). Jako platformu Azure Stack se vyvinula, další služby jsou teď k dispozici a může vyžadovat další prostředky. Zvýšená specifikace odrážet tato revidované doporučení.

### <a name="new-features"></a>Nové funkce

Seznam nových funkcí v této verzi najdete v tématu [v této části](../azure-stack-update-1811.md#new-features) poznámky k verzi služby Azure Stack.

### <a name="fixed-and-known-issues"></a>Oprava a známé problémy

Seznam opravených chybách v této verzi najdete v tématu [v této části](../azure-stack-update-1811.md#fixed-issues) poznámky k verzi služby Azure Stack. Seznam známých problémů najdete v tématu [v této části](../azure-stack-update-1811.md#known-issues-post-installation).

## <a name="build-11809090"></a>Sestavení 1.1809.0.90

### <a name="new-features"></a>Nové funkce

Seznam nových funkcí v této verzi najdete v tématu [v této části](../azure-stack-update-1809.md#new-features) poznámky k verzi služby Azure Stack.

### <a name="fixed-issues"></a>Oprava potíží

Seznam opravených chybách v této verzi najdete v tématu [v této části](../azure-stack-update-1809.md#fixed-issues).

### <a name="known-issues"></a>Známé problémy

Seznam známých problémů v této verzi najdete v tématu [v této části](../azure-stack-update-1809.md#known-issues-post-installation).