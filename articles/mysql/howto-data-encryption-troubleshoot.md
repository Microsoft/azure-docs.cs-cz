---
title: Řešení potíží s šifrováním dat – Azure Database for MySQL
description: Přečtěte si, jak řešit potíže s šifrováním dat v Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: 95b5a7650e0990f13149daeed87da8e261ec37e4
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241119"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>Řešení potíží s šifrováním dat v Azure Database for MySQL

Tento článek popisuje, jak identifikovat a vyřešit běžné problémy, ke kterým může dojít v Azure Database for MySQL, pokud je nakonfigurovaná pomocí šifrování dat pomocí klíče spravovaného zákazníkem.

## <a name="introduction"></a>Úvod

Když nakonfigurujete šifrování dat pro použití klíče spravovaného zákazníkem v Azure Key Vault, vyžadují servery nepřetržitý přístup k tomuto klíči. Pokud server ztratí přístup k klíči spravovanému zákazníkem v Azure Key Vault, bude odepřít všechna připojení, vracet příslušnou chybovou zprávu a změnit její stav na * **nedostupné** _ v Azure Portal.

Pokud už nepotřebujete nedostupný Azure Database for MySQL server, můžete ho odstranit, abyste se přestali náklady. Žádné další akce na serveru nejsou povoleny, dokud nebude obnoven přístup k trezoru klíčů a server je k dispozici. Není také možné změnit možnost šifrování dat z `Yes` (spravované zákazníkem) na `No` (spravované na základě služby) na nepřístupovém serveru, když je zašifrovaný pomocí klíče spravovaného zákazníkem. Abyste mohli znovu získat přístup k serveru, budete muset klíč znovu ověřit ručně. Tato akce je nutná k ochraně dat před neoprávněným přístupem, zatímco oprávnění k klíči spravovanému zákazníkem jsou odvolána.

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>Běžné chyby, které způsobí, že server nebude přístupný

U následujících chybných konfigurací dojde u šifrování dat, které používá Azure Key Vault klíče:

- Trezor klíčů není k dispozici nebo neexistuje:
  - Trezor klíčů byl omylem odstraněn.
  - Přerušovaná chyba sítě způsobí, že Trezor klíčů nebude k dispozici.

- Nemáte oprávnění pro přístup k trezoru klíčů nebo tento klíč neexistuje:
  - Platnost klíče vypršela nebo byla omylem odstraněna nebo zakázána.
  - Spravovaná identita instance Azure Database for MySQL byla omylem odstraněna.
  - Spravovaná identita instance Azure Database for MySQL má nedostatečná oprávnění klíče. Například oprávnění nezahrnují Get, Wrap a Unwrap.
  - Oprávnění spravované identity pro instanci Azure Database for MySQL byla odvolána nebo odstraněna.

## <a name="identify-and-resolve-common-errors"></a>Identifikace a řešení běžných chyb

### <a name="errors-on-the-key-vault"></a>Chyby v trezoru klíčů

#### <a name="disabled-key-vault"></a>Zakázaný Trezor klíčů

- `AzureKeyVaultKeyDisabledMessage`
- _ * Vysvětlení * *: operace se na serveru nedala dokončit, protože Azure Key Vault klíč je zakázaný.

#### <a name="missing-key-vault-permissions"></a>Chybí oprávnění trezoru klíčů.

- `AzureKeyVaultMissingPermissionsMessage`
- **Vysvětlení** : Server nemá požadovaná oprávnění k získání, zabalení a rozbalení Azure Key Vault. Udělte objektu služby s ID všechna chybějící oprávnění.

### <a name="mitigation"></a>Omezení rizik

- Zkontrolujte, jestli je v trezoru klíčů přítomen klíč spravovaný zákazníkem.
- Identifikujte Trezor klíčů a potom v Azure Portalu přejít na Trezor klíčů.
- Ujistěte se, že identifikátor URI klíče identifikuje klíč, který je k dispozici.

## <a name="next-steps"></a>Další kroky

[Pomocí Azure Portal můžete nastavit šifrování dat pomocí klíče spravovaného zákazníkem v Azure Database for MySQL](howto-data-encryption-portal.md)
