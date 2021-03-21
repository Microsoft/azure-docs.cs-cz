---
title: Konfigurace e-mailových oznámení pro výstrahy Azure Security Center
description: Přečtěte si, jak vyladit typy e-mailů odesílaných Azure Security Center v případě výstrah zabezpečení.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/09/2021
ms.author: memildin
ms.openlocfilehash: 342904a3ae996fe8c2eeddf2edfbc4283a3d03eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439438"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Konfigurace e-mailových oznámení pro výstrahy zabezpečení 

Výstrahy zabezpečení musí mít přístup ke správným lidem ve vaší organizaci. Ve výchozím nastavení Security Center vlastníkům předplatného e-mailů vždy, když se u svého předplatného spustí výstraha s vysokou závažností. Tato stránka vysvětluje, jak tato oznámení přizpůsobit.

Pokud chcete definovat vlastní předvolby pro e-maily oznámení, stránka nastavení **e-mailových oznámení** Azure Security Center vám umožní vybrat tyto možnosti:

- ***kdo* má dostávat oznámení** – e-maily je možné odeslat pro výběr jednotlivců nebo pro kohokoli s určitou rolí Azure pro předplatné. 
- k ***čemu* by se měly informovat** – upravte úrovně závažnosti, pro které by Security Center měla odesílat oznámení.

Aby nedošlo k únavě výstrah, Security Center omezí objem odchozích e-mailů. U každého předplatného Security Center odesílá:

- maximálně jeden e-mail za **6 hodin** (4 e-maily za den) pro výstrahy **s vysokou závažností**
- maximálně jeden e-mail za **12 hodin** (2 e-maily za den) pro výstrahy se **střední závažností**
- maximálně jeden e-mail za **24 hodin** pro upozornění **s nízkou závažností**

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Konfigurace podrobností o kontaktu, který bude dostávat e-maily o výstrahách zabezpečení." :::
 
## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Obecná dostupnost (GA)|
|Stanov|Free|
|Požadované role a oprávnění:|**Správce zabezpečení**<br>**Vlastník předplatného** |
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>Přizpůsobení e-mailových oznámení výstrah zabezpečení prostřednictvím portálu<a name="email"></a>
Můžete posílat e-mailová oznámení jednotlivcům nebo všem uživatelům s konkrétními rolemi Azure.

1. V oblasti **nastavení & ceny** Security Center vyberte příslušné předplatné a zvolte **e-mailová oznámení**.

1. Určete příjemce oznámení pomocí jedné nebo obou těchto možností:

    - V rozevíracím seznamu vyberte z dostupných rolí.
    - Zadejte konkrétní e-mailové adresy oddělené čárkami. Počet e-mailových adres, které můžete zadat, není nijak omezený.

1. Pokud chcete u svého předplatného použít kontaktní informace zabezpečení, vyberte **Uložit**.

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>Přizpůsobení e-mailových oznámení výstrah prostřednictvím rozhraní API
E-mailová oznámení můžete spravovat také pomocí zadaných REST API. Úplné podrobnosti najdete v [dokumentaci k rozhraní SecurityContacts API](/rest/api/securitycenter/securitycontacts).

Toto je příklad těla žádosti o požadavek PUT při vytváření konfigurace kontaktu zabezpečení:

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "High"
        },
        "phone": ""
    }
}
```


## <a name="see-also"></a>Viz také
Další informace o výstrahách zabezpečení najdete na následujících stránkách:

- [Výstrahy zabezpečení – referenční průvodce](alerts-reference.md)– Přečtěte si o výstrahách zabezpečení, které se můžou zobrazit v modulu Azure Security Center ochrany před internetovými útoky.
- [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md)– Přečtěte si, jak spravovat výstrahy zabezpečení a reagovat na ně.
- [Automatizace pracovního postupu](workflow-automation.md)– automatizace odpovědí na výstrahy pomocí vlastní logiky oznámení