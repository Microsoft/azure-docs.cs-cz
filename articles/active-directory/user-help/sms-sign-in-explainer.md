---
title: Uživatelské prostředí pro přihlášení pomocí SMS pro telefonní číslo (preview) – Azure AD
description: Další informace o uživatelském prostředí pro přihlášení pomocí SMS pro nová nebo stávající telefonní čísla
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378831"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Použití telefonního čísla jako uživatelského jména (náhled)

Registrace zařízení umožňuje telefonu přístup ke službám vaší organizace a neumožňuje vaší organizaci přístup k telefonu. Pokud jste správce, můžete najít další informace v [konfigurovat a povolit uživatelům ověřování pomocí sms](../authentication/howto-authentication-sms-signin.md).

Pokud vaše organizace nezpřístupnila přihlášení pomocí SMS, při registraci telefonu u účtu se vám nezobrazí možnost.  

## <a name="when-you-have-a-new-phone-number"></a>Když máte nové telefonní číslo

Pokud získáte nový telefon nebo nové číslo a zaregistrujete jej u organizace, pro kterou je k dispozici přihlášení pomocí SMS, dochází k normálnímu procesu registrace telefonu:

1. Vyberte **přidat metodu**.
1. Vyberte **Telefon**.
1. Zadejte telefonní číslo a vyberte **Text mi kód**.
1. Po zadání kódu vyberte **Další**.
1. Zobrazí se výzva s nápisem "SMS ověřeno. Váš telefon byl úspěšně zaregistrován."

> [!Important]
> Vzhledem ke známému problému ve verzi Preview, na krátkou dobu přidání telefonního čísla nebude registrovat číslo pro sms přihlášení. Budete se muset přihlásit pomocí přidaného čísla a potom podle pokynů zaregistrovat číslo pro přihlášení sms.

### <a name="when-the-phone-number-is-in-use"></a>Když je telefonní číslo používáno

Pokud se pokusíte použít telefonní číslo, které používá někdo jiný ve vaší organizaci, zobrazí se následující zpráva:

![Chybová zpráva, když už je vaše telefonní číslo použito](media/sms-sign-in-explainer/sms-sign-in-error.png)

Obraťte se na svého správce, abyste problém napájeli.

## <a name="when-you-have-an-existing-number"></a>Pokud máte existující číslo

Pokud již používáte telefonní číslo s organizací a pomocí telefonního čísla jako uživatelského jména budete k dispozici, můžete se přihlásit pomocí následujících kroků.

1. Pokud je k dispozici přihlášení pomocí SMS, zobrazí se banner s dotazem, zda chcete povolit telefonní číslo pro přihlášení k SMS:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Pokud vyberete stříšku na dlaždici metody telefonu, zobrazí se také tlačítko **Povolit:**

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Chcete-li metodu povolit, vyberte **možnost Povolit**. Budete vyzváni k potvrzení akce:

    ![Potvrzovací dialogové okno pro povolení přihlášení pomocí sms pro telefonní číslo](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Vyberte **Povolit**.

## <a name="when-you-remove-your-phone-number"></a>Když odeberete své telefonní číslo

1. Chcete-li telefonní číslo odstranit, vyberte tlačítko odstranit na dlaždici způsobu přihlášení pomocí serveru SMS.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Po zobrazení výzvy k potvrzení akce vyberte **možnost OK**.

Telefonní číslo, které se používá jako výchozí metoda přihlášení, nelze odebrat. Chcete-li toto číslo odebrat, bylo by třeba změnit výchozí metodu přihlášení a potom telefonní číslo znovu odebrat.
