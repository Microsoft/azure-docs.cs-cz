---
title: Zabezpečení firmwaru – zabezpečení Azure
description: Přečtěte si, jak Microsoft zabezpečuje hardware a firmware Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557719"
---
# <a name="firmware-security"></a>Zabezpečení firmwaru
Tento článek popisuje, jak Microsoft zabezpečuje Cloud hardwarového ekosystému a dodavatelské řetězce.

## <a name="securing-the-cloud-hardware-ecosystem"></a>Zabezpečení ekosystému cloudového hardwaru
Společnost Microsoft aktivně spolupracuje s cloudovým hardwarovým ekosystémem a poskytuje tak průběžné vylepšení zabezpečení podle těchto součástí:

- Spolupráce s partnery hardwaru a firmwaru Azure (jako jsou výrobci komponent a systémové integrátory), aby splňovala požadavky na zabezpečení hardwaru a firmwaru v Azure.

- Díky tomu, aby partneři mohli provádět průběžné posuzování a vylepšování stav zabezpečení svých produktů pomocí požadavků definovaných Microsoftem v oblastech, jako jsou:

  - Zabezpečené spouštění firmwaru
  - Zabezpečené obnovení firmwaru
  - Zabezpečená aktualizace firmwaru
  - Kryptografie firmwaru
  - Zamčený hardware
  - Detailní telemetrie ladění
  - Systémová podpora hardwaru TPM 2,0 pro povolení měřeného spouštění

- Zapojení a přispívání k projektu zabezpečení [Open COMPUTE Project (OCP)](https://www.opencompute.org/wiki/Security) prostřednictvím vývoje specifikací. Specifikace podporují konzistenci a přehlednost pro bezpečný návrh a architekturu v ekosystému.

   > [!NOTE]
   > Příkladem našeho příspěvku k projektu OCP Security je specifikace [zabezpečeného spuštění hardwaru](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) .

## <a name="securing-hardware-and-firmware-supply-chains"></a>Zabezpečení dodavatelských řetězců hardwaru a firmwaru
U cloudových hardwarových dodavatelů a dodavatelů pro Azure je také nutné dodržovat postupy zabezpečení dodavatelského řetězce a požadavky vyvinuté společností Microsoft. Procesy pro vývoj a nasazování hardwaru a firmwaru se vyžadují, aby se sledovaly procesy SDL (Microsoft [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl) ), jako je například:

- Modelování hrozeb
- Bezpečnostní recenze pro návrhy
- Revize firmwaru a testování průniku
- Zabezpečená sestavení a testovací prostředí
- Správa ohrožení zabezpečení a reakce na incidenty

## <a name="next-steps"></a>Další kroky
Další informace o tom, co máme k zajištění integrity a zabezpečení platformy, najdete v těchto tématech:

- [Zabezpečené spouštění](secure-boot.md)
- [Měřené ověření spouštění a ověřování hostitele](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Šifrování neaktivních uložených dat](encryption-atrest.md)
- [Zabezpečení hypervisoru](hypervisor.md)