---
title: Co dělat v případě, že přerušení služby Azure ovlivňuje Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Přečtěte si, co je potřeba při přerušení služby Azure ovlivnit Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: sudbalas
ms.openlocfilehash: e4364c3c5bcba1a04837f3f9e7a0576579211c72
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88796591"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Dostupnost a redundance služby Azure Key Vault

Azure Key Vault funkce více vrstev redundance, aby se zajistilo, že vaše klíče a tajné kódy zůstanou k dispozici pro vaši aplikaci, i když jednotlivé komponenty služby selžou.

Obsah trezoru klíčů se replikuje v rámci oblasti a do sekundární oblasti minimálně 150 kilometrů, ale v rámci stejné geografické úrovně zachovává vysokou odolnost klíčů a tajných kódů. Podrobnosti o dvojicích konkrétních oblastí najdete v dokumentu s [spárovanými](../../best-practices-availability-paired-regions.md) oblastmi Azure.

Pokud jednotlivé komponenty v rámci služby trezoru klíčů selžou, alternativní komponenty v rámci této oblasti v rámci vaší žádosti budou sloužit k tomu, aby se zajistilo, že nedojde k žádnému zhoršení funkčnosti. Ke spuštění tohoto procesu není nutné provádět žádnou akci, probíhá automaticky a bude pro vás transparentní.

V vzácných událostech, že celá oblast Azure není k dispozici, jsou požadavky, které provedete Azure Key Vault v této oblasti, automaticky směrovány (*převzetí*služeb při selhání) do sekundární oblasti. Pokud je primární oblast opět k dispozici, požadavky se přesměrují zpět (po*obnovení*) do primární oblasti. Znovu nemusíte provádět žádnou akci, protože k tomu dochází automaticky.

Tento návrh vysoké dostupnosti Azure Key Vault nevyžaduje pro aktivity údržby žádné výpadky.

Je potřeba mít na paměti několik upozornění:

* V případě převzetí služeb při selhání v oblasti může trvat několik minut, než služba převezme služby při selhání. Žádosti, které se provedou během této doby před převzetím služeb při selhání, můžou selhat.
* V průběhu převzetí služeb při selhání je váš Trezor klíčů v režimu jen pro čtení. Požadavky, které jsou podporovány v tomto režimu:
  * Vypsat trezory klíčů
  * Získat vlastnosti trezorů klíčů
  * Výpis certifikátů
  * Získat certifikáty
  * Výpis tajných kódů
  * Získání tajných kódů
  * Zobrazit seznam klíčů
  * Získat klíče (vlastnosti)
  * Šifrování
  * Dešifrování
  * Balí
  * Rozbalení
  * Ověření
  * Znaménko
  * Backup
* Během převzetí služeb při selhání nebudete moct provádět změny vlastností trezoru klíčů. Nebudete moct měnit zásady přístupu ani konfigurace a nastavení brány firewall.
* Po převzetí služeb při selhání se vrátí všechny typy požadavků (včetně požadavků *na čtení a* zápis).

