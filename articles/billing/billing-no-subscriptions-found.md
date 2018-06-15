---
title: Žádné předplatné Nenalezeno Chyba při pokusu o přihlášení k portálu Azure nebo centra účtů Azure | Microsoft Docs
description: Poskytuje řešení problému, ve kterém žádné předplatné Nenalezeno, dojde k chybě při přihlášení k portálu Azure nebo centra účtů Azure.
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
ms.author: genli
ms.openlocfilehash: 475a4ad72a1c2fc2ebf99387e193713797cc2586
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070613"
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Žádná předplatná nalezena chyba na portálu Azure nebo centra účtů Azure

Může zobrazit chybová zpráva "Nebyla nalezena žádná předplatná", při pokusu o přihlášení k aplikaci [portál Azure](https://portal.azure.com/) nebo [centra účtů Azure](https://account.windowsazure.com/Subscriptions). Tento článek poskytuje řešení tohoto problému.

## <a name="symptom"></a>Příznaky

Při pokusu o přihlášení k aplikaci [portál Azure](https://portal.azure.com/) nebo [centra účtů Azure](https://account.windowsazure.com/Subscriptions), zobrazí se následující chybová zpráva: "Nebyla nalezena žádná předplatná".

## <a name="cause"></a>Příčina

K tomuto problému dochází, pokud jste vybrali v adresáři nesprávný, nebo pokud váš účet nemá dostatečná oprávnění. 

## <a name="solution"></a>Řešení

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scénář 1: Chybová zpráva se dostali [portálu Azure](https://portal.azure.com)

Pokud chcete tento problém vyřešit:

* Zkontrolujte, že správné adresář Azure je vybrána kliknutím na váš účet v pravé horní.

  ![Vyberte adresář, v horní pravé části portálu Azure](./media/billing-no-subscriptions-found/directory-switch.png)
* Pokud je zaškrtnuto vpravo Azure directory, ale stále zobrazí se chybová zpráva, [byl váš účet přidán jako vlastníka](billing-add-change-azure-subscription-administrator.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scénář 2: Chybová zpráva se dostali [centra účtů Azure](https://account.windowsazure.com/Subscriptions)

Zkontrolujte, zda je účet, který jste použili účet správce. Pokud chcete ověřit, kdo je účet správce, postupujte takto:

1. Přihlaste se k [odběry zobrazit na portálu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné, které chcete kontrolovat, a pak hledejte v části **nastavení**.
1. Vyberte **vlastnosti**. Správce účtu předplatného se zobrazí v **správce účtu** pole.  

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) získat rychle vyřešit problém. 
