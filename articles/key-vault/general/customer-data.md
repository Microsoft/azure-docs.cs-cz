---
title: Funkce zákaznických dat azure key vaultu – Azure Key Vault | Dokumenty společnosti Microsoft
description: Informace o zákaznických datech v trezoru klíčů
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 847b6c4616cbbb7fd9c6ccad18ec426dc38a887a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430848"
---
# <a name="azure-key-vault-customer-data-features"></a>Funkce zákaznických dat Azure Key Vault

Azure Key Vault přijímá zákaznická data během vytváření nebo aktualizace trezorů, klíčů, tajných kódů, certifikátů a účtů spravovaného úložiště. Tato zákaznická data jsou přímo viditelná na webu Azure Portal a prostřednictvím rozhraní REST API. Zákaznická data lze upravit nebo odstranit aktualizací nebo odstraněním objektu, který obsahuje data.

Protokoly přístupu k systému jsou generovány při přístupu uživatele nebo aplikace k trezoru klíčů. Podrobné protokoly přístupu jsou k dispozici zákazníkům pomocí Azure Insights.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifikace údajů o zákaznících

Následující informace identifikují zákaznická data v rámci služby Azure Key Vault:

- Zásady přístupu pro Azure Key Vault obsahují ID objektů představující uživatele, skupiny nebo aplikace
- Subjekty certifikátu mohou zahrnovat e-mailové adresy nebo jiné uživatelské či organizační identifikátory.
- Kontakty certifikátů mohou obsahovat e-mailové adresy, jména nebo telefonní čísla uživatelů.
- Vystavitelé certifikátů mohou obsahovat e-mailové adresy, jména, telefonní čísla, přihlašovací údaje účtu a podrobnosti o organizaci.
- Libovolné značky lze použít na objekty v trezoru klíčů Azure. Mezi tyto objekty patří trezory, klíče, tajné klíče, certifikáty a účty úložiště. Použité značky mohou obsahovat osobní údaje
- Protokoly přístupu k úložišti Klíčů Azure obsahují ID objektů, [hlavní název služby](../../active-directory/hybrid/plan-connect-userprincipalname.md)a ADRESY IP pro každé volání rozhraní REST API.
- Diagnostické protokoly služby Azure Key Vault mohou obsahovat ID objektů a IP adresy pro volání rozhraní REST API.

## <a name="deleting-customer-data"></a>Odstranění dat zákazníků

Stejná úložiště REST API, prostředí portálu a sady SDK, která se používají k vytváření trezorů, klíčů, tajných klíčů, certifikátů a účtů spravovaného úložiště, mohou tyto objekty také aktualizovat a odstranit.

Obnovitelné odstranění umožňuje obnovit smazané údaje po dobu 90 dnů po odstranění. Při použití obnovitelného odstranění mohou být data trvale odstraněna před uplynutím 90denní doby uchovávání provedením operace vymazání. Pokud byl trezor nebo odběr nakonfigurován tak, aby blokoval operace vymazání, není možné trvale odstranit data, dokud neuplyne naplánovaná doba uchovávání.

## <a name="exporting-customer-data"></a>Export dat zákazníků

Stejná úložiště REST API, prostředí portálu a sady SDK, které se používají k vytváření trezorů, klíčů, tajných klíčů, certifikátů a účtů spravovaného úložiště, také umožňují zobrazit a exportovat tyto objekty.

Protokolování přístupu k úložišti klíčů Azure Vault je volitelná funkce, kterou lze zapnout pro generování protokolů pro každé volání rozhraní REST API. Tyto protokoly budou převedeny na účet úložiště ve vašem předplatném, kde použijete zásady uchovávání informací, které splňují požadavky vaší organizace.

Diagnostické protokoly služby Azure Key Vault, které obsahují osobní data, lze načíst tak, že na portálu ochrany osobních údajů uživatelů najdete požadavek na export. Tento požadavek musí provést správce klienta.

## <a name="next-steps"></a>Další kroky

- [Protokolování trezoru klíčů Azure](logging.md))

- [Přehled obnovitelného odstranění ve službě Azure Key Vault](soft-delete-cli.md)

- [Operace s klíči Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Tajné operace azure trezoru klíčů](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Certifikáty a zásady azure key vaultu](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Operace úložiště Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
