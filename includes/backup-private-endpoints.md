---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539337"
---
Nyní můžete použít [soukromé koncové body](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) k bezpečnému zálohování dat ze serverů uvnitř virtuální sítě do trezoru služby Recovery Services. Soukromý koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš trezor. Síťový provoz mezi prostředky uvnitř virtuální sítě a trezoru se pohybuje přes vaši virtuální síť a privátní odkaz v páteřní síti Microsoftu. To eliminuje expozici z veřejného internetu. Privátní koncové body se můžou použít k zálohování a obnovení databází SQL a SAP HANA, které běží uvnitř virtuálních počítačů Azure. Lze jej také použít pro místní servery pomocí agenta MARS.

Zálohování virtuálních počítačových služeb Azure nevyžaduje připojení k internetu, a proto nevyžaduje privátní koncové body, které umožňují izolaci sítě.

>[!NOTE]
> Tato funkce je v současné době v omezené dostupnosti a je k dispozici v západní střední USA, jižní střední USA, západní USA 2 a východní USA (dostupnost v jiných oblastech Azure následovat). Vyplňte [tento průzkum](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) a azbackupnetsec@microsoft.com pošlete nám e-mail, pokud máte zájem o použití privátní koncové body pro Azure Backup. Možnost používat tuto funkci podléhá schválení službou Azure Backup.
