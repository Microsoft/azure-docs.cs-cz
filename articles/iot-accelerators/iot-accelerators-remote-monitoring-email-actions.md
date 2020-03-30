---
title: Akce e-mailu v rámci vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento návod vám ukáže, jak přidat akci e-mailu do nového nebo existujícího pravidla.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: c192ba73da0cfaf1832b6a1e572bd71b250a976b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168361"
---
# <a name="add-an-email-action"></a>Přidání akce e-mailu

E-mailové akce pomáhají zajistit, že upozornění nikdy nezmeškáte. Akci e-mailu můžete přidat k existujícímu pravidlu nebo při vytváření nového pravidla.

K dokončení kroků v tomto návodu, budete potřebovat nasazenou instanci akcelerátoru řešení vzdáleného monitorování ve vašem předplatném Azure.

Chcete-li vytvořit nebo upravit pravidlo, musíte být [ **správcem**nebo mít správná oprávnění](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Úprava existujícího pravidla

Chcete-li přidat akci e-mailu k existujícímu pravidlu, postupujte takto:

1. Přejděte k řešení vzdáleného monitorování.

1. Na **řídicím panelu**přejděte na stránku **Pravidla:**

    ![Stránka Pravidla](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Klikněte na zaškrtávací políčko vedle existujícího pravidla, které chcete upravit, a nahoře klikněte na **Upravit.** Zobrazí se upravitelný panel **Pravidla.**

1. V části **Akce** přepněte **možnost E-mail** **na Zapnuto**.

1. Při prvním povolení akce e-mailu v akcelerátoru řešení je nutné [se přihlásit k aplikaci Outlook](#outlook).

1. Zadejte e-mailovou adresu do pole příjemce a stisknutím klávesy **Enter** pro každou e-mailovou adresu přidejte:

    ![Zadání adresy](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Zadejte předmět e-mailu.

1. Zadejte všechny další poznámky pro příjemce e-mailu jako prostý text. Formátování HTML můžete použít, pokud [upravujete šablonu e-mailu](#htmledit).

1. Zkontrolujte, zda je **stav pravidla** nastaven na **povoleno**.

1. Klikněte na **Použít**.

## <a name="create-a-new-rule"></a>Vytvoření nového pravidla

Při vytváření nového pravidla přidejte akci e-mailu takto:

1. Přejděte k řešení vzdáleného monitorování.

1. Na **řídicím panelu**přejděte na stránku **Pravidla:**

    ![Stránka Pravidla](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Postupujte podle kroků v [části Vytvořit pravidlo](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Postupujte podle kroků v části [vytvořit rozšířené pravidlo](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) až do bodu, kde nastavíte úroveň **závažnosti**. Ještě neklikněte na **Použít.**

1. V části **Akce** přepněte **možnost E-mail** **na Zapnuto**.

1. Při prvním povolení akce e-mailu v akcelerátoru řešení je nutné [se přihlásit k aplikaci Outlook](#outlook).

1. Zadejte e-mailovou adresu do pole příjemce a stisknutím klávesy **Enter** pro každou e-mailovou adresu přidejte:

    ![Zadání adresy](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Zadejte předmět e-mailu.

1. Zadejte všechny další poznámky pro příjemce e-mailu jako prostý text. Formátování HTML můžete použít, pokud [upravujete šablonu e-mailu](#htmledit).

1. Zkontrolujte, zda je **stav pravidla** nastaven na **povoleno**.

1. Klikněte na **Použít**.

Pravidlo s akcí e-mailu je nyní povoleno. Při každém spuštění akce je příjemcům odeslán nový e-mail.

## <a name="sign-in-to-outlook"></a>Přihlášení k Outlooku<a name="outlook"></a>

Při prvním povolení akcee e-mailu v akcelerátoru řešení se musíte přihlásit do aplikace Outlook. Tato akce nastaví e-mailový účet, který odesílá e-mailová oznámení.

> [!NOTE]
> Měli byste vytvořit konkrétní účet aplikace Outlook pouze pro oznámení akcelerátoru řešení a použít tento účet, když povolíte první e-mailovou akci.

### <a name="contributor-role-outlook-setup"></a>Nastavení role přispěvatele Outlook

Pokud někdo v roli **přispěvatele** v předplatném nasadil akcelerátor řešení, aplikace nemá dostatečná oprávnění k nastavení a ověření e-mailových akcí prostřednictvím webového uživatelského rozhraní.

Než začnete, vytvořte si účet Outlooku, který se použije k odesílání e-mailových oznámení z akcelerátoru řešení.

Následující kroky ukazují, jak ručně nastavit a ověřit akce e-mailu:

1. Přejděte na [Azure Portal](https://portal.azure.com).

1. Přejděte na skupinu prostředků pro akcelerátor řešení.

1. Klikněte na **konektor office365**:

    ![Připojení rozhraní API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Kliknutím na banner zahájíte proces autorizace:

    ![Autorizace](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Klepněte na **tlačítko Autorizovat**. Budete vyzváni k přihlášení. Účet, který používáte k přihlášení, by měl být e-mailová adresa, kterou aplikace používá k odesílání e-mailových oznámení:

    ![Tlačítko Autorizovat](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. V dolní části klikněte na **Uložit.** Vaše autorizace bude úspěšná, pokud bude banner pryč.

1. Pokud chcete změnit e-mailovou adresu, ze které jsou oznámení odesílána, klikněte na **Upravit připojení rozhraní API**.

    ![změna e-mailu](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Nastavení outlooku role vlastníka

Pokud někdo v roli **vlastníka** v předplatném nasadil akcelerátor řešení, aplikace může ověřit, že e-mailové akce byly správně nastaveny prostřednictvím webového uživatelského rozhraní.

Než začnete, vytvořte si účet Outlooku, který se použije k odesílání e-mailových oznámení z akcelerátoru řešení.

Následující kroky vám pomohou přihlásit se a nastavit e-mailové akce:

1. Klepnutím se přihlásíte do Outlooku. Přejdete na portál Azure:

   ![Přihlášení k Outlooku](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Klepněte na **tlačítko Autorizovat**. Budete vyzváni k přihlášení. Účet, který používáte k přihlášení, by měl být e-mailová adresa, kterou aplikace používá k odesílání e-mailových oznámení:

1. Klikněte na **Uložit**. Vraťte se k akcelerátoru řešení a aktualizujte stránku.

1. Pokud jste e-mailové oznámení úspěšně nakonfigurovali, zobrazí se tato zpráva:

   ![Úspěšné přihlášení k Outlooku](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>Přizpůsobení kódu HTML e-mailu<a name="htmledit"></a>

Akcelerátor řešení vzdáleného monitorování, který je připraven po vybalení, poskytuje základní šablonu HTML pro akční e-maily. Šablona e-mailu používá hodnoty z nastavení akce e-mailu. Zde je příklad e-mailu:

![příklad e-mailu](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

Následující kroky ukazují, jak upravit šablonu e-mailu HTML. Můžete například zahrnout další informace nebo přidat vlastní obrázky:

1. Klonovat úložiště GitHub v jazyce Java nebo .NET Remote Monitoring:

1. Přejděte do umístění šablony e-mailu:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Můžete přidat nebo odebrat všechny parametry v této šabloně přizpůsobit zprávu. Podle potřeby můžete také přidat, odebrat nebo nahradit volání:

    Například v kódu .NET:`emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Například v kódu Jazyka Java:`this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parametry v šabloně mají `${...}`podobu . Chcete-li odstranit parametr, odstraňte požadovaný řádek. Chcete-li přidat parametr, přidejte řádek s hodnotou, kterou chcete vložit.

1. Chcete-li přidat obrázky nebo vlastní text, aktualizujte přímo soubor EmailTemplate.HTML.

## <a name="throttling"></a>Throttling

Akcelerátor řešení vzdáleného monitorování používá aplikaci Outlook k odesílání e-mailových oznámení. Aplikace Outlook omezuje počet e-mailů odeslaných na [30 e-mailů za minutu](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). E-mailové klienty, kteří dostávají e-maily, mohou také omezit počet e-mailů přijatých za minutu. Ověřte si u svého konkrétního e-mailového klienta omezení. Když nastavíte e-mailové oznámení pro pravidlo, pravidlo by mělo vypočítat průměrné hodnoty za období alespoň jedné minuty a nepoužívat okamžité hodnoty:

![Průměrný výpočet](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Další kroky

Tato příručka vám ukázala, jak přidat akci e-mailu k novému nebo existujícímu pravidlu v rámci řešení vzdáleného monitorování. Průvodce také ukázal, a jak upravit HTML, který definuje formát zprávy.

Dalším navrhovaným krokem je naučit se [používat výstrahy a opravovat problémy se zařízením](iot-accelerators-remote-monitoring-maintain.md).
