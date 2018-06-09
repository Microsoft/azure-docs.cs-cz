---
title: Postup kontrola přístupu | Microsoft Docs
description: Zjistěte, jak provést kontrolu s aplikací Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 885c7a144a2ac588e1f1b32fb03b7ed72d058e94
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233447"
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>Jak provádět kontrola přístupu v Azure AD Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management zjednodušuje, jak spravovat podniky privilegovaný přístup k prostředkům v Azure AD a dalším službám Microsoft online jako je Office 365 nebo Microsoft Intune.  

Pokud jste přiřazeni k roli správce privilegovaných rolí správce ve vaší organizaci může požádáni, abyste pravidelně potvrďte, že je stále nutné této role pro úlohu. Může získat e-mailu, který obsahuje odkaz, nebo můžete přejít přímo na [portál Azure](https://portal.azure.com). Postupujte podle kroků v tomto článku provést samoobslužné zkontrolujte přiřazené role.

Pokud jste správcem privilegovaných rolí zájem o recenze přístup, získat další podrobnosti o [spuštění kontrola přístupu](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Přidání aplikace Privileged Identity Management
Můžete použít aplikaci Azure AD Privileged Identity Management (PIM) v [portál Azure](https://portal.azure.com/) k provedení zkontrolovali.  Pokud nemáte aplikaci Azure AD Privileged Identity Management na portálu, postupujte podle těchto kroků, abyste mohli začít.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte své uživatelské jméno v pravém horním rohu portálu Azure a vyberte adresář, kde vám budou pracovat.
3. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.
4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

## <a name="approve-or-deny-access"></a>Schválí nebo zamítne přístup
Při schválit nebo zamítnout přístup, můžete se právě informuje kontrolorovi zda můžete dál používat tuto roli nebo ne. Zvolte **schválit** Pokud chcete, aby zůstali v roli, nebo **Odepřít** Pokud nepotřebujete přístup. Stav vaší nedojde ke změně hned, dokud kontrolorovi platí výsledky.
Postupujte podle těchto kroků můžete najít a dokončit kontrolu přístupu:

1. V aplikaci PIM vyberte **zkontrolujte privilegovaný přístup**. Pokud máte všechny recenze čekající přístup, zobrazí se v okně recenze přístup k Azure AD.
2. Vyberte kontrolní, kterou chcete provést.
3. Pokud jste vytvořili kontrola, můžete zobrazit, jako je jediným uživatelem v kontrola. Vyberte zaškrtnutí políčka vedle vašeho jména.
4. Vyberte buď **schválit** nebo **Odepřít**. Musíte zahrnout důvod pro vaše rozhodnutí v **zadat příslušný důvod** textové pole.  
5. Zavřít **rolí zkontrolujte Azure AD** okno.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
