---
title: Osvědčené postupy použití Key Vault-Azure Key Vault | Microsoft Docs
description: Tento dokument popisuje některé z osvědčených postupů pro použití Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 923fb90f7f0e8eefec650515ed2a3b9b75d2ae77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617914"
---
# <a name="best-practices-to-use-key-vault"></a>Osvědčené postupy pro použití Key Vault

## <a name="control-access-to-your-vault"></a>Řízení přístupu k trezoru

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy, jako jsou certifikáty, připojovací řetězce a hesla. Vzhledem k tomu, že tato data jsou citlivá a důležitá pro podnikání, je nutné zabezpečený přístup k vašim trezorům klíčů povolit pouze autorizovaným aplikacím a uživatelům. Tento [článek](secure-your-key-vault.md)) poskytuje přehled modelu přístupu Key Vault. Vysvětluje ověřování a autorizaci a popisuje, jak zabezpečit přístup k vašim trezorům klíčů.

Návrhy při řízení přístupu k trezoru jsou následující:
1. Uzamčení přístupu ke svému předplatnému, skupině prostředků a trezorům klíčů (RBAC)
2. Vytvoření zásad přístupu pro každý trezor
3. Použijte k udělení přístupu aspoň přístupový objekt zabezpečení s oprávněními.
4. Zapnout bránu firewall a [koncové body služby virtuální](overview-vnet-service-endpoints.md)sítě

## <a name="use-separate-key-vault"></a>Použít samostatné Key Vault

Naším doporučením je použití trezoru pro jednotlivé aplikace (vývoj, předprodukční a produkční prostředí). To vám pomůže sdílet tajné klíče mezi prostředími a zároveň snižuje riziko v případě porušení.

## <a name="backup"></a>Backup

Ujistěte se, že při aktualizaci, odstranění a vytváření objektů v rámci trezoru provádíte pravidelné zálohování vašeho [trezoru](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) .

## <a name="turn-on-logging"></a>Zapnout protokolování

[Zapnout protokolování](logging.md)) pro svůj trezor. Nastavte také výstrahy.

## <a name="turn-on-recovery-options"></a>Zapnout možnosti obnovení

1. Zapnout [obnovitelné odstranění](overview-soft-delete.md)).
2. Zapněte ochranu vyprázdnění, pokud chcete chránit před vynuceným odstraněním tajného nebo trezoru, i když je zapnuté obnovitelné odstranění.
