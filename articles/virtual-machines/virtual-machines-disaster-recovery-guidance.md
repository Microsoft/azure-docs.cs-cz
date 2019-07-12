---
title: Scénáře zotavení po havárii pro virtuální počítače Azure | Dokumentace Microsoftu
description: Zjistěte, co můžete dělat v případě, že výpadku služby Azure má vliv na virtuálních počítačích Azure.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: gwallace
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f071e1ae97228a16799d391e226ba44b99f6096e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721183"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Co dělat v případě, že výpadku služby Azure má vliv na virtuálních počítačích Azure
V Microsoftu usilovně pracujeme na Ujistěte se, že naše služby jsou vždy k dispozici, a když je potřebujete. Vynutí nad rámec naší ovládací prvek někdy ovlivnit nám takovým způsobem, který způsobit přerušení neplánované služeb.

Společnost Microsoft poskytuje Smlouva o úrovni služeb (SLA) pro své služby jako závazek dostupnosti a konektivity. Smlouva SLA pro jednotlivé služby Azure lze nalézt v [smlouvy o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/).

Azure už má mnoho funkcí integrované platformy, které podporují aplikace s vysokou dostupností. Další informace o těchto službách najdete v článku [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Tento článek popisuje na scénář zotavení po havárii true po celou oblast dojde k výpadku z důvodu větší přírodní havárie nebo přerušení rozšířených služby. Toto jsou vzácné opakování, ale musíte připravit pro možnost, že dojde k výpadku celé oblasti. Pokud celou oblast dojde k přerušení služeb, místně redundantní kopie vašich dat by být dočasně není k dispozici. Pokud jste povolili geografickou replikaci, tři další kopie objekty Azure Storage BLOB a tabulek jsou uložené v jiné oblasti. V případě kompletní oblastnímu výpadku nebo havárii, ve kterém primární oblasti se nedá vrátit zpátky Azure změní všechny položky DNS do oblasti geograficky replikovaný.

Při zpracování těchto výjimečných výskytů, poskytujeme následující pokyny pro virtuální počítače Azure v případě přerušení služby celou oblast, ve kterém je virtuální počítač Azure aplikace může nasadit.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Option 1: Spuštění převzetí služeb při selhání pomocí Azure Site Recovery
Azure Site Recovery pro virtuální počítače můžete nakonfigurovat tak, aby mohli obnovit aplikaci s jedním kliknutím v řádu minut. Můžete replikovat do oblasti Azure podle vašeho výběru a nikoliv omezená spárovaných oblastí. Můžete začít tak [replikaci virtuálních počítačů](https://aka.ms/a2a-getting-started). Je možné [vytvořit plán obnovení](../site-recovery/site-recovery-create-recovery-plans.md) tak, aby pro vaši aplikaci můžete automatizovat proces celý převzetí služeb při selhání. Je možné [testovat vaše převzetí služeb při selhání](../site-recovery/site-recovery-test-failover-to-azure.md) předem bez dopadu na produkční aplikace nebo probíhající replikaci. V případě přerušení primární oblasti, jste právě [zahájit převzetí služeb při](../site-recovery/site-recovery-failover.md) a používání vaší aplikace v cílové oblasti.


## <a name="option-2-wait-for-recovery"></a>Option 2: Vyčkat, než obnovení
V takovém případě nemusíte vaší nic dělat. Vědět, že pilně pracujeme na dostupnosti služby. Aktuální stav služby najdete na naší [řídicí panel stavu služby Azure](https://azure.microsoft.com/status/).

Toto je nejlepší, pokud jste nenastavili Azure Site Recovery, geograficky redundantní úložiště jen pro čtení nebo geograficky redundantní úložiště před narušení. Pokud nastavíte geograficky redundantní úložiště nebo geograficky redundantní úložiště jen pro čtení pro účet úložiště ve kterém jsou uloženy virtuální pevné disky virtuálního počítače (VHD), může vypadat obnovit image základního virtuálního pevného disku a zkuste zřídit nový virtuální počítač z něj. Nejedná se upřednostňovanou možnost ověřování, protože neexistují žádné záruky synchronizace dat. V důsledku toho tato možnost není zaručena.


> [!NOTE]
> Mějte na paměti, že nemáte žádné kontrolu nad tento proces a se vztahuje pouze k přerušení služeb na úrovni celé oblasti. Z tohoto důvodu musíte také spoléhat na další specifické pro aplikaci Zálohování strategie pro dosažení nejvyšší úroveň dostupnosti. Další informace najdete v části na [Data strategie zotavení po havárii](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Další kroky

- Spustit [chrání vaše aplikace spuštěné na virtuálních počítačích Azure](https://aka.ms/a2a-getting-started) pomocí Azure Site Recovery

- Další informace o tom, jak implementovat strategie vysoké dostupnosti a zotavení po havárii najdete v tématu [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- K vývoji podrobné technické informace o možnosti cloudové platformy, najdete v článku [technické pokyny k odolnosti Azure](../resiliency/resiliency-technical-guidance.md).


- Pokud jsou pokyny vymazat nebo pokud chcete provést operace vaším jménem společnosti Microsoft, obraťte se na [zákaznickou podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
