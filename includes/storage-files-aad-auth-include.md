---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536573"
---
[Soubory Azure](../articles/storage/files/storage-files-introduction.md) podporují ověřování na základě identity přes blok zpráv serveru (SMB) prostřednictvím [místních služeb Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (preview) a [Služby Azure Active Directory Domain Services (Azure AD DS).](../articles/active-directory-domain-services/overview.md) Tento článek se zaměřuje na to, jak sdílené složky Azure můžete používat služby domény, místní nebo v Azure, pro podporu přístupu k sdíleným souborům Azure na základě identit y přes SMB. Povolení přístupu založeného na identitě pro sdílené složky Azure umožňuje nahradit stávající souborové servery sdílenými složkami Azure bez nahrazení stávající adresářové služby a udržovat bezproblémový přístup uživatelů ke sdíleným položkám. 

Soubory Azure vynucují autorizaci na přístup uživatelů ke sdílené složce i k úrovni adresáře nebo souboru. Přiřazení oprávnění na úrovni sdílené položky lze provést u uživatelů nebo skupin azure a disobů (Azure AD) spravovaných prostřednictvím modelu [řízení přístupu na základě rolí (RBAC).](../articles/role-based-access-control/overview.md) S RBAC přihlašovací údaje, které používáte pro přístup k souborům by měl být k dispozici nebo synchronizovat do Azure AD. Můžete přiřadit integrované role RBAC, jako je data úložiště Data SMB Share Reader uživatelům nebo skupinám ve službě Azure AD udělit přístup pro čtení ke sdílené složce Azure.

Na úrovni adresáře nebo souboru podporují soubory Azure zachování, dědění a [vynucování seznamů DACL systému Windows stejně](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) jako všechny souborové servery Windows. Při kopírování dat přes SMB mezi existující sdílenou složkou a sdílenými složkami Azure můžete zachovat seznamy DACL systému Windows. Ať už máte v plánu vynutit autorizaci nebo ne, můžete použít sdílené složky Azure k zálohování aklů spolu s daty. 
