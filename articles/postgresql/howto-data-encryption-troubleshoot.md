---
title: Šifrování dat pro Azure Database for PostgreSQL – řešení potíží s jedním serverem
description: Přečtěte si, jak řešit potíže s šifrováním dat pro váš Azure Database for PostgreSQL pro jeden server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 6d2e027c47bef3186f95c2183b316b5c15511070
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371498"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-postgresql---single-server"></a>Řešení potíží s šifrováním pomocí klíčů spravovaných zákazníkem v Azure Database for PostgreSQL na jednom serveru
Tento článek popisuje, jak identifikovat a vyřešit běžné problémy a chyby, ke kterým dochází na Azure Database for PostgreSQL jednom serveru s nakonfigurovaným šifrováním dat pomocí klíče spravovaného zákazníkem.

## <a name="introduction"></a>Úvod
Když je šifrování dat nakonfigurované tak, aby používalo klíč spravovaný zákazníkem v Azure Key Vault, je potřeba nepřetržitý přístup k tomuto klíči, aby server zůstal dostupný. Pokud server ztratí přístup k klíči spravovanému zákazníkem v Azure Key Vault, server začne odmítat všechna připojení s příslušnou chybovou zprávou a změní svůj stav na ***nepřístupný*** v Azure Portal.

Pokud už nepřístupný Azure Database for PostgreSQL jeden server nepotřebujete, můžete ho okamžitě odstranit, aby se vám přestaly náklady. Všechny ostatní akce na serveru se nepovolují, dokud nebude obnovený přístup do trezoru klíčů Azure a server bude dostupný znovu. Změna možnosti šifrování dat z ' Ano ' (spravované zákazníkem) na ' ne ' (spravovaného službou) na nepřístupovém serveru je také možné, pokud je server zašifrovaný pomocí spravovaného zákazníkem. Aby byl server dostupný, je nutné klíč znovu ověřit ručně. To je nezbytné k ochraně dat před neoprávněným přístupem, zatímco oprávnění k klíči spravovanému zákazníkem byla odvolána.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Běžné chyby způsobující, že server nebude přístupný

Většina problémů, ke kterým dochází při použití šifrování dat s Azure Key Vault, je způsobena některou z následujících chybových konfigurací –

Trezor klíčů není k dispozici nebo neexistuje.

* Trezor klíčů byl omylem odstraněn.
* Přerušovaná chyba sítě způsobí, že Trezor klíčů nebude k dispozici.

Nemáte oprávnění pro přístup k trezoru klíčů nebo tento klíč neexistuje.

* Klíč byl omylem odstraněn, zakázán nebo platnost klíče vypršela.
* Identita spravovaná instancí serveru Azure Database for PostgreSQL byla omylem odstraněna.
* Oprávnění udělená Azure Database for PostgreSQL spravované identitě klíčů nejsou dostatečná (nezahrnují získání, zabalení a rozbalení).
* Oprávnění pro Azure Database for PostgreSQL identitu s jedním serverem spravované instance byla odvolána nebo odstraněna.

## <a name="identify-and-resolve-common-errors"></a>Identifikace a řešení běžných chyb
### <a name="errors-on-the-key-vault"></a>Chyby v trezoru klíčů

#### <a name="disabled-key-vault"></a>Zakázaný Trezor klíčů
* AzureKeyVaultKeyDisabledMessage
* **Vysvětlení** : operaci nelze dokončit na serveru, protože Azure Key Vault klíč je zakázán.

#### <a name="missing-key-vault-permissions"></a>Chybí oprávnění trezoru klíčů.
* AzureKeyVaultMissingPermissionsMessage
* Server nemá potřebná oprávnění získat, zalamovat a zrušit zalomení pro Azure Key Vault oprávnění. Udělte objektu služby s ID všechna chybějící oprávnění.

### <a name="mitigation"></a>Omezení rizik
* Ověřte, že Key Vault klíč spravovaný zákazníkem:
* Identifikujte Trezor klíčů a potom v Azure Portalu přejít na Trezor klíčů.
* Ujistěte se, že je přítomen klíč identifikovaný identifikátorem URI klíče.


## <a name="next-steps"></a>Další kroky
[Pomocí Azure Portal můžete nastavit šifrování dat pomocí klíče spravovaného zákazníkem pro vaši službu Azure Database for PostgreSQL](howto-data-encryption-portal.md).