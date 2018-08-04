---
title: Zakažte Azure Active Directory Domain Services | Dokumentace Microsoftu
description: Zakažte Azure Active Directory Domain Services pomocí webu Azure portal
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
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: af24c7f9b721aab4f1ab810cf42f737fd14bdf88
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505571"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Zakažte Azure Active Directory Domain Services pomocí webu Azure portal
Tento článek ukazuje, jak zakázat domény služby Azure Active Directory (AD) pro váš adresář Azure AD pomocí webu Azure portal.

> [!WARNING]
> **Odstranění je trvalá a nejde ho vrátit.**
> Postupujte opatrně. Při odstranění spravované domény:
  * Řadiče domény pro spravovanou doménu se zrušení zřízení a odebrán z virtuální sítě.
  * Data ve spravované doméně se trvale odstraní. To zahrnuje vlastní organizační jednotky, objektů zásad skupiny, vlastní záznamy DNS, objektů služby, účty Gmsa atd., které jste vytvořili ve spravované doméně.
  * Počítače připojené k spravované doméně dojít ke ztrátě jejich vztah důvěryhodnosti s doménou a je potřeba odpojit z domény.
  * Nemůžete se přihlásit k těmto počítačům pomocí firemních přihlašovacích údajů AD. Místo toho použijte přihlašovací údaje místního správce pro počítač.
Odstraňuje se spravovaná doména odstranění adresáře služby Azure AD nebo jinak negativně ovlivnit adresáři.
>

Proveďte následující kroky pro odstranění spravované domény služby Azure AD Domain Services:
1. Přejděte [rozšíření Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na webu Azure Portal.
2. Klikněte na název vaší spravované domény.

    ![Vyberte doménu odstranit](./media/getting-started/domain-services-delete-select-domain.png)

3. Na **přehled** stránky, klikněte na tlačítko **odstranit** tlačítko.

    ![Odstranit doménu](./media/getting-started/domain-services-delete-domain.png)

4. Potvrďte odstranění, zadejte název domény DNS pro spravovanou doménu. Klikněte na tlačítko **odstranit** tlačítko až budete hotovi.

    ![Domény potvrzení odstranění](./media/getting-started/domain-services-delete-domain-confirm.png)

Spravovaná doména je odstranit během přibližně 15 až 20 minut.

Vezměte v úvahu [sdílení zpětné vazby](active-directory-ds-contact-us.md) který nám pomůže zjistit, co by funkce pomáhají jste si zvolili Azure AD Domain Services v budoucnu. Tato zpětná vazba nám vyvíjí služby, aby lépe vyhovovala požadavků na nasazení a případy použití.
