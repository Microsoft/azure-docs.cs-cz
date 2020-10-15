---
title: Akce e-mailu v rámci vzdáleného monitorování – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak přidat akci e-mailu k novému nebo existujícímu pravidlu.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 2776b18dc3faded103b49e8ff58a8a0a6dccf144
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074460"
---
# <a name="add-an-email-action"></a>Přidat akci e-mailu

Akce e-mailu vám pomůžou zajistit, že nikdy nebudou výstrahy. Akci e-mailu můžete přidat do existujícího pravidla nebo při vytváření nového pravidla.

K dokončení kroků v tomto průvodci, potřebujete ve svém předplatném Azure nasazenou instanci akcelerátoru řešení vzdáleného monitorování.

Chcete-li vytvořit nebo upravit pravidlo, musíte být [ **správcem**nebo mít správná oprávnění](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Úprava existujícího pravidla

Pomocí těchto kroků můžete přidat akci e-mailu do existujícího pravidla:

1. Přejděte do řešení vzdáleného monitorování.

1. Z **řídicího panelu**přejděte na stránku **pravidla** :

    ![Stránka Pravidla](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Klikněte na zaškrtávací políčko vedle existujícího pravidla, které chcete upravit, a potom v horní části klikněte na **Upravit** . Zobrazí se panel upravitelných **pravidel** .

1. V části **Akce** přepněte na **zapnuto** **e-mailem** .

1. Když v akcelerátoru řešení poprvé povolíte akci e-mailu, musíte se [přihlásit k aplikaci Outlook](#outlook).

1. Zadejte e-mailovou adresu do pole příjemce a stiskněte klávesu **ENTER** pro každou e-mailovou adresu, kterou chcete přidat:

    ![Položka adresy](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Zadejte předmět e-mailu.

1. Zadejte další poznámky pro příjemce e-mailu jako prostý text. Pokud [upravíte šablonu e-mailu](#htmledit), můžete použít formátování HTML.

1. Ujistěte se, že je **stav pravidla** nastavený na **povoleno**.

1. Klikněte na **Použít**.

## <a name="create-a-new-rule"></a>Vytvoření nového pravidla

Pomocí těchto kroků můžete přidat akci e-mailu při vytváření nového pravidla:

1. Přejděte do řešení vzdáleného monitorování.

1. Z **řídicího panelu**přejděte na stránku **pravidla** :

    ![Stránka Pravidla](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Postupujte podle kroků v [části Vytvoření pravidla](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Postupujte podle kroků uvedených v části [Vytvoření rozšířeného pravidla](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) do bodu, ve kterém nastavíte **úroveň závažnosti**. Neklikejte ještě na **použít** .

1. V části **Akce** přepněte na **zapnuto** **e-mailem** .

1. Když v akcelerátoru řešení poprvé povolíte akci e-mailu, musíte se [přihlásit k aplikaci Outlook](#outlook).

1. Zadejte e-mailovou adresu do pole příjemce a stiskněte klávesu **ENTER** pro každou e-mailovou adresu, kterou chcete přidat:

    ![Položka adresy](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Zadejte předmět e-mailu.

1. Zadejte další poznámky pro příjemce e-mailu jako prostý text. Pokud [upravíte šablonu e-mailu](#htmledit), můžete použít formátování HTML.

1. Ujistěte se, že je **stav pravidla** nastavený na **povoleno**.

1. Klikněte na **Použít**.

Pravidlo s akcí e-mail je nyní povoleno. Pokaždé, když se spustí akce, pošle se příjemci nový e-mail.

## <a name="sign-in-to-outlook"></a>Přihlásit se k Outlooku <a name="outlook"></a>

Když v akcelerátoru řešení poprvé povolíte akci e-mailu, musíte se přihlásit k aplikaci Outlook. Tato akce nastaví e-mailový účet, který odesílá e-mailová oznámení.

> [!NOTE]
> Měli byste vytvořit konkrétní účet Outlooku jenom pro oznámení akcelerátoru řešení a použít tento účet, když povolíte svou první e-mailovou akci.

### <a name="contributor-role-outlook-setup"></a>Nastavení role přispěvatele Outlooku

Pokud někdo v roli **přispěvatele** v předplatném nasadil akcelerátor řešení, aplikace nemá dostatečná oprávnění k nastavení a ověření e-mailových akcí přes webové uživatelské rozhraní.

Než začnete, vytvořte si účet Outlooku, který použijete k posílání e-mailových oznámení z akcelerátoru řešení.

Následující kroky ukazují, jak ručně nastavit a ověřit akce e-mailu:

1. Přejděte na [Azure Portal](https://portal.azure.com).

1. Přejděte do skupiny prostředků pro akcelerátor řešení.

1. Klikněte na **konektor Office 365**:

    ![Připojení rozhraní API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Kliknutím na banner zahajte proces autorizace:

    ![Autorizace](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Klikněte na **autorizovat**. Budete vyzváni k přihlášení. Účet, pomocí kterého se přihlašujete, musí být e-mailová adresa, kterou aplikace používá k odesílání e-mailových oznámení:

    ![Autorizovat – tlačítko](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. V dolní části klikněte na **Uložit** . V případě, že dojde k úspěšnému dokončení vaší registrace, bude vaše autorizace úspěšná.

1. Pokud chcete změnit e-mailovou adresu, ze které se odesílají oznámení, klikněte na **Upravit připojení rozhraní API**.

    ![změnit e-mail](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Role vlastníka – nastavení Outlooku

Pokud někdo v **nadřazené** roli v předplatném nasadil akcelerátor řešení, aplikace může ověřit, že se e-mailové akce správně nastavily přes webové uživatelské rozhraní.

Než začnete, vytvořte si účet Outlooku, který použijete k posílání e-mailových oznámení z akcelerátoru řešení.

Následující kroky vám pomůžou se přihlašovat a nastavit e-mailové akce:

1. Kliknutím se přihlásíte do Outlooku. Přejdete na Azure Portal:

   ![Přihlásit se k Outlooku](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Klikněte na **autorizovat**. Budete vyzváni k přihlášení. Účet, pomocí kterého se přihlašujete, musí být e-mailová adresa, kterou aplikace používá k odesílání e-mailových oznámení:

1. Klikněte na **Uložit**. Vraťte se do akcelerátoru řešení a aktualizujte stránku.

1. Pokud jste úspěšně nakonfigurovali e-mailové oznámení, zobrazí se tato zpráva:

   ![Úspěšné přihlášení do Outlooku](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>Přizpůsobení e-mailové zprávy HTML <a name="htmledit"></a>

Předem připravené – akcelerátor řešení vzdáleného monitorování poskytuje základní šablonu HTML pro e-maily akcí. E-mailová šablona používá hodnoty z nastavení akce e-mailu. Tady je příklad e-mailu:

![Příklad e-mailu](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

Následující kroky ukazují, jak upravit šablonu e-mailu HTML. Můžete například zahrnout další informace nebo přidat vlastní image:

1. Naklonujte úložiště GitHub pro vzdálené monitorování Java nebo .NET:

1. Přejděte do umístění e-mailové šablony:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Můžete přidat nebo odebrat parametry v této šabloně pro přizpůsobení zprávy. V případě potřeby můžete také přidat, odebrat nebo nahradit volání:

    Například v kódu .NET:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Například v kódu Java:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parametry v šabloně mají formu `${...}` . Chcete-li odstranit parametr, odstraňte požadovaný řádek. Chcete-li přidat parametr, přidejte řádek s hodnotou, která má být vložena.

1. Chcete-li přidat obrázky nebo vlastní text, aktualizujte soubor EmailTemplate.HTML přímo.

## <a name="throttling"></a>Throttling

Akcelerátor řešení vzdáleného monitorování používá aplikaci Outlook k posílání e-mailových oznámení. Outlook omezuje počet e-mailů odeslaných na [30 e-mailů za 1 minutu](/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). E-mailové klienty, kteří obdrží e-maily, mohou také omezit počet přijatých e-mailů za minutu. V případě omezení si Projděte konkrétního e-mailového klienta. Při nastavování e-mailových oznámení pro pravidlo by mělo pravidlo počítat průměrné hodnoty za období nejméně jedné minuty a nepoužívat okamžité hodnoty:

![Průměrný výpočet](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Další kroky

Tato příručka vám ukázala, jak přidat akci e-mailu do nového nebo existujícího pravidla v rámci řešení vzdáleného monitorování. Průvodce vám také ukázal, jak upravit kód HTML, který definuje formát zprávy.

Navržený další krok se naučíte [používat výstrahy a opravovat problémy zařízení](iot-accelerators-remote-monitoring-maintain.md).