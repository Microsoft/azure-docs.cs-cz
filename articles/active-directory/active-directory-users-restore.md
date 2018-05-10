---
title: Obnovit nebo trvale odebrat nedávno odstranila uživatele v Azure Active Directory | Microsoft Docs
description: Jak obnovit odstraněné uživatele, zobrazit obnovitelné uživatele nebo trvale odstranit uživatele v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/28/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 9e28184000964564bcf170a2c8015f3b4c220209
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Obnovení odstraněného uživatele v Azure Active Directory

Tento článek obsahuje pokyny k obnovení nebo trvale odstranit dříve odstraněné uživatele. Když odstraníte uživatele ve službě Azure Active Directory (Azure AD), je 30 dní od data odstranění uchovávat Odstraněný uživatel. Během této doby se dají obnovovat uživatel a jeho vlastnosti. 

> [!wARNING]
> Poté, co je trvale odstraněn, uživatele nelze obnovit.


## <a name="how-to-restore-a-recently-deleted-user"></a>Obnovení odstraněných uživatelů
Když se uživatel nedávno odstraní, se zachovala všech informací adresáře. Pokud uživatel je obnovit, tyto informace je také obnovit.

1. V [centra pro správu Azure AD](https://aad.portal.azure.com), vyberte **uživatelů a skupin** &gt; **všichni uživatelé**. 
2. V části **zobrazit**, filtrovat stránku zobrazíte **uživatelé v poslední době odstraňovali**. 
3. Vyberte jeden nebo více odstraněných uživatelů.
4. Vyberte **obnovení uživatele**.

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>Jak trvale odstranit nedávno odstraněný uživatel

1. V [centra pro správu Azure AD](https://aad.portal.azure.com), vyberte **uživatelů a skupin** &gt; **všichni uživatelé**. 
2. V části **zobrazit**, filtrovat stránku zobrazíte **uživatelé v poslední době odstraňovali**. 
3. Vyberte jeden nebo více odstraněných uživatelů.
4. Vyberte **trvale odstranit**.

## <a name="required-permissions"></a>Požadovaná oprávnění
Tato oprávnění jsou dostatečná k obnovení uživatele.

Role  | Oprávnění 
--------- | ---------
Správce společnosti<p>Podpora partnerů úrovně 1<p>Podpora partnerů úrovně 2<p>Správce uživatelských účtů | Můžete obnovit odstranění uživatelé 
Správce společnosti<p>Podpora partnerů úrovně 1<p>Podpora partnerů úrovně 2<p>Správce uživatelských účtů | Může trvale odstranit uživatele

## <a name="next-steps"></a>Další postup
Tyto články poskytují další informace o správu uživatelů Azure Active Directory.

* [Rychlé spuštění: Přidání nebo odstranění uživatelů do Azure Active Directory](add-users-azure-active-directory.md)
* [Správa uživatelských profilů](active-directory-users-profile-azure-portal.md)
* [Přidat uživatele typu Host z jiného adresáře](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Přiřazení uživatele k roli ve službě Azure AD](active-directory-users-assign-role-azure-portal.md)
