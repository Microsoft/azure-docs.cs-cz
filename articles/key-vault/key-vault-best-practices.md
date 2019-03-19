---
title: Doporučené postupy pro použití služby Key Vault – Azure Key Vault | Dokumentace Microsoftu
description: Tento dokument vysvětluje některé osvědčené postupy při použití služby Key Vault
services: key-vault
documentationcenter: ''
author: yvprashanth
manager: barbkess
tags: azure-key-vault
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: prashanthyv
ms.openlocfilehash: 85bd1858bc3f2d505e1e4d0a88e8c77a46ae4447
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57882023"
---
# <a name="best-practices-to-use-key-vault"></a>Osvědčené postupy pro použití služby Key Vault

## <a name="control-access-to-your-vault"></a>Řízení přístupu k trezoru

Služba Azure Key Vault je Cloudová služba, která chrání šifrovací klíče a tajné kódy jako hesla, certifikáty a připojovací řetězce. Protože tato data jsou citlivá a pro důležité obchodní informace, je potřeba zabezpečit přístup k vašim trezorům klíčů tím, že jen autorizované aplikace a uživatele. To [článku](key-vault-secure-your-key-vault.md) poskytuje přehled o přístup k modelu služby Key Vault. Vysvětluje ověření a autorizaci a popisuje, jak zabezpečit přístup k vašim trezorům klíčů.

Návrhy při řízení přístupu k trezoru jsou následující:
1. Uzamčení přístupu pro vaše předplatné, skupinu prostředků a klíč trezorů (RBAC)
2. Vytvořit zásady přístupu pro každý trezor
3. Použít alespoň oprávnění přístupu k objektu pro udělení přístupu
4. Zapnout bránu Firewall a [koncové body služby virtuální sítě](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Použití samostatné služby Key Vault

Naše doporučení je použití trezoru za aplikaci pro každé prostředí (vývojové, Předprodukčních a produkčních). To umožňuje sdílet napříč prostředími tajných kódů a také snižuje před internetovými útoky v případě porušení zabezpečení.

## <a name="backup-your-vault"></a>Zálohování trezoru

Ujistěte se, že provedete pravidelně zpět ups z vaší [trezor](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) na aktualizace/odstranění/vytvoření objektů v trezoru.

## <a name="turn-on-logging"></a>Zapnutí protokolování

[Zapnutí protokolování](key-vault-logging.md) pro svůj trezor. Také nastavte výstrahy.

## <a name="turn-on-recovery-options"></a>Zapnout možnosti obnovení

1. Zapnout [obnovitelné odstranění](key-vault-ovw-soft-delete.md).
2. Zapnutí ochrany, pokud chcete ochranu proti odstranění platnost tajného klíče / trezoru, i když je zapnutá funkce obnovitelného odstranění.
