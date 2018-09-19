---
title: Funkce dat zákazníků Azure Key Vault | Dokumentace Microsoftu
description: Další informace o zákaznická data ve službě Key Vault
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/22/2018
ms.author: barclayn
ms.openlocfilehash: 807b8a17570dfdcefa07a42719b7b0b24e4f31d7
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297158"
---
# <a name="azure-key-vault-customer-data-features"></a>Funkce dat zákazníků Azure Key Vault

Azure Key Vault přijímá data zákazníků během vytváření nebo aktualizace trezorů, klíčů, tajných kódů, certifikáty a účty spravované úložiště. Tato data zákazníků jsou viditelné přímo na webu Azure Portal a přes rozhraní REST API. Zákaznická data můžete upravit nebo odstranit, aktualizace nebo odstranění objektu, který obsahuje data.

Přístup k protokolům systému jsou generovány, pokud uživatele nebo aplikace přistupuje k službě Key Vault. Podrobné zobrazení protokolů jsou dostupné pro zákazníky, kteří používají Azure Insights.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifikace zákaznická data

Následující informace uvádí zákaznická data v rámci služby Azure Key Vault:

- Zásady přístupu pro Azure Key Vault obsahovat ID objektů představující uživatele, skupiny nebo aplikace
- Předměty certifikátu může obsahovat e-mailové adresy nebo jiný uživatel nebo organizace identifikátory
- Kontakty certifikátu může obsahovat uživatele e-mailové adresy, názvy nebo telefonních čísel
- Může obsahovat vystavitelů certifikátů, e-mailové adresy, názvy, telefonní čísla, přihlašovací údaje k účtu a údaje o organizaci
- Libovolné značky lze použít k objektům ve službě Azure Key Vault. Mezi tyto objekty patří trezorů, klíčů, tajných kódů, certifikáty a účty úložiště. Značky použité mohou obsahovat osobní data
- Azure Key Vault přístup k protokolům obsahovat ID objektů [UPN](../active-directory/hybrid/plan-connect-userprincipalname.md)a IP adresy pro každé volání rozhraní REST API
- Diagnostické protokoly služby Azure Key Vault může obsahovat ID objektů a IP adresy pro volání rozhraní REST API

## <a name="deleting-customer-data"></a>Odstraňuje se zákaznická data

Stejné rozhraní REST API, prostředí portálu a sady SDK použité k vytvoření trezorů, klíčů, tajných kódů, certifikáty a účty spravované úložiště, jsou také moct aktualizovat a odstranit tyto objekty.

Obnovitelné odstranění umožňuje obnovení odstraněných dat po dobu 90 dnů po jejím odstranění. Při použití obnovitelného odstranění, data mohou před 90 dní, po které vyprší doba uchování pomocí provádí operaci vyprázdnění trvale odstraní. Pokud se trezor nebo předplatné se nakonfiguroval tak bloku vymazat operations, není možné trvale odstranit data až do uplynutí doby uchování naplánované.

## <a name="exporting-customer-data"></a>Export zákaznických dat

Stejná rozhraní REST API, prostředí portálu a sad SDK, které se používají k vytváření trezorů, klíčů, tajných kódů, certifikáty a účty úložiště spravované také umožňují zobrazit a exportovat tyto objekty.

Protokolování přístupu je volitelná funkce, které je možné zapnout na služby Azure Key Vault generují protokoly pro každé volání rozhraní REST API. Tyto protokoly se přesunou do účtu úložiště v rámci vašeho předplatného, kde platí zásady uchovávání informací, které splňuje požadavky vaší organizace.

Azure diagnostické protokoly Key Vault, které obsahují osobní údaje můžete načíst tak, že žádost o export na portálu ochrany osobních údajů uživatele. Tento požadavek musí být provedené správcem tenanta.

## <a name="next-steps"></a>Další postup

- [Protokolování v Azure Key Vault](key-vault-logging.md)

- [Přehled obnovitelného odstranění služby Azure Key Vault](key-vault-soft-delete-cli.md)

- [Azure Key Vault klíčové operace](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Azure Key Vault tajný operace](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault certificates a zásady](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Provoz účet úložiště Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
