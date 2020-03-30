---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175447"
---
| Port č.| Dovnitř nebo ven | Rozsah portu| Požaduje se|   Poznámky |   |
|--------|-----|-----|-----------|----------|-----------|
| Protokol TCP 80 (HTTP)|Out|Síť WAN |Ne|Odchozí port se používá pro přístup k Internetu k načtení aktualizací. <br>Odchozí webový proxy server lze konfigurovat uživatelem. |
| Protokol TCP 443 (HTTPS)|Out|Síť WAN|Ano|Odchozí port se používá pro přístup k datům v cloudu.<br>Odchozí webový proxy server lze konfigurovat uživatelem.|
| UDP 123 (NTP)|Out|Síť WAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že používáte internetový server NTP.  |   
| UDP 53 (DNS)|Out|Síť WAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že používáte internetový server DNS.<br>Doporučujeme používat místní DNS server. |
| TCP 5985 (WinRM)|Ven/Dovnitř|LAN|V některých případech<br>Zobrazit poznámky|Tento port je nutný pro připojení k zařízení prostřednictvím vzdáleného prostředí PowerShell přes PROTOKOL HTTP.  |
| UDP 67 (DHCP)|Out|LAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že používáte místní server DHCP.  |
| Protokol TCP 80 (HTTP)|Ven/Dovnitř|LAN|Ano|Tento port je vstupní port pro místní rozhraní v zařízení pro místní správu. <br>Přístup k místnímu ui přes HTTP se automaticky přesměruje na protokol HTTPS.  |
| Protokol TCP 443 (HTTPS)|Ven/Dovnitř|LAN|Ano|Tento port je vstupní port pro místní rozhraní v zařízení pro místní správu. |
| TCP 445 (SMB)|V|LAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že se připojujete přes SMB. |
| TCP 2049 (NFS)|V|LAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že se připojujete prostřednictvím služby NFS. |
