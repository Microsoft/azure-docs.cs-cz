---
title: Vlastní kontrola balíčku přístupu ve správě oprávnění Azure AD
description: Přečtěte si, jak zkontrolovat přístup uživatelů k balíčkům přístupu ke správě nároků v recenzích přístupu ke službě Azure Active Directory (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967774"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Vlastní kontrola balíčku přístupu ve správě oprávnění Azure AD

Správa nároků Azure AD zjednodušuje způsob, jakým podniky spravují přístup ke skupinám, aplikacím a webům SharePointu. Tento článek popisuje, jak uživatel provádí vlastní kontrolu jejich přiřazený přístup balíčky.

## <a name="open-the-access-review"></a>Otevření kontroly přístupu

Chcete-li provést kontrolu přístupu, musíte nejprve otevřít kontrolu přístupu. Pomocí následujícího postupu vyhledejte a otevřete kontrolu přístupu:

1. Můžete obdržet e-mail od společnosti Microsoft, který vás požádá o kontrolu přístupu. Vyhledejte e-mail a otevřete kontrolu přístupu. Zde je příklad e-mailu s žádostí o kontrolu přístupu: 
    
    ![Přístup k e-mailu seberecenzenta](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Klikněte na odkaz **Zkontrolovat přístup.**

1. Pokud neobdržíte https://myaccess.microsoft.com e-mail, můžete také přejít přímo na a vyhledat nevyřízené recenze přístupu.  (Pro vládu USA `https://myaccess.microsoft.us` použijte místo.)

1. Kliknutím na **Access recenze** na levém navigačním panelu zobrazíte seznam čekajících kontrol přístupu, které vám byly přiřazeny.


1.  Klikněte na recenzi, kterou chcete zahájit.

## <a name="perform-the-access-review"></a>Provedení kontroly přístupu

Po otevření kontroly přístupu uvidíte svůj přístup. Ke kontrole přístupu použijte následující postup:

1.  Rozhodněte se, zda stále potřebujete přístup k přístupovému balíčku. Například projekt, na kterém pracujete, není dokončen, takže stále potřebujete přístup k pokračování v práci na projektu.

1.  Chcete-li zachovat přístup, klepněte na tlačítko **Ano** nebo klepnutím na **tlačítko Ne** přístup odeberte.
    >[!NOTE]
    >Pokud jste uvedli, že již nepotřebujete přístup, nebudete okamžitě odebráni z přístupového balíčku. Po ukončení kontroly nebo po zastavení kontroly bude kontrola odebrána z přístupového balíčku.

1.  Pokud jste klikli na **Ano**, bude pravděpodobně nutné zahrnout do pole **Důvod** příkaz odůvodnění.

1.  Klepněte na **tlačítko Odeslat**.

Pokud si to rozmyslíte a rozhodnete se změnit odpověď před koncem recenze, můžete se ke kontrole vrátit.

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu k přístupovým balíčkům](entitlement-management-access-reviews-review-access.md) 
