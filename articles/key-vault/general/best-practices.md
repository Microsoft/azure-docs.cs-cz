---
title: Osvědčené postupy použití Key Vault-Azure Key Vault | Microsoft Docs
description: Seznamte se s osvědčenými postupy pro Azure Key Vault, včetně řízení přístupu, kdy používat samostatné trezory klíčů, zálohování, protokolování a možnosti obnovení.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: e81cbd7e6584f4a280ab9507a989b52d3b188f2d
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072627"
---
# <a name="best-practices-to-use-key-vault"></a>Osvědčené postupy pro použití Key Vault

## <a name="use-separate-key-vaults"></a>Použít samostatné trezory klíčů

Naším doporučením je použití trezoru pro jednotlivé aplikace (vývoj, předprodukční a produkční prostředí). To vám pomůže sdílet tajné klíče mezi prostředími a zároveň snižuje riziko v případě porušení.

## <a name="control-access-to-your-vault"></a>Řízení přístupu k trezoru

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy, jako jsou certifikáty, připojovací řetězce a hesla. Vzhledem k tomu, že tato data jsou citlivá a důležitá pro podnikání, je nutné zabezpečený přístup k vašim trezorům klíčů povolit pouze autorizovaným aplikacím a uživatelům. Tento [článek](secure-your-key-vault.md) poskytuje přehled modelu přístupu Key Vault. Vysvětluje ověřování a autorizaci a popisuje, jak zabezpečit přístup k vašim trezorům klíčů.

Návrhy při řízení přístupu k trezoru jsou následující:
1. Uzamčení přístupu ke svému předplatnému, skupině prostředků a trezorům klíčů (Azure RBAC)
2. Vytvoření zásad přístupu pro každý trezor
3. Použijte k udělení přístupu aspoň přístupový objekt zabezpečení s oprávněními.
4. Zapnout bránu firewall a [koncové body služby virtuální](overview-vnet-service-endpoints.md) sítě

## <a name="backup"></a>Backup

Ujistěte se, že při aktualizaci, odstranění a vytváření objektů v rámci trezoru provádíte pravidelné zálohování vašeho trezoru.

### <a name="azure-powershell-backup-commands"></a>Příkazy zálohování Azure PowerShell

* [Záložní certifikát](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Záložní klíč](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Tajný klíč zálohy](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Příkazy zálohování Azure CLI

* [Záložní certifikát](/cli/azure/keyvault/certificate#az-keyvault-certificate-backup)
* [Záložní klíč](/cli/azure/keyvault/key#az-keyvault-key-backup)
* [Tajný klíč zálohy](/cli/azure/keyvault/secret#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Zapnout protokolování

[Zapněte protokolování](logging.md) pro svůj trezor. Nastavte také výstrahy.

## <a name="turn-on-recovery-options"></a>Zapnout možnosti obnovení

1. Zapněte [obnovitelné odstranění](soft-delete-overview.md).
2. Zapněte ochranu vyprázdnění, pokud chcete chránit před vynuceným odstraněním tajného nebo trezoru, i když je zapnuté obnovitelné odstranění.