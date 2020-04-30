---
title: Azure Key Vault přesunu trezoru do jiné oblasti | Microsoft Docs
description: Pokyny k přesunu trezoru klíčů do jiné oblasti.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254145"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>Přesunutí Azure Key Vault napříč oblastmi

## <a name="overview"></a>Přehled

Key Vault nepodporuje operaci přesunutí prostředku, která umožňuje přesun trezoru klíčů do jiné oblasti. Tento článek se zabývá dalšími řešeními, pokud potřebujete přesunout Trezor klíčů do jiné oblasti. Každá z možností má omezení a je důležité pochopit důsledky těchto řešení před jejich pokusem v produkčním prostředí.

Pokud potřebujete přesunout Trezor klíčů do jiné oblasti, řešení je vytvoření nového trezoru klíčů v požadované oblasti a ručnímu zkopírování každého jednotlivého tajného klíče ze stávajícího trezoru klíčů do nového trezoru klíčů. Tuto operaci lze provést v jednom z následujících způsobů uvedených níže.

## <a name="design-considerations"></a>Aspekty návrhu

* Názvy Key Vault jsou globálně jedinečné. Nebudete moct znovu použít stejný název trezoru.

* V novém trezoru klíčů budete muset znovu nakonfigurovat zásady přístupu a nastavení konfigurace sítě.

* V novém trezoru klíčů budete muset znovu nakonfigurovat ochranu obnovitelného odstranění a vyprázdnění.

* Operace zálohování a obnovení nezachová nastavení automatické rotace. možná budete muset tato nastavení změnit.

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>Možnost 1 – použití příkazů zálohování a obnovení trezoru klíčů

Každý jednotlivý tajný klíč, klíč a certifikát můžete zálohovat v trezoru pomocí příkazu Backup. Vaše tajné kódy se stáhnou jako zašifrovaný objekt BLOB. Pak můžete objekt BLOB obnovit do nového trezoru klíčů. Příkazy jsou popsány v následujícím odkazu.

[Příkazy Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>Omezení

* Trezor klíčů nemůžete zálohovat v jedné zeměpisné oblasti a obnovit ho do jiné geografické oblasti. Přečtěte si další informace o geografických oblastech Azure. [Odkaz](https://azure.microsoft.com/global-infrastructure/geographies/)

* Příkaz Backup Zálohuje všechny verze všech tajných kódů. Pokud máte tajný klíč s velkým počtem předchozích verzí (větší než 10), může požadavek přesáhnout maximální povolenou velikost žádosti a operace se nemusí zdařit.

## <a name="option-2---manually-download-and-upload-secrets"></a>Možnost 2 – Ruční stažení a nahrání tajných kódů

Některé typy tajných kódů je možné stáhnout ručně. Certifikáty můžete například stáhnout jako soubor. pfx. Tato možnost eliminuje Zeměpisná omezení u některých tajných typů, jako jsou například certifikáty. Soubory. pfx můžete nahrát do libovolného trezoru klíčů v libovolné oblasti. Váš tajný kód se stáhne ve formátu, který není chráněný heslem. Za zabezpečení tajných kódů zodpovídáte po opuštění Key Vault, zatímco se přesun provádí.
