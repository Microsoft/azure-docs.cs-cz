---
title: Zrušení předplatného Azure | Microsoft Docs
description: Popisuje, jak zrušit předplatné Azure, jako je bezplatné zkušební předplatné.
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666436"
---
# <a name="cancel-your-azure-subscription"></a>Zrušení předplatného Azure

Předplatné Azure můžete v Azure Portal zrušit, pokud už předplatné nepotřebujete. 

Před zrušením předplatného:
* Zálohujte svá data. Pokud například ukládáte data do služby Azure Storage nebo SQL, Stáhněte si kopii. Pokud máte virtuální počítač, uložte jeho image místně.
* Vypněte služby. [V portálu pro správu](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)přejdete na stránku prostředky a zastavte všechny běžící virtuální počítače, aplikace nebo jiné služby.
* Zvažte možnost migrace dat. Viz [Přesunutí prostředků do nové skupiny prostředků nebo](../azure-resource-manager/resource-group-move-resources.md)předplatného.
* Odstraní všechny prostředky a všechny skupiny prostředků. Před zrušením předplatného je potřeba je odstranit. Každá skupina prostředků se musí odstranit jednotlivě. Při odstraňování skupiny prostředků je nutné potvrdit odstranění zadáním názvu skupiny prostředků.
* Pokud máte v `AssignableScopes`nástroji nějaké vlastní role, které odkazují na toto předplatné, měli byste tyto vlastní role aktualizovat, aby se předplatné odebralo. Pokud se pokusíte aktualizovat vlastní roli po zrušení předplatného, může se zobrazit chyba. Další informace najdete v tématu [řešení potíží s vlastními rolemi](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) a [vlastními rolemi pro prostředky Azure](../role-based-access-control/custom-roles.md).

Pokud zrušíte placený plán podpory Azure, bude se vám účtovat zbytek období předplatného. Další informace najdete v tématu [plány podpory Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Zrušit odběr v Azure Portal

1. Vyberte své předplatné ze [stránky předplatná v Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Vyberte předplatné, které chcete zrušit.
3. Vyberte **Přehled**a pak vyberte **zrušit předplatné**.
    ![Snímek obrazovky, který zobrazuje tlačítko Storno](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Sledujte výzvy a dokončete zrušení.


## <a name="who-can-cancel-a-subscription"></a>Kdo může předplatné zrušit?

Následující tabulka popisuje oprávnění nutná k zrušení předplatného.

|Typ odběru     |Kdo může zrušit  |
|---------|---------|
|Předplatná vytvořená při registraci do Azure prostřednictvím webu Azure. Například když se přihlásíte k bezplatnému [účtu Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [účet s tarify](https://azure.microsoft.com/offers/ms-azr-0003p/) průběžných plateb nebo jako [předplatitel sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Správce účtu, vlastníci a Přispěvatelé pro předplatné  |
|[Microsoft smlouva Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) a [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Vlastník účtu, vlastníci a Přispěvatelé pro předplatné       |
|[Plán Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) a [plán Azure pro DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Vlastníci a Přispěvatelé pro předplatné      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Co se stane po zrušení předplatného?

Po zrušení se fakturace okamžitě zastaví. Může však trvat až 10 minut, než se zrušení zobrazí na portálu. Pokud zrušíte v průběhu fakturačního období, pošleme konečnou fakturu na vaše typické datum faktury po skončení období.

Po zrušení jsou vaše služby zakázané. To znamená, že vaše virtuální počítače jsou nepřidělené, dočasná IP adresa je uvolněna a úložiště je jen pro čtení.

Před tím, než trvale odstraníte vaše data, počkáme 90 dní, než budete potřebovat přístup, nebo změníte své rozhodnutí. Za zachování dat vám neúčtujeme. Další informace najdete v tématu [Centrum zabezpečení Microsoftu – jak spravujeme vaše data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Opětovná aktivace předplatného

Pokud předplatné zrušíte se svými tarify průběžných plateb omylem, můžete [ho znovu aktivovat v centru účtů](billing-subscription-become-disable.md).

Pokud vaše předplatné není předplatné s tarify průběžných plateb, obraťte se na podporu během 90 dnů od zrušení a znovu aktivujte vaše předplatné.

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
