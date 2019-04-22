---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684429"
---
Pro na data-at-rest:

- Pro na data-at-rest se používá šifrování nástrojem BitLocker XTS AES-256 chránit místní data.
- Pro data uložená ve sdílených složkách je omezený přístup ke sdíleným složkám.

    - U klientů SMB přistupujících k datům sdílené složky musí přihlašovací údaje uživatele přidružené sdílené složky. Tyto přihlašovací údaje jsou definovány v době vytvoření sdílené složky.
    - IP adresy klientů pro klienty systému souborů NFS, které přistupují k sdíleným složkám nutné přidat v době vytvoření sdílené složky.