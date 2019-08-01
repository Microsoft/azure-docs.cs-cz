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
ms.openlocfilehash: d1b10b55481b41f42c6c872522d3dd4dd4be0e77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570593"
---
[Soubory Azure](../articles/storage/files/storage-files-introduction.md) podporují ověřování na základě identity přes protokol SMB (Server Message Block) (Preview) prostřednictvím [Azure Active Directory Domain Services (služba AD DS)](../articles/active-directory-domain-services/overview.md). Virtuální počítače s Windows připojené k doméně mají přístup ke sdíleným složkám Azure pomocí pověření [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) .

Můžete spravovat přístup na úrovni sdílené složky Azure Files k identitě, jako je například uživatel nebo skupina ve službě Azure AD, pomocí [řízení přístupu na základě role (RBAC)](../articles/role-based-access-control/overview.md). Můžete definovat vlastní role RBAC, které zahrnují společné sady oprávnění používaných pro přístup k souborům Azure. Když přiřadíte vlastní roli RBAC k identitě Azure AD, získá tato identita v závislosti na těchto oprávněních přístup ke sdílené složce Azure.

V rámci verze Preview taky Azure Files podporuje zachování, dědění a vynucování [seznamů řízení přístupu NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) pro všechny soubory a adresáře ve sdílené složce. Pokud kopírujete data ze sdílené složky do souborů Azure nebo naopak, můžete určit, že se budou spravovat DACL souborů NTFS. Tímto způsobem můžete implementovat scénáře zálohování pomocí služby soubory Azure, které zachovávají seznamy souborů NTFS mezi místní sdílenou složkou a vaší cloudovou sdílenou složkou souborů. 

> [!NOTE]
> - Pro virtuální počítače se systémem Linux není podporováno ověřování Azure služba AD DS pro přístup SMB (Server Message Block). Podporované jsou pouze virtuální počítače s Windows.
> - Pro počítače připojené k doméně Active Directory se nepodporuje ověřování Azure služba AD DS pro přístup přes protokol SMB. V prozatímním případě zvažte použití [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) ke spuštění migrace dat do souborů Azure a pokračování vynucování řízení přístupu pomocí přihlašovacích údajů služby Active Directory z místních počítačů připojených k doméně služby Active Directory. 
> - Ověřování Azure služba AD DS pro přístup přes protokol SMB je dostupné jenom pro účty úložiště vytvořené po 24. září 2018.
> - Ověřování Azure služba AD DS pro přístup SMB a trvalost DACL se systémem souborů NTFS není podporované u sdílených složek Azure spravovaných pomocí Azure File Sync.
