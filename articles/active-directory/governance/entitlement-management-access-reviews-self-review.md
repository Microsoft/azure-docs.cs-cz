---
title: Samoobslužné přezkoumání balíčku přístupu v Azure AD – Správa nároků
description: Přečtěte si, jak zkontrolovat přístup uživatelů k balíčkům přístupu pro správu opravňujících k řízení přístupu ve Azure Active Directory (Preview).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31c44f2423cdc5c43638fe2515757bcb11a9814c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798439"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Samoobslužné přezkoumání balíčku přístupu v Azure AD – Správa nároků

Správa nároků Azure AD zjednodušuje způsob, jakým podniky spravují přístup ke skupinám, aplikacím a webům SharePointu. Tento článek popisuje, jak uživatel provede vlastní kontrolu nad svými přiřazenými balíčky přístupu.

## <a name="open-the-access-review"></a>Otevřít kontrolu přístupu

Chcete-li provést kontrolu přístupu, je třeba nejprve otevřít kontrolu přístupu. K vyhledání a otevření kontroly přístupu použijte následující postup:

1. Můžete obdržet e-mail od Microsoftu, který vás vyzve ke kontrole přístupu. Vyhledejte e-mail a otevřete tak kontrolu přístupu. Tady je příklad e-mailu požadujícího přezkoumání přístupu: 
    
    ![Přístup k e-mailu s asistencí pro kontrolu přístupu](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Klikněte na odkaz **zkontrolovat přístup** .

1. https://myaccess.microsoft.comPokud neobdržíte e-mail, můžete také přejít přímo na adresu a vyhledat nevyřízené recenze přístupu.  (Pro státní správu USA použijte `https://myaccess.microsoft.us` místo toho.)

1. Kliknutím na kontroly **přístupu** na levém navigačním panelu zobrazte seznam nedokončených revizí přístupu, které jste vám přiřadili.


1.  Klikněte na recenzi, kterou chcete začít.

## <a name="perform-the-access-review"></a>Provést kontrolu přístupu

Po otevření kontroly přístupu uvidíte svůj přístup. K provedení kontroly přístupu použijte následující postup:

1.  Rozhodněte, jestli stále potřebujete přístup k přístupovému balíčku. Například projekt, na kterém pracujete, není dokončen, takže stále potřebujete přístup, abyste mohli pokračovat v práci na projektu.

1.  Kliknutím na **Ano** zachováte přístup, nebo kliknutím na **ne** odeberte přístup.
    >[!NOTE]
    >Pokud jste uvedli, že už nepotřebujete přístup, neodebrali jste ho hned z balíčku pro přístup. Po skončení kontroly dojde k odebrání z balíčku pro přístup, nebo pokud správce zastaví kontrolu.

1.  Pokud jste klikli na **Ano**, možná budete muset zahrnout příkaz odůvodnění do pole **důvod** .

1.  Klikněte na **Odeslat**.

K revizi se můžete vrátit, pokud změníte svůj názor a rozhodnete se změnit odpověď před koncem revize.

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu k balíčkům přístupu](entitlement-management-access-reviews-review-access.md) 
