---
title: Poradce při potížích s šifrováním dat – Azure Database for MySQL
description: Zjistěte, jak řešit potíže s šifrováním dat v Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 42956d115590fd322d2851fd546c505a76a851fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297036"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>Poradce při potížích s šifrováním dat v Azure Database for MySQL

Tento článek popisuje, jak identifikovat a vyřešit běžné problémy, které mohou nastat v Azure Database for MySQL při konfiguraci s šifrováním dat pomocí klíče spravovaného zákazníkem.

## <a name="introduction"></a>Úvod

Při konfiguraci šifrování dat pro použití klíče spravovaného zákazníkem v azure key vault, servery vyžadují nepřetržitý přístup ke klíči. Pokud server ztratí přístup ke klíči spravovanému zákazníkem v azure key vault, odepře všechna připojení, vrátí příslušnou chybovou zprávu a změní jeho stav na ***Nepřístupný*** na webu Azure Portal.

Pokud už nepotřebujete nedostupnou databázi Azure pro server MySQL, můžete ji odstranit a zastavit náklady. Žádné další akce na serveru nejsou povoleny, dokud nebude obnoven přístup k trezoru klíčů a dokud nebude server k dispozici. Není také možné změnit možnost šifrování dat `Yes`z (spravované `No` zákazníkem) na (spravované službou) na nepřístupném serveru, když je zašifrována pomocí klíče spravovaného zákazníkem. Před opětovnou dostupností serveru bude nutné znovu ověřit platnost klíče. Tato akce je nezbytná k ochraně dat před neoprávněným přístupem, zatímco oprávnění ke klíči spravovanému zákazníkem jsou odvolána.

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>Běžné chyby, které způsobují, že se server stane nepřístupným

Následující chybné konfigurace způsobují většinu problémů s šifrováním dat, které používají klíče Azure Key Vault:

- Trezor klíčů není k dispozici nebo neexistuje:
  - Trezor klíčů byl omylem odstraněn.
  - Občasná chyba sítě způsobí, že trezor klíčů nebude k dispozici.

- Nemáte oprávnění k přístupu k trezoru klíčů nebo klíč neexistuje:
  - Platnost klíče vypršela nebo byl omylem odstraněn nebo zakázán.
  - Spravovaná identita instance Azure Database for MySQL byla omylem odstraněna.
  - Spravovaná identita instance Azure Database for MySQL nemá dostatečná oprávnění klíče. Oprávnění například nezahrnují get, wrap a unwrap.
  - Oprávnění spravované identity k instanci Azure Database for MySQL byla odvolána nebo odstraněna.

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

[Nastavení šifrování dat pomocí klientem spravovaného klíče v Azure Database for MySQL pomocí portálu Azure Portal Portal](howto-data-encryption-portal.md)
