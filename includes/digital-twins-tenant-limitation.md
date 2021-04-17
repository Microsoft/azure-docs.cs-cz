---
author: baanders
description: zahrnutí souboru popisujícího omezení mezi klienty pomocí digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589323"
---
V důsledku toho požadavky na rozhraní API pro digitální vlákna Azure vyžadují uživatele nebo instanční objekt, který je součástí stejného tenanta, kde se nachází instance digitálního vlákna Azure. Aby se zabránilo zlomyslné kontrole koncových bodů digitálních vláken Azure, budou žádosti s přístupovými tokeny mimo původního tenanta vracet chybové zprávy "404 Sub-Domain Nenalezeno". Tato chyba se vrátí *i v případě* , že se uživateli nebo instančnímu objektu dostala [role](../articles/digital-twins/concepts-security.md) data Reader pro digitální vlákna Azure nebo služba Azure Digital data Reader prostřednictvím spolupráce [Azure AD B2B](../articles/active-directory/external-identities/what-is-b2b.md) . 