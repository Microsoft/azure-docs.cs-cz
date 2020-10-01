---
title: Azure Key Vault dostupnost a redundance – Azure Key Vault | Microsoft Docs
description: Přečtěte si o dostupnosti Azure Key Vault a redundanci.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 3cc4bdc0fabd9d1e209634a88bed1bf063db917c
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597877"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Dostupnost a redundance služby Azure Key Vault

Azure Key Vault funkce více vrstev redundance, aby se zajistilo, že vaše klíče a tajné kódy zůstanou k dispozici pro vaši aplikaci, i když jednotlivé komponenty služby selžou.

> [!NOTE]
> Tato příručka se vztahuje na trezory. Spravované fondy HSM používají jiný model vysoké dostupnosti a zotavení po havárii. Další informace najdete v tématu [spravovaná příručka pro zotavení po havárii HSM](../managed-hsm/disaster-recovery-guide.md) .

Obsah trezoru klíčů se replikuje v rámci oblasti a do sekundární oblasti minimálně 150 kilometrů, ale v rámci stejné geografické úrovně zachovává vysokou odolnost klíčů a tajných kódů. Podrobnosti o dvojicích konkrétních oblastí najdete v dokumentu s [spárovanými](../../best-practices-availability-paired-regions.md) oblastmi Azure.

Pokud jednotlivé komponenty v rámci služby trezoru klíčů selžou, alternativní komponenty v rámci této oblasti v rámci vaší žádosti budou sloužit k tomu, aby se zajistilo, že nedojde k žádnému zhoršení funkčnosti. Pro spuštění tohoto procesu nemusíte provádět žádnou akci, k tomu dochází automaticky a pro vás bude transparentní.

V vzácných událostech, že celá oblast Azure není k dispozici, jsou požadavky, které provedete Azure Key Vault v této oblasti, automaticky směrovány (*převzetí*služeb při selhání) do sekundární oblasti. Pokud je primární oblast opět k dispozici, požadavky se přesměrují zpět (po*obnovení*) do primární oblasti. Znovu nemusíte provádět žádnou akci, protože k tomu dochází automaticky.

Tento návrh vysoké dostupnosti Azure Key Vault nevyžaduje pro aktivity údržby žádné výpadky.

Je potřeba mít na paměti několik upozornění:

* V případě převzetí služeb při selhání v oblasti může trvat několik minut, než služba převezme služby při selhání. Žádosti, které se provedou během této doby před selháním, může selhat.
* Pokud používáte privátní propojení pro připojení k trezoru klíčů, může trvat až 20 minut, než se připojení znovu naváže v případě převzetí služeb při selhání. 
* V průběhu převzetí služeb při selhání je váš Trezor klíčů v režimu jen pro čtení. Požadavky, které jsou podporovány v tomto režimu:
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
