---
title: Scénáře zotavení po havárii pro virtuální počítače Azure | Microsoft Docs
description: Seznamte se s tím, co dělat v případě, že přerušení služby Azure ovlivňuje virtuální počítače Azure.
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
ms.author: gwallace
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc9ca5f5a638f0b36a28d58172fe8052b3d1522f
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875436"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Co dělat v případě, že přerušení služby Azure ovlivňuje virtuální počítače Azure
V Microsoftu pracujeme na tom, abychom zajistili, že naše služby jsou vždycky k dispozici, až je budete potřebovat. Síly nad rámec našeho ovládacího prvku někdy ovlivňují způsob, jakým způsobují neplánované výpadky služeb.

Společnost Microsoft poskytuje smlouva SLA (SLA) pro své služby jako závazek pro dobu provozu a připojení. Smlouvu SLA pro jednotlivé služby Azure najdete na stránce [smlouvy o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/).

Azure už obsahuje mnoho vestavěných funkcí platformy, které podporují vysoce dostupné aplikace. Další informace o těchto službách najdete v tématu [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Tento článek se zabývá skutečným scénářem zotavení po havárii, když celá oblast dojde k výpadku, protože došlo k závažné přirozené havárii nebo rozšířenému přerušení služby. Jedná se o vzácná opakování, ale musíte připravit na možnost, že dojde k výpadku celé oblasti. Pokud v celé oblasti dojde k přerušení služby, místně redundantní kopie vašich dat nebudou dočasně k dispozici. Pokud jste povolili geografickou replikaci, budou se tři další kopie Azure Storage objektů BLOB a tabulek ukládat v jiné oblasti. V případě úplného výpadku v oblasti regionu nebo havárie, ve kterém se primární oblast nedá obnovit, Azure přemapuje všechny položky DNS do geografické replikované oblasti.

Abychom vám pomohli zvládnout tyto výjimečné výskyty, poskytujeme následující pokyny pro virtuální počítače Azure v případě výpadku služby celé oblasti, kde je nasazená vaše aplikace virtuálního počítače Azure.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Možnost 1: Iniciování převzetí služeb při selhání pomocí Azure Site Recovery
Pro své virtuální počítače můžete nakonfigurovat Azure Site Recovery, abyste mohli aplikaci obnovit jediným kliknutím v několika minutách. Můžete replikovat do oblasti Azure podle vašeho výběru a Neomezovat na spárované oblasti. Můžete začít [replikací virtuálních počítačů](https://aka.ms/a2a-getting-started). Můžete [vytvořit plán obnovení](../site-recovery/site-recovery-create-recovery-plans.md) , abyste mohli automatizovat celý proces převzetí služeb při selhání pro vaši aplikaci. Převzetí [služeb při selhání](../site-recovery/site-recovery-test-failover-to-azure.md) můžete předem testovat bez dopadu na produkční aplikaci nebo probíhající replikaci. V případě přerušení primární oblasti stačí [zahájit převzetí služeb při selhání](../site-recovery/site-recovery-failover.md) a přenést aplikaci do cílové oblasti.


## <a name="option-2-wait-for-recovery"></a>Možnost 2: Počkat na obnovení
V takovém případě není nutná žádná akce s vaší částí. Víte, že usilovně pracujeme na obnovení dostupnosti služby. Aktuální stav služby můžete zobrazit na našem řídicím [panelu Azure Service Health](https://azure.microsoft.com/status/).

Tato možnost je nejlepší, pokud jste nemuseli nastavovat Azure Site Recovery, geograficky redundantní úložiště s přístupem pro čtení nebo geograficky redundantní úložiště před přerušením. Pokud jste pro účet úložiště, kde jsou uložené virtuální pevné disky virtuálních počítačů (VHD), nastavili geograficky redundantní úložiště nebo geograficky redundantní úložiště s přístupem pro čtení, můžete se podívat, jak obnovit základní virtuální pevný disk image a pokusit se o zřízení nového virtuálního počítače. Nejedná se o upřednostňovanou možnost, protože neexistují žádné záruky synchronizace dat. V důsledku toho není zaručena funkčnost této možnosti.


> [!NOTE]
> Mějte na paměti, že k tomuto procesu nemáte žádnou kontrolu a dojde k tomu jenom pro přerušení služeb v rámci oblasti. Z tohoto důvodu musíte také spoléhat na jiné strategie zálohování specifické pro aplikace, abyste dosáhli nejvyšší úrovně dostupnosti. Další informace najdete v části o strategiích [dat pro zotavení po havárii](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Další postup

- Začněte [chránit své aplikace běžící na virtuálních počítačích Azure](https://aka.ms/a2a-getting-started) pomocí Azure Site Recovery

- Další informace o tom, jak implementovat strategii zotavení po havárii a vysokou dostupnost, najdete v tématu [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Podrobné technické porozumění funkcím cloudové platformy najdete v tématu [technické pokyny k odolnosti Azure](../resiliency/resiliency-technical-guidance.md).


- Pokud pokyny nejsou jasné nebo pokud byste chtěli, aby Microsoft prováděl operace vaším jménem, obraťte se na [zákaznickou podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
