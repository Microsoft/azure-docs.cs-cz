---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: eb55d993ad8960f821c2b72f0a53602166b7cc7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900620"
---
Pro data v klidovém stavu:

- Přístup k datům uloženým ve sdílených složek je omezen.

    - Klienti SMB, kteří přistupují k datům sdílené složky, potřebují pověření uživatele přidružená ke sdílené položce. Tato pověření jsou definovány při vytvoření sdílené složky.
    - Ip adresy klientů služby NFS, kteří přistupují ke sdílené položce, je třeba při vytvoření sdílené složky přidat.
