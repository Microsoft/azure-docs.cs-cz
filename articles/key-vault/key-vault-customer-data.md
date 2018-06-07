---
title: Funkce dat zákazníka Azure Key Vault | Microsoft Docs
description: Další informace o zákazníkovi v Key Vault
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
ms.openlocfilehash: 1ddc74b1960095509a77d4b3072017847df42d90
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637357"
---
# <a name="azure-key-vault-customer-data-features"></a>Funkce dat zákazníka Azure Key Vault

Azure Key Vault obdrží data zákazníků během vytvoření či aktualizaci trezory, klíčů, tajné klíče, certifikáty a účty úložiště spravovaný. Tato data zákazníků je přímo viditelný na portálu Azure a přes rozhraní REST API. Zákaznická data, můžete upravit nebo odstranit, aktualizace nebo odstranění objektu, který obsahuje data.

Přístup k protokolům systému jsou generovány, pokud uživatele nebo aplikace přistupuje k Key Vault. Přístup podrobné protokoly jsou dostupné pro zákazníky používající Statistika Azure.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifikace data zákazníků

Následující informace identifikuje zákaznická data v rámci Azure Key Vault:

- Zásady přístupu pro Azure Key Vault obsahovat ID objektů představující uživatele, skupiny nebo aplikace
- Předměty certifikátu může zahrnovat e-mailové adresy nebo jiné uživatele nebo organizace identifikátory
- Kontakty certifikát může obsahovat uživatele e-mailové adresy, názvy nebo telefonních čísel
- Může obsahovat vystavitelů certifikátů, e-mailové adresy, názvy, telefonních čísel, přihlašovací údaje účtu a organizační údaje
- Libovolné značky lze použít pro objekty v Azure Key Vault. Tyto objekty zahrnují trezory, klíče, tajné klíče, certifikáty a účty úložiště. Používá značky mohou obsahovat osobní data
- Azure Key Vault přístup k protokolům obsahovat ID objektů [UPN](../active-directory/connect/active-directory-aadconnect-userprincipalname.md)a IP adresy pro každé volání rozhraní REST API
- Azure Key Vault diagnostických protokolů může obsahovat ID objektů a IP adresy pro volání rozhraní REST API

## <a name="deleting-customer-data"></a>Odstraňování dat zákazníka

Stejné rozhraní REST API, portál prostředí a použít k vytvoření trezorů, klíče, tajné klíče, certifikáty a účty úložiště spravovaný sady SDK jsou taky moct aktualizovat a odstranit tyto objekty.

Obnovitelného odstranění umožňuje obnovit odstraněná data za 90 dnů po jejím odstranění. Při použití obnovitelného odstranění, můžou se data trvale odstranit před 90 dnů, doba uchování vyprší provedením operace vyprázdnění. Pokud trezoru nebo odběr byl nakonfigurován pro bloku vymazat operace, není možné trvale odstranit data, dokud dobu uchování naplánované byla úspěšná.

## <a name="exporting-customer-data"></a>Export dat zákazníka

Stejná rozhraní API REST, portálu prostředí a sady SDK, které se používají k vytvoření trezorů klíčů, tajné klíče, certifikáty a spravované úložiště, které účty jsou také umožňují zobrazit a exportovat tyto objekty.

Protokolování přístupu je volitelná funkce, kterou lze zapnout na Azure Key Vault generuje protokoly pro každé volání rozhraní REST API. Tyto protokoly se převede na účet úložiště v rámci vašeho předplatného, kde můžete použít zásady uchovávání informací, který splňuje požadavky vaší organizace.

Azure Key Vault diagnostické protokoly obsahující osobní údaje mohou být načteny tím, že žádost o export na portálu ochraně osobních údajů uživatelů. Tento požadavek musí být provedeny správce klienta.

## <a name="next-steps"></a>Další postup

- [Protokolování v Azure Key Vault](key-vault-logging.md)

- [Přehled konfigurace soft odstranění služby Azure Key Vault](key-vault-soft-delete-cli.md)

- [trezory](https://docs.microsoft.com/rest/api/keyvault/vaults)

- [Operace klíče v Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Operace nad tajnými klíči Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault certifikátů a zásady](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Vystavitelů certifikátů](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers)

- [Operace účet úložiště v Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
