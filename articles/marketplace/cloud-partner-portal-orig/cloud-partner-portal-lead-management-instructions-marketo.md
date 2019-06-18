---
title: Konfigurace správy potenciálních zákazníků služby marketo | Azure Marketplace
description: Konfigurace správy potenciálních zákazníků pro Marketo pro zákazníky Azure marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: d749a92a1837bad053dc586477bfc27fc65299fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935216"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurace správy potenciálních zákazníků služby marketo

Tento článek popisuje, jak nastavit Marketo pro zpracování potenciálních zákazníků Microsoftu.

1. Přihlaste se k Marketo.
2. Vyberte **návrh Studio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Vyberte **nový formulář**.
    ![Formulář nové platformy Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Vyplnění požadovaných polí ve formuláři nový a potom vyberte **vytvořit**.
    ![Nový formulář pro vytvoření služby Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  V poli podrobnosti, vyberte **Dokončit**.
    ![Formulář dokončit Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Schválení a zavřete.

6.  Na kartě MarketplaceLeadBacked vyberte **kód pro vložení**.
    ![Možnost kód pro vložení Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Kód pro vložení Marketo zobrazí podobně jako v následujícím příkladu kódu.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Zkopírujte hodnoty uvedené v kódu pro vložení, můžete nakonfigurovat **Id serveru**, **Munchkin Id**, a **Id formuláře** v polích Marketo na portál partnerů cloudu.

Další příklad, použijte jako vodítko pro získání ID, budete potřebovat v příkladu kódu pro vložení Marketo.

- Server Id = **ys12**
- Munchkin Id = **123. PQR 789**
- Formulář Id = **1179**\
