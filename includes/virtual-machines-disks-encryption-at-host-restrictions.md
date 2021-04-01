---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86230964"
---
- Nepodporuje disky Ultra.
- Nejde povolit, pokud je na vašich virtuálních počítačích nebo virtuálních počítačích zapnutá možnost Azure Disk Encryption (šifrování virtuálního počítače hosta pomocí BitLockeru/virtuálního počítače).
- Azure Disk Encryption nelze povolit na discích s povoleným šifrováním na hostiteli.
- V existující sadě škálování virtuálního počítače můžete šifrování povolit. Automaticky se ale zašifrují jenom nové virtuální počítače vytvořené po povolení šifrování.
- Aby bylo možné šifrovat stávající virtuální počítače, je nutné je uvolnit a znovu přidělit.