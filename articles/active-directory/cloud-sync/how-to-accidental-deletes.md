---
title: Azure AD Connect náhodné odstranění cloudové synchronizace
description: Toto téma popisuje, jak pomocí funkce náhodného odstranění zabránit odstranění.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613400"
---
# <a name="accidental-delete-prevention"></a>Prevence náhodného odstranění

Následující dokument popisuje funkci náhodného odstranění Azure AD Connect synchronizace cloudu.  Funkce náhodného odstranění je navržená tak, aby vás chránila před nechtěnými změnami konfigurace a změnami v místním adresáři, které by mohly ovlivnit mnoho uživatelů a skupin.  Tato funkce umožňuje:

- Nakonfigurujte schopnost zabránit náhodným odstraněním automaticky. 
- Nastavit počet objektů (prahová hodnota), po jejichž uplynutí bude konfigurace platit 
- Nastavte e-mailovou adresu oznámení, aby se mohla zobrazit e-mailové oznámení, když se příslušná úloha synchronizace umístí do karantény pro tento scénář. 

Pokud chcete tuto funkci použít, nastavte prahovou hodnotu pro počet objektů, které se při odstranění mají zastavit.  Takže pokud se toto číslo dosáhne, synchronizace se zastaví a pošle se oznámení na zadaný e-mail.  To vám umožní prozkoumat, co se chystá.


## <a name="configure-accidental-delete-prevention"></a>Konfigurace prevence náhodného odstranění
Chcete-li použít novou funkci, postupujte podle následujících kroků.


1.  Na portálu Azure Portal vyberte **Azure Active Directory**.
2.  Vyberte **Azure AD Connect**.
3.  Vyberte **Spravovat cloudovou synchronizaci**.
4. V části **Konfigurace** vyberte svou konfiguraci.
5. V části **Nastavení** zadejte následující:
    - **E-mail s oznámením** – e-mail použitý k oznámením
    - **Zabránit nechtěnému odstranění** – zaškrtněte toto políčko, pokud chcete funkci povolit.
    - **Prahová hodnota náhodného odstranění** – zadejte počet objektů, pro které se má aktivovat synchronizace, zastavit a oznámení.

![Náhodné odstranění](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>Další kroky 

- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)
- [Postup instalace Azure AD Connect synchronizace cloudu](how-to-install.md)
 

