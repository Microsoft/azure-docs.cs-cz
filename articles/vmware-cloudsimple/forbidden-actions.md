---
title: Zakázané akce při přístupu se zvýšenými oprávněními
description: Modul VMware obnoví změny, aby se zajistilo, že služba zůstane nepřerušená, když modul VMware zjistí některou z následujících zakázaných akcí.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92915405"
---
# <a name="forbidden-actions-during-elevated-access"></a>Zakázané akce při přístupu se zvýšenými oprávněními

V časovém intervalu zvýšení úrovně jsou některé akce zakázané. Když modul VMware detekuje některou z následujících zakázaných akcí, modul VMware obnoví změny, aby služba zůstala nepřerušená.

## <a name="cluster-actions"></a>Akce clusteru

- Odebírá se cluster z vCenter.
- Změna vSphere vysoké dostupnosti (HA) v clusteru.
- Přidání hostitele do clusteru z vCenter.
- Odebrání hostitele z clusteru vCenter.

## <a name="host-actions"></a>Akce hostitele

- Odebírání úložišť dat na hostiteli ESXi.
- Odinstalace agenta vCenter z hostitele.
- Mění se konfigurace hostitele.
- Provádění změn v hostitelských profilech.
- Umístění hostitele do režimu údržby.

## <a name="network-actions"></a>Síťové akce

- Odstraňuje se výchozí distribuovaný virtuální přepínač (DVS) v privátním cloudu.
- Odebírá se hostitel z výchozího služby DVS.
- Probíhá import nastavení pro Služba DVS.
- Probíhá změna konfigurace nastavení pro Služba DVS.
- Upgradujte libovolný Služba DVS.
- Odstraňuje se skupinu portů pro správu.
- Úprava skupinu portů pro správu.

## <a name="roles-and-permissions-actions"></a>Akce rolí a oprávnění

- Vytváří se globální role.
- Úprava nebo odstranění oprávnění pro všechny objekty správy.
- Úprava nebo odebrání všech výchozích rolí.
- Zvyšte oprávnění role na vyšší úroveň, než je role vlastníka cloudu.

## <a name="other-actions"></a>Další akce

- Odebírají se všechny výchozí licence:
  - vCenter Server
  - Uzly ESXi
  - NSX-T
  - HCX
- Úprava nebo odstranění fondu zdrojů správy.
- Klonování virtuálních počítačů pro správu


## <a name="next-steps"></a>Další kroky
[Údržba a aktualizace CloudSimple](cloudsimple-maintenance-updates.md) 
