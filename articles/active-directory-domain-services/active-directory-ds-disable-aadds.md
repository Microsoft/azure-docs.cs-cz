---
title: Zakázat Azure Active Directory Domain Services | Microsoft Docs
description: Zakázat Azure Active Directory Domain Services pomocí portálu Azure
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: 4fd811c4114e5bd216e05a10bafba0ab0592c53c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213939"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Zakázat Azure Active Directory Domain Services pomocí portálu Azure
Tento článek ukazuje, jak zakázat domény služby Azure Active Directory (AD) pro váš adresář Azure AD pomocí portálu Azure.

> [!WARNING]
> **Odstranění je trvalá a nelze vrátit zpět.**
> Postupujte opatrně. Když odstraníte spravované doméně:
  * Řadiče domény pro spravované doméně jsou zrušte zřízený a odebrat z virtuální sítě.
  * Data na spravované doméně se trvale odstraní. To zahrnuje vlastní organizační jednotky, objekty zásad skupiny, vlastní záznamy DNS, objekty služby, skupinové účty spravovaných služeb atd, které jste vytvořili na spravované domény.
  * Počítače připojené k spravované doméně dojít ke ztrátě jejich vztah důvěryhodnosti s doménou a musí být odpojit z domény.
  * Nemůžete se přihlásit na tyto počítače pomocí podnikové přihlašovací údaje služby AD. Místo toho použijte přihlašovací údaje místního správce pro počítač.
Odstranění spravované doméně odstranit adresář Azure AD nebo jinak nepříznivě ovlivnit adresáři.
>

Proveďte následující kroky pro odstranění vaší spravované domény služby Azure AD Domain Services:
1. Přejděte na [rozšíření Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na portálu Azure.
2. Klikněte na název vaší spravované domény.

    ![Vyberte doménu, kterou chcete odstranit](./media/getting-started/domain-services-delete-select-domain.png)

3. Na **přehled** klikněte na tlačítko **odstranit** tlačítko.

    ![Odstranit doménu](./media/getting-started/domain-services-delete-domain.png)

4. Potvrďte odstranění, zadejte název domény DNS spravované domény. Klikněte **odstranit** tlačítko po dokončení.

    ![Odstranit potvrzení domény](./media/getting-started/domain-services-delete-domain-confirm.png)

Spravované doméně bude odstraněn ze asi 15-20 minut.

Vezměte v úvahu [sdílení zpětné vazby](active-directory-ds-contact-us.md) a pomoci tak pochopit, co by funkce nápovědy v budoucnu zvolíte Azure AD Domain Services. Tato zpětná vazba pomůže nám momentální službu tak, aby lépe vyhovovalo vašim potřebám nasazení a případy použití.
