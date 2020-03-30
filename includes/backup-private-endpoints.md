---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137020"
---
Nyní můžete použít [soukromé koncové body](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) k bezpečnému zálohování dat ze serverů uvnitř virtuální sítě do trezoru služby Recovery Services. Soukromý koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš trezor. Síťový provoz mezi prostředky uvnitř virtuální sítě a trezoru se pohybuje přes vaši virtuální síť a privátní odkaz v páteřní síti Microsoftu. To eliminuje expozici z veřejného internetu. Privátní koncové body se můžou použít k zálohování a obnovení databází SQL a SAP HANA, které běží uvnitř virtuálních počítačů Azure. Lze jej také použít pro místní servery pomocí agenta MARS.

Zálohování virtuálních počítačových služeb Azure nevyžaduje připojení k internetu, a proto nevyžaduje privátní koncové body, které umožňují izolaci sítě.

>[!NOTE]
> Tato funkce je v současné době v raném provozu. Vyplňte [tento průzkum](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) a azbackupnetsec@microsoft.com pošlete nám e-mail, pokud máte zájem o použití privátní koncové body pro Azure Backup. Možnost používat tuto funkci podléhá schválení službou Azure Backup.
