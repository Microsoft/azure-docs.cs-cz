---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269347"
---
[Služba soubory Azure](../articles/storage/files/storage-files-introduction.md) podporuje ověřování na základě identity přes protokol SMB (Server Message Block) (preview) prostřednictvím [domény služby Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/overview.md). Připojené k doméně Windows virtuálních počítačů (VM) můžete přístup k sdílenými složkami Azure pomocí [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) přihlašovací údaje. 

Můžete spravovat soubory Azure úroveň přístupu ke sdílení do identity jako je například uživatel, skupiny ve službě Azure AD s [řízení přístupu na základě role (RBAC)](../articles/role-based-access-control/overview.md). Můžete definovat vlastní role RBAC, které zahrnuje společné sady oprávnění pro přístup k Azure Files. Když přiřadíte vlastní roli RBAC do identity Azure AD, že identita je udělen přístup k sdílené složky Azure podle těchto oprávnění.

V rámci verze preview se soubory Azure také podporuje zachování dědění a vynucování [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) na všechny soubory a adresáře ve sdílené složce. Pokud se kopírování dat ze sdílené složky do služby soubory Azure, nebo naopak, můžete určit, že se zachovají DACL systému souborů NTFS. Tímto způsobem můžete implementovat scénáře zálohování pomocí služby soubory Azure, zachování vašeho systému souborů NTFS DACL mezi vaší místní sdílené složky a sdílené složce cloudu. 

> [!NOTE]
> - Ověřování Azure AD Domain Services pro SMB přístup není podporován pro virtuální počítače s Linuxem. Podporované jsou pouze virtuální počítače s Windows.
> - Ověřování Azure AD Domain Services pro přístup k protokolu SMB se nepodporuje pro počítače připojeného k doméně služby Active Directory. Prozatím můžete využívat [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) k migraci dat do služby soubory Azure a dál vynutit řízení přístupu pomocí přihlašovacích údajů AD z vašeho místního připojený k doméně AD počítače. 
> - Ověřování Azure AD Domain Services pro přístup k protokolu SMB je k dispozici pouze pro účty úložiště vytvořené po 24. září 2018.
> - Ověřování Azure AD Domain Services pro přístup k protokolu SMB a systému souborů NTFS DACL trvalé nepodporuje sdílené složky Azure spravované pomocí služby Azure File Sync. 
