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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565077"
---
[Soubory Azure](../articles/storage/files/storage-files-introduction.md) podporují ověřování na základě identity přes blok zpráv serveru (SMB) prostřednictvím [služby Active Directory (Preview)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) a [služby Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA). Tento článek se zaměřuje na to, jak soubory Azure můžete využít služby domény, místní nebo v Azure, pro podporu přístupu k souborům Azure na základě identit y přes SMB. To vám umožní snadno nahradit stávající souborové servery soubory Azure a nadále používat stávající adresářovou službu, udržování bezproblémového přístupu uživatelů ke sdíleným složkám. 

Soubory Azure vynucují autorizaci na přístup uživatele ke sdílené složce i k úrovni adresáře nebo souboru. Přiřazení oprávnění na úrovni sdílené položky lze přiřadit uživatelům nebo skupinám Azure AD spravovaným prostřednictvím modelu [typického řízení přístupu (RBAC) založeného na rolích.](../articles/role-based-access-control/overview.md) S RBAC přihlašovací údaje, které používáte pro přístup k souborům by měl být k dispozici nebo synchronizovat do Azure AD. Můžete přiřadit integrované role RBAC, jako je data úložiště Data SMB Share Reader uživatelům nebo skupinám ve službě Azure AD udělit přístup pro čtení ke sdílené složce Azure.

Na úrovni adresáře nebo souboru podporují soubory Azure zachování, dědění a [vynucování seznamů DACL systému Windows stejně](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) jako všechny souborové servery Windows. Pokud zkopírujete data přes SMB ze sdílené složky do souborů Azure nebo naopak, můžete si vybrat, zda chcete zachovat seznamy DACL systému Windows. Ať už plánujete vynucovat autorizaci nebo ne, můžete využít soubory Azure k zálohování seznamů ACL spolu s daty. 
