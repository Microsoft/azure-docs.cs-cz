---
title: Zadání podrobností o kontaktu zabezpečení v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak zadat podrobnosti kontaktu zabezpečení v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 9fbd63e1b46b837350be720fadf68777927f9bff
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904762"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>Nastavení e-mailových oznámení pro výstrahy zabezpečení 

Pokud chcete zajistit, aby se do vašeho prostředí zobrazovaly informace o výstrahách zabezpečení ve vaší organizaci, zadejte na stránce nastavení **e-mailových oznámení** jejich e-mailové adresy.

Při nastavování oznámení můžete nakonfigurovat e-maily, které se mají posílat konkrétním jednotlivcům, nebo komukoli s určitou rolí Azure pro předplatné. 

Aby nedošlo k únavě výstrah, Security Center omezí objem odchozích e-mailů. U každého předplatného Security Center odesílá:

- maximálně **čtyři** e-maily za den pro výstrahy **s vysokou závažností**
- maximálně **dva** e-maily za den pro upozornění **středních závažnosti**
- maximálně **jeden** e-mail za den pro upozornění **s nízkou závažností**


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Konfigurace podrobností o kontaktu, který bude dostávat e-maily o výstrahách zabezpečení." :::

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|Free|
|Požadované role a oprávnění:|**Správce zabezpečení**<br>**Vlastník předplatného** |
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov (částečný)<br>![No](./media/icons/no-icon.png) Čína gov, jiné gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>Nastavení e-mailových oznámení pro výstrahy <a name="email"></a>

Můžete posílat e-mailová oznámení jednotlivcům nebo všem uživatelům s konkrétními rolemi Azure.

1. Z oblasti **Nastavení cenové &** Security Center, relevantní předplatné a vybrat **e-mailová oznámení**.

1. Zadejte příjemce oznámení:

    - V rozevíracím seznamu vyberte z dostupných rolí.
    - A zadejte konkrétní e-mailové adresy oddělené čárkami. Počet e-mailových adres, které můžete zadat, není nijak omezený.

1. Pokud chcete u svého předplatného použít kontaktní informace zabezpečení, vyberte **Uložit**.


## <a name="see-also"></a>Viz také
Další informace o výstrahách zabezpečení najdete v následujících tématech:

* [Výstrahy zabezpečení – referenční průvodce](alerts-reference.md) – Přečtěte si o výstrahách zabezpečení, které se můžou zobrazit v modulu Azure Security Center ochrany před internetovými útoky.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Přečtěte si, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Automatizace pracovního postupu](workflow-automation.md) – automatizace odpovědí na výstrahy pomocí vlastní logiky oznámení