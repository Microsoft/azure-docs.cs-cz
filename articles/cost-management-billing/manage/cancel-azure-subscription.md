---
title: Zrušení předplatného Azure | Microsoft Docs
description: Tento článek popisuje, jak zrušit předplatné Azure, například bezplatné zkušební předplatné.
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e4ce97b69250d8af4c94e8eed8df9c9497e0bb46
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993073"
---
# <a name="cancel-your-azure-subscription"></a>Zrušení předplatného Azure

Pokud už předplatné Azure nepotřebujete, můžete ho zrušit na webu Azure Portal.

Před zrušením předplatného:
* Zálohujte svá data. Pokud například uchováváte data v Azure Storage nebo SQL, stáhněte si kopii. Pokud máte virtuální počítač, uložte jeho image místně.
* Vypněte používané služby. Přejděte na [stránku s prostředky na portálu pro správu](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) a **zastavte** všechny běžící virtuální počítače, aplikace nebo jiné služby.
* Zvažte migraci svých dat. Přečtěte si článek [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Odstraňte všechny prostředky a všechny skupiny prostředků. Jejich odstranění se vyžaduje před zrušením předplatného. Každá skupina prostředků se musí odstranit zvlášť. Při odstraňování skupiny prostředků musíte odstranění potvrdit zadáním názvu skupiny prostředků.
* Pokud máte v `AssignableScopes` nějaké vlastní role, které odkazují na toto předplatné, měli byste tyto vlastní role aktualizovat, aby předplatné šlo odebrat. Pokud se pokusíte vlastní roli aktualizovat po zrušení předplatného, může se zobrazit chyba. Další informace najdete v článku [Řešení problémů s vlastními rolemi](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) a [Vlastní role pro prostředky Azure](../../role-based-access-control/custom-roles.md).

Pokud zrušíte placený plán podpory Azure, bude se vám účtovat zbývající část období předplatného. Další informace najdete v článku [Plány podpory Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Zrušení předplatného na webu Azure Portal

1. Vyberte své předplatné na [stránce Předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Vyberte předplatné, které chcete zrušit.
3. Vyberte **Přehled** a pak vyberte **Zrušit předplatné**.
    ![Snímek obrazovky, který zobrazuje tlačítko Zrušit](./media/cancel-azure-subscription/cancel_ibiza.png)
3. Postupujte podle pokynů a dokončete zrušení.


## <a name="who-can-cancel-a-subscription"></a>Kdo může předplatné zrušit?

Následující tabulka popisuje oprávnění nutná ke zrušení předplatného.

|Typ předplatného     |Kdo může zrušit  |
|---------|---------|
|Předplatná vytvořená při registraci Azure prostřednictvím webu Azure Může to být třeba tehdy, když si zaregistrujete [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo účet pro [předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Správce účtu, vlastníci a přispěvatelé předplatného  |
|[Smlouva Microsoft Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) a [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Vlastník účtu, vlastníci a přispěvatelé předplatného       |
|[Plán Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) a [Plán Azure pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Vlastníci a přispěvatelé předplatného      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Co se stane po zrušení předplatného?

Po zrušení se okamžitě zastaví účtování. Zrušení se ale na portálu může projevit až za 10 minut. Pokud předplatné zrušíte uprostřed fakturačního období, po skončení tohoto období vám pošleme konečnou fakturu k běžnému datu faktury.

Po zrušení jsou vaše služby zakázané. To znamená, že se zruší přidělení vašich virtuálních počítačů, uvolní se dočasná IP adresa a úložiště přejde do režimu jen pro čtení.

Před trvalým odstraněním vašich dat počkáme 90 dní pro případ, že k nim budete potřebovat přístup nebo že změníte své rozhodnutí. Za uchování těchto dat vám nic neúčtujeme. Další informace najdete v tématu [Centrum zabezpečení Microsoftu – Jak spravujeme vaše data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Opětovná aktivace předplatného

Pokud omylem zrušíte své předplatné s průběžnými platbami, můžete [ho znovu aktivovat v Centru účtů](subscription-disabled.md).

Pokud se nejedná o předplatné s průběžnými platbami, požádejte během 90 dnů od zrušení podporu o opětovnou aktivaci svého předplatného.

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
