---
title: Chyba Nenašla se žádná předplatná – přihlášení na web Azure Portal | Microsoft Docs
description: Poskytuje řešení problému, kdy při přihlášení k webu Azure Portal nebo k centru účtů Azure dochází k chybě Nenašla se žádná předplatná.
services: ''
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 19a30a99b9df23358aafd88c55bde08c73a3be72
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121548"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Chyba přihlášení Nenašla se žádná předplatná pro Azure Portal nebo centrum účtů Azure

Chybová zpráva Nenašla se žádná předplatná se může zobrazit při pokusu o přihlášení k webu [Azure Portal](https://portal.azure.com/) nebo k [centru účtů Azure](https://account.windowsazure.com/Subscriptions). Tento článek poskytuje řešení tohoto problému.

## <a name="symptom"></a>Příznak

Když se pokusíte přihlásit k webu [Azure Portal](https://portal.azure.com/) nebo [centru účtů Azure](https://account.windowsazure.com/Subscriptions), zobrazí se následující chybová zpráva: Nenašla se žádná předplatná.

## <a name="cause"></a>Příčina

K tomuto problému dochází, pokud jste vybrali nesprávný adresář nebo pokud váš účet nemá dostatečná oprávnění. 

## <a name="solution"></a>Řešení

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scénář 1: Chybová zpráva se objeví na webu [Azure Portal](https://portal.azure.com).

Pokud chcete tento problém vyřešit:

* Klikněte vpravo nahoře na svůj účet a ujistěte se, že je vybraný správný adresář Azure.

  ![Vyberte adresář v pravé horní části webu Azure Portal.](./media/billing-no-subscriptions-found/directory-switch.png)
* Pokud je vybraný správný adresář Azure, ale chybová zpráva se přesto zobrazuje, [přiřaďte vašemu účtu roli vlastníka](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scénář 2: Chybová zpráva se objeví v [centru účtů Azure](https://account.windowsazure.com/Subscriptions).

Zkontrolujte, jestli účet, který jste použili, je účet správce účtu. Pokud chcete ověřit, kdo je správce účtu, postupujte takto:

1. Přihlaste se k [zobrazení předplatných na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné, které chcete zkontrolovat, a pak se podívejte pod **Nastavení**.
1. Vyberte **Vlastnosti**. Správce účtu předplatného se zobrazí v poli **Správce účtu**.  

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
