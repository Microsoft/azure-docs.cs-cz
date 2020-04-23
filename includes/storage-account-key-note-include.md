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

Přístupové klíče účtu úložiště jsou podobné kořenovému heslu vašeho účtu úložiště. Vždy buďte opatrní, abyste chránili přístupové klíče. Pomocí Azure Key Vault můžete bezpečně spravovat a střídat klíče. Vyhněte se distribuci přístupových klíčů jiným uživatelům, pevným kódováním nebo uložením kamkoli do prostého textu, který je přístupný ostatním uživatelům. Pokud se domníváte, že by mohly být ohrožené, můžete klíče otočit.

Pokud je to možné, použijte Azure Active Directory (Azure AD) k autorizaci požadavků do úložiště objektů BLOB a front namísto sdíleného klíče. Azure AD poskytuje vynikající zabezpečení a jednoduchost používání sdíleného klíče. Další informace o autorizaci přístupu k datům pomocí Azure AD najdete v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
