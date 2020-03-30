---
title: Poradce při potížích s šifrováním dat – Databáze Azure pro PostgreSQL – jeden server
description: Zjistěte, jak řešit potíže s šifrováním dat v databázi Azure pro PostgreSQL – jeden server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2902ff17ac14a48f1a11259339c2ab1bc4595980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299256"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Poradce při potížích s šifrováním dat v databázi Azure pro PostgreSQL – jeden server

Tento článek vám pomůže identifikovat a vyřešit běžné problémy, které mohou nastat v jednoserverovém nasazení Azure Database for PostgreSQL při konfiguraci s šifrováním dat pomocí klíče spravovaného zákazníkem.

## <a name="introduction"></a>Úvod

Když nakonfigurujete šifrování dat tak, aby používal klíč spravovaný zákazníkem v úložišti klíčů Azure, server vyžaduje nepřetržitý přístup ke klíči. Pokud server ztratí přístup ke klíči spravovanému zákazníkem v azure key vault, odepře všechna připojení, vrátí příslušnou chybovou zprávu a změní jeho stav na ***Nepřístupný*** na webu Azure Portal.

Pokud už nepotřebujete nedostupnou databázi Azure pro postgreSQL server, můžete ji odstranit a zastavit náklady. Žádné další akce na serveru nejsou povoleny, dokud nebude obnoven přístup k trezoru klíčů a dokud nebude server k dispozici. Není také možné změnit možnost šifrování dat `Yes`z (spravované `No` zákazníkem) na (spravované službou) na nepřístupném serveru, když je zašifrována pomocí klíče spravovaného zákazníkem. Před opětovnou dostupností serveru bude nutné znovu ověřit platnost klíče. Tato akce je nezbytná k ochraně dat před neoprávněným přístupem, zatímco oprávnění ke klíči spravovanému zákazníkem jsou odvolána.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Běžné chyby způsobující nedostupnost serveru

Následující chybné konfigurace způsobují většinu problémů s šifrováním dat, které používají klíče Azure Key Vault:

- Trezor klíčů není k dispozici nebo neexistuje:
  - Trezor klíčů byl omylem odstraněn.
  - Občasná chyba sítě způsobí, že trezor klíčů nebude k dispozici.

- Nemáte oprávnění k přístupu k trezoru klíčů nebo klíč neexistuje:
  - Platnost klíče vypršela nebo byl omylem odstraněn nebo zakázán.
- Spravovaná identita instance Azure Database for PostgreSQL byla omylem odstraněna.
  - Spravovaná identita instance Azure Database for PostgreSQL nemá dostatečná oprávnění klíče. Oprávnění například nezahrnují get, wrap a unwrap.
  - Oprávnění spravované identity k instanci Azure Database for PostgreSQL byla odvolána nebo odstraněna.

## <a name="identify-and-resolve-common-errors"></a>Identifikace a řešení běžných chyb

### <a name="errors-on-the-key-vault"></a>Chyby v trezoru klíčů

#### <a name="disabled-key-vault"></a>Zakázaný trezor klíčů

- `AzureKeyVaultKeyDisabledMessage`
- **Vysvětlení:** Operaci nelze dokončit na serveru, protože klíč Azure Key Vault je zakázán.

#### <a name="missing-key-vault-permissions"></a>Chybějící oprávnění trezoru klíčů

- `AzureKeyVaultMissingPermissionsMessage`
- **Vysvětlení:** Server nemá požadovaná oprávnění Získat, Zalomit a Rozbalit do úložiště klíčů Azure. Udělte všechna chybějící oprávnění instančnímu objektu s ID.

### <a name="mitigation"></a>Omezení rizik

- Zkontrolujte, zda je klíč spravovaný zákazníkem přítomen v trezoru klíčů.
- Identifikujte trezor klíčů a přejděte do trezoru klíčů na webu Azure Portal.
- Ujistěte se, že identifikátor URI klíče identifikuje klíč, který je k dispozici.

## <a name="next-steps"></a>Další kroky

[Nastavení šifrování dat pomocí klíče spravovaného zákazníkem v Azure Database for PostgreSQL pomocí portálu Azure Portal](howto-data-encryption-portal.md)
