---
title: Konfigurace správy potenciálních zákazníků ve Marketě | Azure Marketplace
description: Nakonfigurujte řízení správy pro zákazníky na webu Marketo pro zákazníky Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288525"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurace správy potenciálních zákazníků ve Marketě

Tento článek popisuje, jak nastavit Marketo pro zpracování zájemců Microsoftu pro prodej.

1. Přihlaste se k Marketo.
2. Vyberte **návrh Studio**.
    ![Marketo návrh Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Vyberte **nový formulář**.
    ![Nový formulář Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Vyplňte požadovaná pole v novém formuláři a pak vyberte **vytvořit**.
    ![Vytvoření nového formuláře Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  V poli Podrobnosti o poli vyberte **Dokončit**.
    ![Formulář pro dokončení Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Schvalte a zavřete.

6.  Na kartě MarketplaceLeadBacked vyberte **Vložit kód**.
    ![Možnost kódu pro vložení do markety](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Kód pro vložení do markety zobrazí kód podobný následujícímu příkladu.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Zkopírujte hodnoty uvedené v kódu pro vložení, abyste mohli nakonfigurovat **ID serveru**, **ID Munchkin**a **ID formuláře** v polích Marketo na portál partnerů cloudu.

Následující příklad slouží jako vodítko pro získání potřebných ID z příkladu kódu pro vložení do Marketo.

- ID serveru = **ys12**
- Munchkin ID = **123-PQR-789**
- ID formuláře = **1179**
