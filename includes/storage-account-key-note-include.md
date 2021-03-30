---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86027323"
---
## <a name="protect-your-access-keys"></a>Ochrana přístupových klíčů

Přístupové klíče účtu úložiště jsou podobné kořenovému heslu vašeho účtu úložiště. Vždy buďte opatrní, abyste chránili přístupové klíče. Pomocí Azure Key Vault můžete bezpečně spravovat a střídat klíče. Vyhněte se distribuci přístupových klíčů jiným uživatelům, pevným kódováním nebo uložením kamkoli do prostého textu, který je přístupný ostatním uživatelům. Pokud se domníváte, že by mohly být ohrožené, můžete klíče otočit.

> [!NOTE]
> Microsoft doporučuje pomocí Azure Active Directory (Azure AD) autorizovat požadavky na data objektů BLOB a front (Pokud je to možné) místo sdíleného klíče. Azure AD poskytuje vynikající zabezpečení a jednoduchost používání sdíleného klíče. Další informace o autorizaci přístupu k datům pomocí Azure AD najdete v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
