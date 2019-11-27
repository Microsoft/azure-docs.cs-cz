---
title: Operace a sestavy ochrany heslem – Azure Active Directory
description: Operace po nasazení a vytváření sestav ochrany heslem Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98668fc836aa21bdd14831c4a801557cdab5a202
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381669"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Provozní procedury ochrany heslem Azure AD

Po dokončení [instalace ochrany heslem Azure AD](howto-password-ban-bad-on-premises-deploy.md) v místním prostředí existuje několik položek, které je potřeba nakonfigurovat v Azure Portal.

## <a name="configure-the-custom-banned-password-list"></a>Konfigurovat vlastní seznam zakázaných hesel

Postupujte podle pokynů v článku [Konfigurace vlastního seznamu zakázaných hesel,](howto-password-ban-bad-configure.md) kde najdete postup přizpůsobení seznamu zakázaných hesel ve vaší organizaci.

## <a name="enable-password-protection"></a>Povolení ochrany heslem

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do **Azure Active Directory**, **metod ověřování**a **ochrany heslem**.
1. Nastavte **možnost Povolit ochranu heslem ve službě Windows Server Active Directory** na **hodnotu Ano** .
1. Jak je uvedeno v [Průvodci nasazením](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), doporučujeme nejprve nastavit **režim** na **audit** .
   * Až budete s funkcí spokojeni, můžete **režim** přepnout na **vynutilo** .
1. Klikněte na **Uložit**.

![Povolení součástí ochrany heslem služby Azure AD v Azure Portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Režim auditování

Režim auditu je určený jako způsob, jak software spustit v režimu "co if". Každá služba agenta řadiče domény vyhodnotí příchozí heslo podle aktuálně aktivních zásad. Pokud je aktuální zásada nakonfigurovaná tak, aby byla v režimu auditování, je výsledkem "špatných" hesla zpráva protokolu událostí, ale budou přijata. Toto je jediný rozdíl mezi režimem auditu a vymáhání. všechny ostatní operace mají stejný provoz.

> [!NOTE]
> Microsoft doporučuje, aby se počáteční nasazení a testování vždy spouštělo v režimu auditování. Události v protokolu událostí by se pak měly monitorovat, aby se pokusily odhadnout, jestli by nějaké existující provozní procesy po povolení režimu vykonání byly nerušené.

## <a name="enforce-mode"></a>Režim vymáhání

Režim vymáhání je určený jako konečná konfigurace. Stejně jako v režimu auditování výše každá služba agenta řadiče domény vyhodnocuje příchozí hesla podle aktuálně aktivních zásad. Pokud je povolený režim vymáhání, heslo, které se považuje za nezabezpečené podle zásad, se odmítne.

Když je v režimu vynucení odmítnuto heslo agent řadiče domény služby Azure AD heslem, viditelný dopad zobrazený koncovým uživatelem se shoduje s tím, jak by se jednalo o odmítnutí hesla tradičním vynuceným nastavením složitosti hesel. Uživatel může například na obrazovce pro heslo pro Windows logon\change zobrazit následující tradiční chybovou zprávu:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Tato zpráva je pouze jedním z příkladů možných výsledků. Konkrétní chybová zpráva se může lišit v závislosti na skutečném softwaru nebo scénáři, který se pokouší nastavit nezabezpečené heslo.

Ovlivnění koncoví uživatelé možná budou muset spolupracovat s pracovníky IT, aby porozuměli novým požadavkům a mohli si vybrat zabezpečená hesla.

> [!NOTE]
> Ochrana heslem Azure AD nemá žádné řízení pro konkrétní chybovou zprávu, která se zobrazí v klientském počítači, když se odmítne slabé heslo.

## <a name="enable-mode"></a>Zapnout režim

Toto nastavení by mělo zůstat ve výchozím stavu povoleno (Ano). Konfigurace tohoto nastavení na zakázáno (ne) způsobí, že všichni nasazení agenti řadiče domény Azure AD pro ochranu heslem přejdou do režimu quiescent, ve kterém jsou všechna hesla přijímána, a nebudou spuštěny žádné ověřovací aktivity (například ne i události auditu). bude vygenerováno).

## <a name="next-steps"></a>Další kroky

[Monitorování ochrany hesel služby Azure AD](howto-password-ban-bad-on-premises-monitor.md)
