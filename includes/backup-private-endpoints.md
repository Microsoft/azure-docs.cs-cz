---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81539337"
---
Pomocí [privátních koncových bodů](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) teď můžete data bezpečně zálohovat ze serverů ve virtuální síti do svého trezoru Recovery Services. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš trezor. Síťový provoz mezi prostředky uvnitř virtuální sítě a trezoru se přenáší přes virtuální síť a privátní odkaz na páteřní síť Microsoftu. Tím se eliminuje riziko z veřejného Internetu. Pomocí privátních koncových bodů se dají zálohovat a obnovovat databáze SQL a SAP HANA, které běží v rámci virtuálních počítačů Azure. Dá se taky použít pro vaše místní servery pomocí agenta MARS.

Zálohování virtuálních počítačů Azure nevyžaduje připojení k Internetu, takže nevyžadují privátní koncové body, aby bylo možné izolaci sítě.

>[!NOTE]
> Tato funkce je momentálně omezená a je dostupná v Středozápadní USA, Střed USA – jih, Západní USA 2 a Východní USA (dostupnost v dalších oblastech Azure, které se mají sledovat). Pokud vás zajímá použití privátních koncových bodů pro Azure Backup, vyplňte prosím [Tento průzkum](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) a pošlete nám e-mail. azbackupnetsec@microsoft.com Možnost používat tuto funkci podléhá schválení služby Azure Backup.
