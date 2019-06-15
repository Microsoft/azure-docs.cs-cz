---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "67120585"
---
Pro data v klidovém stavu:

- Nástroj BitLocker XTS-AES 256bitového šifrování se používá k ochraně místní data.
- Přístup k datům uloženým ve sdílených složkách je omezen.

    - Klientů protokolu SMB využívajících sdílet data potřebovat přihlašovací údaje uživatele přidružené sdílené složky. Tyto přihlašovací údaje jsou definovány, když se vytvoří sdílené složky.
    - IP adresy klientů systému souborů NFS, které přístup ke sdílené složce je potřeba přidat po vytvoření sdílené složky.