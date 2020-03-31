---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460559"
---
## <a name="protect-your-access-keys"></a>Ochrana přístupových klíčů

Přístupové klíče účtu úložiště jsou podobné root heslu pro váš účet úložiště. Vždy buďte opatrní při ochraně přístupových klíčů. Azure Key Vault můžete bezpečně spravovat a otáčet klíče. Vyhněte se distribuci přístupových klíčů ostatním uživatelům, jejich pevnému kódování nebo jejich ukládání kdekoli ve formátu prostého textu, který je přístupný ostatním uživatelům. Otočte klávesy, pokud se domníváte, že mohly být ohroženy.

Pokud je to možné, použijte Azure Active Directory (Azure AD) autorizovat požadavky na úložiště objektů Blob a fronty namísto sdíleného klíče. Azure AD poskytuje vynikající zabezpečení a snadné použití přes sdílený klíč. Další informace o autorizaci přístupu k datům pomocí Azure AD najdete v [tématu Autorizace přístupu k objektům BLOB azure a frontám pomocí Služby Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
