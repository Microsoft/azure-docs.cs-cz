---
title: Přesuňte Trezor klíčů do jiné oblasti – Azure Key Vault | Microsoft Docs
description: Tento článek obsahuje pokyny k přesunu trezoru klíčů do jiné oblasti.
services: key-vault
author: msmbaldwin
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: ac2f6347776c2f5d230065b80b1c0336e21e181c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751791"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Přesun trezoru klíčů Azure napříč oblastmi

Azure Key Vault nepodporuje operaci přesunutí prostředku, která povoluje přesun trezoru klíčů z jedné oblasti do druhé. Tento článek popisuje alternativní řešení pro organizace, které mají firmu, musí přesunout Trezor klíčů do jiné oblasti. Každá možnost alternativního řešení má omezení. Před pokusem o jejich použití v produkčním prostředí je důležité pochopit důsledky těchto řešení.

Pokud chcete přesunout Trezor klíčů do jiné oblasti, vytvořte v této jiné oblasti Trezor klíčů a pak ručně zkopírujte každý jednotlivý tajný klíč ze stávajícího trezoru klíčů do nového trezoru klíčů. Můžete to provést pomocí některé z následujících dvou možností.

## <a name="design-considerations"></a>Na co dát pozor při navrhování

Než začnete, pamatujte na následující koncepty:

* Názvy trezoru klíčů jsou globálně jedinečné. Nemůžete znovu použít název trezoru.
* V novém trezoru klíčů je nutné překonfigurovat zásady přístupu a nastavení konfigurace sítě.
* V novém trezoru klíčů je potřeba znovu nakonfigurovat ochranu po tichém odstranění a vyprázdnění.
* Operace zálohování a obnovení nezachová vaše nastavení automatického otočení. Možná budete muset změnit konfiguraci nastavení.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Možnost 1: použití příkazů zálohování a obnovení trezoru klíčů

Každý jednotlivý tajný klíč, klíč a certifikát můžete zálohovat v trezoru pomocí příkazu Backup. Vaše tajné kódy se stáhnou jako zašifrovaný objekt BLOB. Pak můžete objekt BLOB obnovit do nového trezoru klíčů. Seznam příkazů naleznete v tématu [Azure Key Vault Commands](/powershell/module/azurerm.keyvault#key_vault).

Použití příkazů Backup a Restore má dvě omezení:

* Trezor klíčů nemůžete zálohovat v jedné zeměpisné oblasti a obnovit ho do jiné geografické oblasti. Další informace najdete v tématu [geografických oblastí Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

* Příkaz backup zálohuje všechny verze jednotlivých tajných klíčů. Pokud máte tajný klíč s velkým počtem předchozích verzí (více než 10), velikost požadavku může překročit maximální povolenou velikost a operace může selhat.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Možnost 2: ruční stažení a nahrání tajných kódů trezoru klíčů

Určité typy tajných kódů si můžete stáhnout ručně. Certifikáty můžete například stáhnout jako soubor PFX. Tato možnost eliminuje Zeměpisná omezení pro některé typy tajných kódů, jako jsou certifikáty. Soubory PFX můžete nahrát do libovolného trezoru klíčů v libovolné oblasti. Tajné kódy se stáhnou ve formátu, který není chráněný heslem. Zodpovídáte za zabezpečení tajných kódů během přesunu.