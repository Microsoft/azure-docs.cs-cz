---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: fd7124ad83a446d7dde39a836c337a97b12a0a0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560650"
---
Pomocí [privátních koncových bodů](../articles/private-link/private-endpoint-overview.md) teď můžete data bezpečně zálohovat ze serverů ve virtuální síti do svého trezoru Recovery Services. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš trezor. Síťový provoz mezi prostředky uvnitř virtuální sítě a trezoru se přenáší přes virtuální síť a privátní odkaz na páteřní síť Microsoftu. Tím se eliminuje riziko z veřejného Internetu. Pomocí privátních koncových bodů se dají zálohovat a obnovovat databáze SQL a SAP HANA, které běží v rámci virtuálních počítačů Azure. Dá se taky použít pro vaše místní servery pomocí agenta MARS.

Zálohování virtuálních počítačů Azure nevyžaduje připojení k Internetu, takže nevyžadují privátní koncové body, aby bylo možné izolaci sítě.

[Tady](../articles/backup/private-endpoints.md)si můžete přečíst další informace o privátních koncových bodech pro Azure Backup.