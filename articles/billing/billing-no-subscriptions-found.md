---
title: Žádné předplatné Nenalezeno Chyba při pokusu o přihlášení k webu Azure portal nebo centra účtů Azure | Dokumentace Microsoftu
description: Poskytuje řešení problému, ve kterém žádné předplatné Nenalezeno, dojde k chybě při přihlášení k webu Azure portal nebo centra účtů Azure.
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: a1e90f946508f1ffc0a1ee812dde46ee733d715a
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392430"
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Žádné předplatné Nenalezeno chyby v portálu Azure portal nebo v centru účtů Azure

Zobrazí se chybová zpráva "Nenašly se žádné odběry" při pokusu o přihlášení k [webu Azure portal](https://portal.azure.com/) nebo [centra účtů Azure](https://account.windowsazure.com/Subscriptions). Tento článek poskytuje řešení tohoto problému.

## <a name="symptom"></a>Příznak

Při pokusu o přihlášení k aplikaci [webu Azure portal](https://portal.azure.com/) nebo [centra účtů Azure](https://account.windowsazure.com/Subscriptions), zobrazí následující chybová zpráva: "Nenašly se žádné odběry".

## <a name="cause"></a>Příčina

K tomuto problému dochází, pokud jste vybrali na špatný adresář, nebo pokud váš účet nemá dostatečná oprávnění. 

## <a name="solution"></a>Řešení

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scénář 1: Chyba doručení zprávy [webu Azure portal](https://portal.azure.com)

Chcete-li tento problém vyřešit:

* Ujistěte se, že je vybraná na správný adresář Azure kliknutím na váš účet v pravém horním rohu.

  ![Vyberte adresář, v horním pravém rohu webu Azure portal](./media/billing-no-subscriptions-found/directory-switch.png)
* Pokud je vybrána přímo Azure directory, ale i nadále zobrazí chybová zpráva [přiřazením role vlastníka k vašemu účtu](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scénář 2: Chyba doručení zprávy [centra účtů Azure](https://account.windowsazure.com/Subscriptions)

Zkontrolujte, zda je účet, který jste použili účet správce. Pokud chcete ověřit, kdo je správcem účtu, postupujte podle těchto kroků:

1. Přihlaste se k [zobrazit předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné, které chcete zkontrolovat a pak hledejte v části **nastavení**.
1. Vyberte **vlastnosti**. Správce účtu předplatného se zobrazí v **správce účtu** pole.  

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) pro rychlé vyřešení problému. 
