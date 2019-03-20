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
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 33f1ccf3f1c7bc657cc66efe7c5025356c954ad6
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58187757"
---
# <a name="asdk-release-notes"></a>Zpráva k vydání verze ASDK

Tento článek obsahuje informace o změnách, opravy a známé problémy v Azure Stack Development Kit (ASDK). Pokud si nejste jistí, kterou verzi používáte, můžete si [použití portálu ke kontrole](../azure-stack-updates.md#determine-the-current-version).

Udržujte si s tím, co se přihlásíte k odběru je novinkou ASDK [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanálu](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11902069"></a>Sestavení 1.1902.0.69

### <a name="new-features"></a>Nové funkce

- Sestavení 1902 zavádí nové uživatelské rozhraní na portálu Správce služby Azure Stack pro vytváření plánů, nabídek, kvót a doplňkové plány. Další informace, včetně snímků obrazovky najdete v části [vytváření plánů, nabídek a kvót](../azure-stack-create-plan.md).

- Seznam dalších změny a vylepšení v této verzi najdete v tématu [v této části](../azure-stack-update-1902.md#improvements) ve službě Azure Stack zpráva k vydání verze.

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Známé problémy

- Byl zjištěn problém, ve kterém se zahodí pakety více než 1450 bajtů pro interní zatížení nástroje pro vyrovnávání (ILB). Je problém způsobený nastavení jednotek MTU na hostiteli je příliš nízká tak, aby vyhovovaly VXLAN zapouzdřené pakety, které procházejí roli, která od 1901 byl přesunut do hostitele. Existují aspoň dva scénáře, které můžete setkat, ve kterých se objevuje tento problém projevit:

  - Dotazy SQL pro SQL Always On, který je za interní zatížení nástroje pro vyrovnávání (ILB) a více než 660 bajtů.
  - Nasazení Kubernetes nezdaří, pokud se pokusíte povolit více hlavních serverů.  

  Tento problém nastane, pokud máte komunikace mezi virtuálním Počítačem a ILB ve stejné virtuální síti, ale v různých podsítích. Tento problém můžete vyřešit spuštěním následujících příkazů v příkazovém řádku se zvýšenými oprávněními na hostiteli ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>Sestavení 1.1901.0.95

Zobrazit [informace důležité sestavení v poznámkách k verzi Azure Stack](../azure-stack-update-1901.md#build-reference).

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
