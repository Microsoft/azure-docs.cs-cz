---
title: Integrita a zabezpečení platformy Azure – zabezpečení Azure
description: Technický přehled integrity a zabezpečení platformy Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557731"
---
# <a name="platform-integrity-and-security-overview"></a>Základní informace o integritě platformy a zabezpečení
Flotila Azure se skládá z milionů serverů (hostitelů) s tisíci přidanými každý den. Tisíce hostitelů se také pravidelně procházejí každodenní správou prostřednictvím restartování, aktualizací operačního systému nebo oprav. Předtím, než se hostitel může připojit k vozovému parku a zahájit přijímání zákaznických úloh, Microsoft ověří, že je hostitel v zabezpečeném a důvěryhodném stavu. Toto ověření zajišťuje, že v rámci spouštěcího řetězce nebo pracovních postupů údržby nebyly k dispozici škodlivé nebo nechtěné změny součástí sekvence spouštění.

## <a name="securing-azure-hardware-and-firmware"></a>Zabezpečení hardwaru a firmwaru Azure
Tato série článků popisuje, jak Microsoft zajišťuje integritu a zabezpečení hostitelů v různých fázích životního cyklu, od výroby po západ slunce. Adresa článků:
 
- [Zabezpečení firmwaru](firmware.md)
- [Zabezpečené spouštění UEFI](secure-boot.md)
- [Měřené ověření spouštění a ověřování hostitele](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Šifrování neaktivních uložených dat](encryption-atrest.md)
- [Zabezpečení hypervisoru](hypervisor.md)
 
## <a name="next-steps"></a>Další kroky

- Naučte se, jak Microsoft aktivně spolupracuje s cloudovým hardwarovým ekosystémem k [zajištění vylepšení zabezpečení](firmware.md)pro průběžné firmware.

- Pochopte svou [sdílenou odpovědnost v cloudu](shared-responsibility.md).