---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052886"
---
Vícekanálový protokol SMB pro sdílené složky Azure má v současné době tato omezení:
- Dá se použít jenom u místně redundantních účtů úložiště.
- Podporováno pouze pro klienty se systémem Windows. 
- Maximální počet kanálů je čtyři.
- Funkce SMB Direct není podporována.
- Soukromé koncové body účtů úložiště se nepodporují.
- U účtů úložiště s místními Active Directory Domain Services (služba AD DS) nebo Azure služba AD DS [ověřování založené na identitě](../articles/storage/files/storage-files-active-directory-overview.md) povolených pro soubory Azure by klienti SMB nemohli pomocí Průzkumníka souborů Windows nakonfigurovat oprávnění NTFS pro adresáře a soubory.
    - Pro konfiguraci oprávnění použijte nástroj Windows [Icacls](/windows-server/administration/windows-commands/icacls) nebo příkaz [set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) .

