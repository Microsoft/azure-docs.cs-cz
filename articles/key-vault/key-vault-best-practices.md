---
title: Doporučené postupy pro použití trezoru klíčů – trezor klíčů Azure | Dokumenty společnosti Microsoft
description: Tento dokument vysvětluje některé z osvědčených postupů pro použití trezoru klíčů
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cb4bec8170466f0fc667b592d44b0858c41ccd84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270989"
---
# <a name="best-practices-to-use-key-vault"></a>Doporučené postupy pro použití trezoru klíčů

## <a name="control-access-to-your-vault"></a>Řízení přístupu k trezoru

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné klíče, jako jsou certifikáty, připojovací řetězce a hesla. Vzhledem k tomu, že tato data jsou citlivá a důležitá pro podnikání, je třeba zabezpečit přístup k trezorům klíčů povolením pouze autorizovaných aplikací a uživatelů. Tento [článek](key-vault-secure-your-key-vault.md) obsahuje přehled modelu přístupu trezoru klíčů. Vysvětluje ověřování a autorizaci a popisuje, jak zabezpečit přístup k trezorům klíčů.

Návrhy při řízení přístupu k vašemu trezoru jsou následující:
1. Uzamknutí přístupu k předplatnému, skupině prostředků a trezorům klíčů (RBAC)
2. Vytvoření zásad přístupu pro každý trezor
3. K udělení přístupu pomocí objektu zabezpečení s nejnižšími oprávněními
4. Zapnutí koncových bodů brány firewall a [služby virtuální sítě](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Použití samostatného trezoru klíčů

Doporučujeme použít trezor na aplikaci pro každý životní prostředí (Vývoj, Předvýroba a Výroba). To vám pomůže nesdílet tajné klíče napříč prostředími a také snižuje hrozbu v případě porušení.

## <a name="backup"></a>Zálohování

Ujistěte se, že budete mít pravidelné zálohy [vašeho trezoru](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) na aktualizaci / odstranění / vytvoření objektů v rámci trezoru.

## <a name="turn-on-logging"></a>Zapnutí protokolování

[Zapněte protokolování](key-vault-logging.md) pro svůj trezor. Nastavte také výstrahy.

## <a name="turn-on-recovery-options"></a>Zapnutí možností obnovení

1. Zapněte [možnost Obnovitelné odstranění](key-vault-ovw-soft-delete.md).
2. Zapněte ochranu proti pročištění, pokud chcete chránit před vymazáním síly tajného / trezoru i po zapnutí měkkého odstranění.
