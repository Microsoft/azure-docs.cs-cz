---
title: Kontrola přístupu k rolím Azure AD v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak zkontrolovat přístup k rolím Služby Azure Active Directory v privilegované správě identit Azure AD (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847095"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Kontrola přístupu k rolím Azure AD ve správě privilegovaných identit

Správa privilegovaných identit (PIM) zjednodušuje způsob, jakým podniky spravují privilegovaný přístup k prostředkům ve službě Azure Active Directory (AD) a dalších online službách Microsoftu, jako je Office 365 nebo Microsoft Intune. Podle pokynů v tomto článku můžete úspěšně zkontrolovat přiřazené role.

Pokud jste přiřazeni k roli správce, správce privilegované role vaší organizace vás může požádat, abyste pravidelně potvrzovali, že tuto roli pro svou úlohu stále potřebujete. Můžete získat e-mail, který obsahuje odkaz, nebo můžete přejít přímo na [portál Azure](https://portal.azure.com) a začít.

Pokud jste správce privilegovaných rolí nebo globální správce, který má zájem o kontroly přístupu, přečtěte si další podrobnosti v části [Jak zahájit kontrolu přístupu](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Přidání dlaždice řídicího panelu PIM

Pokud nemáte službu Azure AD Privileged Identity Management připnutou na řídicím panelu na vašem portálu Azure, můžete začít podle těchto kroků.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Vyberte své uživatelské jméno v pravém horním rohu portálu Azure a vyberte adresář, kde budete pracovat.
3. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.
4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

## <a name="approve-or-deny-access"></a>Schválení nebo odepření přístupu

Když schválíte nebo odepřete přístup, pouze recenzentovi sdělit, zda tuto roli stále používáte, či nikoli. Pokud chcete v roli zůstat, zvolte **Schválit,** nebo **Odepřít,** pokud už přístup nepotřebujete. Váš stav se nezmění hned, dokud recenzent nepoužije výsledky.
Chcete-li vyhledat a dokončit kontrolu přístupu, postupujte takto:

1. Ve službě Správa privilegovaných identit vyberte **zkontrolovat privilegovaný přístup**. Pokud máte nějaké nevyřízené kontroly přístupu, zobrazí se na stránce kontroly Azure AD **Access.**
2. Vyberte recenzi, kterou chcete dokončit.
3. Pokud jste recenzi nevytvořili, zobrazí se jako jediný uživatel v recenzi. Zaškrtněte políčko vedle svého jména.
4. Zvolte **schválit** nebo **odepřít**. Možná budete muset uvést důvod svého rozhodnutí do textového pole **Poskytnout důvod.**  
5. Zavřete okno **Revize rolí Azure AD.**

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další kroky

- [Provedení kontroly přístupu rolí prostředků Azure v nástroji PIM](pim-resource-roles-perform-access-review.md)
