---
title: Zakázat Azure Active Directory Domain Services | Microsoft Docs
description: Zakázat Azure Active Directory Domain Services pomocí Azure Portal
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: d5917ad94212c8b18d4362528bdfbafb02aec808
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171973"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Zakázat Azure Active Directory Domain Services pomocí Azure Portal
V tomto článku se dozvíte, jak pomocí Azure Portal zakázat služby Azure Active Directory (AD) Domain Services pro adresář Azure AD.

> [!WARNING]
> **Odstranění je trvalé a nelze je vrátit zpět.**
> Pokračujte s opatrností! Při odstraňování spravované domény:
>   * Řadiče domény pro spravovanou doménu se zruší a odeberou se z této virtuální sítě.
>   * Data ve spravované doméně se trvale odstraní. Patří sem vlastní organizační jednotky, objekty zásad skupiny, vlastní záznamy DNS, instanční objekty, účty gMSA atd., které jste vytvořili ve spravované doméně.
>   * Počítače připojené ke spravované doméně ztratily vztah důvěryhodnosti s doménou a musí být z domény odpojování.
>   * K těmto počítačům se nemůžete přihlásit pomocí podnikových přihlašovacích údajů služby AD. Místo toho použijte přihlašovací údaje místního správce pro daný počítač.
> Odstranění spravované domény neodstraní adresář služby Azure AD nebo jinak nepříznivě ovlivní adresář.

Chcete-li odstranit Azure AD Domain Services spravovanou doménu, proveďte následující kroky:
1. V Azure Portal přejděte na [rozšíření Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) .
2. Klikněte na název spravované domény.

    ![Vyberte doménu, kterou chcete odstranit.](./media/getting-started/domain-services-delete-select-domain.png)

3. Na stránce **Přehled** klikněte na tlačítko **Odstranit** .

    ![Odstranit doménu](./media/getting-started/domain-services-delete-domain.png)

4. Odstranění potvrďte tak, že zadáte název domény DNS spravované domény. Po dokončení klikněte na tlačítko **Odstranit** .

    ![Potvrzení odstranění domény](./media/getting-started/domain-services-delete-domain-confirm.png)

Odstranění spravované domény může trvat 15-20 minut nebo déle.

Zvažte [sdílení zpětné vazby](contact-us.md) , které nám pomůžou pochopit, jaké funkce vám pomohou v budoucnu zvolit Azure AD Domain Services. Tato zpětná vazba nám pomůže vyvíjet službu, aby lépe vyhovovala vašim potřebám nasazení a případům použití.
