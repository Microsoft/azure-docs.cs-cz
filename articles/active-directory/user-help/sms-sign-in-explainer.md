---
title: Uživatelské prostředí přihlašování SMS pro telefonní číslo – Azure AD
description: Další informace o uživatelském prostředí přihlašování SMS pro nová nebo existující telefonní čísla
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/21/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 1a50f2032a978a552205d1bba602249f34f0478a
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661584"
---
# <a name="use-your-phone-number-as-a-user-name"></a>Použití telefonního čísla jako uživatelského jména

Registrace zařízení dává vašemu telefonu přístup ke službám vaší organizace a neumožňuje vaší organizaci přístup k vašemu telefonu. Pokud jste správce, najdete další informace v v [konfiguraci a povolení uživatelů pro ověřování pomocí serveru SMS](../authentication/howto-authentication-sms-signin.md).

Pokud vaše organizace neudělala k dispozici přihlášení SMS, nezobrazí se vám možnost při registraci telefonu s vaším účtem.  

## <a name="when-you-have-a-new-phone-number"></a>Pokud máte nové telefonní číslo

Pokud získáte nový telefon nebo nové číslo a zaregistrujete ho v organizaci, pro kterou je k dispozici přihlášení SMS, budete mít k dispozici normální proces registrace telefonu:

1. Vyberte **Přidat metodu**.
1. Vyberte **telefon**.
1. Zadejte telefonní číslo a vyberte **text mi kód**.
1. Po zadání kódu vyberte **Další**.
1. Zobrazí se výzva, která říká SMS ověřený. Váš telefon byl úspěšně zaregistrován. "

> [!Important]
> Kvůli známému problému se po krátké době přidání telefonního čísla nezaregistruje číslo pro přihlášení SMS. Budete se muset přihlásit pomocí přidaného čísla a pak podle pokynů zaregistrovat číslo pro přihlášení ke službě SMS.

### <a name="when-the-phone-number-is-in-use"></a>Když se telefonní číslo používá

Pokud se pokusíte použít telefonní číslo, které používá někdo jiný ve vaší organizaci, zobrazí se tato zpráva:

![Chybová zpráva, když se vaše telefonní číslo už používá](media/sms-sign-in-explainer/sms-sign-in-error.png)

Kontaktujte svého správce a Napravte problém.

## <a name="when-you-have-an-existing-number"></a>Pokud máte existující číslo

Pokud už používáte telefonní číslo s organizací a máte k dispozici své telefonní číslo, které bude k dispozici jako uživatelské jméno, následující kroky vám pomůžou přihlásit se.

1. Pokud je k dispozici přihlášení SMS, zobrazí se nápis s dotazem, jestli chcete povolit telefonní číslo pro přihlášení SMS:

    :::image type="content" source="media/sms-sign-in-explainer/sms-sign-in-banner.png" alt-text="Snímek obrazovky, který zobrazuje banner pro povolení přihlášení služby SMS pro telefonní číslo se zvolenou akcí povolit." lightbox="media/sms-sign-in-explainer/sms-sign-in-banner.png":::

1. Tlačítko **Povolit** se zobrazí také v případě, že vyberete blikající kurzor na dlaždici způsob telefonu:

    [![Banner pro povolení přihlášení SMS k telefonnímu číslu.](media/sms-sign-in-explainer/sms-sign-in-phone-method.png)](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Chcete-li povolit tuto metodu, vyberte možnost **Povolit**. Zobrazí se výzva k potvrzení akce:

    ![Potvrzovací dialog pro povolení přihlášení SMS k telefonnímu číslu](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Vyberte **Povolit**.

## <a name="when-you-remove-your-phone-number"></a>Po odebrání svého telefonního čísla

1. Chcete-li odstranit telefonní číslo, vyberte tlačítko Odstranit na dlaždici způsob přihlášení k serveru SMS.

    [![Banner pro odstranění přihlášení SMS k telefonnímu číslu.](media/sms-sign-in-explainer/sms-sign-in-delete-method.png)](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Až se zobrazí výzva k potvrzení akce, vyberte **OK**.

Nemůžete odebrat telefonní číslo, které se používá jako výchozí metoda přihlašování. Chcete-li odebrat číslo, bude nutné změnit výchozí metodu přihlašování a pak znovu odebrat telefonní číslo.
