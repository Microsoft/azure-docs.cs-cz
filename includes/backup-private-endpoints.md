---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137020"
---
Pomocí [privátních koncových bodů](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) teď můžete data bezpečně zálohovat ze serverů ve virtuální síti do svého trezoru Recovery Services. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš trezor. Síťový provoz mezi prostředky uvnitř virtuální sítě a trezoru se přenáší přes virtuální síť a privátní odkaz na páteřní síť Microsoftu. Tím se eliminuje riziko z veřejného Internetu. Pomocí privátních koncových bodů se dají zálohovat a obnovovat databáze SQL a SAP HANA, které běží v rámci virtuálních počítačů Azure. Dá se taky použít pro vaše místní servery pomocí agenta MARS.

Zálohování virtuálních počítačů Azure nevyžaduje připojení k Internetu, takže nevyžadují privátní koncové body, aby bylo možné izolaci sítě.

>[!NOTE]
> Tato funkce je aktuálně v brzkém použití. Pokud vás zajímá používání privátních koncových bodů pro Azure Backup, vyplňte prosím [Tento průzkum](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) a pošlete nám e-mail na azbackupnetsec@microsoft.com. Možnost používat tuto funkci podléhá schválení služby Azure Backup.
