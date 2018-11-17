---
title: E-mailové akce v rámci vzdálené monitorování – Azure | Dokumentace Microsoftu
description: Tato příručka ukazuje, jak přidat akci e-mailu do nové nebo stávající pravidlo.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 484cdccef3198f45c08210c9d8fd41f66e6c867d
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51828200"
---
# <a name="add-an-email-action"></a>Přidání akce e-mailu

E-mailové akce pomohou Ujistěte se, že jste Nezmeškejte žádné upozornění. E-mailovou akci můžete přidat do existujícího pravidla nebo vytvořit nové pravidlo.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

Chcete-li vytvořit nebo upravit pravidlo, musí být [ **správce**, nebo máte správná oprávnění](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Úprava existujícího pravidla

Postupujte podle těchto kroků přidejte e-mailové akce pro stávající pravidlo:

1. Přejděte do svého řešení vzdáleného monitorování.

1. Z **řídicí panel**, přejděte **pravidla** stránky:

    ![Na stránce pravidla](./media/iot-accelerators-remote-monitoring-email-actions/rules.png)

1. Klikněte na zaškrtávací políčko vedle existující pravidlo upravit, a potom klikněte na **upravit** v horní části. Upravitelné **pravidlo** otevře se panel.

1. V **akce** části, přepnout **zapnutým e-mailem** k **na**.

1. Při prvním povolení e-mailové akce v akcelerátoru řešení musíte [Přihlaste se k Outlooku](#outlook).

1. Zadejte e-mailovou adresu do pole příjemců a stisknutím klávesy **Enter** klíčů pro každou e-mailovou adresu pro přidání:

    ![Vstupní adresa](./media/iot-accelerators-remote-monitoring-email-actions/address.png)

1. Zadejte předmět e-mailu.

1. Zadejte jakékoli poznámky, další příjemci e-mailu jako prostý text. Můžete použít formátování HTML můžete [upravit šablonu e-mailu](#htmledit).

1. Ujistěte se, že **stav pravidla** je nastavena na **povoleno**.

1. Klikněte na tlačítko **Použít**.

## <a name="create-a-new-rule"></a>Vytvoření nového pravidla

Postupujte podle těchto kroků přidejte akci e-mailu při vytváření nové pravidlo:

1. Přejděte do svého řešení vzdáleného monitorování.

1. Z **řídicí panel**, přejděte **pravidla** stránky:

    ![Na stránce pravidla](./media/iot-accelerators-remote-monitoring-email-actions/rules.png)

1. Postupujte podle pokynů [vytvořte oddíl pravidlo](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Následující kroky v [vytvořit rozšířené pravidlo](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) části až do chvíle, kdy jste nastavili **úroveň závažnosti**. Neklepejte **použít** ještě.

1. V **akce** části, přepnout **zapnutým e-mailem** k **na**.

1. Při prvním povolení e-mailové akce v akcelerátoru řešení musíte [Přihlaste se k Outlooku](#outlook).

1. Zadejte e-mailovou adresu do pole příjemců a stisknutím klávesy **Enter** klíčů pro každou e-mailovou adresu pro přidání:

    ![Vstupní adresa](./media/iot-accelerators-remote-monitoring-email-actions/address.png)

1. Zadejte předmět e-mailu.

1. Zadejte jakékoli poznámky, další příjemci e-mailu jako prostý text. Můžete použít formátování HTML můžete [upravit šablonu e-mailu](#htmledit).

1. Ujistěte se, že **stav pravidla** je nastavena na **povoleno**.

1. Klikněte na tlačítko **Použít**.

Pravidla s akcí e-mailu je nyní zapnuta. Pokaždé, když se aktivuje akci, příjemcům přijde nový e-mail.

## Přihlaste se k aplikaci Outlook <a name="outlook"></a>

Při prvním povolení e-mailové akce v akcelerátor řešení, musíte se přihlásit do aplikace Outlook. Tato akce nastaví e-mailový účet, který se odešle e-mailová oznámení.

> [!NOTE]
> By měl vytvořit zvláštní účet Outlooku jenom pro oznámení akcelerátor řešení a tento účet používat, když povolíte první akci e-mailu.

### <a name="contributor-role-outlook-setup"></a>Nastavení aplikace Outlook role přispěvatele

Pokud někdo ve **Přispěvatel** roli v předplatném nasazený akcelerátor řešení, aplikace nemá dostatečná oprávnění k nastavení a ověření e-mailových akcí pomocí webového uživatelského rozhraní.

Než začnete, vytvořte účet Outlooku, který chcete používat k odesílání e-mailových oznámení z akcelerátor řešení.

Následující kroky ukazují, jak nastavit a ruční ověření e-mailových akcí:

1. Přejděte na [Azure Portal](https://portal.azure.com).

1. Přejděte do skupiny prostředků pro akcelerátor řešení.

1. Klikněte na tlačítko **konektor pro Office 365**:

    ![Připojení API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector.png)

1. Kliknutím na banner zahájíte proces autorizace:

    ![autorizace](./media/iot-accelerators-remote-monitoring-email-actions/connector.png)

1. Klikněte na tlačítko **Autorizovat**. Budete vyzváni k přihlášení. Účet, který používáte k přihlášení by měl být že e-mailová adresa, kterou aplikace používá k odesílání e-mailová oznámení:

    ![Povolit tlačítko](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Klikněte na tlačítko **Uložit** v dolní části. Vaše autorizace bude úspěšné, pokud hlavičky je pryč.

1. Chcete-li změnit e-mailovou adresu, ze kterého jsou od odeslání oznámení, klikněte na tlačítko **připojení API. upravte**.

    ![Změna e-mailu](./media/iot-accelerators-remote-monitoring-email-actions/editemail.png)

### <a name="owner-role-outlook-setup"></a>Nastavení aplikace Outlook role vlastníka

Pokud někdo ve **vlastníka** roli v předplatném nasazený akcelerátor řešení, můžete aplikaci ověřit e-mailových akcí byly správně nastaveny prostřednictvím webového uživatelského rozhraní.

Než začnete, vytvořte účet Outlooku, který chcete používat k odesílání e-mailových oznámení z akcelerátor řešení.

Následující kroky umožňují přihlášení a nastavení e-mailových akcí:

1. Klikněte na tlačítko pro přihlášení k aplikaci Outlook. Potom se přesunete na na webu Azure portal:

  ![Přihlaste se k aplikaci Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook.png)

1. Klikněte na tlačítko **Autorizovat**. Budete vyzváni k přihlášení. Účet, který používáte k přihlášení by měl být že e-mailová adresa, kterou aplikace používá k odesílání e-mailová oznámení:

1. Klikněte na **Uložit**. Vraťte se do akcelerátor řešení a aktualizujte stránku.

1. Pokud jste úspěšně nakonfigurovali e-mailové oznámení, zobrazí tato zpráva:

  ![Přihlášení úspěšné aplikace Outlook](./media/iot-accelerators-remote-monitoring-email-actions/success.png)

## Přizpůsobení e-mailu ve formátu HTML <a name="htmledit"></a>

Out-of-the-box, akcelerátor řešení vzdálené monitorování poskytuje základní šablonu HTML pro akci e-mailů. Šablona e-mailu používá hodnoty z akce nastavení e-mailu. Tady je příklad e-mailu:

![Příklad e-mailu](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate.png)

Následující kroky ukazují, jak upravit šablonu e-mailu ve formátu HTML. Například můžete zahrnout další informace nebo přidat vlastní Image:

1. Naklonujte úložiště GitHub pro vzdálené monitorování .NET nebo Javě:

1. Přejděte do umístění šablony e-mailu:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Můžete přidat nebo odebrat všechny parametry v této šabloně chcete přizpůsobit zprávu. Můžete také přidat, odebrat nebo nahradit volání podle potřeby:

    Například v kódu .NET:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Například v kódu v jazyce Java:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parametry v šabloně mít formu `${...}`. Pokud chcete odstranit parametr, odstraňte požadované řádku. Přidání parametru, přidejte řádek s hodnotou pro vložení.

1. Chcete-li přidat obrázky nebo vlastní text, aktualizujte soubor EmailTemplate.HTML přímo.

## <a name="throttling"></a>Throttling

Akcelerátor řešení vzdálené monitorování používá k odesílání e-mailová oznámení Outlooku. Aplikace Outlook omezuje počet e-mailů do [30 e-mailů za 1 minutu](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). E-mailoví klienti příjem e-mailů může také omezit počet e-mailech přijatých za minutu. Obraťte se na vaše konkrétní e-mailovém klientovi na omezení. Při nastavování e-mailové oznámení pro pravidlo by měl pravidlo vypočítá průměrné hodnoty po dobu minimálně jedné minuty a nepoužívat rychlé hodnoty:

![Výpočtu průměru](./media/iot-accelerators-remote-monitoring-email-actions/calculation.png)

## <a name="next-steps"></a>Další postup

Tato příručka vám ukázali, jak přidat akci e-mailu do nové nebo existující pravidla v rámci řešení vzdáleného monitorování. Průvodce také jsme si ukázali, je a jak upravit kód HTML, který definuje formát zprávy.

Navrhované dalším krokem je další [způsob použití výstrah a řešením problémů zařízení](iot-accelerators-remote-monitoring-maintain.md).
