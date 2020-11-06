---
title: Běžné kódy chyb pro Azure Key Vault | Microsoft Docs
description: Běžné kódy chyb pro Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: a36e15a56a5a4c8a637120ca730ae1da764d376d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422894"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Běžné kódy chyb pro Azure Key Vault

Kódy chyb, které jsou uvedené v následující tabulce, můžou být vrácené operací v trezoru klíčů Azure.

| Kód chyby | Zpráva uživatele |
|--|--|
| VaultAlreadyExists |  Pokus o vytvoření nového trezoru klíčů se zadaným názvem se nezdařil, protože název se už používá. Pokud jste nedávno odstranili Trezor klíčů s tímto názvem, může být stále ve stavu tichého odstranění. [Zde](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) můžete ověřit, jestli existuje v případě tichého odstranění. |
| VaultNameNotValid |  Název trezoru by měl být 24 znaků, alfanumerický znak a začínat abecedou. |
| AccessDenied |  V zásadách přístupu možná chybí oprávnění k provedení této operace. |
| ForbiddenByFirewall |  Adresa klienta není autorizována a volající není důvěryhodná služba. |
| ConflictError |  Požadujete více operací se stejnou položkou.  |
| RegionNotSupported |  Zadaná oblast Azure není pro tento prostředek podporovaná. |
| SkuNotSupported |  Zadaný typ SKU není pro tento prostředek podporován. |
| ResourceNotFound |  Zadaný prostředek Azure se nenašel. |
| CertificateExpired |  Ověřte datum vypršení platnosti certifikátu a jeho dobu platnosti. |


## <a name="next-steps"></a>Další kroky

- Projděte si [příručku pro vývojáře Azure Key Vault](developers-guide.md)
- Přečtěte si další informace o [ověřování do trezoru klíčů](authentication.md) .
