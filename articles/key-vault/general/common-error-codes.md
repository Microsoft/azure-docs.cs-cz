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
ms.openlocfilehash: 9ae13b88d767e43c425ceb86d0be455cebc0e6ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462518"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Běžné kódy chyb pro Azure Key Vault

Kódy chyb, které jsou uvedené v následující tabulce, můžou být vrácené operací v trezoru klíčů Azure.

| Kód chyby | Zpráva uživatele |
|--|--|
| VaultAlreadyExists |  Pokus o vytvoření nového trezoru klíčů se zadaným názvem se nezdařil, protože název se už používá. Pokud jste nedávno odstranili Trezor klíčů s tímto názvem, může být stále ve stavu tichého odstranění. [Zde](./key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) můžete ověřit, jestli existuje v případě tichého odstranění. |
| VaultNameNotValid |  Název trezoru by měl být 24 znaků, alfanumerický znak a začínat abecedou. |
| AccessDenied |  V zásadách přístupu možná chybí oprávnění k provedení této operace. |
| ForbiddenByFirewall |  Adresa klienta není autorizována a volající není důvěryhodná služba. |
| ConflictError |  Požadujete více operací se stejnou položkou.  |
| RegionNotSupported |  Zadaná oblast Azure není pro tento prostředek podporovaná. |
| SkuNotSupported |  Zadaný typ SKU není pro tento prostředek podporován. |
| ResourceNotFound |  Zadaný prostředek Azure se nenašel. |
| ResourceGroupNotFound | Zadaná skupina prostředků Azure se nenašla. |
| CertificateExpired |  Ověřte datum vypršení platnosti certifikátu a jeho dobu platnosti. |


## <a name="next-steps"></a>Další kroky

- Projděte si [příručku pro vývojáře Azure Key Vault](developers-guide.md)
- Přečtěte si další informace o [ověřování do trezoru klíčů](authentication.md) .