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
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565077"
---
Služba [soubory Azure](../articles/storage/files/storage-files-introduction.md) podporuje ověřování založené na identitách přes protokol SMB (Server Message Block) prostřednictvím [služby Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (ve verzi Preview) a [Azure Active Directory Domain Services (Azure služba AD DS)](../articles/active-directory-domain-services/overview.md) (GA). Tento článek se zaměřuje na to, jak můžou soubory Azure využívat doménové služby, a to buď místně, nebo v Azure, k podpoře přístupu na základě identity ke službě Azure Files přes SMB. Díky tomu můžete snadno nahradit stávající souborové servery službou Azure Files a nadále používat stávající adresářovou službu a zachovávat tak bezproblémové uživatelské oprávnění ke sdíleným složkám. 

Soubory Azure vynutily autorizaci uživatelů ke sdílené složce i k úrovni adresářů a souborů. Přiřazení oprávnění na úrovni sdílené složky je možné přiřadit ke skupinám uživatelů nebo skupin Azure AD spravovaných prostřednictvím typického modelu [řízení přístupu na základě role (RBAC)](../articles/role-based-access-control/overview.md) . V případě RBAC by přihlašovací údaje, které používáte pro přístup k souborům, měly být k dispozici nebo synchronizovány do Azure AD. Uživatelům nebo skupinám v Azure AD můžete přiřadit předdefinované role RBAC, jako je soubor úložiště. čtečka sdílení souborů SMB pro udělení oprávnění ke čtení sdílené složky Azure.

Na úrovni adresářů a souborů podporuje Azure Files udržování, dědění a vynucování [seznamů DACL](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) , stejně jako všechny souborové servery Windows. Pokud kopírujete data přes protokol SMB ze sdílené složky do souborů Azure nebo naopak, můžete si vybrat, jestli chcete, aby Windows DACL zůstaly. Bez ohledu na to, jestli plánujete autorizaci nebo ne, můžete využívat soubory Azure pro zálohování seznamů ACL společně s daty. 
