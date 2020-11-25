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
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995462"
---
Vícekanálový protokol SMB pro sdílené složky Azure má v současné době tato omezení:
- Dá se použít jenom u místně redundantních účtů úložiště.
- Podporováno pouze pro klienty se systémem Windows. 
- Maximální počet kanálů je čtyři.
- Funkce SMB Direct není podporována.
- U účtů úložiště s místními Active Directory Domain Services (služba AD DS) nebo Azure služba AD DS [ověřování založené na identitě](../articles/storage/files/storage-files-active-directory-overview.md) povolených pro soubory Azure by klienti SMB nemohli pomocí Průzkumníka souborů Windows nakonfigurovat oprávnění NTFS pro adresáře a soubory.
    - Pro konfiguraci oprávnění použijte nástroj Windows [Icacls](/windows-server/administration/windows-commands/icacls) nebo příkaz [set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) .

