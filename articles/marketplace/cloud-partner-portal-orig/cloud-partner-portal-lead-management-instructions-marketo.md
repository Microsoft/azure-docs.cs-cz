---
title: Konfigurace správy potenciálních zákazníků v Marketu | Azure Marketplace
description: Konfigurace správy potenciálních zákazníků pro zákazníky marketu pro Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288525"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurace správy potenciálních zákazníků v Marketu

Tento článek popisuje, jak nastavit Marketo pro zpracování prodejních zájemců společnosti Microsoft.

1. Přihlaste se do Marketo.
2. Vyberte **návrhstudia**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Vyberte **Nový formulář**.
    ![Marketo nová forma](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Vyplňte požadovaná pole v novém formuláři a pak vyberte **Vytvořit**.
    ![Marketo vytvořit novou formu](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  V části Podrobnosti pole vyberte **Dokončit**.
    ![Marketo dokončovací formulář](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Schválit a zavřít.

6.  Na kartě MarketplaceLeadBacked vyberte **Vložit kód**.
    ![Možnost vložit kód Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Kód pro vložení Marketo zobrazí kód podobný následujícímu příkladu.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Zkopírujte hodnoty zobrazené v části Kód pro vložení, abyste mohli nakonfigurovat **ID serveru**, **ID protokolu Munchkin**a **ID formuláře** v polích Marketo na portálu partnerů cloudu.

Použijte další příklad jako vodítko pro získání Ids, které potřebujete z Marketo Vložit kód příkladu.

- Id serveru = **ys12**
- Munchkin Id = **123-PQR-789**
- Id formuláře = **1179**
