---
title: Integrita firmwaru – zabezpečení Azure
description: Přečtěte si o kryptografických měřeních pro zajištění integrity firmwaru.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557668"
---
# <a name="project-cerberus"></a>Project Cerberus

Cerberus je kořenovým hardwarem kompatibilním s NIST 800-193 a identitou, kterou nelze klonovat. Cerberus je navržený tak, aby lépe vyvolal zabezpečení stav infrastruktury Azure tím, že poskytuje silnou kotvu vztahu důvěryhodnosti pro integritu firmwaru.

## <a name="enabling-an-anchor-of-trust"></a>Povolení kotvy vztahu důvěryhodnosti
Každý čip Cerberus má jedinečnou kryptografickou identitu, která je vytvořena pomocí podepsaného řetězu certifikátů, který je rootem certifikační autority (CA) společnosti Microsoft. Měření získaná z Cerberus lze použít k ověření integrity součástí, jako například:

- Hostitel
- Řadič pro správu základní desky (BMC)
- Všechna periferní zařízení, včetně karty síťového rozhraní a [systému založeného na čipu](https://en.wikipedia.org/wiki/System_on_a_chip) (SOC)

Tato kotva důvěry pomáhá chránit firmware platforem od:

- Napadené binární soubory firmwaru spuštěné na platformě
- Malware a hackeři, kteří využívají chyby v operačním systému, aplikaci nebo hypervisoru
- Určité typy útoků dodavatelského řetězce (výroba, sestavení, tranzit)
- Zlomyslné Insider s oprávněními správce nebo přístup k hardwaru

## <a name="cerberus-attestation"></a>Ověření identity Cerberus
Cerberus ověřuje integritu firmwaru pro součásti serveru pomocí manifestu firmwaru platformy (PFM). PFM definuje seznam autorizovaných verzí firmwaru a poskytuje měření platformy pro [službu ověřování hostitelů](measured-boot-host-attestation.md)Azure. Služba ověření identity hostitele ověřuje měření a umožňuje určit, že se mají jenom Důvěryhodní hostitelé připojovat k Azure loďstva a hostovat úlohy pro zákazníky.

Ve spojení se službou ověření identity hostitele Cerberus ' možnosti rozšíření a zvýšení úrovně vysoce zabezpečené produkční infrastruktury Azure.

> [!NOTE]
> Další informace najdete v článku informace o [Cerberus projektu](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) na GitHubu.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co máme k zajištění integrity a zabezpečení platformy, najdete v těchto tématech:

- [Zabezpečení firmwaru](firmware.md)
- [Zabezpečené spouštění](secure-boot.md)
- [Měřené ověření spouštění a ověřování hostitele](measured-boot-host-attestation.md)
- [Šifrování neaktivních uložených dat](encryption-atrest.md)
- [Zabezpečení hypervisoru](hypervisor.md)