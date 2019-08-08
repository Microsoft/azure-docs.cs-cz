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
ms.openlocfilehash: 5c45dbe29bb86150c76cf5bc136c4a7504270f86
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854550"
---
[Soubory Azure](../articles/storage/files/storage-files-introduction.md) podporují ověřování na základě identity přes protokol SMB (Server Message Block) prostřednictvím služby [Azure Active Directory Domain Services (Azure služba AD DS)](../articles/active-directory-domain-services/overview.md). Virtuální počítače s Windows připojené k doméně mají přístup ke sdíleným složkám Azure pomocí pověření [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) .

Můžete spravovat přístup na úrovni sdílené složky Azure Files k identitě, jako je například uživatel nebo skupina ve službě Azure AD, pomocí [řízení přístupu na základě role (RBAC)](../articles/role-based-access-control/overview.md). Můžete definovat vlastní role RBAC, které zahrnují společné sady oprávnění používaných pro přístup k souborům Azure. Když přiřadíte vlastní roli RBAC k identitě Azure AD, získá tato identita v závislosti na těchto oprávněních přístup ke sdílené složce Azure.

Soubory Azure také podporují zachování, dědění a vynucování [seznamů řízení přístupu systému souborů NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) pro všechny soubory a adresáře ve sdílené složce. Pokud kopírujete data ze sdílené složky do souborů Azure nebo naopak, můžete určit, že se budou spravovat DACL souborů NTFS. Tímto způsobem můžete implementovat scénáře zálohování pomocí služby soubory Azure, které zachovávají seznamy souborů NTFS mezi místní sdílenou složkou a vaší cloudovou sdílenou složkou souborů. 

> [!NOTE]
> - Pro virtuální počítače se systémem Linux není podporováno ověřování Azure služba AD DS pro přístup SMB (Server Message Block). Podporované jsou pouze virtuální počítače s Windows.
> - Pro počítače připojené k doméně Active Directory se nepodporuje ověřování Azure služba AD DS pro přístup přes protokol SMB. V prozatímním případě zvažte použití [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) ke spuštění migrace dat do souborů Azure a pokračování vynucování řízení přístupu pomocí přihlašovacích údajů služby Active Directory z místních počítačů připojených k doméně služby Active Directory. 
> - Ověřování Azure služba AD DS pro přístup přes protokol SMB je dostupné jenom pro účty úložiště vytvořené po 24. září 2018.
> - Ověřování Azure služba AD DS pro přístup SMB a trvalost DACL se systémem souborů NTFS není podporované u sdílených složek Azure spravovaných pomocí Azure File Sync.
> - Ověřování Azure služba AD DS nepodporuje ověřování u účtů počítačů vytvořených ve službě Azure služba AD DS.
