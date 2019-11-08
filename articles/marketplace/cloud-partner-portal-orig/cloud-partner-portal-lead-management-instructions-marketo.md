---
title: Konfigurace správy potenciálních zákazníků ve Marketě | Azure Marketplace
description: Nakonfigurujte řízení správy pro zákazníky na webu Marketo pro zákazníky Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 7949507c8c7ef57cded25cde8579c1945aa93a81
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825200"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurace správy potenciálních zákazníků ve Marketě

Tento článek popisuje, jak nastavit Marketo pro zpracování zájemců Microsoftu pro prodej.

1. Přihlaste se k Marketo.
2. Vyberte **návrh Studio**.
    ![Market Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Vyberte **nový formulář**.
    nový formulář ![Market](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Vyplňte požadovaná pole v novém formuláři a pak vyberte **vytvořit**.
    ![Marketo vytvořit nový formulář](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  V poli Podrobnosti o poli vyberte **Dokončit**.
    ![formulář dokončení Market](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Schvalte a zavřete.

6.  Na kartě MarketplaceLeadBacked vyberte **Vložit kód**.
    možnost kódu pro vložení ![Market](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Kód pro vložení do markety zobrazí kód podobný následujícímu příkladu.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Zkopírujte hodnoty uvedené v kódu pro vložení, abyste mohli nakonfigurovat **ID serveru**, **ID Munchkin**a **ID formuláře** v polích Marketo na portál partnerů cloudu.

Následující příklad slouží jako vodítko pro získání potřebných ID z příkladu kódu pro vložení do Marketo.

- ID serveru = **ys12**
- Munchkin ID = **123-PQR-789**
- ID formuláře = **1179**\
