---
title: Řešení potíží s šifrováním dat – Azure Database for PostgreSQL – jeden server
description: Přečtěte si, jak řešit potíže s šifrováním dat na vašem serveru Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: c315e1df473f3d23bab7e2a78ce166f22272ee70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242241"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Řešení potíží s šifrováním dat v Azure Database for PostgreSQL – jeden server

Tento článek vám pomůže identifikovat a vyřešit běžné problémy, ke kterým může dojít při nasazení Azure Database for PostgreSQL na jednom serveru, když se nakonfigurují pomocí šifrování dat pomocí klíče spravovaného zákazníkem.

## <a name="introduction"></a>Úvod

Když nakonfigurujete šifrování dat pro použití klíče spravovaného zákazníkem v Azure Key Vault, server vyžaduje nepřetržitý přístup k tomuto klíči. Pokud server ztratí přístup k klíči spravovanému zákazníkem v Azure Key Vault, bude odepřít všechna připojení, vracet příslušnou chybovou zprávu a změnit její stav na ***nepřístupný*** v Azure Portal.

Pokud už nepotřebujete nedostupný Azure Database for PostgreSQL Server, můžete ho odstranit, abyste se přestali náklady. Žádné další akce na serveru nejsou povoleny, dokud nebude obnoven přístup k trezoru klíčů a server je k dispozici. Není také možné změnit možnost šifrování dat z `Yes` (spravované zákazníkem) na `No` (spravované na základě služby) na nepřístupovém serveru, když je zašifrovaný pomocí klíče spravovaného zákazníkem. Abyste mohli znovu získat přístup k serveru, budete muset klíč znovu ověřit ručně. Tato akce je nutná k ochraně dat před neoprávněným přístupem, zatímco oprávnění k klíči spravovanému zákazníkem jsou odvolána.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Běžné chyby způsobující, že server nebude přístupný

U následujících chybných konfigurací dojde u šifrování dat, které používá Azure Key Vault klíče:

- Trezor klíčů není k dispozici nebo neexistuje:
  - Trezor klíčů byl omylem odstraněn.
  - Přerušovaná chyba sítě způsobí, že Trezor klíčů nebude k dispozici.

- Nemáte oprávnění pro přístup k trezoru klíčů nebo tento klíč neexistuje:
  - Platnost klíče vypršela nebo byla omylem odstraněna nebo zakázána.
- Spravovaná identita instance Azure Database for PostgreSQL byla omylem odstraněna.
  - Spravovaná identita instance Azure Database for PostgreSQL má nedostatečná oprávnění klíče. Například oprávnění nezahrnují Get, Wrap a Unwrap.
  - Oprávnění spravované identity pro instanci Azure Database for PostgreSQL byla odvolána nebo odstraněna.

## <a name="identify-and-resolve-common-errors"></a>Identifikace a řešení běžných chyb

### <a name="errors-on-the-key-vault"></a>Chyby v trezoru klíčů

#### <a name="disabled-key-vault"></a>Zakázaný Trezor klíčů

- `AzureKeyVaultKeyDisabledMessage`
- **Vysvětlení**: operaci nelze dokončit na serveru, protože klíč Azure Key Vault je zakázán.

#### <a name="missing-key-vault-permissions"></a>Chybí oprávnění trezoru klíčů.

- `AzureKeyVaultMissingPermissionsMessage`
- **Vysvětlení**: Server nemá požadovaná oprávnění k získání, zabalení a rozbalení Azure Key Vault. Udělte objektu služby s ID všechna chybějící oprávnění.

### <a name="mitigation"></a>Omezení rizik

- Zkontrolujte, jestli je v trezoru klíčů přítomen klíč spravovaný zákazníkem.
- Identifikujte Trezor klíčů a potom v Azure Portalu přejít na Trezor klíčů.
- Ujistěte se, že identifikátor URI klíče identifikuje klíč, který je k dispozici.

## <a name="next-steps"></a>Další kroky

[Pomocí Azure Portal můžete nastavit šifrování dat pomocí klíče spravovaného zákazníkem v Azure Database for PostgreSQL](howto-data-encryption-portal.md)
