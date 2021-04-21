---
title: Azure Key Vault funkce zákaznických dat – Azure Key Vault | Microsoft Docs
description: Seznamte se s Zákaznickými daty, která Azure Key Vault přijímá během vytváření nebo aktualizace trezorů, klíčů, tajných klíčů, certifikátů a spravovaných účtů úložiště.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4d45c019a6ba073d7553c09784736959faf89d27
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749775"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault funkce zákaznických dat

Azure Key Vault přijímá zákaznická data během vytváření nebo aktualizace trezorů, spravovaných fondů HSM, klíčů, tajných klíčů, certifikátů a spravovaných účtů úložiště. Tato zákaznická data jsou přímo viditelná v Azure Portal a prostřednictvím REST API. Zákaznická data se dají upravovat nebo odstraňovat pomocí aktualizace nebo odstranění objektu, který obsahuje data.

Protokoly přístupu k systému jsou generovány, když uživatel nebo aplikace přistupuje Key Vault. Podrobné protokoly přístupu jsou k dispozici pro zákazníky, kteří používají Azure Insights.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifikace zákaznických dat

Následující informace identifikují zákaznická data v rámci Azure Key Vault:

- Zásady přístupu pro Azure Key Vault obsahují ID objektů, které představují uživatele, skupiny nebo aplikace.
- Subjekt certifikátu může zahrnovat e-mailové adresy nebo jiné identifikátory uživatelů a organizací.
- Kontakty certifikátu můžou obsahovat e-mailové adresy nebo jména uživatelů nebo telefonní čísla.
- Vystavitelé certifikátů můžou obsahovat e-mailové adresy, jména, telefonní čísla, přihlašovací údaje k účtu a organizační podrobnosti.
- U objektů v Azure Key Vault lze použít libovolné značky. Mezi tyto objekty patří trezory, klíče, tajné klíče, certifikáty a účty úložiště. Použité značky mohou obsahovat osobní údaje
- Protokoly Azure Key Vault Accessu obsahují identifikátory objektu, [UPN](../../active-directory/hybrid/plan-connect-userprincipalname.md)a IP adresy pro každé volání REST API
- Protokoly diagnostiky Azure Key Vault můžou obsahovat identifikátory objektů a IP adresy pro REST API volání.

## <a name="deleting-customer-data"></a>Odstraňují se zákaznická data

Stejné rozhraní REST API, možnosti portálu a sady SDK použité k vytváření trezorů, klíčů, tajných klíčů, certifikátů a spravovaných účtů úložiště jsou také schopné aktualizovat a odstranit tyto objekty.

Obnovitelné odstranění umožňuje obnovit Odstraněná data po dobu 90 dnů po odstranění. Při použití obnovitelného odstranění může dojít k trvalému odstranění dat před uplynutím doby uchování 90 dnů, a to provedením operace vyprázdnění. Pokud je trezor nebo předplatné nakonfigurovaný tak, aby blokovala operace vyprázdnění, není možné trvale odstranit data, dokud neuplyne plánovaná doba uchování.

## <a name="exporting-customer-data"></a>Export zákaznických dat

Stejné rozhraní REST API, možnosti portálu a sady SDK, které slouží k vytváření trezorů, klíčů, tajných kódů, certifikátů a spravovaných účtů úložiště, vám také umožní zobrazit a exportovat tyto objekty.

Protokolování přístupu Azure Key Vault je volitelná funkce, kterou lze zapnout pro vygenerování protokolů pro každé REST API volání. Tyto protokoly se přenesou do účtu úložiště ve vašem předplatném, kde aplikujete zásady uchovávání informací, které splňují požadavky vaší organizace.

Azure Key Vault diagnostické protokoly, které obsahují osobní údaje, lze načíst vytvořením žádosti o export na portálu ochrany osobních údajů uživatele. Tuto žádost musí udělat správce klienta.

## <a name="next-steps"></a>Další kroky

- [Protokolování Azure Key Vault](logging.md)

- [Přehled obnovitelného odstranění ve službě Azure Key Vault](./key-vault-recovery.md)

- [Azure Key Vault operací klíčů](/rest/api/keyvault/key-operations)

- [Azure Key Vault operace tajného klíče](/rest/api/keyvault/secret-operations)

- [Azure Key Vault certifikátů a zásad](/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault operací účtu úložiště](/rest/api/keyvault/storage-account-key-operations)