---
title: Zrušení předplatného Azure | Microsoft Docs
description: Popisuje, jak zrušit předplatné Azure, jako je bezplatnou zkušební verzi
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 604e2ee6d2ac7e30813f8b4cd02253407c2b1e3f
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="cancel-your-subscription-for-azure"></a>Zrušení předplatného pro Azure.

Můžete zrušit předplatné Azure, jako [správce účtu](billing-subscription-transfer.md#whoisaa). Po zrušení předplatného, váš přístup k Azure službám a prostředkům se ukončí.

Před zrušením předplatného je:

* Zálohujte data. Například pokud data ukládáte v Azure storage nebo jazyka SQL, stáhněte si kopii. Pokud máte virtuální počítač, uložte image ho místně.
* Ukončení vaší služby. Přejděte na [prostředky stránku v portálu pro správu](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), a **Zastavit** žádné spuštění virtuálního počítače, aplikace nebo jiné služby.
* Zvažte přesunutí vaše data. V tématu [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

Pokud zrušíte placená [plán podpory Azure](https://azure.microsoft.com/support/plans/), stále fakturuje se každý měsíc pro zbytek termín 6 měsíců.

## <a name="cancel-subscription-using-the-azure-portal"></a>Zrušit předplatné pomocí portálu Azure

1. Vyberte své předplatné z [odběry stránky na portálu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte odběr, který chcete zrušit a klikněte na tlačítko **zrušit předplatné**.

    ![Snímek obrazovky, který ukazuje na tlačítko Storno](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Postupujte podle výzev a dokončení zrušení.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Co se stane po I zrušení předplatného?

Jakmile zrušíte, je okamžitě zastavena fakturace. Však to může trvat až 10 minut na zrušení zobrazit na portálu.

Potom budou zakázány vaší služby. To znamená, že virtuální počítače jsou navrátit dočasné IP adresy jsou uvolněny a úložiště je jen pro čtení.

Pokud zrušíte uprostřed fakturační období, pošleme na vaše data typické fakturace po skončení doby poslední faktury. 

Jsme Počkejte, než 90 dní před trvale odstraní vaše data v případě, že budete muset k němu přístup nebo změníte své rozhodnutí. Jsme nemáte účtovat poplatky za zachovat data. Další informace najdete v tématu [Microsoft Trust Center – jak jsme spravovat vaše data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Znovu aktivovat předplatné

Pokud zrušíte předplatné s průběžnými platbami omylem, můžete [aktivovat v centru účtů](billing-subscription-become-disable.md).

Pokud vaše předplatné není průběžné platby, obraťte se na podporu do 90 dnů od zrušení aktivace předplatného.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud máte otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
