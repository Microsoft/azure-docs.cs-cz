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
ms.openlocfilehash: 27184e267bb0472dad6fc9176dfdeee68d5eae58
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611816"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Dostupnost a redundance služby Azure Key Vault

Azure Key Vault funkce více vrstev redundance, aby se zajistilo, že vaše klíče a tajné kódy zůstanou k dispozici pro vaši aplikaci, i když jednotlivé komponenty služby selžou.

> [!NOTE]
> Tato příručka se vztahuje na trezory. Spravované fondy HSM používají jiný model vysoké dostupnosti a zotavení po havárii. Další informace najdete v tématu [spravovaná příručka pro zotavení po havárii HSM](../managed-hsm/disaster-recovery-guide.md) .

Obsah trezoru klíčů se replikuje v rámci oblasti a do sekundární oblasti minimálně 150 km, ale v rámci stejné geografické úrovně zachovává vysokou odolnost klíčů a tajných kódů. Podrobnosti o dvojicích konkrétních oblastí najdete v tématu [spárované oblasti Azure](../../best-practices-availability-paired-regions.md). Výjimka pro model spárovaných oblastí je Brazílie – jih, která umožňuje, aby se data v oblasti Brazílie – jih zajistila pouze možností. Brazílie – jih používá redundantní úložiště zóny (ZRS) k replikaci dat třikrát v jednom umístění nebo oblasti. Pro integrace Premium se k replikaci dat z modulu HSM používá jenom 2 ze 3 oblastí.  

Pokud jednotlivé komponenty v rámci služby trezoru klíčů selžou, alternativní komponenty v rámci této oblasti v rámci vaší žádosti budou sloužit k tomu, aby se zajistilo, že nedojde k žádnému zhoršení funkčnosti. Pro spuštění tohoto procesu nemusíte provádět žádnou akci, k tomu dochází automaticky a pro vás bude transparentní.

V vzácných událostech, které nejsou k dispozici v celé oblasti Azure, jsou požadavky, které jste provedli Azure Key Vault v této oblasti, automaticky směrovány (*převzetí* služeb při selhání) do sekundární oblasti s výjimkou případů Brazílie v oblasti Brazílie – jih. Pokud je primární oblast opět k dispozici, požadavky se přesměrují zpět (po *obnovení*) do primární oblasti. Znovu nemusíte provádět žádnou akci, protože k tomu dochází automaticky.

V oblasti Brazílie – jih musíte naplánovat obnovení vašich trezorů klíčů Azure ve scénáři selhání oblasti. Pokud chcete zálohovat a obnovit Trezor klíčů Azure do libovolné oblasti podle vašeho výběru, proveďte kroky popsané v části [Azure Key Vault Backup](backup.md). 

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
