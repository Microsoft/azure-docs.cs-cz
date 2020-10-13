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
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876934"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Běžné kódy chyb pro Azure Key Vault

Kódy chyb, které jsou uvedené v následující tabulce, můžou být vrácené operací v trezoru klíčů Azure.

| Kód chyby | Zpráva uživatele |
|--|--|
| VaultAlreadyExists |  Zadaný Trezor klíčů už existuje (v nepodmíněném odstranění nebo v jiném předplatném). |
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
